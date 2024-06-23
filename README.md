-- Drop existing database if it exists
USE master;
ALTER DATABASE FlightStream SET SINGLE_USER WITH ROLLBACK IMMEDIATE;
DROP DATABASE IF EXISTS FlightStream;
GO

-- Create the database
CREATE DATABASE FlightStream;
GO

-- Use the FlightStream Database
--USE FlightStream;
GO

-- Create Employee table
CREATE TABLE Employee (
    EmployeeID INT PRIMARY KEY,
    [Name] VARCHAR(100) NOT NULL,
    DateOfBirth DATE NOT NULL,
    Phone VARCHAR(15),
    Email VARCHAR(100) UNIQUE NOT NULL,
    Branch VARCHAR(255),
    ContractType VARCHAR(255)
);
GO

-- Create Salesperson Table
CREATE TABLE Salesperson (
    SalespersonEmployeeID INT PRIMARY KEY,
    EmployeeID INT,
    FOREIGN KEY (EmployeeID) REFERENCES Employee(EmployeeID)
);
GO

-- Create AdminExecutive Table
CREATE TABLE AdminExecutive (
    AdminExecutiveEmployeeID INT PRIMARY KEY,
    EmployeeID INT,
    FOREIGN KEY (EmployeeID) REFERENCES Employee(EmployeeID)
);
GO

-- Create Technician Table
CREATE TABLE Technician (
    TechnicianEmployeeID INT PRIMARY KEY,
    EmployeeID INT,
    FOREIGN KEY (EmployeeID) REFERENCES Employee(EmployeeID)
);
GO

-- Create Subscriber Table
CREATE TABLE Subscriber (
    SubscriberID INT PRIMARY KEY,
    [Name] VARCHAR(100) NOT NULL,
    DateOfBirth DATE NOT NULL,
    Phone VARCHAR(15),
    Email VARCHAR(100) UNIQUE NOT NULL
);
GO

-- Create Zone Table
CREATE TABLE [Zone] (
    ZoneID INT PRIMARY KEY,
    [Type] VARCHAR(255) NOT NULL
);
GO

-- Create DataScoop Table
CREATE TABLE DataScoop (
    DataScoopID INT PRIMARY KEY,
    ZoneID INT,
    FOREIGN KEY (ZoneID) REFERENCES [Zone](ZoneID)
);
GO

-- Create Address Table
CREATE TABLE [Address] (
    AddressID INT PRIMARY KEY,
    SubscriberID INT NULL,
    EmployeeID INT NULL,
    StreetNum VARCHAR(50) NOT NULL,
    Street VARCHAR(255) NOT NULL,
    Suburb VARCHAR(255) NOT NULL,
    City VARCHAR(255) NOT NULL,
    Region VARCHAR(255) NOT NULL,
    PostCode INT NOT NULL,
    Country VARCHAR(255) NOT NULL,
    FOREIGN KEY (SubscriberID) REFERENCES Subscriber(SubscriberID),
    FOREIGN KEY (EmployeeID) REFERENCES Employee(EmployeeID)
);
GO

-- Create Company Table
CREATE TABLE Company (
    CompanyID INT PRIMARY KEY,
    CompanyName VARCHAR(100) NOT NULL,
    Phone VARCHAR(15),
    Email VARCHAR(100) UNIQUE NOT NULL
);
GO

-- Create CompanyAddress Table
CREATE TABLE CompanyAddress (
    CompanyID INT PRIMARY KEY,
    StreetNum VARCHAR(50) NOT NULL,
    Street VARCHAR(255) NOT NULL,
    Suburb VARCHAR(255) NOT NULL,
    City VARCHAR(255) NOT NULL,
    Region VARCHAR(255) NOT NULL,
    PostCode INT NOT NULL,
    Country VARCHAR(255) NOT NULL,
    FOREIGN KEY (CompanyID) REFERENCES Company(CompanyID)
);
GO

-- Create Supplier Table
CREATE TABLE Supplier (
    SupplierID INT PRIMARY KEY,
    SupplierName VARCHAR(100) NOT NULL,
    Phone VARCHAR(15),
    Email VARCHAR(100) UNIQUE NOT NULL
);
GO

-- Create SupplierAddress Table
CREATE TABLE SupplierAddress (
    SupplierID INT PRIMARY KEY,
    StreetNum VARCHAR(50) NOT NULL,
    Street VARCHAR(255) NOT NULL,
    Suburb VARCHAR(255) NOT NULL,
    City VARCHAR(255) NOT NULL,
    Region VARCHAR(255) NOT NULL,
    PostCode INT NOT NULL,
    Country VARCHAR(255) NOT NULL,
    FOREIGN KEY (SupplierID) REFERENCES Supplier(SupplierID)
);
GO

-- Create Part Table
CREATE TABLE Part (
    PartID INT PRIMARY KEY,
    PartName VARCHAR(100) NOT NULL,
    Specification VARCHAR(255) NOT NULL,
    Cost DECIMAL(10, 2) CHECK (Cost BETWEEN 0.00 AND 100000.00) NOT NULL,
    SupplierID INT,
    FOREIGN KEY (SupplierID) REFERENCES Supplier(SupplierID)
);
GO

-- Create DataStore Table
CREATE TABLE DataStore (
    DataStoreID INT PRIMARY KEY,
    AmbientLight DECIMAL(19, 0) CHECK (AmbientLight BETWEEN 0.00 AND 100000.00) NOT NULL,
    Temperature DECIMAL(19, 0) CHECK (Temperature BETWEEN 0.00 AND 100000.00) NOT NULL,
    OrganicSpectral DECIMAL(19, 0) CHECK (OrganicSpectral BETWEEN 0.00 AND 100000.00) NOT NULL,
    Humidty DECIMAL(19, 0) CHECK (Humidty BETWEEN 0.00 AND 100000.00) NOT NULL,
    DataScoopID INT,
    FOREIGN KEY (DataScoopID) REFERENCES DataScoop(DataScoopID)
);
GO

-- Create VideoStream Table
CREATE TABLE VideoStream (
    VideoID INT PRIMARY KEY,
    VideoStream VARCHAR(255) NOT NULL,
    DataScoopID INT,
    FOREIGN KEY (DataScoopID) REFERENCES DataScoop(DataScoopID)
);
GO

-- Create Location Table
CREATE TABLE [Location] (
    LocationID INT PRIMARY KEY,
    Longitude DECIMAL(19, 0) CHECK (Longitude BETWEEN -180.00 AND 180.00) NOT NULL,
    Latitude DECIMAL(19, 0) CHECK (Latitude BETWEEN -90.00 AND 90.00) NOT NULL,
    Altitude DECIMAL(19, 0) NOT NULL,
    DataScoopID INT,
    ZoneID INT,
    FOREIGN KEY (DataScoopID) REFERENCES DataScoop(DataScoopID),
    FOREIGN KEY (ZoneID) REFERENCES [Zone](ZoneID)
);
GO

-- Create Subscription Table
CREATE TABLE Subscription (
    SubscriptionID INT PRIMARY KEY,
    Cost DECIMAL(10,2) CHECK (Cost BETWEEN 0.00 AND 100000.00) NOT NULL,
    Duration INT NOT NULL,
    AccessLevel VARCHAR(50) NOT NULL,
	Discount INT NOT NULL,
    VideoID INT,
    SalespersonEmployeeID INT,
    AdminExecutiveEmployeeID INT,
    FOREIGN KEY (VideoID) REFERENCES VideoStream(VideoID),
    FOREIGN KEY (SalespersonEmployeeID) REFERENCES Salesperson(SalespersonEmployeeID),
    FOREIGN KEY (AdminExecutiveEmployeeID) REFERENCES AdminExecutive(AdminExecutiveEmployeeID)
);
GO

-- Create GoldSubscription Table
CREATE TABLE GoldSubscription (
    SubscriptionID INT PRIMARY KEY,
    VideoID INT,
    FOREIGN KEY (SubscriptionID) REFERENCES Subscription(SubscriptionID),
    FOREIGN KEY (VideoID) REFERENCES VideoStream(VideoID)
);
GO

-- Create PlatinumSubscription Table
CREATE TABLE PlatinumSubscription (
    SubscriptionID INT PRIMARY KEY,
    VideoID INT,
    DataStoreID INT,
    FOREIGN KEY (SubscriptionID) REFERENCES Subscription(SubscriptionID),
    FOREIGN KEY (VideoID) REFERENCES VideoStream(VideoID),
    FOREIGN KEY (DataStoreID) REFERENCES DataStore(DataStoreID)
);
GO

-- Create SuperPlatinumSubscription Table
CREATE TABLE SuperPlatinumSubscription (
    SubscriptionID INT PRIMARY KEY,
    VideoID INT,
    DataStoreID INT,
    ZoneID INT,
    FOREIGN KEY (SubscriptionID) REFERENCES Subscription(SubscriptionID),
    FOREIGN KEY (VideoID) REFERENCES VideoStream(VideoID),
    FOREIGN KEY (DataStoreID) REFERENCES DataStore(DataStoreID),
    FOREIGN KEY (ZoneID) REFERENCES [Zone](ZoneID)
);
GO

