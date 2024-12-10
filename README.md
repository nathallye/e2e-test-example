# End-to-end testing example with EAS build on Expo

This repository demonstrates how to configure and run end-to-end testing with EAS Build on Expo, step by step

## Table of Contents

1. [Configuring EAS Build](#configuring-eas-build)
2. [Expo Dashboard Configuration](#expo-dashboard-configuration)
3. [Running the Application](#running-the-application)
4. [Installing Maestro](#installing-maestro)
5. [Creating Your First Test](#creating-your-first-test)
6. [Building on EAS](#building-on-eas)
7. [Updating `eas.json`](#updating-easjson)

## Configuring EAS Build

Expo Application Services (EAS) is a cloud build service used to build and publish React Native apps

1. **Initialize EAS:**

   ```bash
   eas init
   ```

   > If the CLI is not installed, use: `npm i -g eas-cli`

2. **Configure all platforms:**

   ```bash
   eas build:configure
   ```

   > This command generates an `eas.json` file

## Expo Dashboard configuration

Disable the new Android build infrastructure in your project's **Expo dashboard**:

1. Navigate to **Project Settings**.
2. Disable **New Android Builds Infrastructure**.

> The new infrastructure speeds up builds by 40%, but it lacks virtualization support required to start the Android emulator. This step is only necessary for Android

## Running the application

Run end-to-end tests using **Expo Go** or a development build

1. **Pre-build the application:**

   ```bash
   npx expo prebuild
   ```

   > This pre-builds for both platforms

2. **After pre-build, run the application:**

   ```bash
   npm run android
   ```

   > The command works for iOS as well

## Installing Maestro

Maestro is a powerful tool for running end-to-end tests on mobile apps using simple YAML files to describe user interactions

1. **Install Maestro:**

   ```bash
   curl -fsSL "https://get.maestro.mobile.dev" | bash
   ```

   > Documentation: [Installing Maestro](https://maestro.mobile.dev/getting-started/installing-maestro).

2. **Set PATH environment variable:**

   ```bash
   export PATH="$PATH":"$HOME/.maestro/bin"
   ```

3. **Verify installation:**

   ```bash
   maestro
   ```

## Creating our first test

1. Create a `maestro` directory in the root of your project.
2. Inside the directory, create a new YAML file for your first test, e.g., `home.yaml`.

### Example `home.yaml` Flow:

```yaml
appId: "com.anonymous.e2etestexample"
---
- launchApp
- assertVisible: "Welcome!"
```

> This flow launches the app and verifies that a "Welcome!" text is visible.

3. **Run the test:**

   ```bash
   maestro test maestro/home.yaml
   ```

## Building on EAS

1. **Creating a custom Build workflow:**

   - Create a `.eas` folder in our project.
   - Inside it, create a `build-and-maestro-test.yaml` file:

     ```yaml
     build:
       name: Create a build and run Maestro tests on it
       steps:
         - eas/build
         - eas/maestro_test:
             inputs:
               flow_path: |
                 maestro/home.yaml
                 maestro/expand_test.yaml
     ```

   > Refer to the [Expo documentation](https://docs.expo.dev/build-reference/e2e-tests/#create-a-custom-build-workflow-for-running-maestro-e2e-tests).

## Updating `eas.json`

Add a new build profile for the custom workflow:

```json
{
  "build": {
    "build-and-maestro-test": {
      "withoutCredentials": true,
      "config": "build-and-maestro-test.yml",
      "android": {
        "buildType": "apk",
        "image": "latest"
      },
      "ios": {
        "simulator": true,
        "image": "latest"
      }
    },
    "development": {
      "developmentClient": true,
      "distribution": "internal"
    }
  }
}
```

## Running the custom Build

Run the following command:

```bash
eas build --profile build-and-maestro-test
```

Select all platforms to start the build. You can monitor progress in the **Expo dashboard** under the **Builds** section.
