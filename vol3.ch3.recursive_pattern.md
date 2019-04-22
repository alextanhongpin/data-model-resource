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


## Level 2 Recursive Pattern

```mysql
CREATE TABLE IF NOT EXISTS work_effort_type (
	id BINARY(16),
	parent_work_effort_type_id BINARY(16) NOT NULL,
	name VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id),
	FOREIGN KEY (parent_work_effort_type_id) REFERENCES work_effort_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS work_effort (
	id BINARY(16),
	parent_work_effort_id BINARY(16) NOT NULL,
	prerequisite_work_effort_id BINARY(16) NOT NULL,
	work_effort_type_id BINARY(16) NOT NULL,
	name VARCHAR(255) NOT NULL DEFAULT '',
	scheduled_start_date DATE NOT NULL DEFAULT '1000-01-01',
	scheduled_end_date DATE NOT NULL DEFAULT '9999-12-31',
	PRIMARY KEY (id),
	FOREIGN KEY (parent_work_effort_id) REFERENCES work_effort(id),
	FOREIGN KEY (prerequisite_work_effort_id) REFERENCES work_effort(id),
	FOREIGN KEY (work_effort_type_id) REFERENCES work_effort_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;
```
