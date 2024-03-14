.........................................................................index.php............................................................................................................

<?php
session_start();

require_once("classes\dbConfig.php");

if(isset($_GET["page"]) && $_GET["page"] == 'login')
{
    include_once 'controller\login.php';

} elseif(isset($_GET["page"]) && $_GET["page"] == 'signup') {

    include_once 'controller\signup.php';

} elseif(isset($_GET["page"]) && $_GET["page"] == 'signupValidation') {
    
    include_once 'controller\signupValidation.php';

} elseif(isset($_GET["page"]) && $_GET["page"] == 'loginValidation') {
    
    include_once 'controller\loginValidation.php';

} elseif(isset($_GET["page"]) && $_GET["page"] == 'dashboard') {

    include_once 'controller\dashboard.php';

} elseif(isset($_GET["page"]) && $_GET["page"] == 'profile') {

    include_once 'controller\profile.php';

} elseif(isset($_GET["page"]) && $_GET["page"] == 'logout') {

    include_once 'controller\logout.php';

} elseif(isset($_GET["page"]) && $_GET["page"] == 'editProfile') {

    include_once 'controller\editProfile.php';

} elseif(isset($_GET["page"]) && $_GET["page"] == 'editProfileValidation') {

    include_once 'controller\editProfileValidation.php';

} elseif(isset($_GET["page"]) && $_GET["page"] == 'updatePassword') {

    include_once 'controller\updatePassword.php';

} elseif(isset($_GET["page"]) && $_GET["page"] == 'updatePasswordValidation') {

    include_once 'controller\updatePasswordValidation.php';

} elseif(isset($_POST["page"]) && $_POST["page"] == 'posttweet') {

    include_once 'controller\posttweet.php';

}  elseif(isset($_POST["page"]) && $_POST["page"] == 'like') {

    include_once 'controller\like.php';

}  elseif(isset($_POST["page"]) && $_POST["page"] == 'delete_tweet') {

    include_once 'controller\delete_tweet.php';

} else {
    if(isset( $_SESSION["user_id"]) &&  $_SESSION["user_id"]){
        include_once 'controller\dashboard.php';
    } else {
        include_once 'controller\login.php';
    }
   
}
...........................................................................dbConfig.php.............................
<?php 

    $db = mysqli_connect("localhost", "root", "", "twitter") or die("Connectivity Failed");

?>

...........................................................................................dashboard.php.............
<?php

if (!isset($_SESSION["user_id"]) ||  !$_SESSION["user_id"]) {
    // remove all session variables
    session_unset();
    session_destroy();
    header('Location: index.php?page=login&err=8');
}

?>

<!DOCTYPE html>
<html>

<head>
    <title>Twitter</title>
    <link rel="stylesheet" href="views\css\global.css" />
    <link rel="stylesheet" href="views\css\happening.css" />
    <link rel="stylesheet" href="views\css\layout.css" />
    <link rel="stylesheet" href="views\css\brand.css" />
    <link rel="stylesheet" href="views\css\sidebar-menu.css" />
    <link rel="stylesheet" href="views\css\tweet.css" />
    <link rel="stylesheet" href="views\css\trends-for-you.css" />
    <link rel="stylesheet" href="https://unicons.iconscout.com/release/v3.0.6/css/line.css">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css" integrity="sha512-DTOQO9RWCH3ppGqcWaEA1BIZOC6xxalwEsw9c2QQeAIftl+Vegovlnee1c9QX4TctnWMn13TZye+giMm8e2LwA==" crossorigin="anonymous" referrerpolicy="no-referrer" />
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.3/css/all.min.css" />
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" integrity="sha384-T3c6CoIi6uLrA9TneNEoa7RxnatzjcDSCmG1MXxSR1GAsXEV/Dwwykc2MPK8M2HN" crossorigin="anonymous">
</head>

<body>
    <nav class="navbar fixed-top">
        <div class="navbar-brand">
            <a href="?page=dashboard"><img src="https://static.dezeen.com/uploads/2012/06/dezeen_twitter-bird.gif" alt="Logo"></a>
        </div>
        <div class="foryou">
            For You
        </div>
        <form class="d-flex search">
            <input class="form-control me-2" type="search" placeholder="Search" aria-label="Search">
            <button class="btn btn-primary search1" type="submit">Search</button>
        </form>
    </nav>
    <section class="landing">
        <div class="layout">
            <div class="layout__left-sidebar">
                <div class="sidebar-menu">
                    <a href="#">
                        <div class="sidebar-menu__item sidebar-menu__item--active">
                            <img src="views\img\home.svg" class="sidebar-menu__item-icon" />
                            HOME
                        </div>
                    </a>

                    <a href="?page=profile">
                        <div class="sidebar-menu__item" >
                            <img src="views\img\profile.svg" class="sidebar-menu__item-icon" />
                            Profile
                        </div>
                    </a>

                    <a href="?page=logout">
                        <div class="sidebar-menu__item">
                            <img class="sidebar-menu__item-icon" />
                            <a href="?page=logout">Log out</a>
                        </div>
                    </a>

                </div>
            </div>
            <div class="layout__main">
                <?php
                $userId = (int)$_SESSION["user_id"];

                $query = "SELECT * FROM users WHERE `id` =" . (int)$userId;

                $resultData = mysqli_query($db, $query) or die(mysqli_error($db));

                if ((mysqli_num_rows($resultData) > 0)) {
                    $userData = $resultData->fetch_assoc();
                    $name = $userData['username'];
                    $email = $userData['mail'];
                    $bio = $userData['bio'];
                    $address = $userData['address'];
                    $documentRoot = $_SERVER['DOCUMENT_ROOT'];
                    $projectDirectory = "/TwitterAssignment";
                    $uploadDirRelative = "/views/img/uploads/";
                    $uploadDir = $projectDirectory . $uploadDirRelative;
                    $uniqueFileName = $userData['filename'];
                    $uploadPath = $uploadDir . $uniqueFileName;
                    $fileExist = $documentRoot . $uploadPath;
                }
                ?>
                <div class="wrapper">
                    <div>
                        <div class="input-box">
                            <div class="avatar">
                                <img src="<?php echo (file_exists($fileExist) && isset($uniqueFileName) && ($uniqueFileName)) ? $uploadPath : 'views/img/addImage.jpg'; ?>" alt="profile" class="tweet__author-logo">
                            </div>
                            <input type="hidden" id="userId" value="<?php echo $userId ?>">
                            <div class="post-box">
                                <textarea class="post-tweet" type="text" name="tweetText" id="tweetTextId" placeholder="What's happening..."></textarea>
                            </div>
                        </div>
                    </div>
                    <div class="wrapper2">
                        <div class="up_icon">
                            <!-- Image Upload -->
                            <i class="fa-regular fa-image img1" id="postImgId">
                                <div class="text-center">
                                    <input type="file" name="tweetImage" id="tweetImageFileInput" style="display: none;">
                                </div>
                            </i>
                            <!-- Video Upload -->
                            <i class="fa-solid fa-video vid2" id="postVideoId">
                                <div class="text-center">
                                    <input type="file" name="tweetVideo" id="tweetVideoFileInput" style="display: none;">
                                </div>
                            </i>
                        </div>

                        <!-- Image Preview -->
                        <div class="container text-center m-2 mb-5" id="imagePreviewDivId" style="display: none;">
                            <img src="" id="postImgPreviewId" style="display: none; width:100%!important;">
                        </div>

                        <!-- Video Preview -->
                        <div class="container text-center m-2 mb-5" id="videoPreviewDivId" style="display: none;">
                            <video controls id="postVideoPreviewId" style="display: none; width:100%!important;">
                                Your browser does not support the video tag.
                            </video>
                        </div>

                        <button id="postBtnId" type="button" class="btn btn-primary postbtn" style="background-color: #1da1f2">POST</button>
                    </div>
                </div>
                <?php
                $query = "SELECT * FROM posts ORDER BY id DESC";
                $postData = mysqli_query($db, $query) or die(mysqli_error($db));
                if ((mysqli_num_rows($postData) > 0)) {
                    $allPosts = mysqli_fetch_all($postData, MYSQLI_ASSOC);
                    if (isset($allPosts) && $allPosts) {
                        foreach ($allPosts as $post) {

                            $query = "SELECT * FROM users WHERE `id` =" . (int)$post['iduser'];

                            $resultData = mysqli_query($db, $query) or die(mysqli_error($db));

                            $userData = $resultData->fetch_assoc();

                            $documentRoot = $_SERVER['DOCUMENT_ROOT'];
                            $projectDirectory = "/TwitterAssignment";
                            $uploadDirRelative = "/views/img/uploads/";
                            $uploadDir = $projectDirectory . $uploadDirRelative;
                            $uniqueFileName = $userData['filename'];
                            $uploadPath = $uploadDir . $uniqueFileName;
                            $fileExist = $documentRoot . $uploadPath;

                            $postFileName = $post['imagename'];
                            $uploadPostPath = $uploadDir . $postFileName;
                            $filePostExist = $documentRoot . $uploadPostPath;

                            $postVideoFileName = $post['videoname'];
                            $uploadPostVideoPath = $uploadDir . $postVideoFileName;
                            $filePostVideoExist = $documentRoot . $uploadPostPath;
                ?>
                            <div class="allTweets">
                                <div>
                                    <img src="<?php echo (file_exists($fileExist) && isset($uniqueFileName) && ($uniqueFileName)) ? $uploadPath : 'views/img/addImage.jpg'; ?>" alt="profile" class="tweet__author-logo">
                                    <span style="font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; font-size: 18px; font-weight: bold; color: #1da1f2"><?php echo $userData['username']; ?></span>
                                </div>
                                <p style="font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; font-size: 17px" class="tweetTextDiv"><?php echo $post['text']; ?></p>
                                <div class="container">
                                    <?php
                                    if (file_exists($filePostExist) && isset($postFileName) && ($postFileName)) {
                                    ?>
                                        <img src="<?php echo (file_exists($filePostExist) && isset($postFileName) && ($postFileName)) ?
                                                        $uploadPostPath : 'views/img/addImage.jpg'; ?>" style="width:100%!important;">
                                    <?php
                                    }
                                    ?>
                                    <?php
                                    if (file_exists($filePostVideoExist) && isset($postVideoFileName) && ($postVideoFileName)) {
                                    ?>
                                        <video controls src="<?php echo (file_exists($filePostVideoExist) && isset($postVideoFileName) && ($postVideoFileName)) ?
                                                                    $uploadPostVideoPath : 'views/img/addImage.jpg'; ?>" style="width:100%!important;">
                                        </video>
                                    <?php
                                    }
                                    ?>
                                    <button class="btn btn-light likeButton <?php echo in_array($userId, explode(',', $post['likebyusers'])) ? 'liked' : ''; ?>" data-post-id="<?php echo $post['id'] ?>" data-user-id="<?php echo $userId ?>">
                                        <i class="fas fa-heart"></i>
                                        <span class="likeCount"><?php echo $post['like_count'] ?></span> Likes
                                    </button>
                                    <!-- Delete Button -->
                                    <?php if ($post['iduser'] == $userId) { ?>
                                        <button class="btn btn-light deleteButton" data-post-id="<?php echo $post['id']; ?>">
                                            <i class="fas fa-trash"></i>
                                            Delete
                                        </button>
                                    <?php } ?>

                                </div>

                            </div>
                <?php
                        }
                    }
                }
                ?>
            </div>
            <div class="layout__right-sidebar-container">
                <div class="layout__right-sidebar">
                    <div class="trends-for-you" style="width: 177%!important;">
                        <div class="trends-for-you__block">
                            <div class="trends-for-you__heading">
                                Trends for you
                            </div>
                        </div>
                        <div class="trends-for-you__block">
                            <div class="trends-for-you__meta-information">
                                Trending in India
                            </div>
                            <div class="trends-for-you__trend-name">
                                #kisan_andolan
                            </div>
                            <div class="trends-for-you__meta-information">
                                155k Tweets
                            </div>
                            <div class="trends-for-you__trend-name">
                                #jobs
                            </div>
                            <div class="trends-for-you__meta-information">
                                120k Tweets
                            </div>
                            <div class="trends-for-you__trend-name">
                                #cricket
                            </div>
                            <div class="trends-for-you__meta-information">
                                175k Tweets
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </section>
    <script src="https://code.jquery.com/jquery-3.6.4.min.js"></script>
    <script src="views\js\dashboard.js"></script>
    <script>

    </script>

