---
title: Limity zasobów bazy danych SQL platformy Azure — wystąpienie zarządzane | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie limitów zasobów usługi Azure SQL Database dla wystąpienia zarządzanego.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
manager: craigg
ms.date: 06/13/2019
ms.openlocfilehash: 15f64c7087ea4d24f271af67b251030a2196fa10
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67070370"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Omówienie usługi Azure SQL Database managed wystąpienia limity zasobów

Ten artykuł zawiera omówienie limity zasobów dla wystąpienia zarządzanego usługi Azure SQL Database i zawiera informacje dotyczące sposobów poprosić o zwiększenie limitów.

> [!NOTE]
> Aby poznać różnice w obsługiwanych funkcjach i T-SQL zobacz instrukcje [różnice są wyposażone w](sql-database-features.md) i [Obsługa instrukcji języka T-SQL](sql-database-managed-instance-transact-sql-information.md).

## <a name="instance-level-resource-limits"></a>Limity zasobów na poziomie wystąpienia

Wystąpienie zarządzane ma cechy i limitów zasobów, które są zależne od podstawową infrastrukturę i architektury. Limity są zależne od sprzętu w warstwie generowania i usługi.

### <a name="hardware-generation-characteristics"></a>Właściwości generacji sprzętu

Wystąpienie zarządzane usługi Azure SQL Database można wdrożyć w obu generacji sprzętu: 4. generacji, jak i Gen5. Generacji sprzętu mają inne cechy, zgodnie z opisem w poniższej tabeli:

|   | **4. generacji** | **5. generacji** |
| --- | --- | --- |
| Sprzęt | Intel E5-2673 v3 procesorów 2,4 GHz (Haswell), dołączony dysk SSD — rdzeń wirtualny = 1 PP (fizycznych rdzeni) | Intel E5-2673 v4 (broadwell z zegarem) 2.3 GHz procesorów, szybkie dyski SSD NVMe, — rdzeń wirtualny = LP 1 (hyper wątek) |
| rdzenie wirtualne | 8, 16, 24 rdzenie wirtualne | 4, 8, 16, 24, 32, 40, 64, 80 rdzeni wirtualnych |
| Pamięć (ilość pamięci/rdzeń) | 7 GB na rdzeń wirtualny | 5.1 GB na rdzeń wirtualny |
| Maksymalna liczba OLTP w pamięci pamięci | Limit wystąpienie: 3 GB na rdzeń wirtualny<br/>Limity bazy danych:<br/> -8-rdzeniowy: 8 GB na bazę danych<br/> — 16 rdzeni: 20 GB dla jednej bazy danych<br/> -24-core: 36 GB na bazę danych | Limit wystąpienie: 2,5 GB na rdzeń wirtualny<br/>Limity bazy danych:<br/> -8-rdzeniowy: 13 GB na bazę danych<br/> — 16 rdzeni: 32 GB na bazę danych |
| Maksymalny rozmiar wystąpienia magazynu (ogólnego przeznaczenia) |  8 TB | 8 TB |
| Maksymalna pojemność magazynu wystąpienia (krytyczne dla działania firmy) | 1 TB | 1 TB, 2 TB lub 4 TB w zależności od liczby rdzeni |

### <a name="service-tier-characteristics"></a>Właściwości warstwy usług

Wystąpienia zarządzanego istnieją dwie warstwy usług: Ogólnego przeznaczenia i krytyczne dla działania. Te warstwy zapewniają różne możliwości, zgodnie z opisem w poniższej tabeli:

