# create_dataset
sql ve pl sql ile dataset qurulmasi
[create_tables.sql](https://github.com/user-attachments/files/22027206/create_tables.sql)
-- 1. Categories
CREATE TABLE Categories (
  category_id     NUMBER PRIMARY KEY,
  name            VARCHAR2(100) NOT NULL UNIQUE
);

-- 2. Stores
CREATE TABLE Stores (
  store_id        NUMBER PRIMARY KEY,
  name            VARCHAR2(100) NOT NULL,
  city            VARCHAR2(100) NOT NULL,
  address         VARCHAR2(200) NOT NULL,
  phone           VARCHAR(20)   NOT NULL
);

-- 3. Products
CREATE TABLE Products (
  product_id      NUMBER PRIMARY KEY,
  name            VARCHAR2(200) NOT NULL,
  category_id     NUMBER NOT NULL,
  brand           VARCHAR2(100) NOT NULL,
  price           NUMBER(10,2) NOT NULL CHECK (price >= 0),
  stock_quantity  NUMBER DEFAULT 0 NOT NULL CHECK (stock_quantity >= 0),
  FOREIGN KEY (category_id) REFERENCES Categories(category_id)
);

-- 4. Customers
CREATE TABLE Customers (
  customer_id     NUMBER PRIMARY KEY,
  first_name      VARCHAR2(100) NOT NULL,
  last_name       VARCHAR2(100) NOT NULL,
  email           VARCHAR2(200) NOT NULL UNIQUE,
  phone           VARCHAR2(20),
  street          VARCHAR2(50),
  city            VARCHAR2(50),
  created_at      DATE DEFAULT SYSDATE NOT NULL
); 

-- 5. Employees
CREATE TABLE Employees (
  employee_id     NUMBER PRIMARY KEY,
  first_name      VARCHAR2(100) NOT NULL,
  last_name       VARCHAR2(100) NOT NULL,
  email           VARCHAR2(200) NOT NULL UNIQUE,
  phone           VARCHAR2(20),
  store_id        NUMBER NOT NULL,
  hire_date       DATE DEFAULT SYSDATE NOT NULL,
  FOREIGN KEY (store_id) REFERENCES Stores(store_id),
  status          VARCHAR2(20));

-- 6. Orders
CREATE TABLE Orderss (
  order_id        NUMBER PRIMARY KEY,
  customer_id     NUMBER NOT NULL,
  store_id        NUMBER NOT NULL,
  employee_id     NUMBER,
  order_date      DATE DEFAULT SYSDATE NOT NULL,
  status          VARCHAR2(50) NOT NULL CHECK (status IN ('Pending','Processing','Completed','Cancelled')),
  FOREIGN KEY (customer_id) REFERENCES Customers(customer_id),
  FOREIGN KEY (store_id)     REFERENCES Stores(store_id),
  FOREIGN KEY (employee_id)  REFERENCES Employees(employee_id)
);

-- 7. OrderItems (junction)
CREATE TABLE OrderItems (
  order_id        NUMBER NOT NULL,
  product_id      NUMBER NOT NULL,
  quantity        NUMBER NOT NULL CHECK (quantity > 0),
  unit_price      NUMBER(10,2) NOT NULL CHECK (unit_price >= 0),
  PRIMARY KEY (order_id, product_id),
  FOREIGN KEY (order_id)   REFERENCES Orderss(order_id),
  FOREIGN KEY (product_id) REFERENCES Products(product_id)
);

-- 8. Payments
CREATE TABLE Payments (
  payment_id      NUMBER PRIMARY KEY,
  order_id        NUMBER NOT NULL UNIQUE,
  amount          NUMBER(10,2) NOT NULL CHECK (amount >= 0),
  payment_date    DATE DEFAULT SYSDATE NOT NULL,
  method          VARCHAR2(50) NOT NULL CHECK (method IN ('Cash','Card','Online','Credit','Other')),
  FOREIGN KEY (order_id) REFERENCES Orderss(order_id),
  status          VARCHAR2(20) CHECK (status IN ('PENDING','COMPLETED','FAILED')));
