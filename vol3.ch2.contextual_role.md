## Level 1 Contextual Role Pattern.

```mysql
CREATE TABLE IF NOT EXISTS project (
	id BINARY(16),
	project_name VARCHAR(255) NOT NULL DEFAULT '',
	project_sponsor VARCHAR(255) NOT NULL DEFAULT '',
	project_worker VARCHAR(255) NOT NULL DEFAULT '',
	project_lead VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;
```

## Level 1 Contextual Role Pattern, Relationships.

```mysql
CREATE TABLE IF NOT EXISTS project_lead (
	id BINARY(16),
	lead_first_name VARCHAR(255) NOT NULL DEFAULT '',
	lead_last_name VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS worker (
	id BINARY(16),
	worker_first_name VARCHAR(255) NOT NULL DEFAULT '',
	worker_last_name VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS sponsor (
	id BINARY(16),
	sponsor_name VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS project (
	id BINARY(16),
	project_lead_id BINARY(16) NOT NULL,
	project_name VARCHAR(255) NOT NULL DEFAULT '',
	scheduled_start_date DATE NOT NULL DEFAULT CURRENT_DATE,
	PRIMARY KEY (id),
	FOREIGN KEY (project_lead_id) REFERENCES project_lead(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS project_sponsor (
	id BINARY(16),
	sponsor_id BINARY(16) NOT NULL,
	project_id BINARY(16) NOT NULL,
	from_date DATE NOT NULL DEFAULT CURRENT_DATE,
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	PRIMARY KEY (id),
	FOREIGN KEY (sponsor_id) REFERENCES sponsor(id),
	FOREIGN KEY (project_id) REFERENCES project(id),
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS project_worker (
	id BINARY(16),
	worker_id BINARY(16) NOT NULL,
	project_id BINARY(16) NOT NULL,
	from_date DATE NOT NULL DEFAULT CURRENT_DATE,
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	PRIMARY KEY (id),
	FOREIGN KEY (worker_id) REFERENCES worker(id),
	FOREIGN KEY (project_id) REFERENCES project(id),
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;
```


## Level 2 Contextual Role

```mysql
CREATE TABLE IF NOT EXISTS project (
	id BINARY(16),
	project_role_id VARCHAR(255) NOT NULL,
	project_name VARCHAR(255) NOT NULL DEFAULT '',
	scheduled_start_date DATE NOT NULL DEFAULT CURRENT_DATE,
	estimated_hours TINYINT NOT NULL DEFAULT 0,
	PRIMARY KEY (id),
	FOREIGN KEY (project_role_id) REFERENCES project_role(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS party (
	id BINARY(16),
	subtype ENUM('person', 'organization')
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS project_sponsor (
	id BINARY(16),
	party_role_id BINARY(16) NOT NULL,
	project_id BINARY(16) NOT NULL,
	from_date DATE NOT NULL DEFAULT '1000-01-01',
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	PRIMARY KEY (id),
	FOREIGN KEY (party_role_id) REFERENCES party_role(id),
	FOREIGN KEY (project_id) REFERENCES project(id),
	UNIQUE (party_role_id, project_id, from_date) 
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS project_worker (
	id BINARY(16),
	party_role_id BINARY(16) NOT NULL,
	project_id BINARY(16) NOT NULL,
	from_date DATE NOT NULL DEFAULT '1000-01-01',
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	PRIMARY KEY (id),
	FOREIGN KEY (party_role_id) REFERENCES party_role(id),
	FOREIGN KEY (project_id) REFERENCES project(id),
	UNIQUE (party_role_id, project_id, from_date) 
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS role_type (
	id VARCHAR(255),
	name VARCHAR(255) NOT NULL DEFAULT '',
	parent_role_type_id VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id),
	FOREIGN KEY (parent_role_type_id) REFERENCES parent_role_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS party_role (
	id BINARY(16),
	party_id BINARY(16) NOT NULL DEFAULT x'',
	role_type_id VARCHAR(255) NOT NULL DEFAULT '',
	from_date DATE NOT NULL DEFAULT '1000-01-01',
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	PRIMARY KEY (id),
	FOREIGN KEY (party_id) REFERENCES party(id),
	FOREIGN KEY (role_type_id) REFERENCES role_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS project_sponsor (
	id BINARY(16),
	party_role_id VARCHAR(255) NOT NULL,
	project_id BINARY(16) NOT NULL DEFAULT x'',
	from_date DATE NOT NULL DEFAULT '1000-01-01',
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	PRIMARY KEY (id),
	FOREIGN KEY (party_role_id) REFERENCES party(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;
```

## Level 2 Contextual Role Pattern, PARTY Only Alternative

