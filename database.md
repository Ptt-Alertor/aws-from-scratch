# Setup a Database

## RDS (SQL Database)

* Choose RDS Service
* Create RDS SQL database, choose one Database Engine you like
  * Check `Only enable options eligible for RDS Free Usage Tier`
* Set everything
* Test connection between RDS and EC2
  * Connect EC2 by SSH
  * Copy, Modify and Save Code as `db_test.php`

    ```php
    <?php
    $servername = "localhost";
    $dbname = "myDB"
    $username = "username";
    $password = "password";

    try {
        $conn = new PDO("mysql:host=$servername;dbname=$dbname", $username, $password);
        // set the PDO error mode to exception
        $conn->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
        echo "Connected successfully";
    } catch(PDOException $e) {
        echo "Connection failed: " . $e->getMessage();
    }
    ```

  * Execute the Code

    ```bash
    $php -f db_test.php
    Connected successfully
    ```

* Write a simple app that could get query result from database and show on web page
