---
title: Używanie narzędzia Azure Import/Export do przesyłania danych do i z usługi Azure Storage | Dokumenty firmy Microsoft
description: Dowiedz się, jak tworzyć zadania importowania i eksportowania w witrynie Azure portal do przesyłania danych do i z usługi Azure Storage.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 03/15/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: eee0fc2797fbe0666a6b848fde574c7807f47cc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282447"
---
# <a name="what-is-azure-importexport-service"></a>Co to jest usługa importowania/eksportowania platformy Azure?

Usługa Importu/eksportu platformy Azure służy do bezpiecznego importowania dużych ilości danych do magazynu obiektów Blob platformy Azure i plików azure przez wysyłanie dysków do centrum danych platformy Azure. Ta usługa może również służyć do przesyłania danych z magazynu obiektów Blob platformy Azure na dyski i wysyłać do witryn lokalnych. Dane z jednego lub więcej dysków można zaimportować do magazynu obiektów Blob platformy Azure lub usługi Azure Files.

Dostarczaj własne dyski i przesyłaj dane za pomocą usługi Azure Import/Export. Można również użyć dysków dostarczonych przez firmę Microsoft.

Jeśli chcesz przenieść dane przy użyciu dysków dostarczonych przez firmę Microsoft, możesz użyć [usługi Azure Data Box Disk](../../databox/data-box-disk-overview.md) do importowania danych na platformę Azure. Firma Microsoft dostarcza do centrum danych do centrum danych do 5 zaszyfrowanych dysków PÓŁPRZEWODNIKOWYCH (SSD) o łącznej pojemności 40 TB na zamówienie za pośrednictwem operatora regionalnego. Można szybko skonfigurować dyski, skopiować dane na dyski za pośrednictwem połączenia USB 3.0 i wysłać dyski z powrotem na platformę Azure. Aby uzyskać więcej informacji, przejdź do [przeglądu usługi Azure Data Box Disk](../../databox/data-box-disk-overview.md).

## <a name="azure-importexport-use-cases"></a>Przypadki użycia importu/eksportowania platformy Azure

Należy rozważyć użycie usługi Azure Import/Export, gdy przekazywanie lub pobieranie danych przez sieć jest zbyt powolne lub uzyskanie dodatkowej przepustowości sieci jest zaporowe. Użyj tej usługi w następujących scenariuszach:

* **Migracja danych do chmury:** szybko i ekonomicznie przenieś duże ilości danych na platformę Azure.
* **Dystrybucja zawartości**: Szybkie wysyłanie danych do witryn klientów.
* **Kopia zapasowa:** Wykonywanie kopii zapasowych danych lokalnych do przechowywania w usłudze Azure Storage.
* **Odzyskiwanie danych:** odzyskaj dużą ilość danych przechowywanych w magazynie i złóż je do lokalizacji lokalnej.

## <a name="importexport-components"></a>Składniki importu/eksportu

Usługa importu/eksportu używa następujących składników:

* **Import/eksportowanie usługi:** Ta usługa dostępna w witrynie Azure portal ułatwia użytkownikowi tworzenie i śledzenie zadań importowania (przekazywania) danych i eksportowania (pobierania).  

* **Narzędzie WAImportExport**: Jest to narzędzie wiersza polecenia, które wykonuje następujące czynności:
  * Przygotowuje dyski, które są wysyłane do importu.
  * Ułatwia kopiowanie danych na dysk.
  * Szyfruje dane na dysku za pomocą 128-bitowego funkcji BitLocker AES. Do ochrony klucza funkcji BitLocker można użyć zewnętrznego ochraniacza kluczy.
  * Generuje pliki dziennika dysku używane podczas tworzenia importu.
  * Pomaga zidentyfikować liczbę dysków potrzebnych do zadań eksportu.

