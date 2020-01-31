## Requirements

- Github account and a repository to host the built web application files
- TravisCI account
- Flutter SDK (beta channel)

## Setup

### Workflow

Before setting up the pipeline on Travis and creating the Flutter Web application, it is necessary to decide which branching model is to be adopted as this decision influences the pipeline design. As this example serves as a guide on how to integrate Flutter Web and Travis CI, a single branch model (Trunk Based Development) will be used.

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

### Travis CI

For Travis to detect changes in your repository, you need to first register it in the dashboard. Head over to https://travis-ci.org/ if your repository is public, or https://travis-ci.com/ if it is private.

In Travis you implement the pipeline under the *YAML* schema, defined on a file named `.travis.yml`. For a clean setup create this file on the root of your repository.

### Pipeline Machine Configuration

The first lines of .travis.yml normally identify the configuration of the machine to be used to run the pipeline. As Flutter uses `dart2js`, a tool to **transpile** Dart code in Javascript code, there is no constraint related to the operating system environment in order to build the application so you can choose whathever operating system that you desire to build for the web. In this example it is used the `Ubuntu Bionic (18.04)` distribution:

```
os: linux
distro: bionic
```

### Build Phase

Before building the application, Travis needs to have Flutter setup in the machine. One of the ways this can be performed is by defining this setup as steps to be run **before** the build script:

```
before_script:
  - git clone https://github.com/flutter/flutter.git -b beta
  - export PATH=`pwd`/flutter/bin:`pwd`/flutter/bin/cache/dart-sdk/bin:$PATH
  - flutter config --enable-web
```

Now for the build of the application, declare it under `script`:

```
script:
    - flutter build web
```

This command creates all necessary files to represent a Flutter application as a single-page application, and stores them on `build/web` folder inside of your application folder.

### Deploy Phase

Travis already provides support to deploy to Github Pages, using the [`deploy`](https://docs.travis-ci.com/user/deployment/pages/) job, and we only need to configure it for our workflow. The essential configuration is the following:

- `provider` in deploy job as `pages`. By doing so, Travis will recognize that a deploy in Github Pages needs to be done.
- `skip_cleanup` must be true, or else Travis will delete all files created in the build, which prevents the deploy of the static files generated in the application build. 
- `github_token` a [personal access token](https://help.github.com/en/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line) with read and write permissions of the repository which will serve the Github Pages static files. Once you obtain the token, define it as an environment variable in Travis pipeline settings.
- `keep_history` decides to keep the history of the branch in which the files will be pushed to (if set to true, history is kept, otherwise Travis performs a force push).
- `local_dir` the relative path from the pipeline build workspace to the directory in which the static files to be deployed are located.

```
deploy:
  provider: pages
  skip_cleanup: true
  github_token: $GITHUB_TOKEN
  keep_history: true
  local_dir: build/web
```

By default Travis pushes the files to `gh-pages` branch of the repository that triggered the pipeline build. You can configure the deploy to other branch or repository using the `repo` and `target_branch` keys. Take in consideration that if your want to modify the repository to be deployed, you need to include the username of the repository owner (`<repo_owner>/<repo_to_deploy>`). Another cool feature which Travis provides is the set of the custom domain to be used on Github Pages. This is done by modifying the value of `fqdn`. See all the other cool features that Travis provides [here](https://docs.travis-ci.com/user/deployment/pages/).


Thats all, trigger a push/pull request on the build branch and Travis will deploy your Flutter application! Best part? It's all free!

You can see the live version of the classic Flutter Counter [here](https://freitzzz.dev/flutter-web-cd-travis-ghpages).


## References and Acknowledgments

The write of this guide was possible and inspired by the following guides / documentation:

- https://flutter.dev/docs/get-started/web
- https://flutter.dev/docs/development/platform-integration/web
- https://docs.travis-ci.com/user/deployment/pages
- https://medium.com/apolloblog/automatically-build-your-flutter-apps-with-travis-ci-4c1e47a5ae69
- https://github.com/flutter/samples/blob/master/.travis.yml
