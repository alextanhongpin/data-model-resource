## Level 1 Contact Mechanism Pattern

```mysql
CREATE TABLE IF NOT EXISTS party (
	id BINARY(16),
	name VARCHAR(255) NOT NULL DEFAULT '',
	primary_street_address_part VARCHAR(255) NOT NULL DEFAULT '', 
	primary_suite_apartment VARCHAR(255) NOT NULL DEFAULT '',
	primary_address_part_1 VARCHAR(255) NOT NULL DEFAULT '',
	primary_post_office_box VARCHAR(255) NOT NULL DEFAULT '',
	primary_city VARCHAR(255) NOT NULL DEFAULT '',
	primary_state_region VARCHAR(255) NOT NULL DEFAULT '',
	primary_country VARCHAR(255) NOT NULL DEFAULT '',
	primary_postal_code VARCHAR(255) NOT NULL DEFAULT '',
	personal_country_telephone_code VARCHAR(255) NOT NULL DEFAULT '',
	personal_area_code VARCHAR(255) NOT NULL DEFAULT '',
	personal_telephone_number VARCHAR(255) NOT NULL DEFAULT '',
	business_country_telephone_code VARCHAR(255) NOT NULL DEFAULT '',
	business_area_code VARCHAR(255) NOT NULL DEFAULT '',
	business_telephone_number VARCHAR(255) NOT NULL DEFAULT '',
	business_email_address VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 


CREATE TABLE IF NOT EXISTS facility (
	id BINARY(16),
	name VARCHAR(255) NOT NULL DEFAULT '',
	address_part_1 VARCHAR(255) NOT NULL DEFAULT '',
	address_part_2 VARCHAR(255) NOT NULL DEFAULT '',
	address_part_3 VARCHAR(255) NOT NULL DEFAULT '',
	city VARCHAR(255) NOT NULL DEFAULT '',
	state_region VARCHAR(255) NOT NULL DEFAULT '',
	country VARCHAR(255) NOT NULL DEFAULT '',
	postal_code VARCHAR(255) NOT NULL DEFAULT '',
	area_code VARCHAR(255) NOT NULL DEFAULT '',
	telephone_number VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 

CREATE TABLE IF NOT EXISTS order (
	id BINARY(16),
	description VARCHAR(255) NOT NULL DEFAULT '',
	ship_to_address_part_1 VARCHAR(255) NOT NULL DEFAULT '',
	ship_to_address_part_2 VARCHAR(255) NOT NULL DEFAULT '',
	ship_to_address_part_3 VARCHAR(255) NOT NULL DEFAULT '',
	ship_to_city VARCHAR(255) NOT NULL DEFAULT '',
	ship_to_state_region VARCHAR(255) NOT NULL DEFAULT '',
	ship_to_country VARCHAR(255) NOT NULL DEFAULT '',
	ship_to_postal_code VARCHAR(255) NOT NULL DEFAULT '',
	bill_to_address_part_1 VARCHAR(255) NOT NULL DEFAULT '',
	bill_to_address_part_2 VARCHAR(255) NOT NULL DEFAULT '', 
	bill_to_address_part_3 VARCHAR(255) NOT NULL DEFAULT '',
	bill_to_city VARCHAR(255) NOT NULL DEFAULT '',
	bill_to_state_region VARCHAR(255) NOT NULL DEFAULT '',
	bill_to_country VARCHAR(255) NOT NULL DEFAULT '',
	bill_to_postal_code VARCHAR(255) NOT NULL DEFAULT '',
	country_telephone_code VARCHAR(255) NOT NULL DEFAULT '',
	area_code VARCHAR(255) NOT NULL DEFAULT '',
	telephone_number VARCHAR(255) NOT NULL DEFAULT '',
	email_address VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 
```

## Level 2 Contact Mechanism Pattern

