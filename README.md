
https://github.com/mostafaMahfouz25/crud-mvc-php


php

require_once 'model/Login.php';



//  if(!empty($_SESSION["id"])){

//      header("Location: index.php");

//  }



$login = new Login();



if(isset($_POST["submit"])){



    $result = $login->login($_POST["usernameemail"], $_POST["password"]);



    if($result == 1){

        $_SESSION["login"] = true;

        $_SESSION["id"] = $login->idUser();

        header("Location: index.php");

    } elseif($result == 2){

        $error = 'Wrong Password';

    } elseif($result == 3){

        $error = 'User Not Registered';

    }

}



require 'view/loginView.php';

?>


...........indexcontroller.php
<?php

require_once 'model/Select.php';





$select = new Select();

 if(!empty($_SESSION["id"])){

     $user = $select->selectUserById($_SESSION["id"]);

 } else {

     header("Location: index.php?route=login");

 }

require 'view/indexView.php';

?>

...........loginController

<?php
require_once 'model/Select.php';


$select = new Select();
 if(!empty($_SESSION["id"])){
     $user = $select->selectUserById($_SESSION["id"]);
 } else {
     header("Location: index.php?route=login");
 }
require 'view/indexView.php';
?>

...........logoutController

<?php

// remove all session variables

session_unset();



// destroy the session

session_destroy();

header("Location: index.php");



?>
.........registrationController

<?php

require_once 'model/Register.php';



//  if(!empty($_SESSION["id"])){

//      header("Location: index.php");

//  }



$register = new Register();



if(isset($_POST["submit"])){



    $result = $register->registration($_POST["name"], $_POST["username"], $_POST["email"], $_POST["password"], $_POST["confirmpassword"]);



    if($result == 1){

        $message = 'Registration Successful please log in';

    } elseif($result == 2){

        $error = 'Username or Email Has Already Taken';

    } elseif($result == 3){

        $error = 'Password Does Not Match';

    } elseif($result == 4){

        $error = 'Name must contain only alphabets';

    }

    elseif($result == 5){

        $error = 'Password must be 8 digit';

    }

}



require 'view/registrationView.php';

?>
................ Connection.php

<?php

class Connection{

  public $host = "localhost";

  public $user = "root";

  public $password = "";

  public $db_name = "oop_reglog";

  public $conn;



  public function __construct(){

    $this->conn = mysqli_connect($this->host, $this->user, $this->password, $this->db_name);

  }

}

?>
.......Login.php
<?php

class Login extends Connection{

  public $id;

  public function login($usernameemail, $password){

    $result = mysqli_query($this->conn, "SELECT * FROM tb_user WHERE username = '$usernameemail' OR email = '$usernameemail'");

    $row = mysqli_fetch_assoc($result);



    if(mysqli_num_rows($result) > 0){

      if($password == $row["password"]){

        $this->id = $row["id"];

        return 1;

        // Login successful

      }

      else{

        return 2;

        // Wrong password

      }

    }

    else{

      return 3;

      // User not registered

    }

  }



  public function idUser(){

    return $this->id;

  }

}

?>
.......................... Register.php
<?php

class Register extends Connection{

  public function registration($name, $username, $email, $password, $confirmpassword){

    $rexName = "/^[a-zA-Z]+(?:\s?[a-zA-Z]+)*\.?$/";

    $rexPass = "/^.{8,}$/";



    if (!preg_match($rexName, $name)) {

      return 4;

      // Name contains numbers

    }

    if (!preg_match($rexPass, $password)) {

      return 5;

      // password 8 digit

        }





    $duplicate = mysqli_query($this->conn, "SELECT * FROM tb_user WHERE username = '$username' OR email = '$email'");

    if(mysqli_num_rows($duplicate) > 0){

      return 2;

      // Username or email has already taken

    }

    else{

      if($password == $confirmpassword){

        $query = "INSERT INTO tb_user  VALUES('', '$name', '$username', '$email', '$password')";

        mysqli_query($this->conn, $query);

        return 1;

        // Registration successful

      }

      else{

        return 3;

        // Password does not match

      }

    }

  }

}

?>
................Select php
<?php

class Select extends Connection{

  public function selectUserById($id){

    $result = mysqli_query($this->conn, "SELECT * FROM tb_user WHERE id = $id");

    return mysqli_fetch_assoc($result);

  }

}

?>

...................index.css



body {

    font-family: Arial, sans-serif;

    background-color: #f4f4f4;



}

.containner {

    max-width: 500px;

    margin: 0 auto;

    background-color: #fff;

    padding: 20px;

    border-radius: 5px;

    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);

}



h1 {

    color: #333;

    text-align: center;

    margin-top: 50px;

}



a {

    color: #fff;

    text-decoration: none;

}



div{

    text-align: center;   

    width: 96%;

    padding: 10px;

    background-color: #007bff;

    border: none;

    border-radius: 5px;

    cursor: pointer;

}



