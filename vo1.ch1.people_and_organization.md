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
