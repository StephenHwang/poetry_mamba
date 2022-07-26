# Poetry + Mamba
Exploration of using Poetry and Mamba for Python package and environment management.

## Usage
Example case of a basic Python environment with Snakemake.

### Setup
Ensure `environment.yml` file with desired `conda` packages is in the directory.
May skip creating the temp environment to generate the initial `environment.yml`, `conda-linux-64.lock`, `pyproject.toml`, and  `poetry.lock` files if `conda-lock`, `mamba`, and `poetry` are already installed.

    # Create a temporary environment to create required starting files
    conda create --yes -p /tmp/tmp_env -c conda-forge mamba conda-lock poetry='1.*'
    conda activate /tmp/tmp_env

    # Create Conda lock file(s) from environment.yml
    conda-lock -k explicit --conda mamba
    # Set up Poetry with exact Python version from envirnment.yml
    poetry init --python=$(python --version | sed -En "s/Python //p")

    # If applicable, fix package versions installed by Conda to prevent upgrades
    poetry add --lock snakemake=7.9.0
    # Add conda-lock and any other starting packages to pyproject.toml and poetry.lock
    poetry add --lock conda-lock

    # Remove the bootstrap env
    conda deactivate
    rm -rf /tmp/tmp_env

    # create the env
    conda create --name snakemake --file conda-linux-64.lock
    conda activate snakemake
    poetry install

### Usage
#### Activate Env:
By activating the conda env, there is no need to use poetry run or poetry shell as poetry.
    conda activate snakemake

#### Adding dependencies
##### Method 1 (Poetry):
Adding dependencies to `pyproject.toml` file and using `poetry install`.

    # adding to pyproject.toml
    [tool.poetry.dependencies]
    numpy = "*"
    pandas = "*"

    [tool.poetry.dev-dependencies]
    ipython = "*"
    ipdb = "*"

    # then run
    poetry install

##### Method 2 (Poetry):
The add command adds required packages to your `pyproject.toml` file and installs them.

    poetry add numpy
    poetry add ipdb --dev


##### Method 3 (Mamba):
Least preferred method. Prioritize using Poetry for adding Python dependencies and only install packages via Conda if there's a reason to do so (ie R package). After installation consider adding an entry with the same version specification to Poetry's `pyproject.toml` (without ^ or ~ before the version number) to let Poetry know that the package is there and should not be upgraded.

    mamba install numpy


# Update packages within the env
    # Re-generate Conda lock file and update conda packages based on environment.yml
    conda-lock -k explicit --conda mamba
    mamba update --file conda-linux-64.lock
    # Update Poetry packages to latest versions and (think delete poetry.lock and running install again)
    poetry update



















