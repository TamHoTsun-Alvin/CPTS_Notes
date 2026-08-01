Note: This method is only suitable for transferring small files, and should be considered as last resort if files in question is big or other method is doable

Preparation:

-Encoding file to Base64 (Linux)

```
cat <filename> |base64 -w 0;echo
```

-Encoding file to Base64 (Windows):
```
[Convert]::ToBase64String([System.IO.File]::ReadAllBytes("C:\path\to\your\file.ext"))
#or outputting to a file to bypass transfer restriction
$bytes = [System.IO.File]::ReadAllBytes("C:\path\to\your\file.ext") 
$base64 = [Convert]::ToBase64String($bytes) [System.IO.File]::WriteAllText("C:\path\to\output.txt", $base64)
```

On receiving end:

-Decoding base64 to file (Linux)
```
base64 -d <<< "<base64_string>" > <output_filename>
#or from a file
base64 -d encoded.txt > <output_filename>
```
-Decoding base64 to file (Windows)
```
[IO.File]::WriteAllBytes("<output_full_path>", [Convert]::FromBase64String("<base64_string>"))
```

Ref: https://academy.hackthebox.com/app/module/24/section/514 / https://academy.hackthebox.com/app/module/24/section/160