# Step-by-Step Process of Project for SPO600

### 1. Access the server using the following command
```bash
ssh username@aarch64-003.spo600.cdot.systems
```

### 2. Clone the GCC compiler from github
```bash
## currently in ~ directory
mkdir project
cd project
git clone git://gcc.gnu.org/git/gcc.git
```

### 3. Congiure the build
Refer to [wiki](http://spo600.cdot.systems/doku.php?id=spo600:building_gcc)

```bash
mkdir ~/project/gcc-build-001
cd ~/project/gcc-build-001
~/project/gcc/configure --prefix=$HOME/project/gcc-test-001
```

### 4. Perform the build
The following command could make it build on a 16-core system.

``` bash
time make -j 24 |& tee build.log
```

### 5. Install the build
Run the following command inside the gcc-build-001 directory

```bash
make install
```

### 6. Add `PATH` to use the test gcc compiler as first option.

```bash
PATH=$HOME/project/gcc-test-001/bin:$PATH
```

### 7. Check the GCC version.

```bash
which gcc
```