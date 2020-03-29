---
title: Omówienie kont magazynu
titleSuffix: Azure Storage
description: Poznaj opcje tworzenia i używania konta usługi Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 3694a1d04ec25f44cfcf9395bdd148e2fd3c0d9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371561"
---
# <a name="storage-account-overview"></a>Omówienie kont magazynu

Konto magazynu platformy Azure zawiera wszystkie obiekty danych usługi Azure Storage: obiekty blob, pliki, kolejki, tabele i dyski. Konto magazynu udostępnia unikatowy obszar nazw dla danych usługi Azure Storage, który jest dostępny z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. Dane na koncie magazynu platformy Azure są trwałe i wysoce dostępne, bezpieczne i skalowalne.

Aby dowiedzieć się, jak utworzyć konto magazynu platformy Azure, zobacz [Tworzenie konta magazynu](storage-account-create.md).

## <a name="types-of-storage-accounts"></a>Typy kont magazynu

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

### <a name="general-purpose-v2-accounts"></a>Konta ogólnego przeznaczenia, wersja 2

Konta magazynu ogólnego przeznaczenia w wersji 2 obsługują najnowsze funkcje usługi Azure Storage i zawierają wszystkie funkcje kont magazynu ogólnego przeznaczenia w wersji 1 i blob. Konta ogólnego przeznaczenia w wersji 2 zapewniają najniższe ceny pojemności dla gigabajtów usługi Azure Storage, a także konkurencyjne w branży ceny transakcyjne. Konta magazynu ogólnego przeznaczenia w wersji 2 obsługują te usługi usługi Azure Storage:

- Obiekty BLOB (wszystkie typy: Blok, Dołącz, Strona)
- Jezioro Danych Gen2
- Pliki
- Dyski
- Kolejki
- Tabele

> [!NOTE]
> Firma Microsoft zaleca używanie konta magazynu ogólnego przeznaczenia w wersji 2 dla większości scenariuszy. Można łatwo uaktualnić ogólne przeznaczenie konta magazynu v1 lub blob do ogólnego przeznaczenia konta w wersji 2 bez przestojów i bez konieczności kopiowania danych.
>
> Aby uzyskać więcej informacji na temat uaktualniania do konta ogólnego przeznaczenia w wersji 2, zobacz [Uaktualnianie do konta magazynu ogólnego przeznaczenia w wersji 2](storage-account-upgrade.md).

