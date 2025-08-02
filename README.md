# Appfilter Merge Tool

This tool is designed to automatically download and merge remote `appfilter.xml` files into a local base appfilter by:

- Parsing remote and local `appfilter.xml` files
- Comparing components
- Finding entries with the same package and drawable but different components
- Appending matching entries to the local `appfilter.xml` file

This is particularly useful for maintaining and extending icon packs for Android launchers.

---

## 🛠 Features

- Download `appfilter.xml` files from remote URLs
- Apply blacklist filters for packages, drawables, and components
- Prevent duplication of existing entries
- Preserve XML structure and formatting
- Automatically generate additional `<item>` lines for matching drawables

## 🔁 How It Works

1. Triggered manually via GitHub Action (`workflow_dispatch` event)
2. Downloads and parses remote `appfilter.xml` files
3. Compares with your base `appfilter.xml`
4. Adds missing components that:
   - Share the same package + drawable
   - Are not already in the file
   - Are not blacklisted
5. Commits the update to a branch (`crowdsource`) and opens a PR to `main`



## 📁 Directory Structure
Directory layout based on the current configuration:

```text
.github/
└── workflows/
    ├── crowdsource_appfilter.yaml                   # GitHub Action workflow
    ├── requirements.txt                # Python dependencies
    └── crowdsource_appfilter/
        ├── crowdsource.py              # The merge script
        └── blacklists/
            ├── packageblacklist.txt
            ├── componentblacklist.txt
            └── drawableblacklist.txt

newicons/
└── appfilter.xml                       # The appfilter being extended
```

## 🚀 How to use 

### Include as Github Action into your repository
1. Copy the files in the `workflows` folder into your repositories `workflows` folder
2. Edit the environment variables at the top of `crowdsource_appfilter.yaml` to suit your repository
```yaml
env:
  REMOTE_APPFILTER_URLS: '["https://raw.githubusercontent.com/LawnchairLauncher/lawnicons/refs/heads/develop/app/assets/appfilter.xml","https://raw.githubusercontent.com/Delta-Icons/android/refs/heads/master/app/src/main/assets/appfilter.xml","https://raw.githubusercontent.com/Arcticons-Team/Arcticons/refs/heads/main/app/src/main/assets/appfilter.xml"]' 
  PACKAGE_BLACKLIST_PATH: .github/workflows/crowdsource_appfilter/blacklists/packageblacklist.txt      # The path to the package blacklist file, change if necessary
  DRAWABLE_BLACKLIST_PATH: .github/workflows/crowdsource_appfilter/blacklists/drawableblacklist.txt    # The path to the drawable blacklist file, change if necessary
  COMPONENT_BLACKLIST_PATH: .github/workflows/crowdsource_appfilter/blacklists/componentblacklist.txt  # The path to the component blacklist file, change if necessary
  APPFILTER_PATH: newicons/appfilter.xml                                # The path to the appfilter.xml file, change if necessary            
  BASE_BRANCH: main                                                     # The base branch for the pull request, change if necessary
  PR_TITLE: Crowdsource Appfilter.xml                                        # The title of the pull request, change if necessary                          
  PR_BODY: Crowdsourced update of `appfilter.xml` via GitHub Action, applying blacklist filters.            # The body of the pull request, change if necessary
  OWNER: Arcticons-Team                                            # The owner of the repository, change if necessary
  BRANCH: crowdsource    
  REPOSITORY: Arcticons-Team/Crowdsource_Appfilter                   # The repository where the action will run, change if necessary            
```

### 🖥️ Run the Script Locally

To test or use the merge logic locally:

#### 1. Install dependencies

```bash
pip install -r .github/workflows/requirements.txt
```
#### 2. Set environment variables

Modify the environment variables below as needed
```bash
export REMOTE_APPFILTER_URLS='["https://raw.githubusercontent.com/LawnchairLauncher/lawnicons/refs/heads/develop/app/assets/appfilter.xml","https://raw.githubusercontent.com/Delta-Icons/android/refs/heads/master/app/src/main/assets/appfilter.xml","https://raw.githubusercontent.com/Arcticons-Team/Arcticons/refs/heads/main/app/src/main/assets/appfilter.xml"]'
export APPFILTER_PATH=newicons/appfilter.xml
export PACKAGE_BLACKLIST_PATH=.github/workflows/crowdsource_appfilter/blacklists/packageblacklist.txt
export COMPONENT_BLACKLIST_PATH=.github/workflows/crowdsource_appfilter/blacklists/componentblacklist.txt
export DRAWABLE_BLACKLIST_PATH=.github/workflows/crowdsource_appfilter/blacklists/drawableblacklist.txt
```
#### 3. Run the script

```bash
python .github/workflows/crowdsource_appfilter/crowdsource.py
```

## Blacklist Files

Each blacklist file contains one entry per line.

### componentblacklist.txt
```text
ComponentInfo{com.flipkart.android/com.flipkart.android.SilverIconAlias}
ComponentInfo{kfc_ko.kore.kg.kfc_korea/kfc_ko.kore.kg.kfc_korea.activity.IntroActivity}
ComponentInfo{com.mzmnvdkghk/com.mzmnvdkghk.ActivityMain}
```

### drawableblacklist.txt
```text
wallpapers
_3c_aio_toolbox
phone
```


### packageblacklist.txt
```text
com.android.phone
com.android.contacts
com.ally.MobileBanking
```