-- Create Contract Table
CREATE TABLE [Contract] (
    ContractID INT PRIMARY KEY,
    StartDate DATE NOT NULL,
    EndDate DATE NOT NULL,
    ContractForm VARBINARY(MAX) NOT NULL,
    AdminExecutiveEmployeeID INT,
    SubscriberID INT,
    SubscriptionID INT,
	DataScoopID INT,
    FOREIGN KEY (AdminExecutiveEmployeeID) REFERENCES AdminExecutive(AdminExecutiveEmployeeID),
    FOREIGN KEY (SubscriberID) REFERENCES Subscriber(SubscriberID),
    FOREIGN KEY (SubscriptionID) REFERENCES Subscription(SubscriptionID),
	FOREIGN KEY (DataScoopID) REFERENCES DataScoop(DataScoopID)
);
GO

-- Create Maintenance Table
CREATE TABLE Maintenance (
    MaintenanceID INT PRIMARY KEY,
    ReportDate DATE NOT NULL,
    NextReportDate DATE NOT NULL,
    DataScoopID INT,
    TechnicianEmployeeID INT,
    FOREIGN KEY (DataScoopID) REFERENCES DataScoop(DataScoopID),
    FOREIGN KEY (TechnicianEmployeeID) REFERENCES Technician(TechnicianEmployeeID)
);
GO

-- Create Invoice Table
CREATE TABLE Invoice (
    InvoiceID INT PRIMARY KEY,
    DateIssued DATE NOT NULL,
    DateDue DATE NOT NULL,
    Amount DECIMAL(19, 0) CHECK (Amount BETWEEN 0.00 AND 100000.00) NOT NULL,
    SupplierID INT,
    PartID INT,
    TechnicianEmployeeID INT,
	DataScoopID INT,
    FOREIGN KEY (SupplierID) REFERENCES Supplier(SupplierID),
    FOREIGN KEY (PartID) REFERENCES Part(PartID),
    FOREIGN KEY (TechnicianEmployeeID) REFERENCES Technician(TechnicianEmployeeID),
	FOREIGN KEY (DataScoopID) REFERENCES DataScoop(DataScoopID)
);
GO

-- Insert mock data into Employee table
INSERT INTO Employee (EmployeeID, [Name], DateOfBirth, Phone, Email, Branch, ContractType)
VALUES 
(1, 'John Doe', '1985-01-01', '(123) 456-7890', 'john.doe@example.com', 'New York', 'Full-Time'),
(2, 'Jane Smith', '1990-02-02', '(321) 654-0987', 'jane.smith@example.com', 'Los Angeles', 'Part-Time'),
(3, 'Jim Brown', '1980-03-03', '(987) 654-3210', 'jim.brown@example.com', 'Chicago', 'Contract'),
(4, 'Sarah Connor', '1985-05-05', '(123) 456-1234', 'sarah.connor@example.com', 'Houston', 'Full-Time'),
(5, 'Michael Johnson', '1990-06-06', '(321) 654-4321', 'michael.johnson@example.com', 'Phoenix', 'Part-Time'),
(6, 'Emily Davis', '1980-07-07', '(987) 654-5678', 'emily.davis@example.com', 'Philadelphia', 'Contract'),
(7, 'Matthew Miller', '1985-08-08', '(123) 456-7899', 'matthew.miller@example.com', 'San Antonio', 'Full-Time'),
(8, 'Ashley Wilson', '1990-09-09', '(321) 654-6789', 'ashley.wilson@example.com', 'San Diego', 'Part-Time'),
(9, 'Joshua Moore', '1980-10-10', '(987) 654-7890', 'joshua.moore@example.com', 'Dallas', 'Contract'),
(10, 'Amanda Taylor', '1985-11-11', '(123) 456-8901', 'amanda.taylor@example.com', 'San Jose', 'Full-Time'),
(11, 'Daniel Anderson', '1990-12-12', '(321) 654-9012', 'daniel.anderson@example.com', 'Austin', 'Part-Time'),
(12, 'Olivia Thomas', '1980-01-13', '(987) 654-0123', 'olivia.thomas@example.com', 'Jacksonville', 'Contract'),
(13, 'James Jackson', '1985-02-14', '(123) 456-2345', 'james.jackson@example.com', 'Fort Worth', 'Full-Time'),
(14, 'Sophia White', '1990-03-15', '(321) 654-3456', 'sophia.white@example.com', 'Columbus', 'Part-Time'),
(15, 'David Harris', '1980-04-16', '(987) 654-4567', 'david.harris@example.com', 'Charlotte', 'Contract'),
(16, 'Grace Clark', '1985-05-17', '(123) 456-5678', 'grace.clark@example.com', 'Indianapolis', 'Full-Time'),
(17, 'Benjamin Lewis', '1990-06-18', '(321) 654-6789', 'benjamin.lewis@example.com', 'San Francisco', 'Part-Time'),
(18, 'Zoe Robinson', '1980-07-19', '(987) 654-7890', 'zoe.robinson@example.com', 'Seattle', 'Contract'),
(19, 'Liam Martinez', '1985-08-20', '(123) 456-8901', 'liam.martinez@example.com', 'Denver', 'Full-Time'),
(20, 'Charlotte Walker', '1990-09-21', '(321) 654-9012', 'charlotte.walker@example.com', 'Washington', 'Part-Time');
GO

-- Insert mock data into Salesperson table
INSERT INTO Salesperson (SalespersonEmployeeID, EmployeeID)
VALUES 
(1, 1),
(2, 2),
(3, 3),
(4, 4),
(5, 5);
GO

-- Insert mock data into AdminExecutive table
INSERT INTO AdminExecutive (AdminExecutiveEmployeeID, EmployeeID)
VALUES 
(6, 6),
(7, 7),
(8, 8),
(9, 9),
(10, 10);
GO

-- Insert mock data into Technician table
INSERT INTO Technician (TechnicianEmployeeID, EmployeeID)
VALUES 
(11, 11),
(12, 12),
(13, 13),
(14, 14),
(15, 15);
GO

-- Insert mock data into Subscriber table
INSERT INTO Subscriber (SubscriberID, [Name], DateOfBirth, Phone, Email)
VALUES 
(1, 'Alice Johnson', '1990-01-01', '(123) 456-7890', 'alice.johnson@example.com'),
(2, 'Bob Smith', '1992-02-02', '(234) 567-8901', 'bob.smith@example.com'),
(3, 'Charlie Brown', '1985-03-03', '(345) 678-9012', 'charlie.brown@example.com'),
(4, 'David Wilson', '1988-04-04', '(456) 789-0123', 'david.wilson@example.com'),
(5, 'Eve Davis', '1995-05-05', '(567) 890-1234', 'eve.davis@example.com'),
(6, 'Frank Miller', '1991-06-06', '(678) 901-2345', 'frank.miller@example.com'),
(7, 'Grace Lee', '1987-07-07', '(789) 012-3456', 'grace.lee@example.com'),
(8, 'Hank Thomas', '1993-08-08', '(890) 123-4567', 'hank.thomas@example.com'),
(9, 'Ivy Martinez', '1994-09-09', '(901) 234-5678', 'ivy.martinez@example.com'),
(10, 'Jack Robinson', '1989-10-10', '(012) 345-6789', 'jack.robinson@example.com'),
(11, 'Kate Wilson', '1996-11-11', '(123) 456-7890', 'kate.wilson@example.com'),
(12, 'Leo Walker', '1990-12-12', '(234) 567-8901', 'leo.walker@example.com'),
(13, 'Mona Evans', '1985-01-13', '(345) 678-9012', 'mona.evans@example.com'),
(14, 'Nina Clark', '1988-02-14', '(456) 789-0123', 'nina.clark@example.com'),
(15, 'Oscar Wright', '1995-03-15', '(567) 890-1234', 'oscar.wright@example.com'),
(16, 'Paul Young', '1991-04-16', '(678) 901-2345', 'paul.young@example.com'),
(17, 'Quinn King', '1987-05-17', '(789) 012-3456', 'quinn.king@example.com'),
(18, 'Rose Scott', '1993-06-18', '(890) 123-4567', 'rose.scott@example.com'),
(19, 'Sam Harris', '1994-07-19', '(901) 234-5678', 'sam.harris@example.com'),
(20, 'Tina Martinez', '1989-08-20', '(012) 345-6789', 'tina.martinez@example.com'),
(21, 'Uma Carter', '1996-09-21', '(123) 456-7890', 'uma.carter@example.com'),
(22, 'Vince Parker', '1990-10-22', '(234) 567-8901', 'vince.parker@example.com'),
(23, 'Wendy Lopez', '1985-11-23', '(345) 678-9012', 'wendy.lopez@example.com'),
(24, 'Xander Hill', '1988-12-24', '(456) 789-0123', 'xander.hill@example.com'),
(25, 'Yara Adams', '1995-01-25', '(567) 890-1234', 'yara.adams@example.com'),
(26, 'Zane Baker', '1991-02-26', '(678) 901-2345', 'zane.baker@example.com'),
(27, 'Ava Turner', '1987-03-27', '(789) 012-3456', 'ava.turner@example.com'),
(28, 'Ben Stewart', '1993-04-28', '(890) 123-4567', 'ben.stewart@example.com'),
(29, 'Clara Ward', '1994-05-29', '(901) 234-5678', 'clara.ward@example.com'),
(30, 'Derek Reed', '1989-06-30', '(012) 345-6789', 'derek.reed@example.com'),
(31, 'Eli Rogers', '1996-07-31', '(123) 456-7890', 'eli.rogers@example.com'),
(32, 'Fiona Morgan', '1990-08-01', '(234) 567-8901', 'fiona.morgan@example.com'),
(33, 'George Peterson', '1985-09-02', '(345) 678-9012', 'george.peterson@example.com'),
(34, 'Holly Hughes', '1988-10-03', '(456) 789-0123', 'holly.hughes@example.com'),
(35, 'Ian Gray', '1995-11-04', '(567) 890-1234', 'ian.gray@example.com'),
(36, 'Julia Cooper', '1991-12-05', '(678) 901-2345', 'julia.cooper@example.com'),
(37, 'Kyle Rivera', '1987-01-06', '(789) 012-3456', 'kyle.rivera@example.com'),
(38, 'Lila Bailey', '1993-02-07', '(890) 123-4567', 'lila.bailey@example.com'),
(39, 'Max Murphy', '1994-03-08', '(901) 234-5678', 'max.murphy@example.com'),
(40, 'Nina Carter', '1989-04-09', '(012) 345-6789', 'nina.carter@example.com');
GO

