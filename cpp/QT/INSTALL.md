# Что это?

QT ("кью-ты") - фреймворк для разработки кроссплатформенного программного обеспечения на языке программирования C++. На самом деле не только для C++.

# Зависимости

Для `Linux` нужен `make`, `gcc` и `g++`.

Для `Windows` нужен `MinGW` вместе с `make`. Установка [MinGW на Windows <-- тык](https://github.com/The220th/SharedLib/tree/main/cpp/Windows/MinGW). 

# Установка

Рекомендуется узнать про [aqtinstall (aqt, another qt installer)](https://github.com/miurahr/aqtinstall). Именно он будет по большей части использоваться при установке.

Отдельно будет рассмотрена установка:

- для `Linux` [<-- тык](#linux)

- для `Windows` [<-- тык](#windows)

## Linux

Установите `aqt`:

``` bash
> python3 -m pip install --upgrade pip

> pip3 install aqtinstall
```

Просмотр и выбор необходимой версии:

``` bash
> aqt list-qt linux desktop
```

Пусть необходимо установить версию `6.2.0`.

Теперь нужно выбрать "архитектуру":

``` bash
> aqt list-qt linux desktop --arch 6.2.0
```

Скорее всего это будет `gcc_64`.

Установка:

``` bash
> aqt install-qt linux desktop 6.2.0 gcc_64 # Установка в текущую директорию

#Можно явным образом указать директорию установки
> aqt install-qt --outputdir /path/to/install/dir desktop 6.2.0 gcc_64 # где-то 730 MB

# Можно установить также все модули
> aqt install-qt --outputdir /path/to/install/dir linux desktop 6.2.0 gcc_64 -m all # где-то 5.8 GB
```

Если нужны ещё дополнительные модули, то можете узнать доступные:

```bash
> aqt list-qt linux desktop --modules 6.2.0 gcc_64
```

И установить их:

``` bash
> aqt install-qt linux desktop 6.2.0 gcc_64 -m module_1 module_n
```

Теперь добавьте строку к конец файла `~/.bashrc`:

``` bash
...

export PATH="/path/to/install/dir/6.2.0/gcc_64/bin:$PATH"
```

Проверьте, что всё работает:

``` bash
> qmake --version

#QMake version [version]
#Using Qt version 6.2.0 in /path/to/#install/dir/6.2.0/gcc_64/lib

# Можете вывести подробную конфигурацию:
> qmake -query
```

## Windows

Установите `aqt`:

``` bash
> python -m pip install --upgrade pip

> pip install aqtinstall
```

Просмотр и выбор необходимой версии:

``` bash
> aqt list-qt windows desktop
```

Пусть необходимо установить версию `6.2.0`.

Теперь нужно выбрать "архитектуру":

``` bash
> aqt list-qt windows desktop --arch 6.2.0
```

Конечно, выбираем тот, что с `mingw`. Например, `win64_mingw81`.

Установка:

``` bash
> aqt install-qt windows desktop 6.2.0 win64_mingw81 # Установка в текущую директорию

#Можно явным образом указать директорию установки
> aqt install-qt --outputdir C:\path\to\install\dir windows desktop 6.2.0 win64_mingw81

# Можно установить также все модули
> aqt install-qt --outputdir C:\path\to\install\dir windows desktop 6.2.0 win64_mingw81 -m all
```

Если нужны ещё дополнительные модули, то можете узнать доступные:

```bash
> aqt list-qt windows desktop --modules 6.2.0 win64_mingw81
```

И установить их:

``` bash
> aqt install-qt windows desktop 6.2.0 win64_mingw81 -m module_1 module_n
```

Теперь нужно добавить путь к папке `C:\path\to\install\dir\6.2.0\mingw81_64\bin` в переменную среды `PATH`.

Проверьте, что всё работает:

``` bash
> qmake --version

#QMake version [version]
#Using Qt version 6.2.0 in C:/path/to/install/dir/6.2.0/mingw81_64/lib

# Можете вывести подробную конфигурацию:
> qmake -query
```

# Пример компиляции

Создайте файл `HELLO.h`:

``` cpp
#ifndef __HELLO_H

#define __HELLO_H

#include <string>

const std::string HELLOTEXT = "Hello world!";

#endif
```

Создайте файл `main.cpp`:

``` cpp
#include <iostream>
#include <string>

#include <QApplication>
#include <QPushButton>

#include "HELLO.h"

using namespace std;

void buttonFunc();

int main(int argc, char **argv)
{
    QApplication app(argc, argv);

    QPushButton button(HELLOTEXT.c_str());
    button.show();

    QPushButton *lpBtn = &button;
    QObject::connect(lpBtn, &QPushButton::released, [=]()
    {
        buttonFunc();
        string buffS("\"" + HELLOTEXT + "\", but pushed.");
        lpBtn->setText(buffS.c_str());
        lpBtn->resize(175, 50);
    });

    return app.exec();
}

void buttonFunc()
{
    cout << "pushed" << endl;
}
```

`pro-файл` - это файл, который содержит информацию о проекте для `qmake`. [Подробнее](https://doc.qt.io/qt-5/qmake-project-files.html).

Создайте файл `test.pro`:

``` bash
QT += core gui

QT += widgets

#CONFIG += c++11
TARGET = nameOfTest # название бинарника
TEMPLATE = app


SOURCES += ./main.cpp

HEADERS  += ./HELLO.h
```

Сгенерируйте файл `Makefile`:

``` bash
> qmake test.pro
```

И скомпилируйте:

``` bash
> make
```

И запустите:

``` bash
> ./nameOfTest # unix-like and unix

> nameOfTest.exe # windows
```

# Настройка `vscode`, `codium`

Установите расширение `C/C++ for Visual Studio Code`.

Если используете `codium`, то скачайте [релиз](https://github.com/microsoft/vscode-cpptools/releases), и установите как vsix-пакет.

![... -> Install from VSIX...](https://i.imgur.com/2zBOJp9.png)

После этого в корне проекта в директории `.vscode` в файле `c_cpp_properties.json` добавьте в `includePath` путь к тем QT-модулям, которые вы будете использовать. Например, файл `c_cpp_properties.json` может выглядеть так:

``` json
{
    "configurations": [
        {
            "name": "AnyName",
            "includePath": [
                "/path/to/install/dir/6.2.0/gcc_64/include/QtWidgets",
                "/path/to/install/dir/6.2.0/gcc_64/include/QtCore",
                "/path/to/install/dir/6.2.0/gcc_64/include/QtGui",
                "${default}"
            ],
            "defines": [
                "${default}"
            ],
            "macFrameworkPath": [
                "${default}"
            ],
            "forcedInclude": [
                "${default}"
            ],
            "compileCommands": "${default}",
            "browse": {
                "limitSymbolsToIncludedHeaders": true,
                "databaseFilename": "${default}",
                "path": [
                    "${default}"
                ]
            },
            "intelliSenseMode": "${default}",
            "cStandard": "${default}",
            "cppStandard": "${default}",
            "compilerPath": "${default}"
        }
    ],
    "version": 4
}

```

На windows строка `"/path/to/install/dir/6.2.0/gcc_64/include/QtCore"` выглядела бы так `"C:/path/to/install/dir/6.2.0/gcc_64/include/QtCore"`. То есть "слеши", нормельные, а не виндовские.