# SC-04. Спроба забронювати власний товар

```mermaid
sequenceDiagram
    actor User as Інший користувач
    participant S as WishlistService
    participant W as Wishlist
    participant Item as WishlistItem
    participant R as Reservation

    User->>S: reserveItem(itemId)
    S->>Item: findById(itemId)
    Item-->>S: item
    S->>W: findById(wishlistId)
    W-->>S: wishlist
    S->>W: isOwnedBy(currentUserId)
    W-->>S: true

    alt товар власний
        activate S
        S->>R: createReservation(itemId, currentUserId)
        R-->>S: reservationId
        S->>R: deleteReservation(reservationId)
        R-->>S: deleted
        S-->>User: відмова
        deactivate S
    else товар чужий
        S-->>User: перевірки доступності SC-03
    end
```
