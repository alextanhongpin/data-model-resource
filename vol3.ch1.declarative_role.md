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