a:hover {

    text-decoration: underline;

}



.container {

    text-align: center;

    margin-top: 50px;

}

..............  login.css


body {

    font-family: Arial, sans-serif;

    background-color: #f4f4f4;

}



h2 {

    color: #333;

    text-align: center;

}



.containner {

    max-width: 300px;

    margin: 0 auto;

    background-color: #fff;

    padding: 20px;

    border-radius: 5px;

    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);

}



label {

    display: block;

    margin-bottom: 10px;

}



input[type="text"],

input[type="password"] {

    width: 93%;

    padding: 10px;

    margin-bottom: 10px;

    border: 1px solid #ccc;

    border-radius: 5px;

}



button {

    width: 100%;

    padding: 10px;

    background-color: #007bff;

    color: #fff;

    border: none;

    border-radius: 5px;

    cursor: pointer;

}



button:hover {

    background-color: #0056b3;

}



a {

    color: #007bff;

    text-decoration: none;

    text-align: center;

}



a:hover {

    text-decoration: underline;

}



span{

    margin-left: 30px;

}



p {

    padding: 20px;

    background-color: #f44336; 

    color: white;

    margin-bottom: 15px;

  }

.................register.css  



body {

    font-family: Arial, sans-serif;

    background-color: #f4f4f4;

}



h2 {

    color: #333;

    text-align: center;

}



.containner {

    max-width: 300px;

    margin: 0 auto;

    background-color: #fff;

    padding: 20px;

    border-radius: 5px;

    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);

}



label {

    display: block;

    margin-bottom: 10px;

}



input[type="text"],

input[type="email"],

input[type="password"] {

    width: 93%;

    padding: 10px;

    margin-bottom: 10px;

    border: 1px solid #ccc;

    border-radius: 5px;

}



button {

    width: 100%;

    padding: 10px;

    background-color: #007bff;

    color: #fff;

    border: none;

    border-radius: 5px;

    cursor: pointer;

}



button:hover {

    background-color: #0056b3;

}



a {

    color: #007bff;

    text-decoration: none;

    text-align: center;

}



a:hover {

    text-decoration: underline;

}



span{

    margin-left: 30px;

}



p {

    padding: 20px;

    background-color: #f44336; 

    color: white;

    margin-bottom: 15px;

  }
................indexView.php
<!DOCTYPE html>

<html lang="en" dir="ltr">

<head>

    <meta charset="utf-8">

    <title>Index</title>

    <link rel="stylesheet" href="public/css/index.css">



</head>

<body> <div class = "containner">

        <h1>Welcome <?php echo $user["name"]; ?></h1>

        <h1>Username :<?php echo $user["username"]; ?> </h1>

        <h1>E-mail :<?php echo $user["email"]; ?> </h1>

        <a href="?route=logout"><div>Logout</div></a>

</div>

</body>

</html>

................loginView.php
<!DOCTYPE html>

<html lang="en" dir="ltr">

<head>

    <meta charset="utf-8">

    <title>Login</title>

    <link rel="stylesheet" href="public/css/login.css">



</head>

<body>

    <div class = containner>

    <h2>Login</h2>

    <?php if(isset($error)) echo "<p>$error</p>"; ?>

    <form class="" action="" method="post" autocomplete="off">

        <label for="">Username or Email : </label>

        <input type="text" name="usernameemail" required value=""> <br>

        <label for="">Password</label>

        <input type="password" name="password" required value=""> <br>

        <button type="submit" name="submit">Login</button>

    </form>

    <br>

   <span>Don't have an account ? </span><a href="?route=registration">Register</a>

</div>

</body>

</html>

..
................registerationView.php
<!DOCTYPE html>

<html lang="en" dir="ltr">

<head>

    <meta charset="utf-8">

    <title>Registration</title>

    <link rel="stylesheet" href="public/css/register.css">



</head>

<body>

    <div class ="containner">

    <h2>Registration</h2>

    <?php if(isset($error)) echo "<p>$error</p>"; ?>

    <?php if(isset($message)) echo "<p>$message</p>"; ?>

    <form class="" action="" method="post" autocomplete="off">

        <label for="">Name : </label>

        <input type="text" name="name" required value=""> <br>

        <label for="">Username : </label>

        <input type="text" name="username" required value=""> <br>

        <label for="">Email : </label>

        <input type="email" name="email" required value=""> <br>

        <label for="">Password : </label>

        <input type="password" name="password" required value=""> <br>

        <label for="">Confirm Password : </label>

        <input type="password" name="confirmpassword" required value=""> <br>

        <button type="submit" name="submit">Register</button>

    </form>

    <br> 

    <span>Already have an account ? <a href="?route=login">Login</a>

</div>

</body>

</html>
......................index.php
<?php





session_start();









 require_once 'model/Connection.php';

require_once 'model/Register.php';

require_once 'model/Login.php';

require_once 'model/Select.php';



