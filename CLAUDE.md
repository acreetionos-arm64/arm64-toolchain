# CLAUDE.md - arm64-toolchain AI Development Context

This file provides AI development context and guidance for working with the arm64-toolchain submodule of the AcreetionOS ARM64 workspace.

## Repository Context

**Domain**: Cross-compilation Toolchain and Development Environment
**Primary Technologies**: GCC cross-compiler, GNU binutils, CMake, Docker, shell scripting
**Development Focus**: ARM64 cross-compilation infrastructure for x86_64 host systems

### Repository Purpose
The arm64-toolchain repository serves as the foundational infrastructure for the entire AcreetionOS ARM64 project, providing cross-compilation capabilities that enable building ARM64 binaries on x86_64 development machines. This includes toolchain installation automation, environment configuration, build system integration files, and validation tools to ensure consistent cross-compilation across different development environments.

### Scope and Boundaries
- **In Scope**: Cross-compilation toolchain setup, environment configuration, build system integration, validation and testing tools
- **Out of Scope**: Native ARM64 development (handled on target hardware), package-specific compilation (custom-packages), final build orchestration (iso-builder)
- **Key Focus**: Automated toolchain installation, multi-platform compatibility, integration with dependent submodules

## Development Patterns

### Code Organization
```
arm64-toolchain/
├── scripts/                    # Shell scripts for automation
│   ├── install-toolchain.sh    # Platform-specific installation
│   ├── setup-cross-env.sh      # Environment configuration
│   └── validate-toolchain.sh   # Validation and testing
├── configs/                    # Build system configurations
│   ├── cmake/                  # CMake toolchain files
│   ├── autotools/              # Autotools cross-compilation
│   └── pkg-config/             # Package config settings
├── sysroot/                    # Target system root directory
└── docker/                     # Containerized environments
```

### File Structure Conventions
- Installation scripts detect platform automatically and use appropriate package managers
- Configuration files follow standard naming conventions (arm64-toolchain.cmake)
- Environment setup scripts are idempotent and can be sourced multiple times safely
- Validation scripts provide detailed output and exit codes for automation

### Naming Conventions
- Scripts use descriptive names with action-verb prefixes (install-, setup-, validate-)
- Configuration files include architecture designation (arm64-)
- Environment variables use consistent prefixes (ARM64_, CROSS_COMPILE)
- Docker images follow standard naming (arm64-dev, arm64-build)

### Common Workflows
1. **Platform Detection**: Identify host OS and package manager for appropriate installation
2. **Toolchain Installation**: Download and install cross-compilation packages
3. **Environment Setup**: Configure PATH, compiler variables, and build system settings
4. **Validation**: Test cross-compilation functionality and verify ARM64 output
5. **Integration**: Provide configuration files for dependent submodules

## Key Technologies

### Primary Languages
- **Bash Shell Scripting**: Installation automation, environment setup, validation scripts
- **CMake Configuration**: Toolchain files for cross-compilation projects
- **Docker Configuration**: Container definitions for consistent development environments
- **Make/Autotools**: Build system configuration for cross-compilation

### Frameworks and Tools
- **GNU Toolchain**: gcc-aarch64-linux-gnu, binutils-aarch64-linux-gnu
- **Build Systems**: CMake, Autotools, pkg-config configuration
- **Package Managers**: apt (Ubuntu/Debian), dnf (Fedora), brew (macOS)
- **Container Technology**: Docker for environment consistency and isolation

### Build Systems
- **Cross-compilation Setup**: Host x86_64 targeting ARM64 aarch64
- **Sysroot Management**: ARM64 target system libraries and headers
- **Environment Configuration**: Cross-compilation variables and PATH setup
- **Validation Framework**: Automated testing of cross-compilation functionality

### Configuration Management
- **Platform Detection**: Automatic OS and package manager identification
- **Environment Isolation**: Separate cross-compilation environment from host
- **Version Management**: Consistent toolchain versions across development environments
- **Integration Support**: Configuration files for dependent submodules

## Integration Points

### Submodule Dependencies
- **Provides to ALL**: Every other submodule requiring ARM64 compilation depends on this
- **Critical Dependencies**: custom-packages (Qt/KDE cross-compilation), package-builder (automation), iso-builder (package compilation)
- **Secondary Dependencies**: boot-systems (bootloader compilation), hardware-support (driver compilation)

