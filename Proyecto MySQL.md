# Proyecto MySQL

### Tablas

```sql
DROP DATABASE ProyectoMySQL;

CREATE DATABASE ProyectoMySQL;

USE ProyectoMySQL;

CREATE TABLE IF NOT EXISTS countries (
	isocode VARCHAR(6) PRIMARY KEY,
    name VARCHAR(50) UNIQUE,
    alfaisotwo VARCHAR(2) UNIQUE,
    alfaisothree VARCHAR(4) UNIQUE
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS subdivisioncategories (
	id INT PRIMARY KEY AUTO_INCREMENT,
    description VARCHAR(40) UNIQUE
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS stateorregions (
	code VARCHAR(6) PRIMARY KEY,
    name VARCHAR(60) UNIQUE,
    country_id VARCHAR(6),
    CONSTRAINT FK_country_id_stateorregions FOREIGN KEY (country_id) REFERENCES countries(isocode),
    code3166 VARCHAR(10) UNIQUE,
    subdivision_id INT,
    CONSTRAINT FK_subdivision_id_stateorregions FOREIGN KEY (subdivision_id) REFERENCES subdivisioncategories(id)
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS citiesormunicipalities (
	code VARCHAR(6) PRIMARY KEY,
    name VARCHAR(60) UNIQUE,
    statereg_id VARCHAR(6),
    CONSTRAINT FK_statereg_id_citiesormunicipalities FOREIGN KEY (statereg_id) REFERENCES stateorregions(code)
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS typesidentifications (
	id INT PRIMARY KEY AUTO_INCREMENT,
    description VARCHAR(60) UNIQUE,
    sufix VARCHAR(5) UNIQUE
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS audiences (
	id INT PRIMARY KEY AUTO_INCREMENT,
    description VARCHAR(60)
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS unitofmeasure (
	id INT PRIMARY KEY AUTO_INCREMENT,
    description VARCHAR(60) UNIQUE
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS categories_polls (
	id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(80) UNIQUE
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS polls (
	id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(80) UNIQUE,
    description TEXT,
    isactive BOOLEAN,
    categorypoll_id INT,
    CONSTRAINT FK_categorypoll_id_polls FOREIGN KEY (categorypoll_id) REFERENCES categories_polls(id)
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS periods (
	id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(50) UNIQUE
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS memberships (
	id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(50) UNIQUE,
    description TEXT
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS benefits (
	id INT PRIMARY KEY AUTO_INCREMENT,
    descriptions VARCHAR(80),
    detail TEXT
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS categories (
	id INT PRIMARY KEY AUTO_INCREMENT,
    description VARCHAR(60) UNIQUE
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS audiencebenefits (
	audience_id INT,
    benefit_id INT,
    PRIMARY KEY (audience_id,benefit_id),
    CONSTRAINT FK_audience_id_audiencebenefits FOREIGN KEY (audience_id) REFERENCES audiences(id),
    CONSTRAINT FK_benefit_id_audiencebenefits FOREIGN KEY (benefit_id) REFERENCES benefits(id)
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS membershipperiods (
	membership_id INT,
    period_id INT,
    PRIMARY KEY (membership_id,period_id),
    CONSTRAINT FK_membership_id_membershipperiods FOREIGN KEY (membership_id) REFERENCES memberships(id),
    CONSTRAINT FK_period_id_membershipperiods FOREIGN KEY (period_id) REFERENCES periods(id)
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS products (
	id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(60) UNIQUE,
    detail TEXT,
    price DOUBLE,
    category_id INT,
    CONSTRAINT FK_category_id_products FOREIGN KEY (category_id) REFERENCES categories(id),
    image VARCHAR(60)
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS customers (
	id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(80),
    city_id VARCHAR(6),
    CONSTRAINT FK_city_id_customers FOREIGN KEY (city_id) REFERENCES citiesormunicipalities(code),
    audience_id INT,
    CONSTRAINT FK_audience_id_customers FOREIGN KEY (audience_id) REFERENCES audiences(id),
    cellphone VARCHAR(20) UNIQUE,
    email VARCHAR(100) UNIQUE,
   	msisactive BOOLEAN, -- Añadido
    membership_id INT, -- Añadido
    CONSTRAINT FK_membership_id_customers FOREIGN KEY (membership_id) REFERENCES memberships(id),
    address VARCHAR(120)
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS companies (
	id VARCHAR(20) PRIMARY KEY,
    type_id INT,
    CONSTRAINT FK_type_id_companies FOREIGN KEY (type_id) REFERENCES typesidentifications(id),
    name VARCHAR(80),
    category_id INT,
    CONSTRAINT FK_category_id_companies FOREIGN KEY (category_id) REFERENCES categories(id),
    city_id VARCHAR(6),
    CONSTRAINT FK_city_id_companies FOREIGN KEY (city_id) REFERENCES citiesormunicipalities(code),
    audience_id INT,
    CONSTRAINT FK_audience_id_companies FOREIGN KEY (audience_id) REFERENCES audiences(id),
    cellphone VARCHAR(15) UNIQUE,
    email VARCHAR(80) UNIQUE
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS companyproducts (
	company_id VARCHAR(20),
    product_id INT,
    PRIMARY KEY (company_id,product_id),
    CONSTRAINT FK_company_id_companyproducts FOREIGN KEY (company_id) REFERENCES companies(id),
    CONSTRAINT FK_product_id_companyproducts FOREIGN KEY (product_id) REFERENCES products(id),
    price DOUBLE,
    unitmeasure_id INT,
    CONSTRAINT FK_unitmeasure_id_companyproducts FOREIGN KEY (unitmeasure_id) REFERENCES unitofmeasure(id)
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS favorites (
	id INT PRIMARY KEY AUTO_INCREMENT,
    customer_id INT,
    CONSTRAINT FK_customer_id_favorites FOREIGN KEY (customer_id) REFERENCES customers(id),
    product_id INT,
    CONSTRAINT FK_product_id_favorites FOREIGN KEY (product_id) REFERENCES products(id)
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS details_favorites (
	id INT PRIMARY KEY AUTO_INCREMENT,
    favorite_id INT,
    CONSTRAINT FK_favorite_id_details_favorites FOREIGN KEY (favorite_id) REFERENCES favorites(id),
    product_id INT,
    CONSTRAINT FK_product_id_details_favorites FOREIGN KEY (product_id) REFERENCES products(id)
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS quality_products (
	product_id INT,
    customer_id INT,
    poll_id INT,
    company_id VARCHAR(20),
    PRIMARY KEY (product_id,customer_id,poll_id,company_id),
    CONSTRAINT FK_product_id_quality_products FOREIGN KEY (product_id) REFERENCES products(id),
    CONSTRAINT FK_customer_id_quality_products FOREIGN KEY (customer_id) REFERENCES customers(id),
    CONSTRAINT FK_poll_id_quality_products FOREIGN KEY (poll_id) REFERENCES polls(id),
    CONSTRAINT FK_company_id_quality_products FOREIGN KEY (company_id) REFERENCES companies(id),
    daterating DATETIME,
    rating DOUBLE
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS rates (
	customer_id INT,
    company_id VARCHAR(20),
    poll_id INT,
    PRIMARY KEY (customer_id,company_id,poll_id),
    CONSTRAINT FK_customer_id_rates FOREIGN KEY (customer_id) REFERENCES customers(id),
    CONSTRAINT FK_company_id_rates FOREIGN KEY (company_id) REFERENCES companies(id),
    CONSTRAINT FK_poll_id_rates FOREIGN KEY (poll_id) REFERENCES polls(id),
    daterating DATETIME,
    rating DOUBLE
) ENGINE = INNODB;

CREATE TABLE IF NOT EXISTS membershipbenefits (
	membership_id INT,
    period_id INT,
    audience_id INT,
    benefit_id INT,
    PRIMARY KEY (membership_id,period_id,audience_id,benefit_id),
    CONSTRAINT FK_membership_id_membershipbenefits FOREIGN KEY (membership_id) REFERENCES memberships(id),
    CONSTRAINT FK_period_id_membershipbenefits FOREIGN KEY (period_id) REFERENCES periods(id),
    CONSTRAINT FK_audience_id_membershipbenefits FOREIGN KEY (audience_id) REFERENCES audiences(id),
    CONSTRAINT FK_benefit_id_membershipbenefits FOREIGN KEY (benefit_id) REFERENCES benefits(id)
) ENGINE = INNODB;

SHOW TABLES;
```



### Inserts de prueba

