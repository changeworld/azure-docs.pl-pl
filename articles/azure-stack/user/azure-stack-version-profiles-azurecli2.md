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
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: af7c0f9d7e02e4a3074f2fddbdf8a6e0ab52d423
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2018
ms.locfileid: "44024328"
---
# <a name="use-api-version-profiles-with-azure-cli-20-in-azure-stack"></a>Profilami wersji interfejsu API za pomocą usługi Azure Stack w wersji 2.0 interfejsu wiersza polecenia platformy Azure

Możesz wykonać kroki opisane w tym artykule, aby ustawić się interfejsu wiersza polecenia platformy Azure (CLI) do zarządzania zasobami Azure Stack Development Kit na platformach klienckich systemu Linux, Mac i Windows.

## <a name="install-cli"></a>Instalowanie interfejsu wiersza polecenia

Zaloguj się do deweloperskiej stacji roboczej i zainstalować interfejs wiersza polecenia. Usługa Azure Stack wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0. Można to zrobić, korzystając z procedury opisanej w [instalowanie interfejsu wiersza polecenia platformy Azure w wersji 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) artykułu. Aby sprawdzić, czy instalacja się powiodła, otwórz terminal lub okno wiersza polecenia i uruchom następujące polecenie:

```azurecli
az --version
```

Powinien zostać wyświetlony wersji wiersza polecenia platformy Azure i inne zależne biblioteki, które są zainstalowane na tym komputerze.

## <a name="trust-the-azure-stack-ca-root-certificate"></a>Traktować jako zaufany certyfikat główny urzędu usługi Azure Stack

