---
title: Usługi Azure SQL Database na dużą skalę — omówienie | Dokumentacja firmy Microsoft
description: W tym artykule opisano warstwę usługi na dużą skalę w modelu zakupu opartego na rdzeniach wirtualnych w usłudze Azure SQL Database oraz wyjaśniono, jak to różni się od warstwy usług ogólnego przeznaczenia i krytyczne dla działania firmy.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 05/06/2019
ms.openlocfilehash: 4aeda5612b2b3e9e2073a65320b238266c8bb33a
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537856"
---
# <a name="hyperscale-service-tier-for-up-to-100-tb"></a>Warstwy usług w Hiperskali dla maksymalnie 100 TB

Usługa Azure SQL Database jest oparty na architekturę aparatu bazy danych programu SQL Server, która jest uwzględniany w środowisku chmury w celu zapewnienia dostępności 99,99%, nawet w przypadku wystąpienia awarii infrastruktury. Istnieją trzy modele architektury, które są używane w usłudze Azure SQL Database:
- Ogólnego przeznaczenia/Standard 
-  Hiperskala
-  Business Critical/Premium

Warstwy usług na dużą skalę w usłudze Azure SQL Database jest najnowsza warstwy usług w modelu zakupu opartego na rdzeniach wirtualnych. Ta warstwa usługi jest wysoce skalowalny magazyn i warstwy wydajności obliczeniowej, który korzysta z architektury platformy Azure do skalowania magazynu i zasoby obliczeniowe dla usługi Azure SQL Database w znacznym stopniu po przekroczeniu limitów dostępnych dla firm i ogólnego przeznaczenia Warstwy usług krytycznych.

> 
> [!NOTE]
> Aby uzyskać więcej informacji o warstwach usług ogólnego przeznaczenia i krytyczne dla działania firmy w modelu zakupu opartego na rdzeniach wirtualnych, zobacz [ogólnego przeznaczenia](sql-database-service-tier-general-purpose.md) i [krytyczne dla działania firmy](sql-database-service-tier-business-critical.md) warstwy usług. Dla porównania modelu zakupu opartego na rdzeniach wirtualnych za pomocą modelu zakupu opartego na jednostkach DTU, zobacz [zakupu modeli i zasobów bazy danych SQL Azure](sql-database-service-tiers.md).


## <a name="what-are-the-hyperscale-capabilities"></a>Jakie są możliwości na dużą skalę

Warstwy usług na dużą skalę w usłudze Azure SQL Database zapewnia następujące dodatkowe funkcje:

- Obsługa do 100 TB, rozmiar bazy danych
- Niemal natychmiastowych kopii zapasowych, (na podstawie migawki plików przechowywanych w usłudze Azure Blob storage) bazy danych bez względu na rozmiar wpływu we/wy na zasoby obliczeniowe  
- Szybkie przywracanie bazy danych (na podstawie migawki plików) w ciągu kilku minut, a nie godzin lub dni (nie rozmiar operacji na danych)
- Ogólną wydajność ze względu na większą przepływność dziennika i krótszy czas zatwierdzenia transakcji, niezależnie od ilości danych
- Szybkie skalowanie w poziomie — można udostępnić co najmniej jeden węzeł tylko do odczytu dla odciążania obciążenia odczytu i do użytku jako spełniają hot
- Szybkie skalowanie w górę — możesz można w stałym czasie skalować zasoby obliczeniowe, aby pomieścić dużych obciążeń, jak i kiedy potrzebne i następnie skalowanie zasobów obliczeniowych wróci, gdy nie jest potrzebny.

Warstwy usługi w Hiperskali usuwa wielu praktycznych limitów tradycyjnie widoczne w bazach danych w chmurze. Gdzie większość innych baz danych są ograniczone zasoby dostępne w jednym węźle baz danych w warstwie usługi w Hiperskali mają bez tych ograniczeń. Za pomocą jego architekturę elastycznych systemów pamięci magazynu rośnie, zgodnie z potrzebami. W rzeczywistości baz danych na dużą skalę, nie są tworzone z zdefiniowany maksymalny rozmiar. Bazę danych na dużą skalę wraz ze wzrostem, stosownie do potrzeb — i opłata jest naliczana tylko za pojemność, której używasz. Warstwy usług na dużą skalę intensywnie odczytujących obciążeń zapewnia szybkiego skalowania w poziomie przez udostępnienie dodatkowe repliki do odczytu, w razie potrzeby przeniesienie obciążeń odczytu.

