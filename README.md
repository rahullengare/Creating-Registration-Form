# Creating Registration Form
## About Project:

This project involves deploying a **user registration form** on an **Amazon EC2 instance** using open-source technologies. Users can enter their personal details (name, age, gender, city, email, and password), which are then securely stored in a **MySQL (MariaDB)** database using **PHP**. The system is hosted on a **Linux-based Nginx web server** and is built for scalability, reliability, and ease of deployment.

This kind of setup is common in real-world web development, especially for creating login/registration portals, sign-up systems, or membership-based applications.

## Technologies Used:

- **PHP:** Backend logic to process form data
- **MariaDB:** MySQL-compatible database for storing user records
- **HTML:** Frontend form for user input
- **SSH:** Secure terminal access to EC2 instance
- **Nginx**: Web server used to forward HTTP requests to the Node.js app.
- **AWS EC2 (Amazon Elastic Compute Cloud)**: Virtual server where the app is hosted.
- **Amazon Linux**: The operating system used on the EC2 instance.

## Step 1: Launch an EC2 Instance

1. Go to AWS Console → EC2
2. Launch Instance.
    - Name → register_form
    - Choose AMI → Amazon Linux.
    - Instance type → t2.micro
    - Key pair → pem_server_key
    - security group → launch-wizard-1

## Step 2: Connect and Install Packages

1. Connect to EC2 via SSH

```bash
ssh -i "pem-server-key.pem" ec2-user@ec2-107-20-44-28.compute-1.amazonaws.com
```

2. Update system

```bash
sudo yum update
```

3. Install nginx then start & enable

```bash
sudo yum install nginx -y
sudo systemctl start nginx
sudo systemctl enable nginx
```

4. Install mariadb105-server then start & enable

```bash
sudo yum install mariadb105-server -y
sudo systemctl start mariadb
sudo systemctl enable mariadb
```

5. Install php-fpm then start & enable

```bash
sudo yum install php-fpm -y
sudo systemctl start php-fpm
sudo systemctl enable php-fpm
```

6. Create file → signup.html

```bash
sudo vim /usr/share/nginx/html/signup.html
```

code of signup.html file:

```html
 <!DOCTYPE html>
 <html>
 <head>
 <title>Signup Form</title>
 </head>
 <body>
 <h2>Signup Form</h2>
 <form action="submit.php" method="post">
 <label for="name">Name:</label><br>
 
 <input type="text" id="name" name="name" required><br><br>
 <label for="email">Email:</label><br>
 
 <input type="email" id="email" name="email" required><br><br>
 <label for="website">Website:</label><br>
 
 <input type="url" id="website" name="website"><br><br>
 <label for="comment">Comment:</label><br>
 
 <textarea id="comment" name="comment" rows="4" cols="50"></textarea><br><br>
 <label>Gender:</label><br>
 <input type="radio" id="female" name="gender" value="female" required>
 <label for="female">Female</label><br>
 <input type="radio" id="male" name="gender" value="male">
 <label for="male">Male</label><br>
 <input type="radio" id="other" name="gender" value="other">
 <label for="other">Other</label><br><br>
 <input type="submit" value="Submit">
 </form>
 </body>
 </html>
```

7. log into the mysql db and set password
```bash
sudo mysql
alter user root@localhost identified by "root";
exit;
```

8.  Create the MySQL Database 

```bash
sudo mysql -u root -p
"password -> root"
create database myntra;
use myntra;
```

9. Inside the MySQL create Table:

```sql
CREATE TABLE users (
 id INT PRIMARY KEY AUTO_INCREMENT,
 name VARCHAR(20),
 email VARCHAR(100),
 website VARCHAR(255),
 gender VARCHAR(6),
 comment VARCHAR(100)
 );

exit;
```

10. Create php file → submit.php

```bash
sudo vim /usr/share/nginx/html/submit.php
```

Code of register.php file:

```php
 <?php
error_reporting(E_ALL);
ini_set('display_errors', 1);

$name    = $_POST['name'];
$email   = $_POST['email'];
$website = $_POST['website'];
$comment = $_POST['comment'];
$gender  = $_POST['gender'];

// Database connection
$servername = "localhost";
$username   = "root";
$password   = "root";
$dbname     = "myntra";

// Create connection
$conn = mysqli_connect($servername, $username, $password, $dbname);

// Check connection
if (!$conn) {
    die("Connection failed: " . mysqli_connect_error());
}

// Insert query
$sql = "INSERT INTO users (name, email, website, comment, gender)
        VALUES ('$name', '$email', '$website', '$comment', '$gender')";
?>
<!DOCTYPE html>
<html>
<head>
    <title>Form Submission Result</title>
</head>
<body>
<?php
if (mysqli_query($conn, $sql)) {
    echo "<h2>✅ New record created successfully!</h2>";
    echo "<h3>Submitted Information:</h3>";
    echo "<ul>";
    echo "<li><strong>Name:</strong> " . htmlspecialchars($name) . "</li>";
    echo "<li><strong>Email:</strong> " . htmlspecialchars($email) . "</li>";
    echo "<li><strong>Website:</strong> " . htmlspecialchars($website) . "</li>";
    echo "<li><strong>Comment:</strong> " . htmlspecialchars($comment) . "</li>";
    echo "<li><strong>Gender:</strong> " . htmlspecialchars($gender) . "</li>";
    echo "</ul>";
} else {
    echo "<h3>❌ Error: " . $sql . "<br>" . mysqli_error($conn) . "</h3>";
}

mysqli_close($conn);
?>
</body>
</html>
```

11. install connector for MySQL 

```bash
sudo yum install php8.4-mysqlnd.x86_64 -y
```

12. restart your nginx & php-fpm 

```bash
sudo systemctl restart nginx
sudo systemctl restart php-fpm
```

13. now checking your database connected 

```bash
<enter-your-public-ip>/signup.html
```

## Step 3: Terminating Your instance

1. Your use are done then got to AWS console 
2. Click on EC2 → instance 
3. Select instance You want to terminated
4. Click on Instance state 
5. Choose **Terminate (delete) instance**
6. Now click delete