### Data Flow
```
Host System → Toolchain Installation → Environment Setup → Cross-compilation → ARM64 Binaries
     ↓              ↓                      ↓                    ↓               ↓
Package Manager → arm64-toolchain → Environment Variables → Dependent Modules → Target Hardware
```

### API Interfaces
- **Environment Variables**: Standardized cross-compilation variables (CC, CXX, AR, etc.)
- **Configuration Files**: CMake toolchain, autotools site files, pkg-config settings
- **Shell Functions**: Reusable functions for cross-compilation validation and setup
- **Docker Images**: Consistent containerized development environments

### Cross-Repository Communication
- **Environment Export**: Provides cross-compilation environment to all dependent submodules
- **Configuration Sharing**: Build system configurations used by multiple repositories
- **Validation Support**: Testing infrastructure for cross-compilation correctness
- **Version Coordination**: Ensures consistent toolchain versions across workspace

## Testing Strategy

### Testing Philosophy
Comprehensive validation of cross-compilation infrastructure across multiple host platforms to ensure consistent ARM64 binary generation and compatibility with dependent submodules.

### Test Types and Coverage
- **Installation Testing**: Validate toolchain installation on different host platforms
- **Cross-compilation Testing**: Verify ARM64 binary generation from test programs
- **Environment Testing**: Ensure environment variables and configuration correctness
- **Integration Testing**: Test compatibility with dependent submodule requirements
- **Container Testing**: Validate Docker environment consistency and functionality

### Automation Approaches
```bash
# Automated testing pipeline
./scripts/test-installation.sh --platform=ubuntu --version=22.04
./scripts/test-cross-compilation.sh --language=c,cpp --frameworks=qt
./scripts/test-integration.sh --submodules=custom-packages,iso-builder
./scripts/test-containers.sh --images=arm64-dev,arm64-build
```

### Validation Criteria
- Cross-compilation toolchain installs successfully on all supported platforms
- Generated ARM64 binaries validate as correct architecture and executable format
- Environment configuration integrates properly with CMake, autotools, and makepkg
- Docker containers provide consistent development environment across platforms
- Integration with dependent submodules works without additional configuration

## Build and Deployment

### Installation Process
```bash
# Platform-specific automated installation
./scripts/install-toolchain.sh --platform=auto --validate

# Manual component installation
sudo apt install gcc-aarch64-linux-gnu binutils-aarch64-linux-gnu
sudo apt install libc6-dev-arm64-cross linux-libc-dev-arm64-cross

# Sysroot setup
./scripts/setup-sysroot.sh --populate-libraries
```

### Environment Configuration
```bash
# Cross-compilation environment setup
export TARGET_ARCH=aarch64
export CROSS_COMPILE=aarch64-linux-gnu-
export CC=${CROSS_COMPILE}gcc
export CXX=${CROSS_COMPILE}g++
export AR=${CROSS_COMPILE}ar
export STRIP=${CROSS_COMPILE}strip
export PKG_CONFIG_PATH=/usr/lib/aarch64-linux-gnu/pkgconfig
```

### Deployment Considerations
- Toolchain installation requires administrator privileges on host system
- Environment setup must be persistent across development sessions
- Configuration files must be accessible to all dependent submodules
- Docker images should be pre-built and cached for development efficiency

### ARM64-Specific Concerns
- **Target Architecture**: Ensure all tools target aarch64 (ARM64) not armhf (32-bit)
- **ABI Compatibility**: Use correct ARM64 ABI and calling conventions
- **Library Compatibility**: Ensure sysroot contains ARM64 versions of required libraries
- **Optimization Flags**: Configure ARM64-specific compiler optimizations

## Quality Standards

### Code Review Criteria
- **Platform Compatibility**: Scripts work correctly on Ubuntu, Fedora, macOS, and other supported platforms
- **Error Handling**: Robust error detection and recovery for installation and setup failures
- **Idempotency**: Scripts can be run multiple times safely without causing issues
- **Documentation**: Clear usage instructions and troubleshooting information
- **Security**: Secure download and installation of toolchain components

### Documentation Requirements
- Platform-specific installation instructions with prerequisites
- Troubleshooting guides for common cross-compilation issues
- Integration examples showing usage with dependent submodules
- Environment variable documentation with examples and validation

### Performance Standards
- Toolchain installation completes within reasonable time (< 10 minutes)
- Cross-compilation performance comparable to native compilation where possible
- Minimal overhead from environment setup and configuration
- Efficient disk space usage for toolchain and sysroot components

