---
title: Usługa Azure SQL Database bez użycia serwera (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: W tym artykule opisano nową warstwę bezserwerowe środowisko obliczeniowe i porównuje go z istniejącej warstwy zainicjowanych zasobów obliczeniowych
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 05/20/2019
ms.openlocfilehash: a9f883a9776f68a7ece471caca5dc1d7af2aec32
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393540"
---
# <a name="sql-database-serverless-preview"></a>Bazy danych SQL Database bez użycia serwera (wersja zapoznawcza)

## <a name="serverless-compute-tier"></a>Warstwa bezserwerowych usług obliczeniowych

Bez użycia serwera bazy danych SQL Database (wersja zapoznawcza) jest warstwa wystąpień obliczeniowych, w tym BOM dla kwotę obliczeń, używane przez pojedynczą bazę danych na podstawie sekundę. Aplikacje niewymagające użycia serwera jest ceny do wydajności zoptymalizowana pod kątem pojedynczych baz danych o wzorcach użycia sporadycznie, nieprzewidywalne, które akceptowalny pewne opóźnienie w rozgrzewania obliczeń po okresów bezczynności użycia.

Bazy danych w warstwie bezserwerowe środowisko obliczeniowe jest sparametryzowany przez zakres obliczeniowe, których można używać i opóźnienie autopause.

![rozliczenia bez użycia serwera](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance"></a>Wydajność

- `MinVcore` i `MaxVcore` są konfigurowalne parametry, które określają zakres możliwości obliczeniowych dostępnych dla bazy danych. Limity pamięci i we/wy są proporcjonalne do określony zakres (rdzeń wirtualny).  
- Opóźnienie autopause jest konfigurowalne parametr, który określa okres czasu, przez który baza danych musi być nieaktywne, zanim automatycznie zostało wstrzymane. Bazy danych zostanie automatycznie wznowione podczas następnego logowania wystąpi.

### <a name="pricing"></a>Cennik

- Łączna opłata bezserwerowa baza danych jest sumą rachunku obliczeń i rachunek za przestrzeń dyskową.
Opłaty za obliczenia zależy od liczby rdzeni wirtualnych, używane i pamięć używana na sekundę.
- Minimalne zasoby obliczeniowe naliczane jest oparty na rdzeniach min i minimalna ilość pamięci.
- Gdy baza danych jest wstrzymany, tylko magazyn jest naliczana opłata.

## <a name="scenarios"></a>Scenariusze

Aplikacje niewymagające użycia serwera jest ceny do wydajności zoptymalizowana pod kątem pojedynczych baz danych o wzorcach użycia sporadycznie, nieprzewidywalne, które akceptowalny pewne opóźnienie w rozgrzewania obliczeń po okresów bezczynności użycia. Z kolei warstwa zainicjowanych zasobów obliczeniowych jest ceny do wydajności zoptymalizowana pod kątem jednego lub puli baz danych z wyższym średniego użycia, który niedopuszczalna wszelkich opóźnień rozgrzewania obliczeń.

### <a name="scenarios-well-suited-for-serverless-compute"></a>Scenariusze nadają się do bezserwerowe środowisko obliczeniowe

- Pojedyncze bazy danych z użycia sporadycznie, nieprzewidywalne wzorce grupową nieaktywności może skorzystać z oszczędności ceny, oparte na rozliczanie na sekundę ilości użytych zasobów obliczeniowych.
- Pojedyncze bazy danych z zapotrzebowanie na zasoby, które trudno jest przewidzieć i klientów, którzy wolą delegować obliczenia rozmiaru w usłudze.
- Pojedyncze bazy danych w warstwie zainicjowanych zasobów obliczeniowych często zmieniać poziomy wydajności.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Scenariusze odpowiednie dla zainicjowanych zasobów obliczeniowych

- Pojedyncze bazy danych z więcej zwykłych, znaczny więcej zasobów obliczeniowych użycia wraz z upływem czasu.
- Bazy danych, które nie tolerują wydajności wad i zalet wynikające z kilku częste odzyskiwanie pamięci przycinanie lub opóźnienie w autoresuming ze stanu wstrzymania.
- Wiele baz danych z tymczasowymi, nieprzewidywalnych wzorców, które mogą być konsolidowane do jednego serwera i użyć pul elastycznych w celu uzyskania lepszej optymalizacji ceny.

## <a name="comparison-with-provisioned-compute-tier"></a>Porównanie z warstwą zainicjowanych zasobów obliczeniowych

W poniższej tabeli przedstawiono różnice między warstwami bezserwerowe środowisko obliczeniowe i zainicjowanych zasobów obliczeniowych:

| | **Bezserwerowe środowisko obliczeniowe** | **Zainicjowanych zasobów obliczeniowych** |
|:---|:---|:---|
|**Typowy scenariusz**| Bazy danych z użyciem sporadycznie, nieprzewidywalne grupową nieaktywne okresów. | Bazy danych lub elastycznymi pulami za pomocą bardziej normalne użycie.|
| **Wysiłek związany z zarządzaniem wydajnością** |Niższy|Wyższe|
|**Obliczenia, skalowanie**|Automatyczne|Ręcznie|
|**Obliczenia czasu odpowiedzi**|Niższe po okresach nieaktywne|Natychmiastowe|
|**Poziom szczegółowości rozliczeń**|Na sekundę|Za godzinę|

## <a name="purchasing-model-and-service-tier"></a>Zakup modelu i warstwy usług

Bez użycia serwera bazy danych SQL jest obecnie obsługiwany tylko w warstwie ogólnego przeznaczenia 5 generacji sprzętu w rdzeniach wirtualnych zakupem modelu.

## <a name="autoscale"></a>Automatyczne skalowanie

### <a name="scaling-responsiveness"></a>Skalowanie czasu odpowiedzi

Ogólnie rzecz biorąc, baz danych są uruchamiane na komputerze o wystarczającej pojemności, zapotrzebowanie na zasoby z produktu bez przeszkód dla dowolnej ilości zasobów obliczeniowych, w granicach `maxVcores` wartość. Od czasu do czasu automatycznie równoważenia obciążenia występuje, gdy komputer jest w stanie spełnić zapotrzebowanie na zasoby w ciągu kilku minut. Baza danych pozostaje w trybie online podczas równoważenia obciążenia, z wyjątkiem przez pewien okres, po zakończeniu operacji gdy połączenia zostaną przerwane.

### <a name="memory-management"></a>Zarządzanie pamięcią

Pamięć dla baz danych bez użycia serwera są odzyskiwane ponad często w przypadku baz danych zainicjowanych zasobów obliczeniowych. To zachowanie, ważne jest, aby kontrolować koszty w bez użycia serwera i może wpłynąć na wydajność.

#### <a name="cache-reclamation"></a>Odzyskiwanie pamięci podręcznej

W przeciwieństwie do baz danych zainicjowanych zasobów obliczeniowych pamięci z pamięci podręcznej SQL jest odzyskać z bez użycia serwera bazy danych, gdy wykorzystanie procesora CPU lub pamięci podręcznej jest za mało.

- Wykorzystanie pamięci podręcznej jest uznawany za niska, gdy łączny rozmiar najczęściej ostatnio używane w pamięci podręcznej wpisy spadnie poniżej wartości progowej przez czas.
- Po wyzwoleniu odzyskiwanie pamięci podręcznej, rozmiar pamięci podręcznej docelowej zmniejsza się stopniowo ułamek poprzedniego rozmiaru i odzyskiwanie tylko kontynuuje, jeśli użycie pozostaje niski.
- W przypadku odzyskiwanie pamięci podręcznej zasada służąca do wybierania wpisy w pamięci podręcznej do wykluczenia, to te same zasady wyboru, jak w przypadku baz danych zainicjowanych zasobów obliczeniowych po wysokie wykorzystanie pamięci.
- Rozmiar pamięci podręcznej nigdy nie zostanie zmniejszony poniżej minimalną ilość pamięci, zgodnie z definicją minimalne rdzeni wirtualnych, które można skonfigurować.

Zarówno bez użycia serwera, jak i elastycznie obliczeń baz danych, pamięci podręcznej, który może zostać wykluczony wpisów, jeśli całą dostępną pamięć jest używany.

#### <a name="cache-hydration"></a>Wypełniania pamięci podręcznej

Pamięć podręczna SQL zwiększa rozmiar pobieranych z dysków w taki sam sposób, jak i z tego samego szybkością, jak w przypadku aprowizowanych baz danych. Gdy baza danych jest zajęta, pamięci podręcznej może wzrosnąć nieograniczonego do limitu pamięci max.

## <a name="autopause-and-autoresume"></a>Autopause i autoresume

### <a name="autopause"></a>Autopause

Autopause jest wyzwalany, jeśli wszystkie następujące warunki mają wartość true, czas trwania zwłoki autopause:

- Liczba sesji = 0
- Procesor CPU = 0 (na użytkownika obciążenia w puli użytkownika)

Opcja znajduje się wyłączyć autopause, jeśli to konieczne.

### <a name="autoresume"></a>Autoresume

Autoresume jest wyzwalana w przypadku spełnienia dowolnego z następujących warunków w dowolnym momencie:

|Cecha|Autoresume wyzwalacza|
|---|---|
|Uwierzytelnianie i autoryzacja|Login|
|Wykrywanie zagrożeń|Włączanie/wyłączanie ustawień wykrywania zagrożeń na poziomie bazy danych lub serwera<br>Modyfikowanie ustawień wykrywania zagrożeń na poziomie bazy danych lub serwera|
|Odnajdowanie i klasyfikacja danych|Dodawanie, modyfikowanie lub wyświetlanie etykiet czułości|
|Inspekcja|Wyświetlanie rekordów inspekcji.<br>Aktualizowanie lub wyświetlanie zasad inspekcji|
|Maskowanie danych|Dodawanie, modyfikowanie lub wyświetlanie reguł maskowania danych|
|Transparent Data Encryption|Wyświetl stan lub stan technologii transparent data encryption|
|Magazyn danych zapytań (wydajność)|Modyfikowanie lub wyświetlanie ustawień magazynu zapytań; Usługa dostrajania automatycznego|
|Automatycznego dostrajania|Stosowanie i weryfikacja zalecenia dotyczące automatycznego dostrajania, takich jak automatycznego indeksowania|
|Kopiowanie bazy danych|Tworzenie bazy danych jako kopia<br>Eksport do pliku BACPAC|
|Synchronizacja danych SQL|Synchronizacja między bazami danych Centrum i elementów członkowskich, które działać zgodnie z konfigurowalnym harmonogramem albo są wykonywane ręcznie|
|Modyfikowanie niektórych metadanych bazy danych|Dodawanie nowej bazy danych tagów<br>Zmiana max rdzeni wirtualnych, rdzenie min autopause opóźnienia|
|SQL Server Management Studio (SSMS)|Za pomocą programu SSMS wersja 18 lub i otworzyć nowe okno zapytania dla dowolnej bazy danych na serwerze zostanie wznowiona wszystkie wstrzymane automatycznie database w tym samym serwerze. To zachowanie nie występuje, jeśli za pomocą programu SSMS wersji 17.9.1, za pomocą funkcji IntelliSense — wyłączony.|

### <a name="connectivity"></a>Łączność

Jeśli bezserwerowa baza danych jest wstrzymany, to pierwszy identyfikator logowania będzie wznowić bazy danych i zwrócić komunikat o błędzie informujący, że baza danych jest niedostępna z kodem błędu 40613. Po wznowieniu bazy danych logowania musi zostać powtórzone, można ustanowić łączności. Klienty baz danych przy użyciu logikę ponawiania próby połączenia nie powinni być modyfikowane.

### <a name="latency"></a>Opóźnienie

Czas oczekiwania na autopause lub autoresume bezserwerowa baza danych jest zazwyczaj rzędu kilku 1 minutę.

### <a name="feature-support"></a>Obsługa funkcji

Następujące funkcje, które nie obsługują autopausing i autoresuming. Oznacza to jeśli używane są dowolne z następujących funkcji, następnie baza danych pozostaje w trybie online niezależnie od tego czasu trwania aktywności bazy danych:

- Replikacja geograficzna (aktywna replikacja geograficzna i automatycznie grupy trybu failover)
- Długoterminowe przechowywanie kopii zapasowych (LTR)
- Synchronizacja bazy danych używanej w synchronizacji danych programu SQL.


## <a name="onboarding-into-serverless-compute-tier"></a>Dołączanie do warstwy bezserwerowe środowisko obliczeniowe

Tworzenie nowej bazy danych lub przenoszenie że istniejącą bazę danych w warstwie bezserwerowe środowisko obliczeniowe zgodna z tym samym wzorcem używanym podczas tworzenia nowej bazy danych w aprowizowane warstwa wystąpień obliczeniowych i obejmuje następujące dwa kroki:

1. Określona nazwa celu usługi. Cel usługi określa warstwę usługi, generacja sprzętu i maksymalna rdzeni wirtualnych. W poniższej tabeli przedstawiono opcje celu usługi:

   |Nazwa celu usługi|Warstwa usług|Generacja sprzętu|Maksymalna liczba rdzeni wirtualnych|
   |---|---|---|---|
   |GP_S_Gen5_1|Ogólne zastosowanie|5. generacji|1|
   |GP_S_Gen5_2|Ogólne zastosowanie|5. generacji|2|
   |GP_S_Gen5_4|Ogólne zastosowanie|5. generacji|4|

2. Opcjonalnie można określić minimalną rdzeni wirtualnych i autopause opóźnienia, aby zmienić wartości domyślne. W poniższej tabeli przedstawiono dostępne wartości dla tych parametrów.

   |Parametr|Wartość opcji|Wartość domyślna|
   |---|---|---|---|
   |Minimalna liczba rdzeni wirtualnych|Dowolne {0,5, 1, 2, 4} nie przekracza maksymalny rdzeni wirtualnych|0,5 rdzeni wirtualnych|
   |Autopause opóźnienia|Minimalna: 360 minut (6 godzin)<br>Maks.: do 10 080 minut (7 dni)<br>Zwiększa: 60 minut<br>Wyłącz autopause: -1|360 minut|

> [!NOTE]
> Przy użyciu języka T-SQL, aby przenieść istniejącą bazę danych do bez użycia serwera lub zmienić jego rozmiar obliczeń nie jest obecnie obsługiwane, ale może odbywać się za pośrednictwem witryny Azure portal lub programu PowerShell.

### <a name="create-new-serverless-database-using-azure-portal"></a>Utwórz nową bazę danych bez użycia serwera za pomocą witryny Azure portal

Zobacz [Szybki start: Tworzenie pojedynczej bazy danych w usłudze Azure SQL Database przy użyciu witryny Azure portal](sql-database-single-database-get-started.md).

### <a name="create-new-serverless-database-using-powershell"></a>Utwórz nową bazę danych bez użycia serwera za pomocą programu PowerShell

Poniższy przykład tworzy nową bazę danych w warstwie bezserwerowe środowisko obliczeniowe, zdefiniowane przez cel usługi o nazwie GP_S_Gen5_4 przy użyciu wartości domyślnych dla opóźnienia rdzeni wirtualnych i autopause min.

Aplikacje niewymagające użycia serwera wymaga nowszej wersji programu PowerShell nie jest obecnie dostępna w galerii, więc uruchomienie `Update-Module Az.Sql` można pobrać najnowsze polecenia cmdlet bez użycia serwera są włączone.

```powershell
New-AzSqlDatabase `
  -ResourceGroupName $resourceGroupName `
  -ServerName $serverName `
  -DatabaseName $databaseName `
  -ComputeModel Serverless `
  -Edition GeneralPurpose `
  -ComputeGeneration Gen5 `
  -MinVcore 0.5 `
  -MaxVcore 2 `
  -AutoPauseDelay 720
```

### <a name="move-provisioned-compute-database-into-serverless-compute-tier"></a>Przenoszenie bazy danych zainicjowanych zasobów obliczeniowych w warstwie bezserwerowe środowisko obliczeniowe

Poniższy przykład przenosi istniejących pojedynczej bazy danych z warstwy zainicjowanych zasobów obliczeniowych w warstwie bezserwerowe środowisko obliczeniowe. Ten przykład jawnie określa rdzeni wirtualnych min, max rdzeni wirtualnych i autopause opóźnienia.

```powershell
Set-AzSqlDatabase
  -ResourceGroupName $resourceGroupName `
  -ServerName $serverName `
  -DatabaseName $databaseName `
  -Edition GeneralPurpose `
  -ComputeModel Serverless `
  -ComputeGeneration Gen5 `
  -MinVcore 1 `
  -MaxVcore 4 `
  -AutoPauseDelay 1440
```

### <a name="move-serverless-database-into-provisioned-compute-tier"></a>Przeniesienie bazy danych bez użycia serwera w warstwie zainicjowanych zasobów obliczeniowych

Bezserwerowa baza danych mogą zostać przeniesione w warstwie zainicjowanych zasobów obliczeniowych, w taki sam sposób jak przeniesienie bazy danych zainicjowanych zasobów obliczeniowych w warstwie bezserwerowe środowisko obliczeniowe.

## <a name="modifying-serverless-configuration"></a>Modyfikowanie konfiguracji bez użycia serwera

### <a name="maximum-vcores"></a>Maksymalna liczba rdzeni wirtualnych

Modyfikowanie maksymalna rdzeni wirtualnych odbywa się za pomocą [AzSqlDatabase zestaw](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) polecenia przy użyciu programu PowerShell `MaxVcore` argumentu.

### <a name="minimum-vcores"></a>Minimalna liczba rdzeni wirtualnych

Modyfikowanie minimalne rdzeni wirtualnych odbywa się za pomocą [AzSqlDatabase zestaw](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) polecenia przy użyciu programu PowerShell `MinVcore` argumentu.

### <a name="autopause-delay"></a>Autopause opóźnienia

Modyfikowanie opóźnienie autopause odbywa się za pomocą [AzSqlDatabase zestaw](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) polecenia przy użyciu programu PowerShell `AutoPauseDelay` argumentu.

## <a name="monitoring"></a>Monitorowanie

### <a name="resources-used-and-billed"></a>Zasoby używane i rozliczane

Zasoby bez użycia serwera bazy danych są hermetyzowane w następujących elementach:

#### <a name="app-package"></a>Pakiet aplikacji

Pakiet aplikacji jest zewnętrzne większość zasobów zarządzania granic dla bazy danych, niezależnie od tego, czy baza danych znajduje się w warstwie obliczeniowych bez użycia serwera lub udostępnione. Pakiet aplikacji zawiera wystąpienie programu SQL, i zewnętrzne usługi tego zakresu razem wszystkie zasoby użytkownika i systemowe używane przez bazę danych w bazie danych SQL. Przykładami usług zewnętrznych języków R i wyszukiwanie pełnotekstowe. Wystąpienie programu SQL zazwyczaj zajmuje większą część ekranu ogólnego użycia zasobów przez pakiet aplikacji.

#### <a name="user-resource-pool"></a>Pula zasobów użytkownika

Pula zasobów użytkownika jest wewnętrzny większość granic zarządzania zasobów dla bazy danych, niezależnie od tego, czy baza danych znajduje się w warstwie obliczeniowych bez użycia serwera lub udostępnione. Użytkownik zasobów zakresy puli procesora CPU i we/wy dla obciążenia użytkownikami, generowane przez kwerendy DDL, takie jak tworzenie i ALTER i DML zapytania, takie jak wybieranie, wstawianie, AKTUALIZOWANIE i usuwanie. Te zapytania zazwyczaj reprezentują najbardziej znacznej wykorzystanie w ramach pakietu aplikacji.

### <a name="metrics"></a>Metryki

|Jednostka|Metryka|Opis|Jednostki|
|---|---|---|---|
|Pakiet aplikacji|app_cpu_percent|Procent rdzeni wirtualnych używanych przez aplikację względem rdzeni maksymalny dozwolony dla aplikacji.|Wartość procentowa|
|Pakiet aplikacji|app_cpu_billed|Ilość obliczeń naliczane dla aplikacji w tym okresie raportowania. Kwoty zapłacone w tym okresie jest wynikiem tej metryki i cenę jednostkową (rdzeń wirtualny). <br><br>Wartości tej metryki są określane przez agregowanie wraz z upływem czasu używana maksymalnie procesora CPU i pamięć używana w każdej sekundzie. Jeśli kwota, używany jest mniejsza niż minimalna ilość zapewniona według stawki ustalonej przez minimalny rdzeni wirtualnych i minimalna ilość pamięci, minimalna ilość aprowizowane jest rozliczane. Aby można było porównać procesora CPU z pamięci na potrzeby rozliczeń, pamięć jest znormalizować na jednostki rdzeni wirtualnych przez ponowne skalowanie ilość pamięci w GB, 3 GB na rdzeń wirtualny.|Rdzeń wirtualny w sekundach|
|Pakiet aplikacji|app_memory_percent|Procent pamięci używane przez aplikację względem pamięci maksymalny dozwolony dla aplikacji.|Wartość procentowa|
|Puli użytkownika|cpu_percent|Procent rdzeni wirtualnych, używane przez obciążenie użytkownikami względem rdzeni maksymalny dozwolony dla obciążenia użytkownikami.|Wartość procentowa|
|Puli użytkownika|data_IO_percent|Wartość procentowa danych używane przez obciążenie użytkownikami względem danych max operacje We/Wy na SEKUNDĘ jest dozwolone dla obciążenia użytkownikami.|Wartość procentowa|
|Puli użytkownika|log_IO_percent|Procent dziennika MB/s używane przez obciążenie użytkownikami względem dzienników maksymalna w MB/s dozwolone dla obciążenia użytkownikami.|Wartość procentowa|
|Puli użytkownika|workers_percent|Procent procesów roboczych używanych przez obciążenie użytkownikami względem pracowników maksymalny dozwolony dla obciążenia użytkownikami.|Wartość procentowa|
|Puli użytkownika|sessions_percent|Procent sesji używane przez obciążenie użytkownikami względem sesji dozwolone w przypadku obciążenia użytkownika.|Wartość procentowa|
____

> [!NOTE]
> Metryki w witrynie Azure portal są dostępne w okienku bazy danych dla pojedynczej bazy danych w ramach **monitorowanie**.

### <a name="pause-and-resume-status"></a>Wstrzymywanie i wznawianie stanu

W witrynie Azure portal stan bazy danych jest wyświetlany w okienku przeglądu serwera, który zawiera listę baz danych, które zawiera. Stan bazy danych jest także wyświetlany w okienku przeglądu dla bazy danych.

Za pomocą następującego polecenia programu PowerShell do wykonywania zapytań wstrzymanie i wznowienie stanu bazy danych:

```powershell
Get-AzSqlDatabase `
  -ResourceGroupName $resourcegroupname `
  -ServerName $servername `
  -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

## <a name="resource-limits"></a>Limity zasobów

Limity zasobów, zobacz [warstwa wystąpień obliczeniowych bez użycia serwera](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier)

## <a name="billing"></a>Rozliczenia

Ilość zasobów obliczeniowych, naliczana jest maksymalnie użycie Procesora i pamięci używanej co sekundę. Jeśli używany moc procesora CPU i używanej pamięci jest mniejsza niż minimalna ilość aprowizowaną dla każdego, elastycznie kwoty jest naliczana. Aby można było porównać procesora CPU z pamięci na potrzeby rozliczeń, pamięć jest znormalizować na jednostki rdzeni wirtualnych przez ponowne skalowanie ilość pamięci w GB, 3 GB na rdzeń wirtualny.

- **Zasób rozliczane**: Procesor CPU i pamięć
- **Kwota rozliczane ($)** : cena jednostkowa (rdzeń wirtualny) * max (min rdzeni wirtualnych, rdzenie wirtualne używane, minimalna ilość pamięci GB * pamięci 1/3 GB używane * 1/3) 
- **Częstotliwość rozliczeń**: Na sekundę

Cena jednostkowa — rdzeń wirtualny w koszt na sekundę (rdzeń wirtualny). Zapoznaj się [usługi Azure SQL Database, cennik](https://azure.microsoft.com/pricing/details/sql-database/single/) dla określonej jednostki ceny w danym regionie.

Ilość zasobów obliczeniowych, naliczana jest uwidaczniany przez następujące metryki:

- **Metryka**: app_cpu_billed (rdzeń wirtualny w sekundach)
- **Definicja**: maksymalny (min rdzeni wirtualnych, rdzenie wirtualne używane, minimalna ilość pamięci GB * pamięci 1/3 GB używane * 1/3)
- **Częstotliwość raportowania**: Za minutę

Ta ilość jest obliczane co sekundę i zagregowane ponad 1 minutę.

Należy wziąć pod uwagę bez użycia serwera bazy danych skonfigurowano wartość elementów vCore 1 min i 4 rdzenie wirtualne max.  Odpowiada to około 3 GB, minimalna ilość pamięci i pamięci max 12 GB.  Załóżmy, że ustawiono opóźnienie automatycznego wstrzymywania do 6 godzin, a obciążenie bazy danych jest 2 godzinach pierwszego okresu 24-godzinnego i w przeciwnym razie nieaktywnych.    

W takim przypadku baza danych jest naliczana za zasoby obliczeniowe i Magazyn podczas pierwszego 8 godzin.  Mimo, że baza danych znajduje się nieaktywne uruchamianie po godzinie, nadal jest rozliczana za obliczenia w kolejnych 6 godzin, oparte na obliczenia minimalne zainicjowano obsługę administracyjną, gdy baza danych jest w trybie online.  Tylko magazyn jest rozliczana w pozostałej części 24-godzinnego okresu, gdy baza danych jest wstrzymany.

Mówiąc ściślej na rachunku obliczeniowych, w tym przykładzie jest obliczana w następujący sposób:

|Przedział czasu|rdzenie wirtualne używane w każdej sekundzie|Używane w każdej sekundzie GB|Obliczenia wymiarów rozliczane|rozliczane przedziałach czasu w sekundach (rdzeń wirtualny)|
|---|---|---|---|---|
|0:00-1:00|4|9|rdzenie wirtualne używane|4 rdzenie wirtualne * 3600 sekund = 14400 (rdzeń wirtualny) sekund|
|1:00-2:00|1|12|Pamięć użyta|12 Gb * 1/3 * 3600 sekund = 14400 sekund (rdzeń wirtualny)|
|2:00-8:00|0|0|Minimalna ilość pamięci aprowizacji|3 Gb * 1/3 * 21600 sekundy = 21600 sekund (rdzeń wirtualny)|
|8:00-24:00|0|0|Nie obliczeniowe naliczane podczas wstrzymania|0 — rdzeń wirtualny w sekundach|
|Łączna liczba (rdzeń wirtualny) sekund naliczane przez 24 godziny||||50400 — rdzeń wirtualny w sekundach|

Załóżmy, że cena jednostki obliczeniowej jest $0.000073/vCore/second.  Zasoby obliczeniowe naliczane w tym okresie 24-godzinnym jest wynikiem sekund ceny i rdzeń wirtualny jednostki obliczeniowe naliczane: $0.000073/vCore/second * 50400 sekund — rdzeń wirtualny = 3.68 $

## <a name="available-regions"></a>Dostępne regiony

Warstwa bezserwerowe środowisko obliczeniowe jest dostępna we wszystkich regionach z wyjątkiem następujących regionów: Australia Środkowa, Chiny wschodnie, Chiny Północne, Francja Południowa, Niemcy środkowe, Niemcy północno-wschodnie, Indie Zachodnie, Korea Południowa, Zachodnia RPA, Północne Zjednoczone Królestwo, południowe Zjednoczone Królestwo, zachodnie Zjednoczone Królestwo i zachodnio środkowe stany USA.

## <a name="next-steps"></a>Kolejne kroki

- Aby rozpocząć pracę, zobacz [Szybki Start: Tworzenie pojedynczej bazy danych w usłudze Azure SQL Database przy użyciu witryny Azure portal](sql-database-single-database-get-started.md).
- Limity zasobów, zobacz [aplikacje niewymagające użycia serwera obliczenia limity zasobów warstwy](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier).