> [!NOTE]
> Narzędzie WAImportExport jest dostępne w dwóch wersjach: w wersji 1 i 2. Zalecamy użycie:
>
> * Wersja 1 do importowania/eksportowania do magazynu obiektów blob platformy Azure.
> * Wersja 2 do importowania danych do plików platformy Azure.
>
> Narzędzie WAImportExport jest zgodne tylko z 64-bitowym systemem operacyjnym Windows. W przypadku obsługi określonych wersji systemu operacyjnego przejdź do [wymagań dotyczących importu/eksportu platformy Azure](storage-import-export-requirements.md#supported-operating-systems).

* **Dyski: Dyski**SSD (SSD) lub Dyski twarde (HDD) można wysyłać do centrum danych platformy Azure. Podczas tworzenia zadania importu wysyłasz dyski zawierające dane. Podczas tworzenia zadania eksportu, należy wysłać puste dyski do centrum danych platformy Azure. W przypadku określonych typów dysków przejdź do [strony Obsługiwane typy dysków](storage-import-export-requirements.md#supported-hardware).

## <a name="how-does-importexport-work"></a>Jak działa import/eksport?

Usługa Importu/eksportu platformy Azure umożliwia transfer danych do obiektów blob platformy Azure i plików platformy Azure przez tworzenie zadań. Tworzenie zadań za pomocą witryny Azure portal lub interfejsu API REST usługi Azure Resource Manager. Każde zadanie jest skojarzone z jednym kontem magazynu.

Zadania mogą być zadania importu lub eksportu. Zadanie importowania umożliwia importowanie danych do obiektów Blobs platformy Azure lub plików platformy Azure, podczas gdy zadanie eksportowania umożliwia eksportowanie danych z obiektów blob platformy Azure. W przypadku zadania importu wysyłasz dyski zawierające dane. Podczas tworzenia zadania eksportu, należy wysłać puste dyski do centrum danych platformy Azure. W każdym przypadku można wysłać do 10 dysków na zadanie.

### <a name="inside-an-import-job"></a>Wewnątrz zadania importu

Na wysokim poziomie zadanie importu obejmuje następujące kroki:

1. Określ dane do zaimportowania, liczbę potrzebnych dysków, docelową lokalizację obiektu blob dla danych w magazynie platformy Azure.
2. Narzędzie WAImportExport służy do kopiowania danych na dyski. Szyfruj dyski za pomocą funkcji BitLocker.
3. Utwórz zadanie importu na koncie magazynu docelowego w witrynie Azure portal. Przekaż pliki dziennika dysku.
4. Podaj adres zwrotny i numer konta przewoźnika, aby wysyłać dyski z powrotem do Ciebie.
5. Wyślij dyski na adres wysyłki podany podczas tworzenia zadania.
6. Zaktualizuj numer śledzenia dostawy w szczegółach zadania importu i prześlij zadanie importu.
7. Dyski są odbierane i przetwarzane w centrum danych platformy Azure.
8. Dyski są wysyłane przy użyciu konta operatora na adres zwrotny podany w zadaniu importu.

> [!NOTE]
> W przypadku przesyłek lokalnych (w kraju/regionie centrum danych) prosimy o udostępnienie krajowego konta przewoźnika.
>
> W przypadku przesyłek za granicą (poza krajem/regionem centrum danych) prosimy o udostępnienie konta międzynarodowego przewoźnika.

 ![Rysunek 1:Importowanie przepływu zadań](./media/storage-import-export-service/importjob.png)

Aby uzyskać instrukcje krok po kroku dotyczące importowania danych, przejdź do:

* [Importowanie danych do obiektów blob platformy Azure](storage-import-export-data-to-blobs.md)
* [Importowanie danych do plików platformy Azure](storage-import-export-data-to-files.md)

### <a name="inside-an-export-job"></a>Wewnątrz zadania eksportu

> [!IMPORTANT]
> Usługa obsługuje tylko eksport obiektów blob platformy Azure. Eksport plików platformy Azure nie jest obsługiwany.

Na wysokim poziomie zadanie eksportu obejmuje następujące kroki:

1. Określ dane do wyeksportowania, liczbę potrzebnych dysków, źródłowe obiekty BLOB lub ścieżki kontenerów danych w magazynie obiektów Blob.
2. Utwórz zadanie eksportu na koncie magazynu źródłowego w witrynie Azure portal.
3. Określ źródłowe obiekty BLOB lub ścieżki kontenerów dla danych, które mają być eksportowane.
4. Podaj adres zwrotny i numer konta przewoźnika, aby wysyłać dyski z powrotem do Ciebie.
5. Wyślij dyski na adres wysyłki podany podczas tworzenia zadania.
6. Zaktualizuj numer śledzenia dostawy w szczegółach zadania eksportu i prześlij zadanie eksportu.
7. Dyski są odbierane i przetwarzane w centrum danych platformy Azure.
8. Dyski są szyfrowane za pomocą funkcji BitLocker, a klucze są dostępne za pośrednictwem witryny Azure portal.  
9. Dyski są wysyłane przy użyciu konta operatora na adres zwrotny podany w zadaniu importu.

> [!NOTE]
> W przypadku przesyłek lokalnych (w kraju/regionie centrum danych) prosimy o udostępnienie krajowego konta przewoźnika.
>
> W przypadku przesyłek za granicą (poza krajem/regionem centrum danych) prosimy o udostępnienie konta międzynarodowego przewoźnika.
  
 ![Rysunek 2:Eksportowanie przepływu zadań](./media/storage-import-export-service/exportjob.png)

Aby uzyskać instrukcje krok po kroku dotyczące eksportu danych, przejdź do [tematu Eksportowanie danych z obiektów Blob platformy Azure](storage-import-export-data-from-blobs.md).

## <a name="region-availability"></a>Dostępność w danym regionie

Usługa Import/Eksport platformy Azure obsługuje kopiowanie danych do i ze wszystkich kont magazynu platformy Azure. Dyski można wysyłać do jednej z wymienionych lokalizacji. Jeśli twoje konto magazynu znajduje się w lokalizacji platformy Azure, która nie jest określona w tym miejscu, podczas tworzenia zadania jest dostępna alternatywna lokalizacja wysyłki.

### <a name="supported-shipping-locations"></a>Obsługiwane lokalizacje wysyłkowe

|Kraj/region  |Kraj/region  |Kraj/region  |Kraj/region  |
|---------|---------|---------|---------|
|Wschodnie stany USA    | Europa Północna        | Indie Środkowe        |US Gov Iowa         |
|Zachodnie stany USA     |Europa Zachodnia         | Indie Południowe        | US DoD (region wschodni)        |
|Wschodnie stany USA 2    | Azja Wschodnia        |  Indie Zachodnie        | US DoD (region środkowy)        |
|Zachodnie stany USA 2     | Azja Południowo-Wschodnia        | Kanada Środkowa        | Chiny Wschodnie         |
|Środkowe stany USA     | Australia Wschodnia        | Kanada Wschodnia        | Chiny Północne        |
|Północno-środkowe stany USA     |  Australia Południowo-Wschodnia       | Brazylia Południowa        | Południowe Zjednoczone Królestwo        |
|Południowo-środkowe stany USA     | Japonia Zachodnia        |Korea Środkowa         | Niemcy Środkowe        |
|Zachodnio-środkowe stany USA     |  Japonia Wschodnia       | US Gov Wirginia        | Niemcy Północno-Wschodnie        |

## <a name="security-considerations"></a>Zagadnienia dotyczące bezpieczeństwa

Dane na dysku są szyfrowane przy użyciu szyfrowania dysków funkcji AES 128-bitowych funkcji BitLocker. To szyfrowanie chroni dane podczas przesyłania.

W przypadku zadań importowania dyski są szyfrowane na dwa sposoby.  

* Określ opcję podczas korzystania z pliku *dataset.csv* podczas uruchamiania narzędzia WAImportExport podczas przygotowywania dysku.

* Włącz szyfrowanie funkcją BitLocker ręcznie na dysku. Określ klucz szyfrowania w *pliku driveset.csv* podczas uruchamiania wiersza polecenia narzędzia WAImportExport podczas przygotowywania dysku. Klucz szyfrowania funkcji BitLocker można dodatkowo chronić za pomocą zewnętrznego programu ochrony kluczy (znanego również jako klucz zarządzany firmy Microsoft) lub klucza zarządzanego przez klienta. Aby uzyskać więcej informacji, zobacz jak [chronić klucz funkcji BitLocker za pomocą klucza zarządzanego przez klienta](storage-import-export-encryption-key-portal.md).

W przypadku zadań eksportowania po skopiowaniu danych na dyski usługa szyfruje dysk przy użyciu funkcji BitLocker przed wysłaniem go z powrotem do Ciebie. Klucz szyfrowania jest dostarczany za pośrednictwem witryny Azure portal. Dysk musi zostać odblokowany za pomocą narzędzia WAImporExport za pomocą klucza.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

### <a name="pricing"></a>Cennik

**Opłata za obsługę dysku**

Istnieje opłata za obsługę dysku za każdy dysk przetworzony w ramach zadania importu lub eksportu. Zobacz szczegóły dotyczące [cen importu/eksportu platformy Azure](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Koszty wysyłki**

Podczas wysyłania dysków na platformę Azure płacisz koszty wysyłki przewoźnikowi. Gdy firma Microsoft zwróci dyski do użytkownika, koszt wysyłki jest obciążany kontem przewoźnika, które podałeś w momencie tworzenia zadania.

**Koszty transakcji**

[Standardowa opłata za transakcję magazynu](https://azure.microsoft.com/pricing/details/storage/) ma zastosowanie podczas importowania i eksportu danych. Standardowe opłaty wychodzące mają również zastosowanie wraz z opłatami za transakcje magazynu, gdy dane są eksportowane z usługi Azure Storage. Aby uzyskać więcej informacji na temat kosztów wyjścia, zobacz [Ceny transferu danych.](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak korzystać z usługi Import/Eksportowanie w celu:

* [Importowanie danych do obiektów blob platformy Azure](storage-import-export-data-to-blobs.md)
* [Eksportowanie danych z obiektów blob platformy Azure](storage-import-export-data-from-blobs.md)
* [Importowanie danych do plików platformy Azure](storage-import-export-data-to-files.md)
