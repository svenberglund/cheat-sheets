
# Table of Contents
1. [LDAP basic concepts](#Concepts)
2. [LDAP query examples](#ldap-query-examples)
3. [Windows AD](#win-ad)


# Concepts:

* *DIT* - Directory information tree
tree structure of directory like 

```
Acme 
     - People
            + John
	    + Jane
            + Ben
            + Burt
              ...

     - Groups
            - sales
                   + Member - Burt
                   + Member - Ben
            + admin
```            
            
* RootDSE is the top level - Acme in the above case.\
  On levels below there may be organisational levels (ou) or entrys.

* *Entry*

  Every entry (e.g. person "class" or object) has a unique `DN` and one or more `objectClasses`

* *DN* (distinguished name)

  Unique identifier for a entry or organisational unit.

* *objectClass*

  Packaging of a bunch of attributes

* *attribute*
  
  Example: ‘cn’ = ‘common name’, ‘sn’ = surname’, ‘o’ = organization

* *schema*

  Packaging files where attribute and object classes are contained.

  schema example for rootDSE Acme:
  ```

      1. dn: dc=example,dc=com,dc=au
 
      1.1 dn: ou=People,dc=example,dc=com,dc=au
      1.2 dn: ou=Groups,dc=example,dc=com,dc=au
  ```

* *dc*
     Domain component, a search path component, see example below\
     think of a component of a path

* *ou*
     Orgainzational unit

* *CN* 
     Common name. - think of an object instance at leaf level

     
# Query examples <a name="ldap-query-examples" /> 
```
("CN=Dev-India,OU=Distribution Groups,DC=gp,DC=gl,DC=google,DC=com");
```

From the com Domain Component, find the google Domain Component, and then inside it the gl Domain Component and then inside it the gp Domain Component.
In the gp Domain Component, find the Organizational Unit called Distribution Groups and then find the the object that has a common name of Dev-India.




# Windows Active Directory <a name="win-ad" /> 

To join windows machine to domain, do these steps:

* Make sure it uses the dns (probably on the DC machine. Set that under network settings.

* Run `sysprep` tool to get a unique SID  (at least on VM). check "generalize" box.

* Join it to domain, under name and domain settings.



