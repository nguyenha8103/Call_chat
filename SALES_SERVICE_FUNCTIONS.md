# SALES SERVICE - FUNCTIONS SPECIFICATION

**Service Name:** Sales Service  
**Total Modules:** 8  
**Total Functions:** 56  
**Database:** PostgreSQL 15+  
**Cache:** Redis 7+  
**Message Queue:** RabbitMQ (for order processing)

---

## 📋 TABLE OF CONTENTS

1. [Service Overview](#service-overview)
2. [MODULE 1: Product Management (7 functions)](#module-1-product-management)
3. [MODULE 2: Inventory Management (8 functions)](#module-2-inventory-management)
4. [MODULE 3: Order Management (9 functions)](#module-3-order-management)
5. [MODULE 4: Sales Management (6 functions)](#module-4-sales-management)
6. [MODULE 5: Quote Management (7 functions)](#module-5-quote-management)
7. [MODULE 6: Contract Management (7 functions)](#module-6-contract-management)
8. [MODULE 7: Pricing & Discount (6 functions)](#module-7-pricing--discount)
9. [MODULE 8: Invoice & Payment (6 functions)](#module-8-invoice--payment)
10. [Completion Checklist](#completion-checklist)

---

## SERVICE OVERVIEW

### Purpose
Sales Service quản lý toàn bộ quy trình bán hàng từ sản phẩm, kho hàng, báo giá, đơn hàng, hợp đồng đến hóa đơn và thanh toán.

### Key Features
- ✅ Quản lý sản phẩm & dịch vụ với variants, bundles
- ✅ Quản lý tồn kho theo nhiều kho, tracking batch/serial number
- ✅ Quy trình đơn hàng: Draft → Confirmed → Processing → Shipped → Delivered → Completed
- ✅ Báo giá tự động với approval workflow
- ✅ Quản lý hợp đồng với e-signature integration
- ✅ Pricing rules & discount campaigns
- ✅ Hóa đơn tự động & payment tracking

### Integration Points
- **IAM Service:** Workspace, User, Permissions
- **CRM Service:** Customers, Deals, Contacts
- **Call Service:** Call notes → Order notes
- **Chat Service:** Chat orders
- **Marketing Service:** Campaigns → Discount codes
- **Analytics Service:** Sales reports, Revenue analytics
- **Notification Service:** Order status updates, Payment reminders

---

## MODULE 1: PRODUCT MANAGEMENT

**Mô tả:** Quản lý danh mục sản phẩm, dịch vụ, variants, bundles, categories

---

### 1.1. Create Product

**Endpoint:** `POST /api/products`

**Description:** Tạo sản phẩm hoặc dịch vụ mới.

**Request Body:**
```json
{
  "workspaceId": "uuid",
  "sku": "PROD-001",
  "name": "MacBook Pro 16 inch M3 Max",
  "description": "Apple MacBook Pro 16 inch with M3 Max chip",
  "type": "product",  // "product" | "service" | "bundle"
  "categoryId": "uuid",
  "basePrice": 89990000,
  "costPrice": 75000000,
  "currency": "VND",
  "unit": "chiếc",
  "weight": 2.15,
  "weightUnit": "kg",
  "dimensions": {
    "length": 35.57,
    "width": 24.81,
    "height": 1.68,
    "unit": "cm"
  },
  "images": [
    "https://cdn.nextx.vn/products/macbook-pro-m3-max-1.jpg",
    "https://cdn.nextx.vn/products/macbook-pro-m3-max-2.jpg"
  ],
  "hasVariants": true,
  "variants": [
    {
      "sku": "PROD-001-SILVER-512GB",
      "name": "Silver - 512GB SSD",
      "attributes": {
        "color": "Silver",
        "storage": "512GB"
      },
      "price": 89990000,
      "costPrice": 75000000,
      "barcode": "194253408376"
    },
    {
      "sku": "PROD-001-SPACE-1TB",
      "name": "Space Black - 1TB SSD",
      "attributes": {
        "color": "Space Black",
        "storage": "1TB"
      },
      "price": 99990000,
      "costPrice": 82000000,
      "barcode": "194253408383"
    }
  ],
  "trackInventory": true,
  "lowStockThreshold": 5,
  "taxable": true,
  "taxRate": 10,
  "isActive": true,
  "tags": ["laptop", "apple", "macbook", "premium"]
}
```

**Response:** `201 Created`
```json
{
  "success": true,
  "data": {
    "id": "prod-uuid",
    "sku": "PROD-001",
    "name": "MacBook Pro 16 inch M3 Max",
    "type": "product",
    "basePrice": 89990000,
    "hasVariants": true,
    "variantCount": 2,
    "createdAt": "2026-02-04T10:00:00Z"
  }
}
```

**Business Rules:**
- SKU phải unique trong workspace
- Nếu `hasVariants = true`, phải có ít nhất 1 variant
- Variant SKU phải unique
- `costPrice <= basePrice` (warning if violated)
- `lowStockThreshold >= 0`

**Permissions:** `sales.products.create`

---

### 1.2. Update Product

**Endpoint:** `PUT /api/products/{productId}`

**Description:** Cập nhật thông tin sản phẩm.

**Request Body:** (Same as Create, all fields optional)

**Response:** `200 OK`

**Business Rules:**
- Không được sửa SKU nếu sản phẩm đã có transactions
- Không được disable product nếu còn tồn kho > 0

**Permissions:** `sales.products.update`

---

### 1.3. Delete Product

**Endpoint:** `DELETE /api/products/{productId}`

**Description:** Xóa sản phẩm (soft delete).

**Response:** `200 OK`

**Business Rules:**
- Chỉ xóa được nếu chưa có bất kỳ order nào
- Nếu có tồn kho > 0, warning user

**Permissions:** `sales.products.delete`

---

### 1.4. Get Product Details

**Endpoint:** `GET /api/products/{productId}`

**Description:** Lấy chi tiết sản phẩm.

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "id": "prod-uuid",
    "sku": "PROD-001",
    "name": "MacBook Pro 16 inch M3 Max",
    "description": "...",
    "type": "product",
    "category": {
      "id": "cat-uuid",
      "name": "Laptops"
    },
    "basePrice": 89990000,
    "costPrice": 75000000,
    "currency": "VND",
    "variants": [...],
    "inventory": {
      "totalStock": 25,
      "availableStock": 20,
      "reservedStock": 5,
      "warehouses": [
        {
          "warehouseId": "wh-001",
          "warehouseName": "Kho Hà Nội",
          "stock": 15
        },
        {
          "warehouseId": "wh-002",
          "warehouseName": "Kho TP.HCM",
          "stock": 10
        }
      ]
    },
    "salesStats": {
      "totalSold": 150,
      "revenue": 13498500000,
      "averagePrice": 89990000
    },
    "createdAt": "2026-01-01T00:00:00Z",
    "updatedAt": "2026-02-04T10:00:00Z"
  }
}
```

**Permissions:** `sales.products.view`

---

### 1.5. List Products

**Endpoint:** `GET /api/products?page=1&pageSize=20&categoryId=uuid&search=macbook&status=active`

**Description:** Lấy danh sách sản phẩm với filter & pagination.

**Query Parameters:**
- `page` (int, default: 1)
- `pageSize` (int, default: 20)
- `search` (string): Search by name, SKU, barcode
- `categoryId` (uuid): Filter by category
- `type` (enum): product | service | bundle
- `status` (enum): active | inactive | out_of_stock
- `tags` (array): Filter by tags
- `sortBy` (string): name | sku | price | stock | created_at
- `sortOrder` (string): asc | desc

**Response:** `200 OK` (Paginated list)

**Permissions:** `sales.products.view`

---

### 1.6. Manage Product Categories

**Endpoint:** `POST /api/products/categories`

**Description:** Tạo category cho sản phẩm.

**Request Body:**
```json
{
  "workspaceId": "uuid",
  "name": "Laptops",
  "parentCategoryId": null,
  "description": "Laptop computers",
  "imageUrl": "https://cdn.nextx.vn/categories/laptops.jpg",
  "displayOrder": 1,
  "isActive": true
}
```

**Response:** `201 Created`

**Business Rules:**
- Category name unique trong cùng parent
- Support nested categories (max 3 levels)

**Related Endpoints:**
- `PUT /api/products/categories/{id}` - Update category
- `DELETE /api/products/categories/{id}` - Delete (chỉ khi không có products)
- `GET /api/products/categories` - List categories (tree structure)

**Permissions:** `sales.products.manage_categories`

---

### 1.7. Bulk Import Products

**Endpoint:** `POST /api/products/import`

**Description:** Import hàng loạt sản phẩm từ Excel/CSV.

**Request:** `multipart/form-data`
- `file`: Excel/CSV file
- `workspaceId`: uuid
- `updateExisting`: boolean (update if SKU exists)

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "totalRows": 100,
    "successCount": 95,
    "errorCount": 5,
    "errors": [
      {
        "row": 12,
        "sku": "PROD-012",
        "error": "Duplicate SKU"
      }
    ]
  }
}
```

**Business Rules:**
- Validate all rows before import
- Support rollback if any critical errors
- Maximum 1000 products per import

**Permissions:** `sales.products.import`

---

## MODULE 2: INVENTORY MANAGEMENT

**Mô tả:** Quản lý tồn kho, kho hàng, nhập/xuất kho, kiểm kê, tracking batch/serial

---

### 2.1. Create Warehouse

**Endpoint:** `POST /api/warehouses`

**Description:** Tạo kho hàng mới.

**Request Body:**
```json
{
  "workspaceId": "uuid",
  "code": "WH-HN-01",
  "name": "Kho Hà Nội",
  "type": "main",  // "main" | "retail" | "transit"
  "address": {
    "street": "123 Đường ABC",
    "ward": "Phường XYZ",
    "district": "Quận 1",
    "city": "Hà Nội",
    "country": "Vietnam",
    "postalCode": "100000",
    "latitude": 21.028511,
    "longitude": 105.804817
  },
  "contactPerson": "Nguyễn Văn A",
  "phone": "+84 123 456 789",
  "email": "warehouse.hn@nextx.vn",
  "capacity": 5000,
  "capacityUnit": "m3",
  "isActive": true
}
```

**Response:** `201 Created`

**Permissions:** `sales.warehouses.create`

---

### 2.2. Stock Adjustment (Nhập/Xuất kho)

**Endpoint:** `POST /api/inventory/adjustments`

**Description:** Điều chỉnh tồn kho (nhập kho, xuất kho, kiểm kê).

**Request Body:**
```json
{
  "workspaceId": "uuid",
  "warehouseId": "uuid",
  "type": "in",  // "in" | "out" | "transfer" | "adjustment"
  "referenceType": "purchase_order",  // "purchase_order" | "sales_order" | "transfer" | "manual"
  "referenceId": "uuid",
  "notes": "Nhập hàng từ nhà cung cấp ABC",
  "items": [
    {
      "productId": "uuid",
      "variantId": "uuid",
      "quantity": 50,
      "unit": "chiếc",
      "batchNumber": "BATCH-2026-001",
      "serialNumbers": ["SN001", "SN002", "..."],
      "expiryDate": "2027-12-31",
      "location": "A-01-05",  // Vị trí trong kho
      "costPrice": 75000000
    }
  ],
  "performedBy": "user-uuid",
  "performedAt": "2026-02-04T10:00:00Z"
}
```

**Response:** `201 Created`
```json
{
  "success": true,
  "data": {
    "id": "adj-uuid",
    "adjustmentNumber": "ADJ-2026-0001",
    "type": "in",
    "totalItems": 1,
    "totalQuantity": 50,
    "status": "completed",
    "createdAt": "2026-02-04T10:00:00Z"
  }
}
```

**Business Rules:**
- Type "out" phải kiểm tra available stock
- Serial numbers phải unique
- Batch number tracking cho các sản phẩm có expiry date
- Auto update product inventory levels

**Permissions:** `sales.inventory.adjust`

---

### 2.3. Stock Transfer (Chuyển kho)

**Endpoint:** `POST /api/inventory/transfers`

**Description:** Chuyển hàng giữa các kho.

**Request Body:**
```json
{
  "workspaceId": "uuid",
  "fromWarehouseId": "uuid",
  "toWarehouseId": "uuid",
  "transferDate": "2026-02-04",
  "expectedDeliveryDate": "2026-02-05",
  "items": [
    {
      "productId": "uuid",
      "variantId": "uuid",
      "quantity": 20,
      "batchNumber": "BATCH-2026-001"
    }
  ],
  "notes": "Chuyển hàng từ Hà Nội xuống TP.HCM",
  "shippingMethod": "truck",
  "trackingNumber": "TRACK-123456"
}
```

**Response:** `201 Created`

**Transfer Status Flow:**
```
draft → pending → in_transit → delivered → completed
                      ↓
                  cancelled
```

**Business Rules:**
- Deduct stock from source warehouse immediately
- Add stock to destination warehouse when status = "delivered"
- Cannot cancel if status = "delivered" or "completed"

**Permissions:** `sales.inventory.transfer`

---

### 2.4. Get Stock Level

**Endpoint:** `GET /api/inventory/stock?productId=uuid&warehouseId=uuid`

**Description:** Lấy thông tin tồn kho của sản phẩm.

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "productId": "uuid",
    "productName": "MacBook Pro 16 inch M3 Max",
    "sku": "PROD-001",
    "totalStock": 125,
    "availableStock": 100,
    "reservedStock": 20,
    "inTransitStock": 5,
    "warehouses": [
      {
        "warehouseId": "wh-001",
        "warehouseName": "Kho Hà Nội",
        "stock": 75,
        "available": 60,
        "reserved": 15,
        "batches": [
          {
            "batchNumber": "BATCH-2026-001",
            "quantity": 50,
            "expiryDate": "2027-12-31",
            "location": "A-01-05"
          }
        ]
      },
      {
        "warehouseId": "wh-002",
        "warehouseName": "Kho TP.HCM",
        "stock": 50,
        "available": 40,
        "reserved": 5
      }
    ],
    "lastUpdated": "2026-02-04T10:00:00Z"
  }
}
```

**Permissions:** `sales.inventory.view`

---

### 2.5. Stock Reservation (Giữ hàng)

**Endpoint:** `POST /api/inventory/reservations`

**Description:** Giữ hàng cho đơn hàng.

**Request Body:**
```json
{
  "workspaceId": "uuid",
  "orderId": "uuid",
  "warehouseId": "uuid",
  "items": [
    {
      "productId": "uuid",
      "variantId": "uuid",
      "quantity": 2,
      "preferredBatch": "BATCH-2026-001"
    }
  ],
  "expiresAt": "2026-02-05T10:00:00Z"  // Auto release after this
}
```

**Response:** `201 Created`

**Business Rules:**
- Check available stock before reservation
- Auto release reservation after expiry
- Release reservation when order cancelled
- Convert reservation to actual deduction when order confirmed

**Permissions:** `sales.inventory.reserve`

---

### 2.6. Stock Count (Kiểm kê)

**Endpoint:** `POST /api/inventory/stock-counts`

**Description:** Tạo phiên kiểm kê tồn kho.

**Request Body:**
```json
{
  "workspaceId": "uuid",
  "warehouseId": "uuid",
  "countDate": "2026-02-04",
  "countType": "full",  // "full" | "partial" | "cycle"
  "productIds": ["uuid"],  // null for full count
  "assignedTo": ["user-uuid-1", "user-uuid-2"],
  "notes": "Kiểm kê định kỳ tháng 2"
}
```

**Count Status Flow:**
```
draft → in_progress → completed → approved
            ↓
        cancelled
```

**Response:** `201 Created`

**Related Endpoints:**
- `PUT /api/inventory/stock-counts/{id}/items` - Update counted quantities
- `POST /api/inventory/stock-counts/{id}/approve` - Approve and apply adjustments
- `GET /api/inventory/stock-counts/{id}/variances` - View discrepancies

**Permissions:** `sales.inventory.stock_count`

---

### 2.7. Low Stock Alerts

**Endpoint:** `GET /api/inventory/low-stock-alerts`

**Description:** Lấy danh sách sản phẩm sắp hết hàng.

**Response:** `200 OK`
```json
{
  "success": true,
  "data": [
    {
      "productId": "uuid",
      "productName": "MacBook Pro 16 inch M3 Max",
      "sku": "PROD-001",
      "currentStock": 3,
      "lowStockThreshold": 5,
      "status": "critical",  // "warning" | "critical"
      "recommendedOrderQuantity": 20,
      "warehouses": [
        {
          "warehouseId": "wh-001",
          "stock": 3,
          "threshold": 5
        }
      ]
    }
  ]
}
```

**Permissions:** `sales.inventory.view`

---

### 2.8. Inventory Valuation Report

**Endpoint:** `GET /api/inventory/valuation?warehouseId=uuid&date=2026-02-04&method=fifo`

**Description:** Báo cáo giá trị tồn kho.

**Query Parameters:**
- `warehouseId` (uuid, optional): Specific warehouse
- `date` (date): Valuation date
- `method` (enum): fifo | lifo | average

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "valuationDate": "2026-02-04",
    "method": "fifo",
    "totalValue": 15000000000,
    "products": [
      {
        "productId": "uuid",
        "productName": "MacBook Pro 16 inch M3 Max",
        "quantity": 125,
        "averageCost": 75000000,
        "totalValue": 9375000000
      }
    ]
  }
}
```

**Permissions:** `sales.inventory.valuation`

---

## MODULE 3: ORDER MANAGEMENT

**Mô tả:** Quản lý đơn hàng bán, quy trình xử lý từ tạo đến giao hàng

---

### 3.1. Create Sales Order

**Endpoint:** `POST /api/orders`

**Description:** Tạo đơn hàng bán.

**Request Body:**
```json
{
  "workspaceId": "uuid",
  "orderType": "sales",  // "sales" | "purchase" | "return"
  "customerId": "uuid",
  "contactId": "uuid",
  "dealId": "uuid",  // Link to CRM deal
  "orderDate": "2026-02-04",
  "expectedDeliveryDate": "2026-02-10",
  "warehouseId": "uuid",
  "items": [
    {
      "productId": "uuid",
      "variantId": "uuid",
      "quantity": 2,
      "unitPrice": 89990000,
      "discount": {
        "type": "percentage",  // "percentage" | "fixed"
        "value": 10,
        "amount": 17998000
      },
      "taxRate": 10,
      "notes": "Tặng kèm túi đựng laptop"
    }
  ],
  "shippingAddress": {
    "recipientName": "Nguyễn Văn B",
    "phone": "+84 987 654 321",
    "street": "456 Đường DEF",
    "ward": "Phường ABC",
    "district": "Quận 3",
    "city": "TP. Hồ Chí Minh",
    "country": "Vietnam"
  },
  "billingAddress": {...},  // Same structure, optional (default = shipping)
  "shippingMethod": "standard",  // "standard" | "express" | "pickup"
  "shippingCost": 50000,
  "paymentMethod": "bank_transfer",  // "cash" | "bank_transfer" | "card" | "cod"
  "paymentTerms": "net_30",  // "immediate" | "net_7" | "net_15" | "net_30"
  "discountCode": "SUMMER2026",
  "notes": "Khách hàng VIP, giao hàng cẩn thận",
  "internalNotes": "Đã confirm với khách qua điện thoại",
  "tags": ["vip", "corporate"],
  "assignedTo": "user-uuid"  // Sales rep
}
```

**Response:** `201 Created`
```json
{
  "success": true,
  "data": {
    "id": "order-uuid",
    "orderNumber": "SO-2026-0001",
    "orderType": "sales",
    "status": "draft",
    "customer": {
      "id": "cust-uuid",
      "name": "Công ty ABC"
    },
    "totalAmount": 161982000,
    "subtotal": 179980000,
    "discount": 17998000,
    "tax": 16199800,
    "shippingCost": 50000,
    "grandTotal": 162032000,
    "createdAt": "2026-02-04T10:00:00Z"
  }
}
```

**Order Status Flow:**
```
draft → confirmed → processing → shipped → delivered → completed
  ↓                      ↓           ↓
cancelled           on_hold      returned
```

**Business Rules:**
- Auto calculate totals (subtotal, discount, tax, shipping, grand total)
- Check stock availability before confirm
- Reserve stock when status = "confirmed"
- Deduct stock when status = "shipped"
- Apply discount code validation
- Cannot edit order after status = "processing"

**Permissions:** `sales.orders.create`

---

### 3.2. Update Order Status

**Endpoint:** `PUT /api/orders/{orderId}/status`

**Description:** Cập nhật trạng thái đơn hàng.

**Request Body:**
```json
{
  "status": "confirmed",
  "notes": "Đã xác nhận với khách hàng",
  "notifyCustomer": true,
  "notifyAssignee": true
}
```

**Response:** `200 OK`

**Status Transitions:**
- `draft` → `confirmed`, `cancelled`
- `confirmed` → `processing`, `on_hold`, `cancelled`
- `processing` → `shipped`, `on_hold`
- `shipped` → `delivered`, `returned`
- `delivered` → `completed`, `returned`

**Business Rules:**
- Cannot skip status steps
- Send email/SMS to customer when status changes
- Log status change history with timestamp and user

**Permissions:** `sales.orders.update_status`

---

### 3.3. Get Order Details

**Endpoint:** `GET /api/orders/{orderId}`

**Description:** Lấy chi tiết đơn hàng.

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "id": "order-uuid",
    "orderNumber": "SO-2026-0001",
    "orderType": "sales",
    "status": "shipped",
    "customer": {
      "id": "cust-uuid",
      "name": "Công ty ABC",
      "email": "contact@abc.com",
      "phone": "+84 123 456 789"
    },
    "items": [
      {
        "id": "item-uuid",
        "product": {
          "id": "prod-uuid",
          "name": "MacBook Pro 16 inch M3 Max",
          "sku": "PROD-001"
        },
        "variant": {
          "id": "var-uuid",
          "name": "Silver - 512GB",
          "sku": "PROD-001-SILVER-512GB"
        },
        "quantity": 2,
        "unitPrice": 89990000,
        "discount": 17998000,
        "tax": 14399200,
        "subtotal": 179980000,
        "total": 176381200
      }
    ],
    "subtotal": 179980000,
    "discount": 17998000,
    "tax": 16199800,
    "shippingCost": 50000,
    "grandTotal": 162032000,
    "shippingAddress": {...},
    "billingAddress": {...},
    "shippingMethod": "standard",
    "trackingNumber": "TRACK-123456",
    "paymentStatus": "partial",  // "unpaid" | "partial" | "paid" | "refunded"
    "paidAmount": 80000000,
    "remainingAmount": 82032000,
    "payments": [
      {
        "id": "pay-uuid",
        "amount": 80000000,
        "method": "bank_transfer",
        "paidAt": "2026-02-04T14:00:00Z",
        "reference": "REF-123456"
      }
    ],
    "statusHistory": [
      {
        "status": "draft",
        "changedAt": "2026-02-04T10:00:00Z",
        "changedBy": "user-uuid"
      },
      {
        "status": "confirmed",
        "changedAt": "2026-02-04T11:00:00Z",
        "changedBy": "user-uuid"
      },
      {
        "status": "shipped",
        "changedAt": "2026-02-05T09:00:00Z",
        "changedBy": "user-uuid"
      }
    ],
    "assignedTo": {
      "id": "user-uuid",
      "name": "Nguyễn Văn A"
    },
    "createdAt": "2026-02-04T10:00:00Z",
    "updatedAt": "2026-02-05T09:00:00Z"
  }
}
```

**Permissions:** `sales.orders.view`

---

### 3.4. List Orders

**Endpoint:** `GET /api/orders?page=1&status=confirmed&customerId=uuid&fromDate=2026-02-01&toDate=2026-02-28`

**Description:** Lấy danh sách đơn hàng với filters.

**Query Parameters:**
- `page`, `pageSize`
- `status` (enum): draft | confirmed | processing | shipped | delivered | completed | cancelled
- `customerId` (uuid)
- `assignedTo` (uuid)
- `warehouseId` (uuid)
- `fromDate`, `toDate`
- `paymentStatus` (enum): unpaid | partial | paid
- `search` (string): Order number, customer name
- `sortBy`: order_date | total | status
- `sortOrder`: asc | desc

**Response:** `200 OK` (Paginated)

**Permissions:** `sales.orders.view`

---

### 3.5. Cancel Order

**Endpoint:** `POST /api/orders/{orderId}/cancel`

**Description:** Hủy đơn hàng.

**Request Body:**
```json
{
  "reason": "Khách hàng không còn nhu cầu",
  "notifyCustomer": true,
  "restockItems": true
}
```

**Response:** `200 OK`

**Business Rules:**
- Can only cancel if status in ["draft", "confirmed", "processing"]
- Release stock reservation
- Restock items if `restockItems = true`
- Cannot cancel if already shipped

**Permissions:** `sales.orders.cancel`

---

### 3.6. Add Order Note/Comment

**Endpoint:** `POST /api/orders/{orderId}/notes`

**Description:** Thêm ghi chú cho đơn hàng.

**Request Body:**
```json
{
  "content": "Khách yêu cầu giao trước 10h sáng",
  "isInternal": false,  // true = internal note, false = visible to customer
  "attachments": [
    "https://cdn.nextx.vn/attachments/note-1.jpg"
  ]
}
```

**Response:** `201 Created`

**Permissions:** `sales.orders.add_note`

---

### 3.7. Create Return Order

**Endpoint:** `POST /api/orders/{orderId}/returns`

**Description:** Tạo đơn hàng trả lại.

**Request Body:**
```json
{
  "returnDate": "2026-02-10",
  "reason": "Sản phẩm bị lỗi",
  "items": [
    {
      "orderItemId": "uuid",
      "productId": "uuid",
      "variantId": "uuid",
      "quantity": 1,
      "returnReason": "Màn hình bị chết điểm",
      "condition": "damaged"  // "damaged" | "defective" | "wrong_item" | "unwanted"
    }
  ],
  "refundMethod": "original_payment",  // "original_payment" | "store_credit" | "exchange"
  "restockItems": true,
  "notes": "Khách hàng mang hàng trực tiếp đến cửa hàng"
}
```

**Response:** `201 Created`

**Return Status Flow:**
```
pending → approved → received → refunded → completed
   ↓
rejected
```

**Business Rules:**
- Can only return within 7 days (configurable)
- Check return policy
- Restock items if `restockItems = true` and `condition` allows
- Create credit note if refund via store credit

**Permissions:** `sales.orders.create_return`

---

### 3.8. Order Fulfillment (Picking & Packing)

**Endpoint:** `POST /api/orders/{orderId}/fulfillment`

**Description:** Xử lý đóng gói và chuẩn bị giao hàng.

**Request Body:**
```json
{
  "warehouseId": "uuid",
  "items": [
    {
      "orderItemId": "uuid",
      "quantity": 2,
      "pickedFrom": {
        "location": "A-01-05",
        "batchNumber": "BATCH-2026-001",
        "serialNumbers": ["SN001", "SN002"]
      }
    }
  ],
  "packedBy": "user-uuid",
  "packageType": "box",  // "box" | "envelope" | "pallet"
  "packageDimensions": {
    "length": 40,
    "width": 30,
    "height": 10,
    "unit": "cm"
  },
  "weight": 5.2,
  "weightUnit": "kg",
  "packagePhotos": [
    "https://cdn.nextx.vn/packages/pkg-001-1.jpg"
  ],
  "notes": "Đóng gói cẩn thận, hàng dễ vỡ"
}
```

**Response:** `201 Created`

**Business Rules:**
- Verify picked quantities match order
- Generate packing slip
- Update order status to "processing"

**Permissions:** `sales.orders.fulfill`

---

### 3.9. Shipping Integration

**Endpoint:** `POST /api/orders/{orderId}/shipping`

**Description:** Tạo vận đơn và tích hợp với đơn vị vận chuyển.

**Request Body:**
```json
{
  "shippingProvider": "ghn",  // "ghn" | "viettel_post" | "grab_express" | "ninja_van"
  "serviceType": "standard",
  "pickupDate": "2026-02-05",
  "packageValue": 162032000,
  "insuranceRequired": true,
  "codAmount": 82032000,  // COD amount if applicable
  "notes": "Gọi điện trước khi giao"
}
```

**Response:** `201 Created`
```json
{
  "success": true,
  "data": {
    "trackingNumber": "GHNABCD123456",
    "carrierCode": "GHN",
    "shippingLabel": "https://api.ghn.vn/label/123456.pdf",
    "estimatedDelivery": "2026-02-10",
    "shippingFee": 50000
  }
}
```

**Business Rules:**
- Auto update order status to "shipped"
- Send tracking info to customer
- Webhook to receive delivery status updates

**Permissions:** `sales.orders.shipping`

---

## MODULE 4: SALES MANAGEMENT

**Mô tả:** Quản lý hoạt động bán hàng, sales pipeline, targets, commissions

---

### 4.1. Create Sales Opportunity

**Endpoint:** `POST /api/sales/opportunities`

**Description:** Tạo cơ hội bán hàng mới.

**Request Body:**
```json
{
  "workspaceId": "uuid",
  "name": "MacBook deal - Công ty ABC",
  "customerId": "uuid",
  "contactId": "uuid",
  "stage": "qualification",  // "qualification" | "proposal" | "negotiation" | "closed_won" | "closed_lost"
  "probability": 50,  // %
  "expectedValue": 500000000,
  "expectedCloseDate": "2026-03-01",
  "source": "website",  // "website" | "referral" | "cold_call" | "trade_show"
  "assignedTo": "user-uuid",
  "products": [
    {
      "productId": "uuid",
      "quantity": 5,
      "estimatedPrice": 89990000
    }
  ],
  "competitors": ["Apple Store", "CellphoneS"],
  "notes": "Khách hàng quan tâm đến bảo hành và hỗ trợ sau bán",
  "tags": ["enterprise", "high-value"]
}
```

**Response:** `201 Created`

**Opportunity Status Flow:**
```
qualification → proposal → negotiation → closed_won
                                ↓
                           closed_lost
```

**Permissions:** `sales.opportunities.create`

---

### 4.2. Update Opportunity Stage

**Endpoint:** `PUT /api/sales/opportunities/{id}/stage`

**Description:** Cập nhật giai đoạn của opportunity.

**Request Body:**
```json
{
  "stage": "negotiation",
  "probability": 75,
  "notes": "Đã gửi báo giá, đang đàm phán giá",
  "nextAction": "Gọi điện follow-up vào 07/02",
  "nextActionDate": "2026-02-07"
}
```

**Response:** `200 OK`

**Business Rules:**
- Log stage change history
- Update probability based on stage
- Auto create tasks for next actions
- Notify assignee

**Permissions:** `sales.opportunities.update`

---

### 4.3. Convert Opportunity to Order

**Endpoint:** `POST /api/sales/opportunities/{id}/convert`

**Description:** Chuyển opportunity thành đơn hàng thực tế.

**Request Body:**
```json
{
  "createOrder": true,
  "orderData": {
    "orderDate": "2026-02-04",
    "expectedDeliveryDate": "2026-02-10",
    "warehouseId": "uuid",
    "items": [...],
    "shippingAddress": {...},
    "paymentMethod": "bank_transfer"
  }
}
```

**Response:** `201 Created`
```json
{
  "success": true,
  "data": {
    "opportunityId": "opp-uuid",
    "orderId": "order-uuid",
    "orderNumber": "SO-2026-0001"
  }
}
```

**Business Rules:**
- Update opportunity stage to "closed_won"
- Create sales order from opportunity products
- Link order to opportunity

**Permissions:** `sales.opportunities.convert`

---

### 4.4. Sales Targets & KPIs

**Endpoint:** `POST /api/sales/targets`

**Description:** Tạo mục tiêu doanh số cho sales team.

**Request Body:**
```json
{
  "workspaceId": "uuid",
  "name": "Q1 2026 Sales Target",
  "period": "quarter",  // "month" | "quarter" | "year"
  "startDate": "2026-01-01",
  "endDate": "2026-03-31",
  "targetType": "revenue",  // "revenue" | "orders" | "new_customers"
  "targetValue": 10000000000,
  "currency": "VND",
  "assignedTo": [
    {
      "userId": "user-uuid-1",
      "targetValue": 3000000000
    },
    {
      "userId": "user-uuid-2",
      "targetValue": 2500000000
    }
  ]
}
```

**Response:** `201 Created`

**Related Endpoints:**
- `GET /api/sales/targets/{id}/progress` - Track target progress
- `GET /api/sales/kpis?userId=uuid&period=month` - Get KPI dashboard

**Permissions:** `sales.targets.create`

---

### 4.5. Sales Commission Calculation

**Endpoint:** `POST /api/sales/commissions/calculate`

**Description:** Tính hoa hồng cho sales team.

**Request Body:**
```json
{
  "workspaceId": "uuid",
  "period": "month",
  "month": "2026-02",
  "commissionRules": [
    {
      "userId": "user-uuid",
      "baseRate": 3,  // %
      "tiers": [
        {
          "from": 0,
          "to": 100000000,
          "rate": 3
        },
        {
          "from": 100000000,
          "to": 500000000,
          "rate": 5
        },
        {
          "from": 500000000,
          "to": null,
          "rate": 7
        }
      ]
    }
  ]
}
```

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "period": "2026-02",
    "totalCommission": 150000000,
    "users": [
      {
        "userId": "user-uuid",
        "userName": "Nguyễn Văn A",
        "totalSales": 3000000000,
        "commission": 150000000,
        "breakdown": [
          {
            "tier": "0-100M",
            "sales": 100000000,
            "rate": 3,
            "commission": 3000000
          },
          {
            "tier": "100M-500M",
            "sales": 400000000,
            "rate": 5,
            "commission": 20000000
          },
          {
            "tier": "500M+",
            "sales": 2500000000,
            "rate": 7,
            "commission": 175000000
          }
        ]
      }
    ]
  }
}
```

**Permissions:** `sales.commissions.calculate`

---

### 4.6. Sales Analytics Dashboard

**Endpoint:** `GET /api/sales/analytics?period=month&startDate=2026-02-01&endDate=2026-02-28`

**Description:** Dashboard analytics doanh số.

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "period": "2026-02",
    "totalRevenue": 5000000000,
    "totalOrders": 125,
    "averageOrderValue": 40000000,
    "totalCustomers": 85,
    "newCustomers": 15,
    "topProducts": [
      {
        "productId": "uuid",
        "productName": "MacBook Pro 16 inch M3 Max",
        "quantitySold": 45,
        "revenue": 4049550000
      }
    ],
    "topSalesReps": [
      {
        "userId": "uuid",
        "userName": "Nguyễn Văn A",
        "orders": 35,
        "revenue": 1800000000
      }
    ],
    "salesByStage": {
      "qualification": 20,
      "proposal": 15,
      "negotiation": 10,
      "closed_won": 80,
      "closed_lost": 5
    },
    "revenueTrend": [
      {
        "date": "2026-02-01",
        "revenue": 150000000
      },
      {
        "date": "2026-02-02",
        "revenue": 180000000
      }
    ]
  }
}
```

**Permissions:** `sales.analytics.view`

---

## MODULE 5: QUOTE MANAGEMENT

**Mô tả:** Quản lý báo giá, tạo & gửi báo giá cho khách hàng

---

### 5.1. Create Quote

**Endpoint:** `POST /api/quotes`

**Description:** Tạo báo giá mới.

**Request Body:**
```json
{
  "workspaceId": "uuid",
  "customerId": "uuid",
  "contactId": "uuid",
  "opportunityId": "uuid",  // Optional
  "quoteDate": "2026-02-04",
  "validUntil": "2026-02-18",  // 14 days
  "title": "Báo giá MacBook Pro cho Công ty ABC",
  "items": [
    {
      "productId": "uuid",
      "variantId": "uuid",
      "quantity": 5,
      "unitPrice": 89990000,
      "discount": {
        "type": "percentage",
        "value": 10
      },
      "taxRate": 10,
      "description": "MacBook Pro 16 inch M3 Max - Silver - 512GB"
    }
  ],
  "discountCode": "CORPORATE2026",
  "shippingCost": 0,
  "paymentTerms": "net_30",
  "deliveryTerms": "7-10 working days from order confirmation",
  "notes": "Giá đã bao gồm VAT 10%",
  "termsAndConditions": "1. Báo giá có hiệu lực 14 ngày...",
  "assignedTo": "user-uuid"
}
```

**Response:** `201 Created`
```json
{
  "success": true,
  "data": {
    "id": "quote-uuid",
    "quoteNumber": "QT-2026-0001",
    "status": "draft",
    "totalAmount": 404955000,
    "validUntil": "2026-02-18",
    "createdAt": "2026-02-04T10:00:00Z"
  }
}
```

**Quote Status Flow:**
```
draft → sent → viewed → accepted → converted_to_order
           ↓      ↓         ↓
       expired rejected  expired
```

**Business Rules:**
- Auto calculate totals
- Can revise quote (create new version)
- Track customer view status
- Auto expire after `validUntil` date

**Permissions:** `sales.quotes.create`

---

### 5.2. Send Quote to Customer

**Endpoint:** `POST /api/quotes/{quoteId}/send`

**Description:** Gửi báo giá cho khách hàng qua email.

**Request Body:**
```json
{
  "recipientEmail": "contact@abc.com",
  "ccEmails": ["manager@abc.com"],
  "subject": "Báo giá MacBook Pro - Công ty ABC",
  "message": "Kính gửi Quý khách,\n\nChúng tôi xin gửi báo giá như sau...",
  "attachments": [
    {
      "type": "pdf",  // Auto generate PDF
      "template": "default"
    }
  ],
  "notifyAssignee": true
}
```

**Response:** `200 OK`

**Business Rules:**
- Update status to "sent"
- Generate PDF quote
- Track email open (via tracking pixel)
- Track PDF view

**Permissions:** `sales.quotes.send`

---

### 5.3. Customer Accept/Reject Quote

**Endpoint:** `POST /api/quotes/{quoteId}/respond`

**Description:** Khách hàng chấp nhận hoặc từ chối báo giá.

**Request Body:**
```json
{
  "action": "accept",  // "accept" | "reject"
  "signature": "base64-encoded-signature",
  "signedBy": "Nguyễn Văn B",
  "signedAt": "2026-02-05T14:00:00Z",
  "notes": "Chấp nhận báo giá, yêu cầu giao hàng trong 7 ngày"
}
```

**Response:** `200 OK`

**Business Rules:**
- If accept: Update status to "accepted"
- If reject: Update status to "rejected", ask for reason
- Send notification to assignee
- Cannot accept if expired

**Permissions:** `public` (with secure token)

---

### 5.4. Convert Quote to Order

**Endpoint:** `POST /api/quotes/{quoteId}/convert`

**Description:** Chuyển báo giá thành đơn hàng.

**Request Body:**
```json
{
  "orderDate": "2026-02-05",
  "warehouseId": "uuid",
  "shippingAddress": {...},
  "paymentMethod": "bank_transfer",
  "notes": "Đơn hàng từ báo giá QT-2026-0001"
}
```

**Response:** `201 Created`
```json
{
  "success": true,
  "data": {
    "quoteId": "quote-uuid",
    "orderId": "order-uuid",
    "orderNumber": "SO-2026-0005"
  }
}
```

**Business Rules:**
- Quote status must be "accepted"
- Create order with same items & pricing
- Update quote status to "converted_to_order"
- Link order to quote

**Permissions:** `sales.quotes.convert`

---

### 5.5. Create Quote Revision

**Endpoint:** `POST /api/quotes/{quoteId}/revisions`

**Description:** Tạo phiên bản mới của báo giá (revision).

**Request Body:**
```json
{
  "reason": "Khách hàng yêu cầu thay đổi số lượng",
  "changes": {
    "items": [...],  // Updated items
    "validUntil": "2026-02-25"
  }
}
```

**Response:** `201 Created`
```json
{
  "success": true,
  "data": {
    "originalQuoteId": "quote-uuid",
    "newQuoteId": "quote-uuid-v2",
    "quoteNumber": "QT-2026-0001-R1",
    "revisionNumber": 1
  }
}
```

**Business Rules:**
- Original quote marked as "revised"
- New quote created with incremented revision number
- Keep history of all revisions

**Permissions:** `sales.quotes.revise`

---

### 5.6. Quote Templates

**Endpoint:** `POST /api/quotes/templates`

**Description:** Tạo template báo giá để tái sử dụng.

**Request Body:**
```json
{
  "workspaceId": "uuid",
  "name": "Corporate Package - 5 MacBooks",
  "description": "Template for corporate MacBook orders",
  "items": [
    {
      "productId": "uuid",
      "quantity": 5,
      "discount": {
        "type": "percentage",
        "value": 10
      }
    }
  ],
  "validityDays": 14,
  "paymentTerms": "net_30",
  "termsAndConditions": "..."
}
```

**Response:** `201 Created`

**Related Endpoints:**
- `POST /api/quotes/from-template/{templateId}` - Create quote from template
- `GET /api/quotes/templates` - List templates

**Permissions:** `sales.quotes.manage_templates`

---

### 5.7. Quote Analytics

**Endpoint:** `GET /api/quotes/analytics?period=month&startDate=2026-02-01`

**Description:** Phân tích hiệu quả báo giá.

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "totalQuotes": 50,
    "totalValue": 2500000000,
    "acceptedQuotes": 35,
    "acceptanceRate": 70,
    "averageTimeToAccept": 3.5,  // days
    "conversionRate": 68,  // % accepted quotes converted to orders
    "topProducts": [...]
  }
}
```

**Permissions:** `sales.quotes.analytics`

---

## MODULE 6: CONTRACT MANAGEMENT

**Mô tả:** Quản lý hợp đồng bán hàng, ký điện tử, tracking

---

### 6.1. Create Contract

**Endpoint:** `POST /api/contracts`

**Description:** Tạo hợp đồng mới.

**Request Body:**
```json
{
  "workspaceId": "uuid",
  "contractType": "sales",  // "sales" | "service" | "subscription"
  "customerId": "uuid",
  "orderId": "uuid",  // Optional: Link to order
  "quoteId": "uuid",  // Optional: Link to quote
  "contractDate": "2026-02-04",
  "startDate": "2026-02-05",
  "endDate": "2027-02-04",  // 1 year
  "autoRenewal": true,
  "renewalNoticeDays": 30,
  "title": "Hợp đồng cung cấp MacBook Pro",
  "contractNumber": "HD-2026-001",  // Auto-generated if not provided
  "value": 404955000,
  "currency": "VND",
  "paymentSchedule": [
    {
      "installment": 1,
      "dueDate": "2026-02-05",
      "amount": 202477500,
      "description": "50% deposit"
    },
    {
      "installment": 2,
      "dueDate": "2026-02-12",
      "amount": 202477500,
      "description": "50% upon delivery"
    }
  ],
  "items": [
    {
      "productId": "uuid",
      "description": "MacBook Pro 16 inch M3 Max",
      "quantity": 5,
      "unitPrice": 80991000,
      "total": 404955000
    }
  ],
  "terms": {
    "deliveryTerms": "Giao hàng trong vòng 7-10 ngày làm việc",
    "warrantyTerms": "Bảo hành 12 tháng",
    "paymentTerms": "Thanh toán 50% deposit, 50% khi giao hàng",
    "penaltyTerms": "Phạt 0.1%/ngày nếu giao hàng trễ"
  },
  "contractDocument": "https://cdn.nextx.vn/contracts/draft-hd-2026-001.pdf",
  "signatories": [
    {
      "party": "company",  // "company" | "customer"
      "name": "Nguyễn Văn A",
      "title": "Giám đốc",
      "email": "ceo@nextx.vn"
    },
    {
      "party": "customer",
      "name": "Trần Văn B",
      "title": "Giám đốc Công ty ABC",
      "email": "contact@abc.com"
    }
  ],
  "assignedTo": "user-uuid",
  "tags": ["enterprise", "high-value"]
}
```

**Response:** `201 Created`
```json
{
  "success": true,
  "data": {
    "id": "contract-uuid",
    "contractNumber": "HD-2026-001",
    "status": "draft",
    "value": 404955000,
    "startDate": "2026-02-05",
    "endDate": "2027-02-04",
    "createdAt": "2026-02-04T10:00:00Z"
  }
}
```

**Contract Status Flow:**
```
draft → pending_signature → signed → active → completed
  ↓                            ↓        ↓
cancelled                  rejected  expired
```

**Permissions:** `sales.contracts.create`

---

### 6.2. Send Contract for Signature

**Endpoint:** `POST /api/contracts/{contractId}/send-for-signature`

**Description:** Gửi hợp đồng để ký điện tử.

**Request Body:**
```json
{
  "signatureProvider": "docusign",  // "docusign" | "adobe_sign" | "internal"
  "signingOrder": "sequential",  // "sequential" | "parallel"
  "message": "Kính gửi Quý khách, vui lòng ký hợp đồng đính kèm",
  "reminderDays": 3,
  "expiryDays": 7
}
```

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "envelopeId": "env-123456",
    "signatureUrl": "https://docusign.com/sign/abc123",
    "status": "pending_signature",
    "sentAt": "2026-02-04T10:00:00Z"
  }
}
```

**Business Rules:**
- Update status to "pending_signature"
- Send email to signatories with signature link
- Track signature progress
- Auto reminder if not signed within X days

**Permissions:** `sales.contracts.send_signature`

---

### 6.3. Sign Contract (Internal)

**Endpoint:** `POST /api/contracts/{contractId}/sign`

**Description:** Ký hợp đồng (internal signature).

**Request Body:**
```json
{
  "signatoryId": "uuid",
  "signature": "base64-encoded-signature-image",
  "ipAddress": "123.456.789.012",
  "userAgent": "Mozilla/5.0...",
  "signedAt": "2026-02-04T14:00:00Z"
}
```

**Response:** `200 OK`

**Business Rules:**
- Record signature with timestamp, IP, user agent
- Update contract status when all parties signed
- Generate final signed PDF

**Permissions:** `public` (with secure token)

---

### 6.4. Activate Contract

**Endpoint:** `POST /api/contracts/{contractId}/activate`

**Description:** Kích hoạt hợp đồng sau khi ký.

**Request Body:**
```json
{
  "activationDate": "2026-02-05",
  "notes": "Hợp đồng đã được ký bởi cả 2 bên"
}
```

**Response:** `200 OK`

**Business Rules:**
- Status must be "signed"
- Update status to "active"
- Start tracking payment schedule
- Create recurring invoice if subscription

**Permissions:** `sales.contracts.activate`

---

### 6.5. Renew Contract

**Endpoint:** `POST /api/contracts/{contractId}/renew`

**Description:** Gia hạn hợp đồng.

**Request Body:**
```json
{
  "newStartDate": "2027-02-05",
  "newEndDate": "2028-02-04",
  "priceAdjustment": 5,  // % increase
  "notes": "Gia hạn hợp đồng với tăng giá 5%"
}
```

**Response:** `201 Created`
```json
{
  "success": true,
  "data": {
    "originalContractId": "contract-uuid",
    "renewedContractId": "contract-uuid-v2",
    "contractNumber": "HD-2026-001-R1"
  }
}
```

**Business Rules:**
- Original contract marked as "renewed"
- Create new contract with updated terms
- Link to original contract

**Permissions:** `sales.contracts.renew`

---

### 6.6. Terminate Contract

**Endpoint:** `POST /api/contracts/{contractId}/terminate`

**Description:** Chấm dứt hợp đồng trước hạn.

**Request Body:**
```json
{
  "terminationDate": "2026-06-01",
  "reason": "Khách hàng yêu cầu chấm dứt",
  "penaltyAmount": 10000000,
  "notes": "Đã thỏa thuận với khách hàng về penalty"
}
```

**Response:** `200 OK`

**Business Rules:**
- Calculate penalty if applicable
- Cancel pending payments
- Update status to "terminated"

**Permissions:** `sales.contracts.terminate`

---

### 6.7. Contract Compliance Tracking

**Endpoint:** `GET /api/contracts/{contractId}/compliance`

**Description:** Theo dõi tuân thủ điều khoản hợp đồng.

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "contractId": "contract-uuid",
    "contractNumber": "HD-2026-001",
    "complianceStatus": "at_risk",  // "compliant" | "at_risk" | "non_compliant"
    "checks": [
      {
        "checkType": "payment_schedule",
        "status": "compliant",
        "dueDate": "2026-02-05",
        "completedDate": "2026-02-05"
      },
      {
        "checkType": "delivery",
        "status": "at_risk",
        "dueDate": "2026-02-12",
        "expectedDate": "2026-02-14",
        "daysLate": 2
      }
    ],
    "milestones": [
      {
        "name": "Delivery",
        "dueDate": "2026-02-12",
        "status": "pending",
        "penaltyIfLate": 404955  // 0.1% per day
      }
    ]
  }
}
```

