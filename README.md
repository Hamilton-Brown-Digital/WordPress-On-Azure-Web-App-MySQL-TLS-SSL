# Six Degrees - WordPress on Azure Web App

> A WordPress install set to run on an Azure Web App and link to an Azure MySQL instance that has TLS 1.2 enabled and SSL enforced

---

The ```BaltimoreCyberTrustRoot.crt.pem``` certificate is required to link the deployed Web App to an existing Azure MySQL resource (with TLS 1.2 enabled and SSL enforced).

[Download the 'Baltimore CyberTrust Root' PEM from here](https://www.digicert.com/kb/digicert-root-certificates.htm).

**The certificate must reside in the root of the site directory with the name 'BaltimoreCyberTrustRoot.crt.pem'.**

**Please check certificate expiry before deployment.**

---

**The current WordPress version hosted in this repository is: 5.5.3**

---

Two WordPress files have been modified to facilitate the secure connection between the Azure Web App (running the WordPress files) and the Azure Database for MySQL instance:

[wp-config.php](wp-config.php) includes Azure specific references as well as a link to the above certificate. The below highlights the modifications:

```
$connectstr_dbhost = getenv('DATABASE_HOST');
$connectstr_dbname = getenv('DATABASE_NAME');
$connectstr_dbusername = getenv('DATABASE_USERNAME');
$connectstr_dbpassword = getenv('DATABASE_PASSWORD');


define('DB_NAME', $connectstr_dbname);

/** MySQL database username */
define('DB_USER', $connectstr_dbusername);

/** MySQL database password */
define('DB_PASSWORD',$connectstr_dbpassword);

/** MySQL hostname */
define('DB_HOST', $connectstr_dbhost);

/** Database Charset to use in creating database tables. */
define( 'DB_CHARSET', 'utf8' );

/** The Database Collate type. Don't change this if in doubt. */
define( 'DB_COLLATE', '' );

/** For MySQL SSL */define('DB_SSL', true);
define('MYSQL_SSL_CA_PATH','BaltimoreCyberTrustRoot.crt.pem');
/*define('MYSQL_SSL_CA', getenv('MYSQL_SSL_CA'));*/
define('MYSQL_CLIENT_FLAGS', MYSQLI_CLIENT_SSL | MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);
```

*Note in the above, ```getenv``` refers to the Azure Web App - Application Settings required to link the App to the database.*

[wp-db.php](wp-includes/wp-db.php) also contains a reference to the certificate:

```
mysqli_ssl_set($this->dbh, NULL, NULL, ABSPATH . 'BaltimoreCyberTrustRoot.crt.pem', NULL, NULL);
```