$route = $_GET['route'] ?? '';

if (empty($route)) {

    $route = $_POST['route'] ?? '';

}

switch ($route) {

    case '':

        require 'controller/indexController.php';

        break;

    case 'login':

        require 'controller/loginController.php';

        break;

    case 'registration':

        require 'controller/registrationController.php';

        break;

    case 'logout':

        require 'controller/logoutController.php';    

        break;

    default:

        echo '404 Not Found';

        break;

}

?>
























































................. connection.php

<?php

class Connection {

    private $host = "localhost";

    private $username = "root";

    private $password = "";

    private $database = "crud";

    private static $instance;

    private $connection;



    private function __construct() {

        $this->connection = new mysqli($this->host, $this->username, $this->password, $this->database);

        if ($this->connection->connect_error) {

            die("Connection failed: " . $this->connection->connect_error);

        }

    }



    public static function getInstance() {

        if (!self::$instance) {

            self::$instance = new self();

        }

        return self::$instance;

    }



    public function getConnection() {

        return $this->connection;

    }

}

?>

...............indexController.php

<?php

require_once 'model/User.php';



$action = $_GET['action'] ?? '';



switch ($action) {

    case 'create':

        if ($_SERVER["REQUEST_METHOD"] == "POST") {

            $name = $_POST['name'];

            $email = $_POST['email'];

            $user = new User();

            $user->create($name, $email);

        }

        break;

    case 'read':

        $users = User::getAllUsers();

        break;

    case 'update':

        if ($_SERVER["REQUEST_METHOD"] == "POST") {

            $id = $_POST['id'];

            $name = $_POST['name'];

            $email = $_POST['email'];

            $user = new User($id);

            $user->update($name, $email);

        }

        break;

    case 'delete':

        if (isset($_GET['id'])) {

            $id = $_GET['id'];

            $user = new User($id);

            $user->delete();

        }

        break;

    default:

        $users = User::getAllUsers();

        break;

}



include 'view/indexView.php';

?>


......... indexview.php

<!DOCTYPE html>

<html lang="en">

<head>

    <meta charset="UTF-8">

    <title>User CRUD</title>

</head>

<body>

    <h1>User CRUD</h1>



    <!-- Form for creating a new user -->

    <form action="?action=create" method="post">

        <label for="name">Name:</label>

        <input type="text" id="name" name="name" required>

        <label for="email">Email:</label>

        <input type="email" id="email" name="email" required>

        <button type="submit">Create User</button>

    </form>



    <!-- Table to display existing users -->

    <table border="1">

        <thead>

            <tr>

                <th>ID</th>

                <th>Name</th>

                <th>Email</th>

                <th>Actions</th>

            </tr>

        </thead>

        <tbody>

            <!-- Loop through users and display them -->

            <?php foreach ($users as $user): ?>

                <tr>

                    <td><?= $user->getId() ?></td>

                    <td><?= $user->getName() ?></td>

                    <td><?= $user->getEmail() ?></td>

                    <td>

                        <a href="?action=update&id=<?= $user->getId() ?>">Edit</a>

                        <a href="?action=delete&id=<?= $user->getId() ?>" onclick="return confirm('Are you sure you want to delete this user?')">Delete</a>

                    </td>

                </tr>

            <?php endforeach; ?>

        </tbody>

    </table>

</body>

</html>

...........user.php
<?php

require_once 'Connection.php';



class User {

    private $id;

    private $name;

    private $email;

    private $conn;



    public function __construct($id = null) {

        $this->conn = Connection::getInstance()->getConnection();

        if ($id) {

            $result = mysqli_query($this->conn, "SELECT * FROM users WHERE id = $id");

            $row = mysqli_fetch_assoc($result);

            $this->id = $row['id'];

            $this->name = $row['name'];

            $this->email = $row['email'];

        }

    }



    public function create($name, $email) {

        $sql = "INSERT INTO users (name, email) VALUES ('$name', '$email')";

        mysqli_query($this->conn, $sql);

    }



    public static function getAllUsers() {

        require_once 'Connection.php';

        $conn = Connection::getInstance()->getConnection();

        $result = mysqli_query($conn, "SELECT * FROM users");

        $users = [];

        while ($row = mysqli_fetch_assoc($result)) {

            $user = new self();

            $user->id = $row['id'];

            $user->name = $row['name'];

            $user->email = $row['email'];

            $users[] = $user;

        }

        return $users;

    }



    public function update($name, $email) {

        $sql = "UPDATE users SET name = '$name', email = '$email' WHERE id = $this->id";

        mysqli_query($this->conn, $sql);

    }



    public function delete() {

        $sql = "DELETE FROM users WHERE id = $this->id";

        mysqli_query($this->conn, $sql);

    }



    public function getId() {

        return $this->id;

    }



    public function getName() {

        return $this->name;

    }



    public function getEmail() {

        return $this->email;

    }

}

?>











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
