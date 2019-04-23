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

## Level 2 Expanded Recursive Pattern

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
	redone_via_work_effort_id BINARY(16) NOT NULL,
	work_effort_breakdown_id BINARY(16) NOT NULL DEFAULT x'',
	work_effort_precedent_id BINARY(16) NOT NULL DEFAULT x'',
	work_effort_type_id BINARY(16) NOT NULL,
	name VARCHAR(255) NOT NULL DEFAULT '',
	scheduled_start_date DATE NOT NULL DEFAULT '1000-01-01',
	scheduled_end_date DATE NOT NULL DEFAULT '9999-12-31',
	PRIMARY KEY (id),
	FOREIGN KEY (redone_via_work_effort_id) REFERENCES work_effort(id),
	FOREIGN KEY (work_effort_breakdown_id) REFERENCES work_effort_breakdown(id),
	FOREIGN KEY (work_effort_precedent_id) REFERENCES work_effort_precedent(id),
	FOREIGN KEY (work_effort_type_id) REFERENCES work_effort_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS work_effort_breakdown (
	id BINARY(16),
	parent_work_effort_id BINARY(16) NOT NULL,
	child_work_effort_id BINARY(16) NOT NULL,
	from_date DATE NOT NULL DEFAULT '1000-01-01',
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	PRIMARY KEY (id),
	FOREIGN KEY (parent_work_effort_id) REFERENCES work_effort_breakdown(id),
	FOREIGN KEY (child_work_effort_id) REFERENCES work_effort_breakdown(id),
	UNIQUE (parent_work_effort_id, child_work_effort_id, from_date)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS work_effort_precedent (
	id BINARY(16),
	dependent_work_effort_id BINARY(16) NOT NULL,
	prerequisite_work_effort_id BINARY(16) NOT NULL,
	work_effort_precedent_type_id BINARY(16) NOT NULL,
	from_date DATE NOT NULL DEFAULT '1000-01-01',
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	PRIMARY KEY (id),
	FOREIGN KEY (dependent_work_effort_id) REFERENCES work_effort_precedent(id),
	FOREIGN KEY (prerequisite_work_effort_id) REFERENCES work_effort_precedent(id),
	FOREIGN KEY (work_effort_precedent_type_id) REFERENCES work_effort_precedent_type(id),
	UNIQUE (dependent_work_effort_id, prerequisite_work_effort_id, work_effort_precedent_type_id, from_date)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;


CREATE TABLE IF NOT EXISTS work_effort_precedent_type (
	id BINARY(16),
	name VARCHAR(255) NOT NULL DEFAULT ''
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;
```


## Level 3 Recursive Pattern

```mysql
CREATE TABLE IF NOT EXISTS work_effort_association (
	id BINARY(16),
	from_work_effort_id BINARY(16) NOT NULL,
	to_work_effort_id BINARY(16) NOT NULL,
	work_effort_association_type_id BINARY(16) NOT NULL,
	from_date DATE NOT NULL DEFAULT '1000-01-01',
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	PRIMARY KEY (id),
	FOREIGN KEY (from_work_effort_id) REFERENCES work_effort(id),
	FOREIGN KEY (to_work_effort_id) REFERENCES work_effort(id),
	FOREIGN KEY (work_effort_association_type_id) REFERENCES work_effort_association_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS work_effort_association_type (
	id INT UNSIGNED AUTO_INCREMENT,
	parent_work_effort_association_type_id INT UNSIGNED NOT NULL DEFAULT 0,
	name VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id),
	FOREIGN KEY (parent_work_effort_association_type_id) REFERENCES work_effort_association_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS work_effort (
	id BINARY(16),
	name VARCHAR(255) NOT NULL DEFAULT '',
	work_effort_type_id INT UNSIGNED NOT NULL DEFAULT 0,
	scheduled_start_date DATE NOT NULL DEFAULT '1000-01-01',
	scheduled_end_date DATE NOT NULL DEFAULT '9999-12-31',
	estimated_hours TINYINT NOT NULL DEFAULT 0,
	PRIMARY KEY (id),
	FOREIGN KEY (work_effort_type_id) REFERENCES work_effort_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS work_effort_type (
	id INT UNSIGNED AUTO_INCREMENT,
	parent_work_effort_type_id INT UNSIGNED NOT NULL DEFAULT 0,
	name VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id),
	FOREIGN KEY (parent_work_effort_type_id) REFERENCES work_effort_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;
```


## Level 3 Recursive Pattern with Rules
```mysql
CREATE TABLE IF NOT EXISTS work_effort_association_type (
	id INT UNSIGNED AUTO_INCREMENT,
	name VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS work_effort_association_rule (
	id INT UNSIGNED AUTO_INCREMENT,
	parent_work_effort_association_type_id INT UNSIGNED NOT NULL DEFAULT 0,
	name VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id),
	FOREIGN KEY (parent_work_effort_association_type_id) REFERENCES work_effort_association_rule(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS work_effort_association (
	id BINARY(16),
	from_work_effort_id BINARY(16) NOT NULL DEFAULT x'',
	to_work_effort_id BINARY(16) NOT NULL DEFAULT x'',
	work_effort_association_type_id INT UNSIGNED NOT NULL DEFAULT 0,
	work_effort_association_rule_id INT UNSIGNED NOT NULL DEFAULT 0,
	from_date DATE NOT NULL DEFAULT '1000-01-01',
	thru_date DATE NOT NULL DEFAULT '9999-12-31'
	PRIMARY KEY (id),
	FOREIGN KEY (from_work_effort_id) REFERENCES work_effort(id),
	FOREIGN KEY (to_work_effort_id) REFERENCES work_effort(id),
	FOREIGN KEY (work_effort_association_type_id) REFERENCES work_effort_association_type(id),
	FOREIGN KEY (work_effort_association_rule_id) REFERENCES work_effort_association_rule(id),
	UNIQUE (from_work_effort_id, to_work_effort_id, work_effort_association_type_id, work_effort_association_rule_id, from_date)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS work_effort_type (
	id INT UNSIGNED AUTO_INCREMENT,
	parent_work_effort_type_id INT UNSIGNED NOT NULL DEFAULT 0,
	name VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id),
	FOREIGN KEY (parent_work_effort_type_id) REFERENCES work_effort_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS work_effort (
	id BINARY(16),
	name VARCHAR(255),
	scheduled_start_date DATE NOT NULL DEFAULT '1000-01-01',
	scheduled_end_date DATE NOT NULL DEFAULT '9999-12-31',
	estimated_hours TINYINT NOT NULL DEFAULT 0,
	work_effort_type_id INT UNSIGNED NOT NULL DEFAULT 0,
	PRIMARY KEY (id),
	FOREIGN KEY (work_effort_type_id) REFERENCES work_effort_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;
```
