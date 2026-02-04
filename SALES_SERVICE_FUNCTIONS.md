# SALES SERVICE - FUNCTIONS SPECIFICATION

**Service Name:** Sales Service  
**Total Modules:** 8  
**Total Functions:** 56  
**Database:** PostgreSQL 15+ (`sales_db`)  
**Cache:** Redis 7+  
**Message Queue:** RabbitMQ

---

## 📦 MODULE 1: PRODUCT MANAGEMENT (7 functions)

| # | Function | Nhiệm vụ | Input | Output |
|---|----------|----------|-------|--------|
| 1.1 | **Create Product** | Tạo sản phẩm/dịch vụ mới với variants | `workspaceId`, `sku`, `name`, `type`, `price`, `variants[]` | `productId`, `sku`, `success` |
| 1.2 | **Update Product** | Cập nhật thông tin sản phẩm | `productId`, `data` (name, price, description) | `success`, `product` |
| 1.3 | **Delete Product** | Xóa sản phẩm (soft delete) | `productId`, `workspaceId` | `success` |
| 1.4 | **Get Product Details** | Lấy chi tiết sản phẩm + inventory + stats | `productId` | `product` object với variants, stock, sales stats |
| 1.5 | **List Products** | Lấy danh sách sản phẩm với filters | `workspaceId`, `filters`, `pagination` | `products[]`, `total` |
| 1.6 | **Manage Categories** | Tạo/Sửa/Xóa category sản phẩm | `workspaceId`, `name`, `parentId` | `categoryId`, `success` |
| 1.7 | **Bulk Import Products** | Import sản phẩm từ Excel/CSV | `workspaceId`, `file`, `updateExisting` | `successCount`, `errorCount`, `errors[]` |

---

## 📊 MODULE 2: INVENTORY MANAGEMENT (8 functions)

| # | Function | Nhiệm vụ | Input | Output |
|---|----------|----------|-------|--------|
| 2.1 | **Create Warehouse** | Tạo kho hàng mới | `workspaceId`, `code`, `name`, `address`, `capacity` | `warehouseId`, `success` |
| 2.2 | **Stock Adjustment** | Điều chỉnh tồn kho (nhập/xuất/kiểm kê) | `warehouseId`, `type`, `items[]`, `reason` | `adjustmentId`, `success` |
| 2.3 | **Stock Transfer** | Chuyển hàng giữa các kho | `fromWarehouseId`, `toWarehouseId`, `items[]` | `transferId`, `trackingNumber` |
| 2.4 | **Get Stock Level** | Lấy thông tin tồn kho sản phẩm | `productId`, `warehouseId` | `totalStock`, `available`, `reserved`, `warehouses[]` |
| 2.5 | **Stock Reservation** | Giữ hàng cho đơn hàng | `orderId`, `warehouseId`, `items[]`, `expiresAt` | `reservationId`, `success` |
| 2.6 | **Stock Count** | Tạo phiên kiểm kê tồn kho | `warehouseId`, `countType`, `productIds[]`, `assignedTo[]` | `stockCountId`, `success` |
| 2.7 | **Low Stock Alerts** | Lấy danh sách sản phẩm sắp hết hàng | `workspaceId`, `warehouseId` | `alerts[]` với product, currentStock, threshold |
| 2.8 | **Inventory Valuation** | Báo cáo giá trị tồn kho (FIFO/LIFO) | `warehouseId`, `date`, `method` | `totalValue`, `products[]` với quantity, cost |

---

## 🛒 MODULE 3: ORDER MANAGEMENT (9 functions)

