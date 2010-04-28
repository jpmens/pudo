# PuDo

## Synopsis

	pudo directory URI

## Description

`pudo` reads the specified _directory_ for [JSON](http://www.json.org/) files (`*.json`) or [YAML](http://www.yaml.org/) files (`*.yaml`) and creates corresponding documents in the [CouchDB][] database at the specified URI. If `doc.json` respectively `doc.yaml` has a corresponding directory `doc.attachments`, all files therein are uploaded to the `doc` document.

If the read file contains an `_id` field it is used as the document's key, otherwise the base filename (sans `.json` / `.yaml`) is used.

## Example

Consider the directory `movies` with the following files in it:

drwxr-xr-x    238 Apr 28 16:02 movies
-rw-r--r--     88 Apr 28 12:33 movies/airforce1.json
drwxr-xr-x    102 Apr 28 16:03 movies/cpd.attachments
-rw-r--r--  43128 Nov 16 05:42 movies/cpd.attachments/n41380.jpg
-rw-r--r--    231 Apr 28 16:02 movies/cpd.yaml
drwxr-xr-x    136 Apr 28 12:30 movies/k-19.attachments
-rw-r--r--  40882 Apr 28 12:29 movies/k-19.attachments/cover.jpg
-rw-r--r--   1192 Apr 28 12:30 movies/k-19.attachments/review.txt
-rw-r--r--     93 Apr 28 12:25 movies/k-19.json

Now I run `pudo`:

	$ pudo movies http://192.168.1.30:5984/database
	movies/airforce1.json
	movies/cpd.yaml
		n41380.jpg (image/jpeg)
	movies/k-19.json
		cover.jpg (image/jpeg)
		review.txt (text/plain)

creating three documents; the `k-19`  has two files attached to it:

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

## YAML sample

The following YAML file

	title: Clear and Present Danger
	year: 1994
	genre:
	 - action
	 - drama
	 - thriller
	plot: >
	   CIA Analyst Jack Ryan is drawn into an illegal war fought by the
	   US government against a Colombian drug cartel.
	director: Phillip Noyce

translates to JSON as

	{
	   "director" : "Phillip Noyce",
	   "genre" : [
	      "action",
	      "drama",
	      "thriller"
	   ],
	   "plot" : "CIA Analyst Jack Ryan is drawn into an illegal war fought by the US government against a Colombian drug cartel.\n",
	   "title" : "Clear and Present Danger",
	   "_attachments" : {
	      "n41380.jpg" : {
		 "revpos" : 17,
		 "length" : 43128,
		 "content_type" : "image/jpeg",
		 "stub" : true
	      }
	   },
	   "year" : "1994"
	}

(Note that the field `plot` has an ugly newline character on it.)

## See Also

* [couchapp](http://github.com/jchris/couchapp)
* [AnyEvent::CouchDB](http://search.cpan.org/dist/AnyEvent-CouchDB/)
* [couchdb-push](http://search.cpan.org/dist/AnyEvent-CouchDB/bin/couchdb-push)

## Credits

I (Jan-Piet Mens) wrote this. I was creatly inspired by _couchapp_, and I might have stolen a bit of code and ideas from _couchdb-push_ (thanks). 

## Bugs

Probably. In any case `pudo` should be changed to support [CouchDB][]'s bulk interface.

[CouchDB]: http://couchdb.apache.org
