# appfood

CREATE DATABASE appfood;
USE appfood;

CREATE TABLE _user (
    userID INT PRIMARY KEY IDENTITY(1,1),
    full_name VARCHAR(50),
    email VARCHAR(50),
    password VARCHAR(50)
);

CREATE TABLE restaurant (
    res_id INT PRIMARY KEY IDENTITY(1,1),
    res_name VARCHAR(50),
    image VARCHAR(200),
    [desc] VARCHAR(200)
);

CREATE TABLE food (
    food_id INT PRIMARY KEY IDENTITY(1,1),
    food_name VARCHAR(50),
    image VARCHAR(200),
    price FLOAT,
    [desc] VARCHAR(200),
    type_id INT
);

CREATE TABLE food_type (
    type_id INT PRIMARY KEY IDENTITY(1,1),
    type_name VARCHAR(50)
);

CREATE TABLE [order] (
    order_id INT PRIMARY KEY IDENTITY(1,1),
    userID INT,
    food_id INT,
    amount INT,
    code VARCHAR(50),
    arr_sub_id VARCHAR(50),
    FOREIGN KEY (userID) REFERENCES _user(userID),
    FOREIGN KEY (food_id) REFERENCES food(food_id)
);

CREATE TABLE like_res (
    userID INT,
    res_id INT,
    like_date DATETIME,
    PRIMARY KEY (userID, res_id),
    FOREIGN KEY (userID) REFERENCES _user(userID),
    FOREIGN KEY (res_id) REFERENCES restaurant(res_id)
);

CREATE TABLE rate_res (
    userID INT,
    res_id INT,
    amount INT,
    rate_date DATETIME,
    PRIMARY KEY (userID, res_id),
    FOREIGN KEY (userID) REFERENCES _user(userID),
    FOREIGN KEY (res_id) REFERENCES restaurant(res_id)
);

-- Chèn dữ liệu mẫu cho _user
INSERT INTO _user (full_name, email, password) VALUES 
('Dinh Van Thanh', 'abc@gmail.com', '1234434'),
('Tran Thi Hoa', 'aaab@gmail.com', '012333'),
('Nguyen Van Chuong', 'fgca@gmail.com', '343444343'),
('Nguyen Thi Lai', 'abcd@gmail.com', '5765765'),
('Hoang Van Khoa', 'abce@gmail.com', '67678768');

-- Chèn dữ liệu mẫu cho restaurant
INSERT INTO restaurant (res_name, image, [desc]) VALUES 
('Com Tam', 'comtam.jpg', 'Do an sieu ngon'),
('Bun Cha', 'buncha.jpg', 'Do an sieu ngon'),
('Bun Mam Tom', 'bunmamtom.jpg', 'Do an sieu ngon');

-- Chèn dữ liệu mẫu cho food_type
INSERT INTO food_type (type_name) VALUES 
('an vat'),
('an chay');

-- Chèn dữ liệu mẫu cho food
INSERT INTO food (food_name, image, price, [desc], type_id) VALUES 
('Hambuger', 'hambuger.jpg', 50000, 'hambuger', 1),
('Cake', 'Cake.jpg', 50000, 'Cake', 1),
('Coca', 'Coca.jpg', 60000, ' Coca', 2);

-- Chèn dữ liệu mẫu cho [order]
INSERT INTO [order] (userID, food_id, amount, code, arr_sub_id) VALUES 
(1, 1, 2, 'codesss', 's1'),
(2, 2, 1, 'codeaaa', 's2'),
(1, 3, 1, 'codebbb', 's3');

-- Chèn dữ liệu mẫu cho like_res
INSERT INTO like_res (userID, res_id, like_date) VALUES 
(1, 1, '2024-04-01 01:00:00'),
(1, 2, '2024-05-02 01:00:00'),
(2, 1, '2024-06-03 02:00:00'),
(3, 1, '2024-07-04 03:00:00'),
(4, 2, '2024-03-05 04:00:00'),
(5, 3, '2024-02-06 05:00:00');

-- Chèn dữ liệu mẫu cho rate_res
INSERT INTO rate_res (userID, res_id, amount, rate_date) VALUES 
(1, 1, 5, '2024-04-01 10:00:00'),
(2, 1, 4, '2024-02-02 11:00:00'),
(3, 2, 3, '2024-01-03 12:00:00'),
(4, 3, 2, '2024-06-04 13:00:00');

-- Tìm 5 người đã like nhà hàng nhiều nhất
SELECT userID, COUNT(res_id) AS like_count
FROM like_res
GROUP BY userID
ORDER BY like_count DESC
OFFSET 0 ROWS FETCH NEXT 5 ROWS ONLY;

-- Tìm 2 nhà hàng có lượt like nhiều nhất
SELECT res_id, COUNT(userID) AS like_count
FROM like_res
GROUP BY res_id
ORDER BY like_count DESC
OFFSET 0 ROWS FETCH NEXT 2 ROWS ONLY;

-- Tìm người đã đặt hàng nhiều nhất
SELECT userID, COUNT(order_id) AS order_count
FROM [order]
GROUP BY userID
ORDER BY order_count DESC
OFFSET 0 ROWS FETCH NEXT 1 ROWS ONLY;

-- Tìm người dùng không hoạt động trong hệ thống
SELECT u.userID, u.full_name
FROM _user u
LEFT JOIN [order] o ON u.userID = o.userID
LEFT JOIN like_res lr ON u.userID = lr.userID
LEFT JOIN rate_res rr ON u.userID = rr.userID
WHERE o.userID IS NULL 
  AND lr.userID IS NULL 
  AND rr.userID IS NULL;

-- Tìm thông tin người dùng không hoạt động
SELECT u.userID, u.full_name, u.email
FROM _user u
LEFT JOIN [order] AS o ON u.userID = o.userID
LEFT JOIN like_res lr ON u.userID = lr.userID
LEFT JOIN rate_res rr ON u.userID = rr.userID
WHERE o.order_id IS NULL AND lr.res_id IS NULL AND rr.res_id IS NULL;
