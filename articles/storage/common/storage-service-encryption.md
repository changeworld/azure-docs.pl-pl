---
title: Szyfrowanie usługi Azure Storage dla danych magazynowanych
description: Usługa Azure Storage chroni dane, automatycznie szyfrując je przed ich utrwaleniem do chmury. Możesz polegać na kluczach zarządzanych przez firmę Microsoft na potrzeby szyfrowania konta magazynu lub można zarządzać szyfrowaniem przy użyciu własnych kluczy.
services: storage
author: tamram
ms.service: storage
ms.date: 11/26/2019
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 63fa30b4cf4c5887e8fb44b357eb22e55fe230e7
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666141"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Szyfrowanie usługi Azure Storage dla danych magazynowanych

Usługa Azure Storage automatycznie szyfruje dane, gdy zostaną utrwalone w chmurze. Szyfrowanie usługi Azure Storage chroni dane i pomaga sprostać wymaganiom bezpieczeństwa i zgodności w organizacji.

## <a name="about-azure-storage-encryption"></a>Informacje o szyfrowaniu usługi Azure Storage

Dane w usłudze Azure Storage są szyfrowane i odszyfrowywane w sposób niewidoczny dla użytkownika przy użyciu 256-bitowego [szyfrowania AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), jednego z najsilniejszych szyfrów blokowych i jest zgodny ze standardem FIPS 140-2. Szyfrowanie usługi Azure Storage jest podobne do szyfrowania funkcją BitLocker w systemie Windows.

Szyfrowanie usługi Azure Storage jest włączone dla wszystkich nowych kont magazynu, w tym kont magazynu Menedżer zasobów i klasycznych. Nie można wyłączyć szyfrowania usługi Azure Storage. Ponieważ dane są zabezpieczone domyślnie, nie trzeba modyfikować kodu ani aplikacji, aby korzystać z szyfrowania usługi Azure Storage.

Konta magazynu są szyfrowane niezależnie od ich warstwy wydajności (standardowa lub Premium) lub modelu wdrażania (Azure Resource Manager lub klasyczne). Wszystkie opcje nadmiarowości usługi Azure Storage obsługują szyfrowanie, a wszystkie kopie konta magazynu są szyfrowane. Wszystkie zasoby usługi Azure Storage są szyfrowane, w tym obiektów blob, dysków, plików, kolejek i tabel. Wszystkie metadane obiektu są również szyfrowane.

Szyfrowanie nie ma wpływu na wydajność usługi Azure Storage. Nie ma dodatkowych opłat za szyfrowanie usługi Azure Storage.

Każdy blokowy obiekt BLOB, dołączany obiekt BLOB lub stronicowy obiekt BLOB, który został zapisany w usłudze Azure Storage po 20 października 2017, jest szyfrowany. Obiekty blob utworzone przed tą datą nadal są szyfrowane przez proces w tle. Aby wymusić szyfrowanie obiektu BLOB, który został utworzony przed 20 października 2017, można ponownie napisać obiekt BLOB. Aby dowiedzieć się, jak sprawdzić stan szyfrowania obiektu BLOB, zobacz [Sprawdzanie stanu szyfrowania obiektu BLOB](../blobs/storage-blob-encryption-status.md).

