---
title: Azure SQL Database limitów zasobów — wystąpienie zarządzane | Microsoft Docs
description: Ten artykuł zawiera omówienie limitów zasobów Azure SQL Database dla wystąpień zarządzanych.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
ms.date: 08/27/2019
ms.openlocfilehash: 0dea447ed44a61b20faf9a0a1690b2bbdd674b30
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70930622"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Przegląd Azure SQL Database limitów zasobów wystąpienia zarządzanego

Ten artykuł zawiera omówienie limitów zasobów dla Azure SQL Database wystąpienia zarządzanego i zawiera informacje o sposobach żądania zwiększenia do tych limitów.

> [!NOTE]
> Aby uzyskać różnice w obsługiwanych funkcjach i instrukcjach języka T-SQL, zobacz temat [różnice w funkcjach](sql-database-features.md) i [Obsługa instrukcji języka t-SQL](sql-database-managed-instance-transact-sql-information.md).

## <a name="instance-level-resource-limits"></a>Limity zasobów na poziomie wystąpienia

Wystąpienie zarządzane ma cechy i limity zasobów, które są zależne od podstawowej infrastruktury i architektury. Limity zależą od generacji sprzętu i warstwy usług.

### <a name="hardware-generation-characteristics"></a>Charakterystyki generowania sprzętu

Wystąpienie zarządzane Azure SQL Database można wdrożyć na dwóch generacjach sprzętu: Obliczenia i 5 rdzeń. Generacji sprzętu mają różne cechy, zgodnie z opisem w poniższej tabeli:

|   | **Obliczenia** | **5 rdzeń** |
| --- | --- | --- |
| Sprzęt | Procesor Intel E5-2673 v3 (Haswell) 2,4 GHz, dołączony dysk SSD rdzeń wirtualny = 1 PP (rdzeń fizyczny) | Intel E5-2673 v4 (Broadwell) procesory 2,3 GHz, Fast NVMe SSD, rdzeń wirtualny = 1 LP (Hyper-Thread) |
| Liczba rdzeni wirtualnych | 8, 16, 24 rdzeni wirtualnych | 4, 8, 16, 24, 32, 40, 64, 80 rdzeni wirtualnych |
| Maksymalna ilość pamięci (stosunek pamięci/rdzeni) | 7 GB na rdzeń wirtualny<br/>Aby uzyskać więcej pamięci, Dodaj więcej rdzeni wirtualnych. | 5,1 GB na rdzeń wirtualny<br/>Aby uzyskać więcej pamięci, Dodaj więcej rdzeni wirtualnych. |
| Maksymalna pamięć OLTP w pamięci | Limit wystąpień: 3 GB na rdzeń wirtualny<br/>Limity bazy danych:<br/> -8-rdzeniowe: 8 GB na bazę danych<br/> -16-rdzeniowe: 20 GB na bazę danych<br/> -24-rdzeniowe: 36 GB na bazę danych | Limit wystąpień: 2,5 GB na rdzeń wirtualny<br/>Limity bazy danych:<br/> -8-rdzeniowe: 13 GB na bazę danych<br/> -16-rdzeniowe: 32 GB na bazę danych |
| Maksymalna ilość zarezerwowanego wystąpienia |  Ogólnego przeznaczenia: 8 TB<br/>Krytyczne dla działania firmy: 1TB | Ogólnego przeznaczenia: 8 TB<br/> Krytyczne dla działania firmy 1 TB, 2 TB lub 4 TB w zależności od liczby rdzeni |

> [!IMPORTANT]
> - Obliczenia sprzęt jest stopniowo wycofywany. Zaleca się wdrożenie nowych wystąpień zarządzanych na sprzęcie 5 rdzeń.
> - W tej chwili obliczenia sprzęt jest dostępny w następujących regionach: Europa Północna, Europa Zachodnia, Wschodnie stany USA, Południowo-środkowe stany USA, Północno-środkowe stany USA, zachodnie stany USA 2, środkowe stany USA, Kanada środkowa, Indie Południowe, Azja Południowo-Wschodnia i Korea środkowa.

