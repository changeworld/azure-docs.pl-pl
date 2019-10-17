---
title: Omówienie funkcji preskalowanie Azure SQL Database | Microsoft Docs
description: W tym artykule opisano warstwę usługi w modelu zakupów rdzeń wirtualny w Azure SQL Database i wyjaśniono, jak różni się ona od Ogólnego przeznaczenia i Krytyczne dla działania firmy warstw usług.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/01/2019
ms.openlocfilehash: df6926a8f50d7ffb2765557cdf75ed6d09b3810b
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72428041"
---
# <a name="hyperscale-service-tier"></a>Warstwa usługi Hiperskala

Azure SQL Database jest oparta na architekturze SQL Server Database Engine, która jest dostosowywana do środowiska chmury w celu zapewnienia dostępności 99,99% nawet w przypadku awarii infrastruktury. Istnieją trzy modele architektury, które są używane w Azure SQL Database:
- Ogólnego przeznaczenia/Standard 
-  Hiperskalowanie
-  Krytyczne dla działania firmy/Premium

Warstwa usługi do skalowania w Azure SQL Database to najnowsza warstwa usługi w modelu zakupu opartego na rdzeń wirtualny. Ta warstwa usług jest wysoce skalowalną warstwą wydajności magazynu i obliczeń, która wykorzystuje architekturę platformy Azure do skalowania w poziomie magazynu i zasobów obliczeniowych dla Azure SQL Database znacznie przekraczające limity dostępne dla Ogólnego przeznaczenia i firmy Krytyczne warstwy usług.

> 
> [!NOTE]
> Aby uzyskać szczegółowe informacje na temat Ogólnego przeznaczenia i Krytyczne dla działania firmy warstw usług w modelu zakupu opartego na rdzeń wirtualny, zobacz [ogólnego przeznaczenia](sql-database-service-tier-general-purpose.md) i [krytyczne dla działania firmy](sql-database-service-tier-business-critical.md) warstwy usług. Aby zapoznać się z porównaniem modelu zakupu opartego na rdzeń wirtualny z modelem zakupów opartym na jednostkach DTU, zobacz [Azure SQL Database kupowanie modeli i zasobów](sql-database-service-tiers.md).


## <a name="what-are-the-hyperscale-capabilities"></a>Jakie są możliwości skalowania

Warstwa usługi w ramach skalowania w Azure SQL Database zapewnia następujące dodatkowe możliwości:

- Obsługa nawet 100 TB rozmiaru bazy danych
- Niemal natychmiastowe kopie zapasowe bazy danych (oparte na migawkach plików przechowywanych w usłudze Azure Blob Storage) bez względu na rozmiar bez wpływu we/wy na zasoby obliczeniowe  
- Szybka baza danych przywraca (w oparciu o migawki plików) w ciągu kilku minut, a nie godzin lub dni (nie jest to rozmiar operacji na danych)
- Wyższa ogólna wydajność z powodu większej przepływności dzienników i krótszych czasów zatwierdzania transakcji niezależnie od woluminów danych
- Szybkie skalowanie w poziomie — można udostępnić co najmniej jeden węzeł tylko do odczytu w celu odciążenia obciążenia odczytu i użycia jako rezerwy gorącą.
- Szybkie skalowanie w górę — możesz w stałym czasie skalować zasoby obliczeniowe tak, aby pomieściły duże obciążenia, jak i w razie potrzeby, a następnie skalować zasoby obliczeniowe w razie potrzeby.

Warstwa usługi do skalowania usuwa wiele praktycznych ograniczeń tradycyjnie widzianych w bazach danych w chmurze. W przypadku, gdy większość innych baz danych jest ograniczona przez zasoby dostępne w jednym węźle, bazy danych w warstwie usługi w ramach skalowania nie mają takich ograniczeń. W przypadku elastycznej architektury pamięci masowej magazyn rośnie w razie konieczności. W rzeczywistości bazy danych nie są tworzone ze zdefiniowanym maksymalnym rozmiarem. Baza danych w ramach skalowania rośnie w miarę potrzeby — a opłaty są naliczane tylko za używane zasoby. W przypadku obciążeń intensywnie korzystających z odczytu warstwa usługi w ramach skalowania umożliwia szybkie skalowanie w poziomie przez inicjowanie obsługi dodatkowych replik odczytu w razie potrzeby w celu odciążenia operacji odczytu.

