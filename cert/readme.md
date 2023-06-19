# To create a new SSL certificate:

## Using Windows Powershell

#### 1. Run following script in PowerShell

    param (
        [Parameter(Mandatory=$true)][string]$certificatename,
        [Parameter(Mandatory=$true)][SecureString]$certificatepassword
     )
    # setup certificate properties including the commonName (DNSName) property for Chrome 58+
    $certificate = New-SelfSignedCertificate `
        -Subject localhost `
        -DnsName localhost `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -NotBefore (Get-Date) `
        -NotAfter (Get-Date).AddYears(2) `
        -CertStoreLocation "cert:CurrentUser\My" `
        -FriendlyName "Localhost Certificate for .NET Core" `
        -HashAlgorithm SHA256 `
        -KeyUsage DigitalSignature, KeyEncipherment, DataEncipherment `
        -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1") 
    $certificatePath = 'Cert:\CurrentUser\My\' + ($certificate.ThumbPrint)
    # create temporary certificate path
	
    $tmpPath = "C:\tmp"
    If(!(test-path $tmpPath))
    {
    New-Item -ItemType Directory -Force -Path $tmpPath
    }
	
    # set certificate password here
    $pfxPassword = $certificatepassword
    $pfxFilePath = $tmpPath + "\" + $certificatename + ".pfx"
    $cerFilePath = $tmpPath + "\" + $certificatename + ".cer"
	
    # create pfx certificate
    Export-PfxCertificate -Cert $certificatePath -FilePath $pfxFilePath -Password $pfxPassword
	
    Export-Certificate -Cert $certificatePath -FilePath $cerFilePath
    # import the pfx certificate
    Import-PfxCertificate -FilePath $pfxFilePath Cert:\LocalMachine\My -Password $pfxPassword -Exportable
	
    # trust the certificate by importing the pfx certificate into your trusted root
    Import-Certificate -FilePath $cerFilePath -CertStoreLocation Cert:\CurrentUser\Root
	
    # optionally delete the physical certificates (don’t delete the pfx file as you need to copy this to your app directory)
    # Remove-Item $pfxFilePath
    Remove-Item $cerFilePath

#### 2. To convert PFX file to seperate PEM and KEY files

    openssl pkcs12 -in C:/tmp/localhost.pfx -clcerts -nokeys -out C:/tmp/pem/CA.pem
    openssl pkcs12 -in C:/tmp/localhost.pfx -nocerts -nodes -out C:/tmp/pem/CA.key

#### 3. Copy files under /etc/ folder and use in `nginx.conf` as following

    http {
       server {
          listen 443 ssl;
    		
          ssl_certificate /etc/nginx/ssl/certificate.pem;
          ssl_certificate_key /etc/nginx/ssl/private.key;
       }
    }

## Using openssl (recommeded):

#### 1. Create the initial .key file by running the following commands on terminal:

    mkdir cert
    cd cert
    mkdir CA
    cd CA
    openssl genrsa -out CA.key -des3 2048

#### 2. Generate the .pem file with:
    
    openssl req -x509 -sha256 -new -nodes -days 3650 -key CA.key -out CA.pem

#### 3. Generate the certificate:

    mkdir localhost
    cd localhost
    touch localhost.ext

#### 4. Generate a key with:

    openssl genrsa -out localhost.key -des3 2048

#### 5. In order to create the .csr file run:

    openssl req -new -key localhost.key -out localhost.csr

#### 6. Convert to .csr in order to make it valid:

    openssl x509 -req -in localhost.csr -CA ../CA.pem -CAkey ../CA.key -CAcreateserial -days 3650 -sha256 -extfile localhost.ext -out localhost.crt

#### 7. Convert the csr file to .pem and .key, if it doesn't work then try generating it with the in argument pointing to the previous .ext file

    openssl pkcs12 -in localhost.crt -clcerts -nokeys -out C:/tmp/pem/CA.pem
    openssl pkcs12 -in localhost.crt -nocerts -nodes -out C:/tmp/pem/CA.key

