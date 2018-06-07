---
title: Przy użyciu Import/Eksport Azure na przesyłanie danych do i z usługi Azure Storage | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia importowania i eksportowania zadania w portalu Azure do przesyłania danych do i z usługi Azure Storage.
author: alkohli
manager: jeconnoc
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.openlocfilehash: 83ba437e699eb150e86e6c89e478377394966419
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34809546"
---
# <a name="what-is-azure-importexport-service"></a>Co to jest usługa Import/Eksport Azure?

Usługi Import/Eksport Azure umożliwia bezpieczne zaimportować dużych ilości danych do magazynu obiektów Blob platformy Azure i usługi pliki Azure poprzez wysyłanie dysków do centrum danych Azure. Ta usługa mogą służyć do przesyłania danych z magazynu obiektów Blob platformy Azure na dyskach i wysłać do lokalnych witryn. Dane z co najmniej jeden dysk można zaimportować do magazynu obiektów Blob platformy Azure lub usługi pliki Azure. 

## <a name="azure-importexport-usecases"></a>Azure usecases Import/Eksport

Należy rozważyć użycie usługi Import/Eksport Azure przekazanie lub pobranie danych za pośrednictwem sieci jest zbyt wolno lub pobieranie dodatkowej przepustowości sieci jest drogie. Użyj tej usługi w następujących scenariuszach:

* **Migracja danych do chmury**: szybkie przenoszenie dużych ilości danych na platformie Azure i tańszy sposób.
* **Dystrybucję zawartości**: szybko wysyłania danych do lokacji klienta.
* **Kopia zapasowa**: korzystać z kopii zapasowych danych lokalnych do przechowywania w magazynie Azure.
* **Odzyskiwanie danych**: odzyskać dużej ilości danych przechowywanych w magazynie i jest dostarczany do Twojej lokalizacji lokalnej.

## <a name="importexport-components"></a>Import/Eksport składników

Usługa Import/Eksport wykorzystuje następujące składniki:

- **Import/Eksport**usługi: tej usługi, które są dostępne w portalu Azure ułatwia użytkownikom tworzenie i śledzenia, importowanie i eksportowanie zadań.  