**Permissions:** `sales.contracts.compliance`

---

## MODULE 7: PRICING & DISCOUNT

**Mô tả:** Quản lý bảng giá, chiết khấu, khuyến mãi

---

### 7.1. Create Price List

**Endpoint:** `POST /api/pricing/price-lists`

**Description:** Tạo bảng giá.

**Request Body:**
```json
{
  "workspaceId": "uuid",
  "name": "Corporate Pricing 2026",
  "description": "Special pricing for corporate customers",
  "currency": "VND",
  "isDefault": false,
  "validFrom": "2026-02-01",
  "validTo": "2026-12-31",
  "applicableTo": {
    "customerSegments": ["enterprise", "vip"],
    "customerIds": ["cust-uuid-1", "cust-uuid-2"],
    "minOrderValue": 100000000
  },
  "priceRules": [
    {
      "productId": "uuid",
      "variantId": "uuid",
      "basePrice": 89990000,
      "discountType": "percentage",
      "discountValue": 15,
      "finalPrice": 76491500
    }
  ]
}
```

**Response:** `201 Created`

**Permissions:** `sales.pricing.manage`

---

### 7.2. Volume Pricing (Tiered Pricing)

**Endpoint:** `POST /api/pricing/volume-pricing`

**Description:** Tạo giá theo số lượng mua.