-- Insert mock data into Address table
INSERT INTO [Address] (AddressID, SubscriberID, EmployeeID, StreetNum, Street, Suburb, City, Region, PostCode, Country)
VALUES 
(1, 1, NULL, '101', 'First St', 'Chelsea', 'Boston', 'Massachusetts', 2128, 'USA'),
(2, 2, NULL, '202', 'Second St', 'Beverly Hills', 'Los Angeles', 'California', 90210, 'USA'),
(3, 3, NULL, '303', 'Third St', 'Downtown', 'New York', 'New York', 10001, 'USA'),
(4, 4, NULL, '404', 'Fourth St', 'Fitzroy', 'Melbourne', 'Victoria', 3065, 'Australia'),
(5, 5, NULL, '505', 'Fifth St', 'Paddington', 'Sydney', 'New South Wales', 2021, 'Australia'),
(6, 6, NULL, '606', 'Sixth St', 'Kelvin Grove', 'Brisbane', 'Queensland', 4059, 'Australia'),
(7, 7, NULL, '707', 'Seventh St', 'City Centre', 'Dublin', 'Leinster', 2, 'Ireland'),
(8, 8, NULL, '808', 'Eighth St', 'Leith', 'Edinburgh', 'Lothian', 3478, 'Scotland'),
(9, 9, NULL, '909', 'Ninth St', 'Southwark', 'London', 'Greater London', 7632, 'England'),
(10, 10, NULL, '1010', 'Tenth St', 'West End', 'Glasgow', 'Lanarkshire', 7898, 'Scotland'),
(11, 11, NULL, '111', 'Eleventh St', 'Morningside', 'Edinburgh', 'Lothian', 2354, 'Scotland'),
(12, 12, NULL, '222', 'Twelfth St', 'North End', 'Portsmouth', 'Hampshire', 4567, 'England'),
(13, 13, NULL, '333', 'Thirteenth St', 'Jesmond', 'Newcastle', 'Tyne and Wear', 1427, 'England'),
(14, 14, NULL, '444', 'Fourteenth St', 'Clifton', 'Bristol', 'Bristol', 7896, 'England'),
(15, 15, NULL, '555', 'Fifteenth St', 'Queen Anne', 'Seattle', 'Washington', 3762, 'USA'),
(16, 16, NULL, '666', 'Sixteenth St', 'Midtown', 'Atlanta', 'Georgia', 30303, 'USA'),
(17, 17, NULL, '777', 'Seventeenth St', 'Capitol Hill', 'Denver', 'Colorado', 80203, 'USA'),
(18, 18, NULL, '888', 'Eighteenth St', 'Mission', 'San Francisco', 'California', 94110, 'USA'),
(19, 19, NULL, '999', 'Nineteenth St', 'Lakeview', 'Chicago', 'Illinois', 60657, 'USA'),
(20, 20, NULL, '1010', 'Twentieth St', 'Downtown', 'Austin', 'Texas', 78701, 'USA'),
(21, 21, NULL, '1111', 'Twenty-first St', 'Old City', 'Philadelphia', 'Pennsylvania', 19106, 'USA'),
(22, 22, NULL, '1212', 'Twenty-second St', 'Gaslamp', 'San Diego', 'California', 92101, 'USA'),
(23, 23, NULL, '1313', 'Twenty-third St', 'The Strip', 'Las Vegas', 'Nevada', 89109, 'USA'),
(24, 24, NULL, '1414', 'Twenty-fourth St', 'French Quarter', 'New Orleans', 'Louisiana', 70116, 'USA'),
(25, 25, NULL, '1515', 'Twenty-fifth St', 'Downtown', 'Miami', 'Florida', 33131, 'USA'),
(26, 26, NULL, '1616', 'Twenty-sixth St', 'Downtown', 'Orlando', 'Florida', 32801, 'USA'),
(27, 27, NULL, '1717', 'Twenty-seventh St', 'Downtown', 'Nashville', 'Tennessee', 37203, 'USA'),
(28, 28, NULL, '1818', 'Twenty-eighth St', 'Downtown', 'Dallas', 'Texas', 75201, 'USA'),
(29, 29, NULL, '1919', 'Twenty-ninth St', 'Downtown', 'Houston', 'Texas', 77002, 'USA'),
(30, 30, NULL, '2020', 'Thirtieth St', 'Downtown', 'Phoenix', 'Arizona', 85004, 'USA'),
(31, 31, NULL, '2121', 'Thirty-first St', 'Downtown', 'San Antonio', 'Texas', 78205, 'USA'),
(32, 32, NULL, '2222', 'Thirty-second St', 'Downtown', 'San Jose', 'California', 95113, 'USA'),
(33, 33, NULL, '2323', 'Thirty-third St', 'Downtown', 'Jacksonville', 'Florida', 32202, 'USA'),
(34, 34, NULL, '2424', 'Thirty-fourth St', 'Downtown', 'Indianapolis', 'Indiana', 46204, 'USA'),
(35, 35, NULL, '2525', 'Thirty-fifth St', 'Downtown', 'Columbus', 'Ohio', 43215, 'USA'),
(36, 36, NULL, '2626', 'Thirty-sixth St', 'Downtown', 'Charlotte', 'North Carolina', 28202, 'USA'),
(37, 37, NULL, '2727', 'Thirty-seventh St', 'Downtown', 'Fort Worth', 'Texas', 76102, 'USA'),
(38, 38, NULL, '2828', 'Thirty-eighth St', 'Downtown', 'Detroit', 'Michigan', 48226, 'USA'),
(39, 39, NULL, '2929', 'Thirty-ninth St', 'Downtown', 'El Paso', 'Texas', 79901, 'USA'),
(40, 40, NULL, '3030', 'Fortieth St', 'Downtown', 'Memphis', 'Tennessee', 38103, 'USA'),
(41, NULL, 1, '101', 'Main St', 'Downtown', 'New York', 'New York', 10001, 'USA'),
(42, NULL, 2, '202', 'Second St', 'Central', 'Los Angeles', 'California', 90012, 'USA'),
(43, NULL, 3, '303', 'Third St', 'Lincoln Park', 'Chicago', 'Illinois', 60614, 'USA'),
(44, NULL, 4, '404', 'Fourth St', 'Midtown', 'Houston', 'Texas', 77002, 'USA'),
(45, NULL, 5, '505', 'Fifth St', 'Old Town', 'Phoenix', 'Arizona', 85004, 'USA'),
(46, NULL, 6, '606', 'Sixth St', 'Center City', 'Philadelphia', 'Pennsylvania', 19106, 'USA'),
(47, NULL, 7, '707', 'Seventh St', 'Gaslamp', 'San Diego', 'California', 92101, 'USA'),
(48, NULL, 8, '808', 'Eighth St', 'The Strip', 'Las Vegas', 'Nevada', 89109, 'USA'),
(49, NULL, 9, '909', 'Ninth St', 'French Quarter', 'New Orleans', 'Louisiana', 70116, 'USA'),
(50, NULL, 10, '1010', 'Tenth St', 'Downtown', 'Miami', 'Florida', 33131, 'USA'),
(51, NULL, 11, '1111', 'Eleventh St', 'Downtown', 'Orlando', 'Florida', 32801, 'USA'),
(52, NULL, 12, '1212', 'Twelfth St', 'Downtown', 'Nashville', 'Tennessee', 37203, 'USA'),
(53, NULL, 13, '1313', 'Thirteenth St', 'Downtown', 'Dallas', 'Texas', 75201, 'USA'),
(54, NULL, 14, '1414', 'Fourteenth St', 'Downtown', 'Houston', 'Texas', 77002, 'USA'),
(55, NULL, 15, '1515', 'Fifteenth St', 'Downtown', 'Phoenix', 'Arizona', 85004, 'USA'),
(56, NULL, 16, '1616', 'Sixteenth St', 'Downtown', 'Austin', 'Texas', 78701, 'USA'),
(57, NULL, 17, '1717', 'Seventeenth St', 'Downtown', 'San Antonio', 'Texas', 78205, 'USA'),
(58, NULL, 18, '1818', 'Eighteenth St', 'Downtown', 'San Jose', 'California', 95113, 'USA'),
(59, NULL, 19, '1919', 'Nineteenth St', 'Downtown', 'Jacksonville', 'Florida', 32202, 'USA'),
(60, NULL, 20, '2020', 'Twentieth St', 'Downtown', 'Indianapolis', 'Indiana', 46204, 'USA');
GO

