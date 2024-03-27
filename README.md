You need to manage these things.
- proper routing

- proper routing validation ( if controller not found then show message )

- Proper class and methods define.

- Model will call in controllers.


tum direct controller m code kr rhe ho
controller ki classes methods ye sb ?
no error handling
complete MVC m issue hain

..............controller/deleteUserControllerController.php

<?php
// delete user controller
require_once 'model/Delete.php';

$delete = new Delete();

if ($_SERVER["REQUEST_METHOD"] == "POST") {
    if (isset($_POST["deleteUser"])) {
        $result = $delete->deleteUser($_SESSION["id"]);

        if ($result) {

            session_destroy();

            header("Location: index.php?route=login");
            
        } else {
            echo "Error deleting user.";
        }
    }
}


............controller/editController.php
<?php

// edit profile page controller

require_once 'model/Select.php';

$select = new Select();

if (!empty($_SESSION["id"])) {
    $user = $select->selectUserById($_SESSION["id"]);
    require 'view/editView.php';
} else {
    header("Location: index.php?route=login");
}

...........controller/errorController.php
<?php
    require 'view/errors/errorView.php';

..........,,controller/indexController.php


<?php

// setting session at index page and show user data

require_once 'model/Select.php';
require_once 'model/Delete.php';

$select = new Select();
$delete = new Delete();

if(!empty($_SESSION["id"])){
    $user = $select->selectUserById($_SESSION["id"]);
} else {
    header("Location: index.php?route=login");
}

require 'view/indexView.php';

if ($_SERVER["REQUEST_METHOD"] == "POST") {
    if (isset($_POST["deleteUser"])) {
        $result = $delete->deleteUser($_SESSION["id"]);

        if ($result) {
            session_destroy();
            header("Location: index.php?route=login");
        } else {
            echo "Error deleting user.";
        }
    }
}

...........controller/loginController.php

<?php

//login controller and showing all validation messages 

require_once 'model/Login.php';


$login = new Login();

