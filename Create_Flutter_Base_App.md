---
name: create-flutter-base-app
description: Create a new Flutter base app from scratch with Clean Architecture, MobX, get_it/injectable, retrofit, local storage, optional OAuth2 login, and a runnable example feature. Use when Codex needs to scaffold a Flutter project, choose an FVM Flutter version, mirror conventions from a local `example/` reference app, generate a production-ready base structure, or iteratively improve this skill while implementing the app.
---

# Create Flutter Base App

Create a runnable Flutter base app, not just empty folders.

## Workflow

1. Ask for the app name.
Ask: `What would you like to name the application?`

2. Ask for the Flutter version.
Ask which Flutter version to use. Prefer `fvm` when it is available.

3. Verify `fvm`.
If `fvm` is available, use it.

If `fvm` is unavailable, continue with the locally available `flutter` command instead.
If the user explicitly asks to skip `fvm`, continue with the locally available `flutter` command instead even when `fvm` is installed.
If both `fvm` and `flutter` are unavailable, scaffold the project structure and source files manually, then clearly report that dependency installation and code generation could not be run in the current environment.

4. Create the project.
Run the matching commands for the available tool:

If using `fvm`:

```bash
fvm install <flutter_version>
fvm use <flutter_version>
fvm flutter create <app_name>
```

If using plain `flutter`:

```bash
flutter create <app_name>
```

5. Run the app setup commands.

After the project is created, run:

```bash
cd <app_name>
flutter pub get
flutter run
```

6. Ask whether the app requires login.
Ask: `Does the application require login?`

7. Add dependencies.
Update `pubspec.yaml` with these placeholders and keep versions as `x.x.x` so they can be chosen to match the selected Flutter version:

```yaml
dependencies:
  flutter:
    sdk: flutter
  flutter_localizations:
    sdk: flutter
  intl: ^x.x.x
  dio: ^x.x.x
  get_it: ^x.x.x
  injectable: ^x.x.x
  mobx: ^x.x.x
  flutter_mobx: ^x.x.x
  equatable: ^x.x.x
  shared_preferences: ^x.x.x
  retrofit: ^x.x.x
  json_annotation: ^x.x.x

dev_dependencies:
  flutter_gen_runner: ^x.x.x
  build_runner: ^x.x.x
  injectable_generator: ^x.x.x
  retrofit_generator: ^x.x.x
  mobx_codegen: ^x.x.x
  json_serializable: ^x.x.x
  flutter_lints: ^x.x.x
```

8. Read the local reference app before writing feature code.
Read every file under `example/`, especially:

- `example/main.dart`
- `example/locator.dart`
- `example/shared/app_theme.dart`
- `example/retrofit/`
- `example/modules/`

Infer and follow the reference app's:

- folder organization
- locator pattern
- naming rules
- DI wiring style
- datasource and repository structure
- use case structure
- model patterns
- presentation structure
- theme structure for colors and text styles
- retrofit service patterns

If this document conflicts with `example/`, follow `example/`.

9. Scaffold the base structure.
Create these folders under `lib/`:

```text
lib/
├── config/
│   ├── routes/
├── core/
│   ├── base/
│   ├── constants/
│   ├── errors/
│   ├── network/
│   └── services/
├── extensions/
├── firebase/
├── gen/
├── helpers/
├── modules/
│   └── example/
│       ├── app/
│       │   ├── widgets/
│       │   ├── example_page.dart
│       │   └── example_page_viewmodel.dart
│       ├── data/
│       │   ├── datasources/
│       │   └── repositories/
│       ├── domain/
│       │   ├── models/
│       │   ├── repositories/
│       │   └── usecases/
│       └── locator/
├── retrofit/
├── shared/
│   ├── constants/
│   ├── theme/
│   ├── widgets/
│   └── utils/
└── storage/
```

If login is required, also create:

```text
lib/oauth2/
```

Use lowercase directory names.

## Architecture Rules

Use feature-first Clean Architecture.

For each feature:

- keep Flutter UI code in `app/`
- keep page widgets and their companion viewmodels in the same `app/` folder
- keep feature-specific reusable widgets in `app/widgets/`
- keep repository contracts in `domain/repositories/`
- keep repository implementations in `data/repositories/`
- keep datasources in `data/datasources/`
- keep use cases in `domain/usecases/`
- keep models in `domain/models/` unless the reference app shows a different convention

Do not let `domain` depend on Flutter widgets or implementation details.

## State Rules

Use MobX for state management.

Create, at minimum, for each screen:

