# Base CPP Project
This is a **vscode base project** that can be used to build almost any cpp project. The main idea is to copy the folders, and run some commands to build the code.

## ✅Prerequisites
- VSCode
- **Dev Containers** extension (vscode extension)
- Docker

## 📃Steps
1. **Open this project with vscode** (if *dev containers* asks automatically to build the container, build it!)
2. **Copy all the folders with their files** into the main folder of this project. 
- src     (.cpp, .cc)
- include (.h, .hpp, .hh)
- test    (.cpp)
- docs    (Doxygen/, img/, pages/)
- proto   (.proto)
3. **Open the project in the container** (*if it was not done in step 1*)
- `CTRL + SHIFT + P`
- Search and select **Dev Containers: Rebuild and Reopen in Container**
> **⚠️ Important:** If the Container is already built, search and select **Dev Containers: Reopen in Container**

4. **Generate a conan profile**
```sh
conan profile detect
```

5. **Install dependencies** an generate the build folder
```sh
conan install . --build=missing
```

6. **Go** to the **build folder**
```sh
cd build
```

7. **Generate** the **project files**
```sh
cmake .. -DCMAKE_TOOLCHAIN_FILE=Release/generators/conan_toolchain.cmake  -DCMAKE_POLICY_DEFAULT_CMP0091=NEW -DCMAKE_BUILD_TYPE=Release -DCMAKE_EXPORT_COMPILE_COMMANDS=ON
```

8. **Build** the project
```sh
cmake --build . -j8
```

If everything worked properly you should have:
- docs/**generated** folder
- build/bin/**cpp_build** binary (executable)
- build/bin/**cpp_test** binary (executable)
