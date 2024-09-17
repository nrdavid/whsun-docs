<img src='docs/assets/maxwell.jpg'>

## Sun Research Group Handbook

This is the official repository for the Sun Group Handbook hosted at [https://nrdavid.github.io/whsun-docs/](https://nrdavid.github.io/whsun-docs/).

This branch uses [MKdocs](https://www.mkdocs.org/) and [MKdocs-Material](https://squidfunk.github.io/mkdocs-material/).

The site is maintained by the [Sun Research Group](https://whsunresearch.group/) at the University of Michigan in Ann Arbor, MI, USA.

---
### Installation
1. Verify **conda** is installed. Otherwise, you can find the installation [here](https://conda.io/projects/conda/en/latest/user-guide/install/index.html).
    ```sh
    conda --version
    ```

2. Clone the repository.
    ```sh
    git clone git@github.com:nrdavid/whsun-docs.git
    cd whsun-docs
    ```

3. Create the environment from the `environment.yml' file.
    ```sh
    conda env create -f environment.yml
    conda activate whsun-docs
    ```

4. Run the development server.
    ```sh
    mkdocs serve
    ```

---
### Contributing

> First off, thank you so much for contributing to our group's handbook. These contributions will help current and future group members excel in their research.

1. Please create a branch to add/commit changes to followed with a pull request.
2. A reviewer will either accept or reject your request.