</body>

</html>

...........................................................................................delete_tweet.php..............
<?php

if (isset($_POST['action']) && $_POST['action'] == 'delete') {
    $postId = mysqli_real_escape_string($db, $_POST['postId']);
   

    $postId = mysqli_real_escape_string($db, $_POST['postId']);
    $checkOwnershipQuery = "SELECT * FROM posts WHERE id = '$postId' ";
    $checkOwnershipResult = mysqli_query($db, $checkOwnershipQuery);

    if (mysqli_num_rows($checkOwnershipResult) > 0) {
        $deleteQuery = "DELETE FROM posts WHERE id = '$postId'";
        mysqli_query($db, $deleteQuery);

        echo json_encode(['success' => 'Tweet deleted successfully']);
        exit;
    } else {
        echo json_encode(['error' => 'Unable to delete tweet.']);
        exit;
    }
}
?>
.......................................................................................editProfile.php....................

<?php
if (!isset($_SESSION["user_id"]) ||  !$_SESSION["user_id"]) {
    // remove all session variables
    session_unset();

    
    session_destroy();
    

    header('Location: index.php?page=login&err=8');
} else {
    $userId = (int)$_SESSION["user_id"];

    $query = "SELECT * FROM users WHERE `id` =" . (int)$userId;

    $resultData = mysqli_query($db, $query) or die(mysqli_error($db));

    if ((mysqli_num_rows($resultData) > 0)) {
        $userData = $resultData->fetch_assoc();
        $name = $userData['username'];
        $email = $userData['mail'];
        $bio = $userData['bio'];
        $address = $userData['address'];
        $documentRoot = $_SERVER['DOCUMENT_ROOT'];
        $projectDirectory = "/TwitterAssignment";
        $uploadDirRelative = "/views/img/uploads/";
        $uploadDir = $projectDirectory . $uploadDirRelative;
        $uniqueFileName = $userData['filename'];
        $uploadPath = $uploadDir . $uniqueFileName;
        $fileExist = $documentRoot . $uploadPath;
        
?>
        <!DOCTYPE html>
        <html lang="en">

        <head>
            <meta charset="UTF-8">
            <meta http-equiv="X-UA-Compatible" content="IE=edge">
            <meta name="viewport" content="width=device-width, initial-scale=1.0">
            <title>Edit profile</title>
            <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.2/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-EVSTQN3/azprG1Anm3QDgpJLIm9Nao0Yz1ztcQTwFspd3yD65VohhpuuCOmLASjC" crossorigin="anonymous">
            <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.0.2/dist/js/bootstrap.bundle.min.js" integrity="sha384-MrcW6ZMFYlzcLA8Nl+NtUVF0sA7MsXsP1UyJoMp4YLEuNSfAP+JcXn/tWtIaxVXM" crossorigin="anonymous"></script>
            <link rel="stylesheet" href="views\css\login.css">
        </head>

        <body>
        <?php
            if (isset($_GET['err']) && $_GET['err'] == '1') {
                ?>
                 <div class="alert alert-danger" role="alert">
                 All fields are required.
              </div>
                <?php
            } else if(isset($_GET['err']) && $_GET['err'] == '2') {
                ?>
                <div class="alert alert-danger" role="alert">
                All fields are required.
                </div>
                <?php
            } else if(isset($_GET['err']) && $_GET['err'] == '3') {
                ?>
                <div class="alert alert-danger" role="alert">
                Please enter name.
                </div>
                <?php
            } else if(isset($_GET['err']) && $_GET['err'] == '4') {
                ?>
                <div class="alert alert-danger" role="alert">
                Please enter email.
                </div>
                <?php
            } else if(isset($_GET['err']) && $_GET['err'] == '5') {
                ?>
                <div class="alert alert-danger" role="alert">
                Please enter bio.
                <?php
            } else if(isset($_GET['err']) && $_GET['err'] == '6') {
                ?>
                <div class="alert alert-danger" role="alert">
                Please enter address.
                </div>
                <?php
            } else if(isset($_GET['err']) && $_GET['err'] == '7') {
                ?>
                <div class="alert alert-danger" role="alert">
                Please enter valid name.
                </div> 
                <?php
            } else if(isset($_GET['err']) && $_GET['err'] == '8') {
                ?>
                <div class="alert alert-danger" role="alert">
                Please enter valid email.
                </div> 
                <?php
            }  else if(isset($_GET['err']) && $_GET['err'] == '9') {
                ?>
                <div class="alert alert-danger" role="alert">
                Please enter valid bio.
                </div> 
                <?php
            }  else if(isset($_GET['err']) && $_GET['err'] == '10') {
                ?>
                <div class="alert alert-danger" role="alert">
                Please enter valid address.
                </div> 
                <?php
            }  else if(isset($_GET['err']) && $_GET['err'] == '11') {
                ?>
                <div class="alert alert-danger" role="alert">
                Please enter valid image file with jpg, png and jpeg.
                </div> 
                <?php
            }  else if(isset($_GET['err']) && $_GET['err'] == '12') {
                ?>
                <div class="alert alert-danger" role="alert">
                 File size must be less than 10 MB.
                </div> 
                <?php
            }  else if(isset($_GET['err']) && $_GET['err'] == '13') {
                ?>
                <div class="alert alert-danger" role="alert">
                Your profile details is not update.Please try again.
                </div> 
                <?php
            }  else if(isset($_GET['err']) && $_GET['err'] == '14') {
                ?>
                <div class="alert alert-danger" role="alert">
                Profile image is not uplaoded please try again.
                </div> 
                <?php
            }
            ?>

            <div class="editProfileContainer">
                <h3>Edit profile</h3>
                <div class="row">
                    <form method="POST" action="?page=editProfileValidation" enctype="multipart/form-data">
                        <div class="text-center">
                        <img class="rounded-image" src="<?php echo (file_exists($fileExist) && isset($uniqueFileName) && ($uniqueFileName)) ? $uploadPath : 'views/img/addImage.jpg'; ?>" width="75" height="60" id="profileImage" onclick="triggerInput()">

                            <input type="file" name="profileImage" id="fileInput" style="display: none;">
                        </div>
                        <div class="inputDiv">
                            <label for="nameId">Name</label>
                            <input class="inputBoxClass" type="text" id="nameId" name="name" value="<?php if(isset($name) && $name) {  echo $name; } ?>" placeholder="please enter your name" required />
                        </div>
                        <div class="inputDiv">
                            <label for="emailId">Email</label>
                            <input readonly class="form-control" type="text" id="emailId" name="email" value="<?php if(isset($email) && $email) { echo $email; }?>" placeholder="please enter your email" required  />
                        </div>
                        <div class="inputDiv">
                            <label for="bioId">Bio</label>
                            <input class="form-control" type="text" id="bioId" name="bio" value="<?php if(isset($bio) && $bio) { echo $bio; } ?>"  placeholder="please enter your bio" required />
                        </div>
                        <div class="inputDiv">
                            <label for="addressId">Address</label>
                            <input class="form-control" type="text" id="addressId" name="address" value="<?php if(isset($bio) && $bio) { echo $address; } ?>"  placeholder="please enter your address" required />
                        </div>
                        <div class="inputDiv text-center p-2">
                            <button class="updateBtn" type="submit">Update</button>
                        </div>
                    </form>
                </div>
            </div>
            <script src="https://code.jquery.com/jquery-3.6.4.min.js"></script>
            <script src="views\js\editProfile.js"></script>
        </body>

        </html>
<?php

    }
}

