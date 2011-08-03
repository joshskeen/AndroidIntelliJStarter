# Android
Download the latest Mac SDK from here: http://developer.android.com/sdk/index.html

Unzip the archive and move the android-sdk-mac_x86 dir to ~/android-sdk-mac_x86. 
*This project assumes that android lives in ~/android-sdk-mac_x86*  You will need to 
fix some paths in several places if you deviate from this.

Add the android tools to the PATH. Assuming the SDK Dir is in ~/android-sdk-mac_x86
and that you are using .bash\_pivotal rather than .bash\_profile or .bashrc:

    # Note: change .bash_pivotal to .bash_profile or .bashrc if needed
    echo "export PATH='$PATH:$HOME/android-sdk-mac_x86/tools'" >> $HOME/.bash_pivotal

Open a new Terminal window and make you can run the `android` command:
 
    # in a new Terminal window:
    android 

Use the Android SDK and AVD Manager to download all of the SDKs you think you need.
If you need Google Maps then install the Google APIs under 
Available packages => Third party Add-ons.

Note: This project assumes you have SDK Platform Android 2.1 installed. You can change this in 
`default.properties`

# Bootstrapping AndroidIntelliJStarter
Create your local.properties

    android update project -p .

*If this fails* with the following error then you do not have the SDK installed referenced
in default.properties.  Update default.properties or install the SDK specified there.

    Error: The project either has no target set or the target is invalid.
    Please provide a --target to the 'android update' command.

## IntelliJ Platform SDKs
IntelliJ stores Platform SDK configurations somewhere outside of individual projects. Upshot: IntelliJ 
SDKs are not committed in git and you will need to manually add them.

File => Project Structure

Platform Settings => SDKs

Add (plus sign) => Android SDK

locate and choose ~/android-sdk-mac_x86

Choose Java SDK 1.6

Choose your Android SDK. If you don't see it there you will need to install it via the 
Android SDK and AVD Manager. See "Android" above.

## Setting AndroidIntelliJStarter's SDK
Now you need to set the app's SDK.

File => Project Structure

Modules => AndroidIntelliJStarter => Dependencies

Module SDK: choose one.


# Robolectric
robolectric starts out as a read-only submodule of http://github.com/pivotal/robolectric (HEAD).

## Initializing Robolectric
    git submodule update --init
    (cd submodules/robolectric && git checkout master)
    (cd submodules/robolectric && ant clean test) # make sure it runs

## Forking Robolectric
We recommend forking robolectric for your project.

Start by removing the default pivotal/robolectric

Delete the relevant line from the .gitmodules file.

Delete the relevant section from .git/config.

Clean up git and directories

    git rm --cached submodules/robolectric
    rm -rf submodules

Add your own fork

    git submodule add git://github.com/***YOUR-REPO-HERE***/robolectric.git submodules/robolectric
    git submodule init
    (cd submodules/robolectric && ant clean test)


## Contributing back to Robolectric
Contributing back to pivotal/robolectric is accomplished by following the official Github fork
model: http://help.github.com/fork-a-repo/

    # add pivotal/robolectric HEAD as an upstream remote
    cd submodules/robolectric
    git remote add upstream git://github.com/pivotal/robolectric.git
    git fetch upstream

    # merge pivotal/robolectric into your fork
    git merge upstream/master

Next, make a pull request as your client user: http://help.github.com/send-pull-requests/

The pull request can be handled by someone with commit right to robolectric, maybe even you!
See "Managing Pull Requests" at http://help.github.com/send-pull-requests/.

# Roboguice
This project is set up by default to use Roboguice for dependency injection.  See MySampleApplication.ApplicationModule
and SampleRoboguiceTestRunner.TestApplicationModule for injection configuration.  See StarterActivityWithRoboguiceTest
for an example of injection usage.

## To remove Roboguice injection:
- Delete MySampleApplication and remove references
- Delete SampleRoboguiceTestRunner and remove references
- Delete guice* and roboguice* jars in libs/main and libs-src/
- Remove all uses of @Inject, @InjectView, etc.
- Remove reference to MySampleApplication from AndroidManifest

# C2DM Support
We have added base support for C2DM. C2DM is Google's push notification service for Android and is available in
API v. 2.2 and above, though is safely ignored in lower versions.

While 2.2 devices are support C2DM, Google does not provide API code for integrating with the service -- no
registration, unregistration, or notification-receipt handling code. Google suggests copying code from one of
their sample projects for this support, which we have done.  See com.google.android.c2dm. Note that we modified
com.google.android.c2dm.C2DMBaseReceiver to support Roboguice.

To handle C2DM notifications you will need to implement C2DMReceiver, which is currently empty but heavily documented.

## C2DM Resources:
- Official C2DM site: http://code.google.com/android/c2dm
- Pivotal Blog articles: http://pivotallabs.com/blabs/categories/c2dm
- Sample code: http://www.google.com/codesearch#JWblrwroAxw/trunk/android/c2dm/com/google/android/c2dm/&q=C2DMBaseReceiver&type=cs

## To Remove C2DM:
- Remove C2DMReceiver and test
- Delete com.google.android.c2dm
- Remove the C2DM Section of AndroidManifest.xml