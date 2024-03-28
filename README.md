DeleteUserController
<?php
// delete user controller
// require_once 'model/Delete.php';

// $delete = new Delete();

// if ($_SERVER["REQUEST_METHOD"] == "POST") {
//     if (isset($_POST["deleteUser"])) {
//         $result = $delete->deleteUser($_SESSION["id"]);

//         if ($result) {

//             session_destroy();

//             header("Location: index.php?route=login");
            
//         } else {
//             echo "Error deleting user.";
//         }
//     }
// }
class DeleteUserController{
    public function __construct() {
        $this->index();
     }
 
     public function index() {
        $delete = new Delete();

        if ($_SERVER["REQUEST_METHOD"] == "POST") {
            if (isset($_POST["deleteUser"])) {
                $result = $delete->deleteUser($_SESSION["id"]);
        
                if ($result) {
        
                    session_destroy();
        
                    header("Location: /login/index.php");
                    
                } else {
                    echo "Error deleting user.";
                }
            }
        }
     }
}

EditController
<?php

// edit profile page controller

// require_once 'model/Select.php';

// $select = new Select();

// if (!empty($_SESSION["id"])) {
//     $user = $select->selectUserById($_SESSION["id"]);
//     require 'view/editView.php';
// } else {
//     header("Location: index.php?route=login");
// }
Class EditController {
    public function __construct() {
        $this->index();
     }
 
     public function index() {
        $select = new Select();

        if (!empty($_SESSION["id"])) {
            $user = $select->selectUserById($_SESSION["id"]);
            require 'view/editView.php';
        } else {
            header("Location: /logion/index.php");
        }
     }
}
IndexController
<?php

// setting session at index page and show user data

// require_once 'model/Select.php';
// require_once 'model/Delete.php';

// $select = new Select();
// $delete = new Delete();

// if(!empty($_SESSION["id"])){
//     $user = $select->selectUserById($_SESSION["id"]);
// } else {
//     header("Location: index.php?route=login");
// }

// require 'view/indexView.php';

// if ($_SERVER["REQUEST_METHOD"] == "POST") {
//     if (isset($_POST["deleteUser"])) {
//         $result = $delete->deleteUser($_SESSION["id"]);

//         if ($result) {
//             session_destroy();
//             header("Location: index.php?route=login");
//         } else {
//             echo "Error deleting user.";
//         }
//     }
// }

class IndexController {

    public function __construct() {
       $this->index();
    }

    public function index() {

        if(!empty($_SESSION["id"])){
            $select = new Select();
            $user = $select->selectUserById($_SESSION["id"]);
        } else {
            header("Location: /login/index.php");
        }
        
        require 'view/indexView.php';
        
        if ($_SERVER["REQUEST_METHOD"] == "POST") {
            if (isset($_POST["deleteUser"])) {
                $delete = new Delete();
                $result = $delete->deleteUser($_SESSION["id"]);
        
                if ($result) {
                    session_destroy();
                    header("Location: /login/index.php");
                } else {
                    echo "Error deleting user.";
                }
            }
        }
    }
}

LoginController
<?php

//login controller and showing all validation messages 

require_once 'model/Login.php';


// $login = new Login();

// if(isset($_POST["submit"])){
//     $result = $login->loginn($_POST["usernameemail"], $_POST["password"]);
//    if($result == 1){
//     $error = ' Password';
//          $_SESSION["login"] = true;
//           $_SESSION["id"] = $login->idUser();
         
//         header("Location: index.php");
//     } elseif($result == 2){
//      $error = 'Wrong Password';
//     } elseif($result == 3){
//         $error = 'User Not Registered';
//     }
// }
// require 'view/loginView.php';

Class LoginController{
    public function __construct() {
        $this->index();
     }
 
     public function index() {
        if(isset($_POST["submit"])){
            $objLogin = new Login();
            $result = $objLogin->loginn($_POST["usernameemail"], $_POST["password"]);
           if($result == 1){
            $error = ' Password';
                 $_SESSION["login"] = true;
                  $_SESSION["id"] = $objLogin->idUser();
                 
                header("Location: /login/index.php/indexController");
            } elseif($result == 2){
             $error = 'Wrong Password';
            } elseif($result == 3){
                $error = 'User Not Registered';
            }
        }
        require 'view/loginView.php';
     }
}
?>
LogoutController
<?php
//logout user controller

// remove all session variables
// session_unset();
// // destroy the session
// session_destroy();
// header("Location: index.php");
class LogoutController {
public function __construct() {
    $this->index();
 }

 public function index() {
    session_unset();
    // destroy the session
    session_destroy();
    header("Location: /login/index.php");
 }
}

?>

RegistrationController

<?php
//registration controller and showing error messages on screen
//  require_once 'model/Register.php';
//  $register = new Register();
//  if(isset($_POST["submit"])){
//      $result = $register->registration($_POST["name"], $_POST["username"], $_POST["email"], $_POST["password"], $_POST["confirmpassword"]);
//      if($result == 1){
//          $message = 'Registration Successful please log in';
      
//      } elseif($result == 2){
//          $error = 'Username or Email Has Already Taken';
//      } elseif($result == 3){
//          $error = 'Password Does Not Match';
//      } elseif($result == 4){
//          $error = 'Name must contain only alphabets';
//      }
//      elseif($result == 5){
//          $error = 'Password must be 8 digit';
//      }
//  }
//  require 'view/registrationView.php';

Class RegistrationController{
    public function __construct() {
        $this->index();
     }
 
     public function index() {
        if(isset($_POST["submit"])){
            $register = new Register();
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
     }
}

UpdateUserController

<?php

//update user's data controller and sending to index

// require_once 'model/Select.php';
// require_once 'model/Update.php';

// if ($_SERVER["REQUEST_METHOD"] == "POST") {
//     $update = new Update();
//     $result = $update->updateUser(
//         $_POST['id'],
//         $_POST['name'],
//         $_POST['username'],
//         $_POST['email'],
//         $_POST['password']
//     );
//     if ($result) {
//         header("Location: index.php?route=index");
//     } else {
//         echo "Error updating user";
//     }
// } else {
//     header("Location: index.php?route=edit");
// }
Class UpdateUserController {

    public function __construct() {
        $this->index();
     }
 
     public function index() {
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
                header("Location: /login/index.php/IndexController");
            } else {
                echo "Error updating user";
            }
        } else {
            header("Location: /login/index.php/EditController");
        }
     }
}

model
Connection
<?php

//connection class for db
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

Delete
<?php
//delete data query
class Delete extends Connection {
    public function deleteUser($id) {
        $result = mysqli_query($this->conn, "DELETE FROM tb_user WHERE id = '$id'");
        return $result;
    }
}

includeFile
<?php
include_once 'model\Connection.php';
include_once 'model\Login.php';
include_once 'model\Select.php';
include_once 'model\Delete.php';
include_once 'model\Register.php';
include_once 'model\Update.php';
include_once 'controller\LoginController.php';
include_once 'controller\IndexController.php';
include_once 'controller\RegistrationController.php';
include_once 'controller\DeleteUserController.php';
include_once 'controller\LogoutController.php';
include_once 'controller\EditController.php';
include_once 'controller\UpdateUserController.php';
?>
Login
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




