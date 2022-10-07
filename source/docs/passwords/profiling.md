# Password profiling

Having a good wordlist is critical to carrying out a successful password attack.

## Default passwords

Before performing password attacks, it is worth trying a couple of default passwords against the targeted service. 
Manufacturers set default passwords with products and equipment such as switches, firewalls, routers. There are 
scenarios where customers did not change the default password, making the system vulnerable. It is good practice to 
try out `admin:admin`, `admin:password123`, ... If we know the target device, service or software, we can look up 
the default passwords (if any) and try those. 

Some websites that provide default passwords for some products:

* [CIRT.net Default Passwords](https://cirt.net/passwords)
* [DefaultPassword](https://default-password.info/)
* [Datarecovery.com Default Passwords](https://datarecovery.com/rd/default-passwords/)

## Weak Passwords

Professionals collect and generate weak password lists over time and often combine them into one large wordlist. 
Lists are generated based on their experience and what they see in pentesting engagements. These lists may also 
contain leaked passwords that have been published publically. 

Some of those public common weak passwords lists:

* [Skull security passwords](https://wiki.skullsecurity.org/index.php?title=Passwords) - This includes the most 
well-known collections of passwords.
* [SecLists](https://github.com/danielmiessler/SecLists) - A huge collection of all kinds of lists, not only for password cracking.

## Leaked Passwords

Sensitive data such as passwords or hashes may be publicly disclosed or sold as a result of a breach. These public 
or privately available leaks are often referred to as 'dumps'. Depending on the contents of the dump, an attacker 
may need to extract the passwords out of the data. In some cases, the dump may only contain hashes of the passwords 
and require cracking in order to gain the plain-text passwords. 

These are some common password lists that have weak and leaked passwords, including webhost, elitehacker, hak5, 
Hotmail, PhpBB companies' leaks:

* [SecLists/Passwords/Leaked-Databases](https://github.com/danielmiessler/SecLists/tree/master/Passwords/Leaked-Databases)

## Combined wordlists

Combine these wordlists into one large file with `cat`:
     
    cat file1.txt file2.txt file3.txt > combined_list.txt

To clean up the generated combined list to remove duplicated words:
           
    sort combined_list.txt | uniq -u > cleaned_combined_list.txt

## Customized wordlists

Customizing password lists is one of the best ways to increase the chances of finding valid credentials. We can 
create custom password lists from the target website. Often, a website contains valuable information about the 
organisation and its people, including emails and names. The website may contain keywords specific to what the 
organisation does or offers, including product and service names, which may be used in a password.

Tools such as `cewl` can crawl a website and extract strings or keywords: 
          
    cewl -w list.txt -d 5 -m 5 http://target.com

    -w will write the contents to a file (list.txt).
    -m 5 gathers strings (words) that are 5 characters or more
    -d 5 is the depth level of web crawling/spidering (default 2)

The result is a decently sized wordlist based on relevant words for the specific organisation, like names, locations, 
and a lot of their lingo. The created wordlist can be used to fuzz for usernames. 

## Username wordlists

With peoples' names gathered during enumeration, we can generate username lists from the target's website. For 
example, with a {first name} {last name} and a method of generating usernames:

    {first name}: john
    {last name}: smith
    {first name}{last name}:  johnsmith 
    {last name}{first name}:  smithjohn  
    first letter of the {first name}{last name}: jsmith 
    first letter of the {last name}{first name}: sjohn  
    first letter of the {first name}.{last name}: j.smith 
    first letter of the {first name}-{last name}: j-smith 
    ...

There is a tool `username_generator` that creates a list with most of the possible combinations if we have a first 
name and last name.

    git clone https://github.com/therodri2/username_generator.git
    cd username_generator
    python3 username_generator.py -h 
    usage: username_generator.py [-h] -w wordlist [-u]

    Python script to generate user lists for bruteforcing!
    
    optional arguments:
      -h, --help            show this help message and exit
      -w wordlist, --wordlist wordlist
                            Specify path to the wordlist
      -u, --uppercase       Also produce uppercase permutations. Disabled by default

Continued:

    echo "John Smith" > users.lst
    python3 username_generator.py -w users.lst
    usage: username_generator.py [-h] -w wordlist [-u]
    john
    smith
    j.smith
    j-smith
    j_smith
    j+smith
    jsmith
    smithjohn

## Resources

* [Kali Linux – Crunch Utility](https://www.geeksforgeeks.org/kali-linux-crunch-utility/)
* [CUPP – Common User Passwords Profiler](https://www.geeksforgeeks.org/cupp-common-user-passwords-profiler/)