```mysql
CREATE TABLE IF NOT EXISTS party (
	id BINARY(16),
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 

CREATE TABLE IF NOT EXISTS party_telecommunications_number (
	id BINARY(16),
	party_id BINARY(16) NOT NULL,
	telecommunications_number_id INT UNSIGNED NOT NULL DEFAULT 0,
	contact_mechanism_type_id INT UNSIGNED NOT NULL DEFAULT 0,
	contact_mechanism_usage_type_id INT UNSIGNED NOT NULL DEFAULT 0,
	from_date DATE NOT NULL DEFAULT '1000-01-01',
	thru_date DATE NOT NULL DEFAULT '9999-12-31'
	PRIMARY KEY (id),
	FOREIGN KEY (party_id) REFERENCES party(id),
	FOREIGN KEY (telecommunications_number_id) REFERENCES telecommunications_number(id),
	FOREIGN KEY (contact_mechanism_type_id) REFERENCES contact_mechanism_type(id),
	FOREIGN KEY (contact_mechanism_usage_type_id) REFERENCES contact_mechanism_usage_type(id),
	UNIQUE (party_id, telecommunications_number_id, contact_mechanism_type_id, contact_mechanism_usage_type_id, from_date)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 

CREATE TABLE IF NOT EXISTS telecommunications_number (
	id BINARY(16),
	country_telephone_code VARCHAR(255) NOT NULL DEFAULT '',
	area_code VARCHAR(255) NOT NULL DEFAULT '',
	telephone_number VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 

CREATE TABLE IF NOT EXISTS contact_mechanism_usage_type (
	id INT UNSIGNED AUTO_INCREMENT,
	name VARCHAR(255) NOT NULL DEFAULT '',
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 

CREATE TABLE IF NOT EXISTS telecommunications_number_id_classification (
	id BINARY(16),
	telecommunications_number_id INT UNSIGNED NOT NULL DEFAULT 0,
	contact_mechanism_type_id INT UNSIGNED NOT NULL DEFAULT 0,
	from_date DATE NOT NULL DEFAULT '1000-01-01',
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	PRIMARY KEY (id),
	FOREIGN KEY (telecommunications_number_id) REFERENCES telecommunication_number(id),
	FOREIGN KEY (contact_mechanism_type_id) REFERENCES contact_mechanism_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 

CREATE TABLE IF NOT EXISTS contact_mechanism_type (
	id INT UNSIGNED AUTO_INCREMENT,
	parent_contact_mechanism_type_id INT UNSIGNED NOT NULL DEFAULT 0,
	PRIMARY KEY (id),
	FOREIGN KEY (parent_contact_mechanism_type_id) REFERENCES contact_mechanism_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 

CREATE TABLE IF NOT EXISTS contact_mechanism_purpose_type (
	id INT UNSIGNED AUTO_INCREMENT,
	name VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 

CREATE TABLE IF NOT EXISTS contact_mechanism_purpose (
	id INT UNSIGNED AUTO_INCREMENT,
	party_telecommunications_number_id INT UNSIGNED NOT NULL DEFAULT 0,
	party_electronic_address_id INT UNSIGNED NOT NULL DEFAULT 0,
	party_postal_address_id INT UNSIGNED NOT NULL DEFAULT 0,
	contact_mechanism_purpose_type_id INT UNSIGNED NOT NULL DEFAULT 0,
	from_date DATE NOT NULL DEFAULT '1000-01-01',
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	PRIMARY KEY (id),
	FOREIGN KEY (party_telecommunications_number_id) REFERENCES party_telecommunications_number(id),
	FOREIGN KEY (party_electronic_address_id) REFERENCES party_electronic_address(id),
	FOREIGN KEY (party_postal_address_id) REFERENCES party_postal_address(id),
	FOREIGN KEY (contact_mechanism_purpose_type_id) REFERENCES contact_mechanism_purpose_type(id),
	UNIQUE (party_telecommunications_number_id, party_electronic_address_id, party_postal_address_id, contact_mechanism_purpose_type_id, from_date)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 

CREATE TABLE IF NOT EXISTS party_electronic_address (
	id INT UNSIGNED AUTO_INCREMENT,
	party_id BINARY(16) NOT NULL,
	postal_address_id INT UNSIGNED NOT NULL DEFAULT 0,
	contact_mechanism_usage_type_id INT UNSIGNED NOT NULL DEFAULT 0,
	from_date DATE NOT NULL DEFAULT '1000-01-01',
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	PRIMARY KEY (id),
	FOREIGN KEY (party_id) REFERENCES party(id),
	FOREIGN KEY (postal_address_id) REFERENCES postal_address(id),
	FOREIGN KEY (contact_mechanism_usage_type_id) REFERENCES contact_mechanism_usage_type(id),
	UNIQUE (party_id, postal_address_id, contact_mechanism_usage_type_id, from_date)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 

CREATE TABLE IF NOT EXISTS city (
	id INT UNSIGNED AUTO_INCREMENT,
	name VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 

CREATE TABLE IF NOT EXISTS state_region (
	id INT UNSIGNED AUTO_INCREMENT,,
	name VARCHAR(255) NOT NULL DEFAULT ''
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 

CREATE TABLE IF NOT EXISTS country (
	id INT UNSIGNED AUTO_INCREMENT,,
	name VARCHAR(255) NOT NULL DEFAULT ''
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 

CREATE TABLE IF NOT EXISTS postal_code (
	id INT UNSIGNED AUTO_INCREMENT,,
	name VARCHAR(255) NOT NULL DEFAULT ''
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 

CREATE TABLE IF NOT EXISTS postal_address (
	id BINARY(16),
	street_address_part VARCHAR(255) NOT NULL DEFAULT '',
	building_address_part VARCHAR(255) NOT NULL DEFAULT '',
	apt_suite_address_part VARCHAR(255) NOT NULL DEFAULT '',
	city_id INT UNSIGNED NOT NULL DEFAULT 0,
	state_region_id INT UNSIGNED NOT NULL DEFAULT 0,
	country_id INT UNSIGNED NOT NULL DEFAULT 0,
	postal_code_id INT UNSIGNED NOT NULL DEFAULT 0,
	directions VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id),
	FOREIGN KEY (city_id) REFERENCES city(id),
	FOREIGN KEY (state_region_id) REFERENCES state_region(id),
	FOREIGN KEY (country_id) REFERENCES country(id),
	FOREIGN KEY (postal_code_id) REFERENCES postal_code(id),
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 
```