?>

.....................................................................................................editProfileValidation.php.................
<?php
if (!isset($_SESSION["user_id"]) ||  !$_SESSION["user_id"]) {
    // remove all session variables
    session_unset();
    session_destroy();

    header('Location: index.php?page=login&err=8');
} else {
    $rexName = "/^[a-zA-Z]+(?:\s?[a-zA-Z]+)*\.?$/";
    $rexEmail = "/^[a-zA-Z0-9._-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,4}$/";
    $regDesc = "/^[a-zA-Z0-9\s.,'-]+$/";
    $err = 0;
    
    $fileName = $_FILES['profileImage']['name'];
    $fileType = strtolower(pathinfo($fileName, PATHINFO_EXTENSION));
    $fileSize = $_FILES['profileImage']['size'];
    $maxFileSize = 10 * 1024 * 1024;
    $allowedTypes = ['jpg', 'jpeg', 'png'];
    $documentRoot = $_SERVER['DOCUMENT_ROOT'];
    $projectDirectory = "/TwitterAssignment";
    $uploadDirRelative = "/views/img/uploads/";
    $uploadDir = $documentRoot . $projectDirectory . $uploadDirRelative;

    if(!isset($_POST['name']) && !isset($_POST['email']) && !isset($_POST['bio']) && !isset($_POST['address'])) {
        $err = 1;
    } else if($_POST['name'] == '' && $_POST['email'] == '' && $_POST['password'] == '' && $_POST['bio'] == '' && $_POST['address'] == '') {
        $err = 2;
    } else if($_POST['name'] == '') {
        $err = 3;
    } else if($_POST['email'] == '') {
        $err = 4;
    } else if($_POST['bio'] == '') {
        $err = 5;
    } else if($_POST['address'] == '') {
        $err = 6;
    } else if (!preg_match($rexName, $_POST['name'])) {
        $err = 7;
    } else if (!preg_match($rexEmail, $_POST['email'])) {
        $err = 8;
    } else if (!preg_match($regDesc, $_POST['bio'])) {
        $err = 9;
    } else if (!preg_match($regDesc, $_POST['address'])) {
        $err = 10;
    } else if (isset($_FILES['profileImage']['tmp_name']) && $_FILES['profileImage']['tmp_name'] && !in_array($fileType, $allowedTypes)) {
        $err = 11;
    } else if (isset($_FILES['profileImage']['tmp_name']) && $_FILES['profileImage']['tmp_name'] && $fileSize > $maxFileSize) {
        $err = 12;
    } else {
        $randomFileName = uniqid('profile_', true) . '.' . $fileType;
        $uniqueFileName = mysqli_real_escape_string($db, $randomFileName);
        $uploadPath = $uploadDir . $uniqueFileName;

        if (isset($_FILES['profileImage']['tmp_name']) && $_FILES['profileImage']['tmp_name']) {
            if(move_uploaded_file($_FILES['profileImage']['tmp_name'], $uploadPath)) {
                $id = (int)$_SESSION["user_id"];
                $name = mysqli_real_escape_string($db, $_POST['name']);
                $email = mysqli_real_escape_string($db, $_POST['email']);
                $bio = mysqli_real_escape_string($db, $_POST['bio']);
                $address = mysqli_real_escape_string($db, $_POST['address']);
    
                $updateQuery = "UPDATE users SET username = '$name', mail = '$email',
                 bio = '$bio', address = '$address' , filename = '$uniqueFileName' WHERE id = $id AND mail = '$email'";
                
                $resultData =  mysqli_query($db, $updateQuery) or die(mysqli_error($db));
    
              if($resultData) {
                header('Location: index.php?page=profile&succ=1');
              } else {
                $err = 13;
              }
            } else {
                $err = 14;
            }
        } else {

            $id = (int)$_SESSION["user_id"];
            $name = mysqli_real_escape_string($db, $_POST['name']);
            $email = mysqli_real_escape_string($db, $_POST['email']);
            $bio = mysqli_real_escape_string($db, $_POST['bio']);
            $address = mysqli_real_escape_string($db, $_POST['address']);

            $updateQuery = "UPDATE users SET username = '$name', mail = '$email',
             bio = '$bio', address = '$address' WHERE id = $id AND mail = '$email'";
            
            $resultData =  mysqli_query($db, $updateQuery) or die(mysqli_error($db));

          if($resultData) {
            header('Location: index.php?page=profile&succ=1');
          } else {
            $err = 13;
          }
        }
    }

    if(isset($err) && $err) {
        header('Location: index.php?page=editProfile&err=' . $err);
    }
}


...............................................................................................like.php.........................
<?php
if (isset($_POST['action']) && $_POST['action'] == 'like') {
   
    $postId = mysqli_real_escape_string($db, $_POST['postId']);
    $userId = mysqli_real_escape_string($db, $_POST['userId']);

    $query = "SELECT * FROM posts WHERE id = '$postId'";
    $postData = mysqli_query($db, $query) or die(mysqli_error($db));
    $postDetail = mysqli_fetch_assoc($postData);

    $likebyusers = !empty($postDetail['likebyusers']) ? explode(',', $postDetail['likebyusers']) : [];
    
        // Unlike - remove user ID from likebyusers

    if (in_array($userId, $likebyusers)) {
        $likebyusers = array_diff($likebyusers, [$userId]);
    } else {
        
        $likebyusers[] = $userId;
    }

    $likebyusersStr = implode(',', $likebyusers);

    $updateQuery = "UPDATE posts SET likebyusers = '$likebyusersStr', like_count = " . count($likebyusers) . " WHERE id = '$postId'";
    $result = mysqli_query($db, $updateQuery) or die(mysqli_error($db));

    $likeStatus = in_array($userId, $likebyusers);
    $likeCount = count($likebyusers);

    $response = ['likeCount' => $likeCount, 'likeStatus' => $likeStatus];
    header('Content-Type: application/json');
    echo json_encode($response);
    exit;

}
..............................................................................................login.php.....................
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Twitter Login</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.2/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-EVSTQN3/azprG1Anm3QDgpJLIm9Nao0Yz1ztcQTwFspd3yD65VohhpuuCOmLASjC" crossorigin="anonymous">
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.0.2/dist/js/bootstrap.bundle.min.js" integrity="sha384-MrcW6ZMFYlzcLA8Nl+NtUVF0sA7MsXsP1UyJoMp4YLEuNSfAP+JcXn/tWtIaxVXM" crossorigin="anonymous"></script>
    <link rel="stylesheet" href="views\css\login.css">
</head>

<body>
    <div>
        <?php
        if (isset($_GET['err']) && $_GET['err'] == '1') {
        ?>
            <div class="alert alert-danger" role="alert">
                All fields are required.
            </div>
        <?php
        } else if (isset($_GET['err']) && $_GET['err'] == '2') {
        ?>
            <div class="alert alert-danger" role="alert">
                All fields are required.
            </div>
        <?php
        } else if (isset($_GET['err']) && $_GET['err'] == '3') {
        ?>
            <div class="alert alert-danger" role="alert">
                Please enter email.
            </div>
        <?php
        } else if (isset($_GET['err']) && $_GET['err'] == '4') {
        ?>
            <div class="alert alert-danger" role="alert">
                Please enter password.
            </div>
        <?php
        } else if (isset($_GET['err']) && $_GET['err'] == '5') {
        ?>
            <div class="alert alert-danger" role="alert">
                Please enter a valid email.
            <?php
        } else if (isset($_GET['err']) && $_GET['err'] == '6') {
            ?>
                <div class="alert alert-danger" role="alert">
                    Please enter a valid password.
                </div>
            <?php
        } else if (isset($_GET['err']) && $_GET['err'] == '7') {
            ?>
                <div class="alert alert-danger" role="alert">
                    Invalid credentials.
                </div>
            <?php
        } else if (isset($_GET['err']) && $_GET['err'] == '8') {
            ?>
                <div class="alert alert-info" role="alert">
                    Please login first.
                </div>
            <?php
        }
            ?>
            </div>
            <div class="container">
                <div class="box box-one">
                    <img src="views\img\pngwing.com.jpg" />
                    <h3>Login to Twitter</h3>
                </div>
                <div class="box box-two">
                    <form method="POST" action="?page=loginValidation">
                        <h6> Please enter your email</h6>
                        <input type="text" name="email" placeholder="Email" required />
                        <h6>Please enter your password</h6>
                        <input type="password" name="password" placeholder="Password" required />
                        <button type="submit" name="login_btn" class="next-btn">Log in</button>
                    </form>
                </div>
                <p>Don't have an account ? <a href="?page=signup">Sign Up</a></p>
            </div>
</body>

</html>
<?php
.............................................................................loginValidation.php...........................

<?php
$rexEmail = "/^[a-zA-Z0-9._-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,4}$/";
$rexPass = "/^.{8,}$/";
$err = 0;

if(!isset($_POST['email']) && !isset($_POST['password'])) {
    $err = 1;
} else if($_POST['email'] == '' && $_POST['password'] == '') {
    $err = 2;
} else if($_POST['email'] == '') {
    $err = 3;
} else if($_POST['password'] == '') {
    $err = 4;
} else if (!preg_match($rexEmail, $_POST['email'])) {
    $err = 5;
} else if (!preg_match($rexPass, $_POST['password'])) {
    $err = 6;
} else {
    $email = mysqli_real_escape_string($db, $_POST['email']);
    $password = mysqli_real_escape_string($db, sha1($_POST['password']));

    $query = "SELECT * FROM users WHERE mail = '$email' AND password = '$password'";
    $resultData = mysqli_query($db, $query) or die(mysqli_error($db));

    if((mysqli_num_rows($resultData) > 0)) {
        $userData = $resultData->fetch_assoc();
        $_SESSION["user_id"] = (int)$userData['id'];
        header('Location: index.php?page=dashboard');
    } else {
        $err = 7;
    }
}

