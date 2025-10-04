## Setup Fastlane for Flutter

## Install Fastlane (recommended via Bundler)

0. Install [Ruby with Devkit](https://rubyinstaller.org/downloads/#:~:text=column%20for%20recommendations.-,WITH%20DEVKIT) in your Device

1. Create a `Gemfile` at the project root (optional but recommended for reproducible Fastlane version):

```ruby
source 'https://rubygems.org'
gem 'fastlane'
```

2. Install with Bundler:

```bash
# on Windows/macOS/git-bash
bundle install
# or install globally (not recommended):
# gem install fastlane
```

3. Run fastlane using `bundle exec fastlane ...` to ensure the right version.

## Project layout (recommended)

- `android/fastlane/` — Android `Fastfile`, `Appfile`, and metadata.
- `ios/fastlane/` — iOS `Fastfile`, `Appfile` (or you can keep a single top-level `fastlane/` if you prefer).

For Flutter projects, the `fastlane` folders often go inside each platform directory so Fastlane commands run with the right context.

## Android: example Fastfile and setup (if run locally)

Create `android/fastlane/Fastfile` (example lanes below). This lane uses `gradle` tasks to run the Flutter build and then uses `supply` to upload the AAB.

Example `Fastfile` (Android section):

```ruby
default_platform(:android)

platform :android do
  desc "Build a release AAB and upload to Play Store internal track"
  lane :release do
    # Clean and build the appbundle using flutter
    sh "flutter clean"
    sh "flutter pub get"
    sh "flutter build appbundle --release"

    # Path to your generated AAB
    aab_path = Dir["build/app/outputs/bundle/release/app-release.aab"]).first

    # Upload to Play Store using supply
    supply(
      aab: aab_path,
      track: ENV['PLAYSTORE_TRACK'] || 'internal',
      json_key: ENV['PLAYSTORE_JSON_KEY']
    )
  end
end
```

Notes:

- `PLAYSTORE_JSON_KEY` should be set to a file path on the runner (or base64-decoded into a file in CI). Many CI setups store the JSON as a secret and write it to a file before running fastlane.
- Adjust the `aab_path` if your Flutter or Android Gradle outputs differ.

Android signing (keystore):

- Configure `android/app/build.gradle` to load signing config from environment variables or a `keystore.properties` file that is not committed.
- Typical `keystore.properties` keys: `storeFile`, `storePassword`, `keyAlias`, `keyPassword`.

Example snippet to write the Play JSON key in CI (GitHub Actions, runner):

```bash
echo "$PLAYSTORE_JSON" > /tmp/playstore.json
export PLAYSTORE_JSON_KEY=/tmp/playstore.json
bundle exec fastlane android release
```

where `PLAYSTORE_JSON` is stored in the secret store and contains the JSON service account content.

## iOS: example Fastfile and setup

Important: iOS builds and App Store uploads must run on macOS (Xcode). For signing you can use `match` (recommended) or manual certificate provisioning.

Example `ios/fastlane/Fastfile` lane summary:

```ruby
default_platform(:ios)

platform :ios do
  desc "Build and upload to TestFlight / App Store"
  lane :release do
    # sync certificates and profiles (match stores certs in a git repo or storage)
    match(type: "appstore", readonly: false)

    # increment build number
    increment_build_number

    # build ipa using gym or build_app
    build_app(scheme: "Runner")

    # upload to TestFlight/App Store
    upload_to_app_store(skip_wait_for_build_processing: false)
  end
end
```

App Store Connect API Key (recommended):

- Create an API key in App Store Connect (Keys section) and download the `.p8` file. Store the key ID, issuer ID, and path to the `.p8` as environment variables or use `FASTLANE_APPLE_APPLICATION_SPECIFIC_PASSWORD` for other flows.

Environment variables commonly used for iOS CI:

- `MATCH_GIT_URL` (if using match with a private git repo for certificates)
- `FASTLANE_USER` (Apple ID)
- `FASTLANE_APPLE_APPLICATION_SPECIFIC_PASSWORD` (if needed)

If using `match` with a git repository, ensure the CI runner has read/write access to the repo.

## Example Appfile (shared settings)

Create `fastlane/Appfile` or `android/fastlane/Appfile` / `ios/fastlane/Appfile` with:

```ruby
app_identifier("com.example.app") # bundle id
apple_id("user@example.com") # your Apple ID

# Optional: Google Play json key path for supply
# json_key("/path/to/playstore.json")
```