if(isset($_POST["submit"])){
    $result = $login->loginn($_POST["usernameemail"], $_POST["password"]);
   if($result == 1){
    $error = ' Password';
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


................controller/logoutController.php

<?php
//logout user controller

// remove all session variables
session_unset();
// destroy the session
session_destroy();
header("Location: index.php");
?>

..............controller/registrationController.php

<?php
//registration controller and showing error messages on screen
 require_once 'model/Register.php';
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



..........controller/updateUserController.php
<?php

//update user's data controller and sending to index

require_once 'model/Select.php';
require_once 'model/Update.php';

if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $update = new Update();
    $result = $update->updateUser(
        $_POST['id'],
        $_POST['name'],
        $_POST['username'],
        $_POST['email'],
        $_POST['password']
    );
    if ($result) {
        header("Location: index.php?route=index");
    } else {
        echo "Error updating user";
    }
} else {
    header("Location: index.php?route=edit");
}


..........model/Connection.php

<?php

//connection class for db
class Connection{
  public $host = "localhost";
  public $user = "root";
  public $password = "webkul";
  public $db_name = "regist";
  public $conn;
  public function __construct(){
    $this->conn = mysqli_connect($this->host, $this->user, $this->password, $this->db_name);
  }
}
?>

...........model/Delete.php
<?php
//delete data query
class Delete extends Connection {
    public function deleteUser($id) {
        $result = mysqli_query($this->conn, "DELETE FROM tb_user WHERE id = '$id'");
        return $result;
    }
}

.............model/Login.php
<?php

//login page and validation errors 

class Login extends Connection{
  public $id;
  public function loginn($usernameemail, $password){
    $result = mysqli_query($this->conn, "SELECT * FROM tb_user WHERE username = '$usernameemail' OR email = '$usernameemail'") ;
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

............model/Register.php

<?php
//registration page and setting up validtions
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
     $duplicate = mysqli_query($this->conn, "SELECT * FROM tb_user WHERE username = '".$username."' OR email = '".$email."'");
     if(mysqli_num_rows($duplicate) > 0){
       return 2;
       // Username or email has already taken
     }
     else{
       if($password == $confirmpassword){
         $query = "INSERT INTO tb_user (name, username, email, password)  VALUES( '".$name."', '".$username."', '".$email."', '".$password."')";
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


.............model/Select.php
<?php
// selecting data for index page 
class Select extends Connection{
  public function selectUserById($id){
    $result = mysqli_query($this->conn, "SELECT * FROM tb_user WHERE id = '$id'");
    return mysqli_fetch_assoc($result);
  }
}

?>
.............model/Update.php
<?php

//update data query 

class Update extends Connection {
    public function updateUser($id, $name, $username, $email, $password) {
        $result = mysqli_query($this->conn, "UPDATE tb_user SET name = '$name', username = '$username', email = '$email', password = '$password' WHERE id = '$id'");
        return $result;
    }
}

............public/css/edit.css

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

  ..........public/css/index.css


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

.update {
    margin-top: 30px;

}

div{
    text-align: center;   
    width: 96%;
    padding: 10px;
    background-color: #007bff;
    border: none;
  border-radius: 5px;
    cursor: pointer;
    margin: 3px;

}



a:hover {
    text-decoration: underline;
}



.container {

    text-align: center;
    margin-top: 50px;

}
button, input[type="submit"] {
	background: none;
	color: inherit;
	border: none;
	padding: 0;
	font: inherit;
	cursor: pointer;
	outline: inherit;
    color: #fff;

}
.dlt {
    width : 480px;

}


....................public/css/login.css


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

  ..............public/css/register.css

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


..............view/errors/errorView.php
<!DOCTYPE html>
<html>
<head>
<title>Custom 404</title>
</head>
<body>

<h3>Sorry, This controller file is not available....</h3> 

</body>
</html>


.............view/editView.php
<!DOCTYPE html>
<html lang="en" dir="ltr">
<head>
    <meta charset="utf-8">
    <title>Edit User</title>
    <link rel="stylesheet" href="public/css/edit.css">
</head>
<body>
    <div class = "containner">
        <h1>Edit User Details</h1>
        <form action="?route=updateUser" method="post">
            <input type="hidden" name="id" value="<?php echo $user['id']; ?>">
            <label for="name">Name:</label>
            <input type="text" name="name" id="name" value="<?php echo $user['name']; ?>" required><br>
            <label for="username">Username:</label>
            <input type="text" name="username" id="username" value="<?php echo $user['username']; ?>" required><br>
            <label for="email">Email:</label>
            <input type="email" name="email" id="email" value="<?php echo $user['email']; ?>" required><br>
            <label for="password">Password:</label>
            <input type="password" name="password" id="password" required><br>
            <button type="submit">Update User</button>
        </form>
        <a href="?route=index"><div>Back to Home</div></a>
    </div>
</body>
</html>


.............view/indexView.php

<!DOCTYPE html>
<html lang="en" dir="ltr">
<head>
    <meta charset="utf-8">
    <title>Index</title>
    <link rel="stylesheet" href="public/css/index.css">
</head>
<body>
    <div class = "containner">
        <h1>Welcome <?php echo $user["name"]; ?></h1>
        <h1>Username :<?php echo $user["username"]; ?> </h1>
        <h1>E-mail :<?php echo $user["email"]; ?> </h1>
        <a href="?route=edit"><div>Edit Details</div></a>
        
        <form class="bttn" action="?route=deleteUserController" method="post">
            <input type="hidden" name="id" value="<?php echo $user['id']; ?>">
          
            <button type="submit" name="deleteUser"><div class="dlt">Delete Account</div></button>

        </form>

        <a href="?route=logout"><div>Logout</div></a>
    </div>
</body>
</html>


...............view/loginView.php
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
    <?php if(isset($message)) echo "<p>$message</p>"; ?>
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


............view/registrationView.php
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



...........index.php

<?php
ini_set('display_errors', 1);
ini_set('display_startup_errors', 1);
error_reporting(E_ALL);

require_once 'router.php';


?>

.............router.php
<?php

//displaying all PHP errors
ini_set('display_errors', 1);
ini_set('display_startup_errors', 1);
error_reporting(E_ALL);
session_start();
// Autoload classes
spl_autoload_register(function ($className) {
    include_once 'model/' . $className . '.php';
});
// Check if the requested route exists
if (isset($_REQUEST['route']) && !empty($_REQUEST['route'])) {
    $route = $_REQUEST['route'];
    $controllerPath = 'controller/' . $route . 'Controller.php';
    // Check if the controller file exists
    if (file_exists($controllerPath)) {
        require $controllerPath;
    } else {
        // If the controller file does not exist, display a error message
        $route = 'error';       
    }
} else {
    $route = 'index';
}
// Include the requested controller file
require_once 'controller/' . $route . 'Controller.php';

