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


# Order Parties and Contact Mechanism

## Sales Order Parties and Contact Mechanisms 

```mysql
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


## Purchase Order Parties and Contact Mechanisms

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
	-- Party Role Subtype: ship to buyer, placing buyer, supplier, bill to purchaser
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


## Generic Order Roles and Contact Mechanism 

Combine both sales order and purchase order.

```mysql
CREATE TABLE IF NOT EXISTS order (
	id BINARY(16),
	order_date DATE NOT NULL DEFAULT CURRENT_DATE,
	entry_date DATE NOT NULL DEFAULT CURRENT_DATE,
	subtype ENUM ('sales', 'purchase') NOT NULL,
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;


CREATE TABLE IF NOT EXISTS order_item (
	order_id BINARY(16) NOT NULL,
	order_item_seq_id UNSIGNED INT NOT NULL DEFAULT 1,
	quantity INT NOT NULL DEFAULT 0,
	unit_price DECIMAL(13,4) NOT NULL DEFAULT 0,
	shipping_instructions VARCHAR(255) NOT NULL DEFAULT '',
	estimated_delivery_date DATE NOT NULL DEFAULT CURRENT_DATE,
	item_description VARCHAR(255) NOT NULL DEFAULT '',
	subtype ENUM ('sales', 'purchase') NOT NULL,
	PRIMARY KEY (order_item_seq_id, order_id),
	FOREIGN KEY (order_id) REFERENCES order(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS order_item_role (
	order_item_role_type_id VARCHAR(255),
	order_item_id BINARY(16) NOT NULL,
	party_id BINARY(16) NOT NULL,
	PRIMARY KEY (order_item_id, order_item_role_type_id, party_id),
	FOREIGN KEY (order_item_role_type_id) REFERENCES order_item_role_type (id),
	FOREIGN KEY (order_item_id) REFERENCES order_item(id),
	FOREIGN KEY (party_id) REFERENCES party(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;


CREATE TABLE IF NOT EXISTS order_item_contact_mechanism (
 	order_id BINARY(16) NOT NULL,
 	contact_mechanism_id BINARY(16) NOT NULL,
	contact_mechanism_purpose_type_id VARCHAR(255) NOT NULL,
	PRIMARY KEY (order_id, contact_mechanism_id, contact_mechanism_purpose_type_id),
	FOREIGN KEY (order_id) REFERENCES order(id),
	FOREIGN KEY (contact_mechanism_id) REFERENCES contact_mechanism(id),
	FOREIGN KEY (contact_mechanism_purpose_type_id) REFERENCES contact_mechanism_purpose_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;


CREATE TABLE IF NOT EXISTS contact_mechanism (
 	id BINARY(16) NOT NULL,
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;


CREATE TABLE IF NOT EXISTS contact_mechanism_purpose_type (
	id VARCHAR(255),
	description VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS order_contact_mechanism (
	order_id BINARY(16),
	contact_mechanism_id BINARY(16) NOT NULL,
	contact_mechanism_purpose_type_id VARCHAR(255) NOT NULL,
	PRIMARY KEY (order_id, contact_mechanism_id, contact_mechanism_purpose_type_id),
	FOREIGN KEY (order_id) REFERENCES order(id),
	FOREIGN KEY (contact_mechanism_id) REFERENCES contact_mechanism(id),
	FOREIGN KEY (contact_mechanism_purpose_type_id) REFERENCES contact_mechanism_purpose_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS order_item_role_type (
	id VARCHAR(255),
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS order_role_type (
	id VARCHAR(255),
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS party (
	id BINARY(16),
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS order_role (
	party_id BINARY(16) NOT NULL,
	order_id BINARY(16) NOT NULL,
	order_role_type_id BINARY(16) NOT NULL,
	PRIMARY KEY (party_id, order_id, order_role_type_id),
	FOREIGN KEY (party_id) REFERENCES party(id),
	FOREIGN KEY (order_id) REFERENCES order(id),
	FOREIGN KEY (order_role_type_id) REFERENCES order_role_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;
```

## Order Adjustment 

```mysql
CREATE TABLE IF NOT EXISTS order (
	id BINARY(16),
	order_date DATE NOT NULL DEFAULT CURRENT_DATE,
	entry_date DATE NOT NULL DEFAULT CURRENT_DATE,
	subtype ENUM ('sales', 'purchase') NOT NULL,
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS order_item (
	order_id BINARY(16) NOT NULL,
	order_item_seq_id UNSIGNED INT NOT NULL DEFAULT 1,
	quantity INT NOT NULL DEFAULT 0,
	unit_price DECIMAL(13,4) NOT NULL DEFAULT 0,
	shipping_instructions VARCHAR(255) NOT NULL DEFAULT '',
	estimated_delivery_date DATE NOT NULL DEFAULT CURRENT_DATE,
	item_description VARCHAR(255) NOT NULL DEFAULT '',
	subtype ENUM ('sales', 'purchase') NOT NULL,
	PRIMARY KEY (order_item_seq_id, order_id),
	FOREIGN KEY (order_id) REFERENCES order(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS order_adjustment (
	-- NOTE: The id is XOR order_item_id or order_id.
	id BINARY(16),
	amount DECIMAL(13,4) NOT NULL DEFAULT 0,
	percentage DECIMAL(5,4) NOT NULL DEFAULT 0,
	order_adjustment_type_id VARCHAR(255) NOT NULL,
	PRIMARY KEY (id),
	FOREIGN KEY (order_adjustment_type_id) REFERENCES order_adjustment_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS order_adjustment_type (
	id VARCHAR(255),
	description VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

INSERT INTO order_adjustment_type (id) VALUES
('miscelleanous_charge'),
('sales_tax'),
('discount_adjustment'),
('shipping_and_handling_charge'),
('surcharge_adjustment'),
('fee');

CREATE TABLE IF NOT EXISTS geographic_boundary (
	id BINARY(16),
	geo_code VARCHAR(255) NOT NULL DEFAULT '',
	name VARCHAR(255) NOT NULL DEFAULT '',
	abbreviation VARCHAR(255) NOT NULL DEFAULT '',
	subtype ENUM('country', 'city', 'state') NOT NULL,
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS product_category (
	id BINARY(16),
	description VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS sales_tax_lookup (
	sales_tax_seq_id UNSIGNED INT,
	sales_tax_percentage DECIMAL(5,4) NOT NULL DEFAULT 0,
	geographic_boundary_id BINARY(16) NOT NULL DEFAULT x'',
	product_category_id BINARY(16) NOT NULL DEFAULT x'',
	PRIMARY KEY (sales_tax_seq_id, geographic_boundary_id),
	FOREIGN KEY (geographic_boundary_id) REFERENCES geographic_boundary(id)
	FOREIGN KEY (product_category_id) REFERENCES product_category(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;
```

## Order Status

```mysql
CREATE TABLE IF NOT EXISTS order (
	id BINARY(16),
	order_date DATE NOT NULL DEFAULT CURRENT_DATE,
	entry_date DATE NOT NULL DEFAULT CURRENT_DATE,
	subtype ENUM ('sales', 'purchase') NOT NULL,
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS order_item (
	order_id BINARY(16) NOT NULL,
	order_item_seq_id UNSIGNED INT NOT NULL DEFAULT 1,
	quantity INT NOT NULL DEFAULT 0,
	unit_price DECIMAL(13,4) NOT NULL DEFAULT 0,
	shipping_instructions VARCHAR(255) NOT NULL DEFAULT '',
	estimated_delivery_date DATE NOT NULL DEFAULT CURRENT_DATE,
	item_description VARCHAR(255) NOT NULL DEFAULT '',
	subtype ENUM ('sales', 'purchase') NOT NULL,
	PRIMARY KEY (order_item_seq_id, order_id),
	FOREIGN KEY (order_id) REFERENCES order(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS term_type (
	id BINARY(16),
	description VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id)	
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS order_term (
	id BINARY(16),
	term_value VARCHAR(255) NOT NULL DEFAULT '',
	term_type_id VARCHAR(255) NOT NULL DEFAULT '',
	subtype ENUM('order', 'order_item') NOT NULL,
	PRIMARY KEY (id),
	FOREIGN KEY (term_type_id) REFERENCES term_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS order_status_type (
	id VARCHAR(255),
	description VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

INSERT INTO order_status_type (id) VALUES 
('received'),
('approved'),
('cancelled');

CREATE TABLE IF NOT EXISTS order_status (
	id BINARY(16),
	order_status_type_id VARCHAR(255) NOT NULL DEFAULT '',
	subtype ENUM ('order', 'order_item') NOT NULL,
	status_datetime DATE NOT NULL DEFAULT CURRENT_DATE,
	PRIMARY KEY (id),
	FOREIGN KEY (order_status_type) REFERENCES order_status_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;
```

## Order Item Association
## Requirements
## Requests
## Quote
## Agreement