-- Insert mock data into Zone table
INSERT INTO [Zone] (ZoneID, [Type])
VALUES 
(1, 'Urban'),
(2, 'Rural'),
(3, 'Suburban'),
(4, 'Industrial'),
(5, 'Commercial'),
(6, 'Residential'),
(7, 'Recreational'),
(8, 'Agricultural'),
(9, 'Forested'),
(10, 'Desert');
GO

-- Insert mock data into DataScoop table
INSERT INTO DataScoop (DataScoopID, ZoneID)
VALUES 
(1, 1),
(2, 2),
(3, 3),
(4, 4),
(5, 5),
(6, 6),
(7, 7),
(8, 8),
(9, 9),
(10, 10),
(11, 1),
(12, 2),
(13, 3),
(14, 4),
(15, 5),
(16, 6),
(17, 7),
(18, 8),
(19, 9),
(20, 10),
(21, 1),
(22, 2),
(23, 3),
(24, 4),
(25, 5),
(26, 6),
(27, 7),
(28, 8),
(29, 9),
(30, 10),
(31, 1),
(32, 2),
(33, 3),
(34, 4),
(35, 5),
(36, 6),
(37, 7),
(38, 8),
(39, 9),
(40, 10),
(41, 1),
(42, 2),
(43, 3),
(44, 4),
(45, 5),
(46, 6),
(47, 7),
(48, 8),
(49, 9),
(50, 10);
GO

-- Insert mock data into Company table
INSERT INTO Company (CompanyID, CompanyName, Phone, Email)
VALUES 
(1, 'TechCorp', '(111) 111-1111', 'info@techcorp.com'),
(2, 'HealthPlus', '(222) 222-2222', 'info@healthplus.com'),
(3, 'EduWorld', '(333) 333-3333', 'info@eduworld.com'),
(4, 'FinServe', '(444) 444-4444', 'info@finserve.com'),
(5, 'EcoGoods', '(555) 555-5555', 'info@ecogoods.com'),
(6, 'BuildIt', '(666) 666-6666', 'info@buildit.com'),
(7, 'AgriLife', '(777) 777-7777', 'info@agrilife.com'),
(8, 'TravelPro', '(888) 888-8888', 'info@travelpro.com'),
(9, 'RetailHub', '(999) 999-9999', 'info@retailhub.com'),
(10, 'Foodies', '(101) 101-1010', 'info@foodies.com'),
(11, 'AutoMotive', '(202) 202-2020', 'info@automotive.com'),
(12, 'MediCare', '(303) 303-3030', 'info@medicare.com'),
(13, 'FashionFi', '(404) 404-4040', 'info@fashionfi.com'),
(14, 'GameOn', '(505) 505-5050', 'info@gameon.com'),
(15, 'PetCare', '(606) 606-6060', 'info@petcare.com');
GO

-- Insert mock data into CompanyAddress table
INSERT INTO CompanyAddress (CompanyID, StreetNum, Street, Suburb, City, Region, PostCode, Country)
VALUES 
(1, '100', 'Tech St', 'Silicon Valley', 'San Francisco', 'California', 94043, 'USA'),
(2, '200', 'Health Ave', 'Medical District', 'New York', 'New York', 10016, 'USA'),
(3, '300', 'Edu Lane', 'University Park', 'Boston', 'Massachusetts', 02115, 'USA'),
(4, '400', 'Fin Blvd', 'Financial District', 'Chicago', 'Illinois', 60604, 'USA'),
(5, '500', 'Eco Rd', 'Greenfield', 'Portland', 'Oregon', 97204, 'USA'),
(6, '600', 'Build Dr', 'Construction Zone', 'Dallas', 'Texas', 75201, 'USA'),
(7, '700', 'Agri Way', 'Farmville', 'Austin', 'Texas', 78701, 'USA'),
(8, '800', 'Travel St', 'Airport Area', 'Miami', 'Florida', 33101, 'USA'),
(9, '900', 'Retail Blvd', 'Shopping Center', 'Los Angeles', 'California', 90001, 'USA'),
(10, '1000', 'Food Ct', 'Culinary Square', 'New Orleans', 'Louisiana', 70112, 'USA'),
(11, '1100', 'Auto Ave', 'Motor City', 'Detroit', 'Michigan', 48201, 'USA'),
(12, '1200', 'Medi Dr', 'Health Hub', 'Houston', 'Texas', 77001, 'USA'),
(13, '1300', 'Fashion St', 'Trendy Town', 'Seattle', 'Washington', 98101, 'USA'),
(14, '1400', 'Game Ln', 'Entertainment Zone', 'Las Vegas', 'Nevada', 89101, 'USA'),
(15, '1500', 'Pet St', 'Animal Alley', 'Orlando', 'Florida', 32801, 'USA');
GO

-- Insert mock data into Supplier table
INSERT INTO Supplier (SupplierID, SupplierName, Phone, Email)
VALUES 
(1, 'SupplierOne', '(111) 111-1111', 'contact@supplierone.com'),
(2, 'SupplierTwo', '(222) 222-2222', 'contact@suppliertwo.com'),
(3, 'SupplierThree', '(333) 333-3333', 'contact@supplierthree.com'),
(4, 'SupplierFour', '(444) 444-4444', 'contact@supplierfour.com'),
(5, 'SupplierFive', '(555) 555-5555', 'contact@supplierfive.com'),
(6, 'SupplierSix', '(666) 666-6666', 'contact@suppliersix.com'),
(7, 'SupplierSeven', '(777) 777-7777', 'contact@supplierseven.com'),
(8, 'SupplierEight', '(888) 888-8888', 'contact@suppliereight.com'),
(9, 'SupplierNine', '(999) 999-9999', 'contact@suppliernine.com'),
(10, 'SupplierTen', '(101) 101-1010', 'contact@supplierten.com'),
(11, 'SupplierEleven', '(202) 202-2020', 'contact@suppliereleven.com'),
(12, 'SupplierTwelve', '(303) 303-3030', 'contact@suppliertwelve.com'),
(13, 'SupplierThirteen', '(404) 404-4040', 'contact@supplierthirteen.com'),
(14, 'SupplierFourteen', '(505) 505-5050', 'contact@supplierfourteen.com'),
(15, 'SupplierFifteen', '(606) 606-6060', 'contact@supplierfifteen.com');
GO

-- Insert mock data into SupplierAddress table
INSERT INTO SupplierAddress (SupplierID, StreetNum, Street, Suburb, City, Region, PostCode, Country)
VALUES 
(1, '100', 'First St', 'Downtown', 'New York', 'New York', 10001, 'USA'),
(2, '200', 'Second St', 'Central', 'Los Angeles', 'California', 90012, 'USA'),
(3, '300', 'Third St', 'Lincoln Park', 'Chicago', 'Illinois', 60614, 'USA'),
(4, '400', 'Fourth St', 'Midtown', 'Houston', 'Texas', 77002, 'USA'),
(5, '500', 'Fifth St', 'Old Town', 'Phoenix', 'Arizona', 85004, 'USA'),
(6, '600', 'Sixth St', 'Downtown', 'Philadelphia', 'Pennsylvania', 19103, 'USA'),
(7, '700', 'Seventh St', 'Uptown', 'San Antonio', 'Texas', 78205, 'USA'),
(8, '800', 'Eighth St', 'City Centre', 'San Diego', 'California', 92101, 'USA'),
(9, '900', 'Ninth St', 'Old City', 'Dallas', 'Texas', 75201, 'USA'),
(10, '1000', 'Tenth St', 'Historic District', 'San Jose', 'California', 95112, 'USA'),
(11, '1100', 'Eleventh St', 'Tech Valley', 'Austin', 'Texas', 78701, 'USA'),
(12, '1200', 'Twelfth St', 'Business District', 'Jacksonville', 'Florida', 32202, 'USA'),
(13, '1300', 'Thirteenth St', 'Financial District', 'San Francisco', 'California', 94105, 'USA'),
(14, '1400', 'Fourteenth St', 'Medical District', 'Charlotte', 'North Carolina', 28202, 'USA'),
(15, '1500', 'Fifteenth St', 'Cultural District', 'Columbus', 'Ohio', 43215, 'USA');
GO

-- Insert mock data into Part table
INSERT INTO Part (PartID, PartName, Specification, Cost, SupplierID)
VALUES 
(1, 'PartA', 'SpecA', 100.00, 1),
(2, 'PartB', 'SpecB', 200.00, 2),
(3, 'PartC', 'SpecC', 300.00, 3),
(4, 'PartD', 'SpecD', 400.00, 4),
(5, 'PartE', 'SpecE', 500.00, 5),
(6, 'PartF', 'SpecF', 600.00, 6),
(7, 'PartG', 'SpecG', 700.00, 7),
(8, 'PartH', 'SpecH', 800.00, 8),
(9, 'PartI', 'SpecI', 900.00, 9),
(10, 'PartJ', 'SpecJ', 1000.00, 10),
(11, 'PartK', 'SpecK', 1100.00, 11),
(12, 'PartL', 'SpecL', 1200.00, 12),
(13, 'PartM', 'SpecM', 1300.00, 13),
(14, 'PartN', 'SpecN', 1400.00, 14),
(15, 'PartO', 'SpecO', 1500.00, 15),
(16, 'PartP', 'SpecP', 1600.00, 1),
(17, 'PartQ', 'SpecQ', 1700.00, 2),
(18, 'PartR', 'SpecR', 1800.00, 3),
(19, 'PartS', 'SpecS', 1900.00, 4),
(20, 'PartT', 'SpecT', 2000.00, 5),
(21, 'PartU', 'SpecU', 2100.00, 6),
(22, 'PartV', 'SpecV', 2200.00, 7),
(23, 'PartW', 'SpecW', 2300.00, 8),
(24, 'PartX', 'SpecX', 2400.00, 9),
(25, 'PartY', 'SpecY', 2500.00, 10);
GO

