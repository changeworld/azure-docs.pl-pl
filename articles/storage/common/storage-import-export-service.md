---
title: Transfer danych do i z usługi Azure Storage za pomocą usługi Azure Import/Export | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć Importuj i Eksportuj zadania w witrynie Azure portal do przesyłania danych do i z usługi Azure Storage.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/07/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 5bb68f3f0139a98f10e28957209f8530a762b76c
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413903"
---
# <a name="what-is-azure-importexport-service"></a>Co to jest usługa Azure Import/Export?

Usługa Azure Import/Export umożliwia bezpieczne importowania dużych ilości danych do usługi Azure Blob storage i plików platformy Azure przez wysyłanie dysków do centrum danych platformy Azure. Ta usługa może również przenoszenia danych z usługi Azure Blob storage do stacji dysków do wysłania do lokacji lokalnej. Można zaimportować dane z jednego lub więcej dysków do usługi Azure Blob storage lub Azure Files. 

Podaj stacje dysków, a transfer danych za pomocą usługi Azure Import/Export. Można również użyć dysków dostarczonych przez firmę Microsoft. 

Jeśli chcesz przesyłać dane przy użyciu dysków twardych, dostarczone przez firmę Microsoft, możesz użyć [dysku Azure Data Box](../../databox/data-box-disk-overview.md) do importowania danych do platformy Azure. Microsoft jest dostarczany do 5 zaszyfrowanego dysku dysków półprzewodnikowych (SSD) z całkowitej pojemności 40 TB — według kolejności, w centrum danych za pomocą operatora regionalne. Można szybko skonfigurować stacje dysków, skopiować dane do stacji dysków przy użyciu połączenia USB 3.0 i dostarczanie z stacje dysków, wróć do platformy Azure. Aby uzyskać więcej informacji, przejdź do [dysku Azure Data Box — omówienie](../../databox/data-box-disk-overview.md).

## <a name="azure-importexport-usecases"></a>Usługa Azure Import/Export usecases

Należy wziąć pod uwagę przy użyciu usługi Azure Import/Export, gdy przekazanie lub pobranie danych za pośrednictwem sieci jest zbyt wolno lub pobieranie dodatkową przepustowość jest kosztowne. Korzystać z tej usługi, w następujących scenariuszach:

* **Migracja danych do chmury**: Przenoszenie dużych ilości danych na platformę Azure szybko i ekonomiczne.
* **Dystrybucja zawartości**: Szybko wysyłać dane do lokacji klientów.
* **Kopia zapasowa**: Twórz kopie zapasowe danych lokalnych, można przechowywać w usłudze Azure Storage.
* **Odzyskiwanie danych**: Odzyskaj dużą ilość danych przechowywanych w magazynie i z łatwością dostarczyć do Twojej lokalizacji lokalnej.

## <a name="importexport-components"></a>Import/Export składników

Usługa Import/Export używa następujących składników:

- **Usługa Import/Export**: Ta usługa, które są dostępne w witrynie Azure portal ułatwia użytkownikom tworzenie i śledzenie importu danych (przekazywanie) i eksportowanie zadań (Pobieranie).  

- **Narzędzie WAImportExport**: To narzędzie wiersza polecenia, które wykonuje następujące czynności: 
    - Przygotowuje dysków twardych, które są dostarczane do importu.
    - Umożliwia kopiowanie danych na dysku.
    - Szyfruje dane na dysku za pomocą funkcji BitLocker.
    - Generuje pliki dziennika dysku, które są używane podczas tworzenia importu.
    - Ułatwia określenie liczby dysków wymaganych dla zadania eksportu.
    