- one page file
- one page viewmodel file

Find and read the page and page viewmodel files under `example/modules/app`, then follow that pattern for file placement, responsibilities, and naming.

Name files using:

- `<feature_or_screen_name>_page.dart`
- `<feature_or_screen_name>_page_viewmodel.dart`

Put observables, computed values, and actions in the viewmodel.
Keep widget rendering and layout logic in the page.
Use `Observer` in the UI when using MobX, or follow the same reactive pattern as the reference app when it differs.
Generate MobX files with `mobx_codegen`.

Use this reusable starter pattern when scaffolding a new screen.

Example file layout:

- `lib/modules/<feature>/app/<name>_page.dart`
- `lib/modules/<feature>/app/<name>_page_viewmodel.dart`
- `lib/modules/<feature>/app/widgets/`

Base page template:

```dart
import 'package:flutter/material.dart';
import 'package:flutter_mobx/flutter_mobx.dart';
import 'package:<app_name>/locator.dart';
import 'package:<app_name>/modules/<feature>/app/<name>_page_viewmodel.dart';
import 'package:<app_name>/shared/app_theme.dart';

class <Name>Page extends StatefulWidget {
  const <Name>Page({super.key});

  @override
  State<<Name>Page> createState() => _<Name>PageState();
}

class _<Name>PageState extends State<<Name>Page> {
  late final <Name>PageViewModel viewModel;

  @override
  void initState() {
    super.initState();
    viewModel = locator<<Name>PageViewModel>();
    WidgetsBinding.instance.addPostFrameCallback((_) {
      viewModel.initState();
    });
  }

  @override
  void dispose() {
    viewModel.disposeState();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        backgroundColor: AppTheme.white,
        title: Text(
          '<Screen Title>',
          style: AppTheme.blackDark_16w600,
        ),
      ),
      body: RefreshIndicator(
        onRefresh: viewModel.refreshState,
        child: Observer(
          builder: (_) {
            if (viewModel.items.isEmpty) {
              return const SingleChildScrollView(
                physics: AlwaysScrollableScrollPhysics(),
                child: SizedBox(
                  height: 400,
                  child: Center(child: Text('No data')),
                ),
              );
            }

            return ListView.builder(
              physics: const AlwaysScrollableScrollPhysics(),
              itemCount: viewModel.items.length,
              itemBuilder: (context, index) {
                final item = viewModel.items[index];
                return ListTile(
                  title: Text(
                    item.title,
                    style: AppTheme.blackDark_14w400,
                  ),
                );
              },
            );
          },
        ),
      ),
    );
  }
}
```

Base viewmodel template:

```dart
import 'dart:async';

import 'package:injectable/injectable.dart';
import 'package:mobx/mobx.dart';
import 'package:<app_name>/modules/<feature>/domain/models/<entity>.dart';
import 'package:<app_name>/modules/<feature>/domain/usecases/get_<entities>_usecase.dart';

part '<name>_page_viewmodel.g.dart';

@injectable
class <Name>PageViewModel = _<Name>PageViewModel with _$<Name>PageViewModel;

abstract class _<Name>PageViewModel with Store {
  _<Name>PageViewModel(this._get<Entities>Usecase);

  final Get<Entities>Usecase _get<Entities>Usecase;

  StreamSubscription? _listener;

  @observable
  ObservableList<<Entity>> items = ObservableList<<Entity>>();

  @action
  void setItems(List<<Entity>> value) {
    items = ObservableList<<Entity>>.of(value);
  }

  Future<void> initState() async {
    await refreshState();
    await registerListener();
  }

  Future<void> registerListener() async {}

  @action
  Future<void> refreshState() async {
    try {
      final result = await _get<Entities>Usecase.run();
      setItems(result);
    } catch (_) {}
  }

  void disposeState() {
    _listener?.cancel();
  }
}
```

Rules for adapting this template:

- keep `initState`, `refreshState`, and `disposeState` in the viewmodel
- keep formatting helpers and small UI-only getters in the page when they only serve rendering
- move business state and side-effect listeners into the viewmodel
- inject the viewmodel from `locator` in the page, matching the example pattern
- add feature widgets under `app/widgets/` instead of expanding the page file too much

## Dependency Injection Rules

Use `get_it` with `injectable`.
Set up DI using the same locator pattern used by `example/`.

Create locator setup for the feature, for example:

- `lib/modules/example/locator/example_locator.dart`

Register:

- app services
- datasources
- repositories
- use cases
- page viewmodels