-- Insert mock data into DataStore table
INSERT INTO DataStore (DataStoreID, AmbientLight, Temperature, OrganicSpectral, Humidty, DataScoopID)
VALUES 
(1, 50000, 25, 40000, 60, 1),
(2, 60000, 30, 30000, 55, 2),
(3, 70000, 35, 35000, 50, 3),
(4, 80000, 40, 45000, 45, 4),
(5, 90000, 45, 55000, 40, 5),
(6, 10000, 20, 10000, 65, 6),
(7, 20000, 22, 20000, 70, 7),
(8, 30000, 24, 30000, 75, 8),
(9, 40000, 26, 40000, 80, 9),
(10, 50000, 28, 50000, 85, 10),
(11, 60000, 30, 60000, 90, 11),
(12, 70000, 32, 70000, 95, 12),
(13, 80000, 34, 80000, 60, 13),
(14, 90000, 36, 90000, 55, 14),
(15, 10000, 38, 10000, 50, 15),
(16, 20000, 40, 20000, 45, 16),
(17, 30000, 42, 30000, 40, 17),
(18, 40000, 44, 40000, 35, 18),
(19, 50000, 46, 50000, 30, 19),
(20, 60000, 48, 60000, 25, 20),
(21, 70000, 50, 70000, 20, 21),
(22, 80000, 52, 80000, 15, 22),
(23, 90000, 54, 90000, 10, 23),
(24, 10000, 56, 10000, 5, 24),
(25, 20000, 58, 20000, 65, 25),
(26, 30000, 60, 30000, 70, 26),
(27, 40000, 62, 40000, 75, 27),
(28, 50000, 64, 50000, 80, 28),
(29, 60000, 66, 60000, 85, 29),
(30, 70000, 68, 70000, 90, 30),
(31, 80000, 70, 80000, 95, 31),
(32, 90000, 72, 90000, 60, 32),
(33, 10000, 74, 10000, 55, 33),
(34, 20000, 76, 20000, 50, 34),
(35, 30000, 78, 30000, 45, 35),
(36, 40000, 80, 40000, 40, 36),
(37, 50000, 82, 50000, 35, 37),
(38, 60000, 84, 60000, 30, 38),
(39, 70000, 86, 70000, 25, 39),
(40, 80000, 88, 80000, 20, 40),
(41, 90000, 90, 90000, 15, 41),
(42, 10000, 92, 10000, 10, 42),
(43, 20000, 94, 20000, 5, 43),
(44, 30000, 96, 30000, 65, 44),
(45, 40000, 98, 40000, 70, 45),
(46, 50000, 100, 50000, 75, 46),
(47, 60000, 20, 60000, 80, 47),
(48, 70000, 22, 70000, 85, 48),
(49, 80000, 24, 80000, 90, 49),
(50, 90000, 26, 90000, 95, 50),
(51, 10000, 28, 10000, 60, 1),
(52, 20000, 30, 20000, 55, 2),
(53, 30000, 32, 30000, 50, 3),
(54, 40000, 34, 40000, 45, 4),
(55, 50000, 36, 50000, 40, 5),
(56, 60000, 38, 60000, 65, 6),
(57, 70000, 40, 70000, 70, 7),
(58, 80000, 42, 80000, 75, 8),
(59, 90000, 44, 90000, 80, 9),
(60, 10000, 46, 10000, 85, 10),
(61, 20000, 48, 20000, 90, 11),
(62, 30000, 50, 30000, 95, 12),
(63, 40000, 52, 40000, 60, 13),
(64, 50000, 54, 50000, 55, 14),
(65, 60000, 56, 60000, 50, 15),
(66, 70000, 58, 70000, 45, 16),
(67, 80000, 60, 80000, 40, 17),
(68, 90000, 62, 90000, 35, 18),
(69, 10000, 64, 10000, 30, 19),
(70, 20000, 66, 20000, 25, 20),
(71, 30000, 68, 30000, 20, 21),
(72, 40000, 70, 40000, 15, 22),
(73, 50000, 72, 50000, 10, 23),
(74, 60000, 74, 60000, 5, 24),
(75, 70000, 76, 70000, 65, 25),
(76, 80000, 78, 80000, 70, 26),
(77, 90000, 80, 90000, 75, 27),
(78, 10000, 82, 10000, 80, 28),
(79, 20000, 84, 20000, 85, 29),
(80, 30000, 86, 30000, 90, 30),
(81, 40000, 88, 40000, 95, 31),
(82, 50000, 90, 50000, 60, 32),
(83, 60000, 92, 60000, 55, 33),
(84, 70000, 94, 70000, 50, 34),
(85, 80000, 96, 80000, 45, 35),
(86, 90000, 98, 90000, 40, 36),
(87, 10000, 100, 10000, 35, 37),
(88, 20000, 20, 20000, 30, 38),
(89, 30000, 22, 30000, 25, 39),
(90, 40000, 24, 40000, 20, 40),
(91, 50000, 26, 50000, 15, 41),
(92, 60000, 28, 60000, 10, 42),
(93, 70000, 30, 70000, 5, 43),
(94, 80000, 32, 80000, 65, 44),
(95, 90000, 34, 90000, 70, 45),
(96, 10000, 36, 10000, 75, 46),
(97, 20000, 38, 20000, 80, 47),
(98, 30000, 40, 30000, 85, 48),
(99, 40000, 42, 40000, 90, 49),
(100, 50000, 44, 50000, 95, 50);
GO

-- Insert mock data into VideoStream table
INSERT INTO VideoStream (VideoID, VideoStream, DataScoopID)
VALUES 
(1, 'StreamA', 1),
(2, 'StreamB', 2),
(3, 'StreamC', 3),
(4, 'StreamD', 4),
(5, 'StreamE', 5),
(6, 'StreamF', 6),
(7, 'StreamG', 7),
(8, 'StreamH', 8),
(9, 'StreamI', 9),
(10, 'StreamJ', 10),
(11, 'StreamK', 11),
(12, 'StreamL', 12),
(13, 'StreamM', 13),
(14, 'StreamN', 14),
(15, 'StreamO', 15),
(16, 'StreamP', 16),
(17, 'StreamQ', 17),
(18, 'StreamR', 18),
(19, 'StreamS', 19),
(20, 'StreamT', 20),
(21, 'StreamU', 21),
(22, 'StreamV', 22),
(23, 'StreamW', 23),
(24, 'StreamX', 24),
(25, 'StreamY', 25),
(26, 'StreamZ', 26),
(27, 'StreamAA', 27),
(28, 'StreamBB', 28),
(29, 'StreamCC', 29),
(30, 'StreamDD', 30),
(31, 'StreamEE', 31),
(32, 'StreamFF', 32),
(33, 'StreamGG', 33),
(34, 'StreamHH', 34),
(35, 'StreamII', 35),
(36, 'StreamJJ', 36),
(37, 'StreamKK', 37),
(38, 'StreamLL', 38),
(39, 'StreamMM', 39),
(40, 'StreamNN', 40),
(41, 'StreamOO', 41),
(42, 'StreamPP', 42),
(43, 'StreamQQ', 43),
(44, 'StreamRR', 44),
(45, 'StreamSS', 45),
(46, 'StreamTT', 46),
(47, 'StreamUU', 47),
(48, 'StreamVV', 48),
(49, 'StreamWW', 49),
(50, 'StreamXX', 50),
(51, 'StreamYY', 1),
(52, 'StreamZZ', 2),
(53, 'StreamAAA', 3),
(54, 'StreamBBB', 4),
(55, 'StreamCCC', 5),
(56, 'StreamDDD', 6),
(57, 'StreamEEE', 7),
(58, 'StreamFFF', 8),
(59, 'StreamGGG', 9),
(60, 'StreamHHH', 10),
(61, 'StreamIII', 11),
(62, 'StreamJJJ', 12),
(63, 'StreamKKK', 13),
(64, 'StreamLLL', 14),
(65, 'StreamMMM', 15),
(66, 'StreamNNN', 16),
(67, 'StreamOOO', 17),
(68, 'StreamPPP', 18),
(69, 'StreamQQQ', 19),
(70, 'StreamRRR', 20),
(71, 'StreamSSS', 21),
(72, 'StreamTTT', 22),
(73, 'StreamUUU', 23),
(74, 'StreamVVV', 24),
(75, 'StreamWWW', 25),
(76, 'StreamXXX', 26),
(77, 'StreamYYY', 27),
(78, 'StreamZZZ', 28),
(79, 'StreamAAAA', 29),
(80, 'StreamBBBB', 30),
(81, 'StreamCCCC', 31),
(82, 'StreamDDDD', 32),
(83, 'StreamEEEE', 33),
(84, 'StreamFFFF', 34),
(85, 'StreamGGGG', 35),
(86, 'StreamHHHH', 36),
(87, 'StreamIIII', 37),
(88, 'StreamJJJJ', 38),
(89, 'StreamKKKK', 39),
(90, 'StreamLLLL', 40),
(91, 'StreamMMMM', 41),
(92, 'StreamNNNN', 42),
(93, 'StreamOOOO', 43),
(94, 'StreamPPPP', 44),
(95, 'StreamQQQQ', 45),
(96, 'StreamRRRR', 46),
(97, 'StreamSSSS', 47),
(98, 'StreamTTTT', 48),
(99, 'StreamUUUU', 49),
(100, 'StreamVVVV', 50);
GO

