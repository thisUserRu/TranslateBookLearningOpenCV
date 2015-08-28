## [П]|[РС]|(РП) Пример поиска контура

В этом примере происходит поиск контуров на исходном изображении с последующим их рисованием друг за другом. Это хороший пример для того, чтобы поиграться с различными параметрами, в частности, посмотреть к чему приведут изменения режима поиска контуров (в примере используется *CV_RETR_LIST*) или *max_depth*, используемый для рисования контуров (в пример используется 0). Если *max_depth* будет иметь довольно таки большое значение, то возвращаемые функцией *cvFindContours()* контуры будут связаны при помощи *h_next*. Это означает, что для некоторых топологий (*CV_RETR_TREE*, *CV_RETR_CCOMP*) можно увидеть один и тот же контур несколько раз.

Пример 8-3. Поиск и рисование контуров на исходном изображении

```cpp
int main( int argc, char* argv[] ) {
 
    cvNamedWindow( argv[0], 1 );
 
    IplImage* img_8uc1 = cvLoadImage( argv[1], CV_LOAD_IMAGE_GRAYSCALE );
    IplImage* img_edge = cvCreateImage( cvGetSize(img_8uc1), 8, 1 );
    IplImage* img_8uc3 = cvCreateImage( cvGetSize(img_8uc1), 8, 3 );
 
    cvThreshold( img_8uc1, img_edge, 128, 255, CV_THRESH_BINARY );
 
    CvMemStorage* storage = cvCreateMemStorage();
    CvSeq* first_contour = NULL;
 
    int Nc = cvFindContours(
         img_edge
        ,storage
        ,&first_contour
        ,sizeof(CvContour)
        ,CV_RETR_LIST 	// Попробуйте все четыре значения и посмотрите, что получится
    );
 
    int n=0;
    printf( "Total Contours Detected: %d\n", Nc );
    for( CvSeq* c=first_contour; c!=NULL; c=c->h_next ) {
        cvCvtColor( img_8uc1, img_8uc3, CV_GRAY2BGR );
        cvDrawContours(
             img_8uc3
            ,c
            ,CVX_RED
            ,CVX_BLUE
            ,0 	// Попробуйте различные значения и посмотрите, что получится
            ,2
            ,8
        );

        printf("Contour #%d\n", n );
        cvShowImage( argv[0], img_8uc3 );
 
        printf(" %d elements:\n", c->total );
        for( int i=0; i<c->total; ++i ) {
            CvPoint* p = CV_GET_SEQ_ELEM( CvPoint, c, i );
            printf(" (%d,%d)\n", p->x, p->y );
        }

        cvWaitKey(0);
        n++;
    }

    printf("Finished all contours.\n");
    cvCvtColor( img_8uc1, img_8uc3, CV_GRAY2BGR );
    cvShowImage( argv[0], img_8uc3 );
    cvWaitKey(0);

    cvDestroyWindow( argv[0] );

    cvReleaseImage( &img_8uc1 );
    cvReleaseImage( &img_8uc3 );
    cvReleaseImage( &img_edge );
    
    return 0;
}
```