# Tree /opt (Serbada)

```
/opt
├── serbada-api
│   ├── .env
│   ├── docker-compose.yml
│   ├── dist/
│   ├── package.json
│   ├── prisma/
│   │   ├── migrations/
│   │   │   ├── 20251214134208_init_models/
│   │   │   ├── 20251227063000_add_email_verification/
│   │   │   ├── 20251228162508_account_features/
│   │   │   ├── 20251229071054_add_token_version/
│   │   │   ├── 20251229082933_add_password_reset/
│   │   │   ├── 20251229124829_add_order/
│   │   │   ├── 20251229221101_add_cart_items/
│   │   │   ├── 20251230161130_add_order_address_ref/
│   │   │   ├── 20251231173220_add_order_no_paid_at/
│   │   │   ├── 20251231174557_make_order_no_required/
│   │   │   ├── 20260101121403_product_detail_fields/
│   │   │   ├── 20260102203728_add_master_downline_invite_warning/
│   │   │   ├── 20260103065212_add_product_owner/
│   │   │   ├── 20260104064703_admininvite_email_nullable/
│   │   │   └── 20260104065029_admininvite_email_required/
│   │   ├── schema.prisma
│   │   └── seed.cjs
│   ├── scripts/
│   │   ├── backfill-orderNo.ts
│   │   ├── backfill-product-owner.ts
│   │   └── check-null-orderno.ts
│   └── src/
│       ├── controllers/
│       │   └── adminProduct.controller.ts
│       ├── emails/
│       │   ├── order-created.html
│       │   ├── password-changed.html
│       │   ├── payments-success.html
│       │   ├── reset-password.html
│       │   └── verify-email.html
│       ├── lib/
│       │   ├── crypto.ts
│       │   ├── mailer.ts
│       │   ├── midtrans.ts
│       │   ├── password.ts
│       │   ├── prisma.ts
│       │   ├── tokens.ts
│       │   └── users.ts
│       ├── middleware/
│       │   ├── auth.ts
│       │   ├── authJwt.ts
│       │   ├── requireAdminOrMaster.ts
│       │   ├── requireDirectParentOrMaster.ts
│       │   └── requireMaster.ts
│       ├── routes/
│       │   ├── account.routes.ts
│       │   ├── admin.routes.ts
│       │   ├── auth.routes.ts
│       │   ├── auth.verify.routes.ts
│       │   ├── brand.routes.ts
│       │   ├── cart.routes.ts
│       │   ├── orders.ts
│       │   ├── payments.midtrans.routes.ts
│       │   └── product.routes.ts
│       ├── db.ts
│       └── index.ts
├── serbada-web
│   └── admin/
│       ├── accept-invite.html
│       ├── accept-invite.js
│       ├── admin.js
│       ├── dashboard.html
│       ├── downline.html
│       ├── downline.js
│       ├── login.html
│       ├── master-tree.html
│       ├── master-tree.js
│       ├── product.html
│       ├── product.js
│       └── shared/
│           ├── css/
│           ├── footer.html
│           ├── footer.js
│           └── js/
│               ├── auth.js
│               ├── cart.js
│               └── guards.js
└── n8n/
    └── (kosong)
```
