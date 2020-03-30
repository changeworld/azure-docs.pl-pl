---
title: Bezpieczny dostęp do magazynu kluczy za pomocą usługi Batch — usługa Azure Batch
description: Dowiedz się, jak programowo uzyskiwać dostęp do poświadczeń z usługi Key Vault przy użyciu usługi Azure Batch.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.workload: big-compute
ms.topic: article
ms.date: 02/13/2020
ms.author: lahugh
ms.openlocfilehash: 0134e7d92ddca9bd3b45abaf642f33de9d209b33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78192306"
---
# <a name="securely-access-key-vault-with-batch"></a>Bezpieczny dostęp do usługi Key Vault za pomocą usługi Batch

W tym artykule dowiesz się, jak skonfigurować węzły usługi Batch, aby bezpiecznie uzyskiwać dostęp do poświadczeń przechowywanych w usłudze Azure Key Vault. Nie ma sensu umieszczać poświadczeń administratora w ucho, a następnie kodować poświadczenia z dysku twardego, aby uzyskać dostęp do usługi Key Vault ze skryptu. Rozwiązaniem jest użycie certyfikatu, który udziela dostępu węzłom usługi Batch do usługi Key Vault. Za pomocą kilku kroków możemy zaimplementować bezpieczne przechowywanie kluczy dla usługi Batch.

Aby uwierzytelnić się w usłudze Azure Key Vault z węzła usługi Batch, potrzebujesz:

- Poświadczenie usługi Azure Active Directory (Azure AD)
- Certyfikat
- Konto usługi Batch
- Pula wsadowa z co najmniej jednym węzłem

## <a name="obtain-a-certificate"></a>Uzyskiwanie certyfikatu

Jeśli nie masz jeszcze certyfikatu, najprostszym sposobem uzyskania certyfikatu jest wygenerowanie `makecert` certyfikatu z podpisem własnym za pomocą narzędzia wiersza polecenia.

Zazwyczaj można znaleźć `makecert` w tej `C:\Program Files (x86)\Windows Kits\10\bin\<arch>`ścieżce: . Otwórz wiersz polecenia jako administrator `makecert` i przejdź do przy użyciu następującego przykładu.

```console
cd C:\Program Files (x86)\Windows Kits\10\bin\x64
```

Następnie użyj `makecert` narzędzia do tworzenia plików certyfikatów z podpisem własnym o nazwie `batchcertificate.cer` i `batchcertificate.pvk`. Nazwa pospolita (CN) używana nie jest ważna dla tej aplikacji, ale warto zrobić z niej coś, co informuje o tym, do czego służy certyfikat.

```console
makecert -sv batchcertificate.pvk -n "cn=batch.cert.mydomain.org" batchcertificate.cer -b 09/23/2019 -e 09/23/2019 -r -pe -a sha256 -len 2048
```

Partia wymaga `.pfx` pliku. Użyj narzędzia [pvk2pfx,](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx) `.cer` aby `.pvk` przekonwertować pliki i pliki utworzone przez `makecert` jeden `.pfx` plik.

```console
pvk2pfx -pvk batchcertificate.pvk -spc batchcertificate.cer -pfx batchcertificate.pfx -po
```

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Dostęp do usługi Key Vault jest przyznawany **użytkownikowi** lub **podmiotowi usługi.** Aby uzyskać dostęp do usługi Key Vault programowo, użyj jednostki usługi z certyfikatem utworzonym przez nas w poprzednim kroku.

