## Organization
```sql
CREATE TABLE IF NOT EXISTS organization (
	name VARCHAR(255) NOT NULL DEFAULT '',
	organization_subtype ENUM ('Informal Organization', 'Legal Organization'),
	organization_id BINARY(16) NOT NULL,
	PRIMARY KEY (organization_id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

INSERT INTO organization (name, organization_subtype, organization_id) VALUES 
("ABC Corporation", "Legal Organization",  uuid_to_bin(uuid())),
("ABC Subsidiary", "Legal Organization",  uuid_to_bin(uuid())),
("Accounting Division", "Informal Organization",  uuid_to_bin(uuid())),
("Information Systems Department", "Informal Organization",  uuid_to_bin(uuid())),
("Customer Service Division", "Informal Organization",  uuid_to_bin(uuid())),
("Customer Support Team", "Informal Organization",  uuid_to_bin(uuid())),

("ACME Corporation", "Legal Organization",  uuid_to_bin(uuid())),
("Fantastic Supplies", "Legal Organization",  uuid_to_bin(uuid())),
("Hughs Cargo", "Legal Organization",  uuid_to_bin(uuid())),
("Sellers Assistance Corporation", "Legal Organization",  uuid_to_bin(uuid())),
("Smith Family", "Informal Organization",  uuid_to_bin(uuid())),
("Government Quality Commision", "Legal Organization",  uuid_to_bin(uuid()));

DROP TABLE IF EXISTS organization;
```

## Person
```mysql
CREATE TABLE IF NOT EXISTS person (
	id BINARY(16),
	current_last_name VARCHAR(255) NOT NULL DEFAULT '',
	current_first_name VARCHAR(255) NOT NULL DEFAULT '',
	current_middle_name VARCHAR(255) NOT NULL DEFAULT '',
	current_personal_title VARCHAR(255) NOT NULL DEFAULT '',
	current_suffix VARCHAR(255) NOT NULL DEFAULT '',
	current_nickname VARCHAR(255) NOT NULL DEFAULT '',
	gender ENUM ('m', 'f', 'o', 'x') DEFAULT 'x',
	birth_date DATE NOT NULL DEFAULT '0000-01-01',
	height VARCHAR(255) NOT NULL DEFAULT '',
	weight VARCHAR(255) NOT NULL DEFAULT '',
	mothers_maiden_name VARCHAR(255) NOT NULL DEFAULT '',
	marital_status ENUM('single', 'married', 'widowed', 'separated', 'divorced', 'not reported') DEFAULT 'not reported',
	social_security_number VARCHAR(255) NOT NULL DEFAULT '',
	current_passport_no VARCHAR(255) NOT NULL DEFAULT '',
	current_passport_expire_date DATE NOT NULL DEFAULT '0000-01-01',
	total_years_work_experience TINYINT NOT NULL DEFAULT 0,
	comment TEXT,
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

INSERT INTO person (id, current_first_name, current_last_name, gender, birth_date, height, weight) VALUES 
(uuid_to_bin(UUID()), 'John', 'Smith', 'm', '1949-05-01', "6'", '190'),
(uuid_to_bin(UUID()), 'Jones', 'Shirley', 'f', '1959-12-04', "5'2\"", '100');

INSERT INTO person (id, current_first_name, current_last_name, gender) VALUES 
(uuid_to_bin(UUID()), 'Judy', 'Smith', 'f'),
(uuid_to_bin(UUID()), 'Nancy', 'Barry', 'f');

DROP TABLE IF EXISTS person;
```
