# Fastlane Match Tutorial

## Step 1 — Create Signing Repository (One-Time Setup)

You need a private repository to store encrypted certificates.

## Step 2 — Initialize Match (Local Mac in Project Root)

Run this **once** on your Mac (where Xcode already works):

```bash
brew install fastlane
cd ios
fastlane match init
```

When prompted:

- Choose: `git`
- Enter: `https://github.com/your-org/ios-certificates.git`

## Step 3 — Generate App Store Certificates & Profiles

Still on your Mac (in the project root):

```bash
fastlane match appstore
```

## Step 4 — Add Secrets to GitHub

In your GitHub repository → **Settings** → **Secrets** → **Actions**, add the following secrets:

| Name                                  | Value                                       |
| ------------------------------------- | ------------------------------------------- |
| `MATCH_PASSWORD`                      | The password you just entered               |
| `MATCH_GIT_BASIC_AUTHORIZATION`       | GitHub token with access to cert repository |
| `APP_STORE_CONNECT_API_KEY_KEY_ID`    | (if you use API auth)                       |
| `APP_STORE_CONNECT_API_KEY_ISSUER_ID` | (if you use API auth)                       |
| `APP_STORE_CONNECT_API_KEY_KEY`       | (if you use API auth)                       |

## Step 5 — Fastlane Configuration

### `fastlane/Matchfile`

```ruby
git_url("https://github.com/your-org/ios-certificates.git")
storage_mode("git")
type("appstore")
readonly(true) # CI should never create new certs
```

### `fastlane/Fastfile`

```ruby
platform :ios do
  lane :beta do
    sync_code_signing(type: "appstore")

    build_app(
      export_method: "app-store"
    )

    upload_to_testflight
  end
end
```
