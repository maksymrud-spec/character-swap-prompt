# Як налаштувати репо — покрокова інструкція

## 1. Створи репо на GitHub

1. Зайди на https://github.com/new
2. Назва: `character-swap-prompt`
3. Visibility: **Private**
4. НЕ ініціалізуй README (ми завантажимо файли самі)
5. Натисни "Create repository"

## 2. Завантаж файли з цього архіву

```bash
cd character-swap-prompt-skill/

git init
git add .
git commit -m "init: first versioned release of character-swap-prompt skill"
git branch -M main
git remote add origin https://github.com/YOUR_GITHUB_USERNAME/character-swap-prompt.git
git push -u origin main
```

## 3. Перевір raw URL

Після push відкрий у браузері:
```
https://raw.githubusercontent.com/YOUR_GITHUB_USERNAME/character-swap-prompt/main/SKILL.md
```
Має показати текст скілу — якщо так, все готово.

## 4. Налаштуй Claude Project

1. Відкрий Claude → Projects → обери або створи проєкт
2. Натисни "Edit project instructions"
3. Встав вміст файлу `PROJECT_SYSTEM_PROMPT.txt` (замінивши YOUR_GITHUB_USERNAME)
4. Збережи

## 5. Як оновлювати скіл в майбутньому

```bash
# Відредагуй SKILL.md
# Потім:
git add SKILL.md
git commit -m "fix: опиши що змінив"
git push
```

Claude підхопить нову версію автоматично на наступній сесії.

## Структура репо

```
character-swap-prompt/
├── SKILL.md                  ← сам скіл (Claude читає звідси)
├── README.md                 ← опис репо
├── CHANGELOG.md              ← історія змін
├── .gitignore
└── PROJECT_SYSTEM_PROMPT.txt ← промпт для Claude Projects
```
