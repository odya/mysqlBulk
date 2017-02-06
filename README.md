# mysqlBulk
PHP function for MySQL large data import (written by @kvz)

Original of this code is located at: http://kvz.io/blog/2009/03/31/improve-mysql-insert-performance/

# Using the Function
The mysqlBulk function supports a couple of methods.

## Array Input With Method: Loaddata (Preferred)

What would really give it wings, is if you can supply the data as an array. That way I won't have to translate your raw queries to arrays, before I can convert them back to CSV format. Obviously skipping all that conversion saves a lot of time.
```
<?php
$data   = array();
$data[] = array('level' => 'err', 'msg' => 'foobar!');
$data[] = array('level' => 'err', 'msg' => 'foobar!');
$data[] = array('level' => 'err', 'msg' => 'foobar!');

if (false === ($qps = mysqlBulk($data, 'log', 'loaddata', array(
    'query_handler' => 'mysql_query'
)))) {
    trigger_error('mysqlBulk failed!', E_USER_ERROR);
} else {
    echo 'All went well @ '.$qps. ' queries per second'."n";
}
?>
```
Most of the time it's even easier cause you don't have to write queries.

## SQL input with method: loadsql_unsafe

If you can really only deliver raw insert queries, use the loadsql_unsafe method. It's unsafe because I convert your queries to arrays on the fly. That also makes it 10 times slower (still twice as fast as other methods).

This is what the basic flow could look like:
```
<?php
$queries   = array();
$queries[] = "INSERT INTO `log` (`level`, `msg`) VALUES ('err', 'foobar!')";
?>
<?php
$queries[] = "INSERT INTO `log` (`level`, `msg`) VALUES ('err', 'foobar!')";
?>
<?php
$queries[] = "INSERT INTO `log` (`level`, `msg`) VALUES ('err', 'foobar!')";
?>
<?php
if (false === ($qps = mysqlBulk($queries, 'log', 'loadsql_unsafe', array(
    'query_handler' => 'mysql_query'
)))) {
    trigger_error('mysqlBulk failed!', E_USER_ERROR);
} else {
    echo 'All went well @ '.$qps. ' queries per second'."n";
}
?>
```

## Safe SQL Input With Method: Transaction

Want to do a Transaction?
```
<?php
mysqlBulk($queries, 'transaction');
?>
```

## Options

Change the query_handler from mysql_query to your actual query function. If you have a DB Class with an execute() method, you will have to encapsulate them inside an array like this:
```
<?php
$db = new DBClass();
mysqlBulk($queries, 'log', 'none', array(
    'query_handler' => array($db, 'execute')
);
// Now your $db->execute() function will actually
// be used to make the real MySQL calls
?>
```

Don't want mysqlBulk to produce any errors? Use the trigger_errors option.
```
<?php
mysqlBulk($queries, 'log', 'transaction', array(
    'trigger_errors' => false
);
?>
```

Want mysqlBulk to produce notices? Use the trigger_notices option.
```
<?php
mysqlBulk($queries, 'log', 'transaction', array(
    'trigger_notices' => true.
);
?>
```