| **Funkcja** | **Ogólnego przeznaczenia** | **Krytyczne dla działania** |
| --- | --- | --- |
| Liczba rdzeni wirtualnych\* | Gen4: 8, 16, 24<br/>5\. generacji: 4, 8, 16, 24, 32, 40, 64, 80 | Gen4: 8, 16, 24, 32 <br/> 5\. generacji: 4, 8, 16, 24, 32, 40, 64, 80 |
| Memory (Pamięć) | Gen4: 56 GB – 168 GB (7GB/rdzeń wirtualny)<br/>5\. generacji: 40.8 GB – 408 GB (5.1 GB/rdzeń wirtualny) | Gen4: 56 GB – 168 GB (7GB/rdzeń wirtualny)<br/>5\. generacji: 40.8 GB – 408 GB (5.1 GB/rdzeń wirtualny) |
| Maksymalny rozmiar magazynu wystąpień | -2 TB dla 4 rdzenie wirtualne (tylko w przypadku 5. generacji)<br/>-8 TB dla wystąpień o innych rozmiarach | Gen4: 1 TB <br/> 5\. generacji: <br/>-1 TB dla 4, 8, 16 rdzeni wirtualnych<br/>-2 TB dla 24 rdzenie wirtualne<br/>-4 TB dla 32, 40, 64, 80 rdzeni wirtualnych |
| Maksymalny rozmiar magazynu na bazę danych | Określony przez rozmiar maksymalnego rozmiaru magazynu dla każdego wystąpienia | Określony przez rozmiar maksymalnego rozmiaru magazynu dla każdego wystąpienia |
| Maksymalna liczba baz danych dla każdego wystąpienia | 100 | 100 |
| Maksymalna liczba plików bazy danych dla każdego wystąpienia | Maksymalnie 280 | 32 767 plików na bazę danych |
| Danych/dziennika operacji We/Wy (w przybliżeniu) | 500 — 7500 operacji na pliku<br/>\*[Zależy od rozmiaru pliku](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes)| 11-110 tys. obr (1375/rdzeń wirtualny) |
| Przepływność dziennika | 3 MB/s na rdzeniach wirtualnych<br/>Maksymalna liczba 22 MB/s na wystąpienie | 4 MB/s na rdzeniach wirtualnych<br/>Maksymalna liczba 48 MB/s na wystąpienie|
| Przepływność danych (w przybliżeniu) | 100 - 250 MB/s na plik<br/>\*[Zależy od rozmiaru pliku](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes) | ND |
| We/Wy, czas oczekiwania (w przybliżeniu) | 5 – 10 ms | 1 – 2 ms |
| Maksymalny rozmiar bazy danych tempDB | 192 - 1,920 GB (24 GB na rdzeń wirtualny) | Bez ograniczeń — ograniczone przez maksymalny rozmiar wystąpienia: magazynu |
| Maksymalna liczba sesji | 30000 | 30000 |

> [!NOTE]
> - Zarówno danych i dziennika rozmiar pliku w użytkownika i systemowe bazy danych znajdują się w rozmiarze wystąpienia magazynu, która jest porównywana z maksymalny limit rozmiaru magazynu. Użyj <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys.master_files</a> widok systemowy ustalenie Suma używane miejsce w bazach danych. Dzienniki błędów są nie jest trwały i nie są uwzględnione w rozmiarze. Kopie zapasowe nie są uwzględnione w rozmiaru magazynu.
> - Przepływność i operacje We/Wy także zależeć od rozmiaru strony, która nie jest jawnie ograniczone wystąpienia zarządzanego.

## <a name="supported-regions"></a>Obsługiwane regiony