Aby uzyskać więcej informacji na temat podmiotów świadczących usługi platformy Azure, zobacz [Obiekty głównej aplikacji i usługi w usłudze Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

> [!NOTE]
> Podmiot zabezpieczeń usługi musi znajdować się w tej samej dzierżawie usługi Azure AD co magazyn kluczy.

```powershell
$now = [System.DateTime]::Parse("2020-02-10")
# Set this to the expiration date of the certificate
$expirationDate = [System.DateTime]::Parse("2021-02-10")
# Point the script at the cer file you created $cerCertificateFilePath = 'c:\temp\batchcertificate.cer'
$cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$cer.Import($cerCertificateFilePath)
# Load the certificate into memory
$credValue = [System.Convert]::ToBase64String($cer.GetRawCertData())
# Create a new AAD application that uses this certificate
$newADApplication = New-AzureRmADApplication -DisplayName "Batch Key Vault Access" -HomePage "https://batch.mydomain.com" -IdentifierUris "https://batch.mydomain.com" -certValue $credValue -StartDate $now -EndDate $expirationDate
# Create new AAD service principal that uses this application
$newAzureAdPrincipal = New-AzureRmADServicePrincipal -ApplicationId $newADApplication.ApplicationId
```

Adresy URL aplikacji nie są ważne, ponieważ używamy ich tylko do dostępu do usługi Key Vault.

## <a name="grant-rights-to-key-vault"></a>Przyznanie praw do usługi Key Vault

Podmiot zabezpieczeń usługi utworzony w poprzednim kroku wymaga uprawnień do pobierania wpisów tajnych z usługi Key Vault. Uprawnienie można udzielić za pośrednictwem witryny Azure portal lub polecenia programu PowerShell poniżej.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'BatchVault' -ServicePrincipalName '"https://batch.mydomain.com' -PermissionsToSecrets 'Get'
```

## <a name="assign-a-certificate-to-a-batch-account"></a>Przypisywanie certyfikatu do konta usługi Batch

Utwórz pulę partii, a następnie przejdź do karty certyfikatu w puli i przypisz utworzony certyfikat. Certyfikat znajduje się teraz we wszystkich węzłach usługi Batch.

Następnie musimy przypisać certyfikat do konta usługi Batch. Przypisanie certyfikatu do konta pozwala nam przypisać go do pul, a następnie do węzłów. Najprostszym sposobem, aby to zrobić, jest przejście do konta usługi Batch w portalu, przejście do **pozycji Certyfikaty**i **wybranie**opcji Dodaj . Przekaż `.pfx` plik wygenerowany w [uzyskać certyfikat](#obtain-a-certificate) i podać hasło. Po zakończeniu certyfikat jest dodawany do listy i można zweryfikować odcisk palca.

Teraz podczas tworzenia puli usługi Batch można przejść do **pozycji Certyfikaty** w puli i przypisać certyfikat utworzony do tej puli. Po wykonaniu tej opcji należy wybrać **opcję LocalMachine** dla lokalizacji sklepu. Certyfikat jest ładowany na wszystkie węzły usługi Batch w puli.

## <a name="install-azure-powershell"></a>Instalowanie programu Azure PowerShell

Jeśli planujesz dostęp do usługi Key Vault przy użyciu skryptów programu PowerShell w węzłach, musisz zainstalować bibliotekę programu Azure PowerShell. Istnieje kilka sposobów, aby to zrobić, jeśli węzły mają windows management framework (WMF) 5 zainstalowany, a następnie można użyć polecenia install-module, aby go pobrać. Jeśli używasz węzłów, które nie mają WMF 5, najprostszym sposobem zainstalowania go `.msi` jest powiązanie pliku programu Azure PowerShell z plikami usługi Batch, a następnie wywołanie instalatora jako pierwszej części skryptu uruchamiania usługi Batch. Zobacz ten przykład, aby uzyskać szczegółowe informacje:

```powershell
$psModuleCheck=Get-Module -ListAvailable -Name Azure -Refresh
if($psModuleCheck.count -eq 0) {
    $psInstallerPath = Join-Path $downloadPath "azure-powershell.3.4.0.msi" Start-Process msiexec.exe -ArgumentList /i, $psInstallerPath, /quiet -wait
}
```

## <a name="access-key-vault"></a>Uzyskiwanie dostępu do usługi Key Vault

Teraz wszyscy jesteśmy skonfigurowani, aby uzyskać dostęp do usługi Key Vault w skryptach uruchomionych w węzłach usługi Batch. Aby uzyskać dostęp do usługi Key Vault ze skryptu, wystarczy, aby skrypt uwierzytelnił się w usłudze Azure AD przy użyciu certyfikatu. Aby to zrobić w programie PowerShell, należy użyć następujących przykładowych poleceń. Określ odpowiedni identyfikator GUID dla **odcisku palca,** **identyfikator aplikacji** (identyfikator jednostki usługi) i **identyfikator dzierżawy** (dzierżawy, w której istnieje jednostka usługi).

```powershell
Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint -ApplicationId
```

Po uwierzytelnieniu dostęp KeyVault, jak zwykle.

```powershell
$adminPassword=Get-AzureKeyVaultSecret -VaultName BatchVault -Name batchAdminPass
```

Są to poświadczenia do użycia w skrypcie.
