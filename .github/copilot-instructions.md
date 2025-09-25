# GitHub Copilot Instructions - arm64-toolchain

This file provides GitHub Copilot with domain-specific context for the arm64-toolchain submodule of the AcreetionOS ARM64 project.

## Repository Domain

**Specialization**: Cross-compilation Toolchain and Development Environment
**Primary Function**: ARM64 cross-compilation infrastructure for x86_64 hosts
**Technical Domain**: GNU toolchain, shell scripting, build system integration, containerization

## Code Style and Conventions

### Language-Specific Guidelines
- **Bash Scripts**: POSIX-compliant with robust error handling and platform detection
- **CMake**: Modern CMake practices with proper toolchain file structure
- **Docker**: Multi-stage builds with security best practices and minimal images
- **Configuration Files**: Clear, well-documented settings with inline explanations
- **Documentation**: Comprehensive with examples and troubleshooting guides

### Coding Standards
- All scripts include comprehensive error handling (`set -euo pipefail`)
- Platform detection uses standardized patterns with fallbacks
- Environment setup is idempotent and can be sourced safely multiple times
- Validation functions provide detailed output and appropriate exit codes
- Installation scripts support both automated and interactive modes

### File Organization
```
scripts/
├── install-toolchain.sh      # Platform-specific installation
├── setup-cross-env.sh        # Environment configuration
├── validate-toolchain.sh     # Installation validation
└── uninstall-toolchain.sh    # Cleanup and removal
configs/
├── cmake/arm64-toolchain.cmake
├── autotools/arm64-config.site
└── pkg-config/arm64.pc
```

### Naming Conventions
- Scripts use action-verb prefixes: `install-`, `setup-`, `validate-`, `test-`
- Configuration files include architecture: `arm64-toolchain.cmake`
- Environment variables use consistent prefixes: `ARM64_`, `CROSS_COMPILE`
- Functions use descriptive names: `detect_platform()`, `install_cross_compiler()`

## Framework Preferences

### Preferred Approaches
- **Automated Installation**: Script-based installation with platform detection
- **Environment Isolation**: Separate cross-compilation environment from host
- **Validation First**: Always validate installation and configuration
- **Multi-platform Support**: Support Ubuntu, Fedora, macOS, and other platforms

### Architecture Patterns
```bash
# Platform detection pattern
detect_platform() {
    case "$(uname -s)" in
        Linux)
            if command -v apt >/dev/null 2>&1; then
                echo "ubuntu"
            elif command -v dnf >/dev/null 2>&1; then
                echo "fedora"
            elif command -v pacman >/dev/null 2>&1; then
                echo "arch"
            fi
            ;;
        Darwin) echo "macos" ;;
        *) echo "unsupported" ;;
    esac
}

# Installation with validation pattern
install_with_validation() {
    local component="$1"
    install_component "$component" || return 1
    validate_component "$component" || {
        echo "Validation failed, rolling back..." >&2
        uninstall_component "$component"
        return 1
    }
}
```

### Design Principles
- **Automation Over Manual**: Everything should be scriptable and repeatable
- **Fail Fast**: Detect problems early with comprehensive validation
- **Platform Agnostic**: Common interface across different host systems
- **Integration Ready**: Provide standard configuration for dependent modules

### Technology Stack
- **GNU Toolchain**: gcc-aarch64-linux-gnu, binutils-aarch64-linux-gnu
- **Build Systems**: CMake toolchain files, autotools configuration
- **Container Technology**: Docker for consistent development environments
- **Package Managers**: Platform-specific package manager integration

## Testing Approaches

### Unit Testing Strategy
```bash
# Test function pattern
test_cross_compilation() {
    local test_file="/tmp/cross-compile-test-$$.c"
    local test_binary="/tmp/cross-compile-test-$$"

    # Create test program
    cat > "$test_file" << 'EOF'
#include <stdio.h>
int main() {
    printf("Hello ARM64\n");
    return 0;
}
EOF

    # Attempt compilation
    if ! ${CC:-aarch64-linux-gnu-gcc} "$test_file" -o "$test_binary"; then
        echo "FAIL: Cross-compilation failed" >&2
        rm -f "$test_file" "$test_binary"
        return 1
    fi

    # Verify architecture
    if ! file "$test_binary" | grep -q aarch64; then
        echo "FAIL: Binary is not ARM64 architecture" >&2
        rm -f "$test_file" "$test_binary"
        return 1
    fi

    echo "PASS: Cross-compilation successful"
    rm -f "$test_file" "$test_binary"
    return 0
}
```

### Integration Testing
- Test integration with CMake-based projects (Qt/KDE for custom-packages)
- Validate autotools cross-compilation configuration
- Test makepkg integration for Arch Linux package building
- Verify Docker environment consistency and functionality

