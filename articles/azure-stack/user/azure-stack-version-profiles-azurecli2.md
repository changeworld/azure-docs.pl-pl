---
title: Połącz Azure stos z interfejsu wiersza polecenia | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać do zarządzania i wdrażania zasobów na stosie Azure międzyplatformowego interfejsu wiersza polecenia (CLI)
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/25/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: eb01d31d00177560aca3aa71750cd2d1ec096f8f
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938449"
---
# <a name="use-api-version-profiles-with-azure-cli-20-in-azure-stack"></a>Profile wersji interfejsu API Azure CLI 2.0 w stosie Azure

Możesz wykonać kroki opisane w tym artykule, aby skonfigurować Azure interfejsu wiersza polecenia (CLI) do zarządzania zasobami Azure stosu Development Kit z platform klienta Mac, Linux i Windows.

## <a name="install-cli"></a>Instalowanie interfejsu wiersza polecenia

Zaloguj się do stacji roboczej do rozwoju i zainstaluj interfejs wiersza polecenia. Stos Azure wymaga wersji 2.0 z wiersza polecenia platformy Azure. Który można zainstalować przy użyciu procedury opisanej w [zainstalować Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) artykułu. Aby sprawdzić, czy instalacja zakończyła się pomyślnie, Otwórz okno wiersza polecenia lub terminal i uruchom następujące polecenie:

```azurecli
az --version
```

Powinna zostać wyświetlona wersja interfejsu wiersza polecenia Azure i innych zależnych bibliotek, które są zainstalowane na komputerze.

## <a name="trust-the-azure-stack-ca-root-certificate"></a>Certyfikat główny urzędu certyfikacji stosu Azure zaufania

