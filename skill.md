# Flutter Base App - Clean Architecture Skill

## Description
This skill sets up a Flutter project with Clean Architecture from scratch.  
It includes layered structure (presentation, domain, data), state management with BLoC, dependency injection, and core utilities.

---

### 1. Choose Flutter Version
Before creating the project, decide which Flutter version you want to use.  
For example:

- Latest stable: `flutter channel stable && flutter upgrade`
- Specific version: `flutter version 3.13.0`

> Note: Make sure the chosen version is installed on your system.

### 2. Create Flutter Project
After selecting the version, run:
flutter create my_clean_app
cd my_clean_app

### 3. Set up folder 
lib/modules/counter/
 ├── data/
 │   ├── datasources/
 │   └── repositories/
 ├── domain/
 │   ├── models/
 │   └── usecases/
 └── presentation/
 |    ├── UI/
 |   
 └── /
     ├── bloc/
     └── pages/


### 4.Add Dependencies
dependencies:
  flutter:
    sdk: flutter
  flutter_bloc: ^x.x.x
  dio: ^x.x.x
  freezed_annotation: ^x.x.x
  injectable: ^x.x.x
  get_it: ^x.x.x
  equatable: ^x.x.x

dev_dependencies:
  flutter_test:
    sdk: flutter
  build_runner: ^x.x.x
  freezed: ^x.x.x
  injectable_generator: ^x.x.x
  flutter_lints: ^x.x.x
