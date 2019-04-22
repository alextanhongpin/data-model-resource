## Order and Order Items

```mysql

-- NOTE: Create an empty product so that we don't have to resort to NULL.
CREATE TABLE IF NOT EXISTS product (
	id BINARY(16),
	subtype ENUM('good', 'service') NOT NULL,
	name VARCHAR(255) NOT NULL DEFAULT '',
	introduction_date DATE NOT NULL DEFAULT CURRENT_DATE,
	sales_discontinuation_date DATE NOT NULL DEFAULT '9999-12-31',
	support_discontinuation_date DATE NOT NULL DEFAULT '9999-12-31',
	comment VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS order_item (
	order_item_seq_id UNSIGNED INT NOT NULL DEFAULT 1,
	order_id BINARY(16) NOT NULL,
	-- NOTE: Create an empty product so that we don't have to resort to NULL.
	product_id BINARY(16) NOT NULL DEFAULT x'', -- Can be null?
	product_feature_id VARCHAR(255) NOT NULL DEFAULT '', -- Can be null?
	quantity TINYINT NOT NULL DEFAULT 0,
	unit_price DECIMAL(13,4) NOT NULL DEFAULT 0,
	estimated_delivery_date DATE NOT NULL DEFAULT '1000-01-01',
	shipping_instructions VARCHAR(255) NOT NULL DEFAULT '',
	item_description VARCHAR(255) NOT NULL DEFAULT '',
	comment VARCHAR(255) NOT NULL DEFAULT '',
	-- purchase_order_item, sales_order_item
	subtype ENUM ('purchase', 'sales'),
	PRIMARY KEY (order_id, order_item_seq_id),
	FOREIGN KEY (order_id) REFERENCES order(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS order (
	id BINARY(16),
	order_date DATE NOT NULL DEFAULT CURRENT_DATE,
	entry_date DATE NOT NULL DEFAULT CURRENT_DATE,
	-- sales_order and purchase_order.
	subtype ENUM ('purchase', 'sales') NOT NULL, 
	FOREIGN KEY (order_id) REFERENCES order(id),
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;


-- NOTE: For each table, ensure there's a corresponding NULL id so that we don't have to have a FOREIGN KEY references that are NULL.
CREATE TABLE IF NOT EXISTS product_feature (
	id VARCHAR(255) NOT NULL,
	description VARCHAR(255) NOT NULL DEFAULT ''
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;
```


## Order Parties and Contact Mechanism
## Sales Order Parties and Contact Mechanisms 
## Purchase Order Parties and Contact Mechanisms
## Order Adjustment 
## Order Status
## Order Item Association
## Requirements
## Requests
## Quote
## Agreement