if(isset($err) && $err) {
    header('Location: index.php?page=login&err=' . $err);
}

?>

.........................................................................................logout.php...........................
<?php
 // remove all session variables
 session_unset();
 session_destroy();
 header('Location: index.php?page=login');
?>

....................................posttweet.php....................................
<?php
if(isset($_POST['action']) && $_POST['action'] == 'postTweet'){

    $documentRoot = $_SERVER['DOCUMENT_ROOT'];
    $projectDirectory = "/TwitterAssignment";
    $uploadDirRelative = "/views/img/uploads/";
    $userId = mysqli_real_escape_string($db, $_POST['userId']);
    $tweetText = mysqli_real_escape_string($db, $_POST['tweetText']);

    // Handle image upload
    if (isset($_FILES['tweetImage']['tmp_name']) && $_FILES['tweetImage']['tmp_name']) {
        $fileName = $_FILES['tweetImage']['name'];
        $fileType = strtolower(pathinfo($fileName, PATHINFO_EXTENSION));
        $randomFileName = uniqid('post_', true) . '.' . $fileType;
        $uniqueFileName = mysqli_real_escape_string($db, $randomFileName);
        $uploadPath = $documentRoot . $projectDirectory . $uploadDirRelative . $uniqueFileName;
        $allowedTypes = ['jpg', 'jpeg', 'png'];
        if (!in_array($fileType, $allowedTypes)) {
            echo "Please enter valid image only";
        }

        else if (move_uploaded_file($_FILES['tweetImage']['tmp_name'], $uploadPath)) {
            mysqli_query($db, "INSERT INTO posts(iduser, text, imagename) VALUES('" . $userId . "', '" . $tweetText. "', '" . $uniqueFileName. "')") or die(mysqli_error($db));
        }

        
        

    } else if (isset($_FILES['tweetVideo']['tmp_name']) && $_FILES['tweetVideo']['tmp_name']) {
        $fileName = $_FILES['tweetVideo']['name'];
        $fileType = strtolower(pathinfo($fileName, PATHINFO_EXTENSION));
        $randomFileName = uniqid('post_', true) . '.' . $fileType;
        $uniqueFileName = mysqli_real_escape_string($db, $randomFileName);
        $uploadPath = $documentRoot . $projectDirectory . $uploadDirRelative . $uniqueFileName;
        $allowedTypes = ['mp4', 'mp3', '3gp'];

        if (!in_array($fileType, $allowedTypes)) {
            echo "Please enter valid video only";
        }

        else if (move_uploaded_file($_FILES['tweetVideo']['tmp_name'], $uploadPath)) {
            mysqli_query($db, "INSERT INTO posts(iduser, text, videoname) VALUES('" . $userId . "', '" . $tweetText. "', '" . $uniqueFileName. "')") or die(mysqli_error($db));
        }
    } else {
        mysqli_query($db, "INSERT INTO posts(iduser, text) VALUES('" . $userId . "', '" . $tweetText. "')") or die(mysqli_error($db));
       
    }

    exit('posted');
} 
?>

...............................................................................................profile.php..................
<?php

if (!isset($_SESSION["user_id"]) ||  !$_SESSION["user_id"]) {
    // remove all session variables
    session_unset();
    session_destroy();
    header('Location: index.php?page=login&err=8');
}

?>
<!DOCTYPE html>
<html>

<head>
    <title>Twitter</title>
    <link rel="stylesheet" href="views\css\global.css" />
    <link rel="stylesheet" href="views\css\happening.css" />
    <link rel="stylesheet" href="views\css\layout.css" />
    <link rel="stylesheet" href="views\css\brand.css" />
    <link rel="stylesheet" href="views\css\sidebar-menu.css" />
    <link rel="stylesheet" href="views\css\trends-for-you.css" />
    <link rel="stylesheet" href="https://unicons.iconscout.com/release/v3.0.6/css/line.css">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css" integrity="sha512-DTOQO9RWCH3ppGqcWaEA1BIZOC6xxalwEsw9c2QQeAIftl+Vegovlnee1c9QX4TctnWMn13TZye+giMm8e2LwA==" crossorigin="anonymous" referrerpolicy="no-referrer" />
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.3/css/all.min.css" />
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" integrity="sha384-T3c6CoIi6uLrA9TneNEoa7RxnatzjcDSCmG1MXxSR1GAsXEV/Dwwykc2MPK8M2HN" crossorigin="anonymous">
</head>

<body>
    <div id="overlay" class="overlay"></div>
    <nav class="navbar fixed-top">
        <div class="navbar-brand">
            <a href="?page=dashboard"><img src="https://static.dezeen.com/uploads/2012/06/dezeen_twitter-bird.gif" alt="Logo"></a>
        </div>
        <div class="foryou">
            Your Profile
        </div>
        <form class="d-flex search">
            <input class="form-control me-2" type="search" placeholder="Search" aria-label="Search">
            <button class="btn btn-primary search1" type="submit">Search</button>
        </form>
    </nav>
    <section class="landing">
        <?php
        if (isset($_GET['succ']) && $_GET['succ'] == '1') {
        ?>
            <div class="alert alert-success" role="alert">
                Your profile details uppdated sucessfully.
            </div>
        <?php
        } else if (isset($_GET['succ']) && $_GET['succ'] == '2') {
        ?>
            <div class="alert alert-success" role="alert">
                Your password uppdate sucessfully.
            </div>
        <?php
        }
        $userId = (int)$_SESSION["user_id"];

        $query = "SELECT * FROM users WHERE `id` =" . (int)$userId;

        $resultData = mysqli_query($db, $query) or die(mysqli_error($db));

        if ((mysqli_num_rows($resultData) > 0)) {
            $userData = $resultData->fetch_assoc();
            $name = $userData['username'];
            $email = $userData['mail'];
            $bio = $userData['bio'];
            $address = $userData['address'];
            $documentRoot = $_SERVER['DOCUMENT_ROOT'];
            $projectDirectory = "/TwitterAssignment";
            $uploadDirRelative = "/views/img/uploads/";
            $uploadDir = $projectDirectory . $uploadDirRelative;
            $uniqueFileName = $userData['filename'];
            $uploadPath = $uploadDir . $uniqueFileName;
            $fileExist = $documentRoot . $uploadPath;
        }
        ?>
        <div class="layout">
            <div class="layout__left-sidebar">
                <div class="sidebar-menu">
                    <a href="?page=dashboard">
                        <div class="sidebar-menu__item">
                            <img src="views\img\home.svg" class="sidebar-menu__item-icon" />
                            HOME
                        </div>
                    </a>

                    <a href="?page=profile">
                        <div class="sidebar-menu__item sidebar-menu__item--active">
                            <img src="views\img\profile.svg" class="sidebar-menu__item-icon" />
                            Profile
                        </div>
                    </a>

                    <a href="?page=logout">
                        <div class="sidebar-menu__item">
                            <img class="sidebar-menu__item-icon" />
                            <a href="?page=logout">Log out</a>
                        </div>
                    </a>

                </div>
            </div>
            <div class="layout__main">
                <div class="profile-card" style="margin-bottom: 10px;">
                    <div>
                        <img src="<?php echo (file_exists($fileExist) && isset($uniqueFileName) && ($uniqueFileName)) ? $uploadPath : 'views/img/addImage.jpg'; ?>" alt="profile" class="profile-img">
                        <span style="float: right!important;margin-top:5%;">
                            <a class="btn btn-dark" href="?page=editProfile">Edit Profile</a>

                            <a class="btn btn-dark" href="?page=updatePassword">Update Password</a>
                        </span>
                    </div>
                    <h5 style="font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; font-size: 18px; font-weight: bold; color: #1da1f2">
                        <?php if (isset($name) && $name) {
                            echo $name;
                        } ?>
                    </h5>
                    <h6 style="font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; font-size: 14px"style="color:black;">
                        <?php if (isset($bio) && $bio) {
                            echo $bio;
                        } ?>
                    </h6>
                    <address >
                        <a href="#">
                            <h6 style="font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; font-size: 14px">
                            <i class="fa-solid fa-location-dot"></i>
                            <?php if (isset($address) && $address) {
                                echo $address;
                            } ?>
                            </h6>
                        </a>

                    </address>
                    <div class="data">
                        <div class="data-card">
                            <p><b>345</b> Followers</p>
                        </div>
                        <div class="data-card">
                            <p><b>632</b> Following</p>
                        </div>
                    </div>
                </div>

                <!--   -->
                <?php
                $query = "SELECT * FROM posts WHERE `iduser` = " . (int)$userId . " ORDER BY id DESC";
                $postData = mysqli_query($db, $query) or die(mysqli_error($db));
                if ((mysqli_num_rows($postData) > 0)) {
                    $allPosts = mysqli_fetch_all($postData, MYSQLI_ASSOC);
                    if (isset($allPosts) && $allPosts) {
                        foreach ($allPosts as $post) {

                            $query = "SELECT * FROM users WHERE `id` =" . (int)$post['iduser'];

                            $resultData = mysqli_query($db, $query) or die(mysqli_error($db));

                            $userData = $resultData->fetch_assoc();

                            $documentRoot = $_SERVER['DOCUMENT_ROOT'];
                            $projectDirectory = "/TwitterAssignment";
                            $uploadDirRelative = "/views/img/uploads/";
                            $uploadDir = $projectDirectory . $uploadDirRelative;
                            $uniqueFileName = $userData['filename'];
                            $uploadPath = $uploadDir . $uniqueFileName;
                            $fileExist = $documentRoot . $uploadPath;

                            $postFileName = $post['imagename'];
                            $uploadPostPath = $uploadDir . $postFileName;
                            $filePostExist = $documentRoot . $uploadPostPath;

                            $postVideoFileName = $post['videoname'];
                            $uploadPostVideoPath = $uploadDir . $postVideoFileName;
                            $filePostVideoExist = $documentRoot . $uploadPostPath;
                ?>
                            <div class="allTweets">
                                <div>
                                    <img src="<?php echo (file_exists($fileExist) && isset($uniqueFileName) && ($uniqueFileName)) ? $uploadPath : 'views/img/addImage.jpg'; ?>" alt="profile" class="tweet__author-logo">
                                    <span style="font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; font-size: 18px; font-weight: bold; color: #1da1f2">
                                        <?php echo $userData['username']; ?>
                                    </span>
                                </div>
                                <p style="font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; font-size: 17px" class="tweetTextDiv">
                                    <?php echo $post['text']; ?>
                                </p>
                                <div class="container">
                                    <?php
                                    if (file_exists($filePostExist) && isset($postFileName) && ($postFileName)) {
                                    ?>
                                        <img src="<?php echo (file_exists($filePostExist) && isset($postFileName) && ($postFileName)) ?
                                                        $uploadPostPath : 'views/img/addImage.jpg'; ?>" style="width:100%!important;">
                                    <?php
                                    }
                                    ?>
                                    <?php
                                    if (file_exists($filePostVideoExist) && isset($postVideoFileName) && ($postVideoFileName)) {
                                    ?>
                                        <video controls src="<?php echo (file_exists($filePostVideoExist) && isset($postVideoFileName) && ($postVideoFileName)) ?
                                                                    $uploadPostVideoPath : 'views/img/addImage.jpg'; ?>" style="width:100%!important;">
                                        </video>
                                    <?php
                                    }
                                    ?>
                                    <button class="btn btn-light likeButton <?php echo in_array($userId, explode(',', $post['likebyusers'])) ? 'liked' : ''; ?>" data-post-id="<?php echo $post['id'] ?>" data-user-id="<?php echo $userId ?>">
                                        <i class="fas fa-heart"></i>
                                        <span class="likeCount">
                                            <?php echo $post['like_count'] ?>
                                        </span> Likes
                                    </button>
                                    <!-- Delete Button -->
                                    <?php if ($post['iduser'] == $userId) { ?>
                                        <button class="btn btn-light deleteButton" data-post-id="<?php echo $post['id']; ?>">
                                            <i class="fas fa-trash"></i>
                                            Delete
                                        </button>
                                    <?php } ?>

                                </div>

                            </div>
                <?php
                        }
                    }
                }
                ?>
            </div>
            <!--        -->
            <div class="layout__right-sidebar-container">
                <div class="layout__right-sidebar">
                    <div class="trends-for-you" style="width: 177%!important;">
                        <div class="trends-for-you__block">
                            <div class="trends-for-you__heading">
                                Trends for you
                            </div>
                        </div>
                        <div class="trends-for-you__block">
                            <div class="trends-for-you__meta-information">
                                Trending in India
                            </div>
                            <div class="trends-for-you__trend-name">
                                #kisan_andolan
                            </div>
                            <div class="trends-for-you__meta-information">
                                155k Tweets
                            </div>
                            <div class="trends-for-you__trend-name">
                                #jobs
                            </div>
                            <div class="trends-for-you__meta-information">
                                120k Tweets
                            </div>
                            <div class="trends-for-you__trend-name">
                                #cricket
                            </div>
                            <div class="trends-for-you__meta-information">
                                175k Tweets
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </section>
    <script src="https://code.jquery.com/jquery-3.6.4.min.js"></script>
    <script src="views\js\dashboard.js"></script>