Konta magazynu ogólnego przeznaczenia w wersji 2 oferują wiele warstw dostępu do przechowywania danych na podstawie wzorców użycia. Aby uzyskać więcej informacji, zobacz [Warstwy programu Access dla danych blokowych obiektów blob](#access-tiers-for-block-blob-data).

### <a name="general-purpose-v1-accounts"></a>Konta ogólnego przeznaczenia, wersja 1

Konta magazynu ogólnego przeznaczenia w wersji 1 zapewniają dostęp do wszystkich usług usługi Azure Storage, ale mogą nie mieć najnowszych funkcji lub najniższych cen za gigabajt. Konta magazynu ogólnego przeznaczenia w wersji 1 obsługują te usługi usługi Azure Storage:

- Obiekty blob (wszystkie typy)
- Pliki
- Dyski
- Kolejki
- Tabele

W większości przypadków należy używać kont ogólnego przeznaczenia w wersji 2. Dla następujących scenariuszy można użyć kont ogólnego przeznaczenia w wersji 1:

- Aplikacje wymagają klasycznego modelu wdrażania platformy Azure. Konta ogólnego przeznaczenia w wersji 2 i konta magazynu obiektów Blob obsługują tylko model wdrażania usługi Azure Resource Manager.

- Aplikacje są intensywnie korzystające z transakcji lub używają znacznej przepustowości replikacji geograficznej, ale nie wymagają dużej pojemności. W takim przypadku, ogólnego przeznaczenia v1 może być najbardziej ekonomiczny wybór.

- Używasz wersji [interfejsu API REST usług magazynu,](https://msdn.microsoft.com/library/azure/dd894041.aspx) która jest wcześniejsza niż 2014-02-14 lub biblioteki klienta z wersją mniejszą niż 4.x. Nie można uaktualnić aplikacji.

### <a name="blockblobstorage-accounts"></a>Konta BlockBlobStorage

Konto BlockBlobStorage to wyspecjalizowane konto magazynu w warstwie wydajności premium do przechowywania danych obiektów nieustrukturyzowanych jako blokowe obiekty blob lub dołączanie obiektów blob. W porównaniu z ogólnym przeznaczeniem kont v2 i BlobStorage konta BlockBlobStorage zapewniają niskie, spójne opóźnienia i wyższe stawki transakcji.

Konta BlockBlobStorage nie obsługują obecnie warstw do warstw gorących, chłodnych ani archiwizujących warstw dostępu. Ten typ konta magazynu nie obsługuje stronicowych obiektów blob, tabel ani kolejek.

### <a name="filestorage-accounts"></a>Konta FileStorage

Konto FileStorage to wyspecjalizowane konto magazynu używane do przechowywania i tworzenia udziałów plików premium. Ten rodzaj konta magazynu obsługuje pliki, ale nie blokują obiektów blob, dołączania obiektów blob, stronicowych obiektów blob, tabel ani kolejek.

Konta FileStorage oferują unikalne właściwości dedykowane wydajności, takie jak rozerwanie we/wy. Aby uzyskać więcej informacji na temat tych cech, zobacz [warstwy magazynu udziału plików w](../files/storage-files-planning.md#storage-tiers) przewodniku planowania plików.

## <a name="naming-storage-accounts"></a>Nazywanie kont magazynu

Podczas określania nazwy konta magazynu należy pamiętać o następujących regułach:

- Nazwy kont usługi Magazyn muszą mieć długość od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery.
- Nazwa konta magazynu musi być unikatowa w obrębie platformy Azure. Każde konto magazynu musi mieć inną nazwę.

## <a name="performance-tiers"></a>Warstwy wydajności

W zależności od typu utworzonego konta magazynu można wybierać między warstwami wydajności standardowej i premium.

### <a name="general-purpose-storage-accounts"></a>Konta magazynu ogólnego przeznaczenia

Konta magazynu ogólnego przeznaczenia mogą być skonfigurowane dla jednej z następujących warstw wydajności:

- Standardowa warstwa wydajności do przechowywania obiektów blob, plików, tabel, kolejek i dysków maszyn wirtualnych platformy Azure. Aby uzyskać więcej informacji na temat celów skalowalności dla standardowych kont magazynu, zobacz [Cele skalowalności dla standardowych kont magazynu](scalability-targets-standard-account.md).
- Warstwa wydajności premium do przechowywania dysków niezarządzanych maszyn wirtualnych. Firma Microsoft zaleca używanie dysków zarządzanych z maszynami wirtualnymi platformy Azure zamiast dysków niezarządzanych. Aby uzyskać więcej informacji na temat celów skalowalności dla warstwy wydajności premium, zobacz [Cele skalowalności dla kont magazynu obiektów blob strony premium.](../blobs/scalability-targets-premium-page-blobs.md)

### <a name="blockblobstorage-storage-accounts"></a>Konta magazynu BlockBlobStorage

Konta magazynu BlockBlobStorage zapewniają warstwę wydajności premium do przechowywania bloków obiektów blob i dołączania obiektów blob. Aby uzyskać więcej informacji, zobacz [Cele skalowalności dla kont magazynu obiektów blob bloków premium](../blobs/scalability-targets-premium-block-blobs.md).

### <a name="filestorage-storage-accounts"></a>Konta magazynu FileStorage

Konta magazynu FileStorage zapewniają warstwę wydajności premium dla udziałów plików platformy Azure. Aby uzyskać więcej informacji, zobacz [Usługi Azure Files skalowalności i docelowych wydajności](../files/storage-files-scale-targets.md).

## <a name="access-tiers-for-block-blob-data"></a>Warstwy dostępu dla danych obiektów blob bloku

Usługa Azure Storage udostępnia różne opcje uzyskiwania dostępu do danych blokowych obiektów blob na podstawie wzorców użycia. Każda warstwa dostępu w usłudze Azure Storage jest zoptymalizowana pod kątem określonego wzorca użycia danych. Wybierając odpowiednią warstwę dostępu dla swoich potrzeb, można przechowywać dane blokowego obiektu blob w najbardziej opłacalny sposób.

Dostępne warstwy dostępu to:

- **Warstwa Hot** access. Ta warstwa jest zoptymalizowana pod kątem częstego dostępu do obiektów na koncie magazynu. Uzyskiwanie dostępu do danych w warstwie gorąca jest najbardziej opłacalne, podczas gdy koszty magazynu są wyższe. Nowe konta magazynu są domyślnie tworzone w warstwie gorąca.
- Warstwa dostępu **Chłodny.** Ta warstwa jest zoptymalizowana pod kątem przechowywania dużych ilości danych, które są rzadko dostępne i przechowywane przez co najmniej 30 dni. Przechowywanie danych w warstwie chłodnej jest bardziej opłacalne, ale dostęp do tych danych może być droższy niż uzyskiwanie dostępu do danych w warstwie gorąca.
- Warstwa **Archiwum.** Ta warstwa jest dostępna tylko dla poszczególnych bloków obiektów blob. Warstwa archiwum jest zoptymalizowana pod kątem danych, które mogą tolerować kilka godzin opóźnienia pobierania i które pozostaną w warstwie archiwum przez co najmniej 180 dni. Warstwa archiwum jest najbardziej opłacalną opcją przechowywania danych. Jednak dostęp do tych danych jest droższy niż uzyskiwanie dostępu do danych w warstwach gorących lub chłodnych.

Jeśli nastąpi zmiana wzorca użycia danych, możesz przełączać się między tymi warstwami dostępu w dowolnym momencie. Aby uzyskać więcej informacji na temat warstw dostępu, zobacz [Azure Blob storage: hot, cool i archive access tiers](../blobs/storage-blob-storage-tiers.md).

> [!IMPORTANT]
> Zmiana warstwy dostępu dla istniejącego konta magazynu lub obiektu blob może spowodować dodatkowe opłaty. Aby uzyskać więcej informacji, zobacz [sekcję Rozliczanie konta magazynu](#storage-account-billing).

## <a name="redundancy"></a>Nadmiarowość

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

## <a name="encryption"></a>Szyfrowanie

Wszystkie dane na koncie magazynu są szyfrowane po stronie usługi. Aby uzyskać więcej informacji na temat szyfrowania, zobacz [Szyfrowanie usługi Azure Storage Service dla danych w spoczynku](storage-service-encryption.md).

## <a name="storage-account-endpoints"></a>Punkty końcowe konta usługi Storage

Konto magazynu zapewnia unikatową przestrzeń nazw na platformie Azure dla danych użytkownika. Każdy obiekt przechowywany w usłudze Azure Storage ma adres, który zawiera unikatową nazwę konta. Kombinacja nazwy konta i punktu końcowego usługi Azure Storage stanowi punkty końcowe konta magazynu.

Jeśli na przykład konto magazynu ogólnego przeznaczenia nosi nazwę *mystorageaccount,* domyślne punkty końcowe dla tego konta to:

- Magazyn obiektów blob:`https://*mystorageaccount*.blob.core.windows.net`
- Przechowywanie tabel:`https://*mystorageaccount*.table.core.windows.net`
- Magazyn kolejki:`https://*mystorageaccount*.queue.core.windows.net`
- Pliki platformy Azure:`https://*mystorageaccount*.file.core.windows.net`

> [!NOTE]
> Blokuj konta magazynu obiektów blob i obiektów blob uwidaczniają tylko punkt końcowy usługi obiektów Blob.

Skonstruuj adres URL dostępu do obiektu na koncie magazynu, dołączając lokalizację obiektu w koncie magazynu do punktu końcowego. Przykładowo adres obiektu Blob może mieć następujący format: http://*mojekontomagazynu*.blob.core.windows.net/*mojkontener*/*mojblob*.

Można również skonfigurować konto magazynu, aby używać domeny niestandardowej dla obiektów blob. Aby uzyskać więcej informacji, zobacz [Konfigurowanie niestandardowej nazwy domeny dla konta usługi Azure Storage](../blobs/storage-custom-domain-name.md).  

## <a name="control-access-to-account-data"></a>Kontrolowanie dostępu do danych konta

Domyślnie dane na Twoim koncie są dostępne tylko dla Ciebie, tj. właściciela konta. Masz kontrolę nad tym, kto może uzyskiwać dostęp do Twoich danych i jakie mają uprawnienia.

Każde żądanie złożone na koncie pamięci masowej musi być autoryzowane. Na poziomie usługi żądanie musi zawierać prawidłowy nagłówek *autoryzacji.* W szczególności ten nagłówek zawiera wszystkie informacje niezbędne dla usługi do sprawdzania poprawności żądania przed jego wykonaniem.

Możesz udzielić dostępu do danych na koncie magazynu przy użyciu dowolnej z następujących metod:

- **Usługa Azure Active Directory:** Użyj poświadczeń usługi Azure Active Directory (Azure AD), aby uwierzytelnić użytkownika, grupę lub inną tożsamość w celu uzyskania dostępu do danych obiektów blob i kolejek. Jeśli uwierzytelnianie tożsamości zakończy się pomyślnie, usługa Azure AD zwraca token do użycia podczas autoryzowania żądania do magazynu obiektów Blob platformy Azure lub magazynu kolejki. Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie dostępu do usługi Azure Storage przy użyciu usługi Azure Active Directory](storage-auth-aad.md).
- **Autoryzacja klucza współdzielonego:** Użyj klucza dostępu do konta magazynu do konstruowania ciągu połączenia, który aplikacja używa w czasie wykonywania, aby uzyskać dostęp do usługi Azure Storage. Wartości w ciągu połączenia są używane do konstruowania nagłówka *autoryzacji,* który jest przekazywany do usługi Azure Storage. Aby uzyskać więcej informacji, zobacz [Konfigurowanie ciągów połączeń usługi Azure Storage](storage-configure-connection-string.md).
- **Podpis dostępu współdzielonego:** Użyj podpisu dostępu udostępnionego, aby delegować dostęp do zasobów na koncie magazynu, jeśli nie używasz autoryzacji usługi Azure AD. Podpis dostępu współdzielonego to token, który hermetyzuje wszystkie informacje potrzebne do autoryzowania żądania do usługi Azure Storage w adresie URL. Można określić zasób magazynu, przyznane uprawnienia i interwał, w którym uprawnienia są prawidłowe jako część podpisu dostępu udostępnionego. Aby uzyskać więcej informacji, zobacz [Korzystanie z sygnatur dostępu współdzielonego (SAS)](storage-sas-overview.md).

> [!NOTE]
> Uwierzytelnianie użytkowników lub aplikacji przy użyciu poświadczeń usługi Azure AD zapewnia doskonałe bezpieczeństwo i łatwość użycia w stosunku do innych środków autoryzacji. Chociaż można nadal używać autoryzacji klucza udostępnionego z aplikacjami, przy użyciu usługi Azure AD omija potrzebę przechowywania klucza dostępu do konta z kodem. Można również nadal używać podpisów dostępu współdzielonego (SAS) do udzielania szczegółowego dostępu do zasobów na koncie magazynu, ale usługa Azure AD oferuje podobne możliwości bez konieczności zarządzania tokenami sygnatury dostępu Współdzielonego lub martwienia się o odwołanie zagrożonego sygnatury dostępu Współdzielonego.
>
> Firma Microsoft zaleca używanie autoryzacji usługi Azure AD dla aplikacji obiektów blob i kolejek usługi Azure Storage, jeśli to możliwe.

## <a name="copying-data-into-a-storage-account"></a>Kopiowanie danych na konto magazynu

Firma Microsoft udostępnia narzędzia i biblioteki do importowania danych z lokalnych urządzeń magazynujących lub dostawców magazynu w chmurze innych firm. To, którego rozwiązania używasz, zależy od ilości przesyłanych danych.

Po uaktualnieniu do konta ogólnego przeznaczenia w wersji 2 z ogólnego przeznaczenia konta magazynu v1 lub blob dane są automatycznie migrowane. Firma Microsoft zaleca tę ścieżkę uaktualniania konta. Jeśli jednak zdecydujesz się przenieść dane z konta ogólnego przeznaczenia w wersji 1 na konto magazynu obiektów Blob, przeprowadzisz dane ręcznie, korzystając z narzędzi i bibliotek opisanych poniżej.

### <a name="azcopy"></a>Narzędzie AzCopy

Narzędzie AzCopy to narzędzie wiersza polecenia systemu Windows przeznaczone do kopiowania z wysoką wydajnością danych z i do usługi Azure Storage. Za pomocą programu AzCopy można kopiować dane do konta magazynu obiektów Blob z istniejącego konta magazynu ogólnego przeznaczenia lub przesyłać dane z lokalnych urządzeń magazynujących. Aby uzyskać więcej informacji, zobacz [Transfer danych za pomocą narzędzia wiersza polecenia AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="data-movement-library"></a>Biblioteka przenoszenia danych

Biblioteka przenoszenia danych usługi Azure Storage dla programu .NET jest oparta na podstawowym środowisku przenoszenia danych, w którym działa narzędzie AzCopy. Biblioteka została zaprojektowana na potrzeby przeprowadzania wysokowydajnych, niezawodnych i prostych operacji transferu danych w sposób podobny do narzędzia AzCopy. Biblioteka przenoszenia danych służy do korzystania z funkcji AzCopy natywnie. Aby uzyskać więcej informacji, zobacz [Biblioteka przenoszenia danych usługi Azure Storage dla platformy .NET](https://github.com/Azure/azure-storage-net-data-movement)

### <a name="rest-api-or-client-library"></a>Interfejs API REST lub biblioteka klienta

Można utworzyć niestandardową aplikację do migracji danych z konta magazynu ogólnego przeznaczenia w wersji 1 na konto magazynu obiektów Blob. Użyj jednej z bibliotek klienta platformy Azure lub interfejsu API REST usług magazynu platformy Azure. Usługa Azure Storage udostępnia rozbudowane biblioteki dla wielu języków programowania i platform, takich jak .NET, Java, C++, Node.JS, PHP, Ruby i Python. Biblioteki klienta oferują zaawansowane możliwości, takie jak logika ponowień, rejestrowanie i przekazywanie równoległe. Możliwe jest również programowanie bezpośrednio przy użyciu interfejsu API REST, który może być wywoływany przez dowolny język programowania mający możliwość wysyłania żądań HTTP lub HTTPS.

Aby uzyskać więcej informacji na temat interfejsu API REST usługi Azure Storage, zobacz [Odwołanie interfejsu API REST usług Usług magazynu platformy Azure .](https://docs.microsoft.com/rest/api/storageservices/)

> [!IMPORTANT]
> Obiekty blob zaszyfrowane za pomocą szyfrowania po stronie klienta przechowują metadane związane z szyfrowaniem w ramach obiektu blob. W przypadku kopiowania obiektu blob zaszyfrowanego przy użyciu szyfrowania po stronie klienta upewnij się, że podczas operacji kopiowania są zachowywane metadane obiektu blob, a w szczególności metadane związane z szyfrowaniem. W przypadku kopiowania obiektu blob bez metadanych szyfrowania nie można ponownie pobrać zawartości tego obiektu. Aby uzyskać więcej informacji na temat metadanych związanych z szyfrowaniem, zobacz [Azure Storage Client-Side Encryption (Szyfrowanie po stronie klienta usługi Azure Storage)](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="storage-account-billing"></a>Rozliczanie konta usługi Storage

[!INCLUDE [storage-account-billing-include](../../../includes/storage-account-billing-include.md)]

## <a name="next-steps"></a>Następne kroki

- [Tworzenie konta magazynu](storage-account-create.md)
- [Tworzenie konta magazynu blokowych obiektów blob](../blobs/storage-blob-create-account-block-blob.md)