## Level 3 Contact Mechanism 


```mysql
CREATE TABLE IF NOT EXISTS contact_mechanism (
	id BINARY(16),
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 

CREATE TABLE IF NOT EXISTS contact_mechanism_category_classification (
	id BINARY(16),
	contact_mechanism_id BINARY(16) NOT NULL DEFAULT x'',
	contact_mechanism_category_id INT UNSIGNED NOT NULL DEFAULT 0,
	from_date DATE NOT NULL DEFAULT '1000-01-01',
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	PRIMARY KEY (id),
	FOREIGN KEY (contact_mechanism_id) REFERENCES contact_mechanism(id),
	FOREIGN KEY (contact_mechanism_category_id) REFERENCES contact_mechanism_category(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 

CREATE TABLE IF NOT EXISTS contact_mechanism_category (
	id INT UNSIGNED AUTO_INCREMENT,
	contact_mechanism_category_type_id INT UNSIGNED NOT NULL DEFAULT 0,
	name VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id),
	FOREIGN KEY (contact_mechanism_category_type) REFERENCES contact_mechanism_category_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 

CREATE TABLE IF NOT EXISTS contact_mechanism_category_type (
	id INT UNSIGNED AUTO_INCREMENT,
	name VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 


CREATE TABLE IF NOT EXISTS postal_address_boundary (
	id INT UNSIGNED AUTO_INCREMENT,
	contact_mechanism_id BINARY(16) NOT NULL DEFAULT x'',
	geographic_boundary_id INT UNSIGNED NOT NULL DEFAULT 0,
	from_date DATE NOT NULL DEFAULT '1000-01-01',
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 

CREATE TABLE IF NOT EXISTS geographic_boundary (
	id INT UNSIGNED AUTO_INCREMENT,
	name VARCHAR(255) NOT NULL DEFAULT '',
	geographic_boundary_type_id INT UNSIGNED NOT NULL DEFAULT 0,
	name VARCHAR(255) NOT NULL DEFAULT ''
	geographic_boundary_code VARCHAR(255) NOT NULL DEFAULT '',
	abbreviation VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id),
	FOREIGN KEY (geographic_boundary_type_id) REFERENCES geographic_boundary_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 

CREATE TABLE IF NOT EXISTS geographic_boundary_type (
	id INT UNSIGNED AUTO_INCREMENT,
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 

INSERT INTO geographic_boundary_type (id) VALUES 
('postal_code'),
('country'),
('province'),
('county_city'),
('territory'),
('city'),
('state'),
('county'),
('region');

CREATE TABLE IF NOT EXISTS party (
	id BINARY(16),
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 


CREATE TABLE IF NOT EXISTS party (
	id BINARY(16),
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 

CREATE TABLE IF NOT EXISTS facility (
	id BINARY(16),
	name VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 

CREATE TABLE IF NOT EXISTS order (
	id BINARY(16),
	description VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 

CREATE TABLE IF NOT EXISTS contact_mechanism_usage_type (
	id INT UNSIGNED AUTO_INCREMENT,
	name VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 

CREATE TABLE IF NOT EXISTS contact_mechanism_usage (
	id INT UNSIGNED AUTO_INCREMENT,
	party_contact_mechanism_id BINARY(16) NOT NULL DEFAULT '', 
	contact_mechanism_usage_type_id INT UNSIGNED NOT NULL DEFAULT 0,
	from_date DATE NOT NULL DEFAULT '1000-01-01',
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	PRIMARY KEY (id),
	FOREIGN KEY (party_contact_mechanism_id) REFERENCES party_contact_mechanism(id),
	FOREIGN KEY (contact_mechanism_usage_type) REFERENCES contact_mechanism_usage_type(id),
	UNIQUE (party_contact_mechanism_id, contact_mechanism_usage_type_id, from_date)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 

CREATE TABLE IF NOT EXISTS party_contact_mechanism (
	id BINARY(16)
	party_id BINARY(16) NOT NULL DEFAULT x'',
	contact_mechanism_id BINARY(16) NOT NULL DEFAULT x'',
	from_date DATE NOT NULL DEFAULT '1000-01-01',
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	PRIMARY KEY (id),
	FOREIGN KEY (party_id) REFERENCES party(id),
	FOREIGN KEY (contact_mechanism_id) REFERENCES contact_mechanism(id),
	UNIQUE (party_id, contact_mechanism_id, from_date)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 

CREATE TABLE IF NOT EXISTS facility_contact_mechanism (
	id BINARY(16),
	facility_id BINARY(16) NOT NULL DEFAULT x'',
	contact_mechanism_id BINARY(16) NOT NULL DEFAULT x'',
	from_date DATE NOT NULL DEFAULT '1000-01-01',
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	PRIMARY KEY (id),
	FOREIGN KEY (facility_id) REFERENCES facility(id),
	FOREIGN KEY (contact_mechanism_id) REFERENCES contact_mechanism(id),
	UNIQUE (facility_id, contact_mechanism_id, from_date)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 

CREATE TABLE IF NOT EXISTS order_contact_mechanism (
	id BINARY(16),
	order_id BINARY(16) NOT NULL DEFAULT x'',
	contact_mechanism_id BINARY(16) NOT NULL DEFAULT x'',
	contact_mechanism_for_party_id BINARY(16) NOT NULL DEFAULT x'',
	from_date DATE NOT NULL DEFAULT '1000-01-01',
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	PRIMARY KEY (id),
	FOREIGN KEY (order_id) REFERENCES order(id),
	FOREIGN KEY (contact_mechanism_id) REFERENCES contact_mechanism(id),
	FOREIGN KEY (contact_mechanism_for_party_id) REFERENCES party(id),
	UNIQUE (order_id, contact_mechanism_id, contact_mechanism_for_party_id, from_date)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 

CREATE TABLE IF NOT EXISTS contact_mechanism_purpose (
	id BINARY(16),
	party_contact_mechanism_id BINARY(16) NOT NULL DEFAULT '', 
	facility_contact_mechanism_id BINARY(16) NOT NULL DEFAULT '',
	order_contact_mechanism_id BINARY(16) NOT NULL DEFAULT '',
	contact_mechanism_purpose_type_id BINARY(16) NOT NULL DEFAULT '',
	from_date DATE NOT NULL DEFAULT '1000-01-01',
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	PRIMARY KEY (id),
	FOREIGN KEY (party_contact_mechanism_id) REFERENCES party_contact_mechanism(id),
	FOREIGN KEY (facility_contact_mechanism_id) REFERENCES facility_contact_mechanism(id),
	FOREIGN KEY (order_contact_mechanism_id) REFERENCES order_contact_mechanism(id),
	FOREIGN KEY (contact_mechanism_purpose_type_id) REFERENCES contact_mechanism_purpose_type(id),
	UNIQUE (party_contact_mechanism_id, facility_contact_mechanism_id, order_contact_mechanism_id, contact_mechanism_purpose_type_id, from_date)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 
```