-- Insert mock data into Location table
INSERT INTO [Location] (LocationID, Longitude, Latitude, Altitude, DataScoopID, ZoneID)
VALUES 
(1, -73.935242, 40.730610, 10, 1, 1),
(2, -118.243683, 34.052235, 15, 2, 2),
(3, -87.623177, 41.881832, 20, 3, 3),
(4, -95.369804, 29.760427, 25, 4, 4),
(5, -112.074036, 33.448376, 30, 5, 5),
(6, -75.165222, 39.952583, 35, 6, 6),
(7, -98.493629, 29.424122, 40, 7, 7),
(8, -117.161087, 32.715736, 45, 8, 8),
(9, -96.796856, 32.776272, 50, 9, 9),
(10, -121.886329, 37.338207, 55, 10, 10),
(11, -71.0589, 42.3601, 60, 11, 1),
(12, -118.2437, 34.0522, 65, 12, 2),
(13, -87.6298, 41.8781, 70, 13, 3),
(14, -95.3698, 29.7604, 75, 14, 4),
(15, -112.0740, 33.4484, 80, 15, 5),
(16, -75.1652, 39.9526, 85, 16, 6),
(17, -98.4936, 29.4241, 90, 17, 7),
(18, -117.1611, 32.7157, 95, 18, 8),
(19, -96.7969, 32.7763, 100, 19, 9),
(20, -121.8863, 37.3382, 105, 20, 10),
(21, -73.9352, 40.7306, 110, 21, 1),
(22, -118.2437, 34.0522, 115, 22, 2),
(23, -87.6298, 41.8781, 120, 23, 3),
(24, -95.3698, 29.7604, 125, 24, 4),
(25, -112.0740, 33.4484, 130, 25, 5),
(26, -75.1652, 39.9526, 135, 26, 6),
(27, -98.4936, 29.4241, 140, 27, 7),
(28, -117.1611, 32.7157, 145, 28, 8),
(29, -96.7969, 32.7763, 150, 29, 9),
(30, -121.8863, 37.3382, 155, 30, 10),
(31, -73.9352, 40.7306, 160, 31, 1),
(32, -118.2437, 34.0522, 165, 32, 2),
(33, -87.6298, 41.8781, 170, 33, 3),
(34, -95.3698, 29.7604, 175, 34, 4),
(35, -112.0740, 33.4484, 180, 35, 5),
(36, -75.1652, 39.9526, 185, 36, 6),
(37, -98.4936, 29.4241, 190, 37, 7),
(38, -117.1611, 32.7157, 195, 38, 8),
(39, -96.7969, 32.7763, 200, 39, 9),
(40, -121.8863, 37.3382, 205, 40, 10),
(41, -73.9352, 40.7306, 210, 41, 1),
(42, -118.2437, 34.0522, 215, 42, 2),
(43, -87.6298, 41.8781, 220, 43, 3),
(44, -95.3698, 29.7604, 225, 44, 4),
(45, -112.0740, 33.4484, 230, 45, 5),
(46, -75.1652, 39.9526, 235, 46, 6),
(47, -98.4936, 29.4241, 240, 47, 7),
(48, -117.1611, 32.7157, 245, 48, 8),
(49, -96.7969, 32.7763, 250, 49, 9),
(50, -121.8863, 37.3382, 255, 50, 10);
GO

-- Insert mock data into Subscription table
INSERT INTO Subscription (SubscriptionID, Cost, Duration, AccessLevel, Discount, VideoID, SalespersonEmployeeID, AdminExecutiveEmployeeID)
VALUES 
(1, 10.00, 1, 'Standard', 25, 1, 1, 6),
(2, 20.00, 2, 'Gold', 25, 2, 2, 7),
(3, 30.00, 3, 'Platinum', 30, 3, 3, 8),
(4, 40.00, 4, 'Super Platinum', 30, 4, 4, 9),
(5, 10.00, 1, 'Standard', 25, 5, 5, 10),
(6, 20.00, 2, 'Gold', 25, 6, 1, 6),
(7, 30.00, 3, 'Platinum', 30, 7, 2, 7),
(8, 40.00, 4, 'Super Platinum', 30, 8, 3, 8),
(9, 10.00, 1, 'Standard', 25, 9, 4, 9),
(10, 20.00, 2, 'Gold', 25, 10, 5, 10),
(11, 30.00, 3, 'Platinum', 30, 11, 1, 6),
(12, 40.00, 4, 'Super Platinum', 30, 12, 2, 7),
(13, 10.00, 1, 'Standard', 30, 13, 3, 8),
(14, 20.00, 2, 'Gold', 25, 14, 4, 9),
(15, 30.00, 3, 'Platinum', 25, 15, 5, 10),
(16, 40.00, 4, 'Super Platinum', 30, 16, 1, 6),
(17, 10.00, 1, 'Standard', 25, 17, 2, 7),
(18, 20.00, 2, 'Gold', 25, 18, 3, 8),
(19, 30.00, 3, 'Platinum', 30, 19, 4, 9),
(20, 40.00, 4, 'Super Platinum', 30, 20, 5, 10),
(21, 10.00, 1, 'Standard', 30, 21, 1, 6),
(22, 20.00, 2, 'Gold', 25, 22, 2, 7),
(23, 30.00, 3, 'Platinum', 25, 23, 3, 8),
(24, 40.00, 4, 'Super Platinum', 30, 24, 4, 9),
(25, 10.00, 1, 'Standard', 25, 25, 5, 10),
(26, 20.00, 2, 'Gold', 25, 26, 1, 6),
(27, 30.00, 3, 'Platinum', 30, 27, 2, 7),
(28, 40.00, 4, 'Super Platinum', 30, 28, 3, 8),
(29, 10.00, 1, 'Standard', 30, 29, 4, 9),
(30, 20.00, 2, 'Gold', 25, 30, 5, 10),
(31, 30.00, 3, 'Platinum', 25, 31, 1, 6),
(32, 40.00, 4, 'Super Platinum', 25, 32, 2, 7),
(33, 10.00, 1, 'Standard', 30, 33, 3, 8),
(34, 20.00, 2, 'Gold', 25, 34, 4, 9),
(35, 30.00, 3, 'Platinum', 30, 35, 5, 10),
(36, 40.00, 4, 'Super Platinum', 30, 36, 1, 6),
(37, 10.00, 1, 'Standard', 25, 37, 2, 7),
(38, 20.00, 2, 'Gold', 30, 38, 3, 8),
(39, 30.00, 3, 'Platinum', 25, 39, 4, 9),
(40, 40.00, 4, 'Super Platinum', 25, 40, 5, 10);
GO

-- Insert mock data into GoldSubscription table
INSERT INTO GoldSubscription (SubscriptionID, VideoID)
VALUES 
(2, 2),
(6, 6),
(10, 10),
(14, 14),
(18, 18),
(22, 22),
(26, 26),
(30, 30),
(34, 34),
(38, 38);
GO

-- Insert mock data into PlatinumSubscription table
INSERT INTO PlatinumSubscription (SubscriptionID, VideoID, DataStoreID)
VALUES 
(3, 3, 3),
(7, 7, 7),
(11, 11, 11),
(15, 15, 15),
(19, 19, 19),
(23, 23, 23),
(27, 27, 27),
(31, 31, 31),
(35, 35, 35),
(39, 39, 39);
GO

-- Insert mock data into SuperPlatinumSubscription table
INSERT INTO SuperPlatinumSubscription (SubscriptionID, VideoID, DataStoreID, ZoneID)
VALUES 
(4, 4, 4, 4),
(8, 8, 8, 8),
(12, 12, 12, 3),
(16, 16, 16, 2),
(20, 20, 20, 1),
(24, 24, 24, 5),
(28, 28, 28, 6),
(32, 32, 32, 7),
(36, 36, 36, 8),
(40, 40, 40, 9);
GO