### Security Considerations
- Verify checksums and signatures for downloaded toolchain components
- Use official package repositories when possible for toolchain installation
- Implement secure defaults for cross-compilation environment configuration
- Regular updates for security patches in toolchain components

## Troubleshooting

### Common Development Issues
- **Toolchain not found**: PATH configuration issue or incomplete installation
- **Library linking errors**: Sysroot misconfiguration or missing ARM64 libraries
- **CMake configuration failures**: Incorrect toolchain file path or content
- **Cross-compilation failures**: Environment variables not set or incorrect values

### Debug Procedures
```bash
# Environment debugging
echo "Cross-compilation environment check:"
echo "CC: ${CC:-not set}"
echo "CXX: ${CXX:-not set}"
echo "TARGET_ARCH: ${TARGET_ARCH:-not set}"
echo "PKG_CONFIG_PATH: ${PKG_CONFIG_PATH:-not set}"

# Toolchain validation
which ${CROSS_COMPILE}gcc || echo "Cross-compiler not found in PATH"
${CROSS_COMPILE}gcc --version 2>/dev/null || echo "Cross-compiler not functional"

# Test compilation
echo 'int main(){return 0;}' > /tmp/test.c
${CC} /tmp/test.c -o /tmp/test-arm64 || echo "Cross-compilation failed"
file /tmp/test-arm64 | grep -q aarch64 || echo "Incorrect architecture"
```

### Performance Analysis
- Installation time profiling for optimization opportunities
- Cross-compilation speed comparison with native compilation
- Disk space usage analysis for sysroot and toolchain components
- Memory usage monitoring during cross-compilation processes

### Error Handling Patterns
```bash
# Robust installation with rollback
install_component() {
    local component="$1"
    local backup_dir="/tmp/toolchain-backup-$$"

    # Create backup if component exists
    if command -v "$component" >/dev/null 2>&1; then
        cp "$(which $component)" "$backup_dir/"
    fi

    # Attempt installation
    if ! install_package "$component"; then
        echo "Installation failed, rolling back..." >&2
        # Restore from backup if available
        [[ -f "$backup_dir/$component" ]] && cp "$backup_dir/$component" /usr/local/bin/
        return 1
    fi

    # Cleanup
    rm -rf "$backup_dir"
}
```

## Milestone Alignment

### Current Milestone Objectives
**Milestone 0 - Infrastructure Setup**: Establish cross-compilation toolchain infrastructure that enables ARM64 development across all submodules

### Repository-Specific Goals
- Complete automated toolchain installation for all supported host platforms
- Provide comprehensive environment configuration and validation tools
- Create Docker-based development environment for consistency
- Integrate with dependent submodules' build processes

### Success Criteria
- Toolchain installation works on Ubuntu, Fedora, macOS, and other supported platforms
- Cross-compilation generates valid ARM64 binaries that run on target hardware
- Integration with custom-packages Qt/KDE cross-compilation is functional
- All dependent submodules can successfully use the toolchain without additional setup

### Dependencies on Other Submodules
- **None**: This is the foundational repository that enables other submodules
- **Enables**: All submodules requiring ARM64 compilation depend on this infrastructure
- **Testing Coordination**: Validation requires coordination with dependent submodules

## Reference Materials

