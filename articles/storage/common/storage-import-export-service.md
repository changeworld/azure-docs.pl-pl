---
title: Transfer danych do i z usługi Azure Storage za pomocą usługi Azure Import/Export | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć Importuj i Eksportuj zadania w witrynie Azure portal do przesyłania danych do i z usługi Azure Storage.
author: alkohli
manager: jeconnoc
services: storage
ms.service: storage
ms.topic: article
ms.date: 07/11/2018
ms.author: alkohli
ms.openlocfilehash: ab73420d1bfe0dbddcf2a0e3c3dd34203e4bb2d7
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008420"
---
# <a name="what-is-azure-importexport-service"></a>Co to jest usługa Azure Import/Export?

Usługa Azure Import/Export umożliwia bezpieczne importowania dużych ilości danych do usługi Azure Blob storage i plików platformy Azure przez wysyłanie dysków do centrum danych platformy Azure. Ta usługa może również przenoszenia danych z usługi Azure Blob storage do stacji dysków do wysłania do lokacji lokalnej. Do usługi Azure Blob storage lub Azure Files można zaimportować dane z co najmniej jeden dysk. 

Usługa Azure Import/Export, należy podać własne dyski. Jeśli chcesz przenieść dane przy użyciu dysków dostarczonych przez firmę Microsoft, można użyć dysku Azure Data Box do importowania danych do platformy Azure. Microsoft jest dostarczany do 5 zaszyfrowanych dysków półprzewodnikowych (SSD) o pojemności 40 TB — według kolejności, w centrum danych za pomocą operatora regionalne. Można szybko skonfigurować dyski, skopiować dane na dyskach za pośrednictwem połączenia USB 3.0 i odeślij dyski z powrotem do platformy Azure. Aby uzyskać więcej informacji, przejdź do [dysku Azure Data Box — omówienie](https://docs.microsoft.com/azure/databox/data-box-disk-overview).

## <a name="azure-importexport-usecases"></a>Usługa Azure Import/Export usecases

Należy wziąć pod uwagę przy użyciu usługi Azure Import/Export, gdy przekazanie lub pobranie danych za pośrednictwem sieci jest zbyt wolno lub pobieranie dodatkową przepustowość jest kosztowne. Korzystać z tej usługi, w następujących scenariuszach:

* **Migracja danych do chmury**: przenoszenie dużych ilości danych na platformę Azure szybko i ekonomiczne.
* **Dystrybucja zawartości**: szybko wysyłać dane do lokacji klientów.
* **Kopia zapasowa**: twórz kopie zapasowe danych lokalnych do przechowywania w usłudze Azure Storage.
* **Odzyskiwanie danych**: odzyskać dużą ilość danych przechowywanych w magazynie i z łatwością dostarczyć do Twojej lokalizacji lokalnej.

## <a name="importexport-components"></a>Import/Export składników

Usługa Import/Export używa następujących składników:

- **Import/Export**usługi: pomaga użytkownikom tworzenie i śledzenia, importowanie i eksportowanie zadań, tej usługi, które są dostępne w witrynie Azure portal.  

- **Narzędzie WAImportExport**: to narzędzie wiersza polecenia, które wykonuje następujące czynności: 
    - Przygotowuje dyski, które są dostarczane do importu.
    - Umożliwia kopiowanie danych na dysku.
    - Szyfruje dane na dysku za pomocą funkcji BitLocker.
    - Generuje pliki dziennika dysku, które są używane podczas tworzenia importu.
    - Ułatwia określenie liczby dysków wymaganych dla zadania eksportu.

    To narzędzie jest dostępne w dwóch wersji, wersji 1 i 2. Zaleca się, że używasz:

    - W wersji 1 usługi import/export do usługi Azure Blob storage. 
    - W wersji 2 do importowania danych do usługi Azure files.

    Narzędzie WAImportExport jest zgodna tylko z 64-bitowym systemie operacyjnym Windows. Aby uzyskać konkretne obsługiwane wersje systemu operacyjnego, przejdź do [wymagania dotyczące usługi Azure Import/Export](storage-import-export-requirements.md#supported-operating-systems).

- **Dyski**: mogą być dysków półprzewodnikowych (SSD) lub stacje dysków twardych (HDD) centrach danych platformy Azure. Podczas tworzenia zadania importu, są dostarczane dysków z danymi. Podczas tworzenia zadania eksportu, dostarczasz pustych dysków do centrum danych platformy Azure. Dla typów określonego dysku, przejdź do [obsługiwanych typów dysków](storage-import-export-requirements.md#supported-hardware).

## <a name="how-does-importexport-work"></a>Jak działa importu/eksportu?

Usługa Azure Import/Export umożliwia transfer danych do usługi Azure Files i obiektów blob Azure, tworząc zadania. Umożliwia utworzenie zadania w witrynie Azure portal lub interfejsu REST API usługi Azure Resource Manager. Każde zadanie jest skojarzone z jednego konta magazynu. 

Zadania mogą być importowania lub eksportowania zadania. Zadania importu umożliwia importowanie danych do obiektów blob platformy Azure lub usługi Azure files, natomiast zadanie eksportu umożliwia danych można wyeksportować za pomocą obiektów blob platformy Azure. Do zadania importu są dostarczane dyskach zawierających dane. Podczas tworzenia zadania eksportu dostarczasz pustych dysków do centrum danych platformy Azure. W każdym przypadku można wysłać do 10 dysków na zadanie.

> [!IMPORTANT]
> Eksportowanie danych do usługi Azure Files nie jest obsługiwane.

W tej sekcji wysokiego poziomu kroków zaangażowane w importu i opisano zadania eksportu. 


### <a name="inside-an-import-job"></a>Wewnątrz zadania importu

Na wysokim poziomie zadania importu obejmuje następujące czynności:

1. Określ dane mają być importowane, liczba dysków, których potrzebujesz, docelowej lokalizacji obiektu blob danych w usłudze Azure storage.
2. Narzędzie WAImportExport służy do kopiowania danych do stacji dysków. Szyfrowanie dysków funkcją BitLocker.
3. Tworzenie zadania importu na koncie magazynu docelowego w witrynie Azure portal. Przekazywanie plików dziennika dysku.
2. Podaj adres zwrotny i numer konta operatora na potrzeby wysyłki dysków do Ciebie.
3. Dostarczaj dysków na adres wysyłkowy podane podczas tworzenia zadania.
4. Zaktualizuj dostarczania śledzenia liczby w szczegółach zadania importu i przesłać zadanie importu.
5. Dyski są odbierane i przetwarzane w centrum danych platformy Azure.
6. Dyski są dostarczane za pomocą konta operatora adres zwrotny dostarczane w ramach zadania importu.
  
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
  
    ![Rysunek 2:Export zadania przepływu](./media/storage-import-export-service/exportjob.png)

Aby uzyskać instrukcje krok po kroku na eksport danych, przejdź do [eksportowanie danych z obiektów blob platformy Azure](storage-import-export-data-from-blobs.md).

## <a name="region-availability"></a>Dostępność w danym regionie 

Usługa Azure Import/Export obsługuje kopiowanie danych do i z wszystkich kont usługi Azure storage. Możesz przesłać dysków do jednego z wymienionych lokalizacjach. W przypadku konta magazynu w lokalizacji platformy Azure, która nie jest określona w tym miejscu, lokalizacji wysyłki alternatywne znajduje się po utworzeniu zadania.

### <a name="supported-shipping-locations"></a>Obsługiwane lokalizacji wysyłki


|Kraj  |Kraj  |Kraj  |Kraj  |
|---------|---------|---------|---------|
|Wschodnie stany USA    | Europa Północna        | Indie Środkowe        |Administracja USA — Iowa         |
|Zachodnie stany USA     |Europa Zachodnia         | Indie Południowe        | US DoD — wschodnie stany        |
|Wschodnie stany USA 2    | Azja Wschodnia        |  Indie Zachodnie        | US DoD — środkowe stany        |
|Zachodnie stany USA 2     | Azja Południowo-Wschodnia        | Kanada Środkowa        | Chiny Wschodnie         |
|Środkowe stany USA     | Australia Wschodnia        | Kanada Wschodnia        | Chiny Północne        |
|Środkowo-północne stany USA     |  Australia Południowo-Wschodnia       | Brazylia Południowa        | Południowe Zjednoczone Królestwo        |
|Środkowo-południowe stany USA     | Japonia Zachodnia        |Korea Środkowa         | Niemcy Środkowe        |
|Środkowo-zachodnie stany USA     |  Japonia Wschodnia       | Administracja USA — Wirginia        | Niemcy Północno-Wschodnie        |


## <a name="security-considerations"></a>Zagadnienia związane z zabezpieczeniami

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

Podczas importowania danych do usługi Azure Storage są bez kosztów transakcji, oprócz kosztów transakcji magazynu w warstwie standardowa. Opłaty za wyjście standardowe mają zastosowanie w przypadku, gdy dane są eksportowane z usługi Blob storage. Aby uzyskać więcej informacji na temat tego względu koszt transakcji, zobacz [cennikiem transferu danych.](https://azure.microsoft.com/pricing/details/data-transfers/)



## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak korzystać z usługi Import/Eksport do:
* [Importowanie danych do obiektów blob platformy Azure](storage-import-export-data-to-blobs.md)
* [Eksportowanie danych z obiektów blob platformy Azure](storage-import-export-data-from-blobs.md)
* [Importowanie danych do usługi Azure Files](storage-import-export-data-to-files.md)

