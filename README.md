# Лабораторная работа №9

## Выполнил - студент группы ИУ8-22 Мельников Егор

## Homework

*Порядок работы*

1. Загружаем из [предыдущей лаборатоной](https://github.com/jo22i/lab08) необходимые файлы проекта.

2. Меняем немного исходники что бы они подходили под специфику данной лаборатороной.

```cpp
#include "../src/print.hpp"

#include <fstream>

int main(int argc, char** argv)
{
  std::ofstream file("log.txt");
  print(std::string("hello2"), file);
  std::cout << "End of example2 program!!" << std::endl;
}
```

3. Немного меняем конфигурацию *CMakeLists.txt* для корректной работы с *CPack*.

```cmake
cmake_minimum_required(VERSION 3.16)

project(examples)

set(CMAKE_CXX_STANDART 11)
set(CMAKE_CXX_STANDART_REQUIRED ON)

include_directories("../src")

add_executable(example1 ${CMAKE_CURRENT_SOURCE_DIR}/example1.cpp)
add_executable(example2 ${CMAKE_CURRENT_SOURCE_DIR}/example2.cpp)

add_library(print STATIC "../src/print.cpp")

target_link_libraries(example1 print)
target_link_libraries(example2 print)

include("../CPackConfig.cmake")
```

4. Создаём *CPackConfig.cmake* для возможности пакетирования нашего проекта.

```cmake
install(TARGETS example1 example2)
include(InstallRequiredSystemLibraries)

set(CPACK_PACKAGE_NAME "print")
set(CPACK_PACKAGE_VERSION_MAJOR 0)
set(CPACK_PACKAGE_VERSION_MINOR 1)
set(CPACK_PACKAGE_VERSION_PATCH 0)
set(CPACK_PACKAGE_VERSION_TWEAK 0)
set(CPACK_PACKAGE_VERSION \${PRINT_VERSION})

set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "static C++ library for printing")

set(CPACK_ARCHIVE_FILE_NAME "print")

set(CPACK_RPM_PACKAGE_NAME "print")
set(CPACK_RPM_PACKAGE_GROUP "labs")
set(CPACK_RPM_PACKAGE_VERSION \${PRINT_VERSION})
set(CPACK_RPM_PACKAGE_RELEASE 1)

include(CPack)
```

5. Собираем и пакетируем проект, собранный архив выносим в главную директорию, а побочную папку _ удаляем из-за ненадобности.

```sh
$ cmake -H. -B_build 
$ cmake --build _build
$ cd _build
$ cpack -G "TGZ"
$ mv print--Linux.tar.gz ../..
```

6. Устанавливаем *GitHub CLI* по [инструкции](https://github.com/cli/cli#installation) для возможности создания релиза на локальной машине.

7. Авторизируемся в сервисе, делаем тег на коммите, пушим его на удалённый репозиторий, и по тегу создаём релиз.

```sh
$ gh auth login
$ git add .
$ git commit -m "Start"
$ git push origin master --follow-tags
$ git tag -a v0.1.0 -m "Version 0.1.0 release"
$ gh release create v0.1.0 './print--Linux.tar.gz#print examples'
```
