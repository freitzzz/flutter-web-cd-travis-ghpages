## Requirements

- Github account and a repository to host the built web application files
- TravisCI account
- Flutter SDK (beta channel)

## Setup

### Workflow

Before setting up the pipeline on Travis and creating the Flutter Web application, it is necessary to decide which branching model is to be adopted as this decision influences the pipeline design. As this example serves as a guide on how to integrate Flutter Web and TravisCI, a single branch model (Trunk Based Development) will be used.

### Flutter Web

Once the workflow is decided, we need to setup Flutter in order to be able to run and "compile" for the web. As the time that this guide is being written (2020/01/30), Flutter Web is still in its early stages and it is necessary to use the SDK provided in the beta channel. If your Flutter version is still not pointing to the beta channel, you can switch to it by running the following commands:

```
flutter channel beta
flutter upgrade
```

Once on the beta channel you need to enable support for Flutter web. To do such run the following command:

`flutter config --enable-web`

If everything went well `flutter doctor` should now also print the following message:

`[✓] Chrome - develop for the web`

We can now initialize our application. Just like any other Flutter project you perform such with the following command:

`flutter create <name_of_my_app>`

Once created, you can now run it on a Chrome browser (As the time this guide is being written, Flutter Web **debug** [is only available on Chrome](https://flutter.dev/docs/get-started/web#requirements)), with the typical `flutter run` command (add `-d chrome` if other devices such as simulators are detected by Flutter Doctor)

Thats it! You are done with the Flutter Web setup.