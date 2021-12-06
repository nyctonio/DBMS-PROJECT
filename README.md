# DBMS-PROJECT

```sql
CREATE TABLE JOINED(
    joining_date DATE PRIMARY KEY,
    user_since VARCHAR(10) NOT NULL
);


CREATE TABLE USER(
    user_id INTEGER PRIMARY KEY AUTO_INCREMENT,
    name TEXT NOT NULL,
    joining_date DATE NOT NULL,
    p_no VARCHAR(10) NOT NULL,
    store_vid BOOL NOT NULL,
    upload_on VARCHAR(10) NOT NULL,
    FOREIGN KEY(joining_date) REFERENCES JOINED(joining_date)
);





CREATE TABLE ENCRYPTION_KEY(
    key_id INTEGER PRIMARY KEY AUTO_INCREMENT,
    key_type TEXT NOT NULL,
    key_value TEXT NOT NULL
);

CREATE TABLE DRIVE(
    drive_mail VARCHAR(20) NOT NULL,
    storage VARCHAR(5),
    PRIMARY KEY(drive_mail)
);

CREATE TABLE BACKUP(
    backup_id INTEGER PRIMARY KEY AUTO_INCREMENT,
    backup_type VARCHAR(10) NOT NULL,
    backup_date DATE NOT NULL
);

CREATE TABLE CREATES(
    user_id INTEGER NOT NULL,
    key_id INTEGER NOT NULL,
    backup_id INTEGER NOT NULL,
    drive_mail VARCHAR(20) NOT NULL,
    FOREIGN KEY(user_id) REFERENCES USER(user_id),
    FOREIGN KEY(key_id) REFERENCES ENCRYPTION_KEY(key_id),
    FOREIGN KEY(backup_id) REFERENCES BACKUP(backup_id),
    FOREIGN KEY(drive_mail) REFERENCES DRIVE(drive_mail)
);


-- procedure to add a new user
DELIMITER && 
CREATE PROCEDURE add_user(IN name TEXT, IN joining_date DATE, IN p_no VARCHAR(10), IN store_vid BOOL, IN upload_on VARCHAR(10))
BEGIN
    INSERT INTO USER(name, joining_date, p_no, store_vid, upload_on)
    VALUES(name, joining_date, p_no, store_vid, upload_on);
END &&
DELIMITER ;

-- procedure to add a new drive
DELIMITER &&
CREATE PROCEDURE add_drive(IN drive_mail VARCHAR(20), IN storage VARCHAR(5))
BEGIN
    INSERT INTO DRIVE(drive_mail, storage)
    VALUES(drive_mail, storage);
END &&
DELIMITER ;

-- procedure to add a new backup
DELIMITER &&
CREATE PROCEDURE add_backup(IN backup_type VARCHAR(10), IN backup_date DATE)
BEGIN
    INSERT INTO BACKUP(backup_type, backup_date)
    VALUES(backup_type, backup_date);
END &&
DELIMITER ;

-- procedure to add a new encryption key
DELIMITER &&
CREATE PROCEDURE add_encryption_key(IN key_type TEXT, IN key_value TEXT)
BEGIN
    INSERT INTO ENCRYPTION_KEY(key_type, key_value)
    VALUES(key_type, key_value);
END &&
DELIMITER ;

-- create  a trigger to add joining date to the joined table
DELIMITER &&
CREATE TRIGGER add_joining_date BEFORE INSERT ON USER
FOR EACH ROW
BEGIN
    INSERT INTO JOINED(joining_date, user_since)
    VALUES(NEW.joining_date,CURRENT_DATE()-NEW.joining_date);
END &&
DELIMITER ;


CALL add_user('John', '2020-04-01', '1234567890', 1, 'wifi');
CALL add_user('Ritesh', '2020-04-03', '8943238654', 1, 'data');


INSERT INTO DRIVE(drive_mail, storage) VALUES
('ritesh@gmail.com','20GB'),
('ritesh1@gmail.com','15GB'),
('karan@gmail.com','10GB');

INSERT INTO BACKUP(backup_type, backup_date) VALUES
('daily','2020-04-01'),
('weekly','2020-04-02'),
('monthly','2020-04-03');


INSERT INTO ENCRYPTION_KEY(key_type, key_value) VALUES
('256bit','jdhksdhkj87662676237hghgjg'),
('256bit','67836868234hghwejgwejh56752'),
('password','qwerty123');


INSERT INTO CREATES(user_id, key_id, backup_id, drive_mail) VALUES
(1,1,1,'ritesh@gmail.com'),
(1,1,2,'ritesh1@gmail.com'),
(2,2,3,'karan@gmail.com');


-- show the names of user who has gmail ritesh@gmail.com
SELECT name FROM CREATES JOIN USER ON CREATES.user_id = USER.user_id WHERE drive_mail = 'ritesh@gmail.com';

-- show name of all users who has joined before 2020-04-01
SELECT name FROM JOINED JOIN USER ON JOINED.joining_date = USER.joining_date WHERE user_since < '2020-04-01';

-- show name of all users who has entered phone number 1234567890
SELECT name FROM USER WHERE p_no = '1234567890';

-- find the mail id of user with user_id = 1
SELECT drive_mail FROM CREATES WHERE user_id = 1;

-- find the users with backup_type = 'weekly'
SELECT name FROM BACKUP JOIN (CREATES INNER JOIN USER ON CREATES.user_id = USER.user_id) ON BACKUP.backup_id = CREATES.backup_id WHERE backup_type = 'weekly';
```
