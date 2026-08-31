# 🛍️ Lab 8: Table Relationship 1-to-1 — Product Shop

**วิชา:** CP353002 Principles of Software Design  
**เรื่อง:** ความสัมพันธ์ตาราง 1-to-1 ด้วย Spring Boot + JPA + PostgreSQL  
**รูปแบบ:** ทำเดี่ยว

---

## 📋 วัตถุประสงค์

1. นักศึกษาเข้าใจความสัมพันธ์แบบ **One-to-One (1:1)** ระหว่างตารางในฐานข้อมูล
2. นักศึกษาสามารถสร้าง Entity ที่มี `@OneToOne` annotation ได้ถูกต้อง
3. นักศึกษาเข้าใจการใช้ `@JoinColumn` และ `CascadeType` ในการจัดการความสัมพันธ์
4. นักศึกษาสามารถทำ CRUD ที่ครอบคลุมข้อมูลจากทั้งสองตารางได้
5. นักศึกษายังคงปฏิบัติตามหลักการ GRASP, SOLID และ Layered Architecture จาก Lab 7

---

## 🎯 โจทย์

ต่อยอดจาก **Product Shop** ใน Lab 7 โดยเพิ่มตาราง **ProductDetail** ที่มีความสัมพันธ์แบบ **1-to-1** กับตาราง **Product**

```
Product (1) ──────── (1) ProductDetail
  id                       id
  name                     description
  category                 warranty
  brand                    weight
  stock                    dimensions
  price                    manufacturedCountry
  discountType
```

> ✅ Product 1 รายการ มี ProductDetail ได้เพียง 1 รายการเท่านั้น  
> ✅ ProductDetail 1 รายการ เป็นของ Product เพียง 1 รายการเท่านั้น

**สิ่งที่ให้:**
- ไฟล์ Thymeleaf Templates ครบ 4 หน้า (รองรับข้อมูลจากทั้งสองตาราง)
- ไฟล์ CSS (`style.css`)
- ไฟล์ `pom.xml` พร้อมใช้งาน

**สิ่งที่นักศึกษาต้องทำเอง:**
- สร้าง Entity `Product.java` และ `ProductDetail.java` พร้อม `@OneToOne`
- สร้าง `ProductRepository.java` และ `ProductDetailRepository.java`
- สร้าง Strategy Package (เหมือน Lab 7)
- สร้าง `ProductService.java` ที่จัดการทั้งสองตาราง
- สร้าง `ProductController.java` ที่ทำ CRUD ครบทุกฟังก์ชัน
- ตั้งค่า Database ใน `application.properties`

### 📛 การตั้งชื่อโปรเจค

```
lab8-{รหัสนักศึกษา}-sec{หมายเลข section}
```

| รหัสนักศึกษา | Section | ชื่อโปรเจค |
|---|---|---|
| 673380001-1 | 1 | `lab8-673380001-1-sec1` |
| 673380123-4 | 2 | `lab8-673380123-4-sec2` |

---

## 📦 Entity Fields ที่ต้องสร้าง

### Product.java

| Field | Type | Annotation | คำอธิบาย |
|---|---|---|---|
| `id` | Long | `@Id @GeneratedValue` | Primary Key |
| `name` | String | `@Column` | ชื่อสินค้า |
| `category` | String | `@Column` | หมวดหมู่ |
| `brand` | String | `@Column` | ยี่ห้อ |
| `stock` | Integer | `@Column` | จำนวนในคลัง |
| `price` | Double | `@Column` | ราคาปกติ |
| `discountType` | String | `@Column` | ประเภทส่วนลด |
| `detail` | ProductDetail | `@OneToOne(cascade = CascadeType.ALL)` | ความสัมพันธ์ 1:1 |

### ProductDetail.java

| Field | Type | Annotation | คำอธิบาย |
|---|---|---|---|
| `id` | Long | `@Id @GeneratedValue` | Primary Key |
| `description` | String | `@Column` | รายละเอียดสินค้า |
| `warranty` | String | `@Column` | ระยะเวลารับประกัน เช่น `1 Year` |
| `weight` | Double | `@Column` | น้ำหนัก (กิโลกรัม) |
| `dimensions` | String | `@Column` | ขนาด เช่น `15x7x0.8 cm` |
| `manufacturedCountry` | String | `@Column` | ประเทศที่ผลิต |
| `product` | Product | `@OneToOne(mappedBy = "detail")` | ด้าน inverse ของความสัมพันธ์ |

### ตัวอย่าง Code Annotation ใน Entity

```java
// Product.java
@Entity
@Table(name = "products")
public class Product {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    // ... fields อื่นๆ ...

    @OneToOne(cascade = CascadeType.ALL)
    @JoinColumn(name = "detail_id", referencedColumnName = "id")
    private ProductDetail detail;
}

// ProductDetail.java
@Entity
@Table(name = "product_details")
public class ProductDetail {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    // ... fields อื่นๆ ...

    @OneToOne(mappedBy = "detail")
    private Product product;
}
```

---

## 📂 โครงสร้างโปรเจค ← ❌ นักศึกษาต้องสร้างเอง

