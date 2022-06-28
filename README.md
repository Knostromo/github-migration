# github migration

## Background
- There don't seem to have any public working script to migrate repos between github subsription 
- This is a quick and dirty way to do it
- This approach is screaming for further abstraction/optimisation

## Archive/pack up all existing old repos (from existing subscription)

- Make sure your github credential is set up in the env that you are running
- Run this script
```python
# collect all repos to be transferred
repos = [
    "https://github.com/USER/repo1.git",
    "https://github.com/USER/repo2.git",
    "https://github.com/USER/repo3.git"
]

# clone to local dir
for i in repos:
    print("Cloning repo = "+i)
    os.system('git clone '+i)
    
# pack it up
os.system('zip -9 -r oldrepos.zip ./*')

# (optional) test if archive is okay
os.system('unzip -t oldrepos.zip')
```

## Unpack/create/push repos (to new subscription)

- double check your github credential is correct (for new subscription)
- put `oldrepos.zip` into a dir of choice
- Run this script within the dir
```python
# you might want to hard code `root_path` if you plan to re-run this script 
root_path = os.getcwd()

# iteratively create/push to repo on new subscription
for x in os.listdir(root_path):
    print('Creating repo .... ' + x)
    os.system(f"gh repo create {x} --private") # <- **adjust flag accordingly if you want repo to be public**
    
    print('Pushing repo .... ' + x)
    os.chdir(root_path + '/' + x)
    # print('Current wd = ' + root_path + '/' + x) # <- only for debugging
    os.system(f"git config --global --add safe.directory {os.getcwd()}")
    os.system(f"git remote set-url origin https://NEW_github_URL.com/NEW_SUBSCRIPTION_USERNAME/{x}.git")
    os.system(f"git checkout main")
    os.system(f"git push -u origin main")

    print('Completed repo ' + x)
    print('================================\n')
```
- Thats it!

## Notes
- This copies all your codes in main
- Will not copy PRs, releases, history, issues etc