```sql
-- 1. Insertar países
INSERT INTO countries (isocode, name, alfaisotwo, alfaisothree) VALUES
('4', 'Afganistán', 'AF', 'AFG'),
('8', 'Albania', 'AL', 'ALB'),
('276', 'Alemania', 'DE', 'DEU'),
('20', 'Andorra', 'AD', 'AND'),
('24', 'Angola', 'AO', 'AGO'),
('660', 'Anguilla', 'AI', 'AIA'),
('10', 'Antártida', 'AQ', 'ATA'),
('28', 'Antigua y Barbuda', 'AG', 'ATG'),
('682', 'Arabia Saudí', 'SA', 'SAU'),
('12', 'Argelia', 'DZ', 'DZA'),
('32', 'Argentina', 'AR', 'ARG'),
('51', 'Armenia', 'AM', 'ARM'),
('533', 'Aruba', 'AW', 'ABW'),
('36', 'Australia', 'AU', 'AUS'),
('40', 'Austria', 'AT', 'AUT'),
('31', 'Azerbaiyán', 'AZ', 'AZE'),
('44', 'Bahamas', 'BS', 'BHS'),
('50', 'Bangladés', 'BD', 'BGD'),
('52', 'Barbados', 'BB', 'BRB'),
('48', 'Baréin', 'BH', 'BHR'),
('56', 'Belgica', 'BE', 'BEL'),
('84', 'Belice', 'BZ', 'BLZ'),
('204', 'Benin', 'BJ', 'BEN'),
('60', 'Bermudas', 'BM', 'BMU'),
('112', 'Bielorussia', 'BY', 'BLR'),
('104', 'Birmania', 'MM', 'MMR'),
('68', 'Bolivia', 'BO', 'BOL'),
('70', 'Bosnia y Herzegovina', 'BA', 'BIH'),
('72', 'Botsuana', 'BW', 'BWA'),
('76', 'Brasil', 'BR', 'BRA'),
('96', 'Brunéi', 'BN', 'BRN'),
('100', 'Bulgaria', 'BG', 'BGR'),
('854', 'Burkina Faso', 'BF', 'BFA'),
('108', 'Burundi', 'BI', 'BDI'),
('64', 'Bután', 'BT', 'BTN'),
('132', 'Cabo Verde', 'CV', 'CPV'),
('116', 'Camboya', 'KH', 'KHM'),
('120', 'Camerún', 'CM', 'CMR'),
('124', 'Canadá', 'CA', 'CAN'),
('535', 'Caribe Neerlandés', 'BQ', 'BES'),
('634', 'Catar', 'QA', 'QAT'),
('148', 'Chad', 'TD', 'TCD'),
('152', 'Chile', 'CL', 'CHL'),
('156', 'China', 'CN', 'CHN'),
('196', 'Chipre', 'CY', 'CYP'),
('170', 'Colombia', 'CO', 'COL'),
('174', 'Comoras', 'KM', 'COM'),
('408', 'Corea del Norte', 'KP', 'PRK'),
('410', 'Corea del Sur', 'KR', 'KOR'),
('384', 'Costa de Marfil', 'CI', 'CIV'),
('188', 'Costa Rica', 'CR', 'CRI'),
('191', 'Croacia', 'HR', 'HRV'),
('192', 'Cuba', 'CU', 'CUB'),
('531', 'Curazao', 'CW', 'CUW'),
('208', 'Dinamarca', 'DK', 'DNK'),
('212', 'Dominica', 'DM', 'DMA'),
('218', 'Ecuador', 'EC', 'ECU'),
('818', 'Egipto', 'EG', 'EGY'),
('222', 'El Salvador', 'SV', 'SLV'),
('784', 'Emiratos árabes Unidos', 'AE', 'ARE'),
('232', 'Eritrea', 'ER', 'ERI'),
('703', 'Eslovaquia', 'SK', 'SVK'),
('705', 'Eslovenia', 'SI', 'SVN'),
('724', 'España', 'ES', 'ESP'),
('840', 'Estados Unidos', 'US', 'USA'),
('233', 'Estonia', 'EE', 'EST'),
('231', 'Etiopía', 'ET', 'ETH'),
('608', 'Filipinas', 'PH', 'PHL'),
('246', 'Finlandia', 'FI', 'FIN'),
('242', 'Fiyi', 'FJ', 'FJI'),
('250', 'Francia', 'FR', 'FRA'),
('266', 'Gabon', 'GA', 'GAB'),
('270', 'Gambia', 'GM', 'GMB'),
('268', 'Georgia', 'GE', 'GEO'),
('288', 'Ghana', 'GH', 'GHA'),
('292', 'Gibraltar', 'GI', 'GIB'),
('308', 'Granada', 'GD', 'GRD'),
('300', 'Grecia', 'GR', 'GRC'),
('304', 'Groenlandia', 'GL', 'GRL'),
('312', 'Guadelupe', 'GP', 'GLP'),
('316', 'Guam', 'GU', 'GUM'),
('320', 'Guatemala', 'GT', 'GTM'),
('254', 'Guayana Francesa', 'GF', 'GUF'),
('831', 'Guernsey', 'GG', 'GGY'),
('324', 'Guinea', 'GN', 'GIN'),
('226', 'Guinea Ecuatorial', 'GQ', 'GNQ'),
('624', 'Guinea-Bissau', 'GW', 'GNB'),
('328', 'Guyana', 'GY', 'GUY'),
('332', 'Haití', 'HT', 'HTI'),
('340', 'Honduras', 'HN', 'HND'),
('344', 'Hong Kong', 'HK', 'HKG'),
('348', 'Hungría', 'HU', 'HUN'),
('352', 'Iceland', 'IS', 'ISL'),
('356', 'India', 'IN', 'IND'),
('360', 'Indonesia', 'ID', 'IDN'),
('368', 'Irak', 'IQ', 'IRQ'),
('364', 'Iran', 'IR', 'IRN'),
('833', 'Isla de Man', 'IM', 'IMN'),
('162', 'Isla de Navidad', 'CX', 'CXR'),
('574', 'Isla Norfolk', 'NF', 'NFK'),
('372', 'Islandia', 'IE', 'IRL'),
('136', 'Islas Caimán', 'KY', 'CYM'),
('166', 'Islas Cocos', 'CC', 'CCK'),
('184', 'Islas Cook', 'CK', 'COK'),
('234', 'Islas Feroe', 'FO', 'FRO'),
('239', 'Islas Georgias y Sandwich del Sur', 'GS', 'SGS'),
('238', 'Islas Malvinas', 'FK', 'FLK'),
('580', 'Islas Marianas del Norte', 'MP', 'MNP'),
('584', 'Islas Marshall', 'MH', 'MHL'),
('612', 'Islas Pitcairn', 'PN', 'PCN'),
('90', 'Islas Salomón', 'SB', 'SLB'),
('796', 'Islas Turcas y Caicos', 'TC', 'TCA'),
('92', 'Islas Vírgenes Británicas', 'VG', 'VGB'),
('850', 'Islas Vírgenes de los Estados Unidos', 'VI', 'VIR'),
('376', 'Israel', 'IL', 'ISR'),
('380', 'Italia', 'IT', 'ITA'),
('388', 'Jamaica', 'JM', 'JAM'),
('392', 'Japón', 'JP', 'JPN'),
('832', 'Jersey', 'JE', 'JEY'),
('400', 'Jordania', 'JO', 'JOR'),
('398', 'Kazajistán', 'KZ', 'KAZ'),
('404', 'Kenia', 'KE', 'KEN'),
('417', 'Kirguistán', 'KG', 'KGZ'),
('296', 'Kiribati', 'KI', 'KIR'),
('153', 'Kosovo', 'XK', 'XKX'),
('414', 'Kuwait', 'KW', 'KWT'),
('418', 'Laos', 'LA', 'LAO'),
('426', 'Lesoto', 'LS', 'LSO'),
('428', 'Letonia', 'LV', 'LVA'),
('422', 'Libano', 'LB', 'LBN'),
('430', 'Liberia', 'LR', 'LBR'),
('434', 'Libia', 'LY', 'LBY'),
('438', 'Liechtenstein', 'LI', 'LIE'),
('440', 'Lituania', 'LT', 'LTU'),
('442', 'Luxemburgo', 'LU', 'LUX'),
('446', 'Macao', 'MO', 'MAC'),
('807', 'Macedonia', 'MK', 'MKD'),
('450', 'Madagascar', 'MG', 'MDG'),
('458', 'Malasia', 'MY', 'MYS'),
('454', 'Malaui', 'MW', 'MWI'),
('462', 'Maldivas', 'MV', 'MDV'),
('466', 'Mali', 'ML', 'MLI'),
('470', 'Malta', 'MT', 'MLT'),
('504', 'Marruecos', 'MA', 'MAR'),
('474', 'Martinica', 'MQ', 'MTQ'),
('480', 'Mauricio', 'MU', 'MUS'),
('478', 'Mauritania', 'MR', 'MRT'),
('175', 'Mayotte', 'YT', 'MYT'),
('484', 'México', 'MX', 'MEX'),
('498', 'Moldavia', 'MD', 'MDA'),
('492', 'Monaco', 'MC', 'MCO'),
('496', 'Mongolia', 'MN', 'MNG'),
('499', 'Monténégro', 'ME', 'MNE'),
('500', 'Montserrat', 'MS', 'MSR'),
('508', 'Mozambique', 'MZ', 'MOZ'),
('516', 'Namibia', 'NA', 'NAM'),
('520', 'Nauru', 'NR', 'NRU'),
('524', 'Nepal', 'NP', 'NPL'),
('558', 'Nicaragua', 'NI', 'NIC'),
('562', 'Niger', 'NE', 'NER'),
('566', 'Nigeria', 'NG', 'NGA'),
('570', 'Niue', 'NU', 'NIU'),
('578', 'Noruega', 'NO', 'NOR'),
('540', 'Nueva Caledonia', 'NC', 'NCL'),
('554', 'Nueva Zelanda', 'NZ', 'NZL'),
('512', 'Omán', 'OM', 'OMN'),
('528', 'Países Bajos', 'NL', 'NLD'),
('586', 'Pakistán', 'PK', 'PAK'),
('585', 'Palaos', 'PW', 'PLW'),
('275', 'Palestina', 'PS', 'PSE'),
('591', 'Panamá', 'PA', 'PAN'),
('598', 'Papúa Nueva Guinea', 'PG', 'PNG'),
('600', 'Paraguay', 'PY', 'PRY'),
('604', 'Perú', 'PE', 'PER'),
('258', 'Polinesia Francesa', 'PF', 'PYF'),
('616', 'Polonia', 'PL', 'POL'),
('620', 'Portugal', 'PT', 'PRT'),
('630', 'Puerto Rico', 'PR', 'PRI'),
('826', 'Reino Unido', 'GB', 'GBR'),
('732', 'República árabe Saharaui DemocrÁtica', 'EH', 'ESH'),
('140', 'República Centroafricana', 'CF', 'CAF'),
('203', 'República Checa', 'CZ', 'CZE'),
('178', 'República del Congo', 'CG', 'COG'),
('180', 'República DemocrÁtica del Congo', 'CD', 'COD'),
('214', 'República Dominicana', 'DO', 'DOM'),
('638', 'Reunión', 'RE', 'REU'),
('646', 'Ruanda', 'RW', 'RWA'),
('642', 'Rumania', 'RO', 'ROU'),
('643', 'Rusia', 'RU', 'RUS'),
('882', 'Samoa', 'WS', 'WSM'),
('16', 'Samoa Americana', 'AS', 'ASM'),
('652', 'San Bartolomé', 'BL', 'BLM'),
('659', 'San Cristóbal y Nieves', 'KN', 'KNA'),
('674', 'San Marino', 'SM', 'SMR'),
('534', 'San Martín', 'MF', 'MAF'),
('666', 'San Pedro y Miquelón', 'PM', 'SPM'),
('670', 'San Vicente y las Granadinas', 'VC', 'VCT'),
('662', 'Santa Lucía', 'LC', 'LCA'),
('678', 'Santo Tomé y Príncipe', 'ST', 'STP'),
('686', 'Senegal', 'SN', 'SEN'),
('688', 'Serbia', 'RS', 'SRB'),
('690', 'Seychelles', 'SC', 'SYC'),
('694', 'Sierra Leona', 'SL', 'SLE'),
('702', 'Singapur', 'SG', 'SGP'),
('654', 'Sint Helena, Ascension and Tristan da Cunha', 'SH', 'SHN'),
('663', 'Sint-Maarten', 'SX', 'SXM'),
('760', 'Siria', 'SY', 'SYR'),
('706', 'Somalia', 'SO', 'SOM'),
('144', 'Sri Lanka', 'LK', 'LKA'),
('748', 'eSwatani', 'SZ', 'SWZ'),
('710', 'Sudáfrica', 'ZA', 'ZAF'),
('729', 'Sudán', 'SD', 'SDN'),
('728', 'Sudán del Sur', 'SS', 'SSD'),
('752', 'Suecia', 'SE', 'SWE'),
('756', 'Suiza', 'CH', 'CHE'),
('740', 'Surinam', 'SR', 'SUR'),
('764', 'Tailandia', 'TH', 'THA'),
('158', 'Taiwan', 'TW', 'TWN'),
('834', 'Tanzania', 'TZ', 'TZA'),
('762', 'Tayikistán', 'TJ', 'TJK'),
('86', 'Territorio Británico del Océano índico', 'IO', 'IOT'),
('260', 'Tierras Australes y Antárticas Francesas', 'TF', 'ATF'),
('626', 'Timor Oriental', 'TL', 'TLS'),
('768', 'Togo', 'TG', 'TGO'),
('772', 'Tokelau', 'TK', 'TKL'),
('776', 'Tonga', 'TO', 'TON'),
('780', 'Trinidad y Tobago', 'TT', 'TTO'),
('788', 'Túnez', 'TN', 'TUN'),
('795', 'Turkmenistán', 'TM', 'TKM'),
('792', 'Turquía', 'TR', 'TUR'),
('798', 'Tuvalu', 'TV', 'TUV'),
('804', 'Ucrania', 'UA', 'UKR'),
('800', 'Uganda', 'UG', 'UGA'),
('858', 'Uruguay', 'UY', 'URY'),
('860', 'Uzbekistán', 'UZ', 'UZB'),
('548', 'Vanuatu', 'VU', 'VUT'),
('336', 'Vaticano', 'VA', 'VAT'),
('862', 'Venezuela', 'VE', 'VEN'),
('704', 'Vietnam', 'VN', 'VNM'),
('876', 'Wallis y Futuna', 'WF', 'WLF'),
('887', 'Yemen', 'YE', 'YEM'),
('262', 'Yibuti', 'DJ', 'DJI'),
('894', 'Zambia', 'ZM', 'ZMB'),
('716', 'Zimbabwe', 'ZW', 'ZWE');

-- 2. Insertar categorías de subdivisión
INSERT INTO subdivisioncategories (description) VALUES 
('departamento'), 
('estado'), 
('territorio externo'), 
('distrito capital'), 
('distrito');

-- 3. Insertar regiones/estados (depende de countries y subdivisioncategories)
INSERT INTO stateorregions (code, name, country_id, code3166, subdivision_id) VALUES
('05', 'Antioquia', '170', 'CO-ANT', 1),
('08', 'Atlántico', '170', 'CO-ATL', 1),
('11', 'Bogotá', '170', 'CO-DC', 4),
('13', 'Bolívar', '170', 'CO-BOL', 1),
('15', 'Boyacá', '170', 'CO-BOY', 1),
('17', 'Caldas', '170', 'CO-CAL', 1),
('18', 'Caquetá', '170', 'CO-CAQ', 1),
('19', 'Cauca', '170', 'CO-CAU', 1),
('20', 'Cesar', '170', 'CO-CES', 1),
('23', 'Córdoba', '170', 'CO-COR', 1),
('25', 'Cundinamarca', '170', 'CO-CUN', 1),
('27', 'Chocó', '170', 'CO-CHO', 1),
('41', 'Huila', '170', 'CO-HUI', 1),
('44', 'La Guajira', '170', 'CO-LAG', 1),
('47', 'Magdalena', '170', 'CO-MAG', 1),
('50', 'Meta', '170', 'CO-MET', 1),
('52', 'Nariño', '170', 'CO-NAR', 1),
('54', 'Norte de Santander', '170', 'CO-NSA', 1),
('63', 'Quindío', '170', 'CO-QUI', 1),
('66', 'Risaralda', '170', 'CO-RIS', 1),
('68', 'Santander', '170', 'CO-SAN', 1),
('70', 'Sucre', '170', 'CO-SUC', 1),
('73', 'Tolima', '170', 'CO-TOL', 1),
('76', 'Valle del Cauca', '170', 'CO-VAC', 1),
('81', 'Arauca', '170', 'CO-ARA', 1),
('85', 'Casanare', '170', 'CO-CAS', 1),
('86', 'Putumayo', '170', 'CO-PUT', 1),
('88', 'San Andrés y Providencia', '170', 'CO-SAP', 1),
('91', 'Amazonas', '170', 'CO-AMA', 1),
('94', 'Guainía', '170', 'CO-GUA', 1),
('95', 'Guaviare', '170', 'CO-GUV', 1),
('97', 'Vaupés', '170', 'CO-VAU', 1),
('99', 'Vichada', '170', 'CO-VID', 1),
('US-AL', 'Alabama', '840', 'US-AL', 2),
('US-AK', 'Alaska', '840', 'US-AK', 2),
('US-AZ', 'Arizona', '840', 'US-AZ', 2),
('US-AR', 'Arkansas', '840', 'US-AR', 2),
('US-CA', 'California', '840', 'US-CA', 2),
('US-CO', 'Colorado', '840', 'US-CO', 2),
('US-CT', 'Connecticut', '840', 'US-CT', 2),
('US-DE', 'Delaware', '840', 'US-DE', 2),
('US-FL', 'Florida', '840', 'US-FL', 2),
('US-GA', 'Georgia', '840', 'US-GA', 2),
('US-HI', 'Hawaii', '840', 'US-HI', 2),
('US-ID', 'Idaho', '840', 'US-ID', 2),
('US-IL', 'Illinois', '840', 'US-IL', 2),
('US-IN', 'Indiana', '840', 'US-IN', 2),
('US-IA', 'Iowa', '840', 'US-IA', 2),
('US-KS', 'Kansas', '840', 'US-KS', 2),
('US-KY', 'Kentucky', '840', 'US-KY', 2),
('US-LA', 'Louisiana', '840', 'US-LA', 2),
('US-ME', 'Maine', '840', 'US-ME', 2),
('US-MD', 'Maryland', '840', 'US-MD', 2),
('US-MA', 'Massachusetts', '840', 'US-MA', 2),
('US-MI', 'Michigan', '840', 'US-MI', 2),
('US-MN', 'Minnesota', '840', 'US-MN', 2),
('US-MS', 'Mississippi', '840', 'US-MS', 2),
('US-MO', 'Missouri', '840', 'US-MO', 2),
('US-MT', 'Montana', '840', 'US-MT', 2),
('US-NE', 'Nebraska', '840', 'US-NE', 2),
('US-NV', 'Nevada', '840', 'US-NV', 2),
('US-NH', 'New Hampshire', '840', 'US-NH', 2),
('US-NJ', 'New Jersey', '840', 'US-NJ', 2),
('US-NM', 'New Mexico', '840', 'US-NM', 2),
('US-NY', 'New York', '840', 'US-NY', 2),
('US-NC', 'North Carolina', '840', 'US-NC', 2),
('US-ND', 'North Dakota', '840', 'US-ND', 2),
('US-OH', 'Ohio', '840', 'US-OH', 2),
('US-OK', 'Oklahoma', '840', 'US-OK', 2),
('US-OR', 'Oregon', '840', 'US-OR', 2),
('US-PA', 'Pennsylvania', '840', 'US-PA', 2),
('US-RI', 'Rhode Island', '840', 'US-RI', 2),
('US-SC', 'South Carolina', '840', 'US-SC', 2),
('US-SD', 'South Dakota', '840', 'US-SD', 2),
('US-TN', 'Tennessee', '840', 'US-TN', 2),
('US-TX', 'Texas', '840', 'US-TX', 2),
('US-UT', 'Utah', '840', 'US-UT', 2),
('US-VT', 'Vermont', '840', 'US-VT', 2),
('US-VA', 'Virginia', '840', 'US-VA', 2),
('US-WA', 'Washington', '840', 'US-WA', 2),
('US-WV', 'West Virginia', '840', 'US-WV', 2),
('US-WI', 'Wisconsin', '840', 'US-WI', 2),
('US-WY', 'Wyoming', '840', 'US-WY', 2),
('US-DC', 'District of Columbia', '840', 'US-DC', 5),
('US-AS', 'American Samoa', '840', 'US-AS', 3),
('US-GU', 'Guam', '840', 'US-GU', 3),
('US-MP', 'Northern Mariana Islands', '840', 'US-MP', 3),
('US-PR', 'Puerto Rico', '840', 'US-PR', 3),
('US-UM', 'United States Minor Outlying Islands', '840', 'US-UM', 3),
('US-VI', 'Virgin Islands', '840', 'US-VI', 3);

-- 4. Insertar ciudades (depende de stateorregions)
INSERT INTO citiesormunicipalities (code, name, statereg_id) VALUES
('05001', 'Medellín', '05'),
('05002', 'Abejorral', '05'),
('05004', 'Abriaquí', '05'),
('05021', 'Alejandría', '05'),
('05030', 'Amagá', '05'),
('05031', 'Amarí', '05'),
('05034', 'Andes', '05'),
('05036', 'Angelópolis', '05'),
('05038', 'Angostura', '05'),
('05040', 'Anorí', '05'),
('05042', 'Santa Fe de Antioquia', '05'),
('05044', 'Anza', '05'),
('05045', 'Apartadó', '05'),
('05051', 'Arboletes', '05'),
('05055', 'Argelia', '05'),
('05059', 'Armenia', '05'),
('05079', 'Barbosa', '05'),
('05088', 'Belmira', '05'),
('05091', 'Betania', '05'),
('05093', 'Betulia', '05'),
('05101', 'Ciudad Bolívar', '05'),
('05107', 'Briceño', '05'),
('05113', 'Buriticá', '05'),
('05120', 'Cáceres', '05'),
('05125', 'Caicedo', '05'),
('05129', 'Caldas', '05'),
('05134', 'Campamento', '05'),
('05138', 'Cañasgordas', '05'),
('05142', 'Caracolí', '05'),
('05145', 'Caramanta', '05'),
('05147', 'Carepa', '05'),
('05148', 'El Carmen de Viboral', '05'),
('05150', 'Carolina', '05'),
('05154', 'Caucasia', '05'),
('05172', 'Chigorodó', '05'),
('05190', 'Cisneros', '05'),
('05197', 'Cocorná', '05'),
('05206', 'Concepción', '05'),
('05209', 'Concordia', '05'),
('05212', 'Copacabana', '05'),
('05234', 'Dabeiba', '05'),
('05237', 'Don Matías', '05'),
('05240', 'Ebéjico', '05'),
('05250', 'El Bagre', '05'),
('05264', 'Entrerríos', '05'),
('05266', 'Envigado', '05'),
('05282', 'Fredonia', '05'),
('05284', 'Frontino', '05'),
('05306', 'Giraldo', '05'),
('05308', 'Girardota', '05'),
('05310', 'Gómez Plata', '05'),
('05313', 'Granada', '05'),
('05315', 'Guadalupe', '05'),
('05318', 'Guarne', '05'),
('05321', 'Guatapé', '05'),
('05347', 'Heliconia', '05'),
('05353', 'Hispania', '05'),
('05360', 'Itagüí', '05'),
('05361', 'Ituango', '05'),
('05364', 'Jardín', '05'),
('05368', 'Jericó', '05'),
('05376', 'La Ceja', '05'),
('05380', 'La Estrella', '05'),
('05390', 'La Pintada', '05'),
('05400', 'La Unión', '05'),
('05411', 'Liborina', '05'),
('05425', 'Maceo', '05'),
('05440', 'Marinilla', '05'),
('05467', 'Montebello', '05'),
('05475', 'Murindó', '05'),
('05480', 'Mutatá', '05'),
('05483', 'Nariño', '05'),
('05490', 'Necoclí', '05'),
('05495', 'Nechí', '05'),
('05501', 'Olaya', '05'),
('05541', 'Peñol', '05'),
('05543', 'Peque', '05'),
('05576', 'Pueblorrico', '05'),
('05579', 'Puerto Berrío', '05'),
('05585', 'Puerto Nare', '05'),
('05591', 'Puerto Triunfo', '05'),
('05604', 'Remedios', '05'),
('05607', 'Retiro', '05'),
('05613', 'Rionegro', '05'),
('05631', 'Sabaneta', '05'),
('05642', 'Salgar', '05'),
('05647', 'San Andrés', '05'),
('05649', 'San Carlos', '05'),
('05652', 'San Francisco', '05'),
('05656', 'San Jerónimo', '05'),
('05658', 'San José de la Montaña', '05'),
('05659', 'San Juan de Urabá', '05'),
('05660', 'San Luis', '05'),
('05664', 'San Pedro', '05'),
('05665', 'San Pedro de Urabá', '05'),
('05667', 'San Rafael', '05'),
('05670', 'San Roque', '05'),
('05674', 'San Vicente', '05'),
('05679', 'Santa Bárbara', '05'),
('05686', 'Santa Rosa de Osos', '05'),
('05690', 'Santo Domingo', '05'),
('05697', 'El Santuario', '05'),
('05736', 'Segovia', '05'),
('05756', 'Sonson', '05'),
('05761', 'Sopetrán', '05'),
('05789', 'Támesis', '05'),
('05790', 'Tarazá', '05'),
('05792', 'Tarso', '05'),
('05809', 'Titiribí', '05'),
('05819', 'Toledo', '05'),
('05837', 'Turbo', '05'),
('05842', 'Uramita', '05'),
('05847', 'Urrao', '05'),
('05854', 'Valdivia', '05'),
('05856', 'Valparaíso', '05'),
('05858', 'Vegachí', '05'),
('05861', 'Venecia', '05'),
('05873', 'Vigía del Fuerte', '05'),
('05885', 'Yalí', '05'),
('05887', 'Yarumal', '05'),
('05890', 'Yolombó', '05'),
('05893', 'Yondó', '05'),
('05895', 'Zaragoza', '05'),
('08001', 'Barranquilla', '08'),
('08078', 'Baranoa', '08'),
('08137', 'Campo de la Cruz', '08'),
('08141', 'Candelaria', '08'),
('08296', 'Galapa', '08'),
('08372', 'Juan de Acosta', '08'),
('08421', 'Luruaco', '08'),
('08433', 'Malambo', '08'),
('08436', 'Manatí', '08'),
('08520', 'Palmar de Varela', '08'),
('08549', 'Piojó', '08'),
('08558', 'Polonuevo', '08'),
('08560', 'Ponedera', '08'),
('08573', 'Puerto Colombia', '08'),
('08606', 'Repelón', '08'),
('08634', 'Sabanagrande', '08'),
('08638', 'Sabanalarga', '08'),
('08675', 'Santa Lucía', '08'),
('08685', 'Santo Tomás', '08'),
('08758', 'Soledad', '08'),
('08770', 'Suán', '08'),
('08832', 'Tubará', '08'),
('08849', 'Usiacurí', '08'),
('11001', 'Bogotá', '11'),
('13001', 'Cartagena', '13'),
('13006', 'Achí', '13'),
('13030', 'Altos del Rosario', '13'),
('13042', 'Arenal', '13'),
('13052', 'Arjona', '13'),
('13062', 'Arroyohondo', '13'),
('13074', 'Barranco de Loba', '13'),
('13140', 'Calamar', '13'),
('13160', 'Cantagallo', '13'),
('13188', 'Cicuco', '13'),
('13212', 'Córdoba', '13'),
('13222', 'Clemencia', '13'),
('13244', 'El Carmen de Bolívar', '13'),
('13248', 'El Guamo', '13'),
('13268', 'El Peñón', '13'),
('13300', 'Hatillo de Loba', '13'),
('13430', 'Magangué', '13'),
('13433', 'Mahates', '13'),
('13440', 'Margarita', '13'),
('13442', 'María la Baja', '13'),
('13458', 'Montecristo', '13'),
('13468', 'Mompós', '13'),
('13473', 'Morales', '13'),
('13549', 'Pinillos', '13'),
('13580', 'Regidor', '13'),
('13600', 'Río Viejo', '13'),
('13620', 'San Cristóbal', '13'),
('13647', 'San Estanislao', '13'),
('13650', 'San Fernando', '13'),
('13654', 'San Jacinto', '13'),
('13655', 'San Jacinto del Cauca', '13'),
('13657', 'San Juan Nepomuceno', '13'),
('13667', 'San Martín de Loba', '13'),
('13670', 'San Pablo', '13'),
('13673', 'Santa Catalina', '13'),
('13683', 'Santa Rosa', '13'),
('13688', 'Santa Rosa del Sur', '13'),
('13744', 'Simití', '13'),
('13760', 'Soplaviento', '13'),
('13780', 'Talaigua Nuevo', '13'),
('13810', 'Tiquisio', '13'),
('13836', 'Turbaco', '13'),
('13838', 'Turbana', '13'),
('13873', 'Villanueva', '13'),
('13894', 'Zambrano', '13');

-- 5. Insertar tipos de identificación
INSERT INTO typesidentifications (description, sufix)
VALUES ('Cédula de Ciudadanía', 'CC'),
       ('Nit', 'NIT'),
       ('Pasaporte', 'PAS'),
       ('Cédula de Extranjería', 'CE');

-- 6. Insertar audiencias (actualizado para incluir más opciones)
INSERT INTO audiences (description)
VALUES ('General Public'), ('Business'), ('Students'), ('Seniors'), ('Children'), ('Tourists');

-- 7. Insertar unidades de medida (ampliado)
INSERT INTO unitofmeasure (description)
VALUES ('Kilogramo'), ('Libra'), ('Litro'), ('Unidad'), ('Caja'), ('Paquete'), 
       ('Gramo'), ('Mililitro'), ('Metro'), ('Centímetro'), ('Pulgada');

-- 8. Insertar categorías de encuestas (ampliado)
INSERT INTO categories_polls (name)
VALUES ('Satisfacción'), ('Calidad de Producto'), ('Experiencia de Compra'), 
       ('Servicio al Cliente'), ('Lealtad de Marca'), ('Nuevos Productos');

-- 9. Insertar encuestas (depende de categories_polls)
INSERT INTO polls (name, description, isactive, categorypoll_id)
VALUES ('Encuesta de Satisfacción 2023', 'Encuesta general de satisfacción con nuestros servicios', TRUE, 1),
       ('Calidad de Productos Alimenticios', 'Evaluación de calidad de productos alimenticios', TRUE, 2),
       ('Experiencia en Tienda Física', 'Encuesta sobre la experiencia en nuestras tiendas físicas', FALSE, 3),
       ('Servicio Postventa', 'Evaluación del servicio después de la compra', TRUE, 4),
       ('Preferencias de Marca', 'Encuesta sobre preferencias y lealtad de marca', TRUE, 5);

-- 10. Insertar períodos (ampliado)
INSERT INTO periods (name)
VALUES ('Diario'), ('Semanal'), ('Mensual'), ('Anual'), ('Trimestral'), ('Semestral');

-- 11. Insertar membresías (ampliado)
INSERT INTO memberships (name, description)
VALUES ('Básica', 'Membresía básica con beneficios esenciales'),
       ('Premium', 'Membresía premium con todos los beneficios'),
       ('Empresarial', 'Membresía para empresas con descuentos corporativos'),
       ('Estudiante', 'Membresía con descuentos para estudiantes'),
       ('Oro', 'Membresía exclusiva con beneficios premium');

-- 12. Insertar beneficios (ampliado)
INSERT INTO benefits (descriptions, detail)
VALUES ('Descuento 10%', 'Descuento del 10% en todos los productos'),
       ('Envío Gratis', 'Envío gratuito en todas las compras'),
       ('Asesoría Personalizada', 'Acceso a asesoría personalizada por expertos'),
       ('Acceso Prioritario', 'Acceso prioritario a nuevos productos'),
       ('Descuento 15%', 'Descuento del 15% en productos seleccionados'),
       ('Regalos Exclusivos', 'Regalos mensuales para miembros'),
       ('Eventos VIP', 'Acceso a eventos exclusivos para miembros');

-- 13. Insertar categorías de productos (ampliado)
INSERT INTO categories (description)
VALUES ('Alimentos'), ('Electrónicos'), ('Ropa'), ('Hogar'), ('Juguetes'),
       ('Belleza'), ('Deportes'), ('Libros'), ('Mascotas'), ('Salud');

-- 14. Insertar relación audiencia-beneficios (depende de audiences y benefits)
INSERT INTO audiencebenefits (audience_id, benefit_id)
VALUES (1, 1), (1, 2),  -- Público general
       (2, 1), (2, 3), (2, 5),  -- Empresas
       (3, 1), (3, 7),  -- Estudiantes
       (4, 2), (4, 6),  -- Adultos mayores
       (5, 6),          -- Niños
       (6, 1), (6, 4);  -- Turistas

-- 15. Insertar relación membresía-períodos (depende de memberships y periods)
INSERT INTO membershipperiods (membership_id, period_id)
VALUES (1, 3),  -- Básica mensual
       (2, 4),  -- Premium anual
       (3, 5),  -- Empresarial trimestral
       (4, 3),  -- Estudiante mensual
       (5, 4);  -- Oro anual

-- 16. Insertar productos (depende de categories)
INSERT INTO products (name, detail, price, category_id, image)
VALUES 
('Arroz Supremo 1kg', 'Arroz blanco de la más alta calidad', 2500, 1, 'arroz_supremo.jpg'),
('Smartphone Galaxy X', 'Último modelo smartphone con cámara 108MP', 1500000, 2, 'galaxy_x.jpg'),
('Camiseta Algodón Premium', 'Camiseta 100% algodón orgánico', 45000, 3, 'camiseta_premium.jpg'),
('Juego de Ollas Titanio', 'Set de 5 ollas antiadherentes de titanio', 320000, 4, 'ollas_titanio.jpg'),
('Muñeca Interactiva', 'Muñeca que habla y aprende', 120000, 5, 'muneca_interactiva.jpg'),
('Crema Hidratante Facial', 'Crema con ácido hialurónico', 85000, 6, 'crema_facial.jpg'),
('Balón de Fútbol Oficial', 'Balón tamaño 5 para competencia', 120000, 7, 'balon_futbol.jpg'),
('Cien Años de Soledad', 'Edición especial aniversario', 65000, 8, 'cien_anios.jpg'),
('Alimento para Perro Adulto', 'Alimento premium 15kg', 135000, 9, 'alimento_perro.jpg'),
('Vitaminas Multivitamínicas', 'Suplemento con 12 vitaminas esenciales', 45000, 10, 'vitaminas.jpg');

-- 17. Insertar clientes (depende de citiesormunicipalities, audiences y memberships)
INSERT INTO customers (name, city_id, audience_id, cellphone, email, msisactive, membership_id, address)
VALUES
('Juan Pérez', '05001', 1, '3001234567', 'juan.perez@email.com', TRUE, 1, 'Calle 10 #45-20, Medellín'),
('María Gómez', '08001', 3, '3102345678', 'maria.gomez@email.com', TRUE, 4, 'Carrera 5 #12-34, Barranquilla'),
('Empresa XYZ Ltda', '11001', 2, '3203456789', 'contacto@xyz.com', TRUE, 3, 'Avenida 30 #25-60, Bogotá'),
('Carlos Rodríguez', '13001', 1, '3014567890', 'carlos.rod@email.com', FALSE, NULL, 'Calle 20 #15-30, Cartagena'),
('Ana Martínez', '05001', 4, '3155678901', 'ana.martinez@email.com', TRUE, 2, 'Carrera 80 #45-35, Medellín'),
('Luis García', '08001', 1, '3186789012', 'luis.garcia@email.com', TRUE, 1, 'Diagonal 25 #10-15, Barranquilla'),
('Sofía Ramírez', '11001', 3, '3007890123', 'sofia.ramirez@email.com', TRUE, 4, 'Transversal 8 #20-50, Bogotá'),
('Pedro Sánchez', '13001', 1, '3168901234', 'pedro.sanchez@email.com', FALSE, NULL, 'Calle 15 #30-25, Cartagena'),
('Laura Fernández', '05001', 6, '3019012345', 'laura.fernandez@email.com', TRUE, 5, 'Carrera 70 #55-10, Medellín'),
('David Torres', '08001', 1, '3120123456', 'david.torres@email.com', TRUE, 1, 'Avenida 40 #22-18, Barranquilla');

-- 18. Insertar empresas (depende de typesidentifications, categories, citiesormunicipalities y audiences)
INSERT INTO companies (id, type_id, name, category_id, city_id, audience_id, cellphone, email)
VALUES
('900123456-1', 2, 'Alimentos Saludables S.A.', 1, '05001', 2, '6041234567', 'info@alimentossaludables.com'),
('800987654-2', 2, 'TecnoMundo Ltda.', 2, '08001', 2, '6052345678', 'ventas@tecnomundo.com'),
('123456789', 1, 'Moda Elegante', 3, '11001', 2, '6013456789', 'contacto@modaelegante.com'),
('987654321', 2, 'Hogar y Más', 4, '13001', 2, '6074567890', 'servicio@hogarymas.com'),
('543216789-1', 2, 'Juguetes Creativos', 5, '05001', 2, '6045678901', 'info@juguetescreativos.com'),
('135792468-2', 2, 'Belleza Natural', 6, '08001', 2, '6056789012', 'ventas@bellezanatural.com'),
('246813579', 1, 'Deportes Extremos', 7, '11001', 2, '6017890123', 'info@deportesextremos.com'),
('864209753-1', 2, 'Libros y Saberes', 8, '13001', 2, '6078901234', 'contacto@librosysaberes.com'),
('112233445-5', 2, 'Mascotas Felices', 9, '05001', 2, '6049012345', 'info@mascotasfelices.com'),
('556677889-9', 2, 'Salud Integral', 10, '08001', 2, '6050123456', 'contacto@saludintegral.com');

-- 19. Insertar productos de empresa (depende de companies, products y unitofmeasure)
INSERT INTO companyproducts (company_id, product_id, price, unitmeasure_id)
VALUES
('900123456-1', 1, 2800, 1),  -- Alimentos vende arroz por kg
('800987654-2', 2, 1450000, 4),  -- TecnoMundo vende smartphones por unidad
('123456789', 3, 42000, 4),     -- Moda vende camisetas por unidad
('987654321', 4, 310000, 4),     -- Hogar vende ollas por set
('543216789-1', 5, 115000, 4),   -- Juguetes vende muñecas por unidad
('135792468-2', 6, 82000, 7),    -- Belleza vende crema por gramos
('246813579', 7, 115000, 4),     -- Deportes vende balones por unidad
('864209753-1', 8, 62000, 4),    -- Librería vende libros por unidad
('112233445-5', 9, 130000, 1),   -- Mascotas vende alimento por kg
('556677889-9', 10, 43000, 7);   -- Salud vende vitaminas por gramos

-- 20. Insertar favoritos (depende de customers y products)
INSERT INTO favorites (customer_id, product_id)
VALUES
(1, 2), (1, 7),  -- Juan tiene como favoritos smartphone y balón
(2, 1), (2, 6),  -- María tiene como favoritos arroz y crema
(3, 4), (3, 8),  -- Empresa XYZ tiene como favoritos ollas y libro
(4, 3),          -- Carlos tiene como favorito camiseta
(5, 5), (5, 9),  -- Ana tiene como favoritos muñeca y alimento mascotas
(6, 10),         -- Luis tiene como favorito vitaminas
(7, 1), (7, 2), (7, 3),  -- Sofía tiene varios favoritos
(8, 7), (8, 8),  -- Pedro tiene como favoritos balón y libro
(9, 4), (9, 6),  -- Laura tiene como favoritos ollas y crema
(10, 5);         -- David tiene como favorito muñeca

-- 21. Insertar detalles de favoritos (depende de favorites y products)
INSERT INTO details_favorites (favorite_id, product_id)
VALUES
(1, 2), (2, 7),  -- Detalles de favoritos de Juan
(3, 1), (4, 6),  -- Detalles de favoritos de María
(5, 4), (6, 8),  -- Detalles de favoritos de Empresa XYZ
(7, 3),          -- Detalles de favoritos de Carlos
(8, 5), (9, 9),  -- Detalles de favoritos de Ana
(10, 10),        -- Detalles de favoritos de Luis
(11, 1), (12, 2), (13, 3),  -- Detalles de favoritos de Sofía
(14, 7), (15, 8),  -- Detalles de favoritos de Pedro
(16, 4), (17, 6),  -- Detalles de favoritos de Laura
(18, 5);          -- Detalles de favoritos de David

-- 22. Insertar calidad de productos (depende de products, customers, polls y companies)
INSERT INTO quality_products (product_id, customer_id, poll_id, company_id, daterating, rating)
VALUES
(1, 1, 2, '900123456-1', '2023-05-15 14:30:00', 4.5),  -- Juan califica arroz
(2, 2, 1, '800987654-2', '2023-05-16 10:15:00', 5.0),  -- María califica smartphone
(3, 3, 3, '123456789', '2023-05-17 16:45:00', 3.8),    -- Empresa XYZ califica camiseta
(4, 4, 4, '987654321', '2023-05-18 11:20:00', 4.2),    -- Carlos califica ollas
(5, 5, 5, '543216789-1', '2023-05-19 09:30:00', 4.7),   -- Ana califica muñeca
(6, 6, 1, '135792468-2', '2023-05-20 15:10:00', 4.0),   -- Luis califica crema
(7, 7, 2, '246813579', '2023-05-21 17:25:00', 4.8),     -- Sofía califica balón
(8, 8, 3, '864209753-1', '2023-05-22 10:50:00', 4.9),   -- Pedro califica libro
(9, 9, 4, '112233445-5', '2023-05-23 14:15:00', 4.3),   -- Laura califica alimento mascotas
(10, 10, 5, '556677889-9', '2023-05-24 16:40:00', 4.6); -- David califica vitaminas

-- 23. Insertar calificaciones (depende de customers, companies y polls)
INSERT INTO rates (customer_id, company_id, poll_id, daterating, rating)
VALUES
(1, '900123456-1', 1, '2023-05-15 14:35:00', 4.0),  -- Juan califica Alimentos
(2, '800987654-2', 1, '2023-05-16 10:20:00', 4.8),  -- María califica TecnoMundo
(3, '123456789', 1, '2023-05-17 16:50:00', 3.5),    -- Empresa XYZ califica Moda
(4, '987654321', 1, '2023-05-18 11:25:00', 4.2),    -- Carlos califica Hogar
(5, '543216789-1', 1, '2023-05-19 09:35:00', 4.5),  -- Ana califica Juguetes
(6, '135792468-2', 1, '2023-05-20 15:15:00', 4.1),  -- Luis califica Belleza
(7, '246813579', 1, '2023-05-21 17:30:00', 4.7),    -- Sofía califica Deportes
(8, '864209753-1', 1, '2023-05-22 10:55:00', 4.9),  -- Pedro califica Libros
(9, '112233445-5', 1, '2023-05-23 14:20:00', 4.4),  -- Laura califica Mascotas
(10, '556677889-9', 1, '2023-05-24 16:45:00', 4.6); -- David califica Salud

-- 24. Insertar beneficios de membresía (depende de memberships, periods, audiences y benefits)
INSERT INTO membershipbenefits (membership_id, period_id, audience_id, benefit_id)
VALUES
(1, 3, 1, 1),  -- Básica mensual público general: descuento 10%
(1, 3, 1, 2),  -- Básica mensual público general: envío gratis
(2, 4, 1, 1), (2, 4, 1, 2), (2, 4, 1, 3), (2, 4, 1, 4),  -- Premium anual público general: todos beneficios
(3, 5, 2, 1), (3, 5, 2, 3), (3, 5, 2, 5),  -- Empresarial trimestral empresas: descuentos y asesoría
(4, 3, 3, 1), (4, 3, 3, 7),  -- Estudiante mensual estudiantes: descuento y eventos
(5, 4, 1, 1), (5, 4, 1, 2), (5, 4, 1, 3), (5, 4, 1, 4), (5, 4, 1, 5), (5, 4, 1, 6), (5, 4, 1, 7);  -- Oro anual: todos beneficios
```



