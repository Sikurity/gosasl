# Go SASL library

See README.md at `github.com/beltran/gosasl`
this repository adds dynamic linking library interface to support windows platform from `github.com/beltran/gosasl`

## Installation
Gosasl can be installed with:
```
go get github.com/Sikurity/gosasl
```

To add kerberos support gosasl requires header files to build against the GSSAPI C library. They can be installed with:
- Ubuntu: `apt-get install libkrb5-dev`
- MacOS: `brew install homebrew/dupes/heimdal --without-x11`
- Debian: `yum install -y krb5-devel`
- Windows: `choco install mitkerberos --install-arguments="ADDLOCAL=all"`

Then:
```
go get -tags kerberos github.com/Sikurity/gosasl
```

## Example Usage

```go
    mechanism, err := NewGSSAPIMechanism("service")
	if err != nil {
		log.Fatal(err)
    }    
    conn = getConnection("somehost")
    client := NewSaslClientWithMechanism("somehost", mechanism)
    response, err := client.Start()
    if err != nil {
		log.Fatal(err)
    }
    conn.sendResponse(response)

    for true {
        status, challenge = conn.getChallenge()
        if status == COMPLETE {
            break
        } else if status == OK {
            response = client.Step(challenge)
            conn.sendResponse(response)
        } else {
            log.Fatal("Failed to establish connection")
        }
    }
    if !client.Complete() {
        log.Fatal("SASL negotiation did not complete")
    }

    // begin normal communication
    encoded := conn.fetchData()
    decoded := client.Decode(encoded)
    response = processData(decoded)
    conn.sendData(client.Encode(response))

    client.Dispose()
```
