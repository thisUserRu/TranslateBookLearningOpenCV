## Загрузка и установка OpenCV

Основным сайтом OpenCV является [SourceForge](http://SourceForge.net/projects/opencvlibrary) [http://SourceForge.net/projects/opencvlibrary] и [вики](http://opencvlibrary.SourceForge.net) страницы [http://opencvlibrary.SourceForge.net]. Для Linux - opencv-1.0.0.tar.gz; для Windows - OpenCV_1.0.exe. Самую актуальную версию всегда можно скачать с CVS сервера SourceForge.

### Установка

После загрузки библиотеки, потребуется её установить. Подробные инструкции по установки на Linux или Mac OS, всегда можно найти в файле INSTALL в каталоге .../opencv/; этот файл также описывает процесс сбора и запуск процедур тестирования OpenCV. В файле INSTALL также перечислены дополнительные программы, которые потребуются для сбора версии OpenCV для разработчиков, например autoconf, automake, libtool, и swig.

#### Windows

Для начала нелбходимо скачать и запустить инсталятор с SourceForge. Он устновит OpenCV, зарегистрирует фильтры DirectShow и выполнит некоторые постинсталяционные процедуры. После этого все готово для использования OpenCV. Помимо этого, всегда можно посетить директорию .../opencv/_make и открыть opencv.sln с MSVC++ или MSVC.NET 2005, или открыть opencv.dsw для более старой версии MSVC++ и собрать или каким то образом изменить библиотеку. 

Для добавления оптимизации производительности при помощи коммерческой билиотеки IPP для Windows, необходимо приобрести установщик с сайта Intel (http://www.intel.com/software/products/ipp/index.htm) {как оказалось, на момент написания перевода (апрель 2015), неудалось открыть данную страницу по указанному пути, однако, данная ссылка возможно сможет помочь: https://downloadcenter.intel.com/ru/download/21808}. После установки убедитесь, что папка bin (например, c:/program files/
intel/ipp/5.1/ia32/bin) находится по указанному пути. Теперь IPP будет автоматически распознаваться OpenCV и подгружаться во время выполнения (более подробно об этом пойдет речь в главе 3).

#### Linux

Бинарники для Linux не включены в версию OpenCV под Linux из-за большого разнообразия версий GCC и GLIBC для различных дистрибутивов (SuSE, Debian, Ubuntu и т.д.). Если в вашем дистрибутиве нет OpenCV, необходимо собрать её из исходников согласно файлу .../opencv/INSTALL.

Для сбора библотеки и демок потребуется GTK+ 2.x или выше (включая заголовки). Так же потребуются pkgconfig, libpng, zlib, libjpeg, libtiff, и libjasper. Потребуется Python (вклячая заголовки) версии 2.3, 2.4 или 2.5 (пакет разработчика). Так же нужен libavcodec и другие libav* библиотеки (включая заголовки) из ffmpeg 0.4.9-pre1 или более поздней версии (svn checkout svn://svn.mplayerhq.hu/ffmpeg/trunk ffmpeg).

Скачать ffmpeg можно по адресу http://ffmpeg.mplayerhq.hu/download.html. ffmpeg имеет лицензию LGPL. (!)Это позволяет использовать ffmpeg с ПО, у кторого не GPL лицензия (например OpenCV):(!)

	$> ./configure --enable-shared
	$> make
	$> sudo make install

В итоге: /usr/local/lib/libavcodec.so.*, /usr/local/lib/libavformat.so.*,
/usr/local/lib/libavutil.so.*, и заголовочные файлы /usr/local/include/libav*.

Для построения OpenCV:

	$> ./configure
	$> make
	$> sudo make install
	$> sudo ldconfig

Путь по умолчанию для установленной библиотеки: /usr/local/lib/ и /usr/local/include/opencv/. После необходимо добавить /usr/local/lib/ в /etc/ld.so.conf (и запустить ldconfig впоследствии) или в переменную окружения LD_LIBRARY_PATH. 

Установка библиотеки IPP (оптимизация производительности) в Linux происходит тем же способом, что был описан ранее. Предположим, что библиотека установлена в /opt/intel/ipp/5.1/ia32/. В скрипте инициализации (.bashrc или подобный) необходимо добавить <your install_path>/bin/ и <your install_path>/bin/linux32 LD_LIBRARY_PATH:

	LD_LIBRARY_PATH=/opt/intel/ipp/5.1/ia32/bin:/opt/intel/ipp/5.1/ia32/bin/linux32:$LD_LIBRARY_PATH
	export LD_LIBRARY_PATH

Или, построчно добавить <your install_path>/bin и <your install_path>/bin/linux32 в файл /etc/ld.so.conf и запустить команду ldconfig из под root-а (или использовать sudo).

Все готово к использованию. Теперь OpenCV сможет найти и использовать IPP. Для получения более подробной информации, обратитесь к файлу .../opencv/INSTALL.

#### MacOS X

На момент написания книги, присутствуют некоторые ограничения для MacOS X (например, запись AVI); данные ограничения описаны в файле .../opencv/INSTALL.

Сбор библиотеки под MacOS X аналогичен случаю для Linux, со следующими исключениями:
* По умолчанию, Carbon используется вместо GTK+
* По умолчанию, QuickTime используется вместо ffmpeg
* pkg-config не обязательна (используется только в скрипте samples/c/build_all.sh)
* RPM и lgconfig не поддерживаются по умолчанию. Используйте configure+make+sudo make
install для сборки и установки OpenCV, обновите LD_LIBRARY_PATH (если ./configure --prefix=/usr не используется)

Для нормальной функциональности, необходимо доставить libpng, libtiff, libjpeg и libjasper от darwinports и/или fink и сделать их доступными дял ./configure (см. ./configure --help). Для получения актуальной информации, посетите OpenCV Wiki по адресу http://opencvlibrary.SourceForge.net.