## Consultas SQL Especializadas

```sql

-- Consultas SQL Especializadas 1 // TERMINADO

SELECT pro.name AS Producto, c.name AS Empresa, cm.name AS Ciudad, MIN(cp.price) AS Precio_Minimo
FROM products pro
JOIN companyproducts cp ON pro.id = cp.product_id
JOIN companies c ON cp.company_id = c.id
JOIN citiesormunicipalities cm ON c.city_id = cm.code
GROUP BY pro.id, c.id, cm.code
ORDER BY cm.name, pro.name;

-- Consultas SQL Especializadas 2 // VERIFICAR -> DA EMPTY

SELECT c.name AS Cliente, COUNT(qp.product_id) AS Cantidad_Calificaciones
FROM quality_products qp
JOIN customers c ON qp.customer_id = c.id
WHERE qp.daterating >= DATE_SUB(CURRENT_DATE(), INTERVAL 6 MONTH)
GROUP BY qp.customer_id
ORDER BY Cantidad_Calificaciones DESC
LIMIT 5;

-- Consultas SQL Especializadas 3 // TERMINADO

SELECT cat.description AS Categoria, um.description AS Unidad_Medida, COUNT(pro.id) AS Cantidad_Productos
FROM products pro
JOIN categories cat ON pro.category_id = cat.id
LEFT JOIN companyproducts cp ON pro.id = cp.product_id
LEFT JOIN unitofmeasure um ON cp.unitmeasure_id = um.id
GROUP BY Categoria, Unidad_Medida
ORDER BY Categoria, Unidad_Medida;

-- Consultas SQL Especializadas 4 // TERMINADO

SELECT pro.name AS producto, AVG(qp.rating) AS calificacion_promedio, (SELECT AVG(rating) FROM quality_products) AS promedio_general
FROM products pro
JOIN quality_products qp ON pro.id = qp.product_id
GROUP BY pro.id
HAVING calificacion_promedio > (SELECT AVG(rating) FROM quality_products)
ORDER BY calificacion_promedio DESC;

-- Consultas SQL Especializadas 5 // TERMINADO

SELECT c.id AS ID, c.name AS Compañia
FROM companies c
LEFT JOIN quality_products qp ON c.id = qp.company_id
WHERE qp.company_id IS NULL;

-- Consultas SQL Especializadas 6 // TERMINADO

SELECT pro.name AS Producto, COUNT(DISTINCT f.customer_id) AS Cantidad_Clientes
FROM favorites f
JOIN products pro ON f.product_id = pro.id
GROUP BY pro.id
HAVING cantidad_clientes > 10
ORDER BY cantidad_clientes DESC;

-- Consultas SQL Especializadas 7

SELECT cm.name AS Ciudad, cat.description AS Categoria, c.name AS Compañia, c.cellphone AS Telefono, c.email AS Email
FROM companies c
JOIN citiesormunicipalities cm ON c.city_id = cm.code
JOIN categories cat ON c.category_id = cat.id
ORDER BY cm.name, cat.description, c.name;

-- Consultas SQL Especializadas 8 // PENDIENTE



-- Consultas SQL Especializadas 9 // TERMINADO

SELECT pro.name AS Producto
FROM products pro
JOIN companyproducts cp ON pro.id = cp.product_id
WHERE cp.unitmeasure_id IS NULL;

-- Consultas SQL Especializadas 10 // TERMINADO

SELECT ms.name AS Membresia
FROM memberships ms
LEFT JOIN membershipbenefits mb ON ms.id = mb.membership_id
WHERE mb.membership_id IS NULL;

-- Consultas SQL Especializadas 11 // TERMINADO

SELECT pro.name AS producto, AVG(qp.rating) AS Promedio_Calificacion
FROM products pro
JOIN quality_products qp ON pro.id = qp.product_id
WHERE pro.category_id = 2
GROUP BY pro.id
ORDER BY Promedio_Calificacion DESC;

-- Consultas SQL Especializadas 12 // TERMINADO

SELECT c.name AS Cliente, COUNT(DISTINCT qp.company_id) AS Cantidad_Empresas
FROM customers c
JOIN quality_products qp ON c.id = qp.customer_id
GROUP BY c.id
HAVING Cantidad_Empresas > 1
ORDER BY Cantidad_Empresas DESC;

-- Consultas SQL Especializadas 13 // TERMINADO

SELECT cm.name AS Ciudad, COUNT(c.id) AS Cantidad_Clientes
FROM customers c
JOIN citiesormunicipalities cm ON c.city_id = cm.code
WHERE c.msisactive = TRUE
GROUP BY cm.name
ORDER BY Cantidad_Clientes DESC;

-- Consultas SQL Especializadas 14 // TERMINADO

SELECT c.name AS empresa, pro.name AS producto, AVG(qp.rating) AS calificacion_promedio, RANK() OVER (PARTITION BY c.name ORDER BY AVG(qp.rating) DESC) AS ranking
FROM quality_products qp
JOIN companies c ON qp.company_id = c.id
JOIN products pro ON qp.product_id = pro.id
GROUP BY c.id, pro.id
ORDER BY c.name, ranking;

-- Consultas SQL Especializadas 15 // TERMINADO

SELECT c.name AS empresa, COUNT(DISTINCT cp.product_id) AS cantidad_productos
FROM companies c
JOIN companyproducts cp ON c.id = cp.company_id
GROUP BY c.id
HAVING cantidad_productos > 5
ORDER BY cantidad_productos DESC;

-- Consultas SQL Especializadas 16 // TERMINADO

SELECT c.name AS Cliente, p.name AS Producto_Favorito
FROM favorites f
JOIN customers c ON f.customer_id = c.id
JOIN products p ON f.product_id = p.id
LEFT JOIN quality_products qp ON f.product_id = qp.product_id AND f.customer_id = qp.customer_id
WHERE qp.product_id IS NULL;

-- Consultas SQL Especializadas 17 // TERMINADO

SELECT a.description AS Audiencia, b.descriptions AS Beneficio, b.detail AS Descripcion_Beneficio
FROM audiencebenefits ab
JOIN audiences a ON ab.audience_id = a.id
JOIN benefits b ON ab.benefit_id = b.id
ORDER BY a.description, b.descriptions;

-- Consultas SQL Especializadas 18 // TERMINADO

SELECT cm.name AS Ciudad, c.name AS Compañia
FROM companies c
JOIN citiesormunicipalities cm ON c.city_id = cm.code
LEFT JOIN companyproducts cp ON c.id = cp.company_id
WHERE cp.company_id IS NULL;

-- Consultas SQL Especializadas 19 // TERMINADO

SELECT c.name AS empresa, p.name AS producto_duplicado, COUNT(*) AS cantidad
FROM companyproducts cp
JOIN companies c ON cp.company_id = c.id
JOIN products p ON cp.product_id = p.id
GROUP BY c.id, p.name
HAVING cantidad > 1;

-- Consultas SQL Especializadas 20 // TERMINADO

SELECT c.name AS Cliente, COUNT(DISTINCT f.product_id) AS Productos_Favoritos, COUNT(DISTINCT qp.product_id) AS Productos_Calificados, AVG(qp.rating) AS Calificacion_Promedio
FROM customers c
LEFT JOIN 
    favorites f ON c.id = f.customer_id
LEFT JOIN 
    quality_products qp ON c.id = qp.customer_id
GROUP BY 
    c.id
ORDER BY 
    Productos_Favoritos DESC, Calificacion_Promedio DESC;
```