</body>

</html>


.....................................................................................signup.php.......................
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>SignUp</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.2/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-EVSTQN3/azprG1Anm3QDgpJLIm9Nao0Yz1ztcQTwFspd3yD65VohhpuuCOmLASjC" crossorigin="anonymous">
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.0.2/dist/js/bootstrap.bundle.min.js" integrity="sha384-MrcW6ZMFYlzcLA8Nl+NtUVF0sA7MsXsP1UyJoMp4YLEuNSfAP+JcXn/tWtIaxVXM" crossorigin="anonymous"></script>
    <link rel="stylesheet" href="views\css\login.css">
</head>

<body>
    <div>
        <?php
            if (isset($_GET['err']) && $_GET['err'] == '1') {
                ?>
                 <div class="alert alert-danger" role="alert">
                 All fields are required.
              </div>
                <?php
            } else if(isset($_GET['err']) && $_GET['err'] == '2') {
                ?>
                <div class="alert alert-danger" role="alert">
                All fields are required.
                </div>
                <?php
            } else if(isset($_GET['err']) && $_GET['err'] == '3') {
                ?>
                <div class="alert alert-danger" role="alert">
                Please enter name.
                </div>
                <?php
            } else if(isset($_GET['err']) && $_GET['err'] == '4') {
                ?>
                <div class="alert alert-danger" role="alert">
                Please enter email.
                </div>
                <?php
            } else if(isset($_GET['err']) && $_GET['err'] == '5') {
                ?>
                <div class="alert alert-danger" role="alert">
                Please enter password.
                <?php
            } else if(isset($_GET['err']) && $_GET['err'] == '6') {
                ?>
                <div class="alert alert-danger" role="alert">
                Please enter a valid name.
                </div>
                <?php
            } else if(isset($_GET['err']) && $_GET['err'] == '7') {
                ?>
                <div class="alert alert-danger" role="alert">
                Please enter a valid email.
                </div>
                <?php
            } else if(isset($_GET['err']) && $_GET['err'] == '8') {
                ?>
                <div class="alert alert-danger" role="alert">
                Please enter a valid password with at least 8 characters.
                </div>
                <?php
            } else if(isset($_GET['err']) && $_GET['err'] == '9') {
                ?>
                <div class="alert alert-danger" role="alert">
                Your email is already exist.
                </div>
                <?php
            }
            ?>
    </div>
        <div class="container">
                <div class="box box-one">
                    <img src="views\img\pngwing.com.jpg" alt="image">
                    <h3>Create your account</h3>
                </div>
                <div class="box box-two">
                    <form method="POST" action="?page=signupValidation">
                        <input type="text" name="name" placeholder="Your Name"  />
                        <input type="text" name="email" placeholder="Your Email" required />
                        <input type="password" name="password" placeholder="Password" required />
                        <button type="submit" name="sign_btn" class="next-btn">REGISTER</button>
                    </form>
                </div>
                <p>Have already an account ? <a href="?page=login">login</a></p>
        </div>
</body>
</html>


.....................................................................................signupValidation.php....................

<?php
$rexName = "/^[a-zA-Z]+(?:\s?[a-zA-Z]+)*\.?$/";
$rexEmail = "/^[a-zA-Z0-9._-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,4}$/";
$rexPass = "/^.{8,}$/";
$err = 0;

if(!isset($_POST['name']) && !isset($_POST['email']) && !isset($_POST['password'])) {
    $err = 1;
} else if($_POST['name'] == '' && $_POST['email'] == '' && $_POST['password'] == '') {
    $err = 2;
} else if($_POST['name'] == '') {
    $err = 3;
} else if($_POST['email'] == '') {
    $err = 4;
} else if($_POST['password'] == '') {
    $err = 5;
} else if (!preg_match($rexName, $_POST['name'])) {
    $err = 6;
} else if (!preg_match($rexEmail, $_POST['email'])) {
    $err = 7;
} else if (!preg_match($rexPass, $_POST['password'])) {
    $err = 8;
} else {

    $name = mysqli_real_escape_string($db, $_POST['name']);
    $email = mysqli_real_escape_string($db, $_POST['email']);
    $password = mysqli_real_escape_string($db, sha1($_POST['password']));

    $resultData = mysqli_query($db, "SELECT * FROM users WHERE mail = '" . $email . "'") or die(mysqli_error($db));
    if((mysqli_num_rows($resultData) > 0)) {
        $err = 9;
    } else {
        mysqli_query($db, "INSERT INTO users(username, mail, password) VALUES('" . $name . "', '" . $email . "', '" . $password . "')") or die(mysqli_error($db));
        $insertId = mysqli_insert_id($db);
        $_SESSION["user_id"] = (int)$insertId;
        header('Location: index.php?page=dashboard');
    }

}

if(isset($err) && $err) {
    header('Location: index.php?page=signup&err=' . $err);
}

?>


..........................................................................................updatePassword..................................................
<?php
if (!isset($_SESSION["user_id"]) ||  !$_SESSION["user_id"]) {
    // remove all session variables
    session_unset();
    session_destroy();
    

    header('Location: index.php?page=login&err=8');
} else {
 
        
?>
        <!DOCTYPE html>
        <html lang="en">

        <head>
            <meta charset="UTF-8">
            <meta http-equiv="X-UA-Compatible" content="IE=edge">
            <meta name="viewport" content="width=device-width, initial-scale=1.0">
            <title>Twitter Login</title>
            <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.2/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-EVSTQN3/azprG1Anm3QDgpJLIm9Nao0Yz1ztcQTwFspd3yD65VohhpuuCOmLASjC" crossorigin="anonymous">
            <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.0.2/dist/js/bootstrap.bundle.min.js" integrity="sha384-MrcW6ZMFYlzcLA8Nl+NtUVF0sA7MsXsP1UyJoMp4YLEuNSfAP+JcXn/tWtIaxVXM" crossorigin="anonymous"></script>
            <link rel="stylesheet" href="views\css\login.css">
        </head>

        <body>
        <?php
            if (isset($_GET['err']) && $_GET['err'] == '1') {
                ?>
                 <div class="alert alert-danger" role="alert">
                 All fields are required.
              </div>
                <?php
            } else if(isset($_GET['err']) && $_GET['err'] == '2') {
                ?>
                <div class="alert alert-danger" role="alert">
                All fields are required.
                </div>
                <?php
            } else if(isset($_GET['err']) && $_GET['err'] == '3') {
                ?>
                <div class="alert alert-danger" role="alert">
                Please enter password.
                </div>
                <?php
            } else if(isset($_GET['err']) && $_GET['err'] == '4') {
                ?>
                <div class="alert alert-danger" role="alert">
                Please enter confirm password.
                </div>
                <?php
            } else if(isset($_GET['err']) && $_GET['err'] == '5') {
                ?>
                <div class="alert alert-danger" role="alert">
                Please enter confirm password same as password.
                <?php
            } else if(isset($_GET['err']) && $_GET['err'] == '6') {
                ?>
                <div class="alert alert-danger" role="alert">
                Please enter valid password.
                </div>
                <?php
            } else if(isset($_GET['err']) && $_GET['err'] == '7') {
                ?>
                <div class="alert alert-danger" role="alert">
                Please enter valid confirm password.
                </div> 
                <?php
            } else if(isset($_GET['err']) && $_GET['err'] == '8') {
                ?>
                <div class="alert alert-danger" role="alert">
                Password is not updated.Please try again.
                </div> 
                <?php
            } 
            ?>

            <div class="editProfileContainer">
                <h3>Update password</h3>
                <div class="row">
                    <form method="POST" action="?page=updatePasswordValidation" >
                        <div class="inputDiv">
                            <label for="passId">Password</label>
                            <input class="inputBoxClass" type="password" id="passId" name="pass" placeholder="please enter new password" required />
                        </div>
                        <div class="inputDiv">
                            <label for="confirmPasId">Confirm password</label>
                            <input class="form-control" type="password" id="confirmPasId" name="confirmPass" placeholder="please enter confirm password" required  />
                        </div>
                        <div class="inputDiv text-center p-2">
                            <button class="updateBtn" type="submit">Update</button>
                        </div>
                    </form>
                </div>
            </div>
        </body>

        </html>
<?php
}

