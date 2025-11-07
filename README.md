# Example of a Conan project using the Sonar Build Wrapper
The base project is copied from https://github.com/conan-io/examples2/tree/main/tutorial/consuming_packages/conanfile_py.

The instructions below assume a working conan/CMake/C environment, a SonarQube instance, a Sonar Build Wrapper and a SonarScanner CLI.

Note that this example is written in a SonarQube 9.9-style (Build Wrapper and `sonar.cfamily.build-wrapper-output` property). In modern versions of SonarQube (2025.4 LTA at the time of writing), the [preferred way of analyzing](https://docs.sonarsource.com/sonarqube-server/2025.4/analyzing-source-code/languages/c-family/running-the-analysis) is not Build Wrapper, but Compilation Database, and `sonar.cfamily.build-wrapper-output` is deprecated.

## Building with CMake
The following instructions are derived from the [tutorial](https://docs.conan.io/2/tutorial/consuming_packages/the_flexibility_of_conanfile_py.html). I've added the Build Wrapper and the SonarScanner CLI command.
```
rm -rf build bw-out
conan install . --output-folder build --build=missing
cd build
source conanbuild.sh
cmake .. -DCMAKE_TOOLCHAIN_FILE=conan_toolchain.cmake -DCMAKE_BUILD_TYPE=Release
build-wrapper-linux-x86-64 --out-dir ../bw-out cmake --build .
./compressor
source deactivate_conanbuild.sh
cd ..

sonar-scanner -Dsonar-host.url=<sonarqube_url> -Dsonar.token=<token> -Dsonar.cfamily.build-wrapper-output=bw-out
```

## Bulding with `conan build`
`conan build` is a shortcut to install dependencies and compile the project. The following instructions show how to use the Build Wrapper together with `conan build` and how to analyze the project with SonarScanner CLI.
```
rm -rf build bw-out
build-wrapper-linux-x86-64 --out-dir bw-out conan build --output-folder build .
./build/compressor

sonar-scanner -Dsonar-host.url=<sonarqube_url> -Dsonar.token=<token> -Dsonar.cfamily.build-wrapper-output=bw-out
```