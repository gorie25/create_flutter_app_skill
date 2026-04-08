# Flutter Base App - Clean Architecture Skill

## Description
This skill sets up a Flutter project with Clean Architecture from scratch.  
It includes a predefined application folder structure, state management with Mobx following binding object, dependency injection, and core utilities.

---

### 1. Ask For App Name
Before creating the project, ask:

`What would you like to name the application?`

Use the answer as the Flutter project name.

### 2. Choose Flutter Version
Before creating the project, decide which Flutter version you want to use. You can manage it with `fvm`.  
For example:

- Latest stable: `fvm install stable`
- Specific version: `fvm install 3.13.0`

Then set the project version:

- `fvm use stable`
- `fvm use 3.13.0`

> Note: Make sure `fvm` is installed on your system.

### 3. Create Flutter Project
After selecting the version and app name, run:

```bash
fvm flutter create <app_name>
cd <app_name>
```

Replace `<app_name>` with the name provided by the user.

### 4. Ask Before Folder Setup
Before creating the application folders, ask:

`Does the application require login?`

- If the answer is `yes`, include `lib/oauth2/`.
- If the answer is `no`, do not create `lib/oauth2/`.

### 5. Set Up Folder Structure
Create the base structure inside `lib/` like this:

lib/
├── config/
├── core/
├── extensions/
├── firebase/
├── gen/
├── helpers/
├── modules/
│   └── example/
│       ├── data/
│       │   ├── datasources/
│       │   └── repositories/
│       ├── domain/
│       │   ├── models/
│       │   └── usecases/
│       └── presentation/
│           └── UI/
├── retrofit/
├── shared/
└── storage/

If the app requires login, also add:

lib/
└── oauth2/

### 6. Add Dependencies
Add the required packages to `pubspec.yaml`:

```yaml
dependencies:
  flutter:
    sdk: flutter
  flutter_localizations:
    sdk: flutter
  intl: ^x.x.x
  dio: ^x.x.x
  injectable: ^x.x.x
  mobx: ^x.x.x
  equatable: ^x.x.x
  shared_preferences: ^x.x.x

dev_dependencies:
  flutter_gen_runner: ^x.x.x
  build_runner: ^x.x.x
  injectable_generator: ^x.x.x
  flutter_lints: ^x.x.x
```