### <a name="service-tier-characteristics"></a>Charakterystyki warstwy usług

Wystąpienie zarządzane ma dwie warstwy usług: Ogólnego przeznaczenia i Krytyczne dla działania firmy. Te warstwy zapewniają różne możliwości, zgodnie z opisem w poniższej tabeli:

| **Funkcja** | **Ogólnego przeznaczenia** | **Krytyczne dla działania firmy** |
| --- | --- | --- |
| Liczba rdzeni wirtualnych\* | Obliczenia 8, 16, 24<br/>5 rdzeń 4, 8, 16, 24, 32, 40, 64, 80 | Obliczenia 8, 16, 24 <br/> 5 rdzeń 4, 8, 16, 24, 32, 40, 64, 80 |
| Maksymalna pamięć | Obliczenia 56 GB – 168 GB (7GB/rdzeń wirtualny)<br/>5 rdzeń 40,8 GB – 408 GB (5.1 GB/rdzeń wirtualny)<br/>Aby uzyskać więcej pamięci, Dodaj więcej rdzeni wirtualnych. | Obliczenia 56 GB – 168 GB (7GB/rdzeń wirtualny)<br/>5 rdzeń 40,8 GB – 408 GB (5.1 GB/rdzeń wirtualny)<br/>Aby uzyskać więcej pamięci, Dodaj więcej rdzeni wirtualnych. |
| Maksymalny rozmiar zarezerwowanego wystąpienia magazynu | -2 TB dla 4 rdzeni wirtualnych (tylko 5 rdzeń)<br/>-8 TB dla innych rozmiarów | Obliczenia 1 TB <br/> 5 rdzeń <br/>-1 TB dla 4, 8, 16 rdzeni wirtualnych<br/>-2 TB przez 24 rdzeni wirtualnych<br/>-4 TB dla 32, 40, 64, 80 rdzeni wirtualnych |
| Maksymalny rozmiar bazy danych | 8 TB | 4 TB |
| Maksymalna liczba baz danych na wystąpienie | 100 | 100 |
| Maksymalna liczba plików bazy danych na wystąpienie | Do 280 | 32 767 plików na bazę danych |
| Maksymalny rozmiar pliku | 8 TB | 4 TB |
| Maksymalny rozmiar pliku dziennika | 2 TB | 2 TB |
| Operacje we/wy danych/dziennika (przybliżone) | 500 – 7 500 za plik<br/>\*[Zwiększ rozmiar pliku, aby uzyskać więcej operacji we/wy na sekundę](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes)| 5,5 k – 110 K (1375/rdzeń wirtualny)<br/>Dodaj więcej rdzeni wirtualnych, aby uzyskać lepszą wydajność operacji we/wy. |
| Limit przepływności zapisu dziennika | 3 MB/s na rdzeń wirtualny<br/>Maks. 22 MB/s na wystąpienie | 4 MB/s na rdzeń wirtualny<br/>Maks 48 MB/s na wystąpienie|
| Przepływność danych (przybliżona) | 100 – 250 MB/s na plik<br/>\*[Zwiększ rozmiar pliku, aby uzyskać lepszą wydajność we/wy](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes) | ND |
| Opóźnienie operacji we/wy magazynu (w przybliżeniu) | 5-10 ms | 1-2 MS |
| Maksymalny rozmiar bazy danych tempDB | 192 – 1 920 GB (24 GB na rdzeń wirtualny)<br/>Dodaj więcej rdzeni wirtualnych, aby uzyskać więcej przestrzeni TempDB. | Ograniczone przez maksymalny rozmiar magazynu wystąpienia. Rozmiar pliku dziennika bazy danych TempDB jest obecnie ograniczony do 24GB/rdzeń wirtualny. |
| Przetwarzanie OLTP danych w pamięci | Nieobsługiwane | Dostępne |
| Maksymalna liczba sesji | 30000 | 30000 |
| [Repliki tylko do odczytu](sql-database-read-scale-out.md) | 0 | 1 (wliczone w cenę) |

