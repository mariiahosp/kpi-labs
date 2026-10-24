# Лабораторна робота №3. Sequence-діаграми UC-05

## 1. Варіант використання

UC-05 «Забронювати товар». Актор — інший користувач. Сценарії взято з лабораторної роботи №2: SC-03 і SC-04. Вимоги: REQ-05 і REQ-06.

Бронювання створює запис `Reservation` з `item_id`, `user_id` і `reserved_at`. На один `WishlistItem` припадає не більше одного такого запису.

## 2. Учасники

| Учасник          | Звідки                                                        |
| ---------------- | ------------------------------------------------------------- |
| Інший користувач | актор UC-05, сутність `User`                                  |
| WishlistService  | компонент Wishlist Service, приймає `reserveItem()`           |
| Wishlist         | сутність лабораторної №1: `is_public`, `user_id`              |
| WishlistItem     | сутність лабораторної №1: `wishlist_id`, `is_completed`       |
| Reservation      | сутність лабораторної №1: `item_id`, `user_id`, `reserved_at` |

`WishlistService` активний до фрагмента `alt` і завершує активацію після нього.

## 3. SC-03. Успішне бронювання товару

Передумова з Gherkin: товар належить до чужого публічного списку, не завершений і не має активного бронювання.

1. Інший користувач викликає `WishlistService.reserveItem(itemId)`.
2. `WishlistService` викликає `WishlistItem.findById(itemId)` і отримує товар.
3. `WishlistService` викликає `Wishlist.findById(wishlistId)` і отримує список.
4. `WishlistService` викликає `Wishlist.isPublic()` і отримує `true`.
5. `WishlistService` викликає `Wishlist.isOwnedBy(currentUserId)` і отримує `false`.
6. `WishlistService` викликає `WishlistItem.isCompleted()` і отримує `false`.
7. `WishlistService` викликає `Reservation.findByItemId(itemId)` і отримує порожній результат.
8. `alt` «товар доступний»: `WishlistService` викликає `Reservation.createReservation(itemId, currentUserId)` і отримує `reservationId`. Гілка «товар зайнятий або завершений» повертає відмову і запис не створює. Цей сценарій іде гілкою успіху.
9. `WishlistService` повертає іншому користувачу `reservationId`.

Результат Gherkin: система створює бронювання.

## 4. SC-04. Спроба забронювати власний товар

Передумова з Gherkin: товар належить до списку поточного користувача.

1. Інший користувач викликає `WishlistService.reserveItem(itemId)`.
2. `WishlistService` викликає `WishlistItem.findById(itemId)` і отримує товар.
3. `WishlistService` викликає `Wishlist.findById(wishlistId)` і отримує список.
4. `WishlistService` викликає `Wishlist.isOwnedBy(currentUserId)` і отримує `true`.
5. `alt` «товар власний»: `WishlistService` повертає відмову. `Reservation.createReservation()` у цій гілці немає. Гілка «товар чужий» веде до перевірок SC-03. Цей сценарій іде гілкою відмови.

Результат Gherkin: система відхиляє запит, бронювання не створюється.