**Request Body:**
```json
{
  "workspaceId": "uuid",
  "productId": "uuid",
  "name": "MacBook Volume Discount",
  "tiers": [
    {
      "minQuantity": 1,
      "maxQuantity": 4,
      "unitPrice": 89990000,
      "discountPercent": 0
    },
    {
      "minQuantity": 5,
      "maxQuantity": 9,
      "unitPrice": 85490000,
      "discountPercent": 5
    },
    {
      "minQuantity": 10,
      "maxQuantity": null,
      "unitPrice": 80991000,
      "discountPercent": 10
    }
  ],
  "validFrom": "2026-02-01",
  "validTo": "2026-12-31"
}
```

**Response:** `201 Created`

**Business Rules:**
- Auto apply correct tier based on order quantity
- Tiers must not overlap

**Permissions:** `sales.pricing.volume`

---

### 7.3. Create Discount Campaign

**Endpoint:** `POST /api/discounts/campaigns`

**Description:** Tạo chiến dịch giảm giá.

**Request Body:**
```json
{
  "workspaceId": "uuid",
  "name": "Summer Sale 2026",
  "description": "Giảm giá mùa hè",
  "campaignType": "promotional",  // "promotional" | "seasonal" | "clearance"
  "startDate": "2026-06-01",
  "endDate": "2026-06-30",
  "discountType": "percentage",  // "percentage" | "fixed" | "buy_x_get_y"
  "discountValue": 20,
  "maxDiscountAmount": 50000000,  // Cap discount
  "applicableTo": {
    "productCategories": ["cat-uuid"],
    "productIds": ["prod-uuid"],
    "excludeProductIds": []
  },
  "conditions": {
    "minOrderValue": 50000000,
    "minQuantity": 2,
    "customerSegments": ["all"],
    "newCustomersOnly": false
  },
  "stackable": false,  // Can combine with other discounts?
  "isActive": true
}
```

