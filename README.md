# Mishakorobov Centralized CI/CD Workflows (FinOps)

Централизованный репозиторий переиспользуемых GitHub Actions (`workflow_call`) для экосистемы тренажёров Росгвардии:
- `Mishakorobov/Bilety_vohr_testy_2026`
- `Mishakorobov/private_guard_bilety`
- `Mishakorobov/rosguard-tickets-data`

## Переиспользуемые пайплайны

1. **[`reusable-flutter-ci.yml`](.github/workflows/reusable-flutter-ci.yml)** — Двухконтурный Flutter CI:
   - `fast_verify`: единая job с `flutter analyze --no-fatal-infos` + `flutter test` (~1.5 мин, без Gradle).
   - `build_debug_apk`: условная сборка Debug APK (только если изменены `android/**` / `pubspec.*`, добавлен лейбл `build-apk` на PR или принудительный запуск), с кэшем Gradle (`gradle/actions/setup-gradle@v6`, read-only для PR).
2. **[`reusable-android-release.yml`](.github/workflows/reusable-android-release.yml)** — Автоматический Release Pipeline:
   - Валидация совпадения тега `vX.Y.Z` с `pubspec.yaml`, прогон тестов, сборка `--release` APK, генерация `SHA256SUMS.txt`, сохранение R8 `mapping.txt` (`retention-days: 3`) и публикация GitHub Release.
3. **[`reusable-sync-assets.yml`](.github/workflows/reusable-sync-assets.yml)** — FinOps-синхронизация `assets/questions*.json`:
   - Скачивание манифеста и JSON из `rosguard-tickets-data`, проверка SHA-256, прогон `flutter analyze` + `flutter test` в той же job и создание PR через `peter-evans/create-pull-request@v8`.
4. **[`reusable-cache-janitor.yml`](.github/workflows/reusable-cache-janitor.yml)** — Еженедельная очистка устаревших кэшей закрытых/PR веток и FinOps-отчёт по лимиту 10 GB.
