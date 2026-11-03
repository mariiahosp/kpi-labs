# Аудит sequence-діаграм UC-05

## 1. Учасники й ER-модель

Порівняно лінії життя на діаграмах SC-03 і SC-04 із сутностями лабораторної №1.

| Учасник діаграми | Сутність або компонент |
|---|---|
| Інший користувач | `User`, актор SC-03 |
| Поточний користувач | `User`, актор SC-04 |
| Wishlist | `Wishlist` (`is_public`, `user_id`) |
| WishlistItem | `WishlistItem` (`wishlist_id`, `is_completed`) |
| Reservation | `Reservation` (`item_id`, `user_id`, `reserved_at`) |
| WishlistService | компонент системи, який приймає `reserveItem()` |

`WishlistService` не додає таблиці до моделі. `reserved_at` виставляє `WishlistService` усередині `createReservation(itemId, currentUserId)`. На SC-03 цей виклик стоїть лише в гілці `completed = false` і `existing` порожній. На SC-04 лінія `Reservation` є, бо гілка чужого товару може створити запис. Гілка `owned = true` викликає `rejectReservation()` і `createReservation()` не викликає.

## 2. Кроки й сценарії Gherkin

SC-03. Передумова «чужий публічний список» дає `isPublic() = true` і `isOwnedBy(currentUserId) = false`. Передумова «не завершений і без активного бронювання» — це гілка `completed = false` і `existing` порожній. Крок «користувач бронює товар» — це `reserveItem(itemId)`. Результат «система створює бронювання» — це `createReservation(itemId, currentUserId)` і повернення `reservationId`.

SC-04. Передумова «товар належить до списку поточного користувача» — це гілка `owned = true`. Результат «система відхиляє запит, бронювання не створюється» — це `rejectReservation()` без `createReservation()`.

Гілки `else` описують відмову для завершеного, уже заброньованого або приватного товару. Окремих сценаріїв Gherkin для них немає.

## 3. Висновок

Учасники діаграм відповідають `User`, `Wishlist`, `WishlistItem` і `Reservation`. Кроки SC-03 і SC-04 збігаються зі сценаріями Gherkin. PNG треба зібрати заново з оновлених `.md`.
