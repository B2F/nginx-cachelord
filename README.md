Nginx CacheLord
===============

Would your like to get filtered lists of your FastCGI cache entries, from your **fastcgi\_cache\_key**'s $request\_uri values ?

The nginx-cachelord.sh script is a small Unix shell script aiming to manage Nginx's FastCGI cache **selectively**:

###Specify your custom cache directory###

First, if your use another cache directory than **/var/cache/nginx**, your can edit the **cache_dir** variable in the nginx-cachelord.sh script or write it inside a **nginx-cache-dir.ini** text file:

    cache_dir=/var/nginx/cache

###Purge the cache selectively###
Call the script without options and it will erase cache entries according to the mandatory token argument:

    nginx-cachelord.sh example.com/en
    « /var/nginx/cache/ifzenelse/5/cc/f03037321598888c7eb6503853824cc5 » removed
    « /var/nginx/cache/ifzenelse/6/62/00a62ec49a9f974b17ac93b09f777626 » removed
    « /var/nginx/cache/ifzenelse/4/89/1880afbadcbe3787788dca921988d894 » removed

In the above, cache keys containing the "example.com/en" token will be erased without confirmation. To preview your changes, use the **--list** option below.

###List your cache content selectively (--list)###
The --list argument returns cache keys filtered by the token argument (no invalidations involved):

    nginx-cachelord.sh example.com/en --list
    /var/cache/nginx/5/cc/f03037321598888c7eb6503853824cc5:KEY: GETexample.com/en
    /var/cache/nginx/6/62/00a62ec49a9f974b17ac93b09f777626:KEY: GETexample.com/en/tags
    /var/cache/nginx/4/89/1880afbadcbe3787788dca921988d894:KEY: GETexample.com/en/blog

It will search for all cache keys containing the "example.com/en" token in your **cache_dir**  and return the results. Your can call also use it with a dot (like any) and/or with a $ (end of string): 

    nginx-cachelord.sh . --list
    > list all keys

    nginx-cachelord.sh example.com/en$ --list
    /var/cache/nginx/5/cc/f03037321598888c7eb6503853824cc5:KEY: GETexample.com/en

###Show cache files by date (--date)###
The --date option returns results sorted by date:

    nginx-cachelord.sh example.com/en --date
    -rw------- 1 nginx nginx 45660 10 avril 23:27 /var/cache/nginx/4/89/1880afbadcbe3787788dca921988d894
    -rw------- 1 nginx nginx 28974 10 avril 23:26 /var/cache/nginx/6/62/00a62ec49a9f974b17ac93b09f777626
    -rw------- 1 nginx nginx 43654 10 avril 23:26 /var/cache/nginx/5/cc/f03037321598888c7eb6503853824cc5

###Show cache files by date (--new)###
The --new option returns results KEYS text, with most recent results printed first.

    nginx-cachelord.sh example.com/en --date
    GETexample.com/en
    GETexample.com/en/tags
    GETexample.com/en/blog

###Watch for cache changes###
The Unix watch command is helpful to monitor your cache changes, either with --list or --date options, filtered by tokens:

    watch -n1 nginx-cachelord.sh /en --new
    Every 1,0s: ls
    KEYS: GETexample.com/en
    KEYS: GETexample.com/en/tags
    KEYS: GETexample.com/en/blog

The -n1 option means 1 second.


###Count cache entries###
The wc Unix command with the -l option is used to count lines, you can take its advantage to count cache entries with a given token, for instance checking the number of cached pages for a website's different languages:

    nginx-cachelord.sh example.com/fr --list |wc -l
    nginx-cachelord.sh example.com/en --list |wc -l
