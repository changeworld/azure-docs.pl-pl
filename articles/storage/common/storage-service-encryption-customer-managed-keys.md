---
title: Szyfrowanie usługi Azure Storage przy użyciu kluczy zarządzanych przez klienta w usłudze Azure Key Vault | Dokumentacja firmy Microsoft
description: Szyfrowanie usługi Azure Blob storage i usługi Azure Files na stronie usługi, w przypadku przechowywania danych za pomocą funkcji szyfrowania usługi Storage platformy Azure i je odszyfrować, podczas pobierania danych przy użyciu kluczy zarządzanych przez klienta.
services: storage
author: lakasa
ms.service: storage
ms.topic: article
ms.date: 10/11/2018
ms.author: lakasa
ms.component: common
ms.openlocfilehash: 5ef9c15d4edf62ef63b16765f16971a9be5ca58b
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52970709"
---
# <a name="storage-service-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Szyfrowanie usługi Storage przy użyciu kluczy zarządzanych przez klienta w usłudze Azure Key Vault

Platforma Microsoft Azure jest zobowiązana do ochrony i chronić dane zgodnie z wymaganiami co do bezpieczeństwa organizacji i zobowiązaniami w zakresie zgodności. Jednym ze sposobów, że platforma Azure storage chroni dane jest za pośrednictwem szyfrowanie usługi Storage (SSE), który szyfruje dane podczas zapisywanie w magazynie i odszyfrowuje dane podczas pobierania go. Szyfrowanie i odszyfrowywanie jest automatyczne, przejrzyste i korzysta z 256-bitowego [szyfrowania AES](https://wikipedia.org/wiki/Advanced_Encryption_Standard), jeden blok najsilniejszych szyfrów.

Za pomocą kluczy zarządzanych przez firmę Microsoft szyfrowania SSE lub użyć własnych kluczy szyfrowania. W tym artykule opisano sposób użycia kluczy szyfrowania. Aby uzyskać więcej informacji na temat przy użyciu kluczy zarządzanych przez firmę Microsoft lub o SSE ogólnie rzecz biorąc, zobacz [szyfrowanie usługi Storage dla danych magazynowanych](storage-service-encryption.md).

Rozszerzenia SSE dla usługi Azure Blob storage i Azure Files jest zintegrowany z usługi Azure Key Vault, tak aby możesz zarządzać kluczami szyfrowania za pomocą magazynu kluczy. Można tworzyć własne klucze szyfrowania i przechowywać je w magazynie kluczy lub interfejsów API usługi Azure Key Vault umożliwia generowanie kluczy szyfrowania. Usługa Azure Key Vault można zarządzać i kontrolować klucze i również inspekcji użycia kluczy.

> [!Note]  
> Szyfrowanie usługi Storage przy użyciu kluczy zarządzanych przez klienta nie jest dostępna dla [usługi Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md). [Usługa Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md) używa standardowych [funkcji BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) na Windows i [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) w systemie Linux w celu uzyskania rozwiązania zapewniającego szyfrowania zintegrowane z usługą KeyVault.

Dlaczego warto tworzyć własne klucze? Klucze niestandardowe zapewniają większą elastyczność, tak że można utworzyć, obracanie, wyłącz i definiowanie kontroli dostępu. Klucze niestandardowe umożliwiają również przeprowadzać inspekcję kluczy szyfrowania używany do ochrony danych.

## <a name="get-started-with-customer-managed-keys"></a>Rozpoczynanie pracy z kluczy zarządzanych przez klienta

Aby używać kluczy zarządzanych przez klienta za pomocą funkcji SSE, można utworzyć nowego magazynu kluczy i klucza, lub możesz użyć istniejącego magazynu kluczy i klucz. Konto magazynu i magazynu kluczy musi być w tym samym regionie, ale można je w różnych subskrypcjach.

### <a name="step-1-create-a-storage-account"></a>Krok 1: Tworzenie konta magazynu

Najpierw utwórz konto magazynu, jeśli nie masz jeszcze takiego. Aby uzyskać więcej informacji, zobacz temat [Tworzenie konta](storage-quickstart-create-account.md).

### <a name="step-2-enable-sse-for-blob-and-file-storage"></a>Krok 2: Włączanie SSE dla magazynu obiektów Blob i plików

Aby włączyć SSE przy użyciu kluczy zarządzanych przez klienta, dwie funkcje ochrony kluczy: usuwanie nietrwałe i przeczyszczanie, musi być także włączona w usłudze Azure Key Vault. Tych ustawień upewnij się, że klucze nie może być przypadkowo lub celowo usuniętymi. Maksymalny okres przechowywania kluczy wynosi 90 dni, chronić użytkowników przed uczestników złośliwych działań lub oprogramowania wymuszającego okup.

Jeśli chcesz programowo włączyć kluczy zarządzanych przez klienta dla SSE, można użyć [interfejsu API REST dostawcy zasobów magazynu Azure](https://docs.microsoft.com/rest/api/storagerp), [Biblioteka klienta dostawcy zasobów usługi Storage dla platformy .NET](https://docs.microsoft.com/dotnet/api), [ Program Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview), lub [wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/storage/storage-azure-cli).

Aby używać kluczy zarządzanych przez klienta za pomocą funkcji SSE, należy przypisać tożsamość konta magazynu do konta magazynu. Można ustawić tożsamość, wykonując następujące polecenie programu PowerShell lub wiersza polecenia platformy Azure:

```powershell
Set-AzureRmStorageAccount -ResourceGroupName \$resourceGroup -Name \$accountName -AssignIdentity
```

```azurecli-interactive
az storage account \
    --account-name <account_name> \
    --resource-group <resource_group> \
    --assign-identity
```

Należy włączyć usuwanie nietrwałe i przeczyszczanie, wykonując następujące polecenia programu PowerShell lub wiersza polecenia platformy Azure:

```powershell
($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName
$vaultName).ResourceId).Properties | Add-Member -MemberType NoteProperty -Name
enableSoftDelete -Value 'True'

Set-AzureRmResource -resourceid $resource.ResourceId -Properties
$resource.Properties

($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName
$vaultName).ResourceId).Properties | Add-Member -MemberType NoteProperty -Name
enablePurgeProtection -Value 'True'

Set-AzureRmResource -resourceid $resource.ResourceId -Properties
$resource.Properties
```

```azurecli-interactive
az resource update \
    --id $(az keyvault show --name <vault_name> -o tsv | awk '{print $1}') \
    --set properties.enableSoftDelete=true

az resource update \
    --id $(az keyvault show --name <vault_name> -o tsv | awk '{print $1}') \
    --set properties.enablePurgeProtection=true
```

### <a name="step-3-enable-encryption-with-customer-managed-keys"></a>Krok 3: Włączanie szyfrowania za pomocą kluczy zarządzanych przez klienta

Domyślnie SSE używa kluczy zarządzanych przez firmę Microsoft. Usługa SSE można włączyć za pomocą kluczy zarządzanych przez klienta dla konta magazynu przy użyciu [witryny Azure portal](https://portal.azure.com/). Na **ustawienia** bloku konto magazynu, kliknij przycisk **szyfrowania**. Wybierz **użycie swojego własnego klucza** opcji, jak pokazano na poniższej ilustracji.

![Portal zrzut ekranu przedstawiający opcję szyfrowania](./media/storage-service-encryption-customer-managed-keys/ssecmk1.png)

### <a name="step-4-select-your-key"></a>Krok 4: Wybierz klucz

Klucz można określić jako identyfikator URI lub wybierając klucza z magazynu kluczy.

#### <a name="specify-a-key-as-a-uri"></a>Określ klucz jako identyfikator URI

Aby określić klucz z identyfikatora URI, wykonaj następujące kroki:

1. Wybierz **klawisza Enter URI** opcji.
2. W **identyfikator URI klucza** pola, określ identyfikator URI.

   ![Portal zrzut szyfrowanie za pomocą wprowadź identyfikator uri klucza opcję](./media/storage-service-encryption-customer-managed-keys/ssecmk2.png)

#### <a name="specify-a-key-from-a-key-vault"></a>Określenie klucza z magazynu kluczy

Aby określić klucz z key vault, wykonaj następujące kroki:

1. Wybierz **wybierz z magazynu Key Vault** opcji.
2. Wybierz magazyn kluczy zawierającego klucz, którego chcesz użyć.
3. Wybierz klawisz z magazynu kluczy.

   ![Portal Szyfrowań zrzut ekranu przedstawiający opcja własnego klucza](./media/storage-service-encryption-customer-managed-keys/ssecmk3.png)

Konto magazynu nie ma dostępu do magazynu kluczy, po uruchomieniu polecenia programu Azure PowerShell pokazano na poniższej ilustracji, aby udzielić dostępu.

![Portal zrzut ekranu przedstawiający odmowa dostępu do magazynu kluczy](./media/storage-service-encryption-customer-managed-keys/ssecmk4.png)

Należy również przyznać dostęp za pośrednictwem witryny Azure portal, przechodząc do usługi Azure Key Vault w witrynie Azure portal i udzielanie dostępu do konta magazynu.

Klucz powyżej można skojarzyć z istniejącego konta magazynu przy użyciu następujących poleceń programu PowerShell:

```powershell
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount"
$keyVault = Get-AzureRmKeyVault -VaultName "mykeyvault"
$key = Get-AzureKeyVaultKey -VaultName $keyVault.VaultName -Name "keytoencrypt"
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVault.VaultName -ObjectId $storageAccount.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
Set-AzureRmStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName -AccountName $storageAccount.StorageAccountName -KeyvaultEncryption -KeyName $key.Name -KeyVersion $key.Version -KeyVaultUri $keyVault.VaultUri
```

### <a name="step-5-copy-data-to-storage-account"></a>Krok 5: Kopiowanie danych do konta magazynu

Aby przenieść dane do nowego konta magazynu, dzięki czemu jest zaszyfrowany. Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące szyfrowania usługi Storage](storage-service-encryption.md#faq-for-storage-service-encryption).

### <a name="step-6-query-the-status-of-the-encrypted-data"></a>Krok 6: Wykonać zapytanie o stan zaszyfrowanych danych

Kwerenda o stan zaszyfrowanych danych.

## <a name="faq-for-sse-with-customer-managed-keys"></a>Często zadawane pytania dotyczące SSE za pomocą zarządzane kluczy klienta

**Korzystam z usługi Premium storage; Czy można używać kluczy zarządzanych przez klienta, za pomocą funkcji SSE?**  
Tak, SSE za pomocą kluczy zarządzanych przez firmę Microsoft i zarządzanych przez klienta jest obsługiwane zarówno magazynu w warstwie standardowa i Premium storage.

**Można utworzyć nowego konta magazynu za pomocą funkcji SSE za pomocą kluczy zarządzanych przez klienta, włączone za pomocą programu Azure PowerShell i wiersza polecenia platformy Azure?**  
Tak.

**O ile bardziej kosztuje się usługa Azure Storage, czy w przypadku używania kluczy zarządzanych przez klienta za pomocą funkcji SSE?**  
Brak naliczenie opłaty dotyczące korzystania z usługi Azure Key Vault. Aby uzyskać więcej informacji, odwiedź stronę [cenami Key Vault](https://azure.microsoft.com/pricing/details/key-vault/). Nie ma żadnych dodatkowych kosztów SSE, która jest włączona dla wszystkich kont magazynu.

**Szyfrowanie usługi Storage jest dostępna na dyskach zarządzanych platformy Azure?**  
Szyfrowanie usługi Storage jest dostępna dla usługi Azure Managed Disks za pomocą kluczy zarządzanych przez firmę Microsoft, ale nie z klientów zarządzanych kluczy. Audytów Managed Disks obsługuje SSE za pomocą kluczy zarządzanych przez klienta, firma Microsoft zaleca [usługi Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md), który używa standardowych [funkcji BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) na Windows i [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt)w systemie Linux w celu zapewnienia szyfrowania jest zintegrowana z usługą KeyVault.

**Czym różni się szyfrowanie usługi Storage z usługi Azure Disk Encryption?**  
Usługa Azure Disk Encryption zapewnia integrację rozwiązań opartych na systemu operacyjnego, takich jak funkcja BitLocker i DM-Crypt i usłudze Azure KeyVault. Szyfrowanie usługi Storage umożliwia szyfrowanie natywnie w warstwie platformy usługi Azure storage, poniżej maszyny wirtualnej.

**Czy można odwołać dostęp do kluczy szyfrowania?**
Tak, możesz odwołać dostęp w dowolnym momencie. Istnieje kilka sposobów, aby odwołać dostęp do kluczy. Zapoznaj się [programu PowerShell dla usługi Azure Key Vault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) i [wiersza polecenia platformy Azure Key Vault](https://docs.microsoft.com/cli/azure/keyvault) Aby uzyskać więcej informacji. Odbieranie prawa dostępu skutecznie blokuje dostęp do wszystkich obiektów blob na koncie magazynu jako klucz szyfrowania konta jest niedostępny przez usługę Azure Storage.

**W innym regionie można utworzyć konta magazynu i klucza?**  
Nie, konto magazynu oraz usługi Azure Key Vault i klucz muszą znajdować się w tym samym regionie.

**Można włączyć kluczy zarządzanych przez klienta dla SSE podczas tworzenia konta magazynu?**  
Nie. Po utworzeniu konta magazynu tylko kluczy zarządzanych przez firmę Microsoft są dostępne dla SSE. Aby używać kluczy zarządzanych przez klienta, należy zaktualizować właściwości konta magazynu. Programowe Aktualizowanie konta magazynu za pomocą REST lub jednej z bibliotek klienckich magazynu lub zaktualizuj właściwości konta magazynu przy użyciu witryny Azure portal po utworzeniu konta.

**Czy można wyłączyć szyfrowania podczas korzystania z kluczy zarządzanych przez klienta za pomocą funkcji SSE?**  
Nie, nie można wyłączyć szyfrowania. Szyfrowanie jest włączone domyślnie dla usługi Azure Blob storage, Azure Files, kolejki platformy Azure i usługi Azure Table storage. Opcjonalnie można przełączyć się z przy użyciu kluczy zarządzanych przez firmę Microsoft za pomocą kluczy zarządzanych przez klienta i na odwrót.

**Funkcja SSE jest włączona, podczas tworzenia nowego konta magazynu?**  
Funkcja SSE jest włączona dla wszystkich kont magazynu i usługi Azure Blob storage, Azure Files, Azure Queue storage i Azure Table storage.

**Nie można włączyć SSE przy użyciu kluczy zarządzanych przez klienta na moim koncie magazynu.**  
Czy konto magazynu usługi Azure Resource Manager? Klasyczne konta magazynu nie są obsługiwane za pomocą kluczy zarządzanych przez klienta. Usługa SSE za pomocą kluczy zarządzanych przez klienta można włączyć tylko na kontach magazynu usługi Resource Manager.

**Co to jest usuwanie nietrwałe i przeczyszczanie? Należy włączyć to ustawienie, aby SSE za pomocą kluczy zarządzanych przez klienta?**  
Usuwanie nietrwałe i przeczyszczanie musi być włączona funkcja SSE za pomocą kluczy zarządzanych przez klienta. Tych ustawień upewnij się, że klucz nie jest przypadkowo lub celowo usuniętymi. Maksymalny okres przechowywania kluczy wynosi 90 dni, chronić użytkowników przed atakami przed oprogramowaniem wymuszającym okup i uczestników złośliwych działań. Nie można wyłączyć to ustawienie.

**Jest SSE za pomocą kluczy zarządzanych przez klienta dozwolone tylko w określonych regionach?**  
Usługa SSE za pomocą kluczy zarządzanych przez klienta jest dostępna we wszystkich regionach świadczenia usługi Azure Blob storage i Azure Files.

**Jak mam się kontaktować ktoś czy jakichkolwiek problemów lub chcesz wyrazić swoją opinię?**  
Skontaktuj się z pomocą [ ssediscussions@microsoft.com ](mailto:ssediscussions@microsoft.com) dla problemów związanych z szyfrowania usługi Storage.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać więcej informacji na temat kompleksowy zestaw zabezpieczeń funkcji, które ułatwiają deweloperom tworzenie bezpiecznych aplikacji, zobacz [Przewodnik po zabezpieczeniach magazynu](storage-security-guide.md).
- Aby uzyskać informacje ogólne o usłudze Azure Key Vault, zobacz [co to jest usługa Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?
- Dla klientów zaczynających pracę w usłudze Azure Key Vault, zobacz [wprowadzenie do usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started).
