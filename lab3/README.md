# Лабораторна робота №3 — Sequence-діаграми

## Мета
Описати внутрішню взаємодію Wishlist Service у кожному сценарії UC-05 «Забронювати товар».

## Структура
- `spec.md` — UC-05, учасники та кроки сценаріїв SC-03, SC-04 і SC-12.
- `sc-03-reserve-item.md` — sequence-діаграма успішного бронювання (Mermaid `sequenceDiagram`).
- `sc-04-reserve-own-item.md` — sequence-діаграма відмови при бронюванні власного товару (Mermaid `sequenceDiagram`).
- `sc-12-reserve-concurrent.md` — sequence-діаграма одночасного бронювання одного товару (Mermaid `sequenceDiagram`).
- `sc-03-reserve-item.png` — рендер діаграми SC-03.
- `sc-04-reserve-own-item.png` — рендер діаграми SC-04.
- `sc-12-reserve-concurrent.png` — рендер діаграми SC-12.
- `audit.md` — звірка учасників з ER-моделлю і кроків зі сценаріями Gherkin.
- `DEFENSE.md` — звіт для захисту.

## Перевірка
Діаграми описані блоком ` ```mermaid ` з `sequenceDiagram` у відповідному `.md`. Рендер у `.png` будується з цього опису і має збігатися з ним.

## Основа
UC-05 і сценарії SC-03, SC-04, SC-12 взято з лабораторної роботи №2. Учасники відповідають сутностям User, Wishlist, WishlistItem і Reservation з лабораторної роботи №1.