## Subconsultas

```sql

-- Subconsultas 1 // TERMINADO

SELECT p.name AS Producto, p.price, cat.description AS Categoria, (SELECT AVG(price) FROM products WHERE category_id = p.category_id) AS promedio_categoria
FROM products p
JOIN categories cat ON p.category_id = cat.id
WHERE p.price > (SELECT AVG(price) FROM products WHERE category_id = p.category_id)
ORDER BY cat.description, p.price DESC;

-- Subconsultas 2 // TERMINADO

SELECT c.name AS Compañia, COUNT(cp.product_id) AS Cantidad_Productos
FROM companies c
JOIN companyproducts cp ON c.id = cp.company_id
GROUP BY c.id
HAVING cantidad_productos > (SELECT AVG(product_count) 
FROM (SELECT COUNT(product_id) AS product_count 
FROM companyproducts 
GROUP BY company_id) AS avg_counts)
ORDER BY cantidad_productos DESC;

-- Subconsultas 3 // TERMINADO

SELECT DISTINCT p.name AS Producto_Favorito, (SELECT AVG(rating) FROM quality_products WHERE product_id = f.product_id) AS Calificacion_Promedio
FROM favorites f
JOIN products p ON f.product_id = p.id
WHERE f.customer_id = 6 AND EXISTS (SELECT 1 FROM quality_products qp 
WHERE qp.product_id = f.product_id AND qp.customer_id != 6);

-- Subconsultas 4 // TERMINADO

SELECT p.name AS Producto, (SELECT COUNT(*) FROM favorites WHERE product_id = p.id) AS Veces_Favorito
FROM products p
ORDER BY Veces_Favorito DESC
LIMIT 10;

-- Subconsultas 5 // TERMINADO

SELECT name, email
FROM customers
WHERE email NOT IN (SELECT DISTINCT c.email FROM customers c JOIN quality_products qp ON c.id = qp.customer_id)
AND email NOT IN (SELECT DISTINCT c.email FROM customers c JOIN rates r ON c.id = r.customer_id);

-- Subconsultas 6 // TERMINADO

SELECT p.id AS Producto_ID, p.name AS Producto, c.description AS Categoria, ROUND(producto_rating.promedio, 2) AS Calificacion_Producto, ROUND(categoria_minimo.minimo_categoria, 2) AS Minimo_Categoria
FROM products p
JOIN categories c ON p.category_id = c.id
JOIN (SELECT product_id, AVG(rating) AS promedio FROM quality_products GROUP BY product_id) AS producto_rating ON p.id = producto_rating.product_id
JOIN (SELECT p.category_id, MIN(avg_rating) AS minimo_categoria FROM (SELECT product_id, AVG(rating) AS avg_rating FROM quality_products GROUP BY product_id) AS promedios JOIN products p ON promedios.product_id = p.id GROUP BY p.category_id
) AS categoria_minimo ON p.category_id = categoria_minimo.category_id
WHERE producto_rating.promedio < categoria_minimo.minimo_categoria
ORDER BY c.description, (categoria_minimo.minimo_categoria - producto_rating.promedio) DESC;

-- Subconsultas 7 // TERMINADO

SELECT cm.name AS Ciudad
FROM citiesormunicipalities cm
WHERE cm.code NOT IN (SELECT DISTINCT city_id FROM customers WHERE city_id IS NOT NULL);

-- Subconsultas 8 // TERMINADO

SELECT p.name AS Producto
FROM products p
WHERE p.id NOT IN (SELECT DISTINCT product_id FROM quality_products);

-- Subconsultas 9 // TERMINADO

SELECT descriptions AS Beneficio
FROM benefits
WHERE id NOT IN (SELECT DISTINCT benefit_id FROM audiencebenefits);

-- Subconsultas 10 // TERMINADO

SELECT p.name AS producto_favorito
FROM favorites f
JOIN products p ON f.product_id = p.id
WHERE f.customer_id = 6 AND p.id NOT IN (SELECT DISTINCT product_id FROM companyproducts);

-- Subconsultas 11 // TERMINADO

SELECT p.name AS producto,c.name AS empresa,cm.name AS ciudad
FROM products p
JOIN companyproducts cp ON p.id = cp.product_id
JOIN companies c ON cp.company_id = c.id
JOIN citiesormunicipalities cm ON c.city_id = cm.code
WHERE cm.code IN (SELECT city_id FROM companies GROUP BY city_id HAVING COUNT(*) < 3);

-- Subconsultas 12 // TERMINADO

SELECT p.name AS Producto, AVG(qp.rating) AS Calificacion_Promedio, (SELECT AVG(rating) FROM quality_products) AS Promedio_General
FROM products p
JOIN quality_products qp ON p.id = qp.product_id
GROUP BY p.id
HAVING calificacion_promedio > (SELECT AVG(rating) FROM quality_products)
ORDER BY calificacion_promedio DESC;

-- Subconsultas 13 // TERMINADO

SELECT c.name AS empresa, COUNT(DISTINCT p.category_id) AS Categorias_Distintas
FROM companies c
JOIN companyproducts cp ON c.id = cp.company_id
JOIN products p ON cp.product_id = p.id
GROUP BY c.id
HAVING Categorias_Distintas = 1;

-- Subconsultas 14 // TERMINADO

SELECT p.name AS Producto, MAX(cp.price) AS Precio_Maximo
FROM products p
JOIN companyproducts cp ON p.id = cp.product_id
GROUP BY p.id
HAVING MAX(cp.price) = (SELECT MAX(price) FROM companyproducts)
ORDER BY Precio_Maximo DESC;

-- Subconsultas 15 // TERMINADO

SELECT DISTINCT p.name AS Producto_Favorito
FROM favorites f
JOIN products p ON f.product_id = p.id
WHERE f.customer_id = 6 AND EXISTS (SELECT 1 FROM quality_products qp WHERE qp.product_id = f.product_id AND qp.customer_id != 6 AND qp.rating > 4);

-- Subconsultas 16 // TERMINADO

SELECT p.name AS Producto
FROM products p
JOIN quality_products qp ON p.id = qp.product_id
WHERE p.image IS NULL OR p.image = '';

-- Subconsultas 17 // TERMINADO

SELECT m.name AS Membresia
FROM memberships m
WHERE m.id NOT IN (SELECT DISTINCT membership_id FROM membershipperiods);

-- Subconsultas 18 // TERMINADO

SELECT b.descriptions AS Beneficio, COUNT(ab.audience_id) AS Audiencias_Asociadas
FROM benefits b
JOIN audiencebenefits ab ON b.id = ab.benefit_id
GROUP BY b.id
HAVING COUNT(ab.audience_id) > 1
ORDER BY Audiencias_Asociadas DESC;

-- Subconsultas 19 // TERMINADO

SELECT DISTINCT c.name AS Compañia
FROM companies c
JOIN companyproducts cp ON c.id = cp.company_id
WHERE cp.unitmeasure_id IS NULL;

-- Subconsultas 20 // TERMINADO

SELECT c.name AS Cliente, m.name AS Membresia
FROM customers c
JOIN memberships m ON c.membership_id = m.id
WHERE c.msisactive = TRUE AND c.id NOT IN (SELECT DISTINCT customer_id FROM favorites);
```



