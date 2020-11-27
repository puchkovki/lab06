## Laboratory work VI [![Build Status](https://travis-ci.com/puchkovki/lab06-CPack.svg?branch=master)](https://travis-ci.com/puchkovki/lab06-CPack)

Данная лабораторная работа посвещена изучению средств пакетирования на примере **CPack**

```ShellSession
$ open https://cmake.org/Wiki/CMake:CPackPackageGenerators
```

## Tasks

- [x] 1. Создать публичный репозиторий с названием **lab06** на сервисе **GitHub**
- [x] 2. Выполнить инструкцию учебного материала
- [x] 3. Ознакомиться со ссылками учебного материала
- [x] 4. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial

```ShellSession
$ export GITHUB_USERNAME=puchkovki
$ export GITHUB_EMAIL=puchkov.k@phystech.edu
$ alias edit=vi
$ alias gsed=sed # for *-nix system
```

```ShellSession
$ cd ${GITHUB_USERNAME}/workspace
$ pushd .
/mnt/c/Users/dns/Documents/GitHub/puchkovki/workspace /mnt/c/Users/dns/Documents/GitHub/puchkovki/workspace/projects/lab05/third-party /mnt/c/Users/dns/Documents/GitHub/puchkovki/workspace/projects/lab05/third-party/gtest/reports/lab05 /mnt/c/Users/dns/Documents/GitHub/puchkovki/workspace/projects/lab05/third-party/gtest ~
$ source scripts/activate
```

```ShellSession
$ git clone https://github.com/${GITHUB_USERNAME}/lab05 projects/lab06
Cloning into 'projects/lab06'...
Unpacking objects: 100% (43/43), done.
$ cd projects/lab06
$ git remote remove origin
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab06
```

```ShellSession
# set the value of the environment variable defining project version
$ gsed -i '/project(print)/a\ #
set(PRINT_VERSION_STRING "v\${PRINT_VERSION}")
' CMakeLists.txt
$ gsed -i '/project(print)/a\
set(PRINT_VERSION\
  \${PRINT_VERSION_MAJOR}.\${PRINT_VERSION_MINOR}.\${PRINT_VERSION_PATCH}.\${PRINT_VERSION_TWEAK})
' CMakeLists.txt
$ gsed -i '/project(print)/a\
set(PRINT_VERSION_TWEAK 0)
' CMakeLists.txt
$ gsed -i '/project(print)/a\
set(PRINT_VERSION_PATCH 0)
' CMakeLists.txt
$ gsed -i '/project(print)/a\
set(PRINT_VERSION_MINOR 1)
' CMakeLists.txt
$ gsed -i '/project(print)/a\
set(PRINT_VERSION_MAJOR 0)
' CMakeLists.txt
$ git diff
diff --git a/CMakeLists.txt b/CMakeLists.txt
index aa7a323..71b64e3 100644
--- a/CMakeLists.txt
+++ b/CMakeLists.txt
@@ -7,6 +7,13 @@ option(BUILD_EXAMPLES "Build examples" OFF)
 option(BUILD_TESTS "Build tests" OFF)

 project(print)
+set(PRINT_VERSION_MAJOR 0)
+set(PRINT_VERSION_MINOR 1)
+set(PRINT_VERSION_PATCH 0)
+set(PRINT_VERSION_TWEAK 0)
+set(PRINT_VERSION
+  ${PRINT_VERSION_MAJOR}.${PRINT_VERSION_MINOR}.${PRINT_VERSION_PATCH}.${PRINT_VERSION_TWEAK})
+set(PRINT_VERSION_STRING "v${PRINT_VERSION}")

 add_library(print STATIC ${CMAKE_CURRENT_SOURCE_DIR}/sources/print.cpp)
```

```ShellSession
$ touch DESCRIPTION && edit DESCRIPTION # add specific generator files for the rpm package
$ touch ChangeLog.md # create file
$ export DATE="`LANG=en_US date +'%a %b %d %Y'`"
$ cat > ChangeLog.md <<EOF # add release information
* ${DATE} ${GITHUB_USERNAME} <${GITHUB_EMAIL}> 0.1.0.0
- Initial RPM release
EOF
```