Wystąpienia zarządzane można tworzyć tylko w [obsługiwane regiony](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Aby utworzyć wystąpienie zarządzane w regionie, który nie jest obecnie obsługiwane, mogą [Wyślij żądanie pomocy technicznej za pośrednictwem witryny Azure portal](#obtaining-a-larger-quota-for-sql-managed-instance).

## <a name="supported-subscription-types"></a>Typy obsługiwane subskrypcji

Wystąpienie zarządzane obecnie obsługuje wdrożenia tylko dla następujących typów subskrypcji:

- [Umowy Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Płatność za rzeczywiste użycie](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Dostawca usług w chmurze (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Płatność za rzeczywiste użycie, tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Subskrypcje z miesięcznych środków platformy Azure dla subskrybentów programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Ograniczenia zasobów regionalnego

Typy obsługiwane subskrypcji mogą zawierać ograniczoną liczbę zasobów na region. Wystąpienia zarządzanego istnieją dwie domyślne limity na region platformy Azure, w zależności od rodzaju typu subskrypcji:

- **Limit podsieci**: Maksymalna liczba podsieci wdrożonym wystąpienia zarządzanego w jednym regionie.
- **limit pamięci rdzeń wirtualny**: Maksymalna liczba rdzeni wirtualnych, które mogą być wdrażane ze wszystkich wystąpień w jednym regionie.

> [!Note]
> Limity te są ustawienia domyślne i ograniczenia nie weryfikacji technicznej. Limity, może być zwiększenia na żądanie, tworząc specjalny [żądania pomocy technicznej w witrynie Azure portal](#obtaining-a-larger-quota-for-sql-managed-instance) Jeśli potrzebujesz bardziej zarządzanych wystąpień w bieżącym regionie. Alternatywnie można utworzyć nowe wystąpienia zarządzanego w innym regionie platformy Azure, bez wysyłania żądań pomocy technicznej.

W poniższej tabeli przedstawiono domyślne limity regionalne dla obsługiwanych subskrypcji:

|Typ subskrypcji| Maksymalna liczba podsieci wystąpienia zarządzanego | Maksymalna liczba jednostek * rdzeń wirtualny |
| :---| :--- | :--- |
|Płatność zgodnie z rzeczywistym użyciem|3|320|
|CSP |8 (15 w niektórych regionach **)|960 (1440 w niektórych regionach **)|
|Płatność za rzeczywiste użycie, tworzenie i testowanie|3|320|
|Enterprise — tworzenie i testowanie|3|320|
|EA|8 (15 w niektórych regionach **)|960 (1440 w niektórych regionach **)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional oraz platform MSDN|2|32|

\* Podczas planowania wdrożenia należy wziąć pod uwagę że biznesowe krytyczne (BC) rdzeń wirtualny (z powodu dodane redundancy) wykorzystuje 4 x większą pojemność niż rdzeń wirtualny ogólnego przeznaczenia (GP). Tak więc, na obliczeniach, 1 GP rdzeń wirtualny = 1 rdzeń wirtualny jednostki i 1 BC rdzeń wirtualny = 4 jednostki (rdzeń wirtualny). Aby uprościć analizy użycia na wartości domyślne, podsumowanie jednostki (rdzeń wirtualny) we wszystkich podsieciach w regionie, gdzie zarządzanych wystąpień są wdrażane i porównać wyniki z granicami jednostki wystąpienia dla typu Twojej subskrypcji. **Maksymalna liczba jednostek — rdzeń wirtualny** obowiązuje limit dla każdej subskrypcji w regionie. Nie ma żadnego limitu dla poszczególnych podsieci, z tą różnicą, że suma wszystkich rdzeni wirtualnych wdrożonych w wielu podsieciach musi być mniejsza lub równa **maksymalna liczba jednostek — rdzeń wirtualny**.

** Większe limity podsieci i rdzeniach wirtualnych są dostępne w następujących regionach: Australia Wschodnia, wschodnie stany USA, wschodnie stany USA 2, Europa Północna, południowo-środkowe stany USA, Azja południowo-wschodnia, południowe Zjednoczone Królestwo, Europa Zachodnia, zachodnie stany USA 2.

## <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>Uzyskanie większego limitu przydziału dla SQL wystąpienia zarządzanego

Jeśli potrzebujesz więcej wystąpień zarządzanych w Twojej bieżącej regionach, należy wysłać żądanie obsługi, aby rozszerzyć przydział, korzystając z portalu Azure.
Aby zainicjować proces uzyskiwania większego limitu przydziału:

1. Otwórz **Pomoc i obsługa techniczna**i kliknij przycisk **nowe żądanie obsługi**.

   ![Pomoc i obsługa techniczna](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. Na karcie podstawy nowe żądanie pomocy technicznej:
   - Aby uzyskać **typ problemu**, wybierz opcję **limity usług i subskrypcji (przydziały)** .
   - W polu **Subskrypcja** wybierz subskrypcję.
   - Aby uzyskać **typ limitu przydziału**, wybierz opcję **wystąpienie zarządzane usługi SQL Database**.
   - Aby uzyskać **plan pomocy technicznej**, wybierz plan pomocy technicznej.

     ![Problem typu przydziału](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. Kliknij przycisk **Dalej**.
4. Na **kartę Problem** dla nowego żądania pomocy technicznej:
   - Aby uzyskać **ważność**, wybierz poziom ważności problemu.
   - Aby uzyskać **szczegóły**, zawierają dodatkowe informacje o swoim problemie, w tym komunikaty o błędach.
   - Aby uzyskać **przekazywanie pliku**, Dołącz plik z dodatkowymi informacjami (maksymalnie 4 MB).

     ![Szczegóły problemu](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > Prawidłowemu żądaniu powinien zawierać:
     > - Region, subskrypcję, która limit musi zostać zwiększone.
     > - Wymagana liczba rdzeni wirtualnych na warstwę usługi w istniejących podsieci po przydział zwiększyć (Jeśli żadna z istniejących podsieci musi do wyodrębnienia.
     > - Wymagana liczba nowych podsieci i łączna liczba rdzeni wirtualnych na warstwę usługi, w ramach nowych podsieci (jeśli zajdzie potrzeba wdrożenia wystąpienia zarządzanego w nowych podsieci).

5. Kliknij przycisk **Dalej**.
6. Na karcie informacje kontaktowe nowe żądanie pomocy technicznej wprowadź preferowaną metodę kontaktu (adres e-mail lub telefon) i szczegóły dotyczące kontaktu.
7. Kliknij pozycję **Utwórz**.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać więcej informacji na temat wystąpienia zarządzanego, zobacz [co to jest wystąpienie zarządzane?](sql-database-managed-instance.md).
- Aby uzyskać informacje o cenach, zobacz [zarządzana ceny wystąpienia usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Aby dowiedzieć się, jak utworzyć pierwszego wystąpienia zarządzanego, zobacz [przewodnika Szybki Start](sql-database-managed-instance-get-started.md).
