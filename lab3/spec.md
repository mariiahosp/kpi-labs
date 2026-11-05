# Лабораторна робота №3. Sequence-діаграми UC-05

## 1. Варіант використання

UC-05 «Забронювати товар». Сценарії взято з лабораторної роботи №2: SC-03, SC-04 і SC-12. Вимоги: REQ-05, REQ-06 і NFR-04. SC-03 виконує інший користувач. SC-04 виконує поточний користувач: товар належить його списку. SC-12 виконують двоє інших користувачів одночасно.

Бронювання створює запис `Reservation` з `item_id`, `user_id` і `reserved_at`. Час `reserved_at` виставляє `WishlistService` усередині `createReservation()`. На один `WishlistItem` припадає не більше одного такого запису.

## 2. Учасники

| Учасник             | Звідки                                                        |
| ------------------- | ------------------------------------------------------------- |
| Інший користувач    | актор SC-03, сутність `User`                                  |
| Поточний користувач | актор SC-04, сутність `User`                                  |
| Користувач A        | перший актор SC-12, сутність `User`                           |
| Користувач B        | другий актор SC-12, сутність `User`                           |
| WishlistService     | компонент Wishlist Service, приймає `reserveItem()`           |
| Wishlist            | сутність лабораторної №1: `is_public`, `user_id`              |
| WishlistItem        | сутність лабораторної №1: `wishlist_id`, `is_completed`       |
| Reservation         | сутність лабораторної №1: `item_id`, `user_id`, `reserved_at` |

`WishlistService` активний від `reserveItem(itemId)` і завершує активацію після відповіді користувачу. На SC-03 і SC-04 ця активація охоплює фрагменти `break` і лінійне продовження після них. На SC-12 вона починається до `par` і закінчується після `alt`. Кожен виклик `Wishlist`, `WishlistItem` і `Reservation` має власну активацію від вхідного повідомлення до відповіді.

## 3. SC-03. Успішне бронювання товару

Передумова з Gherkin: товар належить до чужого публічного списку, не завершений і не має активного бронювання.

1. Інший користувач викликає `WishlistService.reserveItem(itemId)`.
2. `WishlistService` викликає `WishlistItem.findById(itemId)` і отримує товар.
3. `WishlistService` викликає `Wishlist.findById(wishlistId)` і отримує список.
4. `WishlistService` викликає `Wishlist.isPublic()` і отримує `true`.
5. `WishlistService` викликає `Wishlist.isOwnedBy(currentUserId)` і отримує `false`.
6. `WishlistService` викликає `WishlistItem.isCompleted()` і отримує `completed`.
7. `WishlistService` викликає `Reservation.findByItemId(itemId)` і отримує `existing`.
8. `break`: охорона `completed = true` або `existing` існує викликає `rejectReservation()` і запис не створює. Цей сценарій охорону не бере: Gherkin фіксує, що товар не завершений і бронювання немає.
9. Далі лінійно `Reservation.createReservation(itemId, currentUserId)`. `WishlistService` повертає іншому користувачу `reservationId`.

Результат Gherkin: система створює бронювання.

## 4. SC-04. Спроба забронювати власний товар

Передумова з Gherkin: товар належить до списку поточного користувача.

1. Поточний користувач викликає `WishlistService.reserveItem(itemId)`.
2. `WishlistService` викликає `WishlistItem.findById(itemId)` і отримує товар.
3. `WishlistService` викликає `Wishlist.findById(wishlistId)` і отримує список.
4. `WishlistService` викликає `Wishlist.isOwnedBy(currentUserId)` і отримує `owned`.
5. Перший `break`: охорона `owned = true` викликає `rejectReservation()`. `createReservation()` після цього фрагмента не виконується. Цей сценарій іде цим `break`. Якщо `owned = false`, далі лінійно `Wishlist.isPublic()`, `WishlistItem.isCompleted()` і `Reservation.findByItemId(itemId)`. Другий `break`: охорона `public = false` або `completed = true` або `existing` існує знову викликає `rejectReservation()`. Якщо ця охорона хибна, лінійно `createReservation(itemId, currentUserId)` і повернення `reservationId`.

Результат Gherkin: система відхиляє запит, бронювання не створюється.

## 5. SC-12. Одночасне бронювання одного товару

Передумова з Gherkin: товар належить до чужого публічного списку, не завершений і не має активного бронювання. Двоє користувачів надсилають запит в один момент, тож обидві перевірки ще бачать порожнє бронювання.

1. Користувач A викликає `WishlistService.reserveItem(itemId)`. Користувач B викликає той самий метод для того самого `itemId`.
2. `par`: для кожного запиту `WishlistService` викликає `WishlistItem.findById(itemId)`, `Wishlist.findById(wishlistId)`, `Wishlist.isPublic()`, `Wishlist.isOwnedBy(...)` і `WishlistItem.isCompleted()`. Обидва отримують публічний чужий незавершений товар.
3. Кожна гілка `par` викликає `Reservation.findByItemId(itemId)` і отримує `empty`.
4. `alt`: гілка «першим фіксується запит A» викликає `Reservation.createReservation(itemId, userA)`, повертає A `reservationId`, а повторний `createReservation(itemId, userB)` отримує `conflict`. Користувач B отримує `rejectReservation()`. Гілка «першим фіксується запит B» робить те саме навпаки. У кожній гілці з'являється один запис. Сценарій не фіксує, хто встигає першим: обидві гілки задовольняють Gherkin.

Результат Gherkin: система створює одне бронювання і відхиляє другий запит. `reserved_at` виставляє `WishlistService` усередині успішного `createReservation()`.
