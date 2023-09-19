# Contributing guidelines

## Before contributing

Welcome to
[xxMake](https://github.com/thereisnotime/xxMake)!
Before sending your pull requests, make sure that you read the whole guidelines.

## Contributing

### Contributor

Thankyou for considering improvising the solutions in this repository! Being one of our
contributors, you agree and confirm that:

-   Your work will be distributed under [MIT License](LICENSE.md) once your pull
    request is merged.
-   You submitted work fulfills the styles and standards of this repository.

### Contribution

Any contribution, from fixing a grammatical errors in a comment to improvising current
solutions is appriciated.

**New implementation** is welcome as long as the solution improvises the
`Time Complexity` and/or `Space Complexity` without deteriorating the either of them
compared to the current solution. But **identical implementation** of an existing
solution will not be accepted.

**Improving comments** are also highly welcome. But **fake pull requests**, like
_unnecessarily changing the wording, adding 'a' or 'the', etc_ are highly discouraged.
Such pull requests will be closed immediately and labeled as `invalid`.

1.  If you are a first-time contributor:

    -   Go to
        [https://github.com/thereisnotime/xxMake](https://github.com/thereisnotime/xxMake)
        and click the `fork` button to create your own copy of the project.

    -   Clone the project to your local computer:
        ```
        git clone https://github.com/<your-username>/xxMake.git
        ```
    -   Change the directory:

        ```
        cd xxMake
        ```

    -   Add the upstream repository:

        ```
        git remote add upstream https://github.com/thereisnotime/xxMake.git
        ```

    -   Now, git remote -v will show two remote repositories named:
        -   **upstream**: which refers to the original repository in GitHub
        -   **origin**: which refers to your personal fork

2.  Develop your contribution:

    -   Pull the latest changes from upstream:

        -   ```
            git checkout master
            ```
        -   ```
            git pull upstream master
            ```

    -   Create a branch for the feature you want to work on. Since the branch name will
        appear in the merge message, use a sensible name such as
        **problem-200-optimization**:

        ```
        git checkout -b problem-200-optimization
        ```

    -   Commit locally as you progress
        (`git add .` and `git commit -m "<you message>"`). Use a self-explanatory
        commit message.

3.  To submit your contribution:

    -   Push your changes back to your fork on GitHub:

        ```
        git push origin problem-200-optimization
        ```

    -   Enter your GitHub username and password (repeat contributors or advanced users
        can remove this step by connecting to GitHub with SSH).

    -   Go to GitHub. The new branch will show up with a green Pull Request button.
        Make sure the title and message are clear, concise, and self-explanatory. Then
        click the button to submit it.

### Other Requirements for Submissions

-   Follow the Goals & Philosophy section of the main [README.md](README.md)
-   Most importantly,
    -   **Be consistent in the use of these guidelines when submitting.**
    -   Happy coding!
