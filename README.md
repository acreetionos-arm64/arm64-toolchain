# arm64-toolchain

> Cross-compilation environment and toolchain setup for ARM64 development

## Repository Purpose

This repository provides the ARM64 cross-compilation environment, toolchain configuration, and development setup required for building AcreetionOS components on x86_64 hosts targeting ARM64 architecture. It serves as the foundational infrastructure enabling all other submodules to perform ARM64 cross-compilation.

This repository is part of the AcreetionOS ARM64 multi-repository workspace, providing cross-compilation infrastructure for the overall ARM64 port project.

## Architecture Context

arm64-toolchain integrates with the AcreetionOS ARM64 workspace as follows:

- **Primary Role**: Cross-compilation environment setup and toolchain management
- **Dependencies**: Host system package managers, upstream toolchain packages
- **Integration Points**: All submodules requiring ARM64 compilation (iso-builder, custom-packages, package-builder)
- **Upstream Relationship**: GNU toolchain, binutils, libc for ARM64 architecture

### Related Repositories
- **[custom-packages](https://github.com/acreetionos-arm64/custom-packages)** - Requires toolchain for Qt/KDE cross-compilation
- **[package-builder](https://github.com/acreetionos-arm64/package-builder)** - Uses toolchain for automated package building
- **[iso-builder](https://github.com/acreetionos-arm64/iso-builder)** - Requires toolchain for package compilation
- **[boot-systems](https://github.com/acreetionos-arm64/boot-systems)** - Uses toolchain for bootloader compilation
- **[hardware-support](https://github.com/acreetionos-arm64/hardware-support)** - Requires toolchain for driver compilation

## Development Status

**Current Milestone**: Milestone 0 - Infrastructure Setup
**Completion Status**: 🔄 Cross-compilation environment setup in progress

### Milestone Progress
- ✅ Repository structure established
- 🔄 Cross-compilation toolchain installation scripts
- 📋 Planned: Automated environment validation and testing
- 📋 Planned: Docker containerization for consistent environments
- 📋 Planned: Integration with package-builder automation

## Quick Start Guide

### Prerequisites
- Linux or macOS development environment (x86_64 host)
- Package manager (apt, brew, pacman, etc.)
- Internet connection for toolchain downloads
- At least 5GB free disk space for toolchain installation
- Administrator privileges for system package installation

### Setup Instructions
```bash
# Clone with workspace context
cd /path/to/acreetionos-arm64/workspace
git submodule update --init --recursive
cd arm64-toolchain/

# Install ARM64 cross-compilation toolchain
./install-toolchain.sh

# Set up development environment
source setup-cross-env.sh

# Validate installation
./validate-toolchain.sh
```

### Basic Usage
```bash
# Source cross-compilation environment
source /path/to/arm64-toolchain/setup-cross-env.sh

# Verify toolchain is active
echo $CC          # Should show aarch64-linux-gnu-gcc
echo $CXX         # Should show aarch64-linux-gnu-g++
echo $TARGET_ARCH # Should show aarch64

# Test cross-compilation
aarch64-linux-gnu-gcc --version
aarch64-linux-gnu-gcc test.c -o test-arm64
file test-arm64   # Should show ARM64 architecture
```

## Directory Structure

```
arm64-toolchain/
├── scripts/
│   ├── install-toolchain.sh      # Toolchain installation automation
│   ├── setup-cross-env.sh        # Environment setup script
│   ├── validate-toolchain.sh     # Installation validation
│   └── uninstall-toolchain.sh    # Cleanup script
├── configs/
│   ├── cmake/
│   │   └── arm64-toolchain.cmake  # CMake toolchain file
│   ├── autotools/
│   │   └── arm64-config.site      # Autotools cross-compilation
│   └── pkg-config/
│       └── arm64.pc               # pkg-config settings
├── sysroot/
│   └── aarch64-linux-gnu/         # Target system root (populated)
├── docker/
│   ├── Dockerfile.arm64-dev       # Development container
│   └── docker-compose.yml         # Container orchestration
└── docs/
    ├── troubleshooting.md         # Common issues and solutions
    ├── platform-specific.md       # Platform-specific instructions
    └── integration-guide.md       # Integration with other submodules
```

### Key Files and Directories
- **scripts/**: Automation scripts for toolchain management and environment setup
- **configs/**: Build system configuration files for different compilation frameworks
- **sysroot/**: ARM64 target system root with libraries and headers
- **docker/**: Containerized development environment for consistency
- **docs/**: Comprehensive documentation for setup and troubleshooting

## Development Workflow

### Making Changes
1. **Identify toolchain requirements**: Determine what ARM64 packages and libraries are needed
2. **Update installation scripts**: Modify installation automation for new requirements
3. **Test on multiple platforms**: Validate changes on different host operating systems
4. **Update documentation**: Document any new dependencies or configuration changes
5. **Validate integration**: Test with dependent submodules (custom-packages, iso-builder)

### Testing Changes
- **Multi-platform testing**: Validate on Ubuntu, Fedora, macOS, and other supported hosts
- **Cross-compilation validation**: Test actual ARM64 binary generation
- **Integration testing**: Verify compatibility with dependent submodules
- **Container testing**: Validate Docker environment consistency

### Contributing
1. Focus on host platform compatibility and automated installation
2. Ensure consistent behavior across different Linux distributions
3. Test integration with Qt/KDE development for custom-packages needs
4. Document platform-specific quirks and solutions

## Dependencies

### System Requirements
- **Linux**: Ubuntu 20.04+, Fedora 35+, or equivalent distribution
- **macOS**: macOS 11+ with Xcode command line tools
- **Windows**: WSL2 with supported Linux distribution
- **Resources**: 4GB+ RAM, 10GB+ disk space for full toolchain

### External Dependencies
- **Build essentials**: gcc, g++, make, cmake, autotools
- **Cross-compilation packages**: gcc-aarch64-linux-gnu, binutils-aarch64-linux-gnu
- **Development libraries**: libc6-dev-arm64-cross, linux-libc-dev-arm64-cross
- **Additional tools**: pkg-config, curl, wget, git

### Submodule Dependencies
- **None**: This is a foundational repository that other submodules depend on
- **Provides to**: custom-packages (Qt cross-compilation), package-builder (automation), iso-builder (package compilation)

## Testing Instructions

### Unit Testing
- Toolchain installation script validation on supported platforms
- Cross-compilation environment variable configuration testing
- Binary generation and architecture verification
- CMake and autotools configuration file validation

### Integration Testing
- Cross-compilation of simple C/C++ programs
- Qt/KDE framework cross-compilation validation
- Integration with makepkg and Arch Linux package building
- Docker environment consistency validation

### Validation
- ARM64 binary output verification using `file` and `objdump`
- Library dependency resolution in cross-compilation sysroot
- Performance testing of cross-compilation vs. native compilation
- Multi-platform consistency validation

## Build/Deployment

### Toolchain Installation
```bash
# Automated installation on Ubuntu/Debian
sudo ./scripts/install-toolchain.sh --platform=ubuntu

# Manual installation verification
./scripts/validate-toolchain.sh --verbose

# Docker environment setup
cd docker/
docker-compose up -d arm64-dev
docker exec -it arm64-dev bash
```

### Environment Configuration
```bash
# Persistent environment setup
echo "source /path/to/arm64-toolchain/setup-cross-env.sh" >> ~/.bashrc

# Project-specific environment
export ARM64_TOOLCHAIN_PATH=/path/to/arm64-toolchain
export CMAKE_TOOLCHAIN_FILE=$ARM64_TOOLCHAIN_PATH/configs/cmake/arm64-toolchain.cmake
```

### Cross-compilation Testing
```bash
# Test C compilation
echo 'int main(){return 0;}' > test.c
$CC test.c -o test-arm64
file test-arm64  # Verify ARM64 architecture

# Test C++ compilation with libraries
$CXX -std=c++17 test.cpp -o test-cpp-arm64 -lpthread
ldd test-cpp-arm64  # Check ARM64 library dependencies
```

## Upstream Coordination

### GitLab CE Mirroring
- **Status**: 📋 Planned coordination for toolchain version synchronization
- **Purpose**: Consistent toolchain versions across development environments
- **Approach**: Version tracking and automated updates

### Upstream Relationships
- **GNU Toolchain**: GCC, binutils, glibc for ARM64 architecture
- **Distribution Packages**: Ubuntu/Debian ARM64 cross-compilation packages
- **Container Images**: Official ARM64 development container images

### Contribution Upstream
- Improvements to cross-compilation setup and automation
- Documentation and guides for ARM64 development environments
- Integration patterns for multi-repository development workflows

## Troubleshooting

### Common Issues
- **Toolchain not found**: Ensure installation completed successfully and PATH is configured
- **Library linking errors**: Verify sysroot configuration and ARM64 library availability
- **CMake configuration errors**: Check CMAKE_TOOLCHAIN_FILE path and content
- **Qt cross-compilation failures**: Ensure Qt ARM64 development packages installed

### Debug Information
```bash
# Environment validation
./scripts/validate-toolchain.sh --debug

# Cross-compilation debugging
export VERBOSE=1
$CC -v test.c -o test-debug 2>&1 | tee compilation-debug.log

# Library and dependency analysis
aarch64-linux-gnu-objdump -p binary-file
aarch64-linux-gnu-readelf -d binary-file
```

### Getting Help
- **GitHub Issues**: Use repository-specific issue templates for toolchain problems
- **Documentation**: Check docs/ directory for platform-specific guides
- **Integration Support**: Coordinate with dependent submodule maintainers
- **Community Resources**: ARM development communities and cross-compilation guides

## Project Context

- **AcreetionOS ARM64 Workspace**: [Main Repository](https://github.com/acreetionos-arm64/workspace)
- **Project Documentation**: [Documentation Repository](https://github.com/acreetionos-arm64/documentation)
- **Issue Tracking**: [GitHub Issues](https://github.com/acreetionos-arm64/arm64-toolchain/issues)
- **Development Coordination**: See [CLAUDE.md](./CLAUDE.md) for AI development context

## License

MIT License (organization repositories)

Individual toolchain components maintain their respective licenses (GPL for GCC, etc.).

---

*This repository is part of the AcreetionOS ARM64 port project - a sustainable side project focused on learning Linux distribution development while creating a functional ARM64 port of AcreetionOS.*