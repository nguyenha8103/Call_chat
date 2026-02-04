# SALES SERVICE - FUNCTION LIST (SUMMARY)

**Service:** Sales Service (Quản lý Bán hàng)  
**Port:** 8003  
**Database:** `sales_db` (PostgreSQL)  
**Tech Stack:** ASP.NET Core 8.0 + Entity Framework Core  
**Total Functions:** 62  
**Total Modules:** 7

---

## 📋 MỤC LỤC

1. [PRODUCT MANAGEMENT (7 functions)](#module-1-product-management-7-functions)
2. [QUOTATION MANAGEMENT (9 functions)](#module-2-quotation-management-9-functions)
3. [ORDER MANAGEMENT (12 functions)](#module-3-order-management-12-functions)
4. [CONTRACT MANAGEMENT (8 functions)](#module-4-contract-management-8-functions)
5. [INVENTORY MANAGEMENT (11 functions)](#module-5-inventory-management-11-functions)
6. [INVOICE MANAGEMENT (9 functions)](#module-6-invoice-management-9-functions)
7. [SALES REPORTS (6 functions)](#module-7-sales-reports-6-functions)
8. [KEY FLOWS](#key-flows)
9. [CHECKLIST FIGMA](#checklist-figma)

---

## 📦 MODULE 1: PRODUCT MANAGEMENT (7 functions)

| # | Function | Nhiệm vụ | Input | Output |
|---|----------|----------|-------|--------|
| 1.1 | **Create Product** | Tạo sản phẩm mới với variants | `workspaceId`, `sku`, `name`, `price`, `variants[]` | `productId`, `sku`, `success` |
| 1.2 | **Update Product** | Cập nhật thông tin sản phẩm | `productId`, `data` (name, price, description) | `success`, `product` |
| 1.3 | **Delete Product** | Xóa sản phẩm (soft delete) | `productId`, `workspaceId` | `success` |
| 1.4 | **Get Product Details** | Lấy chi tiết sản phẩm + inventory + stats | `productId` | `product` object với variants, stock, sales stats |
| 1.5 | **List Products** | Lấy danh sách sản phẩm với filters | `workspaceId`, `filters`, `pagination` | `products[]`, `total` |
| 1.6 | **Manage Categories** | Tạo/Sửa/Xóa category sản phẩm | `workspaceId`, `name`, `parentId` | `categoryId`, `success` |
| 1.7 | **Bulk Import Products** | Import sản phẩm từ Excel/CSV | `workspaceId`, `file`, `updateExisting` | `successCount`, `errorCount`, `errors[]` |

---

## 💰 MODULE 2: QUOTATION MANAGEMENT (9 functions)

| # | Function | Nhiệm vụ | Input | Output |
|---|----------|----------|-------|--------|
| 2.1 | **Create Quotation** | Tạo báo giá cho khách hàng | `workspaceId`, `customerId`, `items[]`, `discount`, `validUntil` | `quotationId`, `code`, `total` |
| 2.2 | **Get Quotation Details** | Xem chi tiết báo giá | `quotationId` | `quotation` object với items, customer, pricing |
| 2.3 | **Update Quotation** | Cập nhật báo giá (draft/sent only) | `quotationId`, `data` | `success`, `quotation` |
| 2.4 | **Send Quotation** | Gửi báo giá qua email (PDF) | `quotationId`, `emailTo` | `success`, `sentAt` |
| 2.5 | **List Quotations** | Danh sách báo giá với filters | `workspaceId`, `filters`, `pagination` | `quotations[]`, `total` |
| 2.6 | **Delete Quotation** | Xóa báo giá (soft delete) | `quotationId` | `success` |
| 2.7 | **Duplicate Quotation** | Tạo bản sao báo giá | `quotationId` | `newQuotationId` |
| 2.8 | **Convert to Order** | Chuyển báo giá thành đơn hàng | `quotationId` | `orderId`, `success` |
| 2.9 | **Export Quotations** | Xuất báo giá ra Excel/PDF | `workspaceId`, `filters` | `fileUrl` |

---

## 🛍️ MODULE 3: ORDER MANAGEMENT (12 functions)

| # | Function | Nhiệm vụ | Input | Output |
|---|----------|----------|-------|--------|
| 3.1 | **Create Order** | Tạo đơn hàng — Reserve stock ngay | `workspaceId`, `customerId`, `items[]`, `shipping` | `orderId`, `code`, `total` |
| 3.2 | **Get Order Details** | Xem chi tiết đơn hàng + history | `orderId` | `order` object đầy đủ |
| 3.3 | **Update Order** | Cập nhật đơn hàng (pending/confirmed only) | `orderId`, `data` | `success`, `order` |
| 3.4 | **Delete Order** | Xóa đơn hàng (draft only) | `orderId` | `success` |
| 3.5 | **List Orders** | Danh sách đơn hàng với filters | `workspaceId`, `filters`, `pagination` | `orders[]`, `total` |
| 3.6 | **Search Orders** | Tìm kiếm đơn hàng theo code/customer | `query` | `orders[]` |
| 3.7 | **Update Order Status** | Cập nhật trạng thái (pending → shipping → delivered) | `orderId`, `status`, `notes` | `success`, `order` |
| 3.8 | **Cancel Order** | Hủy đơn hàng — Release stock | `orderId`, `reason` | `success` |
| 3.9 | **Split Order** | Tách đơn hàng (giao nhiều lần) | `orderId`, `items[]` | `newOrderIds[]` |
| 3.10 | **Merge Orders** | Gộp nhiều đơn hàng | `orderIds[]` | `newOrderId` |
| 3.11 | **Complete Order** | Hoàn thành đơn hàng → Auto tạo invoice | `orderId` | `success`, `invoiceId` |
| 3.12 | **Export Orders** | Xuất đơn hàng ra Excel/PDF | `filters` | `fileUrl` |

---

## 📄 MODULE 4: CONTRACT MANAGEMENT (8 functions)

| # | Function | Nhiệm vụ | Input | Output |
|---|----------|----------|-------|--------|
| 4.1 | **Create Contract** | Tạo hợp đồng bán hàng (dài hạn) | `workspaceId`, `customerId`, `quotationId`, `terms` | `contractId`, `code` |
| 4.2 | **Get Contract Details** | Xem chi tiết hợp đồng + progress | `contractId` | `contract` object |
| 4.3 | **Update Contract** | Cập nhật hợp đồng (draft only) | `contractId`, `data` | `success` |
| 4.4 | **List Contracts** | Danh sách hợp đồng với filters | `workspaceId`, `filters` | `contracts[]` |
| 4.5 | **Delete Contract** | Xóa hợp đồng (draft only) | `contractId` | `success` |
| 4.6 | **Sign Contract** | Ký hợp đồng — Upload signed PDF | `contractId`, `signedDocument` | `success`, `signedAt` |
| 4.7 | **Create Orders from Contract** | Tạo đơn hàng theo lịch giao hàng | `contractId`, `schedule` | `orderIds[]` |
| 4.8 | **Track Contract Progress** | Theo dõi tiến độ giao hàng & thanh toán | `contractId` | `progress` object |

---

## 📦 MODULE 5: INVENTORY MANAGEMENT (11 functions)

| # | Function | Nhiệm vụ | Input | Output |
|---|----------|----------|-------|--------|
| 5.1 | **Get Stock Summary** | Xem tổng quan tồn kho toàn workspace | `workspaceId` | `summary` object |
| 5.2 | **Get Product Stock** | Xem tồn kho chi tiết sản phẩm | `productId` | `stock` object (total, reserved, available) |
| 5.3 | **Stock In (Nhập kho)** | Nhập hàng vào kho | `productId`, `quantity`, `purchaseOrderId` | `transactionId`, `newStock` |
| 5.4 | **Stock Out (Xuất kho)** | Xuất hàng không qua đơn hàng | `productId`, `quantity`, `reason` | `transactionId`, `newStock` |
| 5.5 | **Stock Transfer** | Chuyển kho giữa các warehouse | `productId`, `fromWarehouse`, `toWarehouse`, `quantity` | `transactionId` |
| 5.6 | **Stock Adjustment** | Điều chỉnh tồn kho (kiểm kê) | `productId`, `actualQuantity`, `reason` | `transactionId` |
| 5.7 | **Reserve Stock** | Giữ hàng cho order — Update reserved_stock | `orderId`, `items[]` | `success` |
| 5.8 | **Release Reserved Stock** | Hủy giữ hàng (order cancelled) | `orderId` | `success` |
| 5.9 | **Deduct Stock** | Trừ tồn kho thực (order shipping) | `orderId` | `success` |
| 5.10 | **Low Stock Alert** | Danh sách sản phẩm sắp hết hàng | `workspaceId`, `threshold` | `products[]` |
| 5.11 | **Stock Transaction History** | Lịch sử nhập/xuất/điều chỉnh | `productId`, `dateRange` | `transactions[]` |

---

## 🧾 MODULE 6: INVOICE MANAGEMENT (9 functions)

| # | Function | Nhiệm vụ | Input | Output |
|---|----------|----------|-------|--------|
| 6.1 | **Create Invoice** | Tạo hóa đơn từ order/contract | `orderId` hoặc `contractId` | `invoiceId`, `code` |
| 6.2 | **Get Invoice Details** | Xem chi tiết hóa đơn + payments | `invoiceId` | `invoice` object |
| 6.3 | **Update Invoice** | Cập nhật hóa đơn (draft only) | `invoiceId`, `data` | `success` |
| 6.4 | **Delete Invoice** | Xóa hóa đơn (draft only) | `invoiceId` | `success` |
| 6.5 | **List Invoices** | Danh sách hóa đơn với filters | `workspaceId`, `filters` | `invoices[]` |
| 6.6 | **Record Payment** | Ghi nhận thanh toán — Update status | `invoiceId`, `amount`, `method` | `paymentId`, `success` |
| 6.7 | **Void Invoice** | Hủy hóa đơn (sai/duplicate) | `invoiceId`, `reason` | `success` |
| 6.8 | **Send Invoice** | Gửi hóa đơn qua email (PDF) | `invoiceId`, `emailTo` | `success`, `sentAt` |
| 6.9 | **Track Receivables** | Theo dõi công nợ phải thu | `workspaceId` | `receivables` object |

---

## 📊 MODULE 7: SALES REPORTS (6 functions)

| # | Function | Nhiệm vụ | Input | Output |
|---|----------|----------|-------|--------|
| 7.1 | **Sales Overview Report** | Tổng quan doanh thu, đơn hàng, KH | `workspaceId`, `dateRange` | `overview` object |
| 7.2 | **Sales by Product** | Báo cáo bán hàng theo sản phẩm | `workspaceId`, `dateRange` | `products[]` với revenue, profit |
| 7.3 | **Sales by Customer** | Báo cáo theo khách hàng (top KH) | `workspaceId`, `dateRange` | `customers[]` |
| 7.4 | **Sales by Agent** | Báo cáo theo nhân viên bán hàng | `workspaceId`, `dateRange` | `agents[]` với commission |
| 7.5 | **Revenue Forecast** | Dự báo doanh thu (linear regression) | `workspaceId`, `months` | `forecast[]` |
| 7.6 | **Export Reports** | Xuất báo cáo ra Excel/PDF | `reportType`, `filters` | `fileUrl` |

---

## 🔄 KEY FLOWS

### Flow 1: Quotation → Order (Báo giá → Đơn hàng)
```
1. Sales → Create Quotation (2.1) → Tạo báo giá cho KH
2. Sales → Send Quotation (2.4) → Gửi email PDF báo giá
3. Customer → Accept quotation (external)
4. Sales → Convert to Order (2.8) → Chuyển báo giá thành đơn hàng
   → System auto Reserve Stock (5.7)
5. Sales → Update Order Status (3.7) → Confirmed → Processing → Shipping
   → System auto Deduct Stock (5.9) khi status = Shipping
6. Sales → Complete Order (3.11) → Auto create Invoice (6.1)
7. Sales → Record Payment (6.6) → Ghi nhận thanh toán
```

### Flow 2: Direct Order (Đơn hàng trực tiếp)
```
1. Sales → Create Order (3.1) → Tạo đơn hàng
   → System check stock availability
   → System auto Reserve Stock (5.7)
2. Sales → Update Order Status (3.7) → Confirmed → Processing → Shipping
   → System auto Deduct Stock (5.9) khi status = Shipping
3. Sales → Complete Order (3.11) → Auto create Invoice (6.1)
4. Sales → Record Payment (6.6) → Update invoice status
```

### Flow 3: Contract Sales (Hợp đồng bán hàng)
```
1. Sales → Create Quotation (2.1) → Tạo báo giá
2. Sales → Create Contract (4.1) → Tạo hợp đồng từ quotation
   → Set payment schedule (30% ký, 40% giao 50%, 30% hoàn thành)
   → Set delivery schedule (3 đợt giao hàng)
3. Customer → Sign Contract (4.6) → Upload signed PDF
4. Sales → Create Orders from Contract (4.7) → Tạo orders theo lịch giao
5. Sales → Track Contract Progress (4.8) → Theo dõi tiến độ
6. System → Auto create Invoices theo payment milestones
```

### Flow 4: Inventory Management (Quản lý kho)
```
1. Warehouse → Stock In (5.3) → Nhập hàng vào kho
   → Update total_stock
2. Sales → Create Order (3.1) → Reserve Stock (5.7)
   → Update reserved_stock
3. Sales → Update Order Status (3.7) → Shipping → Deduct Stock (5.9)
   → Update total_stock (trừ đi)
   → Update reserved_stock (release)
4. System → Low Stock Alert (5.10) → Notify khi stock < threshold
5. Warehouse → Stock Adjustment (5.6) → Kiểm kê định kỳ
```

### Flow 5: Invoice & Payment (Hóa đơn & Thanh toán)
```
1. Sales → Complete Order (3.11) → Auto create Invoice (6.1)
   → Status = Unpaid
2. Sales → Send Invoice (6.8) → Email PDF invoice to customer
3. Customer → Pays (external)
4. Sales → Record Payment (6.6) → Input amount, method, date
   → If full paid → Invoice status = Paid
   → If partial → Invoice status = Partial
5. Admin → Track Receivables (6.9) → Xem danh sách công nợ
```

---

## ✅ CHECKLIST FIGMA

### ✅ MODULE 1: PRODUCT MANAGEMENT (100%)
- ✅ 1.1 Create Product - Dialog tạo sản phẩm với variants
- ✅ 1.2 Update Product - Dialog cập nhật thông tin
- ✅ 1.3 Delete Product - Dialog xác nhận xóa
- ✅ 1.4 Get Product Details - UI hiển thị chi tiết + stock + stats
- ✅ 1.5 List Products - Table danh sách sản phẩm với filters
- ✅ 1.6 Manage Categories - Tree view categories
- ✅ 1.7 Bulk Import Products - Dialog upload Excel/CSV

### ✅ MODULE 2: QUOTATION MANAGEMENT (100%)
- ✅ 2.1 Create Quotation - Dialog tạo báo giá với product selector
- ✅ 2.2 Get Quotation Details - UI hiển thị chi tiết + preview PDF
- ✅ 2.3 Update Quotation - Dialog cập nhật
- ✅ 2.4 Send Quotation - Dialog nhập email + preview
- ✅ 2.5 List Quotations - Table với status badges
- ✅ 2.6 Delete Quotation - Dialog xác nhận xóa
- ✅ 2.7 Duplicate Quotation - Button "Duplicate"
- ✅ 2.8 Convert to Order - Button "Chuyển thành đơn hàng"
- ✅ 2.9 Export Quotations - Button "Xuất Excel"

### ✅ MODULE 3: ORDER MANAGEMENT (100%)
- ✅ 3.1 Create Order - Dialog tạo đơn hàng với stock check
- ✅ 3.2 Get Order Details - UI chi tiết order + timeline
- ✅ 3.3 Update Order - Dialog cập nhật
- ✅ 3.4 Delete Order - Dialog xác nhận xóa
- ✅ 3.5 List Orders - Table với status flow UI
- ✅ 3.6 Search Orders - Search box với autocomplete
- ✅ 3.7 Update Order Status - Dropdown status với confirmation
- ✅ 3.8 Cancel Order - Dialog nhập lý do hủy
- ✅ 3.9 Split Order - Dialog chọn items để tách
- ✅ 3.10 Merge Orders - Dialog chọn orders để gộp
- ✅ 3.11 Complete Order - Button "Hoàn thành" → Auto tạo invoice
- ✅ 3.12 Export Orders - Button "Xuất Excel"

### ✅ MODULE 4: CONTRACT MANAGEMENT (100%)
- ✅ 4.1 Create Contract - Dialog tạo hợp đồng với payment/delivery schedule
- ✅ 4.2 Get Contract Details - UI hiển thị contract + progress bars
- ✅ 4.3 Update Contract - Dialog cập nhật
- ✅ 4.4 List Contracts - Table với status + progress
- ✅ 4.5 Delete Contract - Dialog xác nhận xóa
- ✅ 4.6 Sign Contract - Dialog upload signed PDF
- ✅ 4.7 Create Orders from Contract - Dialog chọn delivery schedule
- ✅ 4.8 Track Contract Progress - UI progress tracking (delivery + payment)

### ✅ MODULE 5: INVENTORY MANAGEMENT (100%)
- ✅ 5.1 Get Stock Summary - Dashboard cards tổng quan kho
- ✅ 5.2 Get Product Stock - UI hiển thị stock by warehouse/variant
- ✅ 5.3 Stock In - Dialog nhập kho với batch/expiry
- ✅ 5.4 Stock Out - Dialog xuất kho với reason
- ✅ 5.5 Stock Transfer - Dialog chọn from/to warehouse
- ✅ 5.6 Stock Adjustment - Dialog kiểm kê với actual quantity
- ✅ 5.7 Reserve Stock - Logic auto khi tạo order
- ✅ 5.8 Release Reserved Stock - Logic auto khi cancel order
- ✅ 5.9 Deduct Stock - Logic auto khi order shipping
- ✅ 5.10 Low Stock Alert - Badge/notification list
- ✅ 5.11 Stock Transaction History - Table lịch sử nhập/xuất

### ✅ MODULE 6: INVOICE MANAGEMENT (100%)
- ✅ 6.1 Create Invoice - Dialog tạo hóa đơn (auto từ order)
- ✅ 6.2 Get Invoice Details - UI hiển thị invoice + payments
- ✅ 6.3 Update Invoice - Dialog cập nhật
- ✅ 6.4 Delete Invoice - Dialog xác nhận xóa
- ✅ 6.5 List Invoices - Table với payment status + overdue badge
- ✅ 6.6 Record Payment - Dialog ghi nhận thanh toán
- ✅ 6.7 Void Invoice - Dialog nhập lý do hủy
- ✅ 6.8 Send Invoice - Dialog nhập email + preview PDF
- ✅ 6.9 Track Receivables - UI aging report + top debtors

### ✅ MODULE 7: SALES REPORTS (100%)
- ✅ 7.1 Sales Overview Report - Dashboard với charts
- ✅ 7.2 Sales by Product - Table + bar chart
- ✅ 7.3 Sales by Customer - Table với customer segments
- ✅ 7.4 Sales by Agent - Table với commission calculation
- ✅ 7.5 Revenue Forecast - Line chart với prediction
- ✅ 7.6 Export Reports - Button "Xuất Excel/PDF"

---

## 📊 PROGRESS SUMMARY

| Module | Functions | Status | Progress |
|--------|-----------|--------|----------|
| 1. Product Management | 7 | ✅ Done | 100% |
| 2. Quotation Management | 9 | ✅ Done | 100% |
| 3. Order Management | 12 | ✅ Done | 100% |
| 4. Contract Management | 8 | ✅ Done | 100% |
| 5. Inventory Management | 11 | ✅ Done | 100% |
| 6. Invoice Management | 9 | ✅ Done | 100% |
| 7. Sales Reports | 6 | ✅ Done | 100% |
| **TOTAL** | **62** | **✅ DONE** | **100%** |

---

## 🎯 NOTES

### Tech Stack Details:
- **Backend:** ASP.NET Core 8.0
- **ORM:** Entity Framework Core
- **Database:** PostgreSQL (`sales_db`)
- **File Storage:** S3/Azure Blob (product images, signed documents)
- **Email:** SMTP service (quotations, invoices)
- **PDF Generation:** iTextSharp / Rotativa
- **Excel Export:** EPPlus / ClosedXML

### Business Rules:
1. **Stock Reservation Flow:**
   - Order created → Reserve stock (update `reserved_stock`)
   - Order cancelled → Release reserved stock
   - Order shipping → Deduct stock (update `quantity`, release `reserved_stock`)
   - Reserved stock auto-release after 24h if order not confirmed

2. **Order Status Flow:**
   ```
   Draft → Pending → Confirmed → Processing → Shipping → Delivered → Completed
     ↓         ↓         ↓
   Cancelled ← ← (cannot cancel after shipping)
   ```

3. **Pricing Rules:**
   - Subtotal = Σ(quantity × unit_price - item_discount)
   - Total = (Subtotal - order_discount) × (1 + tax%) + shipping_fee
   - Discounts không cộng dồn (chọn discount lớn nhất)

4. **Invoice Rules:**
   - Auto generate từ completed order
   - Due date = Issue date + Payment terms (default 15 days)
   - Status: Draft → Sent → Unpaid → Partial → Paid → Overdue
   - Allow partial payment
   - Auto send reminder 3 days before due date

5. **Contract Rules:**
   - Payment schedule total = 100%
   - Auto create orders theo delivery schedule
   - Track progress: Delivery % và Payment %
   - Cannot delete/modify after signed

### Database Schema:
- **products** table (SKU unique, soft delete)
- **product_variants** table (color, size, etc.)
- **categories** table (nested categories)
- **quotations** table
- **quotation_items** table
- **orders** table (auto-generate code: ORD-YYYYMMDD-XXX)
- **order_items** table
- **contracts** table (code: CT-YYYYMMDD-XXX)
- **contract_payment_schedule** table
- **contract_delivery_schedule** table
- **inventory** table (total_stock, reserved_stock, available_stock)
- **stock_transactions** table (in, out, deduct, adjustment)
- **invoices** table (code: INV-YYYYMMDD-XXX)
- **payments** table
- **price_lists** table (customer-specific pricing)
- **discounts** table (promotion rules)

### Integration Dependencies:
- **CRM Service:** Customer data (name, email, address, segment)
- **IAM Service:** User authentication, workspace context, permissions
- **Notification Service:** Email quotations/invoices, SMS order confirmations
- **Accounting Service:** Revenue tracking, cost accounting, profit calculation
- **Payment Gateway:** VNPay, Momo, Stripe (online payments)

### Permissions:
- **Products:** `sales.products.view`, `sales.products.create`, `sales.products.update`, `sales.products.delete`, `sales.products.import`
- **Quotations:** `sales.quotations.view`, `sales.quotations.create`, `sales.quotations.update`, `sales.quotations.send`, `sales.quotations.convert`
- **Orders:** `sales.orders.view`, `sales.orders.create`, `sales.orders.update`, `sales.orders.cancel`, `sales.orders.split`
- **Contracts:** `sales.contracts.view`, `sales.contracts.create`, `sales.contracts.sign`
- **Inventory:** `sales.inventory.view`, `sales.inventory.stock_in`, `sales.inventory.stock_out`, `sales.inventory.adjust`
- **Invoices:** `sales.invoices.view`, `sales.invoices.create`, `sales.invoices.record_payment`, `sales.invoices.void`
- **Reports:** `sales.reports.view`, `sales.reports.export`

**Total Permissions:** 42

---

## 🚀 DEPLOYMENT INFO

**Production URL:** `https://api.nextx.vn/sales` (Port 8003)  
**Staging URL:** `https://staging.nextx.vn/sales`  
**Local Dev:** `http://localhost:8003`

---

**Last Updated:** 04/02/2026  
**Version:** 1.0.0  
**Status:** ✅ Production Ready