### Test Structure
```bash
#!/bin/bash
# Test suite structure
set -euo pipefail

# Test configuration
TESTS_DIR="$(dirname "$0")"
TOOLCHAIN_DIR="$(dirname "$TESTS_DIR")"

# Test functions
test_installation() { ... }
test_environment_setup() { ... }
test_cross_compilation() { ... }
test_cmake_integration() { ... }

# Run all tests
main() {
    echo "Running arm64-toolchain test suite..."
    test_installation || return 1
    test_environment_setup || return 1
    test_cross_compilation || return 1
    test_cmake_integration || return 1
    echo "All tests passed!"
}

main "$@"
```

### Mocking and Fixtures
- Mock different host platforms for installation testing
- Use Docker containers for isolated testing environments
- Create minimal test projects for cross-compilation validation
- Generate test data for different toolchain configurations

## Documentation Style

### Code Comments
```bash
# ARM64-specific: Use aarch64 target, not armhf (32-bit ARM)
# This is critical for AcreetionOS ARM64 compatibility
export TARGET_ARCH=aarch64
export CROSS_COMPILE=aarch64-linux-gnu-

# Platform detection: Different package managers use different package names
case "$platform" in
    ubuntu|debian)
        # Ubuntu/Debian use standard cross-compilation packages
        packages="gcc-aarch64-linux-gnu binutils-aarch64-linux-gnu"
        ;;
    fedora|rhel)
        # Fedora/RHEL use different package naming convention
        packages="gcc-aarch64-linux-gnu binutils-aarch64-linux-gnu"
        ;;
esac
```

### Documentation Patterns
- Always explain platform-specific differences and requirements
- Include complete examples showing successful installation and usage
- Document troubleshooting steps for common cross-compilation issues
- Provide integration examples with dependent submodules

### README Conventions
- **Prerequisites**: Clear system requirements for each supported platform
- **Installation**: Step-by-step automated and manual installation options
- **Validation**: Commands to verify correct installation and configuration
- **Integration**: Examples of usage with other submodules

### API Documentation
- Document all environment variables and their purposes
- Include examples of CMake and autotools integration
- Explain Docker container usage and customization
- Provide shell function documentation with parameters and return values

## Security Considerations

### Security Best Practices
- Verify checksums and signatures for downloaded toolchain components
- Use official package repositories whenever possible
- Implement secure defaults for development environment configuration
- Regular security updates for toolchain and container images

### Vulnerability Prevention
- Validate all downloaded components before installation
- Use least-privilege principles for installation scripts
- Implement proper input sanitization in shell scripts
- Monitor for security advisories affecting toolchain components

### Secure Coding Patterns
```bash
# Input validation for package names
validate_package_name() {
    local package="$1"
    if [[ ! "$package" =~ ^[a-zA-Z0-9._+-]+$ ]]; then
        echo "Error: Invalid package name '$package'" >&2
        return 1
    fi
}

# Secure download with verification
secure_download() {
    local url="$1"
    local expected_checksum="$2"
    local output_file="$3"

    # Download with error handling
    if ! curl -fsSL "$url" -o "$output_file"; then
        echo "Error: Failed to download $url" >&2
        return 1
    fi

    # Verify checksum
    if ! echo "$expected_checksum $output_file" | sha256sum -c; then
        echo "Error: Checksum verification failed for $output_file" >&2
        rm -f "$output_file"
        return 1
    fi
}
```

### Secrets Management
- Never include credentials or API keys in configuration files
- Use environment variables for sensitive configuration
- Implement secure storage for any required authentication tokens
- Document security considerations for development environments

## Performance Guidelines

### Optimization Priorities
1. **Installation Speed**: Minimize package download and installation time
2. **Environment Setup**: Fast environment configuration and validation
3. **Cross-compilation Performance**: Optimize compiler flags and parallel builds
4. **Container Efficiency**: Minimal container images with fast startup times

### Performance Patterns
```bash
# Parallel installation where possible
install_packages_parallel() {
    local packages=("$@")
    local pids=()

    for package in "${packages[@]}"; do
        install_single_package "$package" &
        pids+=($!)
    done

    # Wait for all installations
    local exit_code=0
    for pid in "${pids[@]}"; do
        wait "$pid" || exit_code=1
    done

    return $exit_code
}

# Cached downloads to avoid repeated downloads
download_with_cache() {
    local url="$1"
    local cache_file="$HOME/.cache/arm64-toolchain/$(basename "$url")"

    if [[ ! -f "$cache_file" ]]; then
        mkdir -p "$(dirname "$cache_file")"
        curl -fsSL "$url" -o "$cache_file"
    fi

    cp "$cache_file" .
}
```