```ShellSession
$ cat > CPackConfig.cmake <<EOF # add meta-information
include(InstallRequiredSystemLibraries)
EOF
```

```ShellSession
$ cat >> CPackConfig.cmake <<EOF # add meta-information for the packages
set(CPACK_PACKAGE_CONTACT ${GITHUB_EMAIL})
set(CPACK_PACKAGE_VERSION_MAJOR \${PRINT_VERSION_MAJOR})
set(CPACK_PACKAGE_VERSION_MINOR \${PRINT_VERSION_MINOR})
set(CPACK_PACKAGE_VERSION_PATCH \${PRINT_VERSION_PATCH})
set(CPACK_PACKAGE_VERSION_TWEAK \${PRINT_VERSION_TWEAK})
set(CPACK_PACKAGE_VERSION \${PRINT_VERSION})
set(CPACK_PACKAGE_DESCRIPTION_FILE \${CMAKE_CURRENT_SOURCE_DIR}/DESCRIPTION)
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "Static C++ library for printing")
EOF
```

```ShellSession
$ cat >> CPackConfig.cmake <<EOF # add meta-information for the resources

set(CPACK_RESOURCE_FILE_LICENSE \${CMAKE_CURRENT_SOURCE_DIR}/LICENSE)
set(CPACK_RESOURCE_FILE_README \${CMAKE_CURRENT_SOURCE_DIR}/README.md)
EOF
```

```ShellSession
$ cat >> CPackConfig.cmake <<EOF # add meta-information for the rpm generator

set(CPACK_RPM_PACKAGE_NAME "print-devel")
set(CPACK_RPM_PACKAGE_LICENSE "MIT")
set(CPACK_RPM_PACKAGE_GROUP "print")
set(CPACK_RPM_CHANGELOG_FILE \${CMAKE_CURRENT_SOURCE_DIR}/ChangeLog.md)
set(CPACK_RPM_PACKAGE_RELEASE ${WDC_VERSION_TWEAK})
EOF
```

```ShellSession
$ cat >> CPackConfig.cmake <<EOF # add meta-information for the debian package

set(CPACK_DEBIAN_PACKAGE_NAME "libprint-dev")
set(CPACK_DEBIAN_PACKAGE_PREDEPENDS "cmake >= 3.0")
set(CPACK_DEBIAN_PACKAGE_RELEASE ${WDC_VERSION_TWEAK})
EOF
```

```ShellSession
$ cat >> CPackConfig.cmake <<EOF # add cpack-module

include(CPack)
EOF
```

```ShellSession
$ cat >> CMakeLists.txt <<EOF # add cpack to the building process

include(CPackConfig.cmake)
EOF
```

```ShellSession
$ gsed -i 's/lab05/lab06/g' README.md
```

```ShellSession
$ git add .
$ git commit -m "added cpack config"
[master 2075a73] added cpack config
 5 files changed, 37 insertions(+), 1 deletion(-)
 create mode 100644 CPackConfig.cmake
 create mode 100644 ChangeLog.md
 create mode 100644 DESCRIPTION
$ git tag v0.1.0.0 # add a tag reference 
$ git push origin master --tags # push with tags
Username for 'https://github.com': puchkovki
Password for 'https://puchkovki@github.com':
Counting objects: 50, done.
remote: Resolving deltas: 100% (14/14), done.
To https://github.com/puchkovki/lab06
 * [new branch]      master -> master
 * [new tag]         v0.1.0.0 -> v0.1.0.0
```

```ShellSession
$ travis login --auto --pro
Successfully logged in as puchkovki!
$ travis enable --pro
Detected repository as puchkovki/lab06, is this correct? |yes| yes
puchkovki/lab06: enabled :)
```

