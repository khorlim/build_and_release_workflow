1. `cd ios`
2. `fastlane init`
3. Edit `Fastfile`:

```ruby
default_platform(:ios)

platform :ios do
  desc "Build and Upload to Test Flight"
  lane :beta do
    api_key = app_store_connect_api_key(
      key_id: ENV["APP_STORE_CONNECT_API_KEY_KEY_ID"],
      issuer_id: ENV["APP_STORE_CONNECT_API_KEY_ISSUER_ID"],
      key_content: ENV["APP_STORE_CONNECT_API_KEY_KEY"]
    )
    sync_code_signing(
      type: "appstore",
      api_key: api_key
    )

    build_app(
      export_method: "app-store"
    )

    upload_to_testflight(
      api_key: api_key,
      changelog: "Testing fastlane"
    )
  end

  desc "Build and Upload to App Store"
  lane :deploy do
    api_key = app_store_connect_api_key(
      key_id: ENV["APP_STORE_CONNECT_API_KEY_KEY_ID"],
      issuer_id: ENV["APP_STORE_CONNECT_API_KEY_ISSUER_ID"],
      key_content: ENV["APP_STORE_CONNECT_API_KEY_KEY"]
    )
    sync_code_signing(
      type: "appstore",
      api_key: api_key
    )

    build_app(
      export_method: "app-store"
    )

    upload_to_app_store(
      api_key: api_key,
      submit_for_review: false,
      automatic_release: false,
      force: true
    )
  end
end
```