## Funciones Agregadas

```sql
```



## Procedimientos Almacenados

```sql

-- Procedimientos Almacenados 1



-- Procedimientos Almacenados 2



-- Procedimientos Almacenados 3



-- Procedimientos Almacenados 4



-- Procedimientos Almacenados 5



-- Procedimientos Almacenados 6



-- Procedimientos Almacenados 7



-- Procedimientos Almacenados 8



-- Procedimientos Almacenados 9



-- Procedimientos Almacenados 10



-- Procedimientos Almacenados 11



-- Procedimientos Almacenados 12



-- Procedimientos Almacenados 13



-- Procedimientos Almacenados 14



-- Procedimientos Almacenados 15



-- Procedimientos Almacenados 16



-- Procedimientos Almacenados 17



-- Procedimientos Almacenados 18



-- Procedimientos Almacenados 19



-- Procedimientos Almacenados 20



```



## Triggers

```sql

-- TRIGGER 1



-- TRIGGER 2



-- TRIGGER 3



-- TRIGGER 4 // TERMINADO

DELIMITER $$
CREATE TRIGGER trg_validar_calificacion
BEFORE INSERT ON rates
FOR EACH ROW
BEGIN
    IF NEW.rating > 5 THEN
        SIGNAL SQLSTATE '45000' 
        SET MESSAGE_TEXT = 'La calificación no puede ser mayor a 5';
    END IF;
END $$
DELIMITER ;

-- TRIGGER 5



-- TRIGGER 6 // TERMINADO

DELIMITER $$
CREATE TRIGGER trg_evitar_duplicados_producto_empresa
BEFORE INSERT ON companyproducts
FOR EACH ROW
BEGIN
    DECLARE v_count INT;
    SELECT COUNT(*) INTO v_count
    FROM companyproducts
    WHERE company_id = NEW.company_id AND product_id = NEW.product_id;
    IF v_count > 0 THEN
        SIGNAL SQLSTATE '45000' 
        SET MESSAGE_TEXT = 'Este producto ya está asociado a la empresa';
    END IF;
END $$
DELIMITER ;

-- TRIGGER 7 // TERMINADO

DELIMITER $$
CREATE TRIGGER trg_notificar_favorito
AFTER INSERT ON details_favorites
FOR EACH ROW
BEGIN
    INSERT INTO notificaciones (usuario_id, mensaje, tipo, fecha)
    VALUES (
        (SELECT customer_id FROM favorites WHERE id = NEW.favorite_id),
        CONCAT('Producto añadido a favoritos: ', 
              (SELECT name FROM products WHERE id = NEW.product_id)),
        'favorito',
        NOW()
    );
END $$
DELIMITER ;

-- TRIGGER 8



-- TRIGGER 9 // TERMINADO

DELIMITER $$
CREATE TRIGGER trg_eliminar_favoritos_producto
AFTER DELETE ON products
FOR EACH ROW
BEGIN
    DELETE FROM details_favorites 
    WHERE product_id = OLD.id;
    DELETE FROM favorites 
    WHERE product_id = OLD.id;
END $$
DELIMITER ;

-- TRIGGER 10 // TERMINADO

DELIMITER $$
CREATE TRIGGER trg_bloquear_audiencias_activas
BEFORE UPDATE ON audiences
FOR EACH ROW
BEGIN
    DECLARE v_en_uso INT;
    SELECT COUNT(*) INTO v_en_uso
    FROM customers
    WHERE audience_id = OLD.id;
    IF v_en_uso > 0 THEN
        SIGNAL SQLSTATE '45000' 
        SET MESSAGE_TEXT = 'No se puede modificar una audiencia en uso';
    END IF;
END $$
DELIMITER ;

-- TRIGGER 11 // TERMINADO

DELIMITER $$
CREATE TRIGGER trg_recalcular_promedio_calidad
AFTER INSERT ON quality_products
FOR EACH ROW
BEGIN
    UPDATE products
    SET average_rating = (
        SELECT AVG(rating)
        FROM quality_products
        WHERE product_id = NEW.product_id
    )
    WHERE id = NEW.product_id;
END $$
DELIMITER ;

-- TRIGGER 12 // TERMINADO

DELIMITER $$
CREATE TRIGGER trg_log_asignacion_beneficio
AFTER INSERT ON membershipbenefits
FOR EACH ROW
BEGIN
    INSERT INTO bitacora (tabla, accion, descripcion, fecha)
    VALUES (
        'membershipbenefits',
        'INSERT',
        CONCAT('Beneficio ', NEW.benefit_id, ' asignado a membresía ', NEW.membership_id),
        NOW()
    );
END $$
DELIMITER ;

-- TRIGGER 13



-- TRIGGER 14 // TERMINADO

DELIMITER $$
CREATE TRIGGER trg_validar_email_unico
BEFORE INSERT ON customers
FOR EACH ROW
BEGIN
    DECLARE v_count INT;
    SELECT COUNT(*) INTO v_count
    FROM customers
    WHERE email = NEW.email;
    IF v_count > 0 THEN
        SIGNAL SQLSTATE '45000' 
        SET MESSAGE_TEXT = 'El correo electrónico ya está registrado';
    END IF;
END $$
DELIMITER ;

-- TRIGGER 15 // TERMINADO

DELIMITER $$
CREATE TRIGGER trg_eliminar_detalles_favoritos
AFTER DELETE ON favorites
FOR EACH ROW
BEGIN
    DELETE FROM details_favorites 
    WHERE favorite_id = OLD.id;
END $$
DELIMITER ;

-- TRIGGER 16



-- TRIGGER 17 // TERMINADO

DELIMITER $$
CREATE TRIGGER trg_proteger_ciudad_con_empresas
BEFORE DELETE ON citiesormunicipalities
FOR EACH ROW
BEGIN
    DECLARE v_count INT;
    
    SELECT COUNT(*) INTO v_count
    FROM companies
    WHERE city_id = OLD.code;
    
    IF v_count > 0 THEN
        SIGNAL SQLSTATE '45000' 
        SET MESSAGE_TEXT = 'No se puede eliminar una ciudad con empresas registradas';
    END IF;
END $$
DELIMITER ;

-- TRIGGER 18 // TERMINADO

DELIMITER $$
CREATE TRIGGER trg_log_cambio_estado_encuesta
AFTER UPDATE ON polls
FOR EACH ROW
BEGIN
    IF NEW.isactive <> OLD.isactive THEN
        INSERT INTO log_encuestas (encuesta_id, estado_anterior, estado_nuevo, fecha_cambio)
        VALUES (NEW.id, OLD.isactive, NEW.isactive, NOW());
    END IF;
END $$
DELIMITER ;

-- TRIGGER 19



-- TRIGGER 20 // TERMINADO

DELIMITER $$
CREATE TRIGGER trg_eliminar_productos_sin_empresa
AFTER DELETE ON companyproducts
FOR EACH ROW
BEGIN
    DECLARE v_count INT;
    
    SELECT COUNT(*) INTO v_count
    FROM companyproducts
    WHERE product_id = OLD.product_id;
    
    IF v_count = 0 THEN
        DELETE FROM products 
        WHERE id = OLD.product_id
        AND id NOT IN (SELECT product_id FROM quality_products);
    END IF;
END $$
DELIMITER ;
```



