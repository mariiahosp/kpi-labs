# SC-04. Спроба забронювати власний товар

```mermaid
sequenceDiagram
    actor User as Поточний користувач
    participant S as WishlistService
    participant W as Wishlist
    participant Item as WishlistItem
    participant R as Reservation

    User->>S: reserveItem(itemId)
    activate S
    S->>Item: findById(itemId)
    Item-->>S: item
    S->>W: findById(wishlistId)
    W-->>S: wishlist
    S->>W: isOwnedBy(currentUserId)
    W-->>S: owned

    alt owned = true
        S-->>User: rejectReservation()
    else owned = false
        S->>W: isPublic()
        W-->>S: public
        S->>Item: isCompleted()
        Item-->>S: completed
        S->>R: findByItemId(itemId)
        R-->>S: existing
        alt public = true і completed = false і existing порожній
            S->>R: createReservation(itemId, currentUserId)
            R-->>S: reservationId
            S-->>User: reservationId
        else список приватний, товар завершений або вже заброньований
            S-->>User: rejectReservation()
        end
    end
    deactivate S
```