-- Insert mock data into Contract table
INSERT INTO [Contract] (ContractID, StartDate, EndDate, ContractForm, AdminExecutiveEmployeeID, SubscriberID, SubscriptionID, DataScoopID)
VALUES 
(1, '2024-01-01', '2024-12-31', 0x1234567890ABCDEF, 6, 1, 1, 1),
(2, '2024-01-01', '2024-12-31', 0x1234567890ABCDEF, 7, 2, 2, 2),
(3, '2024-01-01', '2024-12-31', 0x1234567890ABCDEF, 8, 3, 3, 3),
(4, '2024-01-01', '2024-12-31', 0x1234567890ABCDEF, 9, 4, 4, 4),
(5, '2024-01-01', '2024-12-31', 0x1234567890ABCDEF, 10, 5, 5, 5),
(6, '2024-01-01', '2024-12-31', 0x1234567890ABCDEF, 6, 6, 6, 6),
(7, '2024-01-01', '2024-12-31', 0x1234567890ABCDEF, 7, 7, 7, 7),
(8, '2024-01-01', '2024-12-31', 0x1234567890ABCDEF, 8, 8, 8, 8),
(9, '2024-01-01', '2024-12-31', 0x1234567890ABCDEF, 9, 9, 9, 9),
(10, '2024-01-01', '2024-12-31', 0x1234567890ABCDEF, 10, 10, 10, 10),
(11, '2024-01-01', '2024-12-31', 0x1234567890ABCDEF, 6, 11, 11, 11),
(12, '2024-01-01', '2024-12-31', 0x1234567890ABCDEF, 7, 12, 12, 12),
(13, '2024-01-01', '2024-12-31', 0x1234567890ABCDEF, 8, 13, 13, 13),
(14, '2024-01-01', '2024-12-31', 0x1234567890ABCDEF, 9, 14, 14, 14),
(15, '2024-01-01', '2024-12-31', 0x1234567890ABCDEF, 10, 15, 15, 15),
(16, '2024-01-01', '2024-12-31', 0x1234567890ABCDEF, 6, 16, 16, 16),
(17, '2024-01-01', '2024-12-31', 0x1234567890ABCDEF, 7, 17, 17, 17),
(18, '2024-01-01', '2024-12-31', 0x1234567890ABCDEF, 8, 18, 18, 18),
(19, '2024-01-01', '2024-12-31', 0x1234567890ABCDEF, 9, 19, 19, 19),
(20, '2024-01-01', '2024-12-31', 0x1234567890ABCDEF, 10, 20, 20, 20),
(21, '2024-01-01', '2024-12-31', 0x1234567890ABCDEF, 6, 21, 21, 21),
(22, '2024-01-01', '2024-12-31', 0x1234567890ABCDEF, 7, 22, 22, 22),
(23, '2024-01-01', '2024-12-31', 0x1234567890ABCDEF, 8, 23, 23, 23),
(24, '2024-01-01', '2024-12-31', 0x1234567890ABCDEF, 9, 24, 24, 24),
(25, '2024-01-01', '2024-12-31', 0x1234567890ABCDEF, 10, 25, 25, 25),
(26, '2024-01-01', '2024-12-31', 0x1234567890ABCDEF, 6, 26, 26, 26),
(27, '2024-01-01', '2024-12-31', 0x1234567890ABCDEF, 7, 27, 27, 27),
(28, '2024-01-01', '2024-12-31', 0x1234567890ABCDEF, 8, 28, 28, 28),
(29, '2024-01-01', '2024-12-31', 0x1234567890ABCDEF, 9, 29, 29, 29),
(30, '2024-01-01', '2024-12-31', 0x1234567890ABCDEF, 10, 30, 30, 30),
(31, '2024-01-01', '2024-12-31', 0x1234567890ABCDEF, 6, 31, 31, 31),
(32, '2024-01-01', '2024-12-31', 0x1234567890ABCDEF, 7, 32, 32, 32),
(33, '2024-01-01', '2024-12-31', 0x1234567890ABCDEF, 8, 33, 33, 33),
(34, '2024-01-01', '2024-12-31', 0x1234567890ABCDEF, 9, 34, 34, 34),
(35, '2024-01-01', '2024-12-31', 0x1234567890ABCDEF, 10, 35, 35, 35),
(36, '2024-01-01', '2024-12-31', 0x1234567890ABCDEF, 6, 36, 36, 36),
(37, '2024-01-01', '2024-12-31', 0x1234567890ABCDEF, 7, 37, 37, 37),
(38, '2024-01-01', '2024-12-31', 0x1234567890ABCDEF, 8, 38, 38, 38),
(39, '2024-01-01', '2024-12-31', 0x1234567890ABCDEF, 9, 39, 39, 39),
(40, '2024-01-01', '2024-12-31', 0x1234567890ABCDEF, 10, 40, 40, 40);
GO

-- Insert mock data into Maintenance table
INSERT INTO Maintenance (MaintenanceID, ReportDate, NextReportDate, DataScoopID, TechnicianEmployeeID)
VALUES 
(1, '2024-01-01', '2024-02-01', 1, 11),
(2, '2024-01-01', '2024-02-01', 2, 12),
(3, '2024-01-01', '2024-02-01', 3, 13),
(4, '2024-01-01', '2024-02-01', 4, 14),
(5, '2024-01-01', '2024-02-01', 5, 15),
(6, '2024-01-01', '2024-02-01', 6, 11),
(7, '2024-01-01', '2024-02-01', 7, 12),
(8, '2024-01-01', '2024-02-01', 8, 13),
(9, '2024-01-01', '2024-02-01', 9, 14),
(10, '2024-01-01', '2024-02-01', 10, 15),
(11, '2024-01-01', '2024-02-01', 11, 11),
(12, '2024-01-01', '2024-02-01', 12, 12),
(13, '2024-01-01', '2024-02-01', 13, 13),
(14, '2024-01-01', '2024-02-01', 14, 14),
(15, '2024-01-01', '2024-02-01', 15, 15),
(16, '2024-01-01', '2024-02-01', 16, 11),
(17, '2024-01-01', '2024-02-01', 17, 12),
(18, '2024-01-01', '2024-02-01', 18, 13),
(19, '2024-01-01', '2024-02-01', 19, 14),
(20, '2024-01-01', '2024-02-01', 20, 15);
GO

-- Insert mock data into Invoice table
INSERT INTO Invoice (InvoiceID, DateIssued, DateDue, Amount, SupplierID, PartID, TechnicianEmployeeID)
VALUES 
(1, '2024-01-01', '2024-01-31', 1000.00, 1, 1, 11, 1),
(2, '2024-02-01', '2024-02-28', 2000.00, 2, 2, 12, 2),
(3, '2024-03-01', '2024-03-31', 3000.00, 3, 3, 13, 3),
(4, '2024-04-01', '2024-04-30', 4000.00, 4, 4, 14, 4),
(5, '2024-05-01', '2024-05-31', 5000.00, 5, 5, 15, 5),
(6, '2024-06-01', '2024-06-30', 6000.00, 6, 6, 11, 6),
(7, '2024-07-01', '2024-07-31', 7000.00, 7, 7, 12, 7),
(8, '2024-08-01', '2024-08-31', 8000.00, 8, 8, 13, 8),
(9, '2024-09-01', '2024-09-30', 9000.00, 9, 9, 14, 9),
(10, '2024-10-01', '2024-10-31', 10000.00, 10, 10, 15, 10);
GO

--TRIGGERS AND ENTERPRISE CONSTRAINTS

--Trigger to ensure Employees cannot have mutliple roles assigned to them
CREATE TRIGGER trg_Employee_Roles
ON Employee
AFTER INSERT, UPDATE
AS
BEGIN
	IF EXISTS (
		SELECT EmployeeID
		FROM (
			SELECT EmployeeID FROM Salesperson
			UNION ALL
			SELECT EmployeeID FROM AdminExecutive
			UNION ALL
			SELECT EmployeeID FROM Technician
		) AS CombinedRoles
		GROUP BY EmployeeID
		HAVING COUNT(*) > 1
	)
	BEGIN
		ROLLBACK;
		RAISERROR ('An employee cannot have multiple roles.', 16, 1);
	END
END;

--Add CHECK constraint to Contract table to ensure start and end dates comply with logic
ALTER TABLE [Contract] ADD CONSTRAINT CHK_Contract_Dates CHECK (EndDate> StartDate);

--Add Unique Index for phone numbers across the database
CREATE UNIQUE INDEX UQ_Phone_Employee ON Employee (Phone);
CREATE UNIQUE INDEX UQ_Phone_Subscriber ON Subscriber (Phone);
CREATE UNIQUE INDEX UQ_Phone_Company ON Company (Phone);

--Maintenace schedule report date validation check
ALTER TABLE Maintenace ADD CONSTRAINT CHK_Maintenance_Dates CHECK (NextReportDate > ReportDate);

--INDEX STATEMENTS
--Create indexes for transaction 1.
CREATE INDEX idx_SalespersonEmployeeID ON Subscription(SalespersonEmployeeID);
CREATE INDEX idx_AdminExecutiveEmployeeID ON Subscription(AdminExecutiveEmployeeID);
CREATE INDEX idx_AdminExecutiveEmployeeID_Contract ON [Contract](AdminExecutiveEmployeeID);
CREATE INDEX idx_SubscriberID_Contract ON [Contract](SubscriberID);

--Create indexes for transaction 2
CREATE INDEX idx_SubscriberID_Subscriber ON Subscriber(SubscriberID);
CREATE INDEX idx_SubscriberID_Address ON [Address](SubscriberID);
CREATE INDEX idx_SubscriptionID_Contract ON [Contract](SubscriptionID);
CREATE INDEX idx_SalespersonEmployeeID_Subscription ON Subscription(SalespersonEmployeeID);

--Create indexes for transaction 3
CREATE INDEX idx_DataScoopID_DataStore ON DataStore(DataScoopID);
CREATE INDEX idx_DataScoopID_Location ON [Location](DataScoopID);
CREATE INDEX idx_ZoneID_Location ON [Location](ZoneID);

--Create indexes for transaction 4
CREATE INDEX idx_EndDate_Contract ON [Contract](EndDate);
CREATE INDEX idx_DataScoopID_Contract ON [Contract](DataScoopID);
CREATE INDEX idx_DataScoopID_DataScoop ON DataScoop(DataScoopID);
CREATE INDEX idx_CompanyID_Contract ON [Contract](SubscriberID);
CREATE INDEX idx_CompanyID_Company ON Company(CompanyID);

