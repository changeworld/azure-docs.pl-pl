---
title: Nawiązywanie połączenia usługi Azure Stack przy użyciu interfejsu wiersza polecenia | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrażać zasoby w usłudze Azure Stack i zarządzać nimi za pomocą międzyplatformowego interfejsu wiersza polecenia (CLI)
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 02/28/2019
ms.openlocfilehash: fa1731c9361be83949aa794ed8842681bd81d995
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59057782"
---
# <a name="use-api-version-profiles-with-azure-cli-in-azure-stack"></a>Profilami wersji interfejsu API za pomocą interfejsu wiersza polecenia platformy Azure w usłudze Azure Stack

*Dotyczy Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Możesz wykonać kroki opisane w tym artykule, aby ustawić się interfejsu wiersza polecenia platformy Azure (CLI) do zarządzania zasobami usługi Azure Stack Development Kit (ASDK) na platformach klienckich systemu Linux, Mac i Windows.

## <a name="prepare-for-azure-cli"></a>Przygotowanie do wiersza polecenia platformy Azure

Konieczne będzie certyfikat główny urzędu certyfikacji dla usługi Azure Stack użyć wiersza polecenia platformy Azure na maszynie deweloperskiej. Certyfikat umożliwia zarządzanie zasobami za pomocą interfejsu wiersza polecenia.

 - **Certyfikat główny urzędu certyfikacji usługi Azure Stack** jest wymagany, jeśli używasz interfejsu wiersza polecenia na stacji roboczej poza ASDK.  

 - **Punkt końcowy aliasy maszyny wirtualnej** zawiera alias, takich jak "UbuntuLTS" lub "Win2012Datacenter", który odwołuje się do wydawcy obrazu, oferty, jednostki SKU i wersji jako pojedynczy parametr podczas wdrażania maszyn wirtualnych.  

W następujących sekcjach opisano sposób uzyskiwania tych wartości.

### <a name="export-the-azure-stack-ca-root-certificate"></a>Wyeksportuj certyfikat głównego urzędu certyfikacji usługi Azure Stack

Jeśli używasz zintegrowanego systemu, nie trzeba wyeksportować certyfikat główny urzędu certyfikacji. Należy wyeksportować certyfikat główny urzędu certyfikacji na ASDK.

Aby wyeksportować certyfikat główny ASDK w formacie PEM:

1. [Utwórz maszynę Wirtualną Windows w usłudze Azure Stack](azure-stack-quick-windows-portal.md).

2. Zaloguj się do maszyny, otwórz wiersz PowerShell z podwyższonym poziomem uprawnień, a następnie uruchom następujący skrypt:

    ```powershell  
      $label = "AzureStackSelfSignedRootCert"
      Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
      $root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
      if (-not $root)
      {
          Write-Error "Certificate with subject CN=$label not found"
          return
      }

    Write-Host "Exporting certificate"
    Export-Certificate -Type CERT -FilePath root.cer -Cert $root

    Write-Host "Converting certificate to PEM format"
    certutil -encode root.cer root.pem
    ```

3. Skopiuj certyfikatu na komputerze lokalnym.


### <a name="set-up-the-virtual-machine-aliases-endpoint"></a>Konfigurowanie punktu końcowego aliasy maszyny wirtualnej

Można skonfigurować publicznie dostępnym punkcie końcowym hostujący plik alias maszyny wirtualnej. Plik alias maszyny wirtualnej znajduje się plik w formacie JSON, który zawiera nazwę pospolitą obrazu. Nazwa będzie używana, gdy wdrożysz maszynę Wirtualną jako parametr wiersza polecenia platformy Azure.

1. Jeśli publikujesz niestandardowego obrazu, zanotuj informacje o wydawcy, oferty, jednostki SKU i wersji, które zostały określone podczas publikowania. Jeśli jest to obraz z witryny marketplace, można wyświetlić informacje przy użyciu ```Get-AzureVMImage``` polecenia cmdlet.  

2. Pobierz [przykładowy plik](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) z usługi GitHub.

3. Utwórz konto magazynu w usłudze Azure Stack. Gdy zostanie to zrobione, Utwórz kontener obiektów blob. Ustawienie zasad dostępu do "publicznej".  

4. Przekaż plik JSON do nowego kontenera. Gdy zostanie to zrobione, możesz wyświetlić adres URL obiektu blob. Wybierz nazwę obiektu blob, a następnie wybierając adres URL, z właściwości obiektu blob.

### <a name="install-or-upgrade-cli"></a>Zainstalowanie lub uaktualnienie interfejsu wiersza polecenia

