# Status Patterns

- there is a need to track the status of the entity
- create a status type table to keep a list of statuses and status description
- use past tense for the status name, e.g. order_created, activated, updated rather than active, create_order (these belong to imperative command naming). The naming convention is similar to event sourcing.
- ask yourself if you need to keep track of the current status only or every status change. Using event sourcing to capture changes is also another alternative
- do not mix two different statuses (order status and payment status)
- if there's a relationship for the status between different entity, take a look at Level 4 Status Pattern
- for statuses that has a validity period, rather than `activated`, use the terms `term_started`, `term_ended`.
- for pending statuses, use `_requested`, e.g. `order_requested`, `approval_requested`, etc.
- for simple cases where we need to know the current status, the Level 2 Status Pattern, Current Status is sufficient. but for most cases, it is good to be able to track the changes in status

## Level 1 Status Pattern.

```mysql
CREATE TABLE IF NOT EXISTS order (
	id BINARY(16),
	description VARCHAR(255) NOT NULL DEFAULT '',
	received_datetime DATETIME NOT NULL DEFAULT '1000-01-01',
	entry_datetime DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
	confirmation_datetime DATETIME NOT NULL DEFAULT '1000-01-01',
	cancelled_datetime DATETIME NOT NULL DEFAULT '9999-12-31',
	opened_from_date DATETIME NOT NULL DEFAULT '1000-01-01',
	closed_thru_date DATETIME NOT NULL DEFAULT '9999-12-31'
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;
```

## Level 2 Status Pattern

```mysql
CREATE TABLE IF NOT EXISTS status_type (
	id INT UNSIGNED AUTO_INCREMENT,
	name VARCHAR(255) NOT NULL DEFAULT ''
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS order (
	id BINARY(16),
	status_type_id INT UNSIGNED NOT NULL DEFAULT 0,
	status_datetime DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
	description VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id),
	FOREIGN KEY (status_type_id) REFERENCES status_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;
```

## Level 3 Status Pattern

```mysql

CREATE TABLE IF NOT EXISTS status_type (
	id INT UNSIGNED AUTO_INCREMENT,
	name VARCHAR(255) NOT NULL DEFAULT ''
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;


CREATE TABLE IF NOT EXISTS order_status (
	id BINARY(16),
	order_id BINARY(16) NOT NULL DEFAULT x'',
	status_type_id INT UNSIGNED NOT NULL DEFAULT 0,
	status_datetime DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP;
	status_from_date DATE NOT NULL DEFAULT '1000-01-01',
	status_thru_date DATE NOT NULL DEFAULT '9999-12-31',
	from_date DATE NOT NULL DEFAULT '1000-01-01',
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	PRIMARY KEY (id),
	FOREIGN KEY (order_id) REFERENCES order(id),
	FOREIGN KEY (status_type_id) REFERENCES status_type(id),
	UNIQUE (order_id, status_type_id, from_date)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS order (
	id BINARY(16),
	datetime DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
	description VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;
```

## Level 4 Status Pattern

```mysql
CREATE TABLE IF NOT EXISTS status_type (
	id INT UNSIGNED AUTO_INCREMENT,
	name VARCHAR(255) NOT NULL DEFAULT ''
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS order (
	id BINARY(16),
	description VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS shipment (
	id BINARY(16),
	description VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS work_effort (
	id BINARY(16),
	NAME VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS status_application (
	id BINARY(16),
	order_id BINARY(16) NOT NULL DEFAULT x'',
	shipment_id BINARY(16) NOT NULL DEFAULT x'',
	work_effort_id BINARY(16) NOT NULL DEFAULT x'',
	status_type_id INT UNSIGNED NOT NULL DEFAULT 0,
	status_datetime DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP;
	status_from_date DATE NOT NULL DEFAULT '1000-01-01',
	status_thru_date DATE NOT NULL DEFAULT '9999-12-31',
	from_date DATE NOT NULL DEFAULT '1000-01-01',
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	PRIMARY KEY (id),
	FOREIGN KEY (order_id) REFERENCES order(id),
	FOREIGN KEY (shipment_id) REFERENCES shipment(id),
	FOREIGN KEY (work_effort_id) REFERENCES work_effort(id),
	FOREIGN KEY (status_type_id) REFERENCES status_type(id),
	UNIQUE (id, order_id, shipment_id, work_effort_id, status_type_id, from_date)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;
```

## Status Category Pattern