?>

......................................................................................updatePasswordValidation.php..........................................................................................
<?php
if (!isset($_SESSION["user_id"]) ||  !$_SESSION["user_id"]) {
    // remove all session variables
    session_unset();
    session_destroy();
    

    header('Location: index.php?page=login&err=8');
} else {
    $rexPass = "/^.{8,}$/";
    $err = 0;

    if(!isset($_POST['pass']) && !isset($_POST['confirmPass'])) {
        $err = 1;
    } else if($_POST['pass'] == '' && $_POST['confirmPass'] == '') {
        $err = 2;
    } else if($_POST['pass'] == '') {
        $err = 3;
    } else if($_POST['confirmPass'] == '') {
        $err = 4;
    }  else if ($_POST['confirmPass'] != $_POST['pass']) {
        $err = 5;
    }  else if (!preg_match($rexPass, $_POST['pass'])) {
        $err = 6;
    } else if (!preg_match($rexPass, $_POST['confirmPass'])) {
        $err = 7;
    } else {
        $id = (int)$_SESSION["user_id"];

        $password = mysqli_real_escape_string($db, sha1($_POST['pass']));

        $updateQuery = "UPDATE users SET password = '$password' WHERE id = $id ";
       
        $resultData =  mysqli_query($db, $updateQuery) or die(mysqli_error($db));

        if($resultData) {
          header('Location: index.php?page=profile&succ=2');
        } else {
          $err = 8;
        }

   }

    if(isset($err) && $err) {
        header('Location: index.php?page=updatePassword&err=' . $err);
    }
}

.........................................................................brand.css..............
.brand {
  height: 28px;
  filter: invert(46%) sepia(99%) saturate(1449%) hue-rotate(176deg) brightness(100%) contrast(91%);
  padding: 10px;
 
}
..................................................................global.css................

html {
    font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Ubuntu, "Helvetica Neue", sans-serif;
}

* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}
/* Navbar styles */

.navbar {
    position: fixed;
    /* border: 1px solid black; */
    width: 100%;
    display: flex;
    justify-content: space-between;
    align-items: center;
    background-color:white ;
    padding: 10px 20px;
    /* margin-left: 1.5%; */
}

.navbar-brand{
    height: 50px;
    margin-left: 13%;
}
.navbar-brand img {
    height: 50px; 
}

.navbar-links a {
    color: #333;
    text-decoration: none;
    margin-left: 20px; 
}

.navbar-links a:hover {
    text-decoration: underline;
}
.search
{
    margin-right: 11%;
}
.navbar .search1
{
    
    background-color: #1b94e0;
}
.navbar .search1:hover{
    background-color: white;
   color:#1b94e0;
   border:1px solid #1b94e0;
}

.foryou
{
    font-size: 30px;
    font-weight:bold;
    margin-left: 100px;
}

 nav .search_bar {
    width: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    background-color: #f7f8fb;
    padding: 8px 15px;
    border-radius: 5px;
  }
  
nav .search_bar input {
    width: 90%;
    padding: 3px 15px;
    border: none;
    outline: none;
    font-size: 12px;
    background: none;
  }
  
   nav .search_bar .bi {
    font-size: 14px;
  }
  
....................................................happening.css.....................

.wrapper{
  width: 100%;
  border:1px solid lightblue;
  display:grid;
  border-bottom: 1px solid #e6ecf0;
  margin-bottom: 10px;
  padding: 10px 15px;
  
  }

  .input-box{
    height: 70px;
    width: 95%;
    margin-left:2.5%;
    border-bottom: 1px solid #e6e6e6;
    border-radius: 15px;
    display: flex;
  }
  .wrapper2{
    width: 100%;
  }
  .up_icon
  {
    width: 10%;
    margin-left: 50px;
    margin-top: 10px;
    display: flex;
    justify-content:space-between;
  }
  .img1,.vid2{
    color:#0d8bd9 ;
    
  }
  .postbtn
  {
    margin-left: 80%;
    margin-top: -40px;
  }

  .input-box .avatar
  {
    height: 50px;
    width: 10%;
    margin: 10px;
  }

  .tweet__author-logo {
    width: 49px;
    height: 49px;
    border-radius: 50%;
    margin-right: 10px;
  }
 
  .input-box .tweet-area{
    position: relative;
    min-height: 130px;
    max-height: 170px;
    overflow-y: auto;
  }
  .tweet-area::-webkit-scrollbar{
    width: 0px;
    border:1px solid black;
  }
  .tweet-area .placeholder{
    position: absolute;
    margin-top: -3px;
    font-size: 22px;
    color: #98A5B1;
    pointer-events: none;
    border:1px solid black;
  }
  .tweet-area .input{
    outline: none;
    font-size: 17px;
    min-height: inherit;
    word-wrap: break-word;
    word-break: break-all;
  }
  .tweet-area .editable{
    position: relative;
    z-index: 5;
  }
  .tweet-area .readonly{
    position: absolute;
    top: 0;
    left: 0;
    z-index: -1;
    color: transparent;
    background: transparent;
  }
  .readonly .highlight{
    background: #fd9bb0;
  }
  .input-box .privacy{
    color: #1da1f2;
    margin: 15px 0;
    display: inline-flex;
    align-items: center;
    padding: 7px 10px ;
    border-radius: 50px;
    cursor: pointer;
    transition: background 0.2s ease;
  }
  .privacy:hover, .icons li:hover{
    background: #e7f5fe;
  }
  .privacy i{
    font-size: 18px;
  }
  .privacy span{
    font-size: 15px;
    font-weight: 600;
    margin-left: 7px;
  }
  .bottom{
    display: flex;
    margin-top: 13px;
    align-items: center;
    justify-content: space-between;
  }
  .bottom .icons{
    display: inline-flex;
  }
  .icons li{
    list-style: none;
    color: #1da1f2;
    font-size: 20px;
    margin: 0 2px;
    height: 38px;
    width: 38px;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    border-radius: 50%;
    transition: background 0.2s ease;
  }
  .bottom .content{
    display: flex;
    align-items: center;
    justify-content: center;
  }
  
  .bottom button{
    padding: 9px 18px;
    border: none;
    outline: none;
    border-radius: 50px;
    font-size: 16px;
    font-weight: 700;
    background: #1da1f2;
    color: #fff;
    cursor: pointer;
    opacity: 0.5;
    pointer-events: none;
    transition: background 0.2s ease;
  }
  .bottom button.active{
    opacity: 1;
    pointer-events: auto;
  }
  .bottom button:hover{
    background: #0d8bd9;
  }
  .post-box {
    width: 100%;
    background-color: #fff;
    border-radius: 8px;
    transition: border-color 0.3s;
}

.post-box textarea {
    width: 100%;
    box-sizing: border-box;
    border: none;
    outline: none;
    padding: 10px;
    border-radius: 8px;
    transition: border-color 0.3s;
}

.post-box textarea:hover,
.post-box textarea:focus {
    border-color: #3498db;
}
.allTweets{
  width: 100%;
  border:1px solid lightblue;
  display:grid;
  border-bottom: 1px solid #e6ecf0;
  margin-bottom: 10px;
  padding: 10px 15px;
}
.tweetTextDiv{
  padding: 10px;
}

.liked {
  color: red !important;
}

.usernameSpan {
  font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Ubuntu, "Helvetica Neue", sans-serif;
}

..............................................layout.css............................

section
{
  width: 97%;
  /* border: 1px solid black; */
  margin-left: 1.5%;
  margin-top:100px;
  
}

.layout {
 
  display: grid;
  grid-template-columns: 275px auto 350px;
  margin: 0 auto;
  max-width: 1200px;
  /* border: 1px solid lightblue; */
}

.layout__right-sidebar {
  /* border: 1px solid black; */
  position: fixed;
  margin-left: 30px;
  
}
/* .layout__main
{
 border: 1px solid lightblue; 
} */

.profile-card {
  position: relative;
  width: 100%;
  height: auto;
  background: #fff;
  box-shadow: 0px 0px 30px rgb(0, 0, 0, .2);
  padding: 15px 10px 0px;
  border-radius: 8px 8px 0px 0px;
  display: flex;
  /* align-items: start; */
  justify-content: center;
  flex-direction: column;
}