| # | Function | Nhiệm vụ | Input | Output |
|---|----------|----------|-------|--------|
| 3.1 | **Create Sales Order** | Tạo đơn hàng bán | `workspaceId`, `customerId`, `items[]`, `shipping`, `payment` | `orderId`, `orderNumber`, `total` |
| 3.2 | **Update Order Status** | Cập nhật trạng thái đơn hàng | `orderId`, `status`, `notes`, `notifyCustomer` | `success`, `newStatus` |
| 3.3 | **Get Order Details** | Lấy chi tiết đơn hàng đầy đủ | `orderId` | `order` object với items, payments, history |
| 3.4 | **List Orders** | Lấy danh sách đơn hàng với filters | `workspaceId`, `filters`, `pagination` | `orders[]`, `total` |
| 3.5 | **Cancel Order** | Hủy đơn hàng + release stock | `orderId`, `reason`, `restockItems` | `success` |
| 3.6 | **Add Order Note** | Thêm ghi chú cho đơn hàng | `orderId`, `content`, `isInternal`, `attachments[]` | `noteId`, `success` |
| 3.7 | **Create Return Order** | Tạo đơn trả hàng | `orderId`, `items[]`, `reason`, `refundMethod` | `returnOrderId`, `success` |
| 3.8 | **Order Fulfillment** | Xử lý đóng gói và chuẩn bị giao | `orderId`, `warehouseId`, `items[]`, `packageInfo` | `fulfillmentId`, `success` |
| 3.9 | **Shipping Integration** | Tạo vận đơn với đơn vị vận chuyển | `orderId`, `shippingProvider`, `serviceType` | `trackingNumber`, `shippingLabel` |

---

## 💼 MODULE 4: SALES MANAGEMENT (6 functions)

| # | Function | Nhiệm vụ | Input | Output |
|---|----------|----------|-------|--------|
| 4.1 | **Create Opportunity** | Tạo cơ hội bán hàng mới | `workspaceId`, `customerId`, `name`, `value`, `stage`, `products[]` | `opportunityId`, `success` |
| 4.2 | **Update Opportunity Stage** | Cập nhật giai đoạn opportunity | `opportunityId`, `stage`, `probability`, `notes` | `success`, `newStage` |
| 4.3 | **Convert to Order** | Chuyển opportunity thành đơn hàng | `opportunityId`, `orderData` | `orderId`, `orderNumber` |
| 4.4 | **Sales Targets & KPIs** | Tạo/theo dõi mục tiêu doanh số | `workspaceId`, `period`, `targetValue`, `assignedTo[]` | `targetId`, `progress` |
| 4.5 | **Commission Calculation** | Tính hoa hồng cho sales team | `workspaceId`, `period`, `commissionRules[]` | `totalCommission`, `users[]` với breakdown |
| 4.6 | **Sales Analytics** | Dashboard phân tích doanh số | `workspaceId`, `period`, `filters` | `revenue`, `orders`, `topProducts[]`, `trends[]` |

---

## 📋 MODULE 5: QUOTE MANAGEMENT (7 functions)

| # | Function | Nhiệm vụ | Input | Output |
|---|----------|----------|-------|--------|
| 5.1 | **Create Quote** | Tạo báo giá mới | `workspaceId`, `customerId`, `items[]`, `validUntil`, `terms` | `quoteId`, `quoteNumber` |
| 5.2 | **Send Quote** | Gửi báo giá cho khách qua email | `quoteId`, `recipientEmail`, `message`, `attachPDF` | `success`, `sentAt` |
| 5.3 | **Accept/Reject Quote** | Khách hàng phản hồi báo giá | `quoteId`, `action`, `signature`, `notes` | `success`, `newStatus` |
| 5.4 | **Convert Quote to Order** | Chuyển báo giá thành đơn hàng | `quoteId`, `orderData` | `orderId`, `orderNumber` |
| 5.5 | **Create Quote Revision** | Tạo phiên bản mới của báo giá | `quoteId`, `reason`, `changes` | `newQuoteId`, `revisionNumber` |
| 5.6 | **Quote Templates** | Tạo/sử dụng template báo giá | `workspaceId`, `templateData` | `templateId`, `success` |
| 5.7 | **Quote Analytics** | Phân tích hiệu quả báo giá | `workspaceId`, `period` | `totalQuotes`, `acceptanceRate`, `conversionRate` |

---

## 📄 MODULE 6: CONTRACT MANAGEMENT (7 functions)