Aby uzyskać więcej informacji na temat modułów kryptograficznych związanych z szyfrowaniem usługi Azure Storage, zobacz [interfejs API kryptografii: Kolejna generacja](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

## <a name="about-encryption-key-management"></a>Informacje o zarządzaniu kluczami szyfrowania

Możesz polegać na kluczach zarządzanych przez firmę Microsoft na potrzeby szyfrowania konta magazynu lub można zarządzać szyfrowaniem przy użyciu własnych kluczy. W przypadku wybrania opcji zarządzania szyfrowaniem przy użyciu własnych kluczy dostępne są dwie opcje:

- *Klucz zarządzany przez klienta* można określić za pomocą Azure Key Vault do szyfrowania i odszyfrowywania wszystkich danych na koncie magazynu. Klucz zarządzany przez klienta służy do szyfrowania wszystkich danych we wszystkich usługach na koncie magazynu.
- *Klucz dostarczony przez klienta* można określić w operacjach magazynu obiektów BLOB. Klient wykonujący żądanie odczytu lub zapisu w usłudze BLOB Storage może dołączyć klucz szyfrowania żądania, aby uzyskać szczegółową kontrolę nad sposobem szyfrowania i odszyfrowywania danych obiektów BLOB.

Poniższa tabela zawiera porównanie opcji zarządzania kluczami dla szyfrowania usługi Azure Storage.

|                                        |    Klucze zarządzane przez firmę Microsoft                             |    Klucze zarządzane przez klienta                                                                                                                        |    Klucze dostarczone przez klienta                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    Operacje szyfrowania/odszyfrowywania    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    Obsługiwane usługi Azure Storage    |    Wszystko                                                |    BLOB Storage, Azure Files                                                                                                               |    Magazyn obiektów Blob                                                                  |
|    Magazyn kluczy                         |    Magazyn kluczy firmy Microsoft    |    Azure Key Vault                                                                                                                              |    Azure Key Vault lub dowolnego innego magazynu kluczy                                                                 |
|    Odpowiedzialność za kluczowe rotacje         |    Microsoft                                          |    Klient                                                                                                                                     |    Klient                                                                      |
|    Użycie klucza                           |    Microsoft                                          |    Azure Portal, interfejs API REST dostawcy zasobów magazynu, biblioteki zarządzania usługi Azure Storage, PowerShell, interfejs wiersza polecenia        |    Interfejs API REST usługi Azure Storage (BLOB Storage), biblioteki klienta usługi Azure Storage    |
|    Dostęp do klucza                          |    Tylko firma Microsoft                                     |    Firma Microsoft, klient                                                                                                                    |    Tylko klient                                                                 |

W poniższych sekcjach opisano każdą z opcji zarządzania kluczami w bardziej szczegółowy sposób.

## <a name="microsoft-managed-keys"></a>Klucze zarządzane przez firmę Microsoft

Domyślnie konto magazynu używa zarządzanych przez firmę Microsoft kluczy szyfrowania. Ustawienia szyfrowania dla konta magazynu można wyświetlić w sekcji **szyfrowanie** [Azure Portal](https://portal.azure.com), jak pokazano na poniższej ilustracji.

![Wyświetlanie konta zaszyfrowanego przy użyciu kluczy zarządzanych przez firmę Microsoft](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

## <a name="customer-managed-keys-with-azure-key-vault"></a>Klucze zarządzane przez klienta z Azure Key Vault

Możesz zarządzać szyfrowaniem usługi Azure Storage na poziomie konta magazynu przy użyciu własnych kluczy. W przypadku określenia klucza zarządzanego przez klienta na poziomie konta magazynu ten klucz jest używany do szyfrowania i odszyfrowywania wszystkich danych na koncie magazynu, w tym obiektów blob, kolejek, plików i danych tabeli. Klucze zarządzane przez klienta zapewniają większą elastyczność tworzenia, obracania, wyłączania i odwoływania kontroli dostępu. Możesz również przeprowadzać inspekcję kluczy szyfrowania używanych do ochrony danych.

Aby przechowywać klucze zarządzane przez klienta, należy użyć Azure Key Vault. Możesz utworzyć własne klucze i zapisać je w magazynie kluczy lub użyć Azure Key Vault interfejsów API do wygenerowania kluczy. Konto magazynu i Magazyn kluczy muszą znajdować się w tym samym regionie, ale mogą znajdować się w różnych subskrypcjach. Aby uzyskać więcej informacji na temat Azure Key Vault, zobacz [co to jest Azure Key Vault?](../../key-vault/key-vault-overview.md).

Ten diagram przedstawia sposób, w jaki usługa Azure Storage używa Azure Active Directory i Azure Key Vault do wykonywania żądań przy użyciu klucza zarządzanego przez klienta:

![Diagram przedstawiający sposób działania kluczy zarządzanych przez klienta w usłudze Azure Storage](media/storage-service-encryption/encryption-customer-managed-keys-diagram.png)

Poniższa lista zawiera opis kroków w diagramie:

1. Administrator Azure Key Vault przyznaje uprawnienia do kluczy szyfrowania do zarządzanej tożsamości skojarzonej z kontem magazynu.
2. Administrator usługi Azure Storage konfiguruje szyfrowanie za pomocą klucza zarządzanego przez klienta dla konta magazynu.
3. Usługa Azure Storage korzysta z zarządzanej tożsamości skojarzonej z kontem magazynu w celu uwierzytelniania dostępu do Azure Key Vault za pośrednictwem Azure Active Directory.
4. Usługa Azure Storage zawija klucz szyfrowania konta przy użyciu klucza klienta w Azure Key Vault.
5. W przypadku operacji odczytu/zapisu usługa Azure Storage wysyła żądania do Azure Key Vault, aby zawijać i odpakowywanie klucz szyfrowania konta w celu wykonywania operacji szyfrowania i odszyfrowywania.

### <a name="enable-customer-managed-keys-for-a-storage-account"></a>Włączanie kluczy zarządzanych przez klienta dla konta magazynu

Po włączeniu szyfrowania z kluczami zarządzanymi przez klienta dla konta magazynu usługa Azure Storage zawija klucz szyfrowania konta przy użyciu klucza klienta w skojarzonym magazynie kluczy. Włączenie kluczy zarządzanych przez klienta nie ma wpływu na wydajność, a konto jest szyfrowane z nowym kluczem natychmiast, bez opóźnień.

Nowe konto magazynu zawsze jest szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. W momencie tworzenia konta nie można włączyć kluczy zarządzanych przez klienta. Klucze zarządzane przez klienta są przechowywane w Azure Key Vault, a Magazyn kluczy musi być zainicjowany przy użyciu zasad dostępu, które przyznają kluczowe uprawnienia do zarządzanej tożsamości skojarzonej z kontem magazynu. Tożsamość zarządzana jest dostępna tylko po utworzeniu konta magazynu.

Aby dowiedzieć się, jak używać kluczy zarządzanych przez klienta z Azure Key Vaultm do szyfrowania usługi Azure Storage, zobacz jeden z następujących artykułów:

- [Skonfiguruj klucze zarządzane przez klienta przy użyciu Key Vault na potrzeby szyfrowania usługi Azure Storage z poziomu Azure Portal](storage-encryption-keys-portal.md)
- [Konfigurowanie kluczy zarządzanych przez klienta przy użyciu Key Vault na potrzeby szyfrowania usługi Azure Storage za pomocą programu PowerShell](storage-encryption-keys-powershell.md)
- [Konfigurowanie kluczy zarządzanych przez klienta przy użyciu Key Vault na potrzeby szyfrowania usługi Azure Storage z poziomu interfejsu wiersza polecenia platformy Azure](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> Klucze zarządzane przez klienta korzystają z zarządzanych tożsamości dla zasobów platformy Azure, funkcji Azure Active Directory (Azure AD). Po skonfigurowaniu kluczy zarządzanych przez klienta w Azure Portal, zarządzana tożsamość zostanie automatycznie przypisana do konta magazynu w obszarze okładek. Jeśli później przeniesiesz subskrypcję, grupę zasobów lub konto magazynu z jednego katalogu usługi Azure AD do innego, zarządzana tożsamość skojarzona z kontem magazynu nie zostanie przetransferowana do nowej dzierżawy, więc klucze zarządzane przez klienta mogą przestać działać. Aby uzyskać więcej informacji, zobacz **transfer subskrypcji między katalogami usługi Azure AD** w [często zadawanych pytaniach i znanych problemach z tożsamościami zarządzanymi dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Przechowuj klucze zarządzane przez klienta w Azure Key Vault

Aby włączyć klucze zarządzane przez klienta na koncie magazynu, należy użyć Azure Key Vault do przechowywania kluczy. Należy włączyć zarówno właściwości **nietrwałego usuwania** , jak i **nie przeczyszczania** w magazynie kluczy.

Magazyn kluczy musi znajdować się w tej samej subskrypcji co konto magazynu. Usługa Azure Storage używa zarządzanych tożsamości dla zasobów platformy Azure do uwierzytelniania w magazynie kluczy na potrzeby operacji szyfrowania i odszyfrowywania. Tożsamości zarządzane nie obsługują obecnie scenariuszy między katalogami.

### <a name="rotate-customer-managed-keys"></a>Obróć klucze zarządzane przez klienta

Klucz zarządzany przez klienta można obrócić w Azure Key Vault zgodnie z zasadami zgodności. Gdy klucz jest obrócony, należy zaktualizować konto magazynu, aby używało nowego identyfikatora URI klucza. Aby dowiedzieć się, jak zaktualizować konto magazynu tak, aby używało nowej wersji klucza w Azure Portal, zapoznaj się z sekcją **Aktualizacja wersji klucza** w artykule [Konfigurowanie kluczy zarządzanych przez klienta usługi Azure storage przy użyciu Azure Portal](storage-encryption-keys-portal.md).

Obracanie klucza nie wyzwala ponownego szyfrowania danych na koncie magazynu. Od użytkownika nie są wymagane żadne dalsze działania.

### <a name="revoke-access-to-customer-managed-keys"></a>Odwołaj dostęp do kluczy zarządzanych przez klienta

Aby odwołać dostęp do kluczy zarządzanych przez klienta, należy użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Azure Key Vault PowerShell](/powershell/module/az.keyvault//) lub [interfejs wiersza polecenia Azure Key Vault](/cli/azure/keyvault). Odwoływanie dostępu skutecznie blokuje dostęp do wszystkich danych na koncie magazynu, ponieważ klucz szyfrowania jest niedostępny przez usługę Azure Storage.

### <a name="customer-managed-keys-for-azure-managed-disks-preview"></a>Klucze zarządzane przez klienta dla usługi Azure Managed disks (wersja zapoznawcza)

Klucze zarządzane przez klienta są również dostępne do zarządzania szyfrowaniem usługi Azure Managed disks (wersja zapoznawcza). Klucze zarządzane przez klienta działają inaczej niż w przypadku zasobów usługi Azure Storage. Aby uzyskać więcej informacji, zobacz [szyfrowanie po stronie serwera dla usługi Azure Managed disks](../../virtual-machines/windows/disk-encryption.md) dla systemu Windows lub [szyfrowanie po stronie serwera Azure Managed disks](../../virtual-machines/linux/disk-encryption.md) for Linux.

## <a name="customer-provided-keys-preview"></a>Klucze dostarczone przez klienta (wersja zapoznawcza)

Klienci, którzy wysyłają żądania do usługi Azure Blob Storage, mają możliwość zapewnienia klucza szyfrowania dla pojedynczego żądania. Dołączenie klucza szyfrowania żądania zapewnia szczegółową kontrolę nad ustawieniami szyfrowania operacji usługi BLOB Storage. Klucze dostarczone przez klienta (wersja zapoznawcza) mogą być przechowywane w Azure Key Vault lub w innym magazynie kluczy.

Przykład pokazujący, jak określić klucz dostarczony przez klienta w żądaniu do magazynu obiektów blob, zobacz temat [Określanie klucza dostarczonego przez klienta w żądaniu usługi BLOB Storage za pomocą platformy .NET](../blobs/storage-blob-customer-provided-key.md). 

### <a name="encrypting-read-and-write-operations"></a>Szyfrowanie operacji odczytu i zapisu

Gdy aplikacja kliencka udostępnia klucz szyfrowania w żądaniu, usługa Azure Storage wykonuje szyfrowanie i odszyfrowywanie w niewidoczny sposób podczas odczytywania i zapisywania danych obiektów BLOB. Usługa Azure Storage zapisuje skrót SHA-256 klucza szyfrowania obok zawartości obiektu BLOB. Skrót jest używany do sprawdzania, czy wszystkie kolejne operacje dotyczące obiektu BLOB używają tego samego klucza szyfrowania. 

Usługa Azure Storage nie przechowuje klucza szyfrowania, który jest wysyłany przez klienta wraz z żądaniem, ani nie zarządza nim. Klucz jest bezpiecznie odrzucony zaraz po zakończeniu procesu szyfrowania lub odszyfrowywania.

Gdy klient tworzy lub aktualizuje obiekt BLOB przy użyciu klucza dostarczonego przez klienta, wówczas kolejne żądania odczytu i zapisu dla tego obiektu BLOB muszą również dostarczyć klucz. Jeśli nie podano klucza dla żądania obiektu BLOB, który został już zaszyfrowany za pomocą klucza dostarczonego przez klienta, żądanie kończy się niepowodzeniem z kodem błędu 409 (konflikt).

Jeśli aplikacja kliencka wysyła klucz szyfrowania na żądanie, a konto magazynu jest również szyfrowane przy użyciu klucza zarządzanego przez firmę Microsoft lub klucza zarządzanego przez klienta, usługa Azure Storage korzysta z klucza podanego w żądaniu szyfrowania i odszyfrowywania.

Aby wysłać klucz szyfrowania w ramach żądania, klient musi nawiązać bezpieczne połączenie z usługą Azure Storage przy użyciu protokołu HTTPS.

Każda migawka obiektu BLOB może mieć własny klucz szyfrowania.

### <a name="request-headers-for-specifying-customer-provided-keys"></a>Nagłówki żądań określające klucze dostarczone przez klienta

W przypadku wywołań REST klienci mogą używać następujących nagłówków do bezpiecznego przekazywania informacji o kluczu szyfrowania na żądanie do magazynu obiektów blob:

|Nagłówek żądania | Opis |
|---------------|-------------|
|`x-ms-encryption-key` |Wymagane dla żądań zapisu i odczytu. Zakodowana algorytmem Base64 wartość klucza szyfrowania AES-256. |
|`x-ms-encryption-key-sha256`| Wymagane dla żądań zapisu i odczytu. SHA256 szyfrowany algorytmem Base64 klucza szyfrowania. |
|`x-ms-encryption-algorithm` | Wymagane w przypadku żądań zapisu, które są opcjonalne w przypadku żądań odczytu. Określa algorytm używany do szyfrowania danych przy użyciu podanego klucza. Musi być AES256. |

Określanie kluczy szyfrowania w żądaniu jest opcjonalne. Jednak w przypadku określenia jednego z nagłówków wymienionych powyżej dla operacji zapisu należy określić wszystkie z nich.

### <a name="blob-storage-operations-supporting-customer-provided-keys"></a>Operacje magazynu obiektów BLOB obsługujące klucze dostarczone przez klienta

Następujące operacje magazynu obiektów BLOB obsługują wysyłanie kluczy szyfrowania dostarczonych przez klienta na żądanie:

- [Umieść obiekt BLOB](/rest/api/storageservices/put-blob)
- [Umieść listę zablokowanych](/rest/api/storageservices/put-block-list)
- [Umieść blok](/rest/api/storageservices/put-block)
- [Umieść blok z adresu URL](/rest/api/storageservices/put-block-from-url)
- [Umieść stronę](/rest/api/storageservices/put-page)
- [Umieść stronę na podstawie adresu URL](/rest/api/storageservices/put-page-from-url)
- [Dołącz blok](/rest/api/storageservices/append-block)
- [Ustawianie właściwości obiektu BLOB](/rest/api/storageservices/set-blob-properties)
- [Ustawianie metadanych obiektu BLOB](/rest/api/storageservices/set-blob-metadata)
- [Pobierz obiekt BLOB](/rest/api/storageservices/get-blob)
- [Pobierz właściwości obiektu BLOB](/rest/api/storageservices/get-blob-properties)
- [Pobierz metadane obiektu BLOB](/rest/api/storageservices/get-blob-metadata)
- [Obiekt BLOB migawek](/rest/api/storageservices/snapshot-blob)

### <a name="rotate-customer-provided-keys"></a>Obróć klucze dostarczone przez klienta

Aby obrócić klucz szyfrowania przekazany na żądanie, Pobierz obiekt BLOB i przekazanie go z nowym kluczem szyfrowania.

> [!IMPORTANT]
> Nie można użyć Azure Portal do odczytu lub zapisu do kontenera lub obiektu BLOB zaszyfrowanego przy użyciu klucza dostarczonego w żądaniu.
>
> Pamiętaj, aby chronić klucz szyfrowania udostępniany w żądaniu do magazynu obiektów BLOB w bezpiecznym magazynie kluczy, takim jak Azure Key Vault. Jeśli podjęto próbę wykonania operacji zapisu w kontenerze lub obiekcie blob bez klucza szyfrowania, operacja zakończy się niepowodzeniem i utracisz dostęp do obiektu.

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Szyfrowanie za pomocą usługi Azure Storage a szyfrowanie dysków

Szyfrowanie usługi Azure Storage szyfruje stronicowe obiekty blob, które wykonują kopie zapasowe dysków maszyny wirtualnej platformy Azure. Ponadto wszystkie dyski maszyn wirtualnych platformy Azure, w tym lokalne dyski tymczasowe, mogą być opcjonalnie zaszyfrowane za pomocą [Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md). Azure Disk Encryption korzysta z standardowego w branży [funkcji BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) w systemach Windows i [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) w systemie Linux w celu zapewnienia opartych na systemie operacyjnym rozwiązań do szyfrowania zintegrowanych z programem Azure Key Vault.

## <a name="next-steps"></a>Następne kroki

- [Co to jest Azure Key Vault?](../../key-vault/key-vault-overview.md)
- [Configure customer-managed keys for Azure Storage encryption from the Azure portal (Konfigurowanie kluczy zarządzanych przez klienta w celu szyfrowania usługi Azure Storage w witrynie Azure Portal)](storage-encryption-keys-portal.md)
- [Configure customer-managed keys for Azure Storage encryption from PowerShell (Konfigurowanie kluczy zarządzanych przez klienta w celu szyfrowania usługi Azure Storage za pomocą programu PowerShell)](storage-encryption-keys-powershell.md)
- [Configure customer-managed keys for Azure Storage encryption from Azure CLI (Konfigurowanie kluczy zarządzanych przez klienta w celu szyfrowania usługi Azure Storage za pomocą interfejsu wiersza polecenia platformy Azure)](storage-encryption-keys-cli.md)
