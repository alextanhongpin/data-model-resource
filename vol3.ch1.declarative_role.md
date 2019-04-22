## Level 1 Declarative Role Pattern

```
declarative role (
	declarative role id (PK)
	organization name 
	last name
	first name
)
```

```mysql
CREATE TABLE IF NOT EXISTS customer (
	customer_id BINARY(16),
	first_name VARCHAR(255) NOT NULL DEFAULT '',
	last_name VARCHAR(255) NOT NULL DEFAULT '',
	credit_limit UNSIGNED INT NOT NULL DEFAULT 0,
	organization_name VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (customer_id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS supplier (
	supplier_id BINARY(16),
	credit_limit UNSIGNED INT NOT NULL DEFAULT 0,
	taxation_identifier VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (supplier_id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS partner (
	partner_id BINARY(16),
	first_name VARCHAR(255) NOT NULL DEFAULT '',
	last_name VARCHAR(255) NOT NULL DEFAULT '',
	partner_type_id VARCHAR(255) NOT NULL DEFAULT '',
	organization_name VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (partner_id),
	FOREIGN KEY (partner_type_id) REFERENCES partner_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS employee (
	employee_id BINARY(16),
	first_name VARCHAR(255) NOT NULL DEFAULT '',
	last_name VARCHAR(255) NOT NULL DEFAULT '',
	employee_number VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (employee_id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;
```


## Level 2 Declarative Role Pattern

```mysql
CREATE TABLE IF NOT EXISTS party (
	id BINARY(16),
	subtype ENUM('person', 'organization') NOT NULL DEFAULT 
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS customer (
	id BINARY(16) NOT NULL,
	credit_limit DECIMAL(13,4) NOT NULL DEFAULT 0,
	PRIMARY KEY (id),
	FOREIGN KEY (id) REFERENCES party(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS supplier (
	id BINARY(16) NOT NULL,
	taxation_identifier CHAR(1) NOT NULL DEFAULT '',
	PRIMARY KEY (id),
	FOREIGN KEY (id) REFERENCES party(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS partner (
	id BINARY(16) NOT NULL,
	partner_type_id VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id),
	FOREIGN KEY (id) REFERENCES party(id),
	FOREIGN KEY (partner_type_id) REFERENCES partner_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS employee (
	id BINARY(16) NOT NULL,
	employee_number VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id),
	FOREIGN KEY (id) REFERENCES party(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;
```

## Level 3 Declarative Role Pattern

```mysql
CREATE TABLE IF NOT EXISTS party (
	id BINARY(16),
	subtype ENUM('person', 'organization') NOT NULL DEFAULT 
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS role_type (
	id VARCHAR(255),
	parent_role_type_id VARCHAR(255) NOT NULL DEFAULT '',
	name VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id),
	FOREIGN KEY (parent_role_type_id) REFERENCES role_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS party_role (
	-- NOTE: Why not set the id straight as party_id?
	id BINARY(16),
	party_id BINARY(16) NOT NULL,
	role_type_id VARCHAR(255) NOT NULL DEFAULT '',
	from_date DATE NOT NULL DEFAULT CURRENT_DATE,
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	PRIMARY KEY (party_id, role_type_id, from_date),
	FOREIGN KEY (party_id) REFERENCES party(id),
	FOREIGN KEY (role_type_id) REFERENCES role_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;
```
