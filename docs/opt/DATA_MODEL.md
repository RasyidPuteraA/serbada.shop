# Model Data (Ringkas)

## Sumber Indikator
- `prisma/schema.prisma`.

## Entitas Utama & Field Penting (tanpa PII)

### User
- `id` (PK)
- `phone`, `email`, `name` (data identitas; tidak ditampilkan nilainya)
- `role` (USER, SUPERUSER, MASTER, ADMIN legacy)
- `parentAdminId` (tree/downline)
- `deletedAt`, `createdAt`, `updatedAt`
- `tokenVersion`, `emailVerifiedAt`

### BrandProfile
- `id` (PK)
- `ownerUserId` (FK -> User)
- `brandName`, `slug`, `description`, `logoUrl`, `phone`, `address`, `instagram`

### Product
- `id` (PK)
- `ownerUserId` (FK -> User)
- `name`, `slug`, `description`
- `price`, `stock`, `isActive`
- `category`, `brand`, `imageUrl`, `images[]`

### Order
- `id` (PK)
- `userId` (FK -> User)
- `orderNo` (unique)
- `status` (PENDING/PAID/PROCESSING/SHIPPED/DONE/CANCELLED)
- `subtotal`, `shippingFee`, `total`
- `paidAt`
- `shippingName`, `shippingPhone`, `shippingAddress`, `shippingNote`
- `addressId` (FK -> Address)

### OrderItem
- `id` (PK)
- `orderId` (FK -> Order)
- `productId` (FK -> Product)
- `productName`, `price`, `qty`, `subtotal`

### CartItem
- `id` (PK)
- `userId` (FK -> User)
- `productId` (FK -> Product)
- `qty`, `createdAt`, `updatedAt`

### Address
- `id` (PK)
- `userId` (FK -> User)
- `label`, `receiverName`, `phone`, `addressLine1`, `addressLine2`, `city`, `postalCode`, `notes`, `isDefault`

### NotificationPreference
- `id` (PK)
- `userId` (FK -> User)
- `emailOrderUpdates`, `emailPromos`

### EmailVerificationToken
- `id` (PK)
- `userId` (FK -> User)
- `tokenHash`, `expiresAt`, `usedAt`, `createdAt`

### PasswordResetToken
- `id` (PK)
- `userId` (FK -> User)
- `tokenHash`, `expiresAt`, `usedAt`, `createdAt`

### AdminInvite
- `id` (PK)
- `inviterId` (FK -> User)
- `tokenHash`, `email`, `expiresAt`, `usedAt`, `createdAt`

### AdminWarning
- `id` (PK)
- `targetAdminId` (FK -> User)
- `createdByAdminId` (FK -> User)
- `severity`, `message`, `createdAt`

## Relasi Inti (ERD teks ringkas)
```text
User 1 --- 0..1 BrandProfile
User 1 --- 0..n Product (owner)
User 1 --- 0..n Order
Order 1 --- 1..n OrderItem
Product 1 --- 0..n OrderItem
User 1 --- 0..n CartItem
Product 1 --- 0..n CartItem
User 1 --- 0..n Address
Address 1 --- 0..n Order (optional)
User 1 --- 0..n EmailVerificationToken
User 1 --- 0..n PasswordResetToken
User 1 --- 0..n AdminInvite (inviter)
User 1 --- 0..n AdminWarning (createdBy)
User 1 --- 0..n AdminWarning (target)
User (parentAdminId) --- User (childrenAdmins)
```

## Catatan Pembayaran
- Tidak ada model `Payment` eksplisit di schema.
- Status pembayaran direpresentasikan melalui `Order.status` + `paidAt`.
- Integrasi pembayaran dipetakan lewat webhook Midtrans (detail di `docs/opt/README_OPT.md`).
