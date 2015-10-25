## [П]|[РС]|(РП) Пример реализации калибровки

OK, пришло время отразить всю полученную информацию в одном небольшом примере. В данном разделе будет представлена программа, которая выполняет следующие задачи: она ищет шахматные доски тех размеров, что указал пользователь, подцепляет цельные изображения (т.е. те, на которых можно найти все углы шахматной доски) в количестве, которое запросил пользователь и вычисляет встроенные параметры камеры и коэффициенты искажения. В итоге, программа входит в режим отображения, в котором можно просмотреть неискаженную версию изображения с камеры (рисунок 11-1). При использовании данного алгоритма необходимо существенно изменять положение шахматной доски между успешными захватами. В противном случае, матрицы точек, используемые при вычислении параметров калибровки, могут образовывать поврежденную (недостаточного ранга) матрицу и в конечном итоге, либо будет получено плохое решение, либо решение и вовсе не будет найдено.

Пример 11-1. Чтение ширины и высоты шахматной доски, чтение и формирование коллекции запрашиваемого количества представлений и калибровка камеры

```cpp
// calib.cpp
// Пример использования:
// calib board_w board_h number_of_views
//
// Нажатие 'p' - пауза/воспроизведение, ESC - выход
//
#include <cv.h>
#include <highgui.h>
#include <stdio.h>
#include <stdlib.h>

int 		n_boards = 0; 	// Исходный список
const int 	board_dt = 20; 	// Ожидание 20 кадров на одно
							// представление шахматной доски
int 		board_w;
int 		board_h;

int main(int argc, char* argv[]) {
	
	if(argc != 4){
		printf("ERROR: Wrong number of input parameters\n");
		return -1;
	}

	board_w 	= atoi(argv[1]);
	board_h 	= atoi(argv[2]);
	n_boards 	= atoi(argv[3]);
	int board_n 		= board_w * board_h;
	CvSize board_sz 	= cvSize( board_w, board_h );
	CvCapture* capture 	= cvCreateCameraCapture( 0 );
	assert( capture );

	cvNamedWindow( "Calibration" );

	// Выделение памяти под хранилища
	// 
	CvMat* image_points 		= cvCreateMat( n_boards*board_n, 2, CV_32FC1 );
	CvMat* object_points 		= cvCreateMat( n_boards*board_n, 3, CV_32FC1 );
	CvMat* point_counts 		= cvCreateMat( n_boards, 1, CV_32SC1 );
	CvMat* intrinsic_matrix 	= cvCreateMat( 3, 3, CV_32FC1 );
	CvMat* distortion_coeffs 	= cvCreateMat( 5, 1, CV_32FC1 );

	CvPoint2D32f* corners = new CvPoint2D32f[ board_n ];
	int corner_count;
	int successes = 0;
	int step, frame = 0;
	IplImage *image = cvQueryFrame( capture );
	IplImage *gray_image = cvCreateImage(cvGetSize(image),8,1); //subpixel

	// Захват углов представления в цикле пока не будет достигнуто n_boards
	// успешно захваченных представлений (т.е. тех, у которых найдены 
	// все углы шахматной доски)
	//
	while(successes < n_boards) {
	
		// Пропуск board_dt кадров, предоставленных пользователем,
		// передвигающий шахматную доску
		// 
		if(frame++ % board_dt == 0) {
		
			// Поиск углов шахматной доски
			// 
			int found = cvFindChessboardCorners(
				 image
				,board_sz
				,corners
				,&corner_count
				,CV_CALIB_CB_ADAPTIVE_THRESH | CV_CALIB_CB_FILTER_QUADS
			);

			// Субпиксельная точность для полученных углов
			// 
			cvCvtColor( image, gray_image, CV_BGR2GRAY );
			cvFindCornerSubPix(
				 gray_image
				,corners
				,corner_count
				,cvSize(11,11)
				,cvSize(-1,-1)
				,cvTermCriteria( CV_TERMCRIT_EPS + CV_TERMCRIT_ITER, 30, 0.1 ) 
			);
			
			// Отрисовка углов
			// 
			cvDrawChessboardCorners(
				 image
				,board_sz
				,corners
				,corner_count
				,found
			);
			cvShowImage( "Calibration", image );
			
			// При получении хорошего представления доски, добавить в коллекцию
			// 
			if( corner_count == board_n ) {
				step = successes*board_n;
				for( int i = step, j = 0; j < board_n; ++i, ++j ) {
					CV_MAT_ELEM( *image_points,  float, i, 0 ) = corners[j].x;
					CV_MAT_ELEM( *image_points,  float, i, 1 ) = corners[j].y;
					CV_MAT_ELEM( *object_points, float, i, 0 ) = j/board_w;
					CV_MAT_ELEM( *object_points, float, i, 1 ) = j%board_w;
					CV_MAT_ELEM( *object_points, float, i, 2 ) = 0.0f;
				}
				CV_MAT_ELEM( *point_counts, int, successes, 0 ) = board_n;
				successes++;
			}
		} // окончание пропуска board_dt между захватами доски

		// Обработка нажатия клавиш паузы/воспроизведения и выхода
		// 
		int c = cvWaitKey(15);
		if( c == 'p' ){
			c = 0;
			while( c != 'p' && c != 27 ){
				c = cvWaitKey( 250 );
			}
		}
		if( c == 27 )
			return 0;
		image = cvQueryFrame( capture ); // Получение следующего изображения
	} // Окончание цикла захвата представлений

	// Выделение памяти под матрицы соразмерных с найденным количеством 
	// шахматных досок
	// 
	CvMat* object_points2 	= cvCreateMat( successes*board_n, 3, CV_32FC1);
	CvMat* image_points2 	= cvCreateMat( successes*board_n, 2, CV_32FC1);
	CvMat* point_counts2 	= cvCreateMat( successes, 1, CV_32SC1);

	// Перемещение точек в матрицы правильного размера
	// Детали реализации представлены двумя циклами. Общая
	// форма записи:
	// image_points->rows = object_points->rows = \
	// successes*board_n; point_counts->rows = successes;
	// 
	for( int i = 0; i < successes*board_n; ++i ) {
		CV_MAT_ELEM( *image_points2, float, i, 0 ) = 
			CV_MAT_ELEM( *image_points, float, i, 0 );
		CV_MAT_ELEM( *image_points2, float,i,1 ) = 
			CV_MAT_ELEM( *image_points, float, i, 1 );
		CV_MAT_ELEM( *object_points2, float, i, 0 ) = 
			CV_MAT_ELEM( *object_points, float, i, 0 );
		CV_MAT_ELEM( *object_points2, float, i, 1 ) = 
			CV_MAT_ELEM( *object_points, float, i, 1 );
		CV_MAT_ELEM( *object_points2, float, i, 2 ) = 
			CV_MAT_ELEM( *object_points, float, i, 2 );
	}

	// Все те же номера
	// 
	for( int i = 0; i < successes; ++i ) {
		CV_MAT_ELEM( *point_counts2, int, i, 0 ) = 
			CV_MAT_ELEM( *point_counts, int, i, 0 );
	}

	cvReleaseMat( &object_points );
	cvReleaseMat( &image_points );
	cvReleaseMat( &point_counts );

	// Теперь определены все углы шахматной доски для данных точек.
	// Матрица внутренних параметров должна быть проинициализирована
	// таким образом, что расстояние между двумя фокусными расстояниями 
	// имело соотношение, равное 1.0
	//
	CV_MAT_ELEM( *intrinsic_matrix, float, 0, 0 ) = 1.0f;
	CV_MAT_ELEM( *intrinsic_matrix, float, 1, 1 ) = 1.0f;

	// Калибровка камера
	// 
	cvCalibrateCamera2(
		 object_points2
		,image_points2
		,point_counts2
		,cvGetSize( image )
		,intrinsic_matrix
		,distortion_coeffs
		,NULL
		,NULL
		,0 // CV_CALIB_FIX_ASPECT_RATIO
	);

	// Сохранение внутренних параметров и коэффициентов
	// искажения
	// 
	cvSave( "Intrinsics.xml", intrinsic_matrix );
	cvSave( "Distortion.xml", distortion_coeffs );

	// Пример загрузки сохраненных параметров
	// 
	CvMat *intrinsic 	= (CvMat*)cvLoad( "Intrinsics.xml" );
	CvMat *distortion 	= (CvMat*)cvLoad( "Distortion.xml" );

	// Построение карты искажений, которая будет использована для 
	// для всей последовательности кадров
	//
	IplImage* mapx = cvCreateImage( cvGetSize(image), IPL_DEPTH_32F, 1 );
	IplImage* mapy = cvCreateImage( cvGetSize(image), IPL_DEPTH_32F, 1 );
	cvInitUndistortMap(
		 intrinsic
		,distortion
		,mapx
		,mapy
	);

	// При запуске камеры, сразу отображается необработанное изображение
	//
	cvNamedWindow( "Undistort" );
	
	while( image ) {
		
		IplImage *t = cvCloneImage(image);

		// Отображение необработанного изображения
		// 
		cvShowImage( "Calibration", image ); 

		// Удаление искажений с изображения
		//
		cvRemap( t, image, mapx, mapy );		 
		cvReleaseImage( &t );

		// Отображение откорректированного изображения
		//
		cvShowImage( "Undistort", image ); 

		// Обработка клавиш паузы/воспроизведения и выхода
		// 
		int c = cvWaitKey( 15 );
		if( c == 'p' ) {
			c = 0;
			while( c != 'p' && c != 27 ) {
				c = cvWaitKey( 250 );
			}
		}
		if( c == 27 )
			break;
		image = cvQueryFrame( capture );
	}

	return 0;	
}
```