```mysql
CREATE TABLE IF NOT EXISTS party (
	id BINARY(16),
	subtype ENUM('person', 'organization')
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS project_sponsor (
	id BINARY(16),
	party_id BINARY(16) NOT NULL,
	project_id BINARY(16) NOT NULL,
	from_date DATE NOT NULL DEFAULT CURRENT_DATE,
	thru_date DATE NOT NULL DEFAULT CURRENT_DATE,
	PRIMARY KEY (id),
	FOREIGN KEY (party_id) REFERENCES party(id),
	FOREIGN KEY (project_id) REFERENCES project(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS project_worker (
	id BINARY(16),
	party_id BINARY(16) NOT NULL,
	project_id BINARY(16) NOT NULL,
	from_date DATE NOT NULL DEFAULT CURRENT_DATE,
	thru_date DATE NOT NULL DEFAULT CURRENT_DATE,
	PRIMARY KEY (id),
	FOREIGN KEY (party_id) REFERENCES party(id),
	FOREIGN KEY (project_id) REFERENCES project(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS project (
	id BINARY(16),
	project_lead_party_id BINARY(16) NOT NULL,
	project_name VARCHAR(255) NOT NULL DEFAULT '',
	scheduled_start_date DATE NOT NULL DEFAULT '1000-01-01',
	estimated_hours TINYINT NOT NULL DEFAULT 0,
	PRIMARY KEY (id),
	FOREIGN KEY (project_lead_party_id) REFERENCES party(id),
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;
```

## Level 3 Contextual Role Pattern

```mysql
CREATE TABLE IF NOT EXISTS project (
	id BINARY(16),
	project_name VARCHAR(255) NOT NULL DEFAULT '',
	scheduled_start_date DATE NOT NULL DEFAULT '1000-01-01',
	estimated_hours TINYINT NOT NULL DEFAULT 0,
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS role_type (
	id VARCHAR(255),
	parent_role_type_id VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id),
	FOREIGN KEY (parent_role_type_id) REFERENCES role_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS party (
	id BINARY(16),
	subtype ENUM('person', 'organization') NOT NULL,
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS project_role (
	id BINARY(16),
	party_id BINARY(16) NOT NULL DEFAULT x'',
	role_type_id VARCHAR(255) NOT NULL DEFAULT 0,
	project_id BINARY(16) NOT NULL DEFAULT x'',
	from_date DATE NOT NULL DEFAULT '1000-01-01',
	thru_date DATE NOT NULL DEFAULT '9999-12-31'
	PRIMARY KEY (id),
	FOREIGN KEY (party_id) REFERENCES party(id),
	FOREIGN KEY (role_type_id) REFERENCES role_type(id),
	FOREIGN KEY (project_id) REFERENCES project(id),
	UNIQUE (party_id, role_type_id, project_id, from_date)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;
```


## Hybrid Contextual Model

```mysql
CREATE TABLE IF NOT EXISTS project (
	id BINARY(16),
	party_role_id VARCHAR(255) NOT NULL DEFAULT '',
	project_name VARCHAR(255) NOT NULL DEFAULT '',
	scheduled_start_date DATE NOT NULL DEFAULT '1000-01-01',
	estimated_hours TINYINT NOT NULL DEFAULT 0,
	PRIMARY KEY (id),
	FOREIGN KEY (party_role_id) REFERENCES party_role(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS role_type (
	id VARCHAR(255),
	parent_role_type_id VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id),
	FOREIGN KEY (parent_role_type_id) REFERENCES role_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS party (
	id BINARY(16),
	subtype ENUM('person', 'organization') NOT NULL,
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS project_role (
	id BINARY(16),
	party_id BINARY(16) NOT NULL DEFAULT x'',
	role_type_id VARCHAR(255) NOT NULL DEFAULT 0,
	project_id BINARY(16) NOT NULL DEFAULT x'',
	from_date DATE NOT NULL DEFAULT '1000-01-01',
	thru_date DATE NOT NULL DEFAULT '9999-12-31'
	PRIMARY KEY (id),
	FOREIGN KEY (party_id) REFERENCES party(id),
	FOREIGN KEY (role_type_id) REFERENCES role_type(id),
	FOREIGN KEY (project_id) REFERENCES project(id),
	UNIQUE (party_id, role_type_id, project_id, from_date)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;


CREATE TABLE IF NOT EXISTS project_sponsor (
	id BINARY(16),
	party_role_id BINARY(16) NOT NULL,
	project_id BINARY(16) NOT NULL,
	from_date DATE NOT NULL DEFAULT '1000-01-01',
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS project_worker (
	id BINARY(16),
	party_role_id BINARY(16) NOT NULL,
	project_id BINARY(16) NOT NULL,
	from_date DATE NOT NULL DEFAULT '1000-01-01',
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS party_role (
	id BINARY(16),
	party_id BINARY(16) NOT NULL,
	role_type_id VARCHAR(255) NOT NULL,
	PRIMARY KEY (id),
	FOREIGN KEY (party_id) REFERENCES party(id),
	FOREIGN KEY (role_type_id) REFERENCES role_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;
```
