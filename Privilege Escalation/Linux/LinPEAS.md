For automated Linux PrivEsc enumeration, we could use LinPEAS, this should be used on every linux host we encounter:

First, we need to host linpeas.sh on our end and have it transferred to our target

Next, we simply do:
```
chmod +x linpeas.sh 
./linpeas.sh
```

We can also save it's output to a file:
```
./linpeas.sh > /tmp/results.txt
```

Below is linpeas color scheme:
- **Red/Yellow:** High-risk issues, passwords, or weak file permissions that likely lead to root access (look here first).

- **Green:** General safe information or standard paths.

- **Blue:** Interesting files or configurations that need manual review.