**Response:** `201 Created`

**Permissions:** `sales.discounts.create`

---

### 7.4. Generate Discount Code

**Endpoint:** `POST /api/discounts/codes`

**Description:** Tạo mã giảm giá.

**Request Body:**
```json
{
  "workspaceId": "uuid",
  "campaignId": "uuid",
  "code": "SUMMER2026",  // Auto-generated if not provided
  "discountType": "percentage",
  "discountValue": 15,
  "usageLimit": 100,  // Total uses
  "usagePerCustomer": 1,
  "validFrom": "2026-06-01",
  "validTo": "2026-06-30",
  "minOrderValue": 20000000,
  "applicableProducts": ["prod-uuid"],
  "applicableCategories": ["cat-uuid"]
}
```

**Response:** `201 Created`
```json
{
  "success": true,
  "data": {
    "id": "code-uuid",
    "code": "SUMMER2026",
    "usageCount": 0,
    "remainingUses": 100,
    "createdAt": "2026-02-04T10:00:00Z"
  }
}
```

**Business Rules:**
- Code must be unique
- Track usage count
- Validate code on order creation
- Deactivate when usage limit reached

**Permissions:** `sales.discounts.manage_codes`

---

### 7.5. Apply Discount to Order

**Endpoint:** `POST /api/orders/{orderId}/apply-discount`

