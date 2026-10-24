# SC-04. Спроба забронювати власний товар

```mermaid
sequenceDiagram
    actor User as Інший користувач
    participant S as WishlistService
    participant W as Wishlist
    participant Item as WishlistItem

    User->>S: reserveItem(itemId)
    activate S
    S->>Item: findById(itemId)
    Item-->>S: item
    S->>W: findById(wishlistId)
    W-->>S: wishlist
    S->>W: isOwnedBy(currentUserId)
    W-->>S: true

    alt товар власний
        S-->>User: відмова
    else товар чужий
        S-->>User: перевірки доступності SC-03
    end
    deactivate S
```