.profile-card .profile-img {
  width: 70px;
  height: 70px;
  border-radius: 50%;
  margin-top: 5%;
  margin-left: 10px;
}

 .profile-card h5 {
  font-weight: 600;
  margin-top: 10px;
}

 .profile-card h6 {
  color: rgb(0, 0, 0, .3);
  font-size: 10px;
  padding: 2px 0px;
}

 .profile-card address {
  display: flex;
  align-items: center;
  margin-top: 8px;
}

 .profile-card address a {
  text-decoration: none;
  color: #000;
  font-size: 11px;
  font-style: normal;
}

 .profile-card address a:nth-child(2) {
  margin-left: 10px;

}

 .profile-card p {
  font-size: 13px;
  font-weight: 600;
  text-align: left;
  margin-top: 10px;
  padding: 0px 5px;
  color: rgb(0, 0, 0, .8);
  margin-left: 0px;
}

 .profile-card .data {
  width: 100%;
  display: flex;
  align-items: start;
  margin-top: 8px;
  margin-left: 0px;
}

 .profile-card .data .data-card p {
  font-size: 15px;
  color: rgb(0, 0, 0, .4);
}

 .profile-card .data .data-card p b {
  font-size: 15px;
  color: black;
}

.profile_edit
{
  height: 100px;
  width: 100%;
  /* border: 1px solid black; */
  display: flex;
}

.overlay {
  display: none;
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, .8);
  z-index: 998;
}

.popup {
  height: 400px;
  width:500px;
  display: none;
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  padding: 20px;
  background-color: #fff;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.5);
  z-index: 999;
}
.editclosesave
{
  height: 50px;
  width: 100%;
  /* border:1px solid black; */
  display: flex;
}
.editcontent
{
  height: 300px;
  width: 100%;
  /* border:1px solid black; */
}
.popup .content {
  padding: 20px;
}

.popup .profile-image-section {
  text-align: center;
  
}

.popup .profile-image-label {
  border: 1px solid black;
  margin-right:80%;
  display: inline-block;
  position: relative;
  cursor: pointer;
  border-radius: 50px;
}
.popup .profile-image-label input {
  display: none;
}

.popup .image-preview {
  display: inline-block;
  padding: 10px;
  /* background-color: #4285f4; */
  color: black;
  border-radius: 5px;
  cursor: pointer;
  
}

.profile-pic {
  display: flex;
  justify-content: center;
  align-items: center;
  position: relative;
  width: 80px;
  height: 80px;
  border-radius: 100px;
  overflow: hidden;
  transition: all 0.3s ease;
  border: 1px solid black;

  img {
    width: 100%;
    height: 100%;
    object-fit: cover;
    box-shadow: 0 0 10px 0 rgba(255, 255, 255, 0.35);
  }

  label {
    cursor: pointer;
    position: absolute;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    width: 70%;
    height: 70%;
    background-color: rgba(0, 0, 0, 0.8);
    color: #fafafa;
    transition: background-color 0.2s ease-in-out;
    border-radius: 100px;
    opacity: 0;
  }

  &:hover label {
    opacity: 1;
  }

  span {
    padding: 0.2em;
    height: 2em;
  }

  input {
    display: none;
  }
}
.name {
  height: 80px;
  width: 100%;
  border: 1px solid  black;
  padding: 10px;
  
}

.fname
{
  height: 100px;
  width: 80%;
}
.bio  
{
  height: 100px;
  width: 100%;
  border: 1px solid black;
  margin-top: 10px;
  padding: 10px;
}
.closebtn
{
  border: none;
  margin-top: 10px;
  cursor: pointer;
  margin-top: 15px;
  margin-left: 15px;
  
}
.savebtn
{
  height:40px;
  width: 100px;
  border-radius:20px;
margin-left: 40%;
background-color:black;
color: white;
}
.edittext
{
  height: 50px;
width: 250px;
/* border: 1px solid black; */
margin-left: 15px;
}

................................................login.css................
body {
  background-color: rgb(219, 217, 217);
}

.error {
  color: #FF0000;
}

.container {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background: #fff;
  width: 450px;
  height: 550px;
  border-radius: 20px;
  box-shadow: 0 10px rgb(110, 110, 110, .2);
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  font-family: 'Poppins', sans-serif;
}

.box,
.box button {
  display: flex;
  align-items: center;
}

.box {
  width: 300px;
  height: 240px;
  flex-direction: column;
  justify-content: space-evenly;
}

.box button {
  width: 300px;
  height: 40px;
  background-color: #fff;
  outline: none;
  font-weight: bold;
  border: 1px solid rgb(211, 210, 210);
  border-radius: 20px;
  justify-content: center;
  font-family: 'Poppins', sans-serif;
}

.box-one img {

  height: 40%;
  width: 40%;
}

.box-one button img {
  margin: 0 .5rem;
  height: 24px;
  width: 24px;
}

.box-two input {
  position: relative;
  width: 300px;
  height: 55px;
  outline: none;
  border: 1px solid rgb(211, 210, 210);
  border-radius: 4px;
  padding-left: 10px;
  margin: 4px;

}

.box-two input label {
  position: absolute;
  top: 0;
}

.box-two input::placeholder {
  font-size: 1rem;
}

.box-two input:focus {
  border: 1px solid #00acee;
}

.box-two .next-btn {
  background-color: #121212;
  color: #fff;
  margin: auto;
}

.forgotBtn {
  width: 300px;
  height: 40px;
  background-color: #fff;
  outline: none;
  font-weight: bold;
  border: 1px solid rgb(211, 210, 210);
  border-radius: 20px;
  justify-content: center;
  font-family: 'Poppins', sans-serif;
  background-color: #121212;
  color: #fff !important;
  margin: auto;
  display: flex;
  align-items: center;
}

.box-two .invalid-Passward {
  color: red;
  margin-left: 50px;
}

.container p {
  font-size: .7rem;
  color: rgb(138, 136, 136);
}

.container p a {
  text-decoration: none;
  color: #00acee;
}


section {
  width: 97%;
  /* border: 1px solid black; */
  margin-left: 1.5%;
  margin-top: 100px;

}
/*
.layout {

  display: grid;
  grid-template-columns: 275px auto 350px;
  margin: 0 auto;
  max-width: 1200px;
  border: 1px solid lightblue; 
}

.layout__right-sidebar {
  border: 1px solid black; 
  position: fixed;
  margin-left: 30px;

}

 .layout__main
{
 border: 1px solid lightblue; 
} 

.profile-card {
  position: relative;
  width: 100%;
  height: auto;
  background: #fff;
  box-shadow: 0px 0px 30px rgb(0, 0, 0, .2);
  padding: 15px 10px 0px;
  border-radius: 8px 8px 0px 0px;
  display: flex;
  /* align-items: start; 
  justify-content: center;
  flex-direction: column;
}

.profile-card .profile-img {
  width: 70px;
  height: 70px;
  border-radius: 50%;
  margin-top: 5%;
  margin-left: 10px;
}

.profile-card h5 {
  font-weight: 600;
  margin-top: 10px;
}

.profile-card h6 {
  color: rgb(0, 0, 0, .3);
  font-size: 10px;
  padding: 2px 0px;
}

.profile-card address {
  display: flex;
  align-items: center;
  margin-top: 8px;
}

.profile-card address a {
  text-decoration: none;
  color: #000;
  font-size: 11px;
  font-style: normal;
}

.profile-card address a:nth-child(2) {
  margin-left: 10px;

}

.profile-card p {
  font-size: 13px;
  font-weight: 600;
  text-align: left;
  margin-top: 10px;
  padding: 0px 5px;
  color: rgb(0, 0, 0, .8);
  margin-left: 0px;
}

.profile-card .data {
  width: 100%;
  display: flex;
  align-items: start;
  margin-top: 8px;
  margin-left: 0px;
}

.profile-card .data .data-card p {
  font-size: 15px;
  color: rgb(0, 0, 0, .4);
}

.profile-card .data .data-card p b {
  font-size: 15px;
  color: black;
}

.profile_edit {
  height: 100px;
  width: 100%;
  /* border: 1px solid black; 
  display: flex;
}

.overlay {
  display: none;
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, .8);
  z-index: 998;
}

.popup {
  height: 400px;
  width: 500px;
  display: none;
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  padding: 20px;
  background-color: #fff;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.5);
  z-index: 999;
}

.editclosesave {
  height: 50px;
  width: 100%;
  /* border:1px solid black; 
  display: flex;
}

.editcontent {
  height: 300px;
  width: 100%;
  /* border:1px solid black; 
}

.popup .content {
  padding: 20px;
}

.popup .profile-image-section {
  text-align: center;

}

.popup .profile-image-label {
  border: 1px solid black;
  margin-right: 80%;
  display: inline-block;
  position: relative;
  cursor: pointer;
  border-radius: 50px;
}

.popup .profile-image-label input {
  display: none;
}

.popup .image-preview {
  display: inline-block;
  padding: 10px;
  /* background-color: #4285f4; 
  color: black;
  border-radius: 5px;
  cursor: pointer;

}

.profile-pic {
  display: flex;
  justify-content: center;
  align-items: center;
  position: relative;
  width: 80px;
  height: 80px;
  border-radius: 100px;
  overflow: hidden;
  transition: all 0.3s ease;
  border: 1px solid black;

  img {
    width: 100%;
    height: 100%;
    object-fit: cover;
    box-shadow: 0 0 10px 0 rgba(255, 255, 255, 0.35);
  }

  label {
    cursor: pointer;
    position: absolute;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    width: 70%;
    height: 70%;
    background-color: rgba(0, 0, 0, 0.8);
    color: #fafafa;
    transition: background-color 0.2s ease-in-out;
    border-radius: 100px;
    opacity: 0;
  }

  &:hover label {
    opacity: 1;
  }

  span {
    padding: 0.2em;
    height: 2em;
  }

  input {
    display: none;
  }
}

.name {
  height: 80px;
  width: 100%;
  border: 1px solid black;
  padding: 10px;

}

.fname {
  height: 100px;
  width: 80%;
}

.bio {
  height: 100px;
  width: 100%;
  border: 1px solid black;
  margin-top: 10px;
  padding: 10px;
}

.closebtn {
  border: none;
  margin-top: 10px;
  cursor: pointer;
  margin-top: 15px;
  margin-left: 15px;

}

.savebtn {
  height: 40px;
  width: 100px;
  border-radius: 20px;
  margin-left: 40%;
  background-color: black;
  color: white;
}

.edittext {
  height: 50px;
  width: 250px;
  /* border: 1px solid black; 
  margin-left: 15px;
}

.tweet {
  display: flex;
  border-bottom: 1px solid #e6ecf0;
  margin-bottom: 2px;
  padding: 10px 15px;
  border: 1px solid lightblue;
}

.tweet:hover {
  background: rgba(0, 0, 0, 0.03);
  cursor: pointer;
}

.tweet a {
  color: #1b94e0;
  text-decoration: none;
}

.tweet a:hover {
  text-decoration: underline;
}

.tweet__author-logo {
  width: 49px;
  height: 49px;
  border-radius: 50%;
  margin-right: 10px;
}

.tweet__header {
  display: flex;
}

.tweet__author-name {
  font-size: 15px;
  font-weight: 700;
  color: #14171a;
  margin-right: 5px;
}

.tweet__author-slug {
  color: #5b7083;
  font-size: 15px;
}

.tweet__publish-time {
  margin-left: 5px;
  color: #657786;
}

.tweet__image {
  width: 100%;
  border-radius: 15px;
  margin-top: 10px;
  margin-right: 20px;
}

.tweet__actions {
  display: flex;
  justify-content: space-between;
  margin-top: 10px;
}

.tweet__action-btn {
  padding: 8px 16px;
  border: none;
  border-radius: 20px;
  cursor: pointer;
  font-weight: bold;
  display: flex;
  align-items: center;
}

.tweet__like-btn {
  color: #e0245e;
}

.tweet__comment-btn {
  color: #1da1f2;
}

.tweet__retweet-btn {
  color: #17bf63;
}

.tweet__actions {
  display: flex;
  justify-content: space-between;
  margin-top: 10px;
}

.tweet__action-btn {
  padding: 8px 16px;
  border: none;
  border-radius: 20px;
  cursor: pointer;
  font-weight: bold;
  display: flex;
  align-items: center;
}

.tweet__like-btn {
  color: #e0245e;
  /* Twitter like button color 
}

.tweet__comment-btn {
  color: #1da1f2;
  /* Twitter comment button color 
}

.tweet__retweet-btn {
  color: #17bf63;
  /* Twitter retweet button color 
}

/* Add hover effects 
.tweet__action-btn:hover {
  cursor: pointer;
  color: #1da0f2;
}

.updateBtn {
  margin: auto;
  background-color: #121212;
  color: #fff;
  width: 100%;
  border: 1px solid rgb(211, 210, 210);
  border-radius: 20px;
  justify-content: center;
  outline: none;
  font-weight: bold;
  padding: 5px;
}

.inputDiv {
  margin-bottom: 1rem !important;
}

.inputBoxClass {
  display: block;
  width: 100%;
  padding: 0.375rem 0.75rem;
  font-size: 1rem;
  font-weight: 400;
  line-height: 1.5;
  color: #212529;
  background-color: #fff;
  background-clip: padding-box;
  border: 1px solid #ced4da;
  -webkit-appearance: none;
  -moz-appearance: none;
  appearance: none;
  border-radius: 0.25rem;
  transition: border-color .15s ease-in-out, box-shadow .15s ease-in-out;
}

.editProfileContainer {
  margin: auto;
  background-color: white;
  width: 28% !important;
  margin-top: 50px;
  padding-left: 3.5%;
  padding-right: 3.5%;
  padding-top: 20px;
  margin-bottom: 20px;
  border-radius: 20px;
}

.rounded-image{
  border-radius: 50%;
  cursor: pointer;
}

*/

