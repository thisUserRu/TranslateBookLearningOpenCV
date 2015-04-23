## [П]|[РС]|(РП) Получение последней версии OpenCV через CVS

OpenCV активно развивается и ошибки довольно таки быстро правятся, благодаря предоставлению подробного отчета и кода, демонстрирующий ошибку. Однако, официальные релизы OpenCV выходят один или два раза в год. Если есть серьезный проект, то могут потребоваться исправления и обновления, как только они станут доступны. Для их получения, необходимо получить доступ к OpenCV’s Concurrent Versions System (CVS) на SourceForge.

Данная книга не учит работе с CVS. Если вы уже работали над другими проектами с открытым исходным кодом, то вероятнее всего уже знакомы с CVS. Если же нет, то изучите труд Essential CVS от Jennifer Vesperman (O’Reilly). Командная строка CVS поставляется с Linux, OS X и в большинстве UNIX-подобных системах. Для пользователей Windows, рекомендуем использовать [TortoiseCVS](http://www.tortoisecvs.org/), который прекрасно встраивается в Windows Explorer.

Для получения последней версии OpenCV из репозитория CVS, необходимо получить доступ к каталогу CVSROOT: 
	:pserver:anonymous@opencvlibrary.cvs.sourceforge.net:2401/cvsroot/opencvlibrary

Для пользователей Linux необходимо ввести две команды:
	cvs -d:pserver:anonymous@opencvlibrary.cvs.sourceforge.net:/cvsroot/opencvlibrarylogin
Когда программа запросит пароль, нажмите возврат. Затем используйте следующее:
	cvs -z3 -d:pserver:anonymous@opencvlibrary.cvs.sourceforge.net:/cvsroot/opencvlibraryco -P opencv
