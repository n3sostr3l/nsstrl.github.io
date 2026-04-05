# Отчет по лабораторной работе №2
## Создание и развертывание сайта на sourcecrfaft. CI/CD для развертывания статического сайта на github и sourcecraft

# Отчёт по автоматическому развертыванию статического сайта на MkDocs
## (Sourcecraft + GitHub Pages через GitHub Actions)

### 1. Выполненные действия

#### 1.1. Установка MkDocs и создание сайта

```bash
pip install mkdocs
mkdocs new my-site
cd my-site
echo "site_name: Мой статический сайт" > mkdocs.yml
mkdocs build
```
#### 1.2. Инициализация Git и добавление удалённых репозиториев
```bash
git init
git add .
git commit -m "commit msg"
git remote add origin https://github.com/my-kos/lab3.git
git remote add sourcecraft https://my-kos:ТОКЕН@git.sourcecraft.dev/my-kos/lab3.git
```
#### 1.3. Создание GitHub Actions workflow

Файл .github/workflows/deploy.yml:

```yaml
name: Deploy MkDocs to GitHub Pages
on:
  push:
    branches: [ main ]
permissions:
  contents: write
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: '3.x'
      - run: pip install mkdocs
      - run: mkdocs build
      - uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./site
          publish_branch: gh-pages
```
#### 1.4. Настройка деплоя на Sourcecraft (через UI)
В веб-интерфейсе Sourcecraft для репозитория указано:

- Тип публикации: Static website

- Ветка: main

- Папка: site

#### 1.5. Пуш в оба репозитория
```bash
git push -u origin main; git push -u sourcecraft main
```
### 2. Организация локального репозитория
- Одна рабочая копия – исходные файлы MkDocs (```.md```, ```mkdocs.yml```).

- Два удалённых репозитория (проверка ```git remote -v```):

```text
origin      https://github.com/n3s0str3l/lab3.git (fetch)
origin      https://github.com/n3s0str3l/lab3.git (push)
sourcecraft https://git.sourcecraft.dev/my-kos/lab3.git (fetch)
sourcecraft https://git.sourcecraft.dev/my-kos/lab3.git (push)
```
Ветка ```main``` – исходный код.

Ветка ```gh-pages``` – создаётся ```GitHub Actions```, содержит собранный ```HTML```.

Sourcecraft использует ветку ```main``` и папку ```site```.

### 3. Как выполнить деплой повторно
При внесении изменений в сайт:

```bash
git add .
git commit -m "Описание изменений"
git push origin main       # триггерит GitHub Actions → обновляет GitHub Pages
git push sourcecraft main  # Sourcecraft автоматически пересобирает
```
### 4. Настройки на GitHub
Репозиторий ```https://github.com/n3s0str3l/lab3```.

GitHub Pages: Settings -> Pages -> ветка gh-pages (папка /root).

Actions permissions: Settings -> Actions -> General -> Read and write permissions.

Секреты не требуются (используется GITHUB_TOKEN).

### 5. Настройки на Sourcecraft
Создан пустой репозиторий.

В настройках проекта: Static website, ветка main, папка site.

Токен доступа (для ```git push```) создан в профиле (Settings -> Access Tokens).

URL сайта: https://my-kos.sourcecraft.site/lab3.

### 6. Результаты – 4 ссылки
Платформа	Ссылка на статический сайт	Ссылка на репозиторий
Sourcecraft	https://my-kos.sourcecraft.site/lab3	https://git.sourcecraft.dev/my-kos/lab3
GitHub	https://my-kos.github.io	https://github.com/my-kos/lab3
### 7. Дополнительные материалы
Скриншоты настроек GitHub Pages и Sourcecraft прилагаются.

Лог успешного выполнения GitHub Actions (пример):

```text
Run peaceiris/actions-gh-pages@v3
Deploying to gh-pages branch...
Successfully deployed.
```
Токен Sourcecraft: создан в Settings → Access Tokens с правами write.

## Заключение
Единый локальный репозиторий с двумя удалёнными (origin и sourcecraft) позволяет централизованно управлять исходным кодом MkDocs и автоматически публиковать статический сайт одновременно на GitHub Pages и Sourcecraft.