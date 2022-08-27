# Module Configuration

## Rewrite Module

Perform URL rewriting. Allows to get rid URLs containing multiple para meters and transform to useful informatives new ones. 

The mechanism consist of rewriting the URI of the client request after it is received and before serving the file. The rewritted URI is matched against the location blocks in order to find the configuration that should be applied to the request.

Rewriting is performed by ```rewrite``` directive using regular epressions. 

### Metacharacters

- ^ Beginning: Entity after must be found at the beginning
  - ^h matches hello, hh, h (anything beginning with h)
- $ End: e$ 
  - matching sample, e, file (anything ending with e)
- . (dot) Matches any character: 
  - hell. matches hello, hellx, hell!. Not matches hell
- [ ] Set: Matches any character withing the specified set
  - [a - z] range
  - [ abcd ] set 
  - [ a-z0-9 ] 
  - example hell[a-y123-]
- ^ Negate Set: Matches any character that is not withing the specified set 
- | Alternation: Matches the entity placed either before or after |
    - hello|welcome matches hello, welcome, helloes, awelcome
- ( ) Grouping 
- \ Escape: allows to scape special characters. Hello\. matches Hello., Hello. How are You?, Hi! Hello...
  - not matching Hello,Hello!
- * 0 or More Times: The entity preceding * must be found 0 or more times 
  - he*llo matches hllo,hello,heeeeeeeello
- + 1 or more times 
- ? 0 or 1 
- {x} x times
- {x,y} x to y times 
- {x,} at least x times

## Internal Requests

- External requests directly originate from the client. The URI is the nmatched agains possible location blocks
- Inetrnal requests are triggered by Nginx via specific directives. 
  - error_page, index, rewrite, try_files, add_before_body, add_after_body directives creates internal requests

### Internal Requests Types

- Internal redirects: Nginx redirects the client requests internally, the URI is changed. 
- Sub-requests: Additional requests to generat content complementary to the main request.

### error_page

error_page directive allow to define sv behavior when a specific error code occurs.

```yaml
server {
    server_name website.com;
    error_page 403 /errors/forbidden.html;
    error_page 404 /errors/not_found.html;
}
```

## Conditional Structure

```yaml
server {
    if ($requests_method = POST) {
        ...
    }
}
```

### Operators 

- None: if ($string): is true if specified variable or data is not equial to an empty string or a string starting with 0 character.
- =, !=
- ~ case sensitive
- ~* case insensitive
- !~, !* negate matching
- -f !-f 
  - if (-f $request_filename) tests the existence of a specified file 
- -d, !-d similar -f but directory
- -e !-e, similar -f but file, directory or symbolic link
- -x !-x executable

## Directives 

### rewrite

- server, location, if
- allow to rewrite the URI of the curent request 
- ```rewrite regexp replacement [flag]```
- regexp: the regular expression that the URI should match in order for the replacement to apply
- Flag:
  - last
  - break
  - redirect

### break

prevent rewrite directives

### return