> [!NOTE]
> Narzędzie WAImportExport jest dostępne w dwóch wersji, wersji 1 i 2. Zaleca się, że używasz:
> - W wersji 1 usługi import/export do usługi Azure Blob storage. 
> - W wersji 2 do importowania danych do usługi Azure files.
>
> Narzędzie WAImportExport jest zgodna tylko z 64-bitowym systemie operacyjnym Windows. Aby uzyskać konkretne obsługiwane wersje systemu operacyjnego, przejdź do [wymagania dotyczące usługi Azure Import/Export](storage-import-export-requirements.md#supported-operating-systems).

- **Stacje dysków**: Możesz przesłać dysków półprzewodnikowych (SSD) lub z stacje dysków twardych (HDD) w centrach danych platformy Azure. Podczas tworzenia zadania importu, są dostarczane dysków z danymi. Podczas tworzenia zadania eksportu, dostarczasz pustych dysków do centrum danych platformy Azure. Dla typów określonego dysku, przejdź do [obsługiwanych typów dysków](storage-import-export-requirements.md#supported-hardware).

## <a name="how-does-importexport-work"></a>Jak działa importu/eksportu?

Usługa Azure Import/Export umożliwia transfer danych do usługi Azure Files i obiektów blob Azure, tworząc zadania. Umożliwia utworzenie zadania w witrynie Azure portal lub interfejsu REST API usługi Azure Resource Manager. Każde zadanie jest skojarzone z jednego konta magazynu. 

Zadania mogą być importowania lub eksportowania zadania. Zadania importu umożliwia importowanie danych do obiektów blob platformy Azure lub usługi Azure files, natomiast zadanie eksportu umożliwia danych można wyeksportować za pomocą obiektów blob platformy Azure. Do zadania importu są dostarczane dyskach zawierających dane. Podczas tworzenia zadania eksportu dostarczasz pustych dysków do centrum danych platformy Azure. W każdym przypadku można wysłać do 10 dysków na zadanie.

### <a name="inside-an-import-job"></a>Wewnątrz zadania importu

Na wysokim poziomie zadania importu obejmuje następujące czynności:

1. Określ dane mają być importowane, liczba dysków, których potrzebujesz, docelowej lokalizacji obiektu blob danych w usłudze Azure storage.
2. Narzędzie WAImportExport służy do kopiowania danych do stacji dysków. Szyfrowanie dysków funkcją BitLocker.
3. Tworzenie zadania importu na koncie magazynu docelowego w witrynie Azure portal. Przekazywanie plików dziennika dysku.
4. Podaj adres zwrotny i numer konta operatora na potrzeby wysyłki dysków do Ciebie.
5. Dostarczaj dysków na adres wysyłkowy podane podczas tworzenia zadania.
6. Zaktualizuj dostarczania śledzenia liczby w szczegółach zadania importu i przesłać zadanie importu.
7. Dyski są odbierane i przetwarzane w centrum danych platformy Azure.
8. Dyski są dostarczane za pomocą konta operatora adres zwrotny dostarczane w ramach zadania importu.

> [!NOTE]
> Wydania lokalnego (w ramach kraj centrum danych) udostępnianie konto przewoźnika wywiad krajowy 
>
> Dla wydań za granicą (poza krajem centrum danych) udostępnianie konto przewoźnika międzynarodowych

 ![Rysunek 1:Import zadania przepływu](./media/storage-import-export-service/importjob.png)

Instrukcje krok po kroku na danych, należy zaimportować, przejdź do:

- [Importowanie danych do obiektów blob platformy Azure](storage-import-export-data-to-blobs.md)
- [Importowanie danych do usługi Azure Files](storage-import-export-data-to-files.md)


### <a name="inside-an-export-job"></a>Wewnątrz zadania eksportu

> [!IMPORTANT]
> Usługa obsługuje tylko eksportu obiektów blob platformy Azure. Eksportowanie plików platformy Azure nie jest obsługiwana.

Na wysokim poziomie zadania eksportu obejmuje następujące czynności:

1. Określenie danych, które mają zostać wyeksportowane, liczba dysków można potrzebę, obiekty BLOB źródła lub ścieżki kontenera danych w magazynie obiektów Blob.
3. Tworzenie zadania eksportu na koncie magazynu źródła w witrynie Azure portal.
4. Określ źródła obiektów blob lub ścieżki kontenera dla danych do wyeksportowania.
5. Podaj adres zwrotny i numer konta operatora na potrzeby wysyłki dysków do Ciebie.
6. Dostarczaj dysków na adres wysyłkowy podane podczas tworzenia zadania.
7. Zaktualizuj dostarczania śledzenia liczby w szczegółach zadania eksportu, a następnie prześlij zadanie eksportu.
8. Dyski są odbierane i przetwarzane w centrum danych platformy Azure.
9. Dyski są szyfrowane za pomocą funkcji BitLocker i klucze są dostępne za pośrednictwem witryny Azure portal.  
10. Dyski są dostarczane za pomocą konta operatora adres zwrotny dostarczane w ramach zadania importu.

> [!NOTE]
> Wydania lokalnego (w ramach kraj centrum danych) udostępnianie konto przewoźnika wywiad krajowy 
>
> Dla wydań za granicą (poza krajem centrum danych) udostępnianie konto przewoźnika międzynarodowych
  
 ![Rysunek 2:Export zadania przepływu](./media/storage-import-export-service/exportjob.png)

Aby uzyskać instrukcje krok po kroku na eksport danych, przejdź do [eksportowanie danych z obiektów blob platformy Azure](storage-import-export-data-from-blobs.md).

## <a name="region-availability"></a>Dostępność regionalna 

Usługa Azure Import/Export obsługuje kopiowanie danych do i z wszystkich kont usługi Azure storage. Możesz przesłać dysków do jednego z wymienionych lokalizacjach. W przypadku konta magazynu w lokalizacji platformy Azure, która nie jest określona w tym miejscu, lokalizacji wysyłki alternatywne znajduje się po utworzeniu zadania.

### <a name="supported-shipping-locations"></a>Obsługiwane lokalizacji wysyłki


|Kraj  |Kraj  |Kraj  |Kraj  |
|---------|---------|---------|---------|
|Wschodnie stany USA    | Europa Północna        | Indie Środkowe        |US Gov Iowa         |
|Zachodnie stany USA     |Europa Zachodnia         | Indie Południowe        | Wschodnie stany USA — DoD        |
|Wschodnie stany USA 2    | Azja Wschodnia        |  Indie Zachodnie        | Środkowe stany USA — DoD        |
|Zachodnie stany USA 2     | Azja Południowo-Wschodnia        | Kanada Środkowa        | Chiny Wschodnie         |
|Środkowe stany USA     | Australia Wschodnia        | Kanada Wschodnia        | Chiny Północne        |
|Północno-środkowe stany USA     |  Australia Południowo-Wschodnia       | Brazylia Południowa        | Zjednoczone Królestwo (południe)        |
|Południowo-środkowe stany USA     | Japonia Zachodnia        |Korea Środkowa         | Niemcy Środkowe        |
|Zachodnio-środkowe stany USA     |  Japonia Wschodnia       | US Gov Wirginia        | Niemcy Północno-Wschodnie        |


## <a name="security-considerations"></a>Zagadnienia dotyczące bezpieczeństwa

Dane na dysku są szyfrowane za pomocą szyfrowania dysków funkcją BitLocker. To Szyfrowanie chroni dane, gdy są one przesyłane.

Zadań importu dyski są szyfrowane na dwa sposoby.  


- Wybierz opcję w przypadku korzystania z *dataset.csv* pliku podczas uruchamiania narzędzia WAImportExport podczas przygotowywania dysku. 

- Włącz szyfrowanie funkcją BitLocker ręcznie na dysku. Określ klucz szyfrowania w *driveset.csv* podczas uruchamiania wiersza polecenia narzędzia WAImportExport podczas przygotowywania dysku.


Dla zadania eksportu po skopiowaniu danych na dyskach, usługa szyfruje dysk przy użyciu funkcji BitLocker przed dostarczeniem jej do Ciebie. Klucz szyfrowania jest udostępniany za pośrednictwem witryny Azure portal.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]


### <a name="pricing"></a>Cennik

**Obsługa opłata za dysk**

Istnieje opłata za obsługę dysku dla każdego dysku przetworzonych w ramach importu lub eksportu zadania. Zobacz szczegóły w [cennik usługi Azure Import/Export](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Koszty wysyłki**

Podczas wydawania dysków na platformie Azure, naliczana jest opłata za wysyłkę do firmy przewozowej. Po powrocie z Microsoft dysków dla Ciebie koszt wysyłki jest obciążany opłatą za konta operatora, który dostarczony w czasie tworzenia zadania.

**Koszty transakcji**

[Opłata za transakcję magazynu w warstwie standardowa](https://azure.microsoft.com/pricing/details/storage/) stosowane podczas importowania, jak również Eksport danych. Opłaty za ruch wychodzący standardowe mają również zastosowanie, wraz z opłaty dotyczące transakcji magazynu, gdy dane są eksportowane z usługi Azure Storage. Aby uzyskać więcej informacji na koszty ruchu wychodzącego, zobacz [cennikiem transferu danych.](https://azure.microsoft.com/pricing/details/data-transfers/).



## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak korzystać z usługi Import/Eksport do:
* [Importowanie danych do obiektów blob platformy Azure](storage-import-export-data-to-blobs.md)
* [Eksportowanie danych z obiektów blob platformy Azure](storage-import-export-data-from-blobs.md)
* [Importowanie danych do usługi Azure Files](storage-import-export-data-to-files.md)