```
src/main/java/com/example/demo/
├── DemoApplication.java
├── model/
│   ├── Product.java              ← ❌ มี @OneToOne → ProductDetail
│   └── ProductDetail.java        ← ❌ มี @OneToOne(mappedBy)
├── repository/
│   ├── ProductRepository.java    ← ❌ นักศึกษาสร้างเอง
│   └── ProductDetailRepository.java ← ❌ นักศึกษาสร้างเอง
├── strategy/
│   ├── DiscountStrategy.java     (interface)
│   ├── NoDiscountStrategy.java
│   ├── MemberDiscountStrategy.java   (ลด 10%)
│   ├── SeasonalSaleStrategy.java     (ลด 20%)
│   └── DiscountContext.java
├── service/
│   └── ProductService.java       ← ❌ จัดการทั้งสองตาราง
└── controller/
    └── ProductController.java    ← ❌ CRUD ครบทุกฟังก์ชัน

src/main/resources/
├── application.properties
├── static/css/
│   └── style.css                 ← ✅ มีให้แล้ว
└── templates/products/
    ├── list.html                 ← ✅ มีให้แล้ว
    ├── add.html                  ← ✅ มีให้แล้ว (มีฟอร์ม ProductDetail ด้วย)
    ├── edit.html                 ← ✅ มีให้แล้ว
    └── delete.html               ← ✅ มีให้แล้ว
```

---

## 🗄️ Database Setup

### ติดตั้ง PostgreSQL

**Windows:** ดาวน์โหลดจาก https://www.postgresql.org/download/windows/ → รัน `.exe` → กำหนด password สำหรับ `postgres`

**macOS:**
```bash
brew install postgresql@16
brew services start postgresql@16
```

### สร้าง Database

```bash
psql -U postgres
CREATE DATABASE lab8shop;
\q
```

### ตั้งค่า `application.properties`

```properties
spring.application.name=demo

spring.datasource.url=jdbc:postgresql://localhost:5432/lab8shop
spring.datasource.username=postgres
spring.datasource.password=YOUR_PASSWORD_HERE

spring.jpa.show-sql=true
spring.jpa.hibernate.ddl-auto=update
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.PostgreSQLDialect
```

> ✅ Spring JPA จะสร้างตาราง `products` และ `product_details` ให้อัตโนมัติ  
> ✅ Foreign key `detail_id` ใน `products` จะถูกสร้างอัตโนมัติจาก `@JoinColumn`

---

## 📝 สิ่งที่ต้องส่ง

1. **ลิ้งค์ Git Repository** — Push โปรเจคที่ทำเสร็จขึ้น GitHub

2. **ไฟล์ PDF เล่มรายงาน** แบ่งเป็น 3 ส่วน:

**ส่วนที่ 1: Software Design & Relationship Explanation**
- 🔗 **อธิบายความสัมพันธ์ 1:1** ระหว่าง `Product` และ `ProductDetail`  
  อธิบายว่าทำไมถึงแยกตารางแทนที่จะรวมไว้ตารางเดียว (High Cohesion, SRP)
- 🧩 **Strategy Pattern** — เหมือน Lab 7
- 🏗️ **Layered Architecture** — Entity, Repository, Service, Controller
- 🔄 **Execution Flow** — HTTP Request → Controller → Service → Repository → DB

**ส่วนที่ 2: Code Implementation & Explanation**
- Code ของ `Product.java` และ `ProductDetail.java` พร้อมอธิบาย `@OneToOne`, `@JoinColumn`, `CascadeType`
- Code ของ Service และ Controller ที่จัดการข้อมูลจากทั้งสองตาราง

**ส่วนที่ 3: Web Application & Database Screenshots**

> 📌 ใส่รหัสนักศึกษาและ Section ลงในชื่อสินค้าด้วย

**ตัวอย่างการกรอกข้อมูล:**

| Field | ตัวอย่าง |
|---|---|
| ชื่อสินค้า (Name) | `MacBook Pro 14 (673380123-4 SEC 1)` |
| หมวดหมู่ (Category) | `Electronics` |
| ยี่ห้อ (Brand) | `Apple` |
| จำนวนในคลัง (Stock) | `20` |
| ราคาปกติ (Price) | `69,900.00` |
| รายละเอียด (Description) | `Apple M3 Pro chip, 18GB RAM, 512GB SSD` |
| รับประกัน (Warranty) | `1 Year` |
| น้ำหนัก (Weight) | `1.61 kg` |
| ขนาด (Dimensions) | `31.26 x 22.12 x 1.55 cm` |
| ผลิตที่ (Manufactured Country) | `China` |

**ภาพที่ต้องถ่าย:**
- หน้าจอเพิ่มสินค้า (Create) — กรอกข้อมูลทั้ง Product และ ProductDetail
- หน้าจอรายการสินค้า (Read) — แสดงข้อมูลครบทั้งสองตาราง
- หน้าจอแก้ไขสินค้า (Update)
- หน้าจอยืนยันลบ + ผลหลังลบ (Delete)
- หน้าจอ pgAdmin — แสดงตาราง `products` และ `product_details` พร้อม foreign key

---

## 📊 เกณฑ์การให้คะแนน

| หัวข้อ | คะแนน | รายละเอียด |
|---|---|---|
| **1:1 Relationship Design** | 25% | สร้าง `@OneToOne`, `@JoinColumn`, `CascadeType` ถูกต้องทั้งสอง Entity |
| **Strategy Pattern** | 10% | ใช้ Strategy Pattern ในการคำนวณส่วนลดได้ถูกต้อง |
| **Repository & Service** | 15% | จัดการข้อมูลจากทั้งสองตารางใน Service Layer ได้ถูกต้อง |
| **Controller & CRUD** | 15% | CRUD ครบทุกฟังก์ชัน ใช้ Constructor Injection |
| **Database** | 15% | ตาราง `products` และ `product_details` ถูกสร้างใน DB พร้อม foreign key |
| **PDF Report** | 20% | อธิบายความสัมพันธ์ 1:1, Annotation และ Execution Flow ครบถ้วน |
| **รวม** | **100%** | |

---

> **หมายเหตุ:** นักศึกษาสามารถปรับแต่งหน้าเว็บเพิ่มเติมได้ แต่ความสัมพันธ์ 1:1 และหลักการออกแบบต้องถูกต้อง
