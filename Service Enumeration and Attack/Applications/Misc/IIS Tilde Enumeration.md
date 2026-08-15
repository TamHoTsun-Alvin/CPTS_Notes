IIS Tilde Enumeration is a technique that can be used to reveal hidden files, directories under different version of IIS webserver, it originates from the 8.3 naming format:

For a path / filename, a short name is generated following the following convention:
-first 6 characters of the original filename
-a tilde (~)character
-a sequence number (combined with previous 2 is exactly 8 character)
-If it's filename, a dot (.) and file extension

Example: 

Directoryname: 