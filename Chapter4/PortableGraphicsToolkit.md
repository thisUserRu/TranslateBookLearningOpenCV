## [П]|[РС]|(РП) Набор графических инструментов

Функции OpenCV, которые позволяют взаимодействовать с операционной системой, файловой системой и аппаратными средствами, такими, как камера, собраны в библиотеке **HighGUI** (что означает "высокоуровневый графический пользовательский интерфейс"). **HighGUI** позволяет открывать окна для отображения изображений, читать и записывать графические файлы (изображения и видео), обрабатывать простые события мыши, указателя и клавиатуры. Данная библиотека также позволяет создавать такие полезные элементы, как ползунок. **HighGUI** имеет достаточный функционал для разработки различного рода приложений. При этом наибольшая польза от использования данной библиотеки в её кроссплатформенности. 

библиотека HighGUI состоит из трех частей: аппаратной, файловой и GUI. 

Аппаратная часть в первую очередь касается работы с камерой. В большинстве ОС обработка камеры довольно таки утомительна. **HighGUI** предоставляет простые механизмы подключения и последующего получения изображения с камеры. 

Все, что касается файловой системы, в первую очередь связано с загрузкой и сохранением изображения. Приятной особенностью библиотеки является наличие методов, которые одинаково обрабатывают видеопоток и из файла, и с камеры. Та же идея (относительно) заложена и в методы обработки изображений. Функции просто полагаются на расширения файлов и автоматически обрабатывают все операции по кодированию и декодированию изображений. 

Третья часть HighGUI - GUI. Библиотека предоставляет несколько простых функций, которые позволяют открывать окно и отображать в нем изображения. Тут же (в окне) существует возможность обрабатывать события, поступившие от мыши и клавиатуры. 

По мере продвижения по главе, данные части не будут рассматриваться по отдельности. В начале будут рассмотрены самые полезные функции в общем; а уже потом будет происходить постепенное погружение в различные детали.