### Resource Management
- Monitor disk space during toolchain installation
- Implement cleanup routines for temporary files and downloads
- Optimize Docker layer caching for faster container builds
- Use package manager caching to speed up repeated installations

### Profiling Approaches
- Time installation steps to identify bottlenecks
- Monitor resource usage during cross-compilation
- Profile container startup and build times
- Analyze network usage for download optimization

## ARM64-Specific Considerations

### ARM64 Compatibility
```bash
# Ensure correct ARM64 target architecture
validate_arm64_target() {
    local binary="$1"

    # Check ELF header for ARM64
    if ! file "$binary" | grep -q "aarch64"; then
        echo "Error: Binary $binary is not ARM64 architecture" >&2
        return 1
    fi

    # Verify ARM64 ABI compliance
    if ! readelf -h "$binary" | grep -q "AArch64"; then
        echo "Error: Binary $binary does not use ARM64 ABI" >&2
        return 1
    fi
}

# ARM64-specific compiler flags
configure_arm64_optimization() {
    case "${ARM64_CPU_TARGET:-generic}" in
        cortex-a72)  # Raspberry Pi 4/5
            export CFLAGS="-march=armv8-a -mtune=cortex-a72 -O2"
            ;;
        cortex-a53)  # Pine64 and similar
            export CFLAGS="-march=armv8-a -mtune=cortex-a53 -O2"
            ;;
        *)
            export CFLAGS="-march=armv8-a -O2"
            ;;
    esac
    export CXXFLAGS="$CFLAGS"
}
```

### Cross-Platform Concerns
- Build system must run on x86_64 host and target ARM64
- Library path resolution for cross-compilation sysroot
- Endianness compatibility (ARM64 is little-endian like x86_64)
- ABI differences between x86_64 host tools and ARM64 target

### Architecture-Specific Optimizations
- Use ARM64 NEON SIMD instructions where beneficial
- Optimize for ARM64 cache hierarchy and memory access patterns
- Configure toolchain for target ARM64 CPU implementations
- Implement ARM64-specific debugging and profiling support

### Hardware Considerations
- Support for different ARM64 SoCs (Raspberry Pi, Pine64, servers)
- Memory constraints on development vs. production hardware
- Storage considerations for cross-compilation environment size
- Network requirements for package downloads and updates

## Upstream Compatibility

### Compatibility Requirements
- Maintain compatibility with GNU toolchain upstream releases
- Support standard Linux cross-compilation practices and conventions
- Follow Arch Linux cross-compilation patterns for package building
- Preserve integration with standard development tools and IDEs

### Integration Standards
```bash
# Standard CMake toolchain file structure
set(CMAKE_SYSTEM_NAME Linux)
set(CMAKE_SYSTEM_PROCESSOR aarch64)

set(CMAKE_C_COMPILER aarch64-linux-gnu-gcc)
set(CMAKE_CXX_COMPILER aarch64-linux-gnu-g++)

set(CMAKE_FIND_ROOT_PATH /usr/aarch64-linux-gnu)
set(CMAKE_FIND_ROOT_PATH_MODE_PROGRAM NEVER)
set(CMAKE_FIND_ROOT_PATH_MODE_LIBRARY ONLY)
set(CMAKE_FIND_ROOT_PATH_MODE_INCLUDE ONLY)
```

### Contribution Guidelines
- Document all platform-specific modifications for upstream consideration
- Create reusable components that benefit broader ARM64 development community
- Test compatibility with multiple GNU toolchain versions
- Provide feedback to upstream projects on ARM64 cross-compilation issues

### Version Management
- Track GNU toolchain version compatibility and requirements
- Document supported host platform versions and package manager versions
- Maintain compatibility matrices for different component combinations
- Implement automated testing for toolchain version updates

## Project-Specific Context

### AcreetionOS Integration
- Provide cross-compilation infrastructure for all AcreetionOS ARM64 components
- Support AcreetionOS-specific build requirements and customizations
- Integrate with AcreetionOS package repository and build infrastructure
- Maintain consistency with AcreetionOS development practices and standards

### Multi-Repository Coordination
- **Critical Dependency**: All ARM64 compilation submodules depend on this
- **Environment Provider**: Supplies cross-compilation environment to workspace
- **Configuration Source**: Provides build system configurations to dependent modules
- **Validation Support**: Offers testing infrastructure for ARM64 binary validation

### Submodule Dependencies
- **Enables**: custom-packages, package-builder, iso-builder, boot-systems, hardware-support
- **Critical Path**: Must be completed in Milestone 0 to enable subsequent development
- **Integration Point**: Provides standardized interface for all dependent submodules
- **Testing Coordination**: Validates functionality with dependent submodule requirements

### Build System Integration
- Coordinate with package-builder for automated compilation workflows
- Support iso-builder package compilation and inclusion processes
- Integrate with testing-infrastructure for validation and quality assurance
- Provide consistent environment for releases submodule artifact generation

