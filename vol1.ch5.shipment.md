## Shipment

```mysql
CREATE TABLE IF NOT EXISTS party (
	id BINARY(16),
	subtype ENUM('person', 'organization') NOT NULL,
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;


CREATE TABLE IF NOT EXISTS shipment_type (
	id VARCHAR(255),
	description VARCHAR(255) NOT NULL DEFAULT '',
	parent_shipment_type_id VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id),
	FOREIGN KEY (parent_shipment_type_id) REFERENCES shipment_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

INSERT INTO shipment_type (id, description) VALUES 
('', 'Default shipment type');

INSERT INTO shipment_type (id, parent_shipment_type_id) VALUES
('outgoing_shipment', ''),
('incoming_shipment', ''),
('transfer', ''),
('drop_shipment', ''),
('customer_shipment', 'outgoing_shipment'),
('purchase_return', 'outgoing_shipment'),
('purchase_shipment', 'incoming_shipment'),
('customer_return', 'incoming_shipment');

CREATE TABLE IF NOT EXISTS shipment (
	id BINARY(16),
	estimated_ship_date DATE NOT NULL DEFAULT CURRENT_DATE,
	estimated_ready_date DATE NOT NULL DEFAULT '9999-12-31',
	estimated_arrival_date DATE NOT NULL DEFAULT '9999-12-31',
	estimated_ship_cost DECIMAL(13,4) NOT NULL DEFAULT 0,
	actual_ship_cost DECIMAL(13,4) NOT NULL DEFAULT 0,
	latest_cancel_date DATE NOT NULL DEFAULT '9999-12-31',\
	handling_instructions VARCHAR(255) NOT NULL DEFAULT '',
	last_updated DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
	shipment_type_id VARCHAR(255) NOT NULL DEFAULT '',
	shipped_from_party_id BINARY(16) NOT NULL DEFAULT x'',
	shipped_to_party_id BINARY(16) NOT NULL DEFAULT x'',
	contact_mechanism_id BINARY(16) NOT NULL,
	PRIMARY KEY (id),
	FOREIGN KEY (shipment_type_id) REFERENCES shipment_type(id),
	FOREIGN KEY (shipped_from_party_id) REFERENCES party(id),
	FOREIGN KEY (shipped_to_party_id) REFERENCES party(id),
	FOREIGN KEY (contact_mechanism_id) REFERENCES contact_mechanism(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;
```


## Shipment Detail

```mysql
CREATE TABLE IF NOT EXISTS shipment_item (
	shipment_id BINARY(16) NOT NULL,
	shipment_item_seq_id UNSIGNED INT NOT NULL DEFAULT 1,
	quantity TINYINT NOT NULL DEFAULT 0,
	generating_shipment_id BINARY(16) NOT NULL DEFAULT x'',
	shipment_contents_description VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (shipment_id, shipment_item_seq_id),
	FOREIGN KEY (shipment_id) REFERENCES shipment (id),
	FOREIGN KEY (generating_shipment_id) REFERENCES shipment_item (shipment_id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS shipment_status_type (
	id VARCHAR(255),
	description VARCHAR(255) NOT NULL DEFAULT '',
	PRIMARY KEY (id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;

CREATE TABLE IF NOT EXISTS shipment_status (
	shipment_id BINARY(16),
	shipment_status_type_id VARCHAR(255) NOT NULL,
	status_date DATE NOT NULL DEFAULT CURRENT_DATE,
	PRIMARY KEY (shipment_id, shipment_status_type_id),
	FOREIGN KEY (shipment_id) REFERENCES shipment(id),
	FOREIGN KEY (shipment_status_type_id) REFERENCES shipment_status_type(id)
) ENGINE=InnoDB CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;
```


## Shipment-to-Order Relationship
## Shipment Receipts
## Item Issuance for Outgoing Shipments
## Shipment Documents
## Shipment Routing 
## Shipment Vehicle

