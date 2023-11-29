# Git migrate repositories

1. Create the repository in Bitbucket using the UI
1. Clone the Gitlab repository using the "--bare" option

   ```bash
   git clone --bare <GITLAB-URL>
   ```

1. Add the Bitbucket remote

    ```bash
    cd <REPO-NAME>
    git remote add bitbucket <BITBUCKET-URL>
    ```

1. Push all commits, branches and tags to Bitbucket

    ```bash
    git push --all bitbucket
    git push --tags bitbucket
    ```

1. Remove the temp repository

    ```bash
    cd ..
    rm -rf <REPO-NAME>
    ```

**Source:** <https://stackoverflow.com/a/44111370/1509986>