## Events (Eventos Programados..Usar procedimientos o funciones para cada evento)

```sql
```



## Historias de Usuario con JOINs

```sql
-- JOIN 1 // TERMINADO

SELECT co.name AS Compañia, pro.name AS Producto, pro.price AS Precio
FROM companies co
JOIN companyproducts cp ON co.id = cp.company_id
JOIN products AS pro ON cp.product_id = pro.id;

-- JOIN 2 // TERMINADO

SELECT p.name AS producto, c.name AS empresa, cat.description AS categoria
FROM favorites f
JOIN details_favorites df ON f.id = df.favorite_id
JOIN products p ON df.product_id = p.id
JOIN companyproducts cp ON p.id = cp.product_id
JOIN companies c ON cp.company_id = c.id
JOIN categories cat ON p.category_id = cat.id
WHERE f.customer_id = 6;

-- JOIN 3 // TERMINADO

SELECT co.name AS Compañia, pro.name AS Producto
FROM companies co
LEFT JOIN companyproducts cp ON co.id = cp.company_id
LEFT JOIN products pro ON cp.product_id = pro.id;

-- JOIN 4 // TERMINADO

SELECT pro.name AS Producto, qp.rating AS Calificacion
FROM quality_products qp
RIGHT JOIN products pro ON qp.product_id = pro.id;

-- JOIN 5 // TERMINADO

SELECT pro.name AS Producto, (SELECT AVG(rating) FROM quality_products) AS Promedio, co.name AS Compañia
FROM quality_products qp
JOIN products pro ON qp.product_id = pro.id
JOIN companies co ON qp.company_id = co.id
GROUP BY pro.name, co.name;

-- JOIN 6 // TERMINADO

SELECT cu.name AS Cliente, r.rating
FROM customers cu
LEFT JOIN rates r ON cu.id = r.customer_id;

-- JOIN 7 // TERMINADO

SELECT p.id AS ID, p.name AS Producto, MAX(qp.rating) AS Ultima_Calificacion
FROM favorites f
JOIN details_favorites df ON f.id = df.favorite_id
JOIN products p ON df.product_id = p.id
LEFT JOIN quality_products qp ON p.id = qp.product_id AND f.customer_id = qp.customer_id
WHERE f.customer_id = 6
GROUP BY p.id, p.name
ORDER BY p.name;

-- JOIN 8 // TERMINADO

SELECT m.name AS Membresia, b.descriptions AS Beneficio
FROM memberships m
JOIN membershipbenefits mb ON m.id = mb.membership_id
JOIN benefits b ON mb.benefit_id = b.id;

-- JOIN 9 // TERMINADO

SELECT cu.name AS Cliente, m.name AS Membresia, m.description AS Beneficios
FROM customers cu
JOIN memberships m ON cu.membership_id = m.id
WHERE msisactive = TRUE;

-- JOIN 10 // TERMINADO

SELECT cm.name AS Ciudad, COUNT(co.id) AS Cantidad_Empresas
FROM citiesormunicipalities cm
LEFT JOIN companies co ON cm.code = co.city_id
GROUP BY cm.name;

-- JOIN 11 // TERMINADO

SELECT cu.name AS Cliente, po.name AS Encuesta
FROM customers cu
JOIN rates ON cu.id = rates.customer_id
JOIN polls po ON rates.poll_id = po.id;

-- JOIN 12 // TERMINADO

SELECT cu.name AS Cliente, pro.name AS Producto, qp.rating AS Calificacion
FROM quality_products qp
JOIN customers cu ON qp.customer_id = cu.id
JOIN products pro ON qp.product_id = pro.id;

-- JOIN 13 // TERMINADO

SELECT pro.name AS Producto, a.description AS Audiencia
FROM products pro
JOIN companyproducts cp ON pro.id = cp.product_id
JOIN companies c ON cp.company_id = c.id
JOIN audiences a ON c.audience_id = a.id;

-- JOIN 14 // TERMINADO

SELECT cu.name AS Cliente, pro.name AS Producto_Favorito
FROM customers cu
JOIN favorites f ON cu.id = f.customer_id
JOIN details_favorites df ON f.id = df.favorite_id
JOIN products pro ON df.product_id = pro.id;

-- JOIN 15 // TERMINADO

SELECT ms.name AS Membresia, pe.name AS Periodo, b.descriptions AS Beneficio
FROM memberships ms
JOIN membershipperiods mp ON ms.id = mp.membership_id
JOIN periods pe ON mp.period_id = pe.id
JOIN membershipbenefits mb ON ms.id = mb.membership_id AND pe.id = mb.period_id
JOIN benefits b ON mb.benefit_id = b.id;

-- JOIN 16 // TERMINADO

SELECT c.name AS Cliente, p.name AS Producto, co.name AS Compañia, r.rating AS Calificacion, r.daterating AS Fecha_Calificacion
FROM rates r
JOIN customers c ON r.customer_id = c.id
JOIN products p ON r.poll_id = p.id
JOIN companyproducts cp ON p.id = cp.product_id
JOIN companies co ON cp.company_id = co.id;

-- JOIN 17 // TERMINADO

SELECT pro.name AS Producto_Favorito, r.rating AS Calificacion
FROM details_favorites df
JOIN products pro ON df.product_id = pro.id
LEFT JOIN rates r ON pro.id = r.poll_id AND df.favorite_id IN (
    SELECT id FROM favorites WHERE customer_id = 2
)
WHERE df.favorite_id IN (SELECT id FROM favorites WHERE customer_id = 2);

-- JOIN 18 // TERMINADO

SELECT pro.name AS Producto, cat.description AS Categoria
FROM products pro
JOIN categories cat ON pro.category_id = cat.id;

-- JOIN 19 // TERMINADO

SELECT a.description AS Audiencia, b.descriptions AS Beneficio
FROM audiences a
LEFT JOIN audiencebenefits ab ON a.id = ab.audience_id
LEFT JOIN benefits b ON ab.benefit_id = b.id;

-- JOIN 20 // TERMINADO

SELECT c.name AS Cliente, pro.name AS Producto, po.name AS Encuesta, r.rating AS Calificacion, r.daterating AS Fecha
FROM rates r
JOIN customers c ON r.customer_id = c.id
JOIN products pro ON r.poll_id = pro.id
JOIN polls po ON r.poll_id = po.id
JOIN companyproducts cp ON pro.id = cp.product_id
JOIN companies co ON cp.company_id = co.id;

```



## Historias de Usuario con Funciones Definidas por el Usuario (UDF)

```sql
```