1. Pobierz certyfikat główny urzędu certyfikacji stosu Azure z [operatorem stosu Azure](..\azure-stack-cli-admin.md#export-the-azure-stack-ca-root-certificate) i zaufania temu certyfikatowi. Aby zaufać certyfikat główny urzędu certyfikacji stosu Azure, należy dołączyć do istniejącego certyfikatu Python.

2. Znajdź lokalizację certyfikatu na tym komputerze. Lokalizacja może się różnić w zależności od tego, w którym zainstalowano języka Python. Musisz mieć [pip](https://pip.pypa.io) i [certifi](https://pypi.org/project/certifi/) zainstalowany moduł. Służy następujące polecenie języka Python z poziomu wiersza bash:

  ```bash  
    python -c "import certifi; print(certifi.where())"
  ```

  Zanotuj lokalizację certyfikatu. Na przykład `~/lib/python3.5/site-packages/certifi/cacert.pem`. Określonej ścieżki będzie zależeć od system operacyjny i wersję języka Python, który został zainstalowany.

### <a name="set-the-path-for-a-development-machine-inside-the-cloud"></a>Ustaw ścieżkę na komputerze deweloperskim w chmurze

Jeśli używasz interfejsu wiersza polecenia z maszyny z systemem Linux, który jest tworzony w środowisku Azure stosu, uruchom następujące polecenie bash ze ścieżką do swojego certyfikatu.

```bash
sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
```

### <a name="set-the-path-for-a-development-machine-outside-the-cloud"></a>Ustaw ścieżkę na komputerze deweloperskim poza chmury

Jeśli używasz interfejsu wiersza polecenia na komputerze **poza** środowiska Azure stosu:  

1. Należy zdefiniować [połączenie z siecią VPN Azure stos](azure-stack-connect-azure-stack.md).

2. Skopiuj certyfikat PEM pochodzący z operatora stosu Azure i zanotuj lokalizację pliku (PATH_TO_PEM_FILE).

3. Uruchom następujące polecenia, w zależności od zakończenia na stacji roboczej rozwoju systemu operacyjnego.

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

Write-Host "Extracting needed information from the cert file"
$md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
$sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
$sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

$issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
$subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
$labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
$serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
$md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
$sha1Entry    = [string]::Format("# SHA1 Finterprint: {0}", $sha1Hash)
$sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
$certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

$rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
$serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

Write-Host "Adding the certificate content to Python Cert store"
Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

Write-Host "Python Cert store was updated for allowing the azure stack CA root certificate"
```

## <a name="get-the-virtual-machine-aliases-endpoint"></a>Pobierz punktu końcowego aliasy maszyny wirtualnej

Zanim użytkownicy mogą tworzyć maszyny wirtualne za pomocą interfejsu wiersza polecenia, muszą skontaktuj się z operatora stosu Azure i uzyskać identyfikator URI punktu końcowego aliasy maszyny wirtualnej. Na przykład Azure używa następujący identyfikator URI: https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json. Administrator chmury powinien skonfigurować podobne punktu końcowego stosu Azure przy użyciu obrazów, które są dostępne w witrynie marketplace stosu Azure. Użytkownikom należy przekazać identyfikator URI punktu końcowego do `endpoint-vm-image-alias-doc` parametr `az cloud register` polecenia, jak pokazano w następnej sekcji. 
   

## <a name="connect-to-azure-stack"></a>Nawiązywanie połączenia z usługą Azure Stack

Poniższe kroki umożliwiają nawiązanie Azure stosu:

1. Zarejestruj środowiska Azure stosu, uruchamiając `az cloud register` polecenia.
   
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

2. Ustawianie aktywnego środowiska przy użyciu następujących poleceń.

   a. Dla *chmury administracyjne* środowiska, użyj:

      ```azurecli
      az cloud set \
        -n AzureStackAdmin
      ```

   b. Aby uzyskać *użytkownika* środowiska, użyj:

      ```azurecli
      az cloud set \
        -n AzureStackUser
      ```

3. Zaktualizuj konfigurację środowiska, aby użyć określonego profilu wersji interfejsu API Azure stosu. Aby zaktualizować konfigurację, uruchom następujące polecenie:

   ```azurecli
   az cloud update \
     --profile 2017-03-09-profile
   ```

4. Zaloguj się do środowiska Azure stosu przy użyciu `az login` polecenia. Można Zaloguj się do środowiska Azure stosu jako użytkownik lub jako [nazwy głównej usługi](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-objects). 

   * Zaloguj się jako *użytkownika*: można określić nazwę użytkownika i hasło bezpośrednio w ramach `az login` polecenie lub uwierzytelniania za pomocą przeglądarki. Musisz wykonać jego, jeśli konto ma włączone uwierzytelnianie wieloskładnikowe.

      ```azurecli
      az login \
        -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
      ```

      > [!NOTE]
      > Jeśli Twoje konto użytkownika ma włączone uwierzytelnianie wieloskładnikowe, można użyć `az login command` bez podawania `-u` parametru. Uruchomienie tego polecenia zapewnia adres URL i kod, którego musisz użyć do uwierzytelniania.
   
   * Zaloguj się jako *nazwy głównej usługi*: przed zalogowaniem, [Tworzenie nazwy głównej usługi za pośrednictwem portalu Azure](azure-stack-create-service-principals.md) lub interfejsu wiersza polecenia i jej przypisanie roli. Teraz Zaloguj się przy użyciu następującego polecenia:

      ```azurecli
      az login \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
        --service-principal \
        -u <Application Id of the Service Principal> \
        -p <Key generated for the Service Principal>
      ```

## <a name="test-the-connectivity"></a>Testowanie łączności

Teraz, gdy mamy wszystkie ustawienia, można utworzyć zasoby w stosie Azure można użyć interfejsu wiersza polecenia. Można na przykład utworzyć grupę zasobów dla aplikacji i Dodaj maszynę wirtualną. Aby utworzyć grupę zasobów o nazwie "MyResourceGroup", użyj następującego polecenia:

```azurecli
az group create \
  -n MyResourceGroup -l local
```

Jeśli grupa zasobów została utworzona pomyślnie, poprzednie polecenie wyświetla następujące właściwości nowo utworzonego zasobu:

![Tworzenie grupy zasobów danych wyjściowych](media/azure-stack-connect-cli/image1.png)

## <a name="known-issues"></a>Znane problemy
Istnieją znane problemy, które trzeba pamiętać podczas przy użyciu interfejsu wiersza polecenia Azure stosu:

 - Tj interfejsu wiersza polecenia w trybie interakcyjnym `az interactive` polecenie nie jest jeszcze obsługiwane w stosie Azure.
 - Aby uzyskać listę dostępnych w stosie Azure obrazów maszyny wirtualnej, użyj `az vm images list --all` polecenia zamiast `az vm image list` polecenia. Określanie `--all` option zapewnia, że odpowiedzi zwraca tylko obrazy, które są dostępne w środowisku Azure stosu.
 - Aliasy obrazu maszyny wirtualnej, które są dostępne w systemie Azure nie może być stosowane do stosu Azure. Korzystając z obrazów maszyny wirtualnej, użyć całego parametru URN (Canonical: UbuntuServer:14.04.3-LTS:1.0.0) zamiast alias obrazu. Ten URN musi odpowiadać specyfikacji obrazu wynikające z `az vm images list` polecenia.

## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie szablonów z wiersza polecenia platformy Azure](azure-stack-deploy-template-command-line.md)

[Włącz wiersza polecenia platformy Azure dla użytkowników usługi Azure stosu (Operator)](..\azure-stack-cli-admin.md)

[Zarządzanie uprawnieniami użytkowników](azure-stack-manage-permissions.md)