| # | Function | Nhiệm vụ | Input | Output |
|---|----------|----------|-------|--------|
| 6.1 | **Create Contract** | Tạo hợp đồng bán hàng | `workspaceId`, `customerId`, `value`, `terms`, `signatories[]` | `contractId`, `contractNumber` |
| 6.2 | **Send for Signature** | Gửi hợp đồng để ký điện tử | `contractId`, `signatureProvider`, `message` | `envelopeId`, `signatureUrl` |
| 6.3 | **Sign Contract** | Ký hợp đồng (internal signature) | `contractId`, `signatoryId`, `signature`, `ipAddress` | `success`, `signedAt` |
| 6.4 | **Activate Contract** | Kích hoạt hợp đồng sau khi ký | `contractId`, `activationDate`, `notes` | `success`, `activeStatus` |
| 6.5 | **Renew Contract** | Gia hạn hợp đồng | `contractId`, `newEndDate`, `priceAdjustment` | `renewedContractId`, `success` |
| 6.6 | **Terminate Contract** | Chấm dứt hợp đồng trước hạn | `contractId`, `terminationDate`, `reason`, `penalty` | `success` |
| 6.7 | **Contract Compliance** | Theo dõi tuân thủ điều khoản | `contractId` | `complianceStatus`, `checks[]`, `milestones[]` |

---

## 💰 MODULE 7: PRICING & DISCOUNT (6 functions)

| # | Function | Nhiệm vụ | Input | Output |
|---|----------|----------|-------|--------|
| 7.1 | **Create Price List** | Tạo bảng giá cho segment khách hàng | `workspaceId`, `name`, `currency`, `applicableTo`, `priceRules[]` | `priceListId`, `success` |
| 7.2 | **Volume Pricing** | Tạo giá theo số lượng mua (tiers) | `workspaceId`, `productId`, `tiers[]` | `volumePricingId`, `success` |
| 7.3 | **Create Discount Campaign** | Tạo chiến dịch giảm giá | `workspaceId`, `name`, `type`, `value`, `conditions`, `period` | `campaignId`, `success` |
| 7.4 | **Generate Discount Code** | Tạo mã giảm giá | `workspaceId`, `campaignId`, `code`, `usageLimit`, `conditions` | `codeId`, `code` |
| 7.5 | **Apply Discount to Order** | Áp dụng discount vào đơn hàng | `orderId`, `discountCode` | `success`, `discountAmount`, `newTotal` |
| 7.6 | **Discount Performance** | Báo cáo hiệu quả chiến dịch giảm giá | `campaignId`, `period` | `totalOrders`, `revenue`, `totalDiscount`, `roi` |

---

## 🧾 MODULE 8: INVOICE & PAYMENT (6 functions)

| # | Function | Nhiệm vụ | Input | Output |
|---|----------|----------|-------|--------|
| 8.1 | **Create Invoice** | Tạo hóa đơn | `workspaceId`, `customerId`, `orderId`, `items[]`, `dueDate` | `invoiceId`, `invoiceNumber`, `pdfUrl` |
| 8.2 | **Record Payment** | Ghi nhận thanh toán | `invoiceId`, `amount`, `paymentMethod`, `reference`, `attachments[]` | `paymentId`, `success`, `paymentStatus` |
| 8.3 | **Send Invoice** | Gửi hóa đơn cho khách qua email | `invoiceId`, `recipientEmail`, `message`, `attachPDF` | `success`, `sentAt` |
| 8.4 | **Payment Reminder** | Gửi nhắc nở thanh toán | `invoiceId`, `reminderType`, `message` | `success` |
| 8.5 | **Accounts Receivable** | Báo cáo công nợ phải thu | `workspaceId`, `asOfDate`, `customerId` | `totalReceivable`, `aging[]`, `customers[]` |
| 8.6 | **Create Credit Note** | Tạo hóa đơn điều chỉnh/hoàn trả | `invoiceId`, `items[]`, `reason`, `refundMethod` | `creditNoteId`, `creditAmount` |

---

## 🔄 KEY FLOWS

