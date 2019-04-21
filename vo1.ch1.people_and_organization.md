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


## Person - Alternate Model

Gender:
```mysql
CREATE TABLE IF NOT EXISTS gender_type (
	id CHAR(1),
	description VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

INSERT INTO gender_type (id, description) VALUES 
('m', 'male'), 
('f', 'female'),
('o', 'other'),
('-', 'not registered'); -- NOTE: We could have used 'x' for not registered, but then the third gender happens to be called gender 'x';

DROP TABLE IF EXISTS gender_type;
```

Marital Status & Marital Status Type:
```sql
CREATE TABLE IF NOT EXISTS marital_status_type (
	id CHAR(1),
	description VARCHAR(255),
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

INSERT INTO marital_status_type (id, description) VALUES 
('1', 'Single. This refers to a person who has never been married.'),
('2', 'Married. This refers to a person who is recognised as married under the marriage laws in Singapore. It includes a person who has remarried.'),
('3', 'Widowed. This refers to a person whose spouse(s) is/are deceased and who has not remarried.'),
('4', 'Separated. This refers to a person who has been legally separated or estranged from his/her spouse(s) and who has not remarried.'),
('5', 'Divorced. This refers to a person whose marriage(s) has/have been legally dissolved and who has not remarried.'),
('x', 'Not reported. This includes instances where the marital status is unknown, not reported or where there is no/insufficient information available on the marital status')
;

DROP TABLE IF EXISTS marital_status_type;

CREATE TABLE IF NOT EXISTS marital_status (
	marital_status_type_id CHAR(1) NOT NULL,
	from_date DATE NOT NULL DEFAULT '1001-01-01', 
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	PRIMARY KEY (marital_status_type_id, from_date),
	FOREIGN KEY (marital_status_type_id) REFERENCES marital_status_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

DROP TABLE IF EXISTS marital_status;
```

Physical Characteristic & Physical Characteristic Type:
```sql
CREATE TABLE IF NOT EXISTS physical_characteristic_type (
	id VARCHAR(32),
	description VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

INSERT INTO physical_characteristic_type (id, description) VALUES 
('weight', 'weight in kg'),
('height', 'height in inch'),
('blood_pressure', 'blood pressure');

CREATE TABLE IF NOT EXISTS physical_characteristic (
	person_id BINARY(16) NOT NULL,
	physical_characteristic_type_id VARCHAR(32) NOT NULL,
	from_date DATE NOT NULL DEFAULT '0001-01-01', 
	thru_date DATE NOT NULL DEFAULT '9999-12-31', 
	value VARCHAR(255),
	PRIMARY KEY (person_id, physical_characteristic_type_id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;
DROP TABLE IF EXISTS physical_characteristic;
```

Person:
```sql
CREATE TABLE IF NOT EXISTS person (
	id BINARY(16),
	birth_date DATE NOT NULL DEFAULT '1000-01-01',
	mothers_maiden_name VARCHAR(255) NOT NULL DEFAULT '',
	social_security_number VARCHAR(255) NOT NULL DEFAULT '',
	total_years_work_experience TINYINT NOT NULL DEFAULT 0,
	comment TEXT,
	gender_type_id CHAR(1) NOT NULL,
	PRIMARY KEY (id),
	FOREIGN KEY (gender_type_id) REFERENCES gender_type (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;


DROP TABLE IF EXISTS person;


-- Citizenship & Passport;
CREATE TABLE IF NOT EXISTS country (
	id VARCHAR(255),
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;
DROP TABLE IF EXISTS country;

-- Citizenship & Passport.
CREATE TABLE IF NOT EXISTS citizenship (
	from_date DATE NOT NULL DEFAULT '1000-01-01', 
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	person_id BINARY(16) NOT NULL,
	country_id VARCHAR(255) NOT NULL,
	PRIMARY KEY (from_date, person_date, country_id),
	FOREIGN KEY (country_id) REFERENCES country(id),
	FOREIGN KEY (person_id) REFERENCES person(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;
DROP TABLE IF EXISTS citizenship;


-- Not Complete.
CREATE TABLE IF NOT EXISTS passport (
	id BINARY(16),
	num VARCHAR(255) NOT NULL,
	issue_date DATE NOT NULL DEFAULT '0001-01-01', 
	expiration_date DATE NOT NULL DEFAULT '9999-12-31',
	person_id BINARY(16) NOT NULL,
	country_id VARCHAR(255) NOT NULL,
	FOREIGN KEY (country_id) REFERENCES country(id),
	FOREIGN KEY (person_id) REFERENCES person(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;
DROP TABLE IF EXISTS passport;

/*
CREATE TABLE IF NOT EXISTS person_name (
	person_id
	person_name_type_id
	name_seq_id
	from_date
	thru_date
	name
)
CREATE TABLE IF NOT EXISTS person_name_type (
	id
	description
)
*/
```

## Party
https://dba.stackexchange.com/questions/179654/developing-a-database-for-a-funds-transfers-business-where-a-people-and-organi/179712#179712

```mysql
CREATE TABLE IF NOT EXISTS party (
	id BINARY(16),
	party_subtype ENUM ('organization', 'person') NOT NULL,
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS party_type (
	id VARCHAR(255),
	description VARCHAR(255),
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS party_classification (
	party_id BINARY(16) NOT NULL,
	party_type_id VARCHAR(255) NOT NULL DEFAULT '',
	from_date DATE NOT NULL DEFAULT '1000-01-01',
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	PRIMARY KEY (party_id, party_type_id, from_date),
	FOREIGN KEY (party_id) REFERENCES party(id),
	FOREIGN KEY (party_type_id) REFERENCES party_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS organization (
	id BINARY(16),
	name VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id),
	-- Party and Organization shares the same id.
	FOREIGN KEY (id) REFERENCES party(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS person (
	id BINARY(16),
	name VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id),
	FOREIGN KEY (id) REFERENCES party(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;
```

Test:
```sql
SET @id = 'e7f695fa-6446-11e9-bd46-0242ac120002';
INSERT INTO party (id) VALUES (UUID_TO_BIN(@id, true));
INSERT INTO organization (id, name) VALUES (UUID_TO_BIN(@id, true), 'John Corp');
SELECT * FROM party WHERE id = UUID_TO_BIN(@id, true);
SELECT * FROM organization WHERE id = UUID_TO_BIN(@id, true);
INSERT INTO party_type (id) VALUES ('large organization');
INSERT INTO party_classification (party_id, party_type_id) VALUES (UUID_TO_BIN(@id, true), 'large organization');
SELECT * FROM party_classification WHERE party_id = UUID_TO_BIN(@id, true);
```