```ShellSession
$ cmake -H. -B_build # generate projects files
-- The C compiler identification is GNU 7.4.0
-- The CXX compiler identification is GNU 7.4.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: /mnt/c/Users/dns/Documents/GitHub/puchkovki/workspace/projects/lab06/_build
$ cmake --build _build # build the project
Scanning dependencies of target print
[ 50%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[100%] Linking CXX static library libprint.a
[100%] Built target print
$ cd _build # move to the directory with Makefile
$ cpack -G "TGZ" # generate tgz package
CPack: Create package using TGZ
CPack: Install projects
CPack: - Run preinstall target for: print
CPack: - Install project: print
CPack: Create package
CPack: - package: /mnt/c/Users/dns/Documents/GitHub/puchkovki/workspace/projects/lab06/_build/print-0.1.0.0-Linux.tar.gz generated.
$ cd ..
```

```ShellSession
#another way to build the project using cmake
$ cmake -H. -B_build -DCPACK_GENERATOR="TGZ" #choose generator
-- Configuring done
-- Generating done
-- Build files have been written to: /mnt/c/Users/dns/Documents/GitHub/puchkovki/workspace/projects/lab06/_build
$ cmake --build _build --target package #set an impilicit target
[100%] Built target print
Run CPack packaging tool...
CPack: Create package using TGZ
CPack: Install projects
CPack: - Run preinstall target for: print
CPack: - Install project: print
CPack: Create package
CPack: - package: /mnt/c/Users/dns/Documents/GitHub/puchkovki/workspace/projects/lab06/_build/print-0.1.0.0-Linux.tar.gz generated.
```

```ShellSession
$ mkdir artifacts
$ mv _build/*.tar.gz artifacts
$ tree artifacts
artifacts
└ print-0.1.0.0-Linux.tar.gz
```

## Report

```ShellSession
$ popd
/mnt/c/Users/dns/Documents/GitHub/puchkovki/workspace/projects/lab06/_build /mnt/c/Users/dns/Documents/GitHub/puchkovki/workspace /mnt/c/Users/dns/Documents/GitHub/puchkovki/workspace/projects/lab05/third-party /mnt/c/Users/dns/Documents/GitHub/puchkovki/workspace/projects/lab05/third-party/gtest/reports/lab05 /mnt/c/Users/dns/Documents/GitHub/puchkovki/workspace/projects/lab05/third-party/gtest ~
$ export LAB_NUMBER=06
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
Cloning into 'tasks/lab06'...
Resolving deltas: 100% (31/31), done.
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gist-paste REPORT.md
```

## Homework

После того, как вы настроили взаимодействие с системой непрерывной интеграции,</br>
обеспечив автоматическую сборку и тестирование ваших изменений, стоит задуматься</br>
о создание пакетов для измениний, которые помечаются тэгами (см. вкладку [releases](https://github.com/tp-labs/lab06/releases)).</br>
Пакет должен содержать приложение _solver_ из [предыдущего задания](https://github.com/tp-labs/lab03#задание-1)
Таким образом, каждый новый релиз будет состоять из следующих компонентов:
- архивы с файлами исходного кода (`.tar.gz`, `.zip`)
- пакеты с бинарным файлом _solver_ (`.deb`, `.rpm`, `.msi`, `.dmg`)

В качестве подсказки:
```bash
$ cat .travis.yml
os: osx
script:
...
- cpack -G DragNDrop # dmg

$ cat .travis.yml
os: linux
script:
...
- cpack -G DEB # deb

$ cat .travis.yml
os: linux
addons:
  apt:
    packages:
    - rpm
script:
...
- cpack -G RPM # rpm

$ cat appveyor.yml
platform:
- x86
- x64
build_script:
...
- cpack -G WIX # msi
```

Для этого нужно добавить ветвление в конфигурационные файлы для **CI** со следующей логикой:</br>
если **commit** помечен тэгом, то необходимо собрать пакеты (`DEB, RPM, WIX, DragNDrop, ...`) </br>
и разместить их на сервисе **GitHub**. (см. пример для [Travi CI](https://docs.travis-ci.com/user/deployment/releases))</br>

## Links

- [DMG](https://cmake.org/cmake/help/latest/module/CPackDMG.html)
- [DEB](https://cmake.org/cmake/help/latest/module/CPackDeb.html)
- [RPM](https://cmake.org/cmake/help/latest/module/CPackRPM.html)
- [NSIS](https://cmake.org/cmake/help/latest/module/CPackNSIS.html)

```
Copyright (c) 2015-2019 The ISC Authors
```
