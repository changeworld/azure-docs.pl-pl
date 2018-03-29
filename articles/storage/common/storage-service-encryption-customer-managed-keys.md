---
title: Szyfrowanie usługi Magazyn Azure przy użyciu kluczy zarządzany przez klienta w usłudze Azure Key Vault | Dokumentacja firmy Microsoft
description: Szyfrowanie usługi magazynu obiektów Blob Azure na stronie usługi, gdy dane są przechowywane przy użyciu funkcji szyfrowanie usługi Magazyn Azure, a go odszyfrować podczas pobierania danych przy użyciu kluczy zarządzany przez klienta.
services: storage
author: lakasa
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/07/2018
ms.author: lakasa
ms.openlocfilehash: 04688f943ac9eba27ca193aa2054c69b6a94547d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="storage-service-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Szyfrowanie usługi Magazyn przy użyciu kluczy zarządzany przez klienta w usłudze Azure Key Vault

Microsoft Azure jest zaangażowana ochrony i ochrony danych w celu spełnienia organizacji bezpieczeństwa i zgodności zobowiązań. Jednym ze sposobów czy usługi Azure Storage chroni dane jest za pomocą magazynu usługi szyfrowania (SSE), który szyfruje dane podczas zapisywania go do magazynu i odszyfrowuje dane podczas pobierania go. Szyfrowanie i odszyfrowywanie jest automatyczne, przezroczysty i używa 256-bitowego [szyfrowania AES](https://wikipedia.org/wiki/Advanced_Encryption_Standard), jednego bloku najwyższy szyfrów dostępne.

Klucze szyfrowania zarządzany przez firmę Microsoft można używać z SSE lub skorzystać z kluczy szyfrowania. W tym artykule opisano sposób używania kluczy szyfrowania. Aby uzyskać więcej informacji o użyciu kluczy zarządzany przez firmę Microsoft lub o SSE ogólnie, zobacz [szyfrowanie usługi Magazyn danych magazynowanych](storage-service-encryption.md).

SSE magazynu obiektów Blob i plik jest zintegrowana z usługą Azure Key Vault, dzięki czemu magazynu kluczy umożliwia zarządzanie kluczami szyfrowania. Można utworzyć kluczy szyfrowania i przechowywać je w magazynie kluczy lub interfejsów API usługi Azure Key Vault służy do generowania kluczy szyfrowania. Z usługi Azure Key Vault można zarządzać i kontrolować klucze i również przeprowadzać inspekcję użycie klucza.

Dlaczego warto utworzyć własne klucze? Niestandardowe klucze zapewniają większą elastyczność, dzięki czemu można utworzyć, obracanie, wyłącz i zdefiniuj kontroli dostępu. Niestandardowe klucze umożliwiają również przeprowadzać inspekcję kluczy szyfrowania używany do ochrony danych.

## <a name="get-started-with-customer-managed-keys"></a>Rozpoczynanie pracy z kluczami zarządzany przez klienta

Aby używać kluczy zarządzany przez klienta z SSE, możesz utworzyć nowego magazynu kluczy oraz klucz lub użyć istniejącego magazynu kluczy oraz klucz. Konto magazynu i magazynu kluczy musi być w tym samym regionie, ale można je w ramach różnych subskrypcji. 

### <a name="step-1-create-a-storage-account"></a>Krok 1: Utwórz konto magazynu

Najpierw należy utworzyć konto magazynu, jeśli nie masz już. Aby uzyskać więcej informacji, zobacz [Utwórz nowe konto magazynu](storage-quickstart-create-account.md).

### <a name="step-2-enable-sse-for-blob-and-file-storage"></a>Krok 2: Włączanie SSE magazynu obiektów Blob i plików

Aby włączyć SSE przy użyciu kluczy zarządzany przez klienta, dwie funkcje ochrony kluczy, Usuń elastyczne i przeczyszczanie, również musi być włączony. Te ustawienia upewnij się, że klucze nie może być przypadkowo lub celowo usuniętymi. Maksymalny okres przechowywania kluczy wynosi 90 dni, ochrona użytkowników względem złośliwych osób lub ransomware ataki.

Jeśli chcesz programowo włączyć klucze zarządzany przez klienta dla SSE, można użyć [interfejsu API REST dostawcy zasobów magazynu Azure](https://docs.microsoft.com/rest/api/storagerp), [biblioteki klienta dostawcy zasobów magazynu dla platformy .NET](https://docs.microsoft.com/dotnet/api), [ Program Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview), lub [Azure CLI](https://docs.microsoft.com/azure/storage/storage-azure-cli).

Aby używać kluczy zarządzany przez klienta z SSE, należy przypisać tożsamość konta magazynu do konta magazynu. Można ustawić tożsamości, wykonując następujące polecenie programu PowerShell:

```powershell
Set-AzureRmStorageAccount -ResourceGroupName \$resourceGroup -Name \$accountName -AssignIdentity
```

Usuń elastyczne i przeczyszczanie można włączyć, wykonując następujące polecenia programu PowerShell:

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

### <a name="step-3-enable-encryption-with-customer-managed-keys"></a>Krok 3: Włączenie szyfrowania za pomocą kluczy zarządzany przez klienta

Domyślnie SSE używa kluczy zarządzany przez firmę Microsoft. Można włączyć SSE z kluczami zarządzany przez klienta dla konta magazynu za pomocą [portalu Azure](https://portal.azure.com/). Na **ustawienia** bloku dla konta magazynu, kliknij przycisk **szyfrowania**. Wybierz **Użyj własnego klucza** opcji, jak pokazano na poniższej ilustracji.

![Opcja szyfrowania portalu zrzut ekranu przedstawiający](./media/storage-service-encryption-customer-managed-keys/ssecmk1.png)

### <a name="step-4-select-your-key"></a>Krok 4: Wybierz klucz

Klucz można określić jako identyfikatora URI lub wybierając klucz z magazynu kluczy.

#### <a name="specify-a-key-as-a-uri"></a>Określ klucz jako identyfikatora URI

Aby określić klucz z identyfikatora URI, wykonaj następujące kroki:

1. Wybierz **klawisza Enter URI** opcji.  
2. W **identyfikator URI klucza** pola, określ identyfikator URI.

    ![Portalu zrzut ekranu przedstawiający szyfrowania z wprowadź identyfikator uri klucza opcji](./media/storage-service-encryption-customer-managed-keys/ssecmk2.png)


#### <a name="specify-a-key-from-a-key-vault"></a>Określ klucz z magazynu kluczy 

Aby określić klucz z magazynu kluczy, wykonaj następujące kroki:

1. Wybierz **wybierać Key Vault** opcji.  
2. Wybierz magazyn kluczy, zawierającego klucz, który ma być używany.
3. Wybierz klucz z magazynu kluczy.

    ![Szyfrowanie zrzut ekranu przedstawiający Portal opcja własnego klucza](./media/storage-service-encryption-customer-managed-keys/ssecmk3.png)

Konto magazynu nie ma dostępu do magazynu kluczy, po uruchomieniu polecenia programu Azure PowerShell pokazano na poniższej ilustracji, aby udzielić dostępu.

![Portal zrzut ekranu przedstawiający odmowa dostępu do magazynu kluczy](./media/storage-service-encryption-customer-managed-keys/ssecmk4.png)

Można również przyznać dostęp za pośrednictwem portalu Azure, przechodząc do usługi Azure Key Vault w portalu Azure i udzielanie dostępu do konta magazynu.


Klucz powyżej można skojarzyć z istniejącego konta magazynu przy użyciu następujących poleceń programu PowerShell:
```powershell
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount"
$keyVault = Get-AzureRmKeyVault -VaultName "mykeyvault"
$key = Get-AzureKeyVaultKey -VaultName $keyVault.VaultName -Name "keytoencrypt"
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVault.VaultName -ObjectId $storageAccount.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
Set-AzureRmStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName -AccountName $storageAccount.StorageAccountName -EnableEncryptionService "Blob" -KeyvaultEncryption -KeyName $key.Name -KeyVersion $key.Version -KeyVaultUri $keyVault.VaultUri
```


### <a name="step-5-copy-data-to-storage-account"></a>Krok 5: Kopiowanie danych do konta magazynu

Aby przenieść dane do nowego konta magazynu, dzięki czemu jest on zaszyfrowany. Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące szyfrowanie usługi Magazyn](storage-service-encryption.md#faq-for-storage-service-encryption).

### <a name="step-6-query-the-status-of-the-encrypted-data"></a>Krok 6: Zapytać o stan zaszyfrowanych danych

Zapytać o stan zaszyfrowanych danych.

## <a name="faq-for-sse-with-customer-managed-keys"></a>Często zadawane pytania dotyczące SSE z klientów zarządzanych — klawisze

**Pytanie: czy używany Magazyn w warstwie Premium; Czy można używać kluczy zarządzany przez klienta z SSE?**

A: tak SSE z kluczami zarządzany przez firmę Microsoft i zarządzany przez klienta jest obsługiwany w standardowych magazynu i Magazyn w warstwie Premium.

**Pytanie: czy można utworzyć nowego konta magazynu z SSE z kluczami zarządzany przez klienta, włączyć za pomocą programu Azure PowerShell i interfejsu wiersza polecenia Azure?**

Odp. Tak.

**Pytanie: jak bardziej usługi Azure Storage kosztów korzystania z kluczy zarządzany przez klienta z SSE?**

Odpowiedź: Brak koszt skojarzony przy użyciu usługi Azure Key Vault. Aby uzyskać więcej informacji, odwiedź stronę [klucza magazynu cennik](https://azure.microsoft.com/pricing/details/key-vault/). Nie ma żadnych dodatkowych kosztów dla SSE, który jest włączona dla wszystkich kont magazynu.

**Pytanie: czy czy można odwołać dostęp do kluczy szyfrowania?**

Odpowiedź: tak, w dowolnym momencie można odwołać dostęp. Istnieje kilka sposobów, aby odwołać dostęp do kluczy. Zapoznaj się [PowerShell magazynu kluczy Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) i [interfejsu wiersza polecenia Azure Key Vault](https://docs.microsoft.com/cli/azure/keyvault) więcej szczegółów. Odbieranie prawa dostępu zablokuje skutecznie dostęp do wszystkich obiektów blob na koncie magazynu jako konto klucz szyfrowania jest niedostępne dla magazynu Azure.

**Pytanie: czy można utworzyć konta magazynu i klucza w innym regionie?**

A: nie, konta magazynu i usługi Azure Key Vault i klucz muszą znajdować się w tym samym regionie.

**Pytanie: czy można włączyć klucze zarządzany przez klienta dla SSE podczas tworzenia konta magazynu?**

Odpowiedź: nie. Po utworzeniu konta magazynu, klucze tylko zarządzany przez firmę Microsoft są dostępne dla SSE. Aby używać kluczy zarządzany przez klienta, należy zaktualizować właściwości konta magazynu. Użyj REST lub jednej z bibliotek klienckich magazynu, aby programowo zaktualizować konta magazynu lub aktualizowanie właściwości konta magazynu przy użyciu portalu Azure po utworzeniu konta.

**Pytanie: czy można wyłączyć szyfrowania podczas korzystania z SSE kluczy zarządzany przez klienta?**

Odpowiedź: nie, nie można wyłączyć szyfrowania. Szyfrowanie jest włączone domyślnie dla wszystkich usług — obiekt Blob, plików, tabel i kolejek magazynu. Opcjonalnie można przełączyć się z klawiszy zarządzany przez firmę Microsoft przy użyciu kluczy zarządzany przez klienta i na odwrót.

**Pytanie: jest SSE domyślnie włączona, tworząc nowe konto magazynu?**

Odpowiedź: SSE jest włączone domyślnie dla wszystkich kont magazynu i wszystkie magazyny usług — obiekt Blob, plików, tabel i kolejek.

**Pytanie: nie można włączyć SSE za pomocą klawiszy zarządzany przez klienta na moim koncie magazynu.**

A: to konto magazynu platformy Azure Resource Manager? Klasycznych kont magazynu nie są obsługiwane z kluczami zarządzany przez klienta. SSE z kluczami zarządzany przez klienta można włączyć tylko dla kont magazynu Menedżera zasobów.

**Pytanie: jaka jest usuwania nietrwałego i przeczyszczanie? Należy włączyć to ustawienie, aby użyć SSE z kluczami zarządzany przez klienta?**

A: usunąć soft i przeczyszczanie musi być włączona SSE za pomocą kluczy zarządzany przez klienta. Te ustawienia upewnij się, że klucz nie jest ani przypadkowo lub celowo usuniętymi. Maksymalny okres przechowywania kluczy wynosi 90 dni, ochrona użytkowników przed atakami ransomware i złośliwych osób. Nie można wyłączyć to ustawienie.

**Pytanie: jest SSE z kluczami zarządzany przez klienta, dozwolone tylko w określonych regionach?**

Odpowiedź: SSE z kluczami zarządzany przez klienta jest dostępna we wszystkich regionach magazynu obiektów Blob i plików.

**Pytanie: jak można skontaktować się z osobą czy wystąpienia jakichkolwiek problemów lub chcesz wyrazić swoją opinię?**

Odpowiedź: kontaktu [ ssediscussions@microsoft.com ](mailto:ssediscussions@microsoft.com) wszelkie problemy związane z magazynu usługi szyfrowania.

## <a name="next-steps"></a>Kolejne kroki

-   Aby uzyskać więcej informacji na rozbudowany zestaw zabezpieczeń możliwości, które ułatwiają deweloperom tworzenie bezpiecznych aplikacji, zobacz [przewodnik zabezpieczeń magazynu](storage-security-guide.md).

-   Aby uzyskać przegląd informacji dotyczących usługi Azure Key Vault, zobacz [co to jest usługa Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?

-   W ramach wprowadzenia usługi Azure Key Vault, zobacz [wprowadzenie do korzystania z usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started).
