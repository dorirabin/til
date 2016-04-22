# Multiple SSH keys for multiple Github accounts

1. Create SSH key for both accounts

2. Modify ```~/.ssh/config```

  The value of "Host" is arbitary. It will be used to map the git remote url.

  ```
  # Default (Account A)
  Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/account_a_id_rsa

  # Account B
  Host github-example
    HostName github.com
    User git
    IdentityFile ~/.ssh/account_b_id_rsa
  ```

3. Modify git remote url (for non-default account only)

  The remote url should be in format of ```git@<Host>:<User>/<Repository>.git```

  In previous step, "github-example" is used as the "Host". 

  ```
  git remote set-url origin git@github-example:account_b/example-repo.git
  ```