Ponadto czas wymagany do tworzenia kopii zapasowych bazy danych lub Skaluj w górę lub w dół nie jest już powiązany z ilością danych w bazie danych. Bazy danych na dużą skalę kopię zapasową można wykonywać niemal natychmiastowe. Baza danych w dziesiątki terabajtów można również skalować w górę lub w dół, w ciągu kilku minut. Ta funkcja powoduje zwolnienie z wątpliwości dotyczących są zapakowane w przez wybrane opcje konfiguracji początkowej.

Aby uzyskać więcej informacji na temat rozmiarów wystąpień obliczeniowych dla warstwy usług na dużą skalę, zobacz [właściwości warstwy usługi](sql-database-service-tiers-vcore.md#service-tier-characteristics).

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Kto powinien rozważyć warstwę usługi na dużą skalę

O dużej skali, z którą warstwę usług jest przeznaczone głównie dla klientów, którzy mają dużych baz danych albo w środowisku lokalnym i chcesz modernizuj swoje aplikacje dzięki przeniesieniu do chmury lub dla klientów, którzy są już w chmurze i są ograniczone przez maksymalny rozmiar bazy danych ograniczenia (1 – 4 TB). Celem jest także dla klientów, którzy wyszukiwania o wysokiej wydajności i wysokiej skalowalności dla magazynu i obliczeń.

Warstwy usługi w Hiperskali obsługuje wszystkich obciążeń programu SQL Server, ale przede wszystkim jest on zoptymalizowany pod kątem OLTP. Warstwy usługi w Hiperskali obsługuje także hybrydowych i analityczne obciążenia (składnicy danych).

> [!IMPORTANT]
> Pule elastyczne nie obsługują warstwy usług na dużą skalę.

## <a name="hyperscale-pricing-model"></a>Model cen na dużą skalę

Warstwy usługi w Hiperskali jest dostępna tylko w [modelu rdzenia wirtualnego](sql-database-service-tiers-vcore.md). Aby wyrównać za pomocą nowej architektury, model rozliczania usług jest nieco różne od ogólnego przeznaczenia i krytyczne dla działania firmy warstwach usługi:

- **Obliczenia**:

  Cena jednostkowa obliczeń na dużą skalę odbywa się dla repliki. [Korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) cena jest stosowana do odczytu replik skalowania automatycznego. Domyślnie możemy utworzyć replikę podstawową i jedną replikę tylko do odczytu na bazę danych na dużą skalę.  Użytkownik może zmienić łącznej liczby replik, w tym podstawowego od 1 do 5.

- **Magazyn**:

  Nie trzeba określić rozmiar maksymalny danych, podczas konfigurowania bazy danych na dużą skalę. W warstwie Hiperskalowanie opłaty za magazyn w przypadku bazy danych są oparte na rzeczywistym użyciu. Magazyn jest przydzielany automatycznie między 10 GB i 100 TB, w przyrostach, które są dynamicznie dostosowywana między 10 GB i 40 GB.  

Aby uzyskać więcej informacji na temat cen na dużą skalę, zobacz [cennik usługi Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>Architektura rozproszona funkcji

W przeciwieństwie do tradycyjnych baz danych, które mają scentralizowane wszystkie funkcje zarządzania danych w jednej lokalizacji/process (nawet więc o nazwie rozproszonych baz danych w środowisku produkcyjnym już dziś wiele kopii aparatu monolityczne danych) oddziela bazę danych na dużą skalę aparat przetwarzania zapytań, gdzie semantyka różnych aparatów danych rozdzielić ze składników, które zapewniają długoterminowego przechowywania i trwałości dla danych. W ten sposób pojemności magazynu można sprawnie skalować w poziomie w zakresie, w jakim potrzebne (początkowa docelowy to 100 TB). Repliki tylko do odczytu udostępniać te same składniki magazynu, więc żadna kopia danych jest wymagany do uruchomienia nowej repliki do odczytu. 

Na poniższym diagramie przedstawiono różne rodzaje węzłów w bazie danych o dużej skali:

![architektura](./media/sql-database-hyperscale/hyperscale-architecture.png)

Bazę danych na dużą skalę zawiera różne rodzaje węzłów:

### <a name="compute-node"></a>Węzeł obliczeniowy

Węzeł obliczeniowy jest, gdzie znajduje się w aparacie relacyjnym, dzięki czemu występują wszystkie elementy języka, przetwarzanie zapytań i tak dalej. Wszystkie interakcje użytkownika z bazy danych na dużą skalę możliwe za pomocą węzłów obliczeniowych. Obliczenia węzły mają oparte na dyskach SSD (oznaczony RBPEX - odporne na błędy rozszerzenie puli buforów na powyższym diagramie) pamięci podręczne, aby zminimalizować liczbę sieci dwustronne wymagane do pobierania strony danych. Ma w jednym węźle obliczeniowym podstawowy, w których są przetwarzane obciążenia odczytu i zapisu oraz transakcji. Istnieją co najmniej jeden węzeł pomocnicza usługa obliczeniowa, które działają jako gorąca wstrzymania węzłów na potrzeby trybu failover, a także działać jako węzły obliczeniowe tylko do odczytu dla odciążania, przeczytaj obciążeń (Jeśli ta funkcja jest konieczne).

### <a name="page-server-node"></a>Węzeł serwera strony

Serwery na stronie są systemy reprezentujący aparatu magazynu skalowanych w poziomie.  Każdy serwer strony jest odpowiedzialny za podzbiór stron w bazie danych.  Nominalnie każdy serwer formantów strony od 128 GB do 1 TB danych. Żadne dane nie są udostępniane w więcej niż jeden serwer strony (poza repliki, które są przechowywane w celu zapewnienia nadmiarowości i dostępności). Zadania serwera strony jest obsługiwać strony bazy danych w węzłach obliczeniowych na żądanie i zachować strony, zaktualizować, ponieważ transakcje aktualizacji danych. Serwery na stronie są zawsze na bieżąco przez odtworzenie rekordów dziennika usługi dziennika. Serwery na stronie utrzymaniem oparte na dyskach SSD pamięci podręczne, aby zwiększyć wydajność. Możliwość długoterminowego magazynowania danych strony jest przechowywana w usłudze Azure Storage dla dodatkowej niezawodności.

### <a name="log-service-node"></a>Węzeł usługi dziennika

Węzła usługi log akceptuje rekordów dziennika z węzła obliczeniowego podstawowego utrzymuje się je w trwałość pamięci podręcznej i przekazuje rekordy dziennika do pozostałych węzłów obliczeniowych (dzięki czemu mogą aktualizować ich pamięciami podręcznymi) oraz na serwerach odpowiednie strony, aby dane mogą być zaktualizowane t w tym miejscu. W ten sposób wszystkie zmiany danych z węzła obliczeniowego podstawowego są propagowane przez usługę log do wszystkich węzłów obliczeniowych dodatkowej i serwery na stronie. Na koniec rekordy dziennika są przekazywane do magazynu długoterminowego w usłudze Azure Storage, która jest repozytorium nieograniczony magazyn. Ten mechanizm eliminuje konieczność obcinanie dziennika częste. Usługa dziennika ma również lokalnej pamięci podręcznej, aby przyspieszyć dostęp.

### <a name="azure-storage-node"></a>Węzeł usługi Azure storage

Węzeł usługi Azure storage jest ostatecznym miejscem docelowym danych z serwerów strony. Ten magazyn jest używany do wykonywania kopii zapasowych, a także jak w przypadku replikacji między regionami platformy Azure. Tworzenie kopii zapasowych składają się z migawkami plików danych. Przywracanie operacji są szybkie z migawek i przywrócenie danych do dowolnego punktu w czasie.

## <a name="backup-and-restore"></a>Tworzenie kopii zapasowej i przywracanie

Kopie zapasowe są base migawki plików i dlatego są prawie natychmiastowe. Rozdzielenie magazynu i mocy obliczeniowej Włącz wypychanie szczegółów operacji wykonywania kopii zapasowej i przywracania do warstwy magazynu zmniejszyć obciążenie przetwarzania w węźle obliczeniowym podstawowego. W rezultacie kopii zapasowej dużej bazy danych nie ma wpływu na wydajność węzła obliczeniowego podstawowego. Podobnie przeprowadzać operacje przywracania są wykonywane tylko przez kopiowanie migawki plików i jako takie nie rozmiar operacji danych. Aby przeprowadzać operacje przywracania w obrębie tego samego konta magazynu operacja przywracania jest szybkie.

## <a name="scale-and-performance-advantages"></a>Zalety skalowalność i wydajność

Dzięki możliwości szybkiego uruchomienia dodatkowych tylko do odczytu węzłów obliczeniowych w górę/w dół Hiperskali architektura umożliwia znaczne odczyt możliwości skalowania i można także zwolnić węzła obliczeniowego podstawowego do obsługi liczby żądań zapisu. Ponadto węzły obliczeniowe można skalować w górę/w dół szybko z uwagi na architekturę magazyn udostępniony architektury na dużą skalę.

## <a name="create-a-hyperscale-database"></a>Tworzenie bazy danych na dużą skalę

Można utworzyć bazę danych na dużą skalę za pomocą [witryny Azure portal](https://portal.azure.com), [języka T-SQL](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current), [Powershell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabase) lub [interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create). Bazy danych w Hiperskali są dostępne, tylko przy użyciu [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md).

Następujące polecenie języka T-SQL tworzy bazę danych na dużą skalę. Należy określić zarówno wersji, jak i usługa cel w `CREATE DATABASE` instrukcji. Zapoznaj się [limity zasobów](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases#hyperscale-service-tier) Lista celów prawidłową usługę.

```sql
-- Create a HyperScale Database
CREATE DATABASE [HyperScaleDB1] (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```
Spowoduje to utworzenie bazy danych na dużą skalę na sprzęcie 5. generacji z 4 rdzenie.

## <a name="migrate-an-existing-azure-sql-database-to-the-hyperscale-service-tier"></a>Migrowanie istniejącej bazy danych SQL Azure do warstwy usług na dużą skalę

Można przenieść istniejące bazy danych Azure SQL na dużą skalę za pomocą [witryny Azure portal](https://portal.azure.com), [języka T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current), [Powershell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) lub [interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update). W tym momencie jest to jednokierunkowe migracji. Nie można przenieść bazy danych w Hiperskali, do innej warstwy usług. Firma Microsoft zaleca, Utwórz kopię produkcyjnych bazach danych i migrację do w Hiperskali dla weryfikacji koncepcji (weryfikacji koncepcji).

Następujące polecenie języka T-SQL przenosi bazę danych do warstwy usług na dużą skalę. Należy określić zarówno wersji, jak i usługa cel w `ALTER DATABASE` instrukcji.

```sql
-- Alter a database to make it a HyperScale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Połącz się z repliką skalę odczytywania, bazy danych na dużą skalę

W przypadku baz danych na dużą skalę `ApplicationIntent` argumentu w ciągu połączenia, udostępniane przez klienta określa, czy połączenie jest kierowany do repliki zapisu lub tylko do odczytu repliki pomocniczej. Jeśli `ApplicationIntent` równa `READONLY` i bazy danych nie ma w replice pomocniczej, połączenia będą kierowane do repliki podstawowej i wartość domyślna to `ReadWrite` zachowanie.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```
## <a name="disaster-recovery-for-hyperscale-databases"></a>Odzyskiwanie po awarii dla bazy danych na dużą skalę
### <a name="restoring-a-hyperscale-database-to-a-different-geography"></a>Przywracanie bazy danych na dużą skalę do innej lokalizacji geograficznej
Jeśli chcesz przywrócić bazy danych na dużą skalę bazy danych SQL Azure w regionie innym niż ten, który obecnie jest obsługiwany w operacji odzyskiwania po awarii lub testowania odzyskiwania po awarii, przenoszenia lub z innego powodu, w ramach podstawowej metody jest geoprzywracanie bazy danych.  Obejmuje to dokładnie te same kroki co można użyć w celu przywrócenia wszelkie inne bazy danych SQL AZURE do innego regionu:
1. Tworzenie serwera usługi SQL Database w regionie docelowym, jeśli nie masz już odpowiedniego serwera istnieje.  Ten serwer powinien należeć do tej samej subskrypcji co oryginalny serwer (źródło).
2. Postępuj zgodnie z instrukcjami w [geoprzywracanie](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#geo-restore) tematu strony na przywrócenie bazy danych SQL Azure z automatycznych kopii zapasowych.

> [!NOTE]
> Ponieważ źródłowy i docelowy znajdują się w oddzielnych regionach, baza danych nie mogą mieć Magazyn migawek źródłowej bazy danych, tak jak inne niż geograficznie przeprowadzać operacje przywracania, które są wykonywane bardzo szybko.  W przypadku przywracania geograficznego bazy danych na dużą skalę będzie operacją danych o rozmiarze, nawet jeśli miejsce docelowe jest w sparowanym regionie magazyn z replikacją geograficzną.  Oznacza to, że podczas przywracania geograficznego zajmie trochę czasu proporcjonalny do rozmiaru przywracana baza danych.  Jeśli element docelowy znajduje się w sparowanym regionie, kopia zostanie umieszczona w obrębie centrum danych, który będzie znacznie szybsze niż kopię zamiejscowych za pośrednictwem Internetu, ale wciąż będzie kopiować wszystkie bity.

## <a name=regions></a>Dostępne regiony

Warstwa Azure SQL Database na dużą skalę jest obecnie dostępna w następujących regionach:

- Australia Wschodnia
- Australia Południowo-Wschodnia
- Brazylia Południowa
- Kanada Środkowa
- Środkowe stany USA
- Chiny Wschodnie 2
- Chiny Północne 2
- Azja Wschodnia
- East US
- Wschodnie stany USA 2
- Francja Środkowa
- Japonia Wschodnia
- Japonia Zachodnia
- Korea Środkowa
- Korea Południowa
- Środkowo-północne stany USA
- Europa Północna
- Republika Południowej Afryki (północ)
- Środkowo-południowe stany USA
- Azja Południowo-Wschodnia
- Południowe Zjednoczone Królestwo
- Zachodnie Zjednoczone Królestwo
- Europa Zachodnia
- Zachodnie stany USA
- Zachodnie stany USA 2

Jeśli chcesz utworzyć bazę danych na dużą skalę w regionie, który nie jest wymieniony jako obsługiwany, możesz wysłać żądania przechodzenia do portalu Azure. Pracujemy nad rozwiń listę obsługiwanych regionów, dlatego sprawdź ją ponownie, aby wyświetlić najnowszą listę regionów.

Aby zażądać możliwość tworzenia baz danych na dużą skalę w regionach, nie na liście:

1. Przejdź do [Azure Pomoc i obsługa bloku](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

2. Kliknij pozycję [ **nowe żądanie obsługi**](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

    ![Pomoc platformy Azure i bloku obsługi](media/sql-database-service-tier-hyperscale/whitelist-request-screen-1.png)

3. Aby uzyskać **typ problemu**, wybierz opcję **limity usług i subskrypcji (przydziały)**

4. Wybierz subskrypcję, które są używane do tworzenia baz danych

5. Aby uzyskać **typ limitu przydziału**, wybierz opcję **bazy danych SQL**

6. Kliknij pozycję **Next: Rozwiązania**

1. Kliknij przycisk **zawierają szczegółowe informacje**

    ![Szczegóły problemu](media/sql-database-service-tier-hyperscale/whitelist-request-screen-2.png)

8. Wybierz **typ limitu przydziału bazy danych SQL**: **Inne żądanie limitu przydziału**

9. Wypełnij następujący szablon:

    ![Szczegóły limitu przydziału](media/sql-database-service-tier-hyperscale/whitelist-request-screen-3.png)

    W szablonie należy podać następujące informacje

    > Żądanie utworzenia bazy danych SQL Database platformy Azure na dużą skalę w nowym regionie<br/> Region: [Wypełnij żądanego regionu]  <br/>
    > Jednostka SKU/łącznie rdzeni, łącznie z replik z możliwością odczytu obliczeniowych <br/>
    > Liczba TB szacowany 
    >

10. Wybierz **ważność C**.

11. Wybierz odpowiednią metodę kontaktu i wprowadź szczegóły.

12. Kliknij przycisk **Zapisz** i **kontynuować**

## <a name="known-limitations"></a>Znane ograniczenia
Są to bieżące ograniczenia do warstwy usług na dużą skalę, począwszy od ogólnego udostępnienia.  Pracujemy nad do usunięcia jako wiele z tych ograniczeń, jak to możliwe.

| Problem | Opis |
| :---- | :--------- |
| W okienku Zarządzanie kopiami zapasowymi serwera logicznego nie pokazuje, że będą filtrowane baz danych na dużą skalę z programu SQL server  | W Hiperskali ma oddzielne metodę zarządzania kopiami zapasowymi i jako takie przechowywania długoterminowego i punktu w czasie tworzenia kopii zapasowej ustawień przechowywania nie stosuje się / są unieważniane. W związku z tym baz danych na dużą skalę, nie są wyświetlane w okienku Zarządzanie kopii zapasowej. |
| Przywracanie do określonego momentu | Po migracji bazy danych w warstwie usługi w Hiperskali, przywracanie do punktu w czasie przed migracją nie jest obsługiwane.|
| Przywracanie z innych niż - w Hiperskali DB Hypserscale i na odwrót | Nie można przywrócić bazę danych na dużą skalę w bazie danych — na dużą skalę, nie można przywrócić bazę danych — na dużą skalę w bazie danych na dużą skalę.|
| Jeśli plik bazy danych zwiększa się podczas migracji ze względu na to aktywne obciążenie i przekracza 1 TB na granicy pliku, migracja nie powiedzie się | Środki zaradcze: <br> — Jeśli to możliwe, migracji bazy danych, po nie obciążenia aktualizacji.<br> -Ponów próbę migracji, zakończy się powodzeniem tak długo, jak granica 1 TB nie jest przekroczony podczas migracji.|
| Wystąpienie zarządzane | Wystąpienie usługi Azure SQL Database Managed nie jest obecnie obsługiwane z bazami danych na dużą skalę. |
| Pule elastyczne |  Pule elastyczne nie są obecnie obsługiwane za pomocą bazy danych SQL na dużą skalę.|
| Operacja jednokierunkowa trwa w migracji do usługi w Hiperskali | Po migracji bazy danych na dużą skalę, nie można migrować bezpośrednio do warstwy usług — na dużą skalę. Obecnie jedynym sposobem na migrację bazy danych w Hiperskali do innego niż w Hiperskali jest eksportu/importu za pomocą pliku BACPAC.|
| Migracja bazy danych z obiektów w pamięci | Obiekty w pamięci, należy porzucić i tworzony ponownie jako obiektów innych niż w pamięci, przed przeprowadzeniem migracji bazy danych do warstwy usług na dużą skalę.|
| Dane śledzenia zmian | Nie można użyć Change Tracking danych z bazami danych na dużą skalę. |
| Replikacja geograficzna  | Nie można jeszcze Konfigurowanie replikacji geograficznej dla bazy danych SQL Azure na dużą skalę.  Można przeprowadzić przywracania geograficznego, (Przywracanie bazy danych w innej lokalizacji geograficznej celów odzyskiwania po awarii lub innych) |
| Funkcja TDE/Integracja | Przezroczyste szyfrowanie bazy danych przy użyciu usługi Azure Key Vault (powszechnie znane jako Bring-Your-Own-Key lub BYOK) nie jest jeszcze obsługiwana dla usługi Azure SQL Database o dużej skali, jednak funkcja TDE za pomocą kluczy zarządzanych przez usługę jest w pełni obsługiwane. |
|Funkcje inteligentne bazy danych | 1. Tworzenie indeksu, Drop Index doradcy modele nie są uczone dla baz danych na dużą skalę. <br/>2. Problem ze schematem, DbParameterization - został wyposażony niedawno doradców nie są obsługiwane dla bazy danych na dużą skalę.|



## <a name="next-steps"></a>Kolejne kroki

- Aby — często zadawane pytania na dużą skalę, zobacz [często zadawane pytania dotyczące Hiperskali](sql-database-service-tier-hyperscale-faq.md).
- Aby uzyskać informacji o warstwach usługi, zobacz [warstwy usług](sql-database-service-tiers.md)
- Zobacz [Przegląd zasobów limity na serwerze logicznym](sql-database-resource-limits-logical-server.md) uzyskać informacji dotyczących ograniczeń na poziomach serwera i subskrypcji.
- Zakupu limity modelu pojedynczej bazy danych, zobacz [usługi Azure SQL Database oparty na rdzeniach wirtualnych zakupem modelu limity dla pojedynczej bazy danych](sql-database-vcore-resource-limits-single-databases.md).
- Dla funkcji i listy porównanie, zobacz [typowe funkcje SQL](sql-database-features.md).