Ponadto czas wymagany do utworzenia kopii zapasowej bazy danych lub skalowania w górę lub w dół nie jest już związany z ilością danych w bazie danych. Kopie zapasowe baz danych można wykonać w sposób niemal natychmiastowy. Bazę danych można także skalować w ciągu kilku minut w górę lub w dół. Ta funkcja umożliwia zwolnienie Cię z obaw związanych z zapisaniem konfiguracji początkowej.

Aby uzyskać więcej informacji na temat rozmiarów obliczeń dla warstwy usługi w ramach skalowania, zobacz [Charakterystyka warstwy usług](sql-database-service-tiers-vcore.md#service-tier-characteristics).

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Kto powinien wziąć pod uwagę warstwę usługi do skalowania

Warstwa usługi do skalowania jest przeznaczona dla większości obciążeń firmowych, ponieważ zapewnia dużą elastyczność i wysoką wydajność dzięki niezależnej skalowalnemu zasobom obliczeniowym i magazynu. Dzięki możliwości automatycznego skalowania magazynu do 100 TB jest to doskonały wybór dla klientów, którzy:

- W przypadku dużych baz danych w środowisku lokalnym i chcesz przeprowadzić modernizację swoich aplikacji, przechodząc do chmury
- Znajdują się już w chmurze i są ograniczone przez maksymalne ograniczenia rozmiaru bazy danych innych warstw usług (1-4 TB)
- Mają mniejsze bazy danych, ale wymagają szybkiego skalowania w pionie i w poziomie, wysokiej wydajności, natychmiastowej kopii zapasowej i szybkiego przywracania bazy danych.

Warstwa usługi do skalowania obsługuje szeroką gamę obciążeń SQL Server, od czystej OLTP do czystej analizy, ale jest głównie zoptymalizowana pod kątem obciążeń OLTP i transakcji hybrydowych i przetwarzania analitycznego (HTAP).

> [!IMPORTANT]
> Pule elastyczne nie obsługują warstwy usługi skalowania w górę.

## <a name="hyperscale-pricing-model"></a>Model cen w ramach skalowania

Warstwa usługi do skalowania jest dostępna tylko w [modelu rdzeń wirtualny](sql-database-service-tiers-vcore.md). Aby dostosować się do nowej architektury, model cen jest nieco różny od Ogólnego przeznaczenia lub Krytyczne dla działania firmy warstw usług:

- **Obliczenia**:

  Cena jednostkowa obliczeń w ramach skalowania jest przypadana na replikę. [Korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) cena jest stosowana do automatycznego odczytu replik w skali. Utworzymy replikę podstawową i jedną replikę tylko do odczytu dla bazy danych w ramach jednej skali.  Użytkownicy mogą dostosowywać łączną liczbę replik z uwzględnieniem elementu podstawowego z 1-5.

- **Magazyn**:

  Nie trzeba określać maksymalnego rozmiaru danych podczas konfigurowania bazy danych w ramach skalowania. W warstwie Hiperskalowanie opłaty za magazyn w przypadku bazy danych są oparte na rzeczywistym użyciu. Magazyn jest automatycznie przydzielany z zakresu od 10 GB do 100 TB, w przyrostach, które są dynamicznie dostosowywane do zakresu od 10 GB do 40 GB.  

Aby uzyskać więcej informacji na temat cen ze skalowaniem, zobacz [Cennik usługi Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>Architektura funkcji rozproszonych

W przeciwieństwie do tradycyjnych aparatów baz danych, które mają scentralizowane wszystkie funkcje zarządzania danymi w jednej lokalizacji/procesie (podobnie jak w przypadku rozproszonych baz danych w środowisku produkcyjnym dzisiaj mają wiele kopii aparatu danych monolitycznych), wielowymiarowa baza danych jest oddzielona Aparat przetwarzania zapytań, w którym semantyka różnych aparatów danych odbiega od składników, które zapewniają długoterminowy magazyn i trwałość danych. Dzięki temu pojemność magazynu może być bezproblemowo skalowana w miarę potrzeb (początkowa wartość docelowa to 100 TB). Repliki tylko do odczytu współużytkują te same składniki magazynu, więc żadne kopie danych nie są wymagane do uruchomienia nowej repliki możliwej do odczytu. 

Na poniższym diagramie przedstawiono różne typy węzłów w bazie danych w skali:

![architektura](./media/sql-database-hyperscale/hyperscale-architecture.png)

Baza danych wieloskali zawiera następujące różne typy składników:

### <a name="compute"></a>Wystąpienia obliczeniowe

Węzeł obliczeniowy to miejsce, w którym działa silnik relacyjny, więc wszystkie elementy języka, przetwarzanie zapytań i tak dalej występują. Wszystkie interakcje użytkownika z bazą danych w ramach skalowania są wykonywane za pomocą tych węzłów obliczeniowych. Węzły obliczeniowe mają pamięć podręczną opartą na dyskach SSD (z etykietami RBPEX-odporny na błędy w powyższym diagramie), aby zminimalizować liczbę podróży w sieci wymaganych do pobrania strony danych. Istnieje jeden podstawowy węzeł obliczeniowy, w którym są przetwarzane wszystkie obciążenia odczytu i zapisu. Istnieje co najmniej jeden pomocniczy węzeł obliczeniowy działający jako węzły rezerwy aktywnej do pracy w trybie failover, a także działający jako węzeł obliczeniowy tylko do odczytu do odciążania obciążeń odczytu (Jeśli ta funkcja jest wymagana).

### <a name="page-server"></a>Serwer stronicowy

Serwery stron to systemy reprezentujące skalowalny w poziomie aparat magazynu.  Każdy serwer stron jest odpowiedzialny za podzbiór stron w bazie danych.  W sposób nominalny każdy serwer stronicowania kontroluje między 128 GB i 1 TB danych. Żadne dane nie są udostępniane na więcej niż jednym serwerze stronicowania (poza replikami, które są przechowywane pod kątem nadmiarowości i dostępności). Zadaniem serwera stronicowania jest obsługiwanie stron bazy danych do węzłów obliczeniowych na żądanie i aktualizowanie stron jako transakcji, które aktualizują dane. Serwery stronicowania są aktualne przez odtworzenie rekordów dziennika z usługi log. Serwery stron obsługują również pamięć podręczną opartą na dyskach SSD w celu zwiększenia wydajności. Długoterminowe przechowywanie stron danych jest przechowywane w usłudze Azure Storage w celu zapewnienia dodatkowej niezawodności.

### <a name="log-service"></a>Usługa log

Usługa log przyjmuje rekordy dziennika z podstawowej repliki obliczeniowej, utrzymuje je w trwałej pamięci podręcznej i przekazuje rekordy dziennika do pozostałych replik obliczeniowych (aby można było zaktualizować ich pamięć podręczną) oraz odpowiednie serwery stron, aby można było je zaktualizować. jeszcze. W ten sposób wszystkie zmiany danych z podstawowej repliki obliczeniowej są propagowane za pośrednictwem usługi log na wszystkie pomocnicze repliki obliczeniowe i serwery stron. Na koniec rekordy dziennika są wypychane do długoterminowego przechowywania w usłudze Azure Storage, co jest praktycznie nieskończonym repozytorium magazynu. Ten mechanizm eliminuje konieczność częstego obcinania dzienników. Usługa log ma również lokalną pamięć podręczną umożliwiającą przyspieszenie dostępu do rekordów dzienników.

### <a name="azure-storage"></a>Azure Storage

Usługa Azure Storage zawiera wszystkie pliki danych w bazie danych. Serwery stron zachowują aktualne pliki danych w usłudze Azure Storage. Ten magazyn jest używany na potrzeby tworzenia kopii zapasowych, a także do replikacji między regionami platformy Azure. Kopie zapasowe są implementowane przy użyciu migawek magazynu plików danych. Operacje przywracania przy użyciu migawek są szybkie, niezależnie od rozmiaru danych. Dane można przywrócić do dowolnego punktu w czasie w ramach okresu przechowywania kopii zapasowej bazy danych.

## <a name="backup-and-restore"></a>Tworzenie i przywracanie kopii zapasowych

Kopie zapasowe są tworzone na podstawie migawek plików, a tym samym prawie chwilowo. Rozdzielenie magazynu i obliczeń umożliwia wypychanie operacji tworzenia kopii zapasowej/przywracania do warstwy magazynowania w celu zmniejszenia obciążenia związanego z przetwarzaniem w podstawowej replice obliczeniowej. W związku z tym kopia zapasowa bazy danych nie ma wpływu na wydajność podstawowego węzła obliczeniowego; Analogicznie, przywracanie odbywa się przez przywrócenie migawek plików, co nie jest rozmiarem operacji danych. Przywracanie jest operacją o stałym czasie, a nawet kilka baz danych można przywrócić w ciągu kilku minut, a nie godzin lub dni. Tworzenie nowych baz danych przez przywrócenie istniejącej kopii zapasowej obejmuje również korzystanie z tej funkcji: Tworzenie kopii bazy danych do celów deweloperskich i testowych, nawet w przypadku baz danych o rozmiarze terabajtów, jest doable w ciągu kilku minut.

## <a name="scale-and-performance-advantages"></a>Zalety skalowania i wydajności

Dzięki możliwości szybkiej zmiany w górę i w dół dodatkowych węzłów obliczeniowych tylko do odczytu architektura skalowania zapewnia znaczące możliwości skalowania odczytu, a także zwalnia podstawowy węzeł obliczeniowy do obsługi większej liczby żądań zapisu. Ponadto węzły obliczeniowe można szybko skalować w górę i w dół ze względu na architekturę magazynu udostępnionego w architekturze.

## <a name="create-a-hyperscale-database"></a>Tworzenie bazy danych w ramach skalowania

Bazę danych ze skalą można utworzyć przy użyciu [Azure Portal](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current), [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabase) lub [interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create). Bazy danych ze skalowaniem są dostępne tylko przy użyciu [modelu zakupu opartego na rdzeń wirtualny](sql-database-service-tiers-vcore.md).

Następujące polecenie T-SQL tworzy bazę danych w skali. W instrukcji `CREATE DATABASE` należy określić zarówno cel wersji, jak i usługi. Zapoznaj się z [limitami zasobów](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases#hyperscale-service-tier-for-provisioned-compute) , aby uzyskać listę prawidłowych celów usługi.

```sql
-- Create a HyperScale Database
CREATE DATABASE [HyperScaleDB1] (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```
Spowoduje to utworzenie bazy danych w ramach skalowania na 5 rdzeń sprzęt z 4 rdzeniami.

## <a name="migrate-an-existing-azure-sql-database-to-the-hyperscale-service-tier"></a>Migrowanie istniejącego Azure SQL Database do warstwy usługi w ramach skalowania

Istniejące bazy danych Azure SQL można przenieść do skalowania przy użyciu [Azure Portal](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current), [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) lub [interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update). W tej chwili jest to jednokierunkowa migracja. Nie można przenieść baz danych ze skalowania do innej warstwy usług, poza eksportowaniem i importowaniem danych. W przypadku dowodu koncepcji (POCs) zalecamy utworzenie kopii produkcyjnych baz danych i migrowanie kopii do obszaru skalowania. Migrowanie istniejącej bazy danych Azure SQL Database do warstwy skalowania jest rozmiarem operacji na danych.

Następujące polecenie T-SQL przenosi bazę danych do warstwy usługi. W instrukcji `ALTER DATABASE` należy określić zarówno cel wersji, jak i usługi.

```sql
-- Alter a database to make it a HyperScale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Nawiązywanie połączenia z repliką w skali odczytu bazy danych

W przypadku baz danych w ramach skalowania `ApplicationIntent` argument w parametrach połączenia dostarczonych przez klienta wskazuje, czy połączenie jest kierowane do repliki zapisu, czy do repliki pomocniczej tylko do odczytu. Jeśli `ApplicationIntent` ustawiono na `READONLY`, a baza danych nie ma repliki pomocniczej, połączenie zostanie przekazane do repliki podstawowej i domyślnie zostanie użyte zachowanie `ReadWrite`.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Repliki pomocnicze w ramach skalowania są identyczne, przy użyciu tego samego celu poziomu usługi co replika podstawowa. Jeśli istnieje więcej niż jedna replika pomocnicza, obciążenie jest dystrybuowane między wszystkie dostępne pomocnicze. Każda replika pomocnicza jest aktualizowana niezależnie, więc różne repliki mogą mieć różne opóźnienia danych względem repliki podstawowej.

## <a name="database-high-availability-in-hyperscale"></a>Wysoka dostępność bazy danych w ramach skalowania

Podobnie jak w przypadku wszystkich innych warstw usług, funkcja Moja Skala gwarantuje trwałość danych dla zatwierdzonych transakcji niezależnie od dostępności repliki obliczeniowej. Zakres przestoju spowodowany przez replikę podstawową staje się niedostępny, zależy od typu trybu failover (zaplanowanego a nieplanowanego) oraz od obecności co najmniej jednej repliki pomocniczej. W planowanej pracy w trybie failover (tj. zdarzeniu konserwacji) system tworzy nową replikę podstawową przed zainicjowaniem trybu failover lub używa istniejącej repliki pomocniczej jako lokalizacji docelowej trybu failover. W przypadku nieplanowanego przejścia w tryb failover (tj. awaria sprzętowa repliki podstawowej) system używa repliki pomocniczej jako miejsca docelowego trybu failover, jeśli taki istnieje, lub tworzy nową replikę podstawową z puli dostępnej pojemności obliczeniowej. W tym drugim przypadku czas przestoju jest dłuższy z powodu dodatkowych kroków wymaganych do utworzenia nowej repliki podstawowej.

Aby zapoznać się z umową SLA, zobacz [Umowa SLA dla Azure SQL Database](https://azure.microsoft.com/support/legal/sla/sql-database/).

## <a name="disaster-recovery-for-hyperscale-databases"></a>Odzyskiwanie po awarii dla baz danych w ramach skalowania

### <a name="restoring-a-hyperscale-database-to-a-different-geography"></a>Przywracanie bazy danych ze skalowaniem do innej lokalizacji geograficznej
Jeśli trzeba przywrócić Azure SQL Database przeskalować bazę danych do regionu innego niż ten, w którym jest obecnie hostowana, w ramach operacji odzyskiwania po awarii lub przechodzenia do szczegółów, relokacji lub z innego powodu, podstawowa metoda polega na wykonaniu przywracania geograficznego bazy danych.  Obejmuje to dokładnie te same kroki jak w przypadku przywracania dowolnej innej bazy danych AZURE SQL w innym regionie:
1. Utwórz serwer SQL Database w regionie docelowym, jeśli jeszcze nie masz odpowiedniego serwera.  Ten serwer powinien należeć do tej samej subskrypcji co oryginalny serwer (źródłowy).
2. Postępuj zgodnie z instrukcjami w temacie dotyczącym [przywracania geograficznego](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#geo-restore) strony na przywracaniu baz danych Azure SQL z automatycznych kopii zapasowych.

> [!NOTE]
> Ze względu na to, że źródło i cel znajdują się w oddzielnych regionach, baza danych nie może współużytkować magazynu migawek ze źródłową bazą danych jako do przywracania geograficznego, co kończy się niezwykle szybko.  W przypadku przywracania geograficznego bazy danych w ramach skalowania jest to operacja o rozmiarze danych, nawet jeśli obiekt docelowy znajduje się w sparowanym regionie magazynu z replikacją geograficzną.  Oznacza to, że wykonanie operacji przywracania geograficznego będzie trwać proporcjonalnie do rozmiaru przywracanej bazy danych.  Jeśli obiekt docelowy znajduje się w sparowanym regionie, kopia będzie znajdować się w centrum danych, co będzie znacznie szybsze niż kopiowanie na dużą odległość przez Internet, ale nadal będzie kopiować wszystkie bity.

## <a name=regions></a>Dostępne regiony

Warstwa skalowania Azure SQL Database jest obecnie dostępna w następujących regionach:

- Australia Wschodnia
- Australia Południowo-Wschodnia
- Brazylia Południowa
- Kanada Środkowa
- Środkowe stany USA
- Chiny Wschodnie 2
- Chiny Północne 2
- Azja Wschodnia
- Wschodnie stany USA
- Wschodnie stany USA 2
- Francja Środkowa
- Japonia Wschodnia
- Japonia Zachodnia
- Korea Środkowa
- Korea Południowa
- Północno-środkowe stany USA
- Europa Północna
- Północna Republika Południowej Afryki
- Południowo-środkowe stany USA
- Azja Południowo-Wschodnia
- Południowe Zjednoczone Królestwo
- Zachodnie Zjednoczone Królestwo
- Europa Zachodnia
- Zachodnie stany USA
- Zachodnie stany USA 2

Jeśli chcesz utworzyć bazę danych w formie wieloskali w regionie, który nie jest wymieniony jako obsługiwany, możesz wysłać żądanie dołączania za pośrednictwem Azure Portal. Pracujemy nad rozwinięciem listy obsługiwanych regionów, aby ponownie sprawdzić listę najnowszych regionów.

Aby poprosić o możliwość tworzenia baz danych w regionach, których nie ma na liście:

1. Przejdź do [bloku pomoc i obsługa techniczna platformy Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

2. Kliknij [ **nowe żądanie obsługi**](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

    ![Blok pomocy i obsługi technicznej platformy Azure](media/sql-database-service-tier-hyperscale/request-screen-1.png)

3. W obszarze **typ problemu**wybierz pozycję **usługi i limity subskrypcji (przydziały)**

4. Wybierz subskrypcję, której chcesz użyć do utworzenia baz danych

5. W obszarze **Typ limitu przydziału**wybierz pozycję **baza danych SQL**

6. Kliknij przycisk **Dalej: rozwiązania**

1. Kliknij pozycję **Podaj szczegóły**

    ![Szczegóły problemu](media/sql-database-service-tier-hyperscale/request-screen-2.png)

8. Wybierz **SQL Database typ przydziału**: **inne żądanie limitu przydziału**

9. Wypełnij następujący szablon:

    ![Szczegóły przydziału](media/sql-database-service-tier-hyperscale/request-screen-3.png)

    Podaj następujące informacje w szablonie

    > Żądanie utworzenia SQL Database w nowym regionie platformy Azure<br/> Region: [Wypełnij żądany region]  <br/>
    > Jednostka SKU obliczeń/łączna liczba rdzeni łącznie z replikami do odczytu <br/>
    > Szacowana liczba TB 
    >

10. Wybierz **ważność C**.

11. Wybierz odpowiednią metodę kontaktu i wypełnij szczegóły.

12. Kliknij przycisk **Zapisz** i **Kontynuuj** .

## <a name="known-limitations"></a>Znane ograniczenia
Są to bieżące ograniczenia dotyczące warstwy usług w ramach skalowania na poziomie.  Aktywnie pracujemy nad usunięciem możliwie największej liczby ograniczeń.

| Problem | Opis |
| :---- | :--------- |
| Okienko zarządzanie kopiami zapasowymi dla serwera logicznego nie pokazuje przefiltrowanych baz danych z programu SQL Server  | Funkcja Moja Skala ma oddzielną metodę zarządzania kopiami zapasowymi, a w związku z tym ustawienia przechowywania kopii zapasowych długoterminowego przechowywania i punktu w czasie nie mają zastosowania/są unieważnione. W związku z tym bazy danych nie są wyświetlane w okienku zarządzanie kopią zapasową. |
| Przywracanie do określonego momentu | Po przeprowadzeniu migracji bazy danych do warstwy usługi w warstwie skalowania Przywróć do punktu w czasie przed migracją nie jest obsługiwane.|
| Przywracanie bazy danych bez skalowania do Hypserscale i na odwrót | Nie można przywrócić bazy danych w ramach skalowania do bazy danych bez skalowania ani przywracania bazy danych bez skalowania do bazy danych w skali.|
| Jeśli baza danych ma co najmniej jeden plik danych o rozmiarze większym niż 1 TB, migracja nie powiedzie się | W niektórych przypadkach może być możliwe obejście tego problemu, zmniejszając duże ilości plików poniżej 1 TB. W przypadku migrowania bazy danych używanej podczas procesu migracji upewnij się, że żaden plik nie będzie większy niż 1 TB. Użyj następującego zapytania, aby określić rozmiar plików bazy danych. `SELECT *, name AS file_name, size * 8. / 1024 / 1024 AS file_size_GB FROM sys.database_files WHERE type_desc = 'ROWS'`;|
| Wystąpienie zarządzane | Azure SQL Database wystąpienie zarządzane nie jest obecnie obsługiwane w bazach danych. |
| Pule elastyczne |  Pule elastyczne nie są obecnie obsługiwane z użyciem funkcji wieloskalowania SQL Database.|
| Migracja do funkcji Moje skalowanie jest obecnie operacją jednokierunkową | Po przeprowadzeniu migracji bazy danych do warstwy usługi nie można migrować jej bezpośrednio na warstwę usług, która nie jest w skali. W obecnym czasie jedynym sposobem migrowania bazy danych z Azure Databricks Azure Data Factory funkcji ze skalowaniem do poziomu non-------------------------------------------|
| Migracja baz danych z trwałymi obiektami w pamięci | Funkcja przeskalowania obsługuje tylko nietrwałe obiekty w pamięci (typy tabel, natywne SPs i funkcje).  Trwałe tabele w pamięci i inne obiekty muszą zostać porzucone i odtworzone jako obiekty nieznajdujące się w pamięci przed migracją bazy danych do warstwy usługi.|
| Śledzenie zmian | Nie można jeszcze skonfigurować i używać Change Tracking z bazami danych Azure SQL Database. |
| Replikacja geograficzna  | Nie można jeszcze skonfigurować replikacji geograficznej na potrzeby Azure SQL Database skalowania. |
| Kopia bazy danych | Nie można jeszcze użyć kopii bazy danych w celu utworzenia nowej bazy danych w funkcji wieloskalowania SQL platformy Azure. |
| Integracja TDE/AKV | Szyfrowanie przezroczystej bazy danych przy użyciu Azure Key Vault (nazywanego też kluczem "Przenieś jako własne-Key" lub BYOK) nie jest jeszcze obsługiwane dla Azure SQL Database funkcji TDE, ale z kluczami zarządzanymi przez usługę jest w pełni obsługiwane. |
|Funkcje inteligentnej bazy danych | Z wyjątkiem opcji "Wymuś plan" wszystkie inne opcje dostrajania automatycznego nie są jeszcze obsługiwane w obszarze skalowanie: opcje mogą być dostępne, ale nie zostaną wykonane żadne zalecenia ani działania. |

## <a name="next-steps"></a>Następne kroki

- Często zadawane pytania dotyczące skalowania można znaleźć na [często zadawanych pytaniach dotyczących skalowania](sql-database-service-tier-hyperscale-faq.md).
- Aby uzyskać informacje o warstwach usług, zobacz [warstwy usług](sql-database-service-tiers.md)
- Aby uzyskać informacje na temat limitów na poziomach serwera i subskrypcji, zobacz [Omówienie limitów zasobów na serwerze logicznym](sql-database-resource-limits-logical-server.md) .
- Aby uzyskać ograniczenia modelu zakupów dla pojedynczej bazy danych, zobacz [Azure SQL Database limity modelu zakupu opartego na rdzeń wirtualny dla jednej bazy danych](sql-database-vcore-resource-limits-single-databases.md).
- Aby zapoznać się z funkcjami i listą porównania, zobacz [funkcje wspólne SQL](sql-database-features.md).