**Description:** Áp dụng discount vào đơn hàng.

**Request Body:**
```json
{
  "discountCode": "SUMMER2026"
}
```

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "orderId": "order-uuid",
    "discountApplied": {
      "code": "SUMMER2026",
      "type": "percentage",
      "value": 15,
      "discountAmount": 26997000,
      "subtotal": 179980000,
      "total": 152983000
    }
  }
}
```

**Business Rules:**
- Validate discount code (active, not expired, usage limit)
- Check order meets conditions (min value, products, etc.)
- Calculate discount amount
- Update order total

**Permissions:** `sales.orders.apply_discount`

---

### 7.6. Discount Performance Report

**Endpoint:** `GET /api/discounts/performance?campaignId=uuid&startDate=2026-06-01&endDate=2026-06-30`

**Description:** Báo cáo hiệu quả chiến dịch giảm giá.

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "campaignId": "uuid",
    "campaignName": "Summer Sale 2026",
    "period": "2026-06-01 to 2026-06-30",
    "totalCodes": 50,
    "codesUsed": 35,
    "usageRate": 70,
    "totalOrders": 95,
    "totalRevenue": 4000000000,
    "totalDiscount": 600000000,
    "averageDiscountPerOrder": 6315789,
    "roi": 566,  // %: (revenue - discount) / discount * 100
    "topProducts": [...]
  }
}
```