--Create indexes for transaction 5
CREATE INDEX idx_CompanyName_Company ON Company(CompanyName);
CREATE INDEX idx_SubscriptionID_Subscription ON Subscription(SubscriptionID);

--Create indexes for transaction 6
CREATE INDEX idx_DataScoopID_VideoStream ON VideoStream(DataScoopID);
CREATE INDEX idx_VideoID_Subscription ON Subscription(VideoID);
CREATE INDEX idx_VideoID_VideoStream ON VideoStream(VideoID);

--Create indexes for transaction 7
CREATE INDEX idx_DataScoopID_Invoice ON Invoice(DataScoopID);
CREATE INDEX idx_PartID_Invoice ON Invoice(PartID);
CREATE INDEX idx_PartID_Part ON Part(PartID);
CREATE INDEX idx_SupplierID_Part ON Part(SupplierID);
CREATE INDEX idx_SupplierID_Supplier ON Supplier(SupplierID);

CREATE INDEX idx_ContractID_Contract ON [Contract](ContractID);

--TRANSACTION STATMENTS

--Transaction 1
--Insert a new subscriber
INSERT INTO Subscriber (SubscriberID, [Name], DateOfBirth, Phone, Email)
VALUES (@SubscriberID, @Name, @DateOfBirth, @Phone, @Email);

--Insert a new Subscription
INSERT INTO Subscription (SubscriptionID, Cost, Duration, AccessLevel, VideoID, SalespersonEmployeeID, AdminExecutiveEmployeeID)
VALUES (@SubscriptionID, @Cost, @Duration, @AccessLevel, @VideoID, @SalespersonEmployeeID, @AdminExecutiveEmployeeID);

 --Create a Contract linking the Subscriber to the Subscription
 INSERT INTO [Contract] (ContractID, StartDate, EndDate, ContractForm, AdminExecutiveEmployeeID, SubscriberID, SubscriptionID)
 VALUES (@ContractID, @StartDate, @EndDate, @ContractForm, @AdminExecutiveEmployeeID, @SubscriberID, @SubscriptionID);

SELECT s.[Name] AS SubscriberName, a.StreetNum, a.Street, a.Suburb, a.City, a.Region, a.PostCode, a.Country, sub.Discount
FROM Subscriber s
JOIN [Address] a ON s.SubscriberID = a.SubscriberID
JOIN [Contract] c ON s.SubscriberID = c.SubscriberID
JOIN Subscription sub ON c.SubscriptionID = sub.SubscriptionID
JOIN Salesperson sp ON sub.SalespersonEmployeeID = sp.SalespersonEmployeeID
JOIN Employee e ON sp.EmployeeID = e.EmployeeID
WHERE e.[Name] = @SalespersonName;

--Transaction 3
--Insert a new record into the DataStore table
INSERT INTO DataStore (DataStoreID, AmbientLight, Temperature, OrganicSpectral, Humidty, DataScoopID)
VALUES (@DataStoreID, @AmbientLight, @Temperature, @OrganicSpectral, @Humidty, @DataScoopID);

--Insert a new record into the Location Table 
INSERT INTO [Location] (LocationID, Longitude, Latitude, Altitude, DataScoopID, ZoneID)
VALUES (@LocationID, @Longitude, @Latitude, @Altitude, @DataScoopID, @ZoneID);

--Transaction 4
SELECT com.CompanyName, ds.DataScoopID, loc.Latitude, loc.Longitude
FROM [Contract] c
JOIN DataScoop ds ON c.DataScoopID = ds.DataScoopID
JOIN [Location] loc ON ds.DataScoopID = loc.DataScoopID
JOIN Company com ON c.SubscriberID = com.CompanyID
WHERE c.EndDate >= GETDATE();

--Transaction 5
SELECT com.CompanyName, ds.DataScoopID, dsr.Temperature, dsr.Humidty, dsr.AmbientLight, dsr.OrganicSpectral
FROM [Contract] c
JOIN Subscription sub ON c.SubscriptionID = sub.SubscriptionID
JOIN DataScoop ds ON c.DataScoopID = ds.DataScoopID
JOIN DataStore dsr ON ds.DataScoopID = dsr.DataScoopID
JOIN Company com ON c.SubscriberID = com.CompanyID
WHERE com.CompanyName = @CompanyName;

--Transaction 6
SELECT ds.DataScoopID, s.[Name] AS SubscriberName, vs.VideoID AS StreamID
FROM VideoStream vs
JOIN Subscription sub ON vs.VideoID = sub.VideoID
JOIN [Contract] c ON sub.SubscriptionID = c.SubscriptionID
JOIN Subscriber s ON c.SubscriberID = s.SubscriberID
JOIN DataScoop ds ON vs.DataScoopID = ds.DataScoopID;

--Transaction 7
SELECT ds.DataScoopID, sup.SupplierName, p.PartName
FROM DataScoop ds
JOIN Invoice inv ON ds.DataScoopID = inv.DataScoopID
JOIN Part p ON inv.PartID = p.PartID
JOIN Supplier sup ON p.SupplierID = sup.SupplierID
WHERE ds.DataScoopID = @DataScoopID;

--Transaction 8
-- Update the Zone of the DataScoop
UPDATE DataScoop
SET ZoneID = @NewZoneID
WHERE DataScoopID = @DataScoopID;

-- Update the Location of the DataScoop
UPDATE [Location]
SET Longitude = @NewLongitude, Latitude = @NewLatitude
WHERE DataScoopID = @DataScoopID;

--Transaction 9
DELETE dsr
FROM DataStore dsr
JOIN DataScoop ds ON dsr.DataScoopID = ds.DataScoopID
JOIN [Contract] c ON ds.DataScoopID = c.DataScoopID
WHERE c.ContractID = @ContractID;

--Transaction 10
SELECT ds.DataScoopID, SUM(p.Cost) AS TotalCost
FROM DataScoop ds
JOIN Invoice inv ON ds.DataScoopID = inv.DataScoopID
JOIN Part p ON inv.PartID = p.PartID
GROUP BY ds.DataScoopID;

--USER ROLES AND VIEWS

--CREATE ROLES
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'AdminRole')
BEGIN
    CREATE ROLE AdminRole;
END
GO

IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'SalespersonRole')
BEGIN
    CREATE ROLE SalespersonRole;
END
GO

IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'TechnicianRole')
BEGIN
    CREATE ROLE TechnicianRole;
END
GO

IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'SubscriberRole')
BEGIN
    CREATE ROLE SubscriberRole;
END
GO

--ROLE PERMISSONS AND ACCESS

--Admin Role
GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA dbo TO AdminRole;

--Salesperson Role
GRANT SELECT, INSERT ON Subscriber TO SalespersonRole;
GRANT SELECT, INSERT ON Subscription TO SalespersonRole;
GRANT SELECT, INSERT ON Contract TO SalespersonRole;

--Technician Role
GRANT SELECT, INSERT, UPDATE ON DataScoop TO TechnicianRole;
GRANT SELECT, INSERT, UPDATE ON Maintenance TO TechnicianRole;
GRANT SELECT, INSERT, UPDATE ON Part TO TechnicianRole;

--Subscriber Role
GRANT SELECT ON SubscriberView TO SubscriberRole;

-- CREATE VIEWS FOR ROLES

--Admin View
CREATE VIEW AdminView AS
SELECT * FROM Employee;

--Salesperson View
CREATE VIEW SalespersonView AS
SELECT s.SubscriberID, s.Name, s.DateOfBirth, s.Phone, s.Email, sub.SubscriptionID, sub.Cost, sub.Duration, sub.AccessLevel
FROM Subscriber s
JOIN Contract c ON s.SubscriberID = c.SubscriberID
JOIN Subscription sub ON c.SubscriptionID = sub.SubscriptionID;

--Technician View
CREATE VIEW TechnicianView AS
SELECT d.DataScoopID, d.ZoneID, m.MaintenanceID, m.ReportDate, m.NextReportDate, m.TechnicianEmployeeID
FROM DataScoop d
JOIN Maintenance m ON d.DataScoopID = m.DataScoopID;

--Subscriber View
CREATE VIEW SubscriberView AS
SELECT s.Name, sub.SubscriptionID, sub.Cost, sub.Duration, sub.AccessLevel, d.DataScoopID, ds.AmbientLight, ds.Temperature, ds.OrganicSpectral, ds.Humidity
FROM Subscriber s
JOIN Contract c ON s.SubscriberID = c.SubscriberID
JOIN Subscription sub ON c.SubscriptionID = sub.SubscriptionID
JOIN DataScoop d ON sub.SubscriptionID = d.DataScoopID
JOIN DataStore ds ON d.DataScoopID = ds.DataScoopID
WHERE s.SubscriberID = @SubscriberID;

--ADD USERNAMES TO ROLES
--(replace [YourAdminUser], [YourSalespersonUser], [YourTechnicianUser], [YourSubscriberUser] with actual usernames)
ALTER ROLE AdminRole ADD MEMBER [YourAdminUser];
ALTER ROLE SalespersonRole ADD MEMBER [YourSalespersonUser];
ALTER ROLE TechnicianRole ADD MEMBER [YourTechnicianUser];
ALTER ROLE SubscriberRole ADD MEMBER [YourSubscriberUser];
GO
