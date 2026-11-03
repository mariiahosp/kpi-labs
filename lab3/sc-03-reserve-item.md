# SC-03. Успішне бронювання товару

```mermaid
sequenceDiagram
    actor User as Інший користувач
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
    S->>W: isPublic()
    W-->>S: true
    S->>W: isOwnedBy(currentUserId)
    W-->>S: false
    S->>Item: isCompleted()
    Item-->>S: completed
    S->>R: findByItemId(itemId)
    R-->>S: existing

    alt completed = false і existing порожній
        S->>R: createReservation(itemId, currentUserId)
        R-->>S: reservationId
        S-->>User: reservationId
    else completed = true або existing існує
        S-->>User: rejectReservation()
    end
    deactivate S
```