interrupts the processing of the request and returns the specified htt[ status code or specified test

```return code | text```

### set

initializes or redefines a variable. Note that some variables are reasd only.

``` set variable value```

when a variable that has not yet been initalized nginx will issue log messages if declared ```unitialized_variable_warn on;``` before.

### rewrite_log

if set to on, nginx will issue log messags for every operation performed by the rewrite engine at the notice error level. 

### Common rewrite rules















































































































--------------------

## SSI commands

The principle is simple. Disgn regular HTML pages and insert inside SSI commands that looks like regular HTML comments 

```html
<!--# command param1="value" param2="value2 -->
<!--# include file="header.html" -->
```

This command generates an HTTP sub-request to be processed by Nginx. The body of the response is inserted instead the command itself.

```html
<!--# include virtual="/sources/header.php?id=123" -->
```

This sends a sub-request to the server. The directive wait="yes" is using to specify that Nginx should wait for the completion of the request before moving on to other includes.

If the result of the include is empty Nginx inserts 404 or 500 error page.

### Variables

Nginx SSI module offers the option of working with variables. Displaying a variable can be done with echo command 

```html
<!-- var="variable_name" -->
```

- var: Name of the variable to display.
- default: A string to be displayed in case the var is empty
- encoding: encoding method for the string

the command set, insthead of echo, write the variable value

```html
<!--# echo var="MYVAR"  --> (none)
<!--# set var=MYNAME" value="Gian" -->
<!--# set var="MYVAR" value="hello $MYNAME" -->
<!--# echo var="MYVAR"  --> (hello Gian) 
```

```html
<!-- if expr="expression1" -->
...
<!-- elif expr="expression2" -->
...
<!-- else -->
...
<!-- endif -->
```

```html
<!--# if expr="variable = some_value" -->
<!--# if expr="variable = /pattern/" -->
```

-------------------

## Access and Logging Module

Allows to config the way visitors access your website and the way your server logs requests.

### index

- http, server, location

```yaml
index index.php index.html index.htm
```

#### Log

Controls the behavior of Nginx regarding the access logs. It is a key module for system administrators, allows to analyzing the runtime behavior of web apps.

##### access_log

- http, server, location, if, limit_except
- defines
  - acess log file path
  - format of entries in the access log
- ```acess_log path [format [buffer=size] | off```
- off to disable access loging at the current level
- format argument corresponds to a tepmlate declared with log_format directive

#### log_format

- http, server, location
- template to be utilized by access_log directive
- ```log_format template_name format_string;```
- default template is called **combined** 
- default: ```log_format combined '$remote_addr - $remote_user [$time_local] '"$request: $status $body_bytes_sent '"$http_referer" "$http_user_agent"';```
- ```log_format simple '$remote_addr $request```
  
  #### open_log_file_cache

  - http, server, location
  - configure cache for log file descriptors
  
  ### Log Module Variables

  - connection
  - pipe
  - time_local
  - msec
  - request_time
  - status
  - bytes_sent
  - body_bytes_sent
  - apache_bytes_sent
  - request_length

---------------

## Limits and Restrictions 

Regulates access to the coduments of your websites. Restricts the access and require users to:
- authenticate
- match a set of rules

### Auth_basic module

enables basic authentication funcitonality. Username and password

```yaml
location /admin/ {
  auth_basic "Admin control panel"; #Variables supported
  auth_basic_user_file access/password_file
}

auth_basic can be set to either off or a text message, referred to as authentication challenge or realm. Is displayed by browsers in a username/password box when client attemps to access.

auth_basic_user_file defines the path of the password file relative to the directory of the configuration file.

```txt
password file syntax"
username:[{SCHEME}]password[:comment].

username: plain text user name
SCHEME: optionally. PAssword hashing method
  - PLAIN
  - SHA (SHA-1 Hashing)
  - SSHA SaltedSHA-1 hashing
password: password
comment: plaint text comment for own use 
```

### Access Module

- allow and deny directives
- let allow or deny access to a resource for a specific IP address or IP address range.
- ```allow/deny IP | CIDR | unix: | all```
- unix represent domain sockets

```yaml
location {
  allow 127.0.0.1;
  allow unix;
  deny all; #deny all other ip addresses
}
```

### Limit Connections 

Allows to define maximun number of simultaneous connections to the server for a specific zone.

- limit_conn_zone $variable zone=name:size;
- $variable is the mechanism to differentiate one client from another. Typically $binary_remote_Addr
- name is an aribtrary name given to the zone
- size: max size to the table storing session states

```yaml
# Examples
limit_conn_zone $binary_remote_addr zone=myzone:10m;
limit_conn zone_name connection_limit;
```

### Limit request

allows to limit the number of requests for a defined zone. 

```yaml
limit_req_zone $variable zone=name:max_memory_size rate=rate;
```