1. Pobierz certyfikat główny urzędu usługi Azure Stack z [operator usługi Azure Stack](..\azure-stack-cli-admin.md#export-the-azure-stack-ca-root-certificate) i zaufania temu certyfikatowi. Aby zaufać certyfikatowi głównemu urzędu certyfikacji w usłudze Azure Stack, należy dołączyć do istniejącego certyfikatu języka Python.

1. Znajdź lokalizację certyfikatu na komputerze. Lokalizacja mogą się różnić w zależności od tego, gdzie zainstalowano języka Python. Musisz mieć [pip](https://pip.pypa.io) i [certifi](https://pypi.org/project/certifi/) zainstalowany moduł. Służy następujące polecenie języka Python w wierszu polecenia powłoki bash:

  ```bash  
    python -c "import certifi; print(certifi.where())"
  ```

  Zanotuj lokalizację certyfikatu. Na przykład `~/lib/python3.5/site-packages/certifi/cacert.pem`. Konkretnej ścieżki zależy od Twojego systemu operacyjnego i wersji języka Python, który został zainstalowany.

### <a name="set-the-path-for-a-development-machine-inside-the-cloud"></a>Ustaw ścieżkę dla komputera deweloperskiego z systemem w chmurze

Jeśli używasz interfejsu wiersza polecenia na komputerze systemu Linux, która została utworzona w środowisku usługi Azure Stack, uruchom poniższe polecenie powłoki bash ze ścieżką do certyfikatu.

```bash
sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
```

### <a name="set-the-path-for-a-development-machine-outside-the-cloud"></a>Ustaw ścieżkę na komputerze deweloperskim poza chmury

Jeśli używasz interfejsu wiersza polecenia na komputerze **poza** środowiska usługi Azure Stack:  

1. Należy zdefiniować [połączenia sieci VPN w usłudze Azure Stack](azure-stack-connect-azure-stack.md).

1. Skopiuj certyfikatu PEM z operatora infrastruktury Azure Stack, a następnie zanotuj lokalizację pliku (PATH_TO_PEM_FILE).

1. Uruchom następujące polecenia, w zależności końcową na stacji roboczej programowania systemu operacyjnego.

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
$sha1Entry    = [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
$sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
$certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

$rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
$serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

Write-Host "Adding the certificate content to Python Cert store"
Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

Write-Host "Python Cert store was updated for allowing the azure stack CA root certificate"
```

## <a name="get-the-virtual-machine-aliases-endpoint"></a>Pobieranie punktu końcowego aliasy maszyny wirtualnej

Zanim użytkownicy mogą tworzyć maszyny wirtualne przy użyciu interfejsu wiersza polecenia, muszą skontaktować się z operatora infrastruktury Azure Stack i Uzyskaj identyfikator URI punktu końcowego aliasy maszyny wirtualnej. Na przykład platforma Azure używa następujący identyfikator URI: https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json. Administrator chmury należy skonfigurować podobne punktu końcowego usługi Azure Stack przy użyciu obrazów, które są dostępne w witrynie marketplace usługi Azure Stack. Użytkownicy muszą przekazać identyfikator URI punktu końcowego do `endpoint-vm-image-alias-doc` parametr `az cloud register` polecenia, jak pokazano w następnej sekcji. 
   

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
     --profile 2017-03-09-profile
   ```

1. Zaloguj się do środowiska usługi Azure Stack przy użyciu `az login` polecenia. Możesz zalogować się do środowiska usługi Azure Stack jako użytkownik lub [nazwy głównej usługi](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-objects). 

   * Zaloguj się jako *użytkownika*: można określić nazwę użytkownika i hasło bezpośrednio w ramach `az login` polecenie lub uwierzytelniania za pomocą przeglądarki. Musisz wykonać jego, jeśli konto ma włączonego uwierzytelniania wieloskładnikowego.

      ```azurecli
      az login \
        -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
      ```

      > [!NOTE]
      > Jeśli Twoje konto użytkownika ma włączonego uwierzytelniania wieloskładnikowego, możesz użyć `az login command` bez podawania `-u` parametru. Uruchamiając polecenie udostępnia adres URL i kodu, które muszą użyć do uwierzytelniania.
   
   * Zaloguj się jako *nazwy głównej usługi*: przed zalogowaniem, [utworzyć nazwę główną usługi za pośrednictwem witryny Azure portal](azure-stack-create-service-principals.md) lub interfejsu wiersza polecenia i przypisz mu roli. Teraz Zaloguj się przy użyciu następującego polecenia:

      ```azurecli
      az login \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
        --service-principal \
        -u <Application Id of the Service Principal> \
        -p <Key generated for the Service Principal>
      ```

## <a name="test-the-connectivity"></a>Testowanie łączności

Teraz, gdy mamy już wszystkie elementy konfiguracji, użyjemy interfejsu wiersza polecenia do tworzenia zasobów w ramach usługi Azure Stack. Na przykład można utworzyć grupę zasobów dla aplikacji i Dodaj maszynę wirtualną. Aby utworzyć grupę zasobów o nazwie "MyResourceGroup", użyj następującego polecenia:

```azurecli
az group create \
  -n MyResourceGroup -l local
```

Jeśli grupa zasobów została utworzona pomyślnie, poprzednie polecenie wyświetla następujące właściwości nowo utworzonego zasobu:

![Tworzenie grupy zasobów danych wyjściowych](media/azure-stack-connect-cli/image1.png)

## <a name="known-issues"></a>Znane problemy
Istnieją znane problemy, które należy wiedzieć przy użyciu interfejsu wiersza polecenia w usłudze Azure Stack:

 - Tj tryb interaktywny interfejsu wiersza polecenia `az interactive` polecenie nie jest jeszcze obsługiwane w usłudze Azure Stack.
 - Aby uzyskać listę obrazów maszyn wirtualnych są dostępne w usłudze Azure Stack, użyj `az vm images list --all` polecenia zamiast `az vm image list` polecenia. Określanie `--all` option zapewnia, że odpowiedzi zwraca tylko obrazy, które są dostępne w danym środowisku usługi Azure Stack.
 - Aliasy obrazu maszyny wirtualnej, które są dostępne na platformie Azure nie może być stosowane do usługi Azure Stack. Korzystając z obrazów maszyn wirtualnych, należy użyć całego parametru URN (Canonical: UbuntuServer:14.04.3-LTS:1.0.0) zamiast alias obrazu. Ta URN musi odpowiadać specyfikacji obrazu wyprowadzana z `az vm images list` polecenia.

## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie szablonów za pomocą wiersza polecenia platformy Azure](azure-stack-deploy-template-command-line.md)

[Włączanie interfejsu wiersza polecenia platformy Azure dla użytkowników usługi Azure Stack (Operator)](..\azure-stack-cli-admin.md)

[Zarządzanie uprawnieniami użytkowników](azure-stack-manage-permissions.md)
