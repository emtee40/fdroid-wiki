The procedure is as follows:
1. Install Sdkmanager either via [pip](https://pypi.org/project/sdkmanager/) or the [Linux package manager](https://pkgs.org/search/?q=sdkmanager).
2. Run the command ``sudo mkdir /opt/android-sdk/`` to make the Directory where the Android emulator  folders will be stored.
3. Run the command `` sudo chown $USER /opt/android-sdk `` to make the folder and path accessible without using sudo permissions.
4. To install the emulator, execute ``sdkmanager 'emulator' `` from the terminal.
5. Install latest cmdline tools using ``sdkmanager "cmdline-tools;latest" `` command.
6. Add ANDROID_HOME (also ANDROID_SDK_ROOT) to path, by running `` source /etc/profile ``.
7. Install sdkmanager platform for android 31 using ``sdkmanager "platforms;android31" ``
8. Create an Android Virtual Device (AVD) with the command `` /opt/android-sdk/cmdline-tools/latest/bin/avdmanager create avd --name "Some_preffered_name" --tag default --package "system-images;android-31;default;x86_64" --sdcard 64M --device "Nexus 5" ``
Please note that "Some_preffered_name" is a substitute for a name you would like to use.E.g Nexus_5_API_31
9. To start the emulator, run `` /opt/android-sdk/emulator/emulator @Some_preffered_name `` and you are all set.
