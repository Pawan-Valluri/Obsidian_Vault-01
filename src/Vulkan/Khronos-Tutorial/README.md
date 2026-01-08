# Khronos Vulkan<sup>®</sup> Tutorial

## Attribution

The Khronos Vulkan<sup>®</sup> Tutorial is based on the "[Vulkan Tutorial](https://vulkan-tutorial.com/)" by Alexander Overvoorde licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).

## About

This repository hosts the contents of the [Khronos Vulkan Tutorial](https://docs.vulkan.org/tutorial/latest). The tutorial is part of the [Vulkan Documentation Project](https://github.com/KhronosGroup/Vulkan-Site).

## Differences

Compared to the original tutorial, this version of the tutorial is teaching up-to-date concepts:

* Vulkan 1.4 as a baseline
* Dynamic rendering instead of render passes
* Timeline semaphores
* [Slang](https://shader-slang.org/) as the primary shading language
* Modern C++ (20) with modules
* [Vulkan-Hpp](https://github.com/KhronosGroup/Vulkan-Hpp) with [RAII](https://en.wikipedia.org/wiki/Resource_acquisition_is_initialization)

It also contains Vulkan usage clarifications, improved synchronization and new content.

## Project Structure

The repository is organized into several important directories:

* `en/` - Contains the tutorial content in English, organized by chapters
  * The main tutorial covers fundamental Vulkan concepts (chapters 00-17)
  * The "Building a Simple Engine" section builds upon these fundamentals to create a structured rendering engine
* `attachments/` - Contains code examples, shader files, and resources used in the tutorial
* `images/` - Contains illustrations, diagrams, and screenshots used in the tutorial
* `scripts/` - Contains utility scripts, including dependency installation scripts
* `antora/` - Contains configuration files for the Antora documentation system

## Installation Dependencies

The project provides scripts to help you install the necessary dependencies:

### Linux
Run the following command to install dependencies on Linux:

```bash
./scripts/install_dependencies_linux.sh
```

This script detects your package manager (apt, dnf, pacman) and installs the required dependencies, including:
* Build essentials (gcc, cmake, ninja-build)
* GLFW, GLM, tinyobjloader, stb
* X Window System dependencies

It also provides instructions for installing the Vulkan SDK.

### Windows
Run the following command to install dependencies on Windows:

```batch
scripts\install_dependencies_windows.bat
```

This script uses vcpkg to install the required dependencies, including:
* GLFW, GLM, tinyobjloader, stb

You will also need to install the Vulkan SDK separately from https://vulkan.lunarg.com/.

## License

The contents of this repository are licensed as [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/), unless stated otherwise.
By contributing to this repository, you agree to license your contributions to the public under that same license.

The code listings in the `attachments` directory are licensed as
[CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/).
By contributing to that directory, you agree to license your contributions to the public under that same public domain-like license.

## Contributing

Contributions are welcome, details on how to contribute and the technical requirements that have to be met can be found in [this document](CONTRIBUTING.md).