**Permissions:** `sales.discounts.analytics`

---

## MODULE 8: INVOICE & PAYMENT

**Mô tả:** Quản lý hóa đơn, thanh toán, công nợ

---

### 8.1. Create Invoice

**Endpoint:** `POST /api/invoices`

**Description:** Tạo hóa đơn.

**Request Body:**
```json
{
  "workspaceId": "uuid",
  "invoiceType": "sales",  // "sales" | "service" | "proforma"
  "customerId": "uuid",
  "orderId": "uuid",  // Link to order
  "invoiceDate": "2026-02-05",
  "dueDate": "2026-02-19",  // 14 days payment term
  "currency": "VND",
  "items": [
    {
      "productId": "uuid",
      "description": "MacBook Pro 16 inch M3 Max - Silver - 512GB",
      "quantity": 2,
      "unitPrice": 89990000,
      "discount": 17998000,
      "taxRate": 10,
      "taxAmount": 14399200,
      "total": 161982000
    }
  ],
  "subtotal": 179980000,
  "discount": 17998000,
  "taxAmount": 16199800,
  "shippingCost": 50000,
  "grandTotal": 162032000,
  "paymentTerms": "net_14",
  "notes": "Thanh toán qua chuyển khoản ngân hàng",
  "bankDetails": {
    "bankName": "Vietcombank",
    "accountNumber": "1234567890",
    "accountName": "Công ty NextX",
    "swiftCode": "BFTVVNVX"
  }
}
```