- **Narzędzie WAImportExport**: jest to narzędzie wiersza polecenia, które wykonuje następujące czynności: 
    - Przygotowuje dysków, które są wysyłane do importu.
    - Umożliwia kopiowanie danych na dysku.
    - Szyfrowanie danych na dysku za pomocą funkcji BitLocker.
    - Generuje pliki dziennika dysku używane podczas tworzenia importu.
    - Pomaga zidentyfikować liczby dysków potrzebne do zadań eksportu.

    To narzędzie jest dostępne w dwóch wersjach, wersja 1 i 2. Zaleca się, że używasz:

    - Wersja 1 importu/eksportu do magazynu obiektów Blob platformy Azure. 
    - Do importowania danych do plików Azure w wersji 2.

    Narzędzie WAImportExport jest zgodna tylko z 64-bitowym systemie operacyjnym Windows. Aby obsługiwane określonych wersji systemu operacyjnego, przejdź do tematu [wymagania Import/Eksport Azure](storage-import-export-requirements.md#supported-operating-systems).

- **Dyski**: mogą być dysków półprzewodnikowych (SSD) lub stacje dysków twardych (HDD) do centrum danych Azure. Podczas tworzenia zadania importu, są dostarczane stacje dysków z danymi. Podczas tworzenia zadania eksportu, możesz wysłać pustych dysków do centrum danych Azure. Dla typów określonego dysku, przejdź do [obsługiwanych typów dysków](storage-import-export-requirements.md#supported-hardware).

## <a name="how-does-importexport-work"></a>Jak działa importu/eksportu?

Usługa Import/Eksport Azure umożliwia transfer danych w obiektach blob Azure i usługi pliki Azure przez utworzenie zadania. Umożliwia utworzenie zadania w portalu Azure lub interfejsu REST API Menedżera zasobów Azure. Każde zadanie jest skojarzone z kontem magazynu jednego. 

Zadania można importu lub eksportu zadania. Zadania importu służy do importowania danych do obiektów blob Azure lub usługa pliki Azure, podczas gdy zadanie eksportu umożliwia danych można wyeksportować za pomocą obiektów blob Azure. Dla zadania importu są dostarczane dysków z danymi. Podczas tworzenia zadania eksportu są dostarczane pustych dysków do centrum danych Azure. W każdym przypadku można wysłać do 10 dysków na zadanie.

> [!IMPORTANT]
> Eksportowanie danych do usługi pliki Azure nie jest obsługiwane.

W tej sekcji wysokiego poziomu kroków operacji importowania, oraz opisano zadania eksportu. 


### <a name="inside-an-import-job"></a>Wewnątrz zadania importu

Na wysokim poziomie zadania importu obejmuje następujące kroki:

1. Określ dane do zaimportowania, liczba dysków, które są potrzebne, lokalizacja docelowa obiektu blob danych w magazynie Azure.
2. Aby skopiować dane na dyskach, należy użyć narzędzia WAImportExport. Szyfrowanie dysków funkcją BitLocker.
3. Utwórz zadania importu na koncie magazynu docelowego w portalu Azure. Przekazywanie plików dziennika dysku.
2. Podaj zwrotny adres i numer konta operator wysyłania powrót do dysków.
3. Należy najpierw wydać dysków do adres wysyłkowy podany podczas tworzenia zadania.
4. Zaktualizuj dostarczania śledzenia liczby w ramach szczegółów zadania importu i przesłać zadanie importu.
5. Dyski są odbierane i przetwarzane w centrum danych Azure.
6. Dyski są dostarczane przy użyciu konta operator adres zwrotny w zadania importu.
  
    ![Rysunek 1:Import przepływu pracy](./media/storage-import-export-service/importjob.png)

Instrukcje krok po kroku dotyczące danych importowania, przejdź do:

- [Importowanie danych do obiektów blob Azure](storage-import-export-data-to-blobs.md)
- [Importowanie danych do usługi pliki Azure](storage-import-export-data-to-files.md)


### <a name="inside-an-export-job"></a>Wewnątrz zadania eksportu

> [!IMPORTANT]
> Usługa obsługuje tylko eksportu obiektów blob Azure. Eksportowanie plików Azure nie jest obsługiwane.

Na wysokim poziomie zadania eksportu obejmuje następujące kroki:

1. Określenie danych do wyeksportowania, liczbę dysków można potrzeba, obiekty BLOB źródła lub ścieżki kontenera danych w magazynie obiektów Blob.
3. Utwórz zadanie eksportu na koncie magazynu źródła w portalu Azure.
4. Określ źródło obiektów blob lub kontener ścieżek danych do wyeksportowania.
5. Podaj zwrotny adres i numer konta operator wysyłania powrót do dysków.
6. Należy najpierw wydać dysków do adres wysyłkowy podany podczas tworzenia zadania.
7. Zaktualizuj dostarczania śledzenia liczby szczegółów zadania eksportu i przesłać zadanie eksportu.
8. Dyski są odbierane i przetwarzane w centrum danych Azure.
9. Dyski są szyfrowane za pomocą funkcji BitLocker i klucze są dostępne za pośrednictwem portalu Azure.  
10. Dyski są dostarczane przy użyciu konta operator adres zwrotny w zadania importu.
  
    ![Rysunek 2:Export przepływu pracy](./media/storage-import-export-service/exportjob.png)

Aby uzyskać instrukcje krok po kroku w danych eksportu, [eksportowania danych z obiektów blob Azure](storage-import-export-data-from-blobs.md).

## <a name="region-availability"></a>Dostępność w danym regionie 

Usługa Import/Eksport Azure obsługuje kopiowanie danych do i z wszystkich kont magazynu Azure. Mogą być dysków do jednego z podanych lokalizacji. Jeśli Twoje konto magazynu znajduje się w lokalizacji platformy Azure, która nie została określona w tym miejscu, lokalizacji alternatywnej wysyłki podano podczas tworzenia zadania.

### <a name="supported-shipping-locations"></a>Obsługiwane lokalizacje wysyłki


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

Dane na dysku są szyfrowane przy użyciu szyfrowania dysków funkcją BitLocker. Szyfrowanie chroni Twoje dane podczas przesyłania.

Dla zadania importu dyski są szyfrowane na dwa sposoby.  


- Określ opcje, korzystając z *dataset.csv* pliku podczas uruchamiania narzędzia WAImportExport podczas przygotowywania dysków. 

- Włącz szyfrowanie funkcją BitLocker ręcznie na dysku. Określ klucz szyfrowania w *driveset.csv* podczas uruchamiania wiersza polecenia narzędzia WAImportExport podczas przygotowywania dysków.


Po skopiowaniu danych na dyskach, usługa zadań eksportu szyfruje dysku za pomocą funkcji BitLocker przed dostarczeniem go z powrotem do. Klucz szyfrowania są dostępne za pośrednictwem portalu Azure.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]


### <a name="pricing"></a>Cennik

**Opłata obsługę dysku**

Brak opłatą Obsługa dysku dla każdego dysku przetwarzane jako część import lub zadanie eksportowania. Zobacz szczegółowe informacje o [cennik Import/Eksport Azure](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Koszty wysyłki**

Podczas wydawania dysków na platformie Azure, płacisz koszt wysyłki do wysyłki operatora. Po powrocie do Ciebie dyski Microsoft koszt wysyłki obciążających konto operatora, który dostarczony w czasie tworzenia zadania.

**Koszty transakcji**

Podczas importowania danych do usługi Azure Storage są Brak kosztów transakcji oprócz kosztów transakcji magazynu w warstwie standardowa. Opłaty za wyjście standardowe są stosowane, gdy dane są eksportowane z magazynu obiektów Blob. Aby uzyskać więcej informacji dotyczących kosztów transakcji, zobacz [ceny transferu danych.](https://azure.microsoft.com/pricing/details/data-transfers/)



## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak używać usługi Import/Eksport do:
* [Importowanie danych do obiektów blob Azure](storage-import-export-data-to-blobs.md)
* [Eksportowanie danych z obiektów blob Azure](storage-import-export-data-from-blobs.md)
* [Importowanie danych do usługi pliki Azure](storage-import-export-data-to-files.md)