### Flow 1: Product → Inventory → Order
```
1. Admin → Create Product (1.1) → Tạo sản phẩm với SKU, giá
2. Admin → Stock Adjustment (2.2) → Nhập hàng vào kho
3. Customer → Create Sales Order (3.1) → Tạo đơn hàng
4. System → Stock Reservation (2.5) → Giữ hàng cho đơn
5. Admin → Update Order Status (3.2) → Xác nhận đơn hàng
6. System → Deduct stock when shipped
```

### Flow 2: Quote → Order → Invoice → Payment
```
1. Sales → Create Quote (5.1) → Tạo báo giá
2. Sales → Send Quote (5.2) → Gửi cho khách hàng
3. Customer → Accept Quote (5.3) → Chấp nhận báo giá
4. Sales → Convert Quote to Order (5.4) → Tạo đơn hàng
5. System → Create Invoice (8.1) → Tạo hóa đơn tự động
6. Admin → Record Payment (8.2) → Ghi nhận thanh toán
7. System → Update order status to "completed"
```

### Flow 3: Opportunity → Quote → Contract → Order
```
1. Sales → Create Opportunity (4.1) → Tạo cơ hội bán hàng
2. Sales → Update Stage (4.2) → qualification → proposal → negotiation
3. Sales → Create Quote (5.1) → Gửi báo giá
4. Sales → Create Contract (6.1) → Tạo hợp đồng
5. Both Parties → Sign Contract (6.3) → Ký hợp đồng điện tử
6. Sales → Convert to Order (4.3) → Chuyển thành đơn hàng
7. System → Create Invoice (8.1) → Xuất hóa đơn
```

### Flow 4: Multi-warehouse Stock Transfer
```
1. Admin → Get Stock Level (2.4) → Kiểm tra tồn kho HN: 100, HCM: 10
2. Admin → Stock Transfer (2.3) → Chuyển 50 từ HN xuống HCM
3. System → Update status: in_transit
4. Warehouse HCM → Confirm received → Update status: delivered
5. System → Update stock: HN: 50, HCM: 60
```

### Flow 5: Discount Campaign → Order
```
1. Marketing → Create Discount Campaign (7.3) → Summer Sale 20%
2. Marketing → Generate Discount Code (7.4) → SUMMER2026
3. Customer → Create Sales Order (3.1) → Đơn hàng 100M
4. Customer → Apply Discount (7.5) → Nhập mã SUMMER2026
5. System → Validate code → Apply 20% discount → Total: 80M
6. System → Track usage count
```

### Flow 6: Stock Count & Adjustment
```
1. Admin → Create Stock Count (2.6) → Kiểm kê kho HN
2. Warehouse Staff → Count physical stock → Update counted quantities
3. System → Calculate variances → System: 100, Actual: 95
4. Admin → Approve stock count
5. System → Auto Stock Adjustment (2.2) → Điều chỉnh -5
6. System → Update inventory records
```

---

## ✅ CHECKLIST FIGMA

### ⬜ MODULE 1: PRODUCT MANAGEMENT (0%)
- ⬜ 1.1 Create Product - Dialog tạo sản phẩm với variants
- ⬜ 1.2 Update Product - Form cập nhật sản phẩm
- ⬜ 1.3 Delete Product - Dialog xác nhận xóa
- ⬜ 1.4 Get Product Details - UI chi tiết sản phẩm + stock
- ⬜ 1.5 List Products - Table danh sách với filters
- ⬜ 1.6 Manage Categories - Tree view categories
- ⬜ 1.7 Bulk Import Products - Upload Excel/CSV

### ⬜ MODULE 2: INVENTORY MANAGEMENT (0%)
- ⬜ 2.1 Create Warehouse - Dialog tạo kho
- ⬜ 2.2 Stock Adjustment - Form nhập/xuất kho
- ⬜ 2.3 Stock Transfer - UI chuyển kho với tracking
- ⬜ 2.4 Get Stock Level - Card hiển thị tồn kho
- ⬜ 2.5 Stock Reservation - UI giữ hàng
- ⬜ 2.6 Stock Count - UI kiểm kê với checklist
- ⬜ 2.7 Low Stock Alerts - Notification panel
- ⬜ 2.8 Inventory Valuation - Report với chart