**Response:** `201 Created`
```json
{
  "success": true,
  "data": {
    "id": "invoice-uuid",
    "invoiceNumber": "INV-2026-0001",
    "status": "unpaid",
    "grandTotal": 162032000,
    "dueDate": "2026-02-19",
    "pdfUrl": "https://cdn.nextx.vn/invoices/INV-2026-0001.pdf",
    "createdAt": "2026-02-05T10:00:00Z"
  }
}
```

**Invoice Status:**
- `draft` - Nháp
- `sent` - Đã gửi cho khách
- `viewed` - Khách đã xem
- `unpaid` - Chưa thanh toán
- `partial` - Thanh toán 1 phần
- `paid` - Đã thanh toán đủ
- `overdue` - Quá hạn
- `cancelled` - Đã hủy

**Permissions:** `sales.invoices.create`

---

### 8.2. Record Payment

**Endpoint:** `POST /api/invoices/{invoiceId}/payments`

**Description:** Ghi nhận thanh toán.

**Request Body:**
```json
{
  "paymentDate": "2026-02-06",
  "amount": 162032000,
  "paymentMethod": "bank_transfer",  // "cash" | "bank_transfer" | "card" | "check"
  "reference": "REF-123456",  // Transaction reference
  "notes": "Chuyển khoản từ TK ***7890",
  "attachments": [
    "https://cdn.nextx.vn/payments/proof-001.jpg"
  ],
  "receivedBy": "user-uuid"
}
```

