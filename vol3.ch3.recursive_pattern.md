## Level 1 Recursive Pattern

```sql
CREATE TABLE IF NOT EXISTS project (
	id BINARY(16),
	name VARCHAR(255) NOT NULL DEFAULT '',
	scheduled_start_date DATE NOT NULL DEFAULT '1000-01-01',
	scheduled_end_date DATE NOT NULL DEFAULT '9999-12-31',
	estimated_hours TINYINT NOT NULL 0,
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS phase (
	id BINARY(16),
	project_id BINARY(16) NOT NULL,
	name VARCHAR(255) NOT NULL DEFAULT '',
	scheduled_start_date DATE NOT NULL DEFAULT '1000-01-01',
	scheduled_end_date DATE NOT NULL DEFAULT '9999-12-31',
	estimated_hours TINYINT NOT NULL 0,
	PRIMARY KEY (id),
	FOREIGN KEY (project_id) REFERENCES project(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS task (
	id BINARY(16),
	phase_id BINARY(16) NOT NULL,
	name VARCHAR(255) NOT NULL DEFAULT '',
	scheduled_start_date DATE NOT NULL DEFAULT '1000-01-01',
	scheduled_end_date DATE NOT NULL DEFAULT '9999-12-31',
	estimated_hours TINYINT NOT NULL 0,
	PRIMARY KEY (id),
	FOREIGN KEY (phase_id) REFERENCES phase(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;
```