Resolve dependencies through the locator, not by manual instantiation inside widgets.

## Networking Rules

Use `dio` with `retrofit`.

Create starter networking code for:

- dio client setup
- retrofit service declaration
- interceptor placeholder
- exception or failure mapping

Follow the structure and naming from `example/retrofit/` when present.

## Theme Rules

Read `example/shared/app_theme.dart` before creating the app theme.

Follow these rules for theme structure:

- define app colors as static tokens inside `AppTheme`
- define reusable text styles as named static `TextStyle` tokens inside `AppTheme`
- prefer semantic token names that combine color, size, and weight when the reference app does that
- keep page code consuming shared `AppTheme` tokens instead of ad hoc inline text styles whenever possible
- create the initial color palette and text-style tokens before building feature UI
- when the reference app already has a style-token naming pattern, follow that pattern instead of inventing a new one

## Storage Rules

Use `shared_preferences` for lightweight local storage.
Create a small abstraction for app settings and token placeholders when needed.

If OAuth2 is enabled, create `lib/storage/share_pref.dart` and use it as the shared preferences access layer for auth tokens.

Use this starter structure:

```dart
import 'package:injectable/injectable.dart';
import 'package:shared_preferences/shared_preferences.dart';

late final SharePref sharePref;

@lazySingleton
class SharePref {
  SharePref(this._sharedPreferences) {
    sharePref = this;
  }

  final SharedPreferences _sharedPreferences;

  static const String _accessTokenKey = 'access_token';
  static const String _refreshTokenKey = 'refresh_token';

  Future<bool> setAccessToken(String value) => _sharedPreferences.setString(_accessTokenKey, value);

  String? getAccessToken() => _sharedPreferences.getString(_accessTokenKey);

  Future<bool> setRefreshToken(String value) => _sharedPreferences.setString(_refreshTokenKey, value);

  String? getRefreshToken() => _sharedPreferences.getString(_refreshTokenKey);
}
```

Register `SharedPreferences` and `SharePref` through the app `Locator` so the helper can be resolved by DI.
Only add `lib/storage/share_pref.dart` when the application has OAuth2 or login enabled.

## Authentication Rules

If login is enabled, scaffold `lib/oauth2/` with starter files for:

- token model or auth model
- auth service or repository contract
- token persistence helper
- auth interceptor placeholder

When login is enabled, also:

- create `lib/storage/share_pref.dart`
- expose a global `sharePref` instance from that file
- add `setAccessToken`, `getAccessToken`, `setRefreshToken`, and `getRefreshToken`
- register dependencies through `Locator`

If login is disabled, do not create `lib/oauth2/`.
If login is disabled, do not create `lib/storage/share_pref.dart`.

## Required Starter Files

Generate starter files, not only folders.

At minimum, create:

- `lib/main.dart`
- `lib/app.dart`
- route setup
- theme setup
- networking setup
- base error or failure classes
- local storage setup
- locator setup for the example feature
- one complete example feature wired end-to-end

The example feature should include equivalents of:

- page
- page viewmodel
- model
- repository contract
- repository implementation
- datasource
- use case

## Naming Rules

Use:

- `snake_case` for files
- `PascalCase` for classes
- lowercase directory names

When a naming or organization rule is visible in `example/`, follow `example/`.

For UI files, prefer the same naming and placement as the reference app:

- `lib/modules/<feature>/app/<name>_page.dart`
- `lib/modules/<feature>/app/<name>_page_viewmodel.dart`
- `lib/modules/<feature>/app/widgets/`

## Finish Steps

After scaffolding:

```bash
fvm flutter pub get
fvm dart run build_runner build --delete-conflicting-outputs
```

If generation fails, fix the scaffold until it succeeds.

## Acceptance Criteria

Do not consider the task complete until all of these are true:

- the Flutter project is created with the requested name
- the selected Flutter version is applied through `fvm`
- the base folder structure exists
- required starter files exist
- dependencies are added to `pubspec.yaml`
- `fvm flutter pub get` succeeds
- `fvm dart run build_runner build --delete-conflicting-outputs` succeeds
- the generated app compiles
- the example feature is wired through locator-based DI
- auth starter files exist when login is enabled
- the generated project follows conventions learned from `example/`

## Self-Update Rule

Improve this skill while implementing.

If you discover a missing or better rule from:

- the `example/` reference app
- the generated project
- repeated implementation friction

then update this skill file before continuing.

Persist new rules into the skill document instead of keeping them only in memory.
Keep updates concise and structured.
l
