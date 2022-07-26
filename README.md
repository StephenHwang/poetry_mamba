# Poetry + Mamba
Exploration of Poetry and Mamba for Python package and environment management.

## Usage
Example case of a basic Python environment with Snakemake.

### Setup
Ensure your `environment.yml` file is in the directory.

Create a temp environment to generate the initial environment and lock files files if `conda-lock`, `mamba`, and `poetry` are not installed.
Skip creating the temp environment if the required dependencies are already installed.

    # Create a temporary environment to create required starting files
    conda create --yes -p /tmp/tmp_env -c conda-forge mamba conda-lock poetry='1.*'
    conda activate /tmp/tmp_env

    # Create Conda lock file(s) from environment.yml
    conda-lock -k explicit --conda mamba
    # Set up Poetry with exact Python version from envirnment.yml
    poetry init --python=$(python --version | sed -En "s/Python //p")

Set up packages:

    # If applicable, fix package versions installed by Conda to prevent upgrades
    poetry add --lock snakemake=7.9.0

    # Add conda-lock and any other starting packages to pyproject.toml and poetry.lock
    poetry add --lock conda-lock

    # Or edit pyproject.toml directly:
      [tool.poetry.dependencies]
      numpy = "*"
      pandas = "*"

      [tool.poetry.dev-dependencies]
      ipython = "*"
      ipdb = "*"

    # then run
    poetry update --lock

Remove the temp env and create the true environment:
    # Remove the temp env
    conda deactivate
    rm -rf /tmp/tmp_env

    # Create the env
    conda create --name snakemake --file conda-linux-64.lock
    conda activate snakemake
    poetry install


### Usage
#### Activating the environment:
Poetry recognizes the active environment, so you do not need to use `poetry run` or `poetry shell`.

    conda activate snakemake

#### Adding dependencies

##### Method 1 (Poetry, preferred):
Use `poetry add` to install packages and add them to the `pyproject.toml` file.

    poetry add numpy
    poetry add ipdb --dev

##### Method 2 (Poetry):
Add dependencies to `pyproject.toml` file and use `poetry update`. Note: this will update `poetry.lock` to latest compatible versions.

    # Add dependencies to pyproject.toml
    [tool.poetry.dependencies]
    numpy = "*"
    pandas = "*"

    [tool.poetry.dev-dependencies]
    ipython = "*"
    ipdb = "*"

Then run:

    poetry update


##### Method 3 (Mamba, least preferred):
Least preferred method. Prioritize Poetry for adding Python dependencies and only install packages using Conda if there's a reason to do so (ie. a R package). After installation, consider adding an entry with the same version specification to Poetry's `pyproject.toml` (without ^ or ~ before the version number) to let Poetry know that the package is there and should not be upgraded.

    mamba install numpy


# Update packages
Update packages to latest version and write versions into `poetry.lock` (think delete `poetry.lock` and running install again).

    # Re-generate Conda lock file and update conda packages based on environment.yml
    conda-lock -k explicit --conda mamba
    mamba update --file conda-linux-64.lock
    # Update Poetry packages to latest versions
    poetry update