## Level 4 Contact Mechanism Pattern

```mysql
CREATE TABLE IF NOT EXISTS geographic_boundary_type (
	id INT UNSIGNED AUTO_INCREMENT,
	name VARCHAR(32) NOT NULL DEFAULT '',
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 

INSERT INTO geographic_boundary_type (name) VALUES 
('postal_code'),
('county'),
('province'),
('city'),
('territory'),
('prefecture'),
('state'),
('canton'),
('region'),
('subdivision'),
('continent'),
('country');

CREATE TABLE IF NOT EXISTS geographic_boundary (
	id INT UNSIGNED AUTO_INCREMENT,
	geographic_boundary_type_id INT UNSIGNED NOT NULL DEFAULT 0,
	geographic_boundary_code VARCHAR(255) NOT NULL DEFAULT '',
	abbreviation VARCHAR(255) NOT NULL DEFAULT ''
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 

CREATE TABLE IF NOT EXISTS postal_address_boundary (
	id BINARY(16),
	contact_mechanism_id BINARY(16) NOT NULL DEFAULT x'',
	geographic_boundary_id INT UNSIGNED NOT NULL DEFAULT 0,
	from_date DATE NOT NULL DEFAULT '1000-01-01',
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 

CREATE TABLE IF NOT EXISTS contact_mechanism (
	id BINARY(16),
	subtype ENUM('telecommunication_number', 'electronic_address', 'postal_address'),
	country_code VARCHAR(255) NOT NULL DEFAULT '',
	area_code VARCHAR(255) NOT NULL DEFAULT '',
	phone_number VARCHAR(255) NOT NULL DEFAULT '',
	electronic_address_string VARCHAR(255) NOT NULL DEFAULT '',
	street_address_part_1 VARCHAR(255) NOT NULL DEFAULT '',
	street_address_part_2 VARCHAR(255) NOT NULL DEFAULT '',
	street_address_part_3 VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id),
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 

CREATE TABLE IF NOT EXISTS contact_mechanism_application (
	id BINARY(16),
	party_id BINARY(16) NOT NULL,
	facility_id BINARY(16) NOT NULL,
	order_id BINARY(16) NOT NULL,
	contact_mechanism_id BINARY(16) NOT NULL,
	from_date DATE NOT NULL DEFAULT '1000-01-01', 
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	PRIMARY KEY (id),
	FOREIGN KEY (party_id) REFERENCES party(id),
	FOREIGN KEY (facility_id) REFERENCES facility(id),
	FOREIGN KEY (order_id) REFERENCES order(id),
	FOREIGN KEY (contact_mechanism_id) REFERENCES contact_mechanism(id),
	UNIQUE (party_id, facility_id, order_id, contact_mechanism_id, from_date)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 

CREATE TABLE IF NOT EXISTS party (
	id BINARY(16),
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 

CREATE TABLE IF NOT EXISTS facility (
	id BINARY(16),
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 

CREATE TABLE IF NOT EXISTS order (
	id BINARY(16),
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 

CREATE TABLE IF NOT EXISTS contact_mechanism_category (
	id INT UNSIGNED AUTO_INCREMENT,
	parent_contact_mechanism_category_id INT UNSIGNED NOT NULL,
	contact_mechanism_category_type_id INT UNSIGNED NOT NULL,
	name VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id),
	FOREIGN KEY (parent_contact_mechanism_category_id) REFERENCES parent_contact_mechanism_category(id),
	FOREIGN KEY (contact_mechanism_category_type_id) REFERENCES contact_mechanism_category_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 

CREATE TABLE IF NOT EXISTS contact_mechanism_category_type (
	id INT UNSIGNED AUTO_INCREMENT
	parent_contact_mechanism_category_type_id
	name VARCHAR(255)
	PRIMARY KEY (id),
	FOREIGN KEY (parent_contact_mechanism_category_type_id) REFERENCES contact_mechanism_category_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 

CREATE TABLE IF NOT EXISTS contact_mechanism_category_classification (
	id BINARY(16),
	contact_mechanism_category_id INT UNSIGNED NOT NULL,
	contact_mechanism_id BINARY(16) NOT NULL,
	contact_mechanims_application_id BINARY(16) NOT NULL,
	from_date DATE NOT NULL DEFAULT '1000-01-01',
	thru_date DATE NOT NULL DEFAULT '9999-12-31',
	PRIMARY KEY (id),
	FOREIGN KEY (contact_mechanism_category_id) REFERENCES contact_mechanism_category(id),
	FOREIGN KEY (contact_mechanism_id) REFERENCES contact_mechanism_id(id),
	FOREIGN KEY (contact_mechanism_application_id) REFERENCES contact_mechanism_application_id(id),
	UNIQUE (contact_mechanism_category_id, contact_mechanism_id, contact_mechanism_application_id, from_date)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci; 
```
