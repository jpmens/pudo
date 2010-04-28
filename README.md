# PuDo

## Synopsis

	pudo directory URI

## Description

`pudo` reads the specified _directory_ for JSON files (`*.json`) and creates corresponding documents in the [CouchDB][] database at the specified URI.

If the read JSON file contains an `_id` field it is used as the document's key, otherwise the base filename (sans `.json`) is used.

## Example

Consider the directory `movies` with the following files in it:

	drwxr-xr-x       170 Apr 28 12:33 movies
	-rw-r--r--        88 Apr 28 12:33 movies/airforce1.json
	-rw-r--r--        93 Apr 28 12:25 movies/k-19.json
	drwxr-xr-x       136 Apr 28 12:30 movies/k-19.json.attach
	-rw-r--r--     40882 Apr 28 12:29 movies/k-19.json.attach/cover.jpg
	-rw-r--r--      1192 Apr 28 12:30 movies/k-19.json.attach/review.txt

Now I run `pudo`:

	$ pudo movies http://192.168.1.30:5984/database
	movies/airforce1.json
	movies/k-19.json
	        cover.jpg (image/jpeg)
		review.txt (text/plain)

creating two documents; the second has two files attached to it:

	$ curl -s http://192.168.1.30:5984/database/k-19
	{
	   "_id" : "k-19",
	   "title" : "K-19: The Widowmaker",
	   "_attachments" : {
	      "cover.jpg" : {
		 "revpos" : 38,
		 "length" : 40882,
		 "content_type" : "image/jpeg",
		 "stub" : true
	      },
	      "review.txt" : {
		 "revpos" : 39,
		 "length" : 1192,
		 "content_type" : "text/plain",
		 "stub" : true
	      }
	   },
	   "_rev" : "39-728042cfe9666e945e3720f625d5b5ed",
	   "director" : "Kathryn Bigelow",
	   "year" : 2002
	}

## See Also

* [couchapp](http://github.com/jchris/couchapp)
* [AnyEvent::CouchDB](http://search.cpan.org/dist/AnyEvent-CouchDB/)
* [couchdb-push](http://search.cpan.org/dist/AnyEvent-CouchDB/bin/couchdb-push)

## Credits

I (Jan-Piet Mens) wrote this. I was creatly inspired by _couchapp_, and I might have stolen a bit of code and ideas from _couchdb-push_ (thanks). 

## Bugs

Probably. In any case `pudo` should be changed to support [CouchDB][]'s bulk interface.

[CouchDB]: http://couchdb.apache.org