### Upstream Documentation
- [GCC Cross-compilation Documentation](https://gcc.gnu.org/): Official GCC cross-compilation guide
- [GNU Binutils Documentation](https://sourceware.org/binutils/): Binary utilities documentation
- [CMake Cross-compiling](https://cmake.org/cmake/help/latest/manual/cmake-toolchains.7.html): CMake toolchain configuration
- [Autotools Cross-compilation](https://www.gnu.org/software/automake/manual/html_node/Cross_002dCompilation.html): Autotools cross-compilation setup

### Technical Specifications
- ARM64 ABI specification and calling conventions
- ELF format specification for ARM64 binaries
- Linux cross-compilation best practices and standards
- Container development environment patterns

### Best Practices
- Cross-compilation environment isolation and management
- Multi-platform toolchain installation automation
- Build system integration patterns for cross-compilation
- Testing and validation approaches for cross-compilation infrastructure

### Learning Resources
- Cross-compilation tutorials and guides
- ARM64 development environment setup documentation
- Docker containerization for development environments
- Shell scripting best practices for system administration

## Development Environment

### Required Tools
```bash
# Host system requirements
bash (4.0+)
curl or wget
git
sudo privileges for package installation

# Platform-specific package managers
apt (Ubuntu/Debian)
dnf (Fedora/RHEL)
brew (macOS)
pacman (Arch Linux)
```

### Configuration Files
- **scripts/install-toolchain.sh**: Platform-specific installation automation
- **configs/cmake/arm64-toolchain.cmake**: CMake cross-compilation configuration
- **configs/autotools/arm64-config.site**: Autotools cross-compilation settings
- **docker/Dockerfile.arm64-dev**: Development container definition

### Environment Variables
```bash
# Core cross-compilation variables
export TARGET_ARCH=aarch64
export CROSS_COMPILE=aarch64-linux-gnu-
export CC=${CROSS_COMPILE}gcc
export CXX=${CROSS_COMPILE}g++
export AR=${CROSS_COMPILE}ar
export RANLIB=${CROSS_COMPILE}ranlib
export STRIP=${CROSS_COMPILE}strip

# Build system integration
export CMAKE_TOOLCHAIN_FILE=configs/cmake/arm64-toolchain.cmake
export CONFIG_SITE=configs/autotools/arm64-config.site
export PKG_CONFIG_PATH=/usr/lib/aarch64-linux-gnu/pkgconfig
```

### IDE/Editor Setup
- **VSCode**: Extensions for shell scripting, Docker, and CMake integration
- **CLion**: CMake toolchain configuration for cross-compilation
- **Vim/Neovim**: Shell scripting support and Docker integration
- **Build Integration**: Makefile support for toolchain management tasks

## AI Assistance Guidelines

### Preferred Code Patterns
```bash
# Platform detection pattern
detect_platform() {
    case "$(uname -s)" in
        Linux)
            if command -v apt >/dev/null 2>&1; then
                echo "ubuntu"
            elif command -v dnf >/dev/null 2>&1; then
                echo "fedora"
            fi
            ;;
        Darwin)
            echo "macos"
            ;;
    esac
}

# Cross-compilation validation pattern
validate_cross_compiler() {
    local compiler="${1:-${CC}}"
    if ! command -v "$compiler" >/dev/null 2>&1; then
        echo "Error: Cross-compiler $compiler not found" >&2
        return 1
    fi

    # Test compilation
    echo 'int main(){return 0;}' > /tmp/test-$$.c
    if ! "$compiler" /tmp/test-$$.c -o /tmp/test-$$; then
        echo "Error: Cross-compiler $compiler not functional" >&2
        rm -f /tmp/test-$$.*
        return 1
    fi

    # Verify architecture
    if ! file /tmp/test-$$ | grep -q aarch64; then
        echo "Error: Compiler $compiler not producing ARM64 binaries" >&2
        rm -f /tmp/test-$$.*
        return 1
    fi

    rm -f /tmp/test-$$.*
    return 0
}
```

### Architecture Principles
- **Automation First**: All setup and configuration should be automated
- **Platform Agnostic**: Support multiple host platforms with common interface
- **Validation Focused**: Always validate installation and configuration
- **Integration Ready**: Provide standard interfaces for dependent submodules

### Documentation Style
- Shell scripts include comprehensive help text and usage examples
- Configuration files include inline comments explaining cross-compilation settings
- Error messages provide actionable troubleshooting guidance
- Examples show integration with dependent submodules

### Review Focus Areas
- Multi-platform compatibility and testing on different operating systems
- Cross-compilation correctness and ARM64 binary validation
- Integration compatibility with dependent submodules
- Error handling robustness and recovery procedures

## Project Context

This repository operates within the AcreetionOS ARM64 multi-repository workspace:

- **Main Workspace**: [acreetionos-arm64/workspace](https://github.com/acreetionos-arm64/workspace)
- **Architecture**: Foundational repository enabling all other ARM64 development
- **Coordination**: Critical dependency for 8 out of 10 submodules
- **Timeline**: Must be completed in Milestone 0 to enable subsequent development

## Upstream Relationships

### GNU Toolchain Integration
Maintains compatibility with upstream GNU toolchain releases while providing ARM64-specific configuration and automation for AcreetionOS development workflows.

### GitLab CE Coordination
Planned integration with GitLab CE for toolchain version management and automated environment setup in CI/CD pipelines.

### Community Contribution
Cross-compilation automation and configuration improvements contributed back to relevant communities for broader ARM64 development ecosystem benefit.

---

*This AI context file is maintained to provide effective development assistance specific to the arm64-toolchain domain within the AcreetionOS ARM64 project.*