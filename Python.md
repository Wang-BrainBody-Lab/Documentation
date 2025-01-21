- [Python](#python)
  * [Installing Conda](#installing-conda)
  * [Creating and Managing Environments with Conda](#creating-and-managing-environments-with-conda)
  * [Example Workflow](#example-workflow)
  * [Managing package using pip](#managing-package-using-pip)
 
---
## [Python](https://packaging.python.org/en/latest/tutorials/installing-packages/)

### Installing Conda

By default, when you install Conda on a Linux server, it's typically installed for your user account only. This means:

1. The Conda installation is located in your home directory (usually ~/anaconda3 or ~/miniconda3).
2. Only you can use this Conda installation directly.
3. The Conda commands are added to your user's PATH, not system-wide.

If you don't already have Conda installed, you can install it by downloading and installing Anaconda or Miniconda:

- **Anaconda**: A full distribution with many pre-installed packages.
- **Miniconda**: A minimal installation, allowing you to install only the packages you need. You can download and install Miniconda from [here](https://docs.conda.io/en/latest/miniconda.html).

**Note:** When setting up conda on the server, add  `export PATH="/home/YourUserName/miniconda3/bin:$PATH"`to the `~/.bashrc` file. 
Then run the init command `source ~/miniconda3/bin/activate` and `conda init --all`.

### Creating and Managing Environments with Conda

1. **Create a New Environment**:
   ```bash
   conda create --name project1 python=3.11
   ```

2. **Activate an Environment**:
   ```bash
   conda activate project1
   ```

3. **Deactivate the Current Environment**:
   ```bash
   conda deactivate
   ```

4. **List All Environments**:
   ```bash
   conda env list
   ```

5. **Remove an Environment**:
   ```bash
   conda remove --name project1 --all
   ```

6. **Install Packages in an Environment with conda**:
   ```bash
   conda install --name project1 numpy pandas
   ```

7. **Export an Environment as yml file**:
   This is useful for sharing environments or for reproducing them on a different system.
   ```bash
   conda env export --name project1 > environment.yml
   ```

8. **Create an Environment from an yml file**:
   ```bash
   conda env create --file environment.yml
   ```

9. **Cloning an Environment**:
   You can clone an existing environment to create a new one with the same packages.
   ```bash
   conda create --name project2 --clone project1
   ```

10. **Updating an Environment**:
   Update all packages in the current environment to the latest versions.
   ```bash
   conda update --all
   ```

11. **Specifying Channels**:
   Sometimes, certain packages are available in specific channels. You can specify channels when installing packages.
   ```bash
   conda install -c conda-forge somepackage
   ```

### Example Workflow

1. **Create and Activate a New Environment**:
   ```bash
   conda create --name myenv python=3.9
   conda activate myenv
   ```

2. **Install Required Packages**:
   ```bash
   conda install numpy pandas scipy
   ```

3. **Export the Environment**:
   ```bash
   conda env export > myenv.yml
   ```

4. **Deactivate and Reactivate**:
   ```bash
   conda deactivate
   conda activate myenv
   ```

5. **Remove the Environment**:
   ```bash
   conda remove --name myenv --all
   ```

### Managing package using pip

# Install package using pip
```bash
python3 -m pip install "SomeProject"
```
# Update packages using pip
```bash
python3 -m pip install --upgrade pip setuptools wheel
```
# Uninstall packages using pip
```bash
pip uninstall package
```

### Other useful commands
`pwd` current working directory
`ls` list every file in the current directory
`ls -la` list every file and file details (rwg permissions, file size, etc.) in the current directory 
`cd dir_name` change directory
`mkdir dir_name` create a folder named "dir_name"
`cp file file_cp` copy file
`mv file path` move file to a different directory
`rm file` delete file

# Copy files
```bash
cp -r /home/packageA/. /home/cp/packageB/
```
# Remove file
```bash
rm /home/file1.tif
```
# Remove folder
```bash
rm -r /home/folder1/
```
# Install a package after GitHub download
(Before setting up the package, enter the directory where the setup.py is first)
```bash
python setup.py install
```
