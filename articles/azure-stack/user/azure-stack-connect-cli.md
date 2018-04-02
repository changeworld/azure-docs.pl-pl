---
title: "Połącz Azure stos z interfejsu wiersza polecenia | Dokumentacja firmy Microsoft"
description: "Dowiedz się, jak używać do zarządzania i wdrażania zasobów na stosie Azure międzyplatformowego interfejsu wiersza polecenia (CLI)"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: f576079c-5384-4c23-b5a4-9ae165d1e3c3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: mabrigg
ms.openlocfilehash: ad258cf6c73b2c1ea5f208ebef9906a0e3d35ea4
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2018
---
# <a name="install-and-configure-cli-for-use-with-azure-stack"></a>Instalowanie i Konfigurowanie interfejsu wiersza polecenia do użycia z programem Azure stosu

W tym artykule firma Microsoft prowadzące przez proces przy użyciu interfejsu wiersza polecenia platformy Azure (CLI) do zarządzania zasobami Azure stosu Development Kit z systemem Linux i platform klienckich Mac. 

## <a name="install-cli"></a>Instalowanie interfejsu wiersza polecenia

Następnie zaloguj się do stacji roboczej do rozwoju i zainstaluj interfejs wiersza polecenia. Stos Azure wymaga wersji 2.0 z wiersza polecenia platformy Azure. Który można zainstalować przy użyciu procedury opisanej w [zainstalować Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) artykułu. Aby sprawdzić, czy instalacja zakończyła się pomyślnie, Otwórz okno wiersza polecenia lub terminal i uruchom następujące polecenie:

```azurecli
az --version
```

Powinna zostać wyświetlona wersja interfejsu wiersza polecenia Azure i innych zależnych bibliotek, które są zainstalowane na komputerze.

## <a name="trust-the-azure-stack-ca-root-certificate"></a>Certyfikat główny urzędu certyfikacji stosu Azure zaufania

Pobierz certyfikat główny urzędu certyfikacji stosu Azure z operatora stosu Azure i zaufania temu certyfikatowi. Aby zaufać certyfikat główny urzędu certyfikacji stosu Azure, należy dołączyć do istniejącego certyfikatu Python. Jeśli używasz interfejsu wiersza polecenia z maszyny z systemem Linux, który jest tworzony w środowisku Azure stosu, uruchom następujące polecenie bash:

```bash
sudo cat /var/lib/waagent/Certificates.pem >> ~/lib/azure-cli/lib/python2.7/site-packages/certifi/cacert.pem
```

Jeśli używasz interfejsu wiersza polecenia na komputerze poza środowiskiem worek Azure, należy najpierw skonfigurować [połączenie z siecią VPN Azure stos](azure-stack-connect-azure-stack.md). Teraz skopiuj wyeksportowany wcześniej na deweloperskiej stacji roboczej certyfikat PEM i uruchom następujące polecenia, w zależności od systemu operacyjnego z deweloperskiej stacji roboczej.

### <a name="linux"></a>Linux

```bash
sudo cat PATH_TO_PEM_FILE >> ~/lib/azure-cli/lib/python2.7/site-packages/certifi/cacert.pem
```

### <a name="macos"></a>macOS

```bash
sudo cat PATH_TO_PEM_FILE >> ~/lib/azure-cli/lib/python2.7/site-packages/certifi/cacert.pem
```

### <a name="windows"></a>Windows

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
        --endpoint-active-directory-graph-resource-id "https://graph.windows.net/" \
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
      ```

   b. Aby zarejestrować *użytkownika* środowiska, użyj:

      ```azurecli
      az cloud register \ 
        -n AzureStackUser \ 
        --endpoint-resource-manager "https://management.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".vault.local.azurestack.external" \ 
        --endpoint-active-directory-graph-resource-id "https://graph.windows.net/" \
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

* Tj interfejsu wiersza polecenia w trybie interakcyjnym `az interactive` polecenie nie jest jeszcze obsługiwane w stosie Azure.
* Aby uzyskać listę dostępnych w stosie Azure obrazów maszyny wirtualnej, użyj `az vm images list --all` polecenia zamiast `az vm image list` polecenia. Określanie `--all` option zapewnia, że odpowiedzi zwraca tylko obrazy, które są dostępne w środowisku Azure stosu. 
* Aliasy obrazu maszyny wirtualnej, które są dostępne w systemie Azure nie może być stosowane do stosu Azure. Korzystając z obrazów maszyny wirtualnej, użyć całego parametru URN (Canonical: UbuntuServer:14.04.3-LTS:1.0.0) zamiast alias obrazu. Ten URN musi odpowiadać specyfikacji obrazu wynikające z `az vm images list` polecenia.
* Domyślnie 2.0 interfejsu wiersza polecenia używa "Standard_DS1_v2" jako domyślny rozmiar obrazu maszyny wirtualnej. Jednak ten rozmiar nie jest jeszcze dostępna w stosie Azure, dlatego należy określić `--size` parametru jawnie, podczas tworzenia maszyny wirtualnej. Można pobrać listy rozmiarów maszyn wirtualnych, które są dostępne w stosie Azure przy użyciu `az vm list-sizes --location <locationName>` polecenia.


## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie szablonów z wiersza polecenia platformy Azure](azure-stack-deploy-template-command-line.md)

[Zarządzanie uprawnieniami użytkowników](azure-stack-manage-permissions.md)
