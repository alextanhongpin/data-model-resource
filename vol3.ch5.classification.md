## Level 1 Classification Pattern

```mysql
CREATE TABLE IF NOT EXISTS product (
	id BINARY(16),
	name VARCHAR(255) NOT NULL DEFAULT '',
	type VARCHAR(255) NOT NULL DEFAULT '',
	family VARCHAR(255) NOT NULL DEFAULT '',
	line_1 VARCHAR(255) NOT NULL DEFAULT '',
	line_2 VARCHAR(255) NOT NULL DEFAULT '',
	disk_capacity VARCHAR(255) NOT NULL DEFAULT '',
	color VARCHAR(255) NOT NULL DEFAULT '',
	required_disk_space VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;
```

## Level 2 Classification Pattern

```mysql
CREATE TABLE IF NOT EXISTS product_type (
	id INT UNSIGNED AUTO_INCREMENT,
	parent_product_type_id INT UNSIGNED NOT NULL DEFAULT 0,
	name VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id),
	FOREIGN KEY (parent_product_type_id) REFERENCES product_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS product_family (
	id INT UNSIGNED AUTO_INCREMENT,
	name VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS product_line (
	id BINARY(16),
	name VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS product_product_line_classification (
	id BINARY(16),
	product_id BINARY(16) NOT NULL,
	product_line_id BINARY(16) NOT NULL,
	from_date DATE NOT NULL DEFAULT '1000-01-01',
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	PRIMARY KEY (id),
	FOREIGN KEY (product_id) REFERENCES product(id),
	FOREIGN KEY (product_line_id) REFERENCES product_line(id),
	UNIQUE (product_id, product_line_id, from_date)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS product (
	id BINARY(16),
	name VARCHAR(255) NOT NULL DEFAULT '',
	product_type_id INT UNSIGNED NOT NULL DEFAULT 0,
	product_family_id INT UNSIGNED NOT NULL DEFAULT 0,
	disk_capacity VARCHAR(255) NOT NULL DEFAULT '',
	color VARCHAR(255) NOT NULL DEFAULT '',
	required_disk_space VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id),
	FOREIGN KEY (product_type_id) REFERENCES product_type(id),
	FOREIGN KEY (product_family_id) REFERENCES product_family(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;
```

## Level 3 Classification Pattern

```mysql

CREATE TABLE IF NOT EXISTS product_category (
	id INT UNSIGNED,
	parent_product_category_id INT UNSIGNED NOT NULL DEFAULT 0,
	product_category_type_id INT UNSIGNED NOT NULL DEFAULT 0,
	from_date DATE NOT NULL DEFAULT '1000-01-01',
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	name VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id),
	FOREIGN KEY (parent_product_category_id) REFERENCES product_category(id),
	FOREIGN KEY (product_category_type_id) REFERENCES product_category_type(id),
	UNIQUE (parent_product_category_id, product_category_type_id, from_date)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS product_category_type (
	id INT UNSIGNED,
	parent_product_category_type_id INT UNSIGNED NOT NULL DEFAULT 0,
	name VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id),
	FOREIGN KEY (product_category_type) REFERENCES product_category_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;


CREATE TABLE IF NOT EXISTS product_category_classification (
	id BINARY(16),
	product_id INT UNSIGNED NOT NULL DEFAULT 0,
	product_category_id INT UNSIGNED NOT NULL DEFAULT 0,
	from_date DATE NOT NULL DEFAULT '1000-01-01',
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	PRIMARY KEY (id),
	FOREIGN KEY (product_id) REFERENCES product(id),
	FOREIGN KEY (product_category_id) REFERENCES product_category(id),
	UNIQUE (product_id, product_category_id, from_date)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS product (
	id BINARY(16),
	name VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;
```


## Level 3 Classification Pattern with Rollups and Schemes

```mysql

CREATE TABLE IF NOT EXISTS product (
	id INT UNSIGNED AUTO_INCREMENT,
	name VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;


CREATE TABLE IF NOT EXISTS product_category_classification (
	id BINARY(16),
	product_id INT UNSIGNED NOT NULL DEFAULT 0,
	product_category_id INT UNSIGNED NOT NULL DEFAULT 0,
	from_date DATE NOT NULL DEFAULT '1000-01-01',
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	PRIMARY KEY (id)
	FOREIGN KEY (product_id) REFERENCES product(id),
	FOREIGN KEY (product_category_id) REFERENCES product_category(id),
	UNIQUE (product_id, product_category_id, from_date)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS product_category (
	id INT UNSIGNED AUTO_INCREMENT,
	product_category_type_id INT UNSIGNED NOT NULL DEFAULT 0,
	name VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id),
	FOREIGN KEY (product_category_type_id) REFERENCES product_category_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS product_category_type (
	id INT UNSIGNED AUTO_INCREMENT,
	product_category_type_scheme_id INT UNSIGNED NOT NULL DEFAULT 0,
	name VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id),
	FOREIGN KEY (product_category_type_scheme_id) REFERENCES product_category_type_scheme(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS product_category_type_scheme (
	id INT UNSIGNED AUTO_INCREMENT,
	party_role_id INT UNSIGNED NOT NULL DEFAULT 0,
	name VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id),
	FOREIGN KEY (party_role_id) REFERENCES party_role(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS party_role (
	id INT UNSIGNED AUTO_INCREMENT,
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS product_category_rollup (
	id INT UNSIGNED AUTO_INCREMENT,
	parent_product_category_id INT UNSIGNED NOT NULL DEFAULT 0,
	child_product_category_id INT UNSIGNED NOT NULL DEFAULT 0,
	product_category_rollup_type_id INT UNSIGNED NOT NULL DEFAULT 0,
	from_date DATE NOT NULL DEFAULT '1000-01-01',
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	PRIMARY KEY (id),
	FOREIGN KEY (parent_product_category_id) REFERENCES product_category(id),
	FOREIGN KEY (child_product_category_id) REFERENCES product_category(id),
	FOREIGN KEY (product_category_rollup_type_id) REFERENCES product_category_rollup_type(id),
	UNIQUE (parent_product_category_id, child_product_category_id, product_category_rollup_type_id, from_date)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS product_category_type_rollup (
	id INT UNSIGNED AUTO_INCREMENT,
	parent_product_category_type_id INT UNSIGNED NOT NULL DEFAULT 0,  
	child_product_category_type_id INT UNSIGNED NOT NULL DEFAULT 0,
	product_category_type_rollup_type_id INT UNSIGNED NOT NULL DEFAULT 0,
	from_date DATE NOT NULL DEFAULT '1000-01-01',
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	PRIMARY KEY (id),
	FOREIGN KEY (parent_product_category_type_id) REFERENCES product_category_type(id),
	FOREIGN KEY (child_product_category_type_id) REFERENCES product_category_type(id),
	FOREIGN KEY (product_category_type_rollup_type_id) REFERENCES product_category_type_rollup_type(id),
	UNIQUE (parent_product_category_type_id, child_product_category_type_id, product_category_type_rollup_type_id, from_date)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS product_category_type_rollup_type (
	id INT UNSIGNED AUTO_INCREMENT,
	parent_product_category_type_rollup_type_id INT UNSIGNED NOT NULL DEFAULT 0,
	name VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id),
	FOREIGN KEY (parent_product_category_type_rollup_type_id) REFERENCES product_category_type_rollup_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;
```


