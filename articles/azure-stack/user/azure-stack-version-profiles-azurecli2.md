---
title: Nawiązywanie połączenia usługi Azure Stack przy użyciu interfejsu wiersza polecenia | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrażać zasoby w usłudze Azure Stack i zarządzać nimi za pomocą międzyplatformowego interfejsu wiersza polecenia (CLI)
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 01/24/2019
ms.openlocfilehash: 6d494cbc53ce83cfe4aaef63653eab2da9b35937
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55249947"
---
# <a name="use-api-version-profiles-with-azure-cli-in-azure-stack"></a>Profilami wersji interfejsu API za pomocą interfejsu wiersza polecenia platformy Azure w usłudze Azure Stack

Możesz wykonać kroki opisane w tym artykule, aby ustawić się interfejsu wiersza polecenia platformy Azure (CLI) do zarządzania zasobami Azure Stack Development Kit na platformach klienckich systemu Linux, Mac i Windows.

## <a name="install-cli"></a>Instalowanie interfejsu wiersza polecenia

Zaloguj się do deweloperskiej stacji roboczej i zainstalować interfejs wiersza polecenia. Usługa Azure Stack wymaga wersji 2.0 lub nowszej z wiersza polecenia platformy Azure. Tej wersji można zainstalować za pomocą procedury opisanej w [zainstalować interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) artykułu. Aby sprawdzić, czy instalacja zakończyła się pomyślnie, otwórz terminal lub okno wiersza polecenia i uruchom następujące polecenie:

```azurecli
az --version
```

Powinien zostać wyświetlony wersji wiersza polecenia platformy Azure i inne zależne biblioteki, które są zainstalowane na tym komputerze.

## <a name="trust-the-azure-stack-ca-root-certificate"></a>Traktować jako zaufany certyfikat główny urzędu usługi Azure Stack