### ⬜ MODULE 3: ORDER MANAGEMENT (0%)
- ⬜ 3.1 Create Sales Order - Form tạo đơn hàng
- ⬜ 3.2 Update Order Status - Dropdown status với timeline
- ⬜ 3.3 Get Order Details - UI chi tiết đơn đầy đủ
- ⬜ 3.4 List Orders - Table với filters & search
- ⬜ 3.5 Cancel Order - Dialog xác nhận hủy
- ⬜ 3.6 Add Order Note - Comment box
- ⬜ 3.7 Create Return Order - Dialog trả hàng
- ⬜ 3.8 Order Fulfillment - UI picking & packing
- ⬜ 3.9 Shipping Integration - UI tạo vận đơn

### ⬜ MODULE 4: SALES MANAGEMENT (0%)
- ⬜ 4.1 Create Opportunity - Dialog tạo opportunity
- ⬜ 4.2 Update Opportunity Stage - Kanban board
- ⬜ 4.3 Convert to Order - Dialog convert
- ⬜ 4.4 Sales Targets & KPIs - Dashboard với progress bars
- ⬜ 4.5 Commission Calculation - Table với breakdown
- ⬜ 4.6 Sales Analytics - Dashboard với charts

### ⬜ MODULE 5: QUOTE MANAGEMENT (0%)
- ⬜ 5.1 Create Quote - Form tạo báo giá
- ⬜ 5.2 Send Quote - Dialog gửi email
- ⬜ 5.3 Accept/Reject Quote - UI khách hàng phản hồi
- ⬜ 5.4 Convert Quote to Order - Dialog convert
- ⬜ 5.5 Create Quote Revision - UI version control
- ⬜ 5.6 Quote Templates - Template gallery
- ⬜ 5.7 Quote Analytics - Dashboard báo cáo

### ⬜ MODULE 6: CONTRACT MANAGEMENT (0%)
- ⬜ 6.1 Create Contract - Form tạo hợp đồng
- ⬜ 6.2 Send for Signature - Dialog e-signature
- ⬜ 6.3 Sign Contract - UI ký điện tử
- ⬜ 6.4 Activate Contract - Dialog kích hoạt
- ⬜ 6.5 Renew Contract - Dialog gia hạn
- ⬜ 6.6 Terminate Contract - Dialog chấm dứt
- ⬜ 6.7 Contract Compliance - Dashboard tuân thủ

### ⬜ MODULE 7: PRICING & DISCOUNT (0%)
- ⬜ 7.1 Create Price List - Form bảng giá
- ⬜ 7.2 Volume Pricing - UI tier pricing
- ⬜ 7.3 Create Discount Campaign - Form campaign
- ⬜ 7.4 Generate Discount Code - UI tạo mã giảm giá
- ⬜ 7.5 Apply Discount to Order - Input field + validation
- ⬜ 7.6 Discount Performance - Dashboard báo cáo

### ⬜ MODULE 8: INVOICE & PAYMENT (0%)
- ⬜ 8.1 Create Invoice - Form tạo hóa đơn
- ⬜ 8.2 Record Payment - Dialog ghi nhận thanh toán
- ⬜ 8.3 Send Invoice - Dialog gửi email
- ⬜ 8.4 Payment Reminder - UI nhắc nở
- ⬜ 8.5 Accounts Receivable - Table aging report
- ⬜ 8.6 Create Credit Note - Dialog hoàn trả

---

## 📊 PROGRESS SUMMARY

| Module | Functions | Status | Progress |
|--------|-----------|--------|----------|
| 1. Product Management | 7 | ⬜ Pending | 0% |
| 2. Inventory Management | 8 | ⬜ Pending | 0% |
| 3. Order Management | 9 | ⬜ Pending | 0% |
| 4. Sales Management | 6 | ⬜ Pending | 0% |
| 5. Quote Management | 7 | ⬜ Pending | 0% |
| 6. Contract Management | 7 | ⬜ Pending | 0% |
| 7. Pricing & Discount | 6 | ⬜ Pending | 0% |
| 8. Invoice & Payment | 6 | ⬜ Pending | 0% |
| **TOTAL** | **56** | **⬜ PENDING** | **0%** |

