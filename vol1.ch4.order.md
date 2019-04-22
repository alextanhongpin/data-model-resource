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

CREATE TABLE IF NOT EXISTS contact_mechanism (
	id BINARY(16),
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS order_item (
	order_item_seq_id UNSIGNED INT NOT NULL DEFAULT 1,
	quantity TINYINT NOT NULL DEFAULT 0,
	unit_price DECIMAL(13,4) NOT NULL DEFAULT 0,
	estimated_delivery_date DATE NOT NULL DEFAULT '1000-01-01',
	shipping_instructions VARCHAR(255) NOT NULL DEFAULT '',
	comment VARCHAR(255) NOT NULL DEFAULT '',
	order_id BINARY(16) NOT NULL,
	PRIMARY KEY (order_item_seq_id, order_id),
	FOREIGN KEY (order_id) REFERENCES order(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS order (
	id BINARY(16),
	order_date DATE NOT NULL DEFAULT CURRENT_DATE,
	entry_date DATE NOT NULL DEFAULT CURRENT_DATE,
	subtype ENUM ('sales', 'purchase') NOT NULL,
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS party_role (
	party_id BINARY(16) NOT NULL,
	-- Party Role Subtype: ship to customer, placing customer, internal organization, bill to customer.
	FOREIGN KEY (party_id) REFERENCES party(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS party (
	id BINARY(16),
	subtype ENUM('person', 'organization') NOT NULL,
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS order_role (
	order_id BINARY(16) NOT NULL, 
	party_id BINARY(16) NOT NULL,
	order_role_type_id BINARY(16) NOT NULL,
	percent_contribution DECIMAL(5, 4) NOT NULL DEFAULT 0,
	PRIMARY KEY (order_id, party_id, order_role_type_id),
	FOREIGN KEY (order) REFERENCES order(id),
	FOREIGN KEY (party) REFERENCES party(id),
	FOREIGN KEY (order_role_type) REFERENCES order_role_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;
```

## Sales Order Parties and Contact Mechanisms 
## Purchase Order Parties and Contact Mechanisms
## Order Adjustment 
## Order Status
## Order Item Association
## Requirements
## Requests
## Quote
## Agreement

