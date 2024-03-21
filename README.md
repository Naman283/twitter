config/database.php
<?php
// Database connection parameters
define('DB_HOST', 'localhost');
define('DB_USER', 'your_username');
define('DB_PASS', 'your_password');
define('DB_NAME', 'your_database_name');

controllers/UserController.php

<?php
require_once '../models/User.php';

class UserController {
    public function signup($username, $password) {
        $user = new User();
        return $user->create($username, $password);
    }

    public function login($username, $password) {
        $user = new User();
        return $user->authenticate($username, $password);
    }
}
.............models/User.php

<?php
require_once '../config/database.php';

class User {
    private $conn;

    public function __construct() {
        $this->conn = new mysqli(DB_HOST, DB_USER, DB_PASS, DB_NAME);
        if ($this->conn->connect_error) {
            die("Connection failed: " . $this->conn->connect_error);
        }
    }

    public function create($username, $password) {
        $hashed_password = password_hash($password, PASSWORD_DEFAULT);
        $sql = "INSERT INTO users (username, password) VALUES ('$username', '$hashed_password')";
        if ($this->conn->query($sql) === TRUE) {
            return true;
        } else {
            return false;
        }
    }

    public function authenticate($username, $password) {
        $sql = "SELECT * FROM users WHERE username='$username'";
        $result = $this->conn->query($sql);
        if ($result->num_rows == 1) {
            $row = $result->fetch_assoc();
            if (password_verify($password, $row['password'])) {
                return true;
            } else {
                return false;
            }
        } else {
            return false;
        }
    }
}
.........views/signup.php

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Sign Up</title>
</head>
<body>
    <h2>Sign Up</h2>
    <form action="../index.php?action=signup" method="post">
        <input type="text" name="username" placeholder="Username" required><br><br>
        <input type="password" name="password" placeholder="Password" required><br><br>
        <button type="submit">Sign Up</button>
    </form>
</body>
</html>


........views/login.php
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Login</title>
</head>
<body>
    <h2>Login</h2>
    <form action="../index.php?action=login" method="post">
        <input type="text" name="username" placeholder="Username" required><br><br>
        <input type="password" name="password" placeholder="Password" required><br><br>
        <button type="submit">Login</button>
    </form>
</body>
</html>


.......index.php
<?php
$action = isset($_GET['action']) ? $_GET['action'] : '';

require_once 'controllers/UserController.php';

$userController = new UserController();

switch ($action) {
    case 'signup':
        if ($_SERVER['REQUEST_METHOD'] === 'POST') {
            $username = $_POST['username'];
            $password = $_POST['password'];
            $result = $userController->signup($username, $password);
            if ($result) {
                echo "User registered successfully.";
            } else {
                echo "User registration failed.";
            }
        } else {
            include 'views/signup.php';
        }
        break;
    case 'login':
        if ($_SERVER['REQUEST_METHOD'] === 'POST') {
            $username = $_POST['username'];
            $password = $_POST['password'];
            $result = $userController->login($username, $password);
            if ($result) {
                echo "Login successful.";
            } else {
                echo "Invalid username or password.";
            }
        } else {
            include 'views/login.php';
        }
        break;
    default:
        echo "Invalid action.";
}
.......https://github.com/Eathorne2/PHP-Student-management
https://drive.google.com/file/d/1mJcZiomXdLe34nhQo304OGLlY5lixng2/view?usp=sharing
https://www.free-css.com/free-css-templates/page254/minimalista
