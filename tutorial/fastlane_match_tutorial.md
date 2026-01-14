Step 1 — Create signing repo (1 time only)

You need a private repo to store encrypted certs.

Step 2 — Initialize Match (local Mac in the targeted project root)

Run this ONCE on your Mac (where Xcode already works):

brew install fastlane
cd ios
fastlane match init

Choose:
git

Enter:
https://github.com/your-org/ios-certificates.git

Step 3 — Generate App Store certs & profiles

Still on your Mac (targeted project root):

fastlane match appstore

Step 4 — Add secrets to GitHub

In your GitHub repo → Settings → Secrets → Actions

Add:
Name Value
MATCH_PASSWORD The password you just entered
MATCH_GIT_BASIC_AUTHORIZATION GitHub token with access to cert repo
APP_STORE_CONNECT_API_KEY_KEY_ID (if you use API auth)
APP_STORE_CONNECT_API_KEY_ISSUER_ID
APP_STORE_CONNECT_API_KEY_KEY

Step 5 — Fastlane configuration
fastlane/Matchfile
git_url("https://github.com/your-org/ios-certificates.git")
storage_mode("git")
type("appstore")
readonly(true) # CI should never create new certs

fastlane/Fastfile
platform :ios do
lane :beta do
sync_code_signing(type: "appstore")

    build_app(
      export_method: "app-store"
    )

    upload_to_testflight

end
end
