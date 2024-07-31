# Content

## Python

https://packaging.python.org/en/latest/tutorials/installing-packages/

### Installing Conda

By default, when you install Conda on a Linux server, it's typically installed for your user account only. This means:

1. The Conda installation is located in your home directory (usually ~/anaconda3 or ~/miniconda3).
2. Only you can use this Conda installation directly.
3. The Conda commands are added to your user's PATH, not system-wide.

If you don't already have Conda installed, you can install it by downloading and installing Anaconda or Miniconda:

- **Anaconda**: A full distribution with many pre-installed packages.
- **Miniconda**: A minimal installation, allowing you to install only the packages you need.

You can download and install Miniconda from [here](https://docs.conda.io/en/latest/miniconda.html).

### Creating and Managing Environments with Conda

1. **Create a New Environment**:
   ```bash
   conda create --name project1 python=3.8
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

6. **Install Packages in an Environment**:
   ```bash
   conda install --name project1 numpy pandas
   ```

7. **Export an Environment**:
   This is useful for sharing environments or for reproducing them on different systems.
   ```bash
   conda env export --name project1 > environment.yml
   ```

8. **Create an Environment from an Environment File**:
   ```bash
   conda env create --file environment.yml
   ```

### Managing Environments More Efficiently

1. **Cloning an Environment**:
   You can clone an existing environment to create a new one with the same packages.
   ```bash
   conda create --name project2 --clone project1
   ```

2. **Updating an Environment**:
   Update all packages in the current environment to the latest versions.
   ```bash
   conda update --all
   ```

3. **Specifying Channels**:
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
```bash
# Install package using pip
python3 -m pip install --upgrade pip setuptools wheel
python3 -m pip install "SomeProject"

# Copy files
cp -r /home/packageA/. /home/cp/packageB/

(Before setting up the package, enter the directory where the setup.py is first)
# Set up a package from GitHub
python setup.py install

# Reset
pip uninstall package
python setup.py install

```