```mysql
CREATE TABLE IF NOT EXISTS status_type (
	id INT UNSIGNED AUTO_INCREMENT,
	name VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS status_type_category (
	id INT UNSIGNED AUTO_INCREMENT,
	parent_status_type_category_id INT UNSIGNED NOT NULL DEFAULT 0,
	status_type_category_type_id INT UNSIGNED NOT NULL DEFAULT 0,
	name VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id),
	FOREIGN KEY (parent_status_type_category_id) REFERENCES status_type_category(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS status_type_category_type (
	id INT UNSIGNED AUTO_INCREMENT,
	parent_status_type_category_type_id INT UNSIGNED NOT NULL DEFAULT 0,
	name VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id),
	FOREIGN KEY (parent_status_type_category_type_id) REFERENCES status_type_category_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS status_type_category_application (
	id INT UNSIGNED AUTO_INCREMENT,
	status_type_id INT UNSIGNED NOT NULL DEFAULT 0,
	status_type_category_id INT UNSIGNED NOT NULL DEFAULT 0,
	from_date DATE NOT NULL DEFAULT '1000-01-01',
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	PRIMARY KEY (id),
	FOREIGN KEY (status_type_id) REFERENCES status_type(id),
	FOREIGN KEY (status_type_category_id) REFERENCES status_type_category(id),
	UNIQUE (status_type_id, status_type_category_id, from_date)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;
```

## Status Type with Multi Rollup and Rules Pattern

```mysql
drop database test;
create database test;
use test;


CREATE TABLE IF NOT EXISTS status_type (
	id INT UNSIGNED AUTO_INCREMENT,
	name VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id),
	UNIQUE (name)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;


CREATE TABLE IF NOT EXISTS status_type_association_rule (
	id INT UNSIGNED AUTO_INCREMENT,
	name VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id),
	UNIQUE (name)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS status_type_association_type (
	id INT UNSIGNED AUTO_INCREMENT,
	name VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id),
	UNIQUE (name)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS status_type_category (
	id INT UNSIGNED AUTO_INCREMENT,
	name VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id),
	UNIQUE (name)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS status_type_association (
	id INT UNSIGNED AUTO_INCREMENT,
	from_status_type_id INT UNSIGNED NOT NULL,
	to_status_type_id INT UNSIGNED NOT NULL,
	status_type_association_rule_id INT UNSIGNED NOT NULL,
	status_type_association_type_id INT UNSIGNED NOT NULL,
	status_type_category_id INT UNSIGNED NOT NULL,
	from_date DATE NOT NULL DEFAULT '1000-01-01',
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	PRIMARY KEY (id),
	FOREIGN KEY (from_status_type_id) REFERENCES status_type(id),
	FOREIGN KEY (to_status_type_id) REFERENCES status_type(id),
	FOREIGN KEY (status_type_association_rule_id) REFERENCES status_type_association_rule(id),
	FOREIGN KEY (status_type_association_type_id) REFERENCES status_type_association_type(id),
	FOREIGN KEY (status_type_category_id) REFERENCES status_type_category(id),
	UNIQUE (from_status_type_id, to_status_type_id, status_type_association_rule_id, status_type_association_type_id, status_type_category_id, from_date)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;


INSERT INTO status_type_association_type
(name) VALUES 
('peer-to-peer-association'),
('aggregation'),
('hierachy');

INSERT INTO status_type_association_rule 
(name) VALUES
('precedence'),
('compatible'),
('implied'),
('exclusion'),
('substitution'),
('obsolescent');

INSERT INTO status_type_category 
(name) VALUES 
('jira workflow');

INSERT INTO status_type 
(name) VALUES
('start'),
('open'),
('resolved'),
('reopened'),
('closed'),
('in_progress')
;


INSERT INTO status_type_association (
	from_status_type_id,
	to_status_type_id,
	status_type_association_rule_id,
	status_type_association_type_id,
	status_type_category_id
) VALUES 
(1, 2, 1, 3, 1), -- start to open
(2, 6, 1, 3, 1), -- open to in progress
(2, 3, 1, 3, 1), -- open to resolved
(2, 5, 1, 3, 1), -- open to closed
(3, 5, 1, 3, 1), -- resolved to closed
(3, 4, 1, 3, 1), -- resolved to reopened
(4, 5, 1, 3, 1), -- reopened to closed
(4, 6, 1, 3, 1), -- reopened to in progress
(4, 3, 1, 3, 1), -- reopened to resolved
(5, 5, 1, 3, 1), -- closed to closed
(6, 2, 1, 3, 1), -- in progress to open
(6, 3, 1, 3, 1), -- in progress to resolved
(6, 5, 1, 3, 1) -- in progress to closed
;

select 
st.id as status_from_id,
st.name as status_from,
st2.id as status_to_id,
st2.name as status_to,
star.name as rule,
stat.name as type,
stc.name as category
from status_type_association sta
left join status_type st on (st.id = sta.from_status_type_id)
left join status_type st2 on (st2.id = sta.to_status_type_id)
left join status_type_association_rule star on (star.id = sta.status_type_association_rule_id)
left join status_type_association_type stat on (stat.id = sta.status_type_association_type_id)
left join status_type_category stc on (stc.id = sta.status_type_category_id)
;


-- What does the status 'open' leads to?
select
st.name as to_status
from status_type_association sta
left join status_type st on (st.id = sta.to_status_type_id)
where from_status_type_id = 2; -- 'open'
```