---

## 🎯 NOTES

### Tech Stack Details:
- **Backend:** ASP.NET Core 8.0
- **ORM:** Entity Framework Core 8.0
- **Database:** PostgreSQL 15+ (`sales_db`)
- **Cache:** Redis 7+ (product catalog, stock levels)
- **Message Queue:** RabbitMQ (order processing, stock updates)
- **Payment Gateway:** VNPay, Momo, Stripe integration
- **Shipping APIs:** GHN, Viettel Post, Grab Express, Ninja Van
- **E-signature:** DocuSign, Adobe Sign integration

### Business Rules:
- ✅ SKU phải unique trong workspace
- ✅ Stock reservation tự động khi đơn hàng confirmed
- ✅ Deduct stock khi đơn shipped
- ✅ Order status flow: draft → confirmed → processing → shipped → delivered → completed
- ✅ Cannot cancel order if status = "shipped" or later
- ✅ Auto apply volume pricing based on quantity
- ✅ Discount code validation: active, not expired, usage limit
- ✅ Invoice auto-generated when order completed
- ✅ Payment reminder auto send 3 days before due date
- ✅ Batch/Serial number tracking cho products có expiry date

### Database Schema (Estimated 25 tables):
- **Products** table + **ProductVariants**
- **Categories** table (nested)
- **Warehouses** table
- **InventoryLevels** table
- **StockAdjustments** + **StockAdjustmentItems**
- **StockTransfers** + **StockTransferItems**
- **Orders** + **OrderItems** + **OrderStatusHistory**
- **Quotes** + **QuoteItems** + **QuoteRevisions**
- **Contracts** + **ContractItems** + **ContractSignatures**
- **PriceLists** + **PriceListRules**
- **VolumePricing** + **VolumePricingTiers**
- **DiscountCampaigns** + **DiscountCodes**
- **Invoices** + **InvoiceItems** + **Payments**
- **CreditNotes** + **CreditNoteItems**
- **SalesOpportunities**
- **SalesTargets**
- **Commissions**

### Integration Points:
- **IAM Service:** Workspace, User permissions, RBAC
- **CRM Service:** Customers, Deals, Contacts → Link to orders/quotes
- **Call Service:** Call notes → Order notes, Call → Create order
- **Chat Service:** Chat orders, Chat → Create quote
- **Marketing Service:** Campaigns → Discount codes, Lead → Opportunity
- **Analytics Service:** Sales reports, Revenue analytics, Product performance
- **Notification Service:** Order status updates, Payment reminders, Low stock alerts

### Key Features:
- ✅ Multi-variant products (SKU per variant)
- ✅ Multi-warehouse inventory tracking
- ✅ Batch/Serial number tracking
- ✅ Stock reservation system
- ✅ FIFO/LIFO inventory valuation
- ✅ Quote → Order → Invoice flow
- ✅ E-signature for contracts
- ✅ Volume pricing & tiered discounts
- ✅ Payment tracking (partial payments)
- ✅ Accounts receivable aging report
- ✅ Sales pipeline & opportunity management
- ✅ Commission calculation (tiered)
- ✅ Shipping provider integration
- ✅ Return order processing

### Order Status Flow:
```
draft → confirmed → processing → shipped → delivered → completed
  ↓         ↓           ↓           ↓
cancelled  on_hold   on_hold    returned
```

### Quote Status Flow:
```
draft → sent → viewed → accepted → converted_to_order
          ↓      ↓         ↓
      expired rejected  expired
```

### Contract Status Flow:
```
draft → pending_signature → signed → active → completed
  ↓                            ↓        ↓
cancelled                  rejected  expired
```

### Stock Transfer Status Flow:
```
draft → pending → in_transit → delivered → completed
                      ↓
                  cancelled
```

---

**Last Updated:** February 4, 2026  
**Version:** 1.0.0  
**Author:** NextX Product Team