...............................................sidebar-menu.................................

.sidebar-menu {
  /* border:1px solid black; */
  position: fixed;
}

.sidebar-menu__item {
  font-size: 19px;
  color: #0F141A;
  display: flex;
  align-items: center;
  font-weight: 700;
  padding: 10px;
}

.sidebar-menu__item:hover {
  background: rgba(29, 161, 242, 0.1);
  color: #1da0f2;
  border-radius: 20px;
  cursor: pointer;
}

.sidebar-menu__item--active {
  color: #1da0f2;
}

.sidebar-menu__item-icon {
  height: 26px;
  margin-right: 7px;
}

.sidebar-menu__item--active .sidebar-menu__item-icon {
  filter: invert(67%) sepia(60%) saturate(4956%) hue-rotate(176deg) brightness(95%) contrast(99%);
}
.sidebar-menu a{
  text-decoration: none;
  color: black;
}
.sidebar-menu a:hover{
  color:#1da0f2;
}

........................trends-for-you......................................


.trends-for-you {
  border-radius: 15px;
  background: #f5f8fa;
  margin-bottom: 15px;
}

.trends-for-you__block {
  border-bottom: 1px solid #e6ecf0;
  padding: 10px 15px;
}

.trends-for-you__block:last-child {
  border-bottom: none;
}

.trends-for-you__heading {
  font-size: 19px;
  font-weight: bold;
  color: #14171a;
}

.trends-for-you__meta-information {
  font-size: 13px;
  font-weight: 300;
  color: #657786;
}

.trends-for-you__trend-name {
  font-size: 15px;
  color: #14171a;
  font-weight: bold;
  margin-bottom: 5px;
  margin-top: 2px;
}

.............................................................................dashboard.js.................................

document.addEventListener("DOMContentLoaded", function () {
    // Image Upload Handling
    document.getElementById('postImgId').addEventListener('click', function () {
        // Trigger click event only if the file input is not already focused
        var tweetImageFileInput = document.getElementById('tweetImageFileInput');
        if (!tweetImageFileInput.matches(':focus')) {
            tweetImageFileInput.click();
        }
    });

    document.getElementById('tweetImageFileInput').addEventListener('change', function () {
        readImageURL(this);
    });

    // Video
    document.getElementById('postVideoId').addEventListener('click', function () {
        var tweetVideoFileInput = document.getElementById('tweetVideoFileInput');
        if (!tweetVideoFileInput.matches(':focus')) {
            tweetVideoFileInput.click();
        }
    });

    document.getElementById('tweetVideoFileInput').addEventListener('change', function () {
        readVideoURL(this);
    });

    function readImageURL(input) {
        if (input.files && input.files[0]) {
            var reader = new FileReader();

            reader.onload = function (e) {
                $('#postImgPreviewId').attr('src', e.target.result);
                $('#postImgPreviewId').show();
                $('#imagePreviewDivId').show();
                $('.up_icon').hide();
            };

            reader.readAsDataURL(input.files[0]);
        }
    }

    function readVideoURL(input) {
        if (input.files && input.files[0]) {
            var reader = new FileReader();

            reader.onload = function (e) {
                $('#postVideoPreviewId').attr('src', e.target.result);
                $('#postVideoPreviewId').show();
                $('#videoPreviewDivId').show();
                $('.up_icon').hide();
            };

            reader.readAsDataURL(input.files[0]);
        }
    }
});

$(document).ready(function () {


    $('.likeButton').click(function () {
        var button = $(this);
        var postId = button.data('post-id');
        var userId = button.data('user-id');

        $.ajax({
            url: 'index.php',
            type: 'POST',
            data: {
                page: 'like',
                action: 'like',
                postId: postId,
                userId: userId
            },
            success: function (response) {

                var data = response;
                button.find('.likeCount').text(data.likeCount);
                button.toggleClass('liked', data.likeStatus);
            },
            error: function (xhr, status, error) {
                console.error('Ajax error:', error);
            }
        });
    });

    // delete 
    $(".deleteButton").click(function () {
        
        var postId = $(this).data("post-id");
      
        if (confirm("Are you sure you want to delete this tweet?")) {
            $.ajax({
                type: 'POST',
                url: 'index.php',
                data: {
                    page: 'delete_tweet',
                    action: 'delete',
                    postId: postId,
                },
                success: function (response) {
                    var data = response;

                    console.log(response);
                    location.reload();
                },
                error: function (xhr, status, error) {
                    console.error(xhr.responseText);
                }
            });
        }
    });






    $('#postBtnId').click(function () {
        var textValue = $('#tweetTextId').val();
        var id = $('#userId').val();

        // Create FormData object to handle file uploads
        var formData = new FormData();
        formData.append('page', 'posttweet');
        formData.append('action', 'postTweet');
        formData.append('userId', id);
        formData.append('tweetText', textValue);

        // Append the image file input to FormData
        var imageFileInput = $('#tweetImageFileInput')[0].files[0];
        if (imageFileInput) {
            formData.append('tweetImage', imageFileInput);
        }

        // Append the video file input to FormData
        var videoFileInput = $('#tweetVideoFileInput')[0].files[0];
        if (videoFileInput) {
            formData.append('tweetVideo', videoFileInput);
        }

        $.ajax({
            url: "index.php",
            data: formData,
            type: 'post',
            contentType: false,
            processData: false,
            success: function (response) {
                if (response.trim() === 'posted') {
                    location.reload();
                } else {
                    alert("Failed to post tweet. Please try again.");
                }
            },
            error: function (xhr, status, error) {
                console.error("Error: " + error);
            }
        });
    });
});


...........................................................................................editProfile.js......
function triggerInput() {
    $("#fileInput").click();  
}

$("#fileInput").change(function () {
    readURL(this);
});

function readURL(input) {
    if (input.files && input.files[0]) {
        var reader = new FileReader();

        reader.onload = function (e) {
            $('#profileImage').attr('src', e.target.result);
        };

        reader.readAsDataURL(input.files[0]);
    }
}


