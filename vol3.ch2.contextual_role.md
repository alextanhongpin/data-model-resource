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

