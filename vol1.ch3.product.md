## Product

```mysql
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

CREATE TABLE IF NOT EXISTS product_category_classification (
	product_id BINARY(16) NOT NULL,
	product_category_id VARCHAR(255) NOT NULL,
	from_date DATE NOT NULL DEFAULT CURRENT_DATE,
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	primary_flag tinyint(1) NOT NULL DEFAULT 0,
	comment VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (product_id, product_category_id, from_date),
	FOREIGN KEY (product_id) REFERENCES product(id),
	FOREIGN KEY (product_category_id) REFERENCES product_category(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS product_category (
	id VARCHAR(255),
	description VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS product_category_rollup (
	product_category_id VARCHAR(255) NOT NULL,
	FOREIGN KEY (product_category_id) REFERENCES product_category(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS market_interest (
	from_date DATE NOT NULL DEFAULT CURRENT_DATE,
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	product_category_id VARCHAR(255) NOT NULL,
	party_type_id VARCHAR(255) NOT NULL,
	PRIMARY KEY (from_date, product_category_id, party_type_id)
	FOREIGN KEY (party_type_id) REFERENCES party_type(id),
	FOREIGN KEY (product_category_id) REFERENCES product_category(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS party_type (
	id VARCHAR(255) NOT NULL,
	description VARCHAR(255),
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;
```


## Product Identification Code

```mysql
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

CREATE TABLE IF NOT EXISTS good_identification (
	id BINARY(16),
	product_id BINARY(16) NOT NULL,
	identification_type_id VARCHAR(255) NOT NULL,
	FOREIGN KEY (product_id) REFERENCES product(id),
	FOREIGN KEY (identification_type_id) REFERENCES identification_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

-- NOTE: Find a way to constraint the primary key length, use a shorter code rather than varchar(255).
CREATE TABLE IF NOT EXISTS identification_type (
	id VARCHAR(255),
	description VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

INSERT INTO identification_type (id) VALUES 
('manufacturer_id_no'),
('upca'),
('sku'),
('isbn'),
('upce'),
('other_id');
```


## Product Feature

```mysql
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

CREATE TABLE IF NOT EXISTS product_feature_category (
	id VARCHAR(255),
	description VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

INSERT INTO product_feature_category (id) VALUES 
('product_quality'),
('color'),
('dimension'),
('size'),
('brand'),
('software_feature'),
('hardware_feature'),
('billing_feature'),
('other_feature');


CREATE TABLE IF NOT EXISTS product_feature (
	id VARCHAR(255),
	description VARCHAR(255) NOT NULL DEFAULT '',
	product_feature_category_id VARCHAR(255) NOT NULL,
	value VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id),
	FOREIGN KEY (product_feature_category_id) REFERENCES product_feature_category(id),
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

/* TODO: Clarify table (?).
CREATE TABLE IF NOT EXISTS product_feature_interaction (
	feature_interaction_incompatibility 
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;
*/

CREATE TABLE IF NOT EXISTS product_feature_applicability (
	from_date DATE NOT NULL CURRENT_DATE,
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	product_feature_id VARCHAR(255) NOT NULL,
	product_id BINARY(16) NOT NULL,
	FOREIGN KEY (product_id) REFERENCES product(id),
	FOREIGN KEY (product_feature_id) REFERENCES product_feature(id)
	PRIMARY KEY (product_id, product_feature_id, from_date)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;
```


## Suppliers and manufacturers of products
SKIP
## Inventory item storage
SKIP

## Product Pricing
```mysql
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

CREATE TABLE IF NOT EXISTS product_feature (
	id VARCHAR(255),
	description VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id)
);
CREATE TABLE IF NOT EXISTS organization (
	party_id BINARY(16),
	name VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (party_id),
	FOREIGN KEY (party_id) REFERENCES party(id) 
);

CREATE TABLE IF NOT EXISTS price_component (
	id BINARY (16),
	product_id BINARY(16) NOT NULL,
	product_feature_id BINARY(16) NOT NULL,
	
	-- Variables...
	price_component_type_id -- Refer to the price component type...
	geographic_boundary
	party_type
	product_category
	quantity_break
	order_value
	sale_type
	unit_of_measure
	
	organization_id BINARY(16) NOT NULL,
	from_date DATE NOT NULL DEFAULT CURRENT_DATE,
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	price DECIMAL(13,4) NOT NULL DEFAULT 0,
	percent DECIMAL(5,4) NOT NULL DEFAULT 0,
	comment VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id)
);

CREATE TABLE IF NOT EXISTS price_component_type (
	id VARCHAR(255),
	description VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id)
);
INSERT INTO price_component (id) VALUES 
('base_price'),
('discount_component'),
('surcharge_component'),
('manufacturer_suggested_price'),
('one_time_charge'),
('recurring_charge'),
('utilization_charge');
```


## Product Costing

SKIP

## Product to Product Associations

SKIP

## Products and Parts