> [!NOTE]
> - Rozmiar pliku danych i dziennika w bazach danych użytkownika i systemu jest uwzględniany w rozmiarze magazynu wystąpienia, który jest porównywany z maksymalnym limitem rozmiaru magazynu. Użyj widoku system <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys. master_files</a> , aby określić łączną ilość miejsca używanego przez bazy danych. Dzienniki błędów nie są utrwalane i nie zostały uwzględnione w rozmiarze. Kopie zapasowe nie są uwzględniane w rozmiarze magazynu.
> - Przepływność i operacje we/wy zależą również od rozmiaru strony, która nie jest jawnie ograniczona przez wystąpienie zarządzane.
> Można utworzyć kolejną replikę do odczytu w innym regionie świadczenia usługi Azure przy użyciu grup Autotryb failover.

## <a name="supported-regions"></a>Obsługiwane regiony

Wystąpienia zarządzane można tworzyć tylko w [obsługiwanych regionach](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Aby utworzyć wystąpienie zarządzane w regionie, który nie jest obecnie obsługiwany, można [wysłać żądanie pomocy technicznej za pośrednictwem Azure Portal](#obtaining-a-larger-quota-for-sql-managed-instance).

## <a name="supported-subscription-types"></a>Obsługiwane typy subskrypcji

Wystąpienie zarządzane obecnie obsługuje tylko wdrożenie następujących typów subskrypcji:

- [Umowa Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Płatność zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Dostawca usług w chmurze (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Płatność zgodnie z rzeczywistym użyciem — tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Subskrypcje z miesięcznymi środkami platformy Azure dla subskrybentów programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Ograniczenia zasobów regionalnych

Obsługiwane typy subskrypcji mogą zawierać ograniczoną liczbę zasobów na region. Wystąpienie zarządzane ma dwa domyślne limity dla regionu platformy Azure, w zależności od typu subskrypcji:

- **Limit podsieci**: Maksymalna liczba podsieci, w których wystąpienia zarządzane są wdrażane w jednym regionie.
- **Limit rdzeń wirtualny**: Maksymalna liczba rdzeni wirtualnych, które mogą zostać wdrożone we wszystkich wystąpieniach w jednym regionie.

> [!Note]
> Te limity to ustawienia domyślne, a nie ograniczenia techniczne. Limity można zwiększyć na żądanie, tworząc specjalne [żądanie pomocy technicznej w Azure Portal,](#obtaining-a-larger-quota-for-sql-managed-instance) Jeśli potrzebujesz więcej wystąpień zarządzanych w bieżącym regionie. Alternatywnie można tworzyć nowe wystąpienia zarządzane w innym regionie świadczenia usługi Azure bez wysyłania żądań pomocy technicznej.

W poniższej tabeli przedstawiono **domyślne limity** dla obsługiwanych typów subskrypcji (domyślne limity można rozszerzyć przy użyciu żądania pomocy technicznej opisanego poniżej):

|Typ subskrypcji| Maksymalna liczba podsieci wystąpienia zarządzanego | Maksymalna liczba jednostek rdzeń wirtualny * |
| :---| :--- | :--- |
|Płatność zgodnie z rzeczywistym użyciem|3|320|
|CSP |8 (15 w niektórych regionach * *)|960 (1440 w niektórych regionach * *)|
|Płatność zgodnie z rzeczywistym użyciem — tworzenie i testowanie|3|320|
|Enterprise — tworzenie i testowanie|3|320|
|EA|8 (15 w niektórych regionach * *)|960 (1440 w niektórych regionach * *)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional i Platformy MSDN|2|32|

\*W obszarze Planowanie wdrożeń należy wziąć pod uwagę, że warstwa usług Krytyczne dla działania firmy (BC) wymaga czterech (4) razy więcej rdzeń wirtualny pojemności niż Ogólnego przeznaczenia (GP). Na przykład: 1 rdzeń wirtualny GP = 1 jednostka rdzeń wirtualny i 1 BC rdzeń wirtualny = 4 jednostki rdzeń wirtualny. Aby uprościć analizę zużycia pod kątem domyślnych limitów, należy podsumować jednostki rdzeń wirtualny we wszystkich podsieciach w regionie, w którym wdrożono wystąpienia zarządzane i porównać wyniki z limitami jednostek wystąpienia dla danego typu subskrypcji. **Maksymalna liczba jednostek rdzeń wirtualny** dotyczy każdej subskrypcji w regionie. Nie ma żadnego limitu dla poszczególnych podsieci, z tą różnicą, że suma wszystkich rdzeni wirtualnych wdrożonych w wielu podsieciach musi być mniejsza lub równa **maksymalnej liczbie jednostek rdzeń wirtualny**.

\*\*Większe limity podsieci i rdzeń wirtualny są dostępne w następujących regionach: Australia Wschodnia, Wschodnie stany USA, Wschodnie stany USA 2, Europa Północna, Południowo-środkowe stany USA, Azja Południowo-Wschodnia, Południowe Zjednoczone Królestwo, Europa Zachodnia, zachodnie stany USA 2.

## <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>Uzyskiwanie większego przydziału dla wystąpienia zarządzanego SQL

Jeśli potrzebujesz więcej wystąpień zarządzanych w Twoich bieżących regionach, Wyślij żądanie obsługi, aby zwiększyć przydział przy użyciu Azure Portal.
Aby zainicjować proces uzyskiwania większego przydziału:

1. Otwórz **Pomoc i pomoc techniczną**, a następnie kliknij pozycję **nowe żądanie obsługi**.

   ![Pomoc i obsługa techniczna](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. Na karcie podstawowe informacje o nowym żądaniu obsługi:
   - W obszarze **typ problemu**wybierz pozycję **usługi i limity subskrypcji (przydziały)** .
   - W polu **Subskrypcja** wybierz subskrypcję.
   - W obszarze **Typ limitu przydziału**wybierz pozycję **SQL Database wystąpienie zarządzane**.
   - W przypadku **planu pomocy technicznej**wybierz plan pomocy technicznej.

     ![Przydział typu problemu](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. Kliknij przycisk **Dalej**.
4. Na **karcie problem** dla nowego żądania obsługi:
   - W polu **ważność**wybierz poziom ważności problemu.
   - Abyuzyskać więcej informacji, podaj dodatkowe informacje o problemie, w tym komunikaty o błędach.
   - W przypadku **przekazywania plików**Dołącz plik z więcej informacji (do 4 MB).

     ![Szczegóły problemu](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > Prawidłowe żądanie powinno obejmować:
     > - Region, w którym należy zwiększyć limit subskrypcji.
     > - Wymagana liczba rdzeni wirtualnych na warstwę usług w istniejących podsieciach po zwiększeniu limitu przydziału (Jeśli jakakolwiek z istniejących podsieci musi być rozwinięta).
     > - Wymagana liczba nowych podsieci i łączna liczba rdzeni wirtualnych na warstwę usług w ramach nowych podsieci (jeśli trzeba wdrożyć wystąpienia zarządzane w nowych podsieciach).

5. Kliknij przycisk **Dalej**.
6. Na karcie Informacje kontaktowe dla nowego żądania obsługi wprowadź preferowaną metodę kontaktu (adres e-mail lub telefon) i szczegóły kontaktu.
7. Kliknij przycisk **Utwórz**.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji o wystąpieniu zarządzanym, zobacz [co to jest wystąpienie zarządzane?](sql-database-managed-instance.md)
- Aby uzyskać informacje o cenach, zobacz [SQL Database cenach wystąpienia zarządzanego](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Aby dowiedzieć się, jak utworzyć pierwsze wystąpienie zarządzane, zobacz [Przewodnik Szybki Start](sql-database-managed-instance-get-started.md).