Zaloguj się do deweloperskiej stacji roboczej i zainstalować interfejs wiersza polecenia. Usługa Azure Stack wymaga wersji 2.0 lub nowszej z wiersza polecenia platformy Azure. Najnowszą wersję profilami interfejsu API wymaga bieżącą wersję interfejsu wiersza polecenia.  Interfejs wiersza polecenia można zainstalować za pomocą procedury opisanej w [zainstalować interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) artykułu. Aby sprawdzić, czy instalacja zakończyła się pomyślnie, otwórz terminal lub okno wiersza polecenia i uruchom następujące polecenie:

```shell
az --version
```

Powinien zostać wyświetlony wersji wiersza polecenia platformy Azure i inne zależne biblioteki, które są zainstalowane na tym komputerze.

### <a name="install-python-on-windows"></a>Instalowanie języka Python w Windows

1. Zainstaluj [Python 3 w Twoim systemie](https://www.python.org/downloads/).

2. Uaktualnić pakiet PIP. Narzędzie PIP to Menedżer pakietów dla języka Python. Otwórz wiersz polecenia lub wiersz PowerShell i wpisz następujące polecenie:

    ```powershell  
    python -m pip install --upgrade pip
    ```

3. Zainstaluj **certifi** modułu. [Certifi](https://pypi.org/project/certifi/) jest modułem i kolekcji certyfikatów głównych sprawdzania poprawności wiarygodności certyfikatów protokołu SSL podczas weryfikowania tożsamości hostów TLS. Otwórz wiersz polecenia lub wiersz PowerShell i wpisz następujące polecenie:

    ```powershell
    pip install certifi
    ```

### <a name="install-python-on-linux"></a>Zainstaluj język Python w systemie Linux

1. Obraz Ubuntu 16.04 jest dostarczany za pomocą języka Python 2.7 i język Python 3.5 instalowane domyślnie. Można sprawdzić wersji 3 języka Python, uruchamiając następujące polecenie:

    ```bash  
    python3 --version
    ```

2. Uaktualnić pakiet PIP. Narzędzie PIP to Menedżer pakietów dla języka Python. Otwórz wiersz polecenia lub wiersz PowerShell i wpisz następujące polecenie:

    ```bash  
    sudo -H pip3 install --upgrade pip
    ```

3. Zainstaluj **certifi** modułu. [Certifi](https://pypi.org/project/certifi/) to zbiór certyfikaty główne sprawdzania poprawności wiarygodności certyfikatów protokołu SSL podczas weryfikowania tożsamości hostów TLS. Otwórz wiersz polecenia lub wiersz PowerShell i wpisz następujące polecenie:

    ```bash
    pip3 install certifi
    ```

### <a name="install-python-on-macos"></a>Zainstaluj język Python w systemie macOS

1. Zainstaluj [Python 3 w Twoim systemie](https://www.python.org/downloads/). Dla wersji Python 3.7 Python.org są dostępne dwie opcje Instalatora binarne do pobrania. Wariant domyślny jest 64-bit — tylko i działa w systemie macOS 10.9 (Mavericks) i nowszych systemów. Sprawdź swoją wersję języka python, otwierając terminal i wpisując następujące polecenie:

    ```bash  
    python3 --version
    ```

2. Uaktualnić pakiet PIP. Narzędzie PIP to Menedżer pakietów dla języka Python. Otwórz wiersz polecenia lub wiersz PowerShell i wpisz następujące polecenie:

    ```bash  
    sudo -H pip3 install --upgrade pip
    ```

3. Zainstaluj **certifi** modułu. [Certifi](https://pypi.org/project/certifi/) jest modułem i kolekcji certyfikatów głównych sprawdzania poprawności wiarygodności certyfikatów protokołu SSL podczas weryfikowania tożsamości hostów TLS. Otwórz wiersz polecenia lub wiersz PowerShell i wpisz następujące polecenie:

    ```bash
    pip3 install certifi
    ```

## <a name="windows-azure-ad"></a>Windows (Azure AD)

Ta sekcja przeprowadzi Cię konfigurowania interfejsu wiersza polecenia Jeśli używasz usługi Azure AD jako usługi zarządzania tożsamościami i przy użyciu interfejsu wiersza polecenia na komputerze Windows.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Traktować jako zaufany certyfikat główny urzędu usługi Azure Stack

Jeśli używasz ASDK należy traktować jako zaufany certyfikat główny urzędu certyfikacji na maszynie zdalnej. Nie należy to zrobić przy użyciu zintegrowanych systemów.

Aby zaufać certyfikatowi głównemu urzędu certyfikacji w usłudze Azure Stack, należy dołączyć do istniejącego certyfikatu języka Python.

1. Znajdź lokalizację certyfikatu na komputerze. Lokalizacja mogą się różnić w zależności od tego, gdzie zainstalowano języka Python. Otwórz wiersz polecenia lub wiersz PowerShell i wpisz następujące polecenie:

    ```powershell  
      python -c "import certifi; print(certifi.where())"
    ```

    Zanotuj lokalizację certyfikatu. Na przykład `~/lib/python3.5/site-packages/certifi/cacert.pem`. Konkretnej ścieżki zależy od Twojego systemu operacyjnego i wersji języka Python, który został zainstalowany.

2. Zaufanie certyfikatu głównego urzędu certyfikacji w usłudze Azure Stack przez dołączenie jej do istniejącego certyfikatu języka Python.

    ```powershell
    $pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
    $root.Import($pemFile)

    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry    = [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

    Write-Host "Python Cert store was updated to allow the Azure Stack CA root certificate"
    ```

### <a name="connect-to-azure-stack"></a>Nawiązywanie połączenia z usługą Azure Stack

1. Zarejestruj środowiskiem usługi Azure Stack, uruchamiając `az cloud register` polecenia.

    W niektórych scenariuszach bezpośrednie połączenie z Internetem wychodzącego odbywa się za pośrednictwem serwera proxy lub zapory, która wymusza przejmowanie protokołu SSL. W takich przypadkach `az cloud register` polecenia może zakończyć się niepowodzeniem z powodu błędu, takie jak "Nie można pobrać punkty końcowe w chmurze." Aby obejść ten błąd, można ustawić następujące zmienne środowiskowe:

    ```shell  
    set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
    set ADAL_PYTHON_SSL_NO_VERIFY=1
    ```

2. Rejestrowanie środowiska. Użyj następujących parametrów podczas uruchamiania `az cloud register`.

    | Wartość | Przykład | Opis |
    | --- | --- | --- |
    | Nazwa środowiska | AzureStackUser | Użyj `AzureStackUser` w środowisku użytkownika. Jeśli operator, określić `AzureStackAdmin`. |
    | Punkt końcowy usługi Resource Manager | https://management.local.azurestack.external | **ResourceManagerUrl** jest w usłudze Azure Stack Development Kit (ASDK): `https://management.local.azurestack.external/` **ResourceManagerUrl** w systemach zintegrowanych jest: `https://management.<region>.<fqdn>/` Aby pobrać metadane wymagane: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` Jeśli masz pytania dotyczące punktu końcowego zintegrowanego systemu, skontaktuj się z operatorem chmury. |
    | Punkt końcowy magazynu | local.azurestack.external | `local.azurestack.external` Służy do ASDK. Zintegrowany system należy użyć punktu końcowego w systemie.  |
    | Sufiks Keyvalut | . vault.local.azurestack.external | `.vault.local.azurestack.external` Służy do ASDK. Zintegrowany system należy użyć punktu końcowego w systemie.  |
    | Obraz maszyny Wirtualnej alias doc punktu końcowego — | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | Identyfikator URI dokumentu, który zawiera aliasy obrazu maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [### konfigurowania punktu końcowego maszyny wirtualnej aliasy](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

1. Ustawianie aktywnego środowiska za pomocą następujących poleceń.

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. Aktualizowanie konfiguracji środowiska do korzystania z określonego profilu wersji interfejsu API usługi Azure Stack. Aby zaktualizować konfigurację, uruchom następujące polecenie:

    ```azurecli
    az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >Jeśli używasz wersji usługi Azure Stack przed kompilacją 1808, należy użyć profilu wersji interfejsu API **2017-03-09-profile** zamiast profilu wersji interfejsu API **2018-03-01-hybrydowego**. Należy używać najnowszej wersji interfejsu wiersza polecenia platformy Azure.
 
1. Zaloguj się do środowiska usługi Azure Stack przy użyciu `az login` polecenia. Możesz zalogować się do środowiska usługi Azure Stack jako użytkownik lub [nazwy głównej usługi](../../active-directory/develop/app-objects-and-service-principals.md). 

   - Zaloguj się jako *użytkownika*: 

     Można określić nazwę użytkownika i hasło bezpośrednio w ramach `az login` polecenie lub uwierzytelniania za pomocą przeglądarki. Wykonaj te ostatnie Jeśli Twoje konto ma włączonego uwierzytelniania wieloskładnikowego:

     ```azurecli
     az login -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > Jeśli Twoje konto użytkownika ma włączonego uwierzytelniania wieloskładnikowego, możesz użyć `az login` polecenia bez podawania `-u` parametru. Uruchomienie tego polecenia zapewnia adresu URL i kodu, które muszą użyć do uwierzytelniania.

   - Zaloguj się jako *nazwy głównej usługi*: 
    
     Przed zalogowaniem, [utworzyć nazwę główną usługi za pośrednictwem witryny Azure portal](azure-stack-create-service-principals.md) lub interfejsu wiersza polecenia i przypisz mu roli. Teraz Zaloguj się przy użyciu następującego polecenia:

     ```azurecli  
     az login --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> --service-principal -u <Application Id of the Service Principal> -p <Key generated for the Service Principal>
     ```

### <a name="test-the-connectivity"></a>Testowanie łączności

Wszystko, konfigurowanie i korzystanie z interfejsu wiersza polecenia do tworzenia zasobów w ramach usługi Azure Stack. Na przykład można utworzyć grupę zasobów dla aplikacji i Dodaj maszynę wirtualną. Aby utworzyć grupę zasobów o nazwie "MyResourceGroup", użyj następującego polecenia:

```azurecli
az group create -n MyResourceGroup -l local
```

Jeśli grupa zasobów została utworzona pomyślnie, poprzednie polecenie wyświetla następujące właściwości nowo utworzonego zasobu:

![Tworzenie grupy zasobów danych wyjściowych](media/azure-stack-connect-cli/image1.png)

## <a name="windows-ad-fs"></a>Windows (AD FS)

Ta sekcja przeprowadzi Cię konfigurowania interfejsu wiersza polecenia Jeśli używasz Active Directory federacyjnego Services (AD FS) jako usługi zarządzania tożsamościami i przy użyciu interfejsu wiersza polecenia na komputerze Windows.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Traktować jako zaufany certyfikat główny urzędu usługi Azure Stack

Jeśli używasz ASDK należy traktować jako zaufany certyfikat główny urzędu certyfikacji na maszynie zdalnej. Nie należy to zrobić przy użyciu zintegrowanych systemów.

1. Znajdź lokalizację certyfikatu na komputerze. Lokalizacja mogą się różnić w zależności od tego, gdzie zainstalowano języka Python. Otwórz wiersz polecenia lub wiersz PowerShell i wpisz następujące polecenie:

    ```powershell  
      python -c "import certifi; print(certifi.where())"
    ```

    Zanotuj lokalizację certyfikatu. Na przykład `~/lib/python3.5/site-packages/certifi/cacert.pem`. Konkretnej ścieżki zależy od Twojego systemu operacyjnego i wersji języka Python, który został zainstalowany.

2. Zaufanie certyfikatu głównego urzędu certyfikacji w usłudze Azure Stack przez dołączenie jej do istniejącego certyfikatu języka Python.

    ```powershell
    $pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
    $root.Import($pemFile)

    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry    = [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

    Write-Host "Python Cert store was updated to allow the Azure Stack CA root certificate"
    ```

### <a name="connect-to-azure-stack"></a>Nawiązywanie połączenia z usługą Azure Stack

1. Zarejestruj środowiskiem usługi Azure Stack, uruchamiając `az cloud register` polecenia.

    W niektórych scenariuszach bezpośrednie połączenie z Internetem wychodzącego odbywa się za pośrednictwem serwera proxy lub zapory, która wymusza przejmowanie protokołu SSL. W takich przypadkach `az cloud register` polecenia może zakończyć się niepowodzeniem z powodu błędu, takie jak "Nie można pobrać punkty końcowe w chmurze." Aby obejść ten błąd, można ustawić następujące zmienne środowiskowe:

    ```shell  
    set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
    set ADAL_PYTHON_SSL_NO_VERIFY=1
    ```

2. Rejestrowanie środowiska. Użyj następujących parametrów podczas uruchamiania `az cloud register`.

    | Wartość | Przykład | Opis |
    | --- | --- | --- |
    | Nazwa środowiska | AzureStackUser | Użyj `AzureStackUser` w środowisku użytkownika. Jeśli operator, określić `AzureStackAdmin`. |
    | Punkt końcowy usługi Resource Manager | https://management.local.azurestack.external | **ResourceManagerUrl** jest w usłudze Azure Stack Development Kit (ASDK): `https://management.local.azurestack.external/` **ResourceManagerUrl** w systemach zintegrowanych jest: `https://management.<region>.<fqdn>/` Aby pobrać metadane wymagane: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` Jeśli masz pytania dotyczące punktu końcowego zintegrowanego systemu, skontaktuj się z operatorem chmury. |
    | Punkt końcowy magazynu | local.azurestack.external | `local.azurestack.external` Służy do ASDK. Zintegrowany system należy użyć punktu końcowego w systemie.  |
    | Sufiks Keyvalut | . vault.local.azurestack.external | `.vault.local.azurestack.external` Służy do ASDK. Zintegrowany system należy użyć punktu końcowego w systemie.  |
    | Obraz maszyny Wirtualnej alias doc punktu końcowego — | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | Identyfikator URI dokumentu, który zawiera aliasy obrazu maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [### konfigurowania punktu końcowego maszyny wirtualnej aliasy](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

1. Ustawianie aktywnego środowiska za pomocą następujących poleceń.

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. Aktualizowanie konfiguracji środowiska do korzystania z określonego profilu wersji interfejsu API usługi Azure Stack. Aby zaktualizować konfigurację, uruchom następujące polecenie:

    ```azurecli
    az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >Jeśli używasz wersji usługi Azure Stack przed kompilacją 1808, należy użyć profilu wersji interfejsu API **2017-03-09-profile** zamiast profilu wersji interfejsu API **2018-03-01-hybrydowego**. Należy używać najnowszej wersji interfejsu wiersza polecenia platformy Azure.

1. Zaloguj się do środowiska usługi Azure Stack przy użyciu `az login` polecenia. Możesz zalogować się do środowiska usługi Azure Stack jako użytkownik lub [nazwy głównej usługi](../../active-directory/develop/app-objects-and-service-principals.md). 

   - Zaloguj się jako *użytkownika*:

     Można określić nazwę użytkownika i hasło bezpośrednio w ramach `az login` polecenie lub uwierzytelniania za pomocą przeglądarki. Wykonaj te ostatnie Jeśli Twoje konto ma włączonego uwierzytelniania wieloskładnikowego:

     ```azurecli
     az cloud register  -n <environmentname>   --endpoint-resource-manager "https://management.local.azurestack.external"  --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-active-directory-resource-id "https://management.adfs.azurestack.local/<tenantID>" --endpoint-active-directory-graph-resource-id "https://graph.local.azurestack.external/" --endpoint-active-directory "https://adfs.local.azurestack.external/adfs/" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>   --profile "2018-03-01-hybrid"
     ```

     > [!NOTE]
     > Jeśli Twoje konto użytkownika ma włączonego uwierzytelniania wieloskładnikowego, możesz użyć `az login` polecenia bez podawania `-u` parametru. Uruchomienie tego polecenia zapewnia adresu URL i kodu, które muszą użyć do uwierzytelniania.

   - Zaloguj się jako *nazwy głównej usługi*: 
    
     Przygotuj plik PEM, który ma być używany dla logowania jednostki usługi.

     Na komputerze klienckim, w której utworzono podmiot zabezpieczeń, eksportowanie certyfikatu nazwy głównej usługi, ponieważ pfx z kluczem prywatnym, który znajduje się w `cert:\CurrentUser\My`; certyfikatu, nazwa ma taką samą nazwę jak podmiot zabezpieczeń.

     Konwertuj plik pfx na pem (Użyj narzędzia biblioteki OpenSSL).

     Zaloguj się do interfejsu wiersza polecenia:
  
     ```azurecli  
     az login --service-principal \
      -u <Client ID from the Service Principal details> \
      -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
      --tenant <Tenant ID> \
      --debug 
     ```

### <a name="test-the-connectivity"></a>Testowanie łączności

Wszystko, konfigurowanie i korzystanie z interfejsu wiersza polecenia do tworzenia zasobów w ramach usługi Azure Stack. Na przykład można utworzyć grupę zasobów dla aplikacji i Dodaj maszynę wirtualną. Aby utworzyć grupę zasobów o nazwie "MyResourceGroup", użyj następującego polecenia:

```azurecli
az group create -n MyResourceGroup -l local
```

Jeśli grupa zasobów została utworzona pomyślnie, poprzednie polecenie wyświetla następujące właściwości nowo utworzonego zasobu:

![Tworzenie grupy zasobów danych wyjściowych](media/azure-stack-connect-cli/image1.png)


## <a name="linux-azure-ad"></a>Linux (Azure AD)

Ta sekcja przeprowadzi Cię konfigurowania interfejsu wiersza polecenia Jeśli używasz usługi Azure AD jako usługi zarządzania tożsamościami i przy użyciu interfejsu wiersza polecenia na maszynie z systemem Linux.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Traktować jako zaufany certyfikat główny urzędu usługi Azure Stack

Jeśli używasz ASDK należy traktować jako zaufany certyfikat główny urzędu certyfikacji na maszynie zdalnej. Nie należy to zrobić przy użyciu zintegrowanych systemów.

Zaufanie certyfikatu głównego urzędu certyfikacji w usłudze Azure Stack przez dołączenie jej do istniejącego certyfikatu języka Python.

1. Znajdź lokalizację certyfikatu na komputerze. Lokalizacja mogą się różnić w zależności od tego, gdzie zainstalowano języka Python. Musisz mieć narzędzia pip i certifi [zainstalowany moduł](#install-python-on-linux). Służy następujące polecenie języka Python w wierszu polecenia powłoki bash:

    ```bash  
    python3 -c "import certifi; print(certifi.where())"
    ```

    Zanotuj lokalizację certyfikatu; na przykład `~/lib/python3.5/site-packages/certifi/cacert.pem`. Twoje konkretnej ścieżki zależy od tego, systemu operacyjnego i wersji języka Python, który został zainstalowany.

2. Uruchom poniższe polecenie powłoki bash ze ścieżką do certyfikatu.

   - Dla zdalnego maszyny z systemem Linux:

     ```bash  
     sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
     ```

   - Dla maszyny z systemem Linux w środowisku usługi Azure Stack:

     ```bash  
     sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
     ```

### <a name="connect-to-azure-stack"></a>Nawiązywanie połączenia z usługą Azure Stack

Do łączenia z usługą Azure Stack, należy użyć następujących czynności:

1. Zarejestruj środowiskiem usługi Azure Stack, uruchamiając `az cloud register` polecenia. W niektórych scenariuszach bezpośrednie połączenie z Internetem wychodzącego odbywa się za pośrednictwem serwera proxy lub zapory, która wymusza przejmowanie protokołu SSL. W takich przypadkach `az cloud register` polecenia może zakończyć się niepowodzeniem z powodu błędu, takie jak "Nie można pobrać punkty końcowe w chmurze." Aby obejść ten błąd, można ustawić następujące zmienne środowiskowe:

   ```shell
   set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
   set ADAL_PYTHON_SSL_NO_VERIFY=1
   ```

2. Rejestrowanie środowiska. Użyj następujących parametrów podczas uruchamiania `az cloud register`.

    | Wartość | Przykład | Opis |
    | --- | --- | --- |
    | Nazwa środowiska | AzureStackUser | Użyj `AzureStackUser` w środowisku użytkownika. Jeśli operator, określić `AzureStackAdmin`. |
    | Punkt końcowy usługi Resource Manager | https://management.local.azurestack.external | **ResourceManagerUrl** jest w usłudze Azure Stack Development Kit (ASDK): `https://management.local.azurestack.external/` **ResourceManagerUrl** w systemach zintegrowanych jest: `https://management.<region>.<fqdn>/` Aby pobrać metadane wymagane: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` Jeśli masz pytania dotyczące punktu końcowego zintegrowanego systemu, skontaktuj się z operatorem chmury. |
    | Punkt końcowy magazynu | local.azurestack.external | `local.azurestack.external` Służy do ASDK. Zintegrowany system należy użyć punktu końcowego w systemie.  |
    | Sufiks Keyvalut | . vault.local.azurestack.external | `.vault.local.azurestack.external` Służy do ASDK. Zintegrowany system należy użyć punktu końcowego w systemie.  |
    | Obraz maszyny Wirtualnej alias doc punktu końcowego — | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | Identyfikator URI dokumentu, który zawiera aliasy obrazu maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [### konfigurowania punktu końcowego maszyny wirtualnej aliasy](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

3. Ustawianie aktywnego środowiska. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. Aktualizowanie konfiguracji środowiska do korzystania z określonego profilu wersji interfejsu API usługi Azure Stack. Aby zaktualizować konfigurację, uruchom następujące polecenie:

    ```azurecli
      az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >Jeśli używasz wersji usługi Azure Stack przed kompilacją 1808, należy użyć profilu wersji interfejsu API **2017-03-09-profile** zamiast profilu wersji interfejsu API **2018-03-01-hybrydowego**. Należy używać najnowszej wersji interfejsu wiersza polecenia platformy Azure.

5. Zaloguj się do środowiska usługi Azure Stack przy użyciu `az login` polecenia. Możesz zalogować się do środowiska usługi Azure Stack jako użytkownik lub [nazwy głównej usługi](../../active-directory/develop/app-objects-and-service-principals.md). 

   * Zaloguj się jako *użytkownika*:

     Można określić nazwę użytkownika i hasło bezpośrednio w ramach `az login` polecenie lub uwierzytelniania za pomocą przeglądarki. Wykonaj te ostatnie Jeśli Twoje konto ma włączonego uwierzytelniania wieloskładnikowego:

     ```azurecli
     az login \
       -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
       --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > Jeśli Twoje konto użytkownika ma włączonego uwierzytelniania wieloskładnikowego, możesz użyć `az login` polecenia bez podawania `-u` parametru. Uruchomienie tego polecenia zapewnia adresu URL i kodu, które muszą użyć do uwierzytelniania.
   
   * Zaloguj się jako *nazwy głównej usługi*
    
     Przed zalogowaniem, [utworzyć nazwę główną usługi za pośrednictwem witryny Azure portal](azure-stack-create-service-principals.md) lub interfejsu wiersza polecenia i przypisz mu roli. Teraz Zaloguj się przy użyciu następującego polecenia:

     ```azurecli  
     az login \
       --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
       --service-principal \
       -u <Application Id of the Service Principal> \
       -p <Key generated for the Service Principal>
     ```

### <a name="test-the-connectivity"></a>Testowanie łączności

Wszystko, konfigurowanie i korzystanie z interfejsu wiersza polecenia do tworzenia zasobów w ramach usługi Azure Stack. Na przykład można utworzyć grupę zasobów dla aplikacji i Dodaj maszynę wirtualną. Aby utworzyć grupę zasobów o nazwie "MyResourceGroup", użyj następującego polecenia:

```azurecli
    az group create -n MyResourceGroup -l local
```

Jeśli grupa zasobów została utworzona pomyślnie, poprzednie polecenie wyświetla następujące właściwości nowo utworzonego zasobu:

![Tworzenie grupy zasobów danych wyjściowych](media/azure-stack-connect-cli/image1.png)

## <a name="linux-ad-fs"></a>Linux (AD FS)

Ta sekcja przeprowadzi Cię konfigurowania interfejsu wiersza polecenia jeśli korzystają jako usługi zarządzania Active Directory federacyjnego Services (AD FS), a przy użyciu interfejsu wiersza polecenia na maszynie z systemem Linux.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Traktować jako zaufany certyfikat główny urzędu usługi Azure Stack

Jeśli używasz ASDK należy traktować jako zaufany certyfikat główny urzędu certyfikacji na maszynie zdalnej. Nie należy to zrobić przy użyciu zintegrowanych systemów.

Zaufanie certyfikatu głównego urzędu certyfikacji w usłudze Azure Stack przez dołączenie jej do istniejącego certyfikatu języka Python.

1. Znajdź lokalizację certyfikatu na komputerze. Lokalizacja mogą się różnić w zależności od tego, gdzie zainstalowano języka Python. Musisz mieć narzędzia pip i certifi [zainstalowany moduł](#install-python-on-linux). Służy następujące polecenie języka Python w wierszu polecenia powłoki bash:

    ```bash  
    python3 -c "import certifi; print(certifi.where())"
    ```

    Zanotuj lokalizację certyfikatu; na przykład `~/lib/python3.5/site-packages/certifi/cacert.pem`. Twoje konkretnej ścieżki zależy od tego, systemu operacyjnego i wersji języka Python, który został zainstalowany.

2. Uruchom poniższe polecenie powłoki bash ze ścieżką do certyfikatu.

   - Dla zdalnego maszyny z systemem Linux:

     ```bash  
     sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
     ```

   - Dla maszyny z systemem Linux w środowisku usługi Azure Stack:

     ```bash  
     sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
     ```

### <a name="connect-to-azure-stack"></a>Nawiązywanie połączenia z usługą Azure Stack

Do łączenia z usługą Azure Stack, należy użyć następujących czynności:

1. Zarejestruj środowiskiem usługi Azure Stack, uruchamiając `az cloud register` polecenia. W niektórych scenariuszach bezpośrednie połączenie z Internetem wychodzącego odbywa się za pośrednictwem serwera proxy lub zapory, która wymusza przejmowanie protokołu SSL. W takich przypadkach `az cloud register` polecenia może zakończyć się niepowodzeniem z powodu błędu, takie jak "Nie można pobrać punkty końcowe w chmurze." Aby obejść ten błąd, można ustawić następujące zmienne środowiskowe:

   ```shell
   set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
   set ADAL_PYTHON_SSL_NO_VERIFY=1
   ```

2. Rejestrowanie środowiska. Użyj następujących parametrów podczas uruchamiania `az cloud register`.

    | Wartość | Przykład | Opis |
    | --- | --- | --- |
    | Nazwa środowiska | AzureStackUser | Użyj `AzureStackUser` w środowisku użytkownika. Jeśli operator, określić `AzureStackAdmin`. |
    | Punkt końcowy usługi Resource Manager | https://management.local.azurestack.external | **ResourceManagerUrl** jest w usłudze Azure Stack Development Kit (ASDK): `https://management.local.azurestack.external/` **ResourceManagerUrl** w systemach zintegrowanych jest: `https://management.<region>.<fqdn>/` Aby pobrać metadane wymagane: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` Jeśli masz pytania dotyczące punktu końcowego zintegrowanego systemu, skontaktuj się z operatorem chmury. |
    | Punkt końcowy magazynu | local.azurestack.external | `local.azurestack.external` Służy do ASDK. Zintegrowany system należy użyć punktu końcowego w systemie.  |
    | Sufiks Keyvalut | . vault.local.azurestack.external | `.vault.local.azurestack.external` Służy do ASDK. Zintegrowany system należy użyć punktu końcowego w systemie.  |
    | Obraz maszyny Wirtualnej alias doc punktu końcowego — | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | Identyfikator URI dokumentu, który zawiera aliasy obrazu maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [### konfigurowania punktu końcowego maszyny wirtualnej aliasy](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

3. Ustawianie aktywnego środowiska. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. Aktualizowanie konfiguracji środowiska do korzystania z określonego profilu wersji interfejsu API usługi Azure Stack. Aby zaktualizować konfigurację, uruchom następujące polecenie:

    ```azurecli
      az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >Jeśli używasz wersji usługi Azure Stack przed kompilacją 1808, należy użyć profilu wersji interfejsu API **2017-03-09-profile** zamiast profilu wersji interfejsu API **2018-03-01-hybrydowego**. Należy używać najnowszej wersji interfejsu wiersza polecenia platformy Azure.

5. Zaloguj się do środowiska usługi Azure Stack przy użyciu `az login` polecenia. Możesz zalogować się do środowiska usługi Azure Stack jako użytkownik lub [nazwy głównej usługi](../../active-directory/develop/app-objects-and-service-principals.md). 

6. Rejestrowanie: 

   *  Jako **użytkownika** w przeglądarce sieci web przy użyciu kodu urządzenia:  

   ```azurecli  
    az login --use-device-code
   ```

   > [!NOTE]  
   >Uruchamiając polecenie udostępnia adres URL i kodu, które muszą użyć do uwierzytelniania.

   * Jako nazwę główną usługi:
        
     Przygotuj plik PEM, który ma być używany dla logowania jednostki usługi.

      * Na komputerze klienckim, w której utworzono podmiot zabezpieczeń, eksportowanie certyfikatu nazwy głównej usługi, ponieważ pfx z kluczem prywatnym, który znajduje się w `cert:\CurrentUser\My`; certyfikatu, nazwa ma taką samą nazwę jak podmiot zabezpieczeń.
  
      * Konwertuj plik pfx na pem (Użyj narzędzia biblioteki OpenSSL).

     Zaloguj się do interfejsu wiersza polecenia:

      ```azurecli  
      az login --service-principal \
        -u <Client ID from the Service Principal details> \
        -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
        --tenant <Tenant ID> \
        --debug 
      ```

### <a name="test-the-connectivity"></a>Testowanie łączności

Wszystko, konfigurowanie i korzystanie z interfejsu wiersza polecenia do tworzenia zasobów w ramach usługi Azure Stack. Na przykład można utworzyć grupę zasobów dla aplikacji i Dodaj maszynę wirtualną. Aby utworzyć grupę zasobów o nazwie "MyResourceGroup", użyj następującego polecenia:

```azurecli
  az group create -n MyResourceGroup -l local
```

Jeśli grupa zasobów została utworzona pomyślnie, poprzednie polecenie wyświetla następujące właściwości nowo utworzonego zasobu:

![Tworzenie grupy zasobów danych wyjściowych](media/azure-stack-connect-cli/image1.png)

## <a name="known-issues"></a>Znane problemy

Istnieją znane problemy przy użyciu interfejsu wiersza polecenia w usłudze Azure Stack:

 - Tryb interaktywny interfejsu wiersza polecenia; na przykład `az interactive` polecenia, nie jest jeszcze obsługiwane w usłudze Azure Stack.
 - Aby uzyskać listę obrazów maszyn wirtualnych są dostępne w usłudze Azure Stack, użyj `az vm image list --all` polecenia zamiast `az vm image list` polecenia. Określanie `--all` opcja gwarantuje, że odpowiedzi zwraca tylko obrazy, które są dostępne w danym środowisku usługi Azure Stack.
 - Aliasy obrazu maszyny wirtualnej, które są dostępne na platformie Azure nie może być stosowane do usługi Azure Stack. Korzystając z obrazów maszyn wirtualnych, należy użyć całego parametru URN (Canonical: UbuntuServer:14.04.3-LTS:1.0.0) zamiast alias obrazu. Ta URN musi odpowiadać specyfikacji obrazu wyprowadzana z `az vm images list` polecenia.

## <a name="next-steps"></a>Kolejne kroki

- [Wdrażanie szablonów za pomocą wiersza polecenia platformy Azure](azure-stack-deploy-template-command-line.md)
- [Włączanie interfejsu wiersza polecenia platformy Azure dla użytkowników usługi Azure Stack (Operator)](../azure-stack-cli-admin.md)
- [Zarządzanie uprawnieniami użytkowników](azure-stack-manage-permissions.md) 
