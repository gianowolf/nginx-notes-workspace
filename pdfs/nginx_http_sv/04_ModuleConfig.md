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

--------

## Common rewrite rules 

Basic rewrite rules to stisfy basics of dynamic websites that wish to beautify page links. http://website.com/ was omitted at the beggining of URIs.

#### Search

- input: /search/some-search-keywords
- rewritten: /search.php?q=some-search-keywords
- ```rewrite ^/search/(.*)$ /search.php?q=$1?;```

#### Multiple Parameters

- Input: /index.php/param1/param2/param3
- Rewritten: /index.php?p1=param1&p2=param2&p3=param3
- ```rewrite ^/index.php/(*.)/(*.)/(*.)$ /index.php?p1=$1&p2=$2&p3=$3

#### Wiki-like

- /wiki/some_keyword
- /wiki/index.php?title=some_keyword
- rewrite ^/wiki/(*.)$  /wiki/index.php?title=$1?;
  
#### News Website Article

- /33526/us-economy
- /article.php?id=33526
- rewrite ^/([0-9]+)/.*$  /article.php?id=$1;

-------

## SSI Module

SSI (Server Side Includes) is a sort of server-side programming language interpreted by Nginx. Created in order to render web pages dynamically, from static .html files with client-side scripts to complex pages with server-processes indstrucitons.

### Module Directives

- ssi: Enables parsing files for SSI commands. Nginx only parses the files corresonding to the MIME types selected with the ssi_types directive. Syntax on/off, default off; ```ssi on;```
- ssi_types: Define MIME types that should be eligible for SSI parsing. ```ssi_types type1 [type2] [type3]``` default text/html;
- ssi_client_errors: Enabling this option to silence Nginx error ssi messages.
- ssi_value_length: Maximun length accepted by Nginx in characters
- ssi_ignore_recycled_buffers
- ssi_min_file_chunk
- ssi_last_modified
  
  ### 