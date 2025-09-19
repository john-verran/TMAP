# TMAP Build Instructions for Apple Silicon Macs

This guide shows how to build TMAP (Torrent Mapping Alignment Program) for x86_64 on Apple Silicon Macs using Rosetta 2 emulation.

> **Note**: If you already have a working `tmap` binary in this directory, you may not need to rebuild it. Check with `./tmap --version` first.

## **Why This Approach?**

- **TMAP requires x86_64**: The tool uses SSE intrinsics that are x86-specific
- **Rosetta 2 compatibility**: Built binaries run via Rosetta 2 on Apple Silicon
- **VarBen compatibility**: Works with `--aligner tmap` for Ion Torrent data analysis
- **Legacy support**: Maintains compatibility with existing Ion Torrent workflows

## **Prerequisites**

### **Required Tools**
```bash
# Install build tools
brew install autoconf automake libtool pkg-config make gcc

# Install required libraries
brew install zlib bzip2 curl ncurses
```

### **Verify Prerequisites**
```bash
which gcc && which make && which autoconf && which automake
# Should show paths to all tools
```

## **Build Process**

### **Step 1: Clean Previous Builds (if any)**
```bash
make clean 2>/dev/null || true
rm -f tmap config.h Makefile Makefile.in aclocal.m4 configure config.status config.log
```

### **Step 2: Generate Build Files**
```bash
sh autogen.sh
```
**Expected output**: Some warnings about obsolete macros (these are safe to ignore)

### **Step 3: Configure for x86_64**
```bash
arch -x86_64 ./configure LDFLAGS="-L/opt/homebrew/lib" CPPFLAGS="-I/opt/homebrew/include"
```
**Key points**:
- `arch -x86_64` forces x86_64 compilation
- `LDFLAGS` and `CPPFLAGS` help find Homebrew libraries
- Look for: `checking for BZ2_bzRead in -lbz2... yes` and `checking for gzread in -lz... yes`

### **Step 4: Build TMAP**
```bash
arch -x86_64 make
```
**Expected output**:
- Many compilation warnings (safe to ignore)
- Final linking step with `g++` command
- **Success**: No error messages

### **Step 5: Verify Build**
```bash
ls -la tmap
file tmap
./tmap --version
```

**Expected results**:
- `tmap` file exists and is executable
- `file tmap` shows: `Mach-O 64-bit executable x86_64`
- `./tmap --version` shows: `tmap: torrent mapper Version: 3.4.1`

### **Step 6: Install to Local Bin Directory**
```bash
# Make it executable (should already be, but just in case)
chmod +x ./tmap

# Copy the binary
cp tmap $HOME/.local/bin/

# Verify installation
tmap --version
```