1. Pobierz certyfikat główny urzędu usługi Azure Stack z [operator usługi Azure Stack](../azure-stack-cli-admin.md#export-the-azure-stack-ca-root-certificate) i zaufania temu certyfikatowi. Aby zaufać certyfikatowi głównemu urzędu certyfikacji w usłudze Azure Stack, należy dołączyć do istniejącego certyfikatu języka Python.

1. Znajdź lokalizację certyfikatu na komputerze. Lokalizacja mogą się różnić w zależności od tego, gdzie zainstalowano języka Python. Musisz mieć [pip](https://pip.pypa.io) i [certifi](https://pypi.org/project/certifi/) zainstalowany moduł. Służy następujące polecenie języka Python w wierszu polecenia powłoki bash:

    ```bash  
    python -c "import certifi; print(certifi.where())"
    ```

    Zanotuj lokalizację certyfikatu; na przykład `~/lib/python3.5/site-packages/certifi/cacert.pem`. Twoje konkretnej ścieżki zależy od tego, systemu operacyjnego i wersji języka Python, który został zainstalowany.

### <a name="set-the-path-for-a-development-machine-inside-the-cloud"></a>Ustaw ścieżkę dla komputera deweloperskiego z systemem w chmurze

Jeśli używasz interfejsu wiersza polecenia na komputerze systemu Linux, która została utworzona w środowisku usługi Azure Stack, uruchom poniższe polecenie powłoki bash ze ścieżką do certyfikatu.

```bash
sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
```

### <a name="set-the-path-for-a-development-machine-outside-the-cloud"></a>Ustaw ścieżkę na komputerze deweloperskim poza chmury

Jeśli używasz interfejsu wiersza polecenia na komputerze poza środowiskiem usługi Azure Stack:  

1. Konfigurowanie [połączenia sieci VPN w usłudze Azure Stack](azure-stack-connect-azure-stack.md).
1. Skopiuj certyfikatu PEM z operatora infrastruktury Azure Stack, a następnie zanotuj lokalizację pliku (PATH_TO_PEM_FILE).
1. Uruchom polecenia w poniższych sekcjach, w zależności od systemu operacyjnego na deweloperskiej stacji roboczej.

#### <a name="linux"></a>Linux

```bash
sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
```

#### <a name="macos"></a>macOS

```bash
sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
```

#### <a name="windows"></a>Windows

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

## <a name="get-the-virtual-machine-aliases-endpoint"></a>Pobieranie punktu końcowego aliasy maszyny wirtualnej

Przed utworzeniem maszyn wirtualnych przy użyciu interfejsu wiersza polecenia, należy skontaktować się z operatora infrastruktury Azure Stack i Uzyskaj identyfikator URI punktu końcowego aliasy maszyny wirtualnej. Na przykład platforma Azure używa następujący identyfikator URI: `https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json`. Administrator chmury należy skonfigurować podobne punktu końcowego usługi Azure Stack przy użyciu obrazów, które są dostępne w witrynie marketplace usługi Azure Stack. Musisz przekazać identyfikator URI punktu końcowego z `endpoint-vm-image-alias-doc` parametr `az cloud register` polecenia, jak pokazano w następnej sekcji. 
  
## <a name="connect-to-azure-stack"></a>Nawiązywanie połączenia z usługą Azure Stack

Do łączenia z usługą Azure Stack, należy użyć następujących czynności:

1. Zarejestruj środowiskiem usługi Azure Stack, uruchamiając `az cloud register` polecenia.
   
    a. Aby zarejestrować *chmury administracyjne* środowiska, użyj:

      ```azurecli
      az cloud register \ 
        -n AzureStackAdmin \ 
        --endpoint-resource-manager "https://adminmanagement.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".adminvault.local.azurestack.external" \ 
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
      ```
    b. Aby zarejestrować *użytkownika* środowiska, użyj:

      ```azurecli
      az cloud register \ 
        -n AzureStackUser \ 
        --endpoint-resource-manager "https://management.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".vault.local.azurestack.external" \ 
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
      ```
    c. Aby zarejestrować *użytkownika* w środowisku Wielodostępności, należy użyć:

      ```azurecli
      az cloud register \ 
        -n AzureStackUser \ 
        --endpoint-resource-manager "https://management.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".vault.local.azurestack.external" \ 
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases> \
        --endpoint-active-directory-resource-id=<URI of the ActiveDirectoryServiceEndpointResourceID> \
        --profile 2018-03-01-hybrid
      ```
    d. Aby zarejestrować użytkownika w środowisku usług AD FS, należy użyć:

      ```azurecli
      az cloud register \
        -n AzureStack  \
        --endpoint-resource-manager "https://management.local.azurestack.external" \
        --suffix-storage-endpoint "local.azurestack.external" \
        --suffix-keyvault-dns ".vault.local.azurestack.external"\
        --endpoint-active-directory-resource-id "https://management.adfs.azurestack.local/<tenantID>" \
        --endpoint-active-directory-graph-resource-id "https://graph.local.azurestack.external/"\
        --endpoint-active-directory "https://adfs.local.azurestack.external/adfs/"\
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases> \
        --profile "2018-03-01-hybrid"
      ```
1. Ustawianie aktywnego środowiska za pomocą następujących poleceń.
   
    a. Aby uzyskać *chmury administracyjne* środowiska, użyj:

      ```azurecli
      az cloud set \
        -n AzureStackAdmin
      ```

    b. Aby uzyskać *użytkownika* środowiska, użyj:

      ```azurecli
      az cloud set \
        -n AzureStackUser
      ```

1. Aktualizowanie konfiguracji środowiska do korzystania z określonego profilu wersji interfejsu API usługi Azure Stack. Aby zaktualizować konfigurację, uruchom następujące polecenie:

    ```azurecli
    az cloud update \
      --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >Jeśli używasz wersji usługi Azure Stack przed kompilacją 1808, należy użyć profilu wersji interfejsu API **2017-03-09-profile** zamiast profilu wersji interfejsu API **2018-03-01-hybrydowego**.

1. Zaloguj się do środowiska usługi Azure Stack przy użyciu `az login` polecenia. Możesz zalogować się do środowiska usługi Azure Stack jako użytkownik lub [nazwy głównej usługi](../../active-directory/develop/app-objects-and-service-principals.md). 

    * Środowiska usługi Azure AD
      * Zaloguj się jako *użytkownika*: Można określić nazwę użytkownika i hasło bezpośrednio w ramach `az login` polecenie lub uwierzytelniania za pomocą przeglądarki. Wykonaj te ostatnie Jeśli Twoje konto ma włączonego uwierzytelniania wieloskładnikowego:

      ```azurecli
      az login \
        -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
      ```

      > [!NOTE]
      > Jeśli Twoje konto użytkownika ma włączonego uwierzytelniania wieloskładnikowego, możesz użyć `az login command` bez podawania `-u` parametru. Uruchomienie tego polecenia zapewnia adresu URL i kodu, które muszą użyć do uwierzytelniania.
   
      * Zaloguj się jako *nazwy głównej usługi*: Przed zalogowaniem, [utworzyć nazwę główną usługi za pośrednictwem witryny Azure portal](azure-stack-create-service-principals.md) lub interfejsu wiersza polecenia i przypisz mu roli. Teraz Zaloguj się przy użyciu następującego polecenia:

      ```azurecli  
      az login \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
        --service-principal \
        -u <Application Id of the Service Principal> \
        -p <Key generated for the Service Principal>
      ```
    * Usługi AD FS środowisk

        * Zaloguj się jako użytkownik w przeglądarce sieci web przy użyciu kodu urządzenia:  
           ```azurecli  
           az login --use-device-code
           ```

           > [!NOTE]  
           >Uruchamiając polecenie udostępnia adres URL i kodu, które muszą użyć do uwierzytelniania.

        * Zaloguj się jako nazwy głównej usługi:
        
          1. Przygotuj plik PEM, który ma być używany dla logowania jednostki usługi.

            * Na komputerze klienckim, w której utworzono podmiot zabezpieczeń, eksportowanie certyfikatu nazwy głównej usługi, ponieważ pfx z kluczem prywatnym, który znajduje się w `cert:\CurrentUser\My`; certyfikatu, nazwa ma taką samą nazwę jak podmiot zabezpieczeń.
        
            * Konwertuj plik pfx na pem (Użyj narzędzia biblioteki OpenSSL).

          2.  Zaloguj się do interfejsu wiersza polecenia:
            ```azurecli  
            az login --service-principal \
              -u <Client ID from the Service Principal details> \
              -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
              --tenant <Tenant ID> \
              --debug 
            ```

## <a name="test-the-connectivity"></a>Testowanie łączności

Wszystko, konfigurowanie i korzystanie z interfejsu wiersza polecenia do tworzenia zasobów w ramach usługi Azure Stack. Na przykład można utworzyć grupę zasobów dla aplikacji i Dodaj maszynę wirtualną. Aby utworzyć grupę zasobów o nazwie "MyResourceGroup", użyj następującego polecenia:

```azurecli
az group create \
  -n MyResourceGroup -l local
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