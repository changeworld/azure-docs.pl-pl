---
title: Sposób tworzenia certyfikatów X.509 przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Jak używać programu PowerShell do tworzenia certyfikatów X.509 lokalnie i włączyć X.509 na podstawie zabezpieczeń w usłudze Azure IoT hub w środowisku symulowanym.
author: dsk-2015
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: dkshir
ms.openlocfilehash: d0063ff79a0bda88fffb486f03286f6784ece7fa
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2018
ms.locfileid: "34637603"
---
# <a name="powershell-scripts-to-manage-ca-signed-x509-certificates"></a>Skrypty programu PowerShell do zarządzania certyfikatami X.509 podpisany przez urząd certyfikacji

Zabezpieczenia oparte na certyfikatach X.509 w usłudze IoT Hub, należy rozpocząć od [łańcucha certyfikatów X.509](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification), który zawiera certyfikat główny, a także wszystkie certyfikaty pośrednie przesłaną certyfikatu liścia. To *jak* przewodnik przeprowadzi Cię przez przykładowych skryptów programu PowerShell używające [OpenSSL](https://www.openssl.org/) Aby utworzyć i podpisać certyfikatu x.509. Firma Microsoft zaleca skorzystaj z tego przewodnika wyłącznie eksperymentów, ponieważ wiele z tych kroków nastąpi podczas procesu w świecie rzeczywistym produkcyjnego. Tych certyfikatów można użyć do symulowania zabezpieczeń przy użyciu usługi Azure IoT hub *uwierzytelnianie certyfikatu X.509*. Kroki opisane w tym przewodniku Tworzenie certyfikatów lokalnie na komputerze Windows. 

## <a name="prerequisites"></a>Wymagania wstępne
Ten samouczek zakłada uzyskali plików binarnych biblioteki OpenSSL. Użytkownik może:
    - Pobierz kod źródłowy biblioteki OpenSSL i kompilowania plików binarnych na komputerze, lub 
    - Pobierz i zainstaluj dowolne [plików binarnych biblioteki OpenSSL firm](https://wiki.openssl.org/index.php/Binaries), na przykład z [tego projektu na SourceForge](https://sourceforge.net/projects/openssl/).

<a id="createcerts"></a>

## <a name="create-x509-certificates"></a>Tworzenie certyfikatów X.509
Poniższe kroki pokazują przykład sposobu tworzenia certyfikatów głównych X.509 lokalnie. 

1. Otwórz okno programu PowerShell jako *administratora*.  
   **Uwaga:** należy otworzyć tego w programie PowerShell samego, nie PowerShell ISE, Visual Studio Code lub innych narzędzi, które umieszczają w otoce podstawowej konsola programu PowerShell.  Przy użyciu innej konsoli programu PowerShell w oparciu o spowoduje `openssl` poniższe wiszące polecenia.

2. Przejdź do katalogu roboczego. Uruchom następujący skrypt, aby ustawić zmienne globalne. 
    ```PowerShell
    $openSSLBinSource = "<full_path_to_the_binaries>\OpenSSL\bin"
    $errorActionPreference    = "stop"

    # Note that these values are for test purpose only
    $_rootCertCommonName      = "Azure IoT Root CA"
    $_rootCertSubject         = "CN=$_rootCertCommonName"
    $_intermediateCertSubject = "Azure IoT Intermediate {0} CA"
    $_privateKeyPassword      = "123"

    $rootCACerFileName          = "./RootCA.cer"
    $rootCAPemFileName          = "./RootCA.pem"
    $intermediate1CAPemFileName = "./Intermediate1.pem"
    $intermediate2CAPemFileName = "./Intermediate2.pem"
    $intermediate3CAPemFileName = "./Intermediate3.pem"

    $openSSLBinDir              = Join-Path $ENV:TEMP "openssl-bin"

    # Whether to use ECC or RSA.
    $useEcc                     = $true
    ```
3. Uruchom następujący skrypt, który kopiuje pliki binarne OpenSSL do katalogu roboczego i ustawia zmienne środowiskowe:

    ```PowerShell
    function Initialize-CAOpenSSL()
    {
        Write-Host ("Beginning copy of openssl binaries to {0} (and setting up env variables...)" -f $openSSLBinDir)
        if (-not (Test-Path $openSSLBinDir))
        {
            mkdir $openSSLBinDir | Out-Null
        }

        robocopy $openSSLBinSource $openSSLBinDir * /s 
        robocopy $openSSLBinSource . * /s 

        Write-Host "Setting up PATH and other environment variables."
        $ENV:PATH += "; $openSSLBinDir"
        $ENV:OPENSSL_CONF = Join-Path $openSSLBinDir "openssl.cnf"

        Write-Host "Success"
    }
    Initialize-CAOpenSSL
    ```
4. Następnie uruchom następujący skrypt, który wyszukuje czy certyfikatu przez określony *nazwy podmiotu* jest już zainstalowany, oraz czy OpenSSL jest poprawnie skonfigurowany na komputerze:
    ```PowerShell
    function Get-CACertBySubjectName([string]$subjectName)
    {
        $certificates = gci -Recurse Cert:\LocalMachine\ |? { $_.gettype().name -eq "X509Certificate2" }
        $cert = $certificates |? { $_.subject -eq $subjectName -and $_.PSParentPath -eq "Microsoft.PowerShell.Security\Certificate::LocalMachine\My" }
        if ($NULL -eq $cert)
        {
            throw ("Unable to find certificate with subjectName {0}" -f $subjectName)
        }
    
        write $cert
    }
    function Test-CAPrerequisites()
    {
        $certInstalled = $null
        try
        {
            $certInstalled = Get-CACertBySubjectName $_rootCertSubject
        }
        catch {}

        if ($NULL -ne $certInstalled)
        {
            throw ("Certificate {0} already installed.  Cleanup CA certs 1st" -f $_rootCertSubject)
        }

        if ($NULL -eq $ENV:OPENSSL_CONF)
        {
            throw ("OpenSSL not configured on this system.  Run 'Initialize-CAOpenSSL' (even if you've already done so) to set everything up.")
        }
        Write-Host "Success"
    }
    Test-CAPrerequisites
    ```
    Jeśli wszystko jest poprawnie skonfigurowane, powinien zostać wyświetlony "komunikat Success" wiadomości. 

<a id="createcertchain"></a>

## <a name="create-x509-certificate-chain"></a>Tworzenie łańcucha certyfikatów X.509
Tworzenie łańcucha certyfikatów za pomocą głównego urzędu certyfikacji, na przykład "CN = usługi Azure IoT głównego urzędu certyfikacji", w tym przykładzie użyto, uruchamiając następujący skrypt programu PowerShell. Ten skrypt aktualizuje również swojego magazynu certyfikatów systemu operacyjnego Windows, jak również tworzy pliki certyfikatów w katalogu roboczym. 
    1. Poniższy skrypt tworzy funkcję programu PowerShell, aby utworzyć certyfikat z podpisem własnym, dla danej *nazwy podmiotu* i podpisywania urzędu. 
    ```PowerShell
    function New-CASelfsignedCertificate([string]$commonName, [object]$signingCert, [bool]$isASigner=$true)
    {
        # Build up argument list
        $selfSignedArgs =@{"-DnsName"=$commonName; 
                           "-CertStoreLocation"="cert:\LocalMachine\My";
                           "-NotAfter"=(get-date).AddDays(30); 
                          }

        if ($isASigner -eq $true)
        {
            $selfSignedArgs += @{"-KeyUsage"="CertSign"; }
            $selfSignedArgs += @{"-TextExtension"= @(("2.5.29.19={text}ca=TRUE&pathlength=12")); }
        }
        else
        {
            $selfSignedArgs += @{"-TextExtension"= @("2.5.29.37={text}1.3.6.1.5.5.7.3.2,1.3.6.1.5.5.7.3.1", "2.5.29.19={text}ca=FALSE&pathlength=0")  }
        }

        if ($signingCert -ne $null)
        {
            $selfSignedArgs += @{"-Signer"=$signingCert }
        }

        if ($useEcc -eq $true)
        {
            $selfSignedArgs += @{"-KeyAlgorithm"="ECDSA_nistP256";
                             "-CurveExport"="CurveName" }
        }

        # Now use splatting to process this
        Write-Host ("Generating certificate {0} which is for prototyping, NOT PRODUCTION.  It will expire in 30 days." -f $subjectName)
        write (New-SelfSignedCertificate @selfSignedArgs)
    }
    ``` 
    2. Używając następującej funkcji programu PowerShell tworzy pośrednich certyfikatów X.509 przy użyciu poprzedniej funkcji, a także plików binarnych biblioteki OpenSSL. 
    ```PowerShell
    function New-CAIntermediateCert([string]$commonName, [Microsoft.CertificateServices.Commands.Certificate]$signingCert, [string]$pemFileName)
    {
        $certFileName = ($commonName + ".cer")
        $newCert = New-CASelfsignedCertificate $commonName $signingCert
        Export-Certificate -Cert $newCert -FilePath $certFileName -Type CERT | Out-Null
        Import-Certificate -CertStoreLocation "cert:\LocalMachine\CA" -FilePath $certFileName | Out-Null

        # Store public PEM for later chaining
        openssl x509 -inform deer -in $certFileName -out $pemFileName

        del $certFileName
   
        write $newCert
    }  
    ```
    3. Używając następującej funkcji programu PowerShell tworzy łańcucha certyfikatów X.509. Odczyt [łańcuchy certyfikatów](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification) Aby uzyskać więcej informacji.
    ```PowerShell
    function New-CACertChain()
    {
        Write-Host "Beginning to install certificate chain to your LocalMachine\My store"
        $rootCACert =  New-CASelfsignedCertificate $_rootCertSubject $null
    
        Export-Certificate -Cert $rootCACert -FilePath $rootCACerFileName  -Type CERT
        Import-Certificate -CertStoreLocation "cert:\LocalMachine\Root" -FilePath $rootCACerFileName

        openssl x509 -inform der -in $rootCACerFileName -out $rootCAPemFileName

        $intermediateCert1 = New-CAIntermediateCert ($_intermediateCertSubject -f "1") $rootCACert $intermediate1CAPemFileName
        $intermediateCert2 = New-CAIntermediateCert ($_intermediateCertSubject -f "2") $intermediateCert1 $intermediate2CAPemFileName
        $intermediateCert3 = New-CAIntermediateCert ($_intermediateCertSubject -f "3") $intermediateCert2 $intermediate3CAPemFileName
        Write-Host "Success"
    }    
    ```
    Ten skrypt tworzy plik o nazwie *RootCA.cer* w katalogu roboczym. 
    4. Na koniec Użyj powyższych funkcje programu PowerShell do utworzenia łańcucha certyfikatów X.509, uruchamiając polecenie `New-CACertChain` w oknie programu PowerShell. 


<a id="signverificationcode"></a>

## <a name="proof-of-possession-of-your-x509-ca-certificate"></a>Dowód przesyłany certyfikatu X.509 urzędu certyfikacji

Ten skrypt wykonuje *dowodu posiadania* przepływ dla certyfikatu X.509. 

W oknie programu PowerShell na komputerze uruchom następujący kod:
   
   ```PowerShell
   function New-CAVerificationCert([string]$requestedSubjectName)
   {
       $verifyRequestedFileName = ".\verifyCert4.cer"
       $rootCACert = Get-CACertBySubjectName $_rootCertSubject
       Write-Host "Using Signing Cert:::" 
       Write-Host $rootCACert
   
       $verifyCert = New-CASelfsignedCertificate $requestedSubjectName $rootCACert $false

       Export-Certificate -cert $verifyCert -filePath $verifyRequestedFileName -Type Cert
       if (-not (Test-Path $verifyRequestedFileName))
       {
           throw ("Error: CERT file {0} doesn't exist" -f $verifyRequestedFileName)
       }
   
       Write-Host ("Certificate with subject {0} has been output to {1}" -f $requestedSubjectName, (Join-Path (get-location).path $verifyRequestedFileName)) 
   }
   New-CAVerificationCert "<your verification code>"
   ```

Ten kod tworzy certyfikat z nazwą dany podmiot podpisany przez urząd certyfikacji jako plik o nazwie *VerifyCert4.cer* w katalogu roboczym. Ten plik certyfikatu może pomóc, używanie funkcji validate z Twojego Centrum IoT, czy masz uprawnienie podpisywania (oznacza to, że klucz prywatny) tego urzędu certyfikacji.


<a id="createx509device"></a>

## <a name="create-leaf-x509-certificate-for-your-device"></a>Utwórz certyfikat X.509 liścia dla Twojego urządzenia

W tej sekcji przedstawiono, których można użyć skryptu programu PowerShell, który pokazuje Tworzenie certyfikatu urządzenia liścia i odpowiednie łańcucha certyfikatów. 

W oknie programu PowerShell na komputerze lokalnym Uruchom następujący skrypt, aby utworzyć certyfikat X.509 podpisany przez urząd certyfikacji dla tego urządzenia:

   ```PowerShell
   function New-CADevice([string]$deviceName, [string]$signingCertSubject=$_rootCertSubject)
   {
       $newDevicePfxFileName = ("./{0}.pfx" -f $deviceName)
       $newDevicePemAllFileName      = ("./{0}-all.pem" -f $deviceName)
       $newDevicePemPrivateFileName  = ("./{0}-private.pem" -f $deviceName)
       $newDevicePemPublicFileName   = ("./{0}-public.pem" -f $deviceName)
   
       $signingCert = Get-CACertBySubjectName $signingCertSubject ## "CN=Azure IoT CA Intermediate 1 CA"

       $newDeviceCertPfx = New-CASelfSignedCertificate $deviceName $signingCert $false
   
       $certSecureStringPwd = ConvertTo-SecureString -String $_privateKeyPassword -Force -AsPlainText

       # Export the PFX of the cert we've just created.  The PFX is a format that contains both public and private keys.
       Export-PFXCertificate -cert $newDeviceCertPfx -filePath $newDevicePfxFileName -password $certSecureStringPwd
       if (-not (Test-Path $newDevicePfxFileName))
       {
           throw ("Error: CERT file {0} doesn't exist" -f $newDevicePfxFileName)
       }

       # Begin the massaging.  First, turn the PFX into a PEM file which contains public key, private key, and other attributes.
       Write-Host ("When prompted for password by openssl, enter the password as {0}" -f $_privateKeyPassword)
       openssl pkcs12 -in $newDevicePfxFileName -out $newDevicePemAllFileName -nodes

       # Convert the PEM to get formats we can process
       if ($useEcc -eq $true)
       {
           openssl ec -in $newDevicePemAllFileName -out $newDevicePemPrivateFileName
       }
       else
       {
           openssl rsa -in $newDevicePemAllFileName -out $newDevicePemPrivateFileName
       }
       openssl x509 -in $newDevicePemAllFileName -out $newDevicePemPublicFileName
 
       Write-Host ("Certificate with subject {0} has been output to {1}" -f $cnNewDeviceSubjectName, (Join-Path (get-location).path $newDevicePemPublicFileName)) 
   }
   ```

Następnie uruchom `New-CADevice "<yourTestDevice>"` w oknie programu PowerShell przy użyciu przyjaznej nazwy, który został użyty do utworzenia urządzenia. Po wyświetleniu monitu o hasło klucza prywatnego urzędu certyfikacji, wprowadź "123". Spowoduje to utworzenie  _<yourTestDevice>PFX_ pliku w katalogu roboczym.

## <a name="clean-up-certificates"></a>Czyszczenie certyfikatów

Na pasku rozpoczęcia lub **ustawienia** aplikacji, wyszukaj i wybierz **zarządzania certyfikatami komputera**. Usuń wszystkie certyfikaty wystawione przez ** TestOnly *** usługi Azure IoT urzędu certyfikacji. Te certyfikaty powinny istnieć w następujących trzech miejscach: 

* Certyfikaty — komputer lokalny > osobiste > Certyfikaty
* Certyfikaty — komputer lokalny > Zaufane główne urzędy certyfikacji > Certyfikaty
* Certyfikaty — komputer lokalny > pośrednim urzędem certyfikacji > Certyfikaty

   ![Usuń certyfikaty usługi Azure IoT urzędu certyfikacji TestOnly](./media/iot-hub-security-x509-create-certificates/cleanup.png)
