# iOS aplication release automation

## **Environment dependencies**

* MacOS latest (12.5 tested)
* Ruby 2.6.8+ (installed by default in MacOS)
* Bundler 2.3.18 + (installed by default in MacOS)
* Flutter 3.0.5 (installed in workflow)
* CocoaPods 1.11.3+ (installed in workflow)
* Fastlane 2.208.0+ (installed in workflow)

### **Self-hosted MacOS GitHub runner additional dependencies:**

* GitHub runner (manual installation) [Adding self-hosted runners - GitHub Docs](https://docs.github.com/en/actions/hosting-your-own-runners/adding-self-hosted-runners)

[Configuring the self-hosted runner application as a service - GitHub Docs](https://docs.github.com/en/actions/hosting-your-own-runners/configuring-the-self-hosted-runner-application-as-a-service)

* Install cocoapods

    ```bash
    sudo gem install cocoapods
    ```

* Install fastlane

    ```bash
    sudo gem install fastlane
    ```

* Homebrew (manual installation to install jq) [The Missing Package Manager for macOS (or Linux) — Homebrew](https://brew.sh/)

    ```python
    /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
    ```

* jq (manual installation)

    ```bash
    brew install jq
    ```

### Xcode commands

```plain
xcode-select --install
sudo xcode-select --switch /Applications/Xcode.app/Contents/Developer
sudo xcodebuild -runFirstLaunch
```

### **Use match to manage certificates and profiles in special repository iOS-secrets.git**

To update certificate and profiles( generate new ones):

* Revoke certificates and profiles from apple developer portal with name "match \*\*\*"
* Remove folders with certificates and profiles from repository iOS-secrets.git (commit)
* Start GitHub workflow iOS\_update\_keys.yml

### **Some notes:**

* MATCH\_GIT\_BASIC\_AUTHORIZATION = account:app\_password in base64

```plain
echo -n your_github_username:your_personal_access_token | base64
```

the value must be in one line, edit if necessary.

* FASTLANE\_OPT\_OUT\_USAGE - disabled Fastlane to sent statistics, if value is "yes"
* Use special keychain in workflow (MacOS) to overcome default keychain access limitation.
* Use fastlane workflows to optimize
* [macOS install | Flutter](https://docs.flutter.dev/get-started/install/macos)
