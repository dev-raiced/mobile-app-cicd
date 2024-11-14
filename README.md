# Intro
This is my Azure DevOps pipeline exported and adapted from Microsoft App Center. As it's going to be retired in March 2025. You can find the instructions how to do your migration and what was changed from App Center Build (which use the same pipeline infra as Azure DevOps).

I will try to keep it up to date with further improvements and new version of XCODE, Android, etc...

# Steps

1. Setup Azure DevOps with Git repo
1. Install those extensions from Marketplace to Azure DevOps: `Google Play`, `Apple App Store`
1. Setup Service Connections in your project
    - Get token/credentials from your Google and Apple accounts
1. Go to App Center to prepare the following
    - Create a Variable Group in Az DevOps and copy App Center Build environment variables
    - `Apple`
        - Download Apple Certificate (.p12) and Apple profile file (.mobileprovision)
        - Add the 2 files to DevOps Library as Secure Files
        - Add the passwords/key to the group variable
    - `Android`
        - Download the keystore file (.keystore)
        - Add the file to DevOps Library as Secure Files
        - Add the passwords/key to the group variable
    d. Give permissions to your pipeline to access Secure Files and Variable Group
1. In the App Center you can Export the pipeline as yaml and add it to your Azure DevOps repo (you can find mine anonymized in `./export-app-center` folder)
1. Create an Azure DevOps pipeline from the file in the repo
1. Remove all these tasks:
    - AppCenterDistribute@3
    - Run Jetify
    - ...
1. Remove all these pipeline variables:
    - name: $(Build.Id)
    - AppExtensionProvisionSecureFiles: '[]'
    - system.debug: 'false'
    - lastCommitMessage: 'attempt android '
    - pr.check.id: ''
    - pr.status.id: ''
    - user-defined.JAVA_HOME: $(JAVA_HOME_17_X64)
    - MOBILECENTER_OUTPUT_DIRECTORY: $(Build.ArtifactStagingDirectory)/build
    - MOBILECENTER_BRANCH: development
    - MOBILECENTER_BUILD_ID: $(Build.BuildNumber)
    - MOBILECENTER_SOURCE_DIRECTORY: $(Build.Repository.LocalPath)
    - MOBILECENTER_TRIGGER: manual
    - APPCENTER_BRANCH: development
    - APPCENTER_TRIGGER: manual
    - testSDK
    - buildScripts.toolset: '{"package.json":{"preBuild":"appcenter-pre-build.sh"}}'
    - javaScriptRunTests: 'false'
    - MOBILECENTER_REACTNATIVE_PACKAGE: package.json
    - moduleName: app
    - buildVariant: release
    - isRoot: 'false'
    - runTests: 'false'
    - runLint: 'false'
    - keystorePassword: null
    - keyAlias: null
    - keyPassword: null
    - keystoreFilename: <your-keystoreFilename>
    - keystoreSecureFileId: <your-keystoreSecureFileId>
    - keystoreSecureFilename: <your-keystoreSecureFilename>
    - keystoreEncoded: null
    - automaticSigning: 'false'
    - buildBundle: 'true'
    - APPCENTER_ANDROID_MODULE: app
    - APPCENTER_ANDROID_VARIANT: release
    - VSMobileCenterUpload.ContinueIfSymbolsNotFound: 'true'
    - destinationSubtype: googleplay

1. Add the task to publish to Apple App Store
1. Add the task to publish to Google Play Store

# Improvements

- Auto increment patch version
- Android: Use new Gradle@3
- Android: Publish mapping.txt linked with AAB
- iOS: Update to XCODE 15.4

# Notes
- Careful with the keystore / certificate password if it contains special characters