**Response:** `201 Created`
```json
{
  "success": true,
  "data": {
    "paymentId": "payment-uuid",
    "invoiceId": "invoice-uuid",
    "amount": 162032000,
    "paymentStatus": "paid",
    "remainingAmount": 0,
    "paidAt": "2026-02-06T10:00:00Z"
  }
}
```

**Business Rules:**
- Update invoice status based on paid amount
- If `amount >= grandTotal`: status = "paid"
- If `0 < amount < grandTotal`: status = "partial"
- Send payment receipt to customer

**Permissions:** `sales.payments.record`

---

### 8.3. Send Invoice to Customer

**Endpoint:** `POST /api/invoices/{invoiceId}/send`

**Description:** Gửi hóa đơn cho khách hàng qua email.

**Request Body:**
```json
{
  "recipientEmail": "contact@abc.com",
  "ccEmails": ["accounting@abc.com"],
  "subject": "Hóa đơn #INV-2026-0001",
  "message": "Kính gửi Quý khách,\n\nĐính kèm hóa đơn...",
  "attachPdf": true,
  "notifyOnView": true,
  "reminderDays": 3  // Send reminder X days before due
}
```

**Response:** `200 OK`

**Business Rules:**
- Update status to "sent"
- Track email open
- Track PDF view
- Auto schedule payment reminder

**Permissions:** `sales.invoices.send`

---

### 8.4. Payment Reminder

**Endpoint:** `POST /api/invoices/{invoiceId}/send-reminder`

**Description:** Gửi nhắc nở thanh toán.

**Request Body:**
```json
{
  "reminderType": "friendly",  // "friendly" | "urgent" | "final"
  "recipientEmail": "contact@abc.com",
  "message": "Nhắc nhở thanh toán hóa đơn INV-2026-0001"
}
```

**Response:** `200 OK`

**Auto Reminder Schedule:**
- 3 days before due date: "friendly"
- Due date: "urgent"
- 7 days after due date: "final"

**Permissions:** `sales.invoices.remind`

---

### 8.5. Accounts Receivable Report

**Endpoint:** `GET /api/invoices/accounts-receivable?asOfDate=2026-02-28&customerId=uuid`

**Description:** Báo cáo công nợ phải thu.

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "asOfDate": "2026-02-28",
    "totalReceivable": 500000000,
    "current": 200000000,  // Not due yet
    "overdue1to30": 150000000,
    "overdue31to60": 100000000,
    "overdue61to90": 30000000,
    "overdue90plus": 20000000,
    "customers": [
      {
        "customerId": "uuid",
        "customerName": "Công ty ABC",
        "totalReceivable": 162032000,
        "overdueAmount": 0,
        "invoices": [
          {
            "invoiceNumber": "INV-2026-0001",
            "invoiceDate": "2026-02-05",
            "dueDate": "2026-02-19",
            "amount": 162032000,
            "paidAmount": 0,
            "balance": 162032000,
            "daysOverdue": 0,
            "status": "unpaid"
          }
        ]
      }
    ]
  }
}
```

**Permissions:** `sales.invoices.receivable`

---

### 8.6. Credit Note (Hóa đơn điều chỉnh)

**Endpoint:** `POST /api/invoices/{invoiceId}/credit-note`

**Description:** Tạo hóa đơn điều chỉnh/hoàn trả.

**Request Body:**
```json
{
  "creditDate": "2026-02-10",
  "reason": "Trả lại 1 sản phẩm bị lỗi",
  "items": [
    {
      "invoiceItemId": "uuid",
      "quantity": 1,
      "unitPrice": 89990000,
      "total": 89990000
    }
  ],
  "refundMethod": "original_payment",  // "original_payment" | "store_credit" | "bank_transfer"
  "notes": "Khách hàng trả lại 1 MacBook bị lỗi màn hình"
}
```

**Response:** `201 Created`
```json
{
  "success": true,
  "data": {
    "creditNoteId": "cn-uuid",
    "creditNoteNumber": "CN-2026-0001",
    "originalInvoiceNumber": "INV-2026-0001",
    "creditAmount": 89990000,
    "newBalance": 72042000
  }
}
```

**Business Rules:**
- Reduce invoice balance
- If refund via original payment, create refund record
- If store credit, create credit balance for customer

**Permissions:** `sales.invoices.credit_note`

---

## COMPLETION CHECKLIST

### MODULE 1: Product Management ✅
- [x] 1.1 Create Product
- [x] 1.2 Update Product
- [x] 1.3 Delete Product
- [x] 1.4 Get Product Details
- [x] 1.5 List Products
- [x] 1.6 Manage Product Categories
- [x] 1.7 Bulk Import Products

### MODULE 2: Inventory Management ✅
- [x] 2.1 Create Warehouse
- [x] 2.2 Stock Adjustment
- [x] 2.3 Stock Transfer
- [x] 2.4 Get Stock Level
- [x] 2.5 Stock Reservation
- [x] 2.6 Stock Count
- [x] 2.7 Low Stock Alerts
- [x] 2.8 Inventory Valuation Report

### MODULE 3: Order Management ✅
- [x] 3.1 Create Sales Order
- [x] 3.2 Update Order Status
- [x] 3.3 Get Order Details
- [x] 3.4 List Orders
- [x] 3.5 Cancel Order
- [x] 3.6 Add Order Note
- [x] 3.7 Create Return Order
- [x] 3.8 Order Fulfillment
- [x] 3.9 Shipping Integration

### MODULE 4: Sales Management ✅
- [x] 4.1 Create Sales Opportunity
- [x] 4.2 Update Opportunity Stage
- [x] 4.3 Convert Opportunity to Order
- [x] 4.4 Sales Targets & KPIs
- [x] 4.5 Sales Commission Calculation
- [x] 4.6 Sales Analytics Dashboard

### MODULE 5: Quote Management ✅
- [x] 5.1 Create Quote
- [x] 5.2 Send Quote to Customer
- [x] 5.3 Customer Accept/Reject Quote
- [x] 5.4 Convert Quote to Order
- [x] 5.5 Create Quote Revision
- [x] 5.6 Quote Templates
- [x] 5.7 Quote Analytics

### MODULE 6: Contract Management ✅
- [x] 6.1 Create Contract
- [x] 6.2 Send Contract for Signature
- [x] 6.3 Sign Contract
- [x] 6.4 Activate Contract
- [x] 6.5 Renew Contract
- [x] 6.6 Terminate Contract
- [x] 6.7 Contract Compliance Tracking

### MODULE 7: Pricing & Discount ✅
- [x] 7.1 Create Price List
- [x] 7.2 Volume Pricing
- [x] 7.3 Create Discount Campaign
- [x] 7.4 Generate Discount Code
- [[x] 7.5 Apply Discount to Order
- [x] 7.6 Discount Performance Report

### MODULE 8: Invoice & Payment ✅
- [x] 8.1 Create Invoice
- [x] 8.2 Record Payment
- [x] 8.3 Send Invoice to Customer
- [x] 8.4 Payment Reminder
- [x] 8.5 Accounts Receivable Report
- [x] 8.6 Credit Note

---

## SUMMARY

- **Total Modules:** 8
- **Total Functions:** 56
- **Completion:** 100%

---

**Last Updated:** February 4, 2026  
**Version:** 1.0.0  
**Author:** NextX Product Team