## Common Patterns and Anti-Patterns

### Recommended Patterns
```bash
# Good: Platform-agnostic package installation
install_cross_compiler() {
    local platform
    platform=$(detect_platform)

    case "$platform" in
        ubuntu) install_ubuntu_packages ;;
        fedora) install_fedora_packages ;;
        macos)  install_macos_packages ;;
        *) echo "Unsupported platform: $platform" >&2; return 1 ;;
    esac
}

# Good: Comprehensive validation
validate_installation() {
    validate_cross_compiler || return 1
    validate_cross_compilation || return 1
    validate_cmake_integration || return 1
    echo "Installation validation successful"
}
```

### Anti-Patterns to Avoid
```bash
# Bad: Hardcoded platform assumptions
sudo apt install gcc-aarch64-linux-gnu  # Assumes Ubuntu/Debian

# Bad: No error handling or validation
export CC=aarch64-linux-gnu-gcc  # Assumes compiler exists

# Bad: Mixed architecture targets
gcc -march=x86-64  # Wrong architecture for ARM64 target
```

### Best Practices
- Always detect platform before attempting installation
- Validate every step of toolchain setup and configuration
- Provide comprehensive error messages with troubleshooting guidance
- Support both automated and interactive installation modes

### Code Review Focus
- Multi-platform compatibility and testing across operating systems
- Cross-compilation correctness and ARM64 binary validation
- Error handling robustness and recovery procedures
- Integration testing with dependent submodules

## Error Handling and Logging

### Error Handling Strategy
```bash
# Comprehensive error handling for toolchain operations
install_toolchain() {
    local log_file="/tmp/toolchain-install-$$.log"
    local error_occurred=0

    {
        detect_platform || { error_occurred=1; }
        install_cross_compiler || { error_occurred=1; }
        setup_environment || { error_occurred=1; }
        validate_installation || { error_occurred=1; }
    } 2>&1 | tee "$log_file"

    if [[ $error_occurred -eq 1 ]]; then
        echo "Installation failed. Log saved to: $log_file" >&2
        echo "Run with --debug for detailed troubleshooting information" >&2
        return 1
    fi

    echo "Installation successful. Log: $log_file"
    return 0
}
```

### Logging Conventions
- Use structured logging with timestamps and severity levels
- Log all platform detection and installation decisions
- Include detailed environment setup and validation information
- Separate logs for installation, configuration, and validation phases

### Debug Information
```bash
# Debug mode with comprehensive environment information
debug_environment() {
    echo "=== ARM64 Toolchain Debug Information ==="
    echo "Date: $(date)"
    echo "Platform: $(detect_platform)"
    echo "User: $(whoami)"
    echo "Working Directory: $(pwd)"

    echo "=== Environment Variables ==="
    env | grep -E "(CC|CXX|AR|CROSS_COMPILE|TARGET_ARCH)" | sort

    echo "=== Toolchain Validation ==="
    for tool in gcc g++ ar ranlib strip; do
        local cross_tool="aarch64-linux-gnu-$tool"
        if command -v "$cross_tool" >/dev/null 2>&1; then
            echo "$cross_tool: $(which $cross_tool) ($($cross_tool --version | head -1))"
        else
            echo "$cross_tool: NOT FOUND"
        fi
    done
}
```

### Monitoring Approaches
- Track installation success rates across different platforms
- Monitor toolchain validation and cross-compilation success
- Log performance metrics for installation and setup times
- Implement health checks for development environment consistency

## Development Workflow

### Branch Management
- `feature/platform-support` for new host platform support
- `fix/toolchain-issue` for toolchain installation and configuration fixes
- `enhancement/performance` for installation and cross-compilation optimizations
- `docs/integration-guide` for documentation improvements and examples

### Commit Conventions
```
feat(install): add macOS Homebrew support for ARM64 toolchain
fix(cmake): resolve toolchain file path resolution issue
perf(docker): optimize container build time with multi-stage builds
docs(integration): add Qt cross-compilation examples
```

### PR Guidelines
- Test changes on all supported platforms (Ubuntu, Fedora, macOS)
- Verify cross-compilation functionality with test programs
- Document any new dependencies or configuration requirements
- Include integration testing with at least one dependent submodule

### Review Process
- **Platform Compatibility**: Verify changes work across supported platforms
- **Cross-compilation Correctness**: Validate ARM64 binary generation
- **Integration Testing**: Test compatibility with dependent submodules
- **Documentation Review**: Ensure documentation is complete and accurate

---

*These instructions help GitHub Copilot provide contextually appropriate suggestions for arm64-toolchain development within the AcreetionOS ARM64 project ecosystem.*