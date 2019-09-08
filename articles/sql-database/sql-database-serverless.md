---
title: Azure SQL Database bezserwerowe (wersja zapoznawcza) | Microsoft Docs
description: W tym artykule opisano nową warstwę obliczeniową bez serwera i porównuje ją z istniejącą zainicjowaną warstwą obliczeniową
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: moslake
ms.author: moslake
ms.reviewer: sstein, carlrab
ms.date: 09/06/2019
ms.openlocfilehash: 738d6738469960c8b21809b9320c4d034613c4e3
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802381"
---
# <a name="azure-sql-database-serverless-preview"></a>Azure SQL Database bezserwerowe (wersja zapoznawcza)

Azure SQL Database bezserwerowe (wersja zapoznawcza) to warstwa obliczeniowa dla pojedynczych baz danych, która automatycznie skaluje obliczenia na podstawie zapotrzebowania na obciążenia i rachunków dla ilości użytych obliczeń na sekundę. Warstwa obliczeniowa bezserwerowa również automatycznie wstrzymuje bazy danych w trakcie okresów nieaktywnych, gdy są naliczane opłaty za magazyn i automatycznie wznawiają bazy danych po powrocie działania.

## <a name="serverless-compute-tier"></a>Warstwa bezserwerowych usług obliczeniowych

Warstwa obliczeń bezserwerowych dla pojedynczej bazy danych jest sparametryzowane według zakresu obliczeń skalowania automatycznego i opóźnienia automatycznego wstrzymania.  Konfiguracja tych parametrów służy do kształtowania wydajności bazy danych i kosztu obliczeniowego.

![rozliczenia bezserwerowe](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance-configuration"></a>Konfiguracja wydajności

- **Minimalna rdzeni wirtualnych** i **Maksymalna rdzeni wirtualnych** to konfigurowalne parametry, które definiują zakres wydajności obliczeniowej dostępny dla bazy danych. Limity pamięci i operacji we/wy są proporcjonalne do określonego zakresu rdzeń wirtualny.  
- **Opóźnienie AutoPause** to konfigurowalny parametr, który określa okres czasu, przez który baza danych musi być nieaktywna, zanim zostanie automatycznie wstrzymana. Baza danych zostanie automatycznie wznowiona po wystąpieniu następnego logowania lub innego działania.  Alternatywnie można wyłączyć autowstrzymywanie.

### <a name="cost"></a>Koszt

- Koszt bazy danych bezserwerowej to podsumowanie kosztów i kosztów magazynu obliczeniowego.
- Gdy użycie obliczeniowe ma wartość z przedziału minimalnego i maksymalnego skonfigurowanego limitu, koszt obliczeń jest oparty na rdzeń wirtualny i używanej pamięci.
- Gdy użycie obliczeniowe zostanie skonfigurowane poniżej minimalnych limitów, koszt obliczeń jest uzależniony od minimalnej rdzeni wirtualnych i minimalnej pamięci skonfigurowanej.
- Gdy baza danych jest wstrzymana, koszt obliczeń wynosi zero i są naliczane tylko koszty związane z magazynem.
- Koszt magazynu jest ustalany w taki sam sposób, jak w przypadku alokowanej warstwy obliczeniowej.

Aby uzyskać więcej informacji, zobacz [rozliczenia](sql-database-serverless.md#billing).

## <a name="scenarios"></a>Scenariusze

Bezserwerowa wydajność jest zoptymalizowana pod kątem wydajności dla pojedynczych baz danych z sporadycznymi, nieprzewidywalnymi wzorcami użycia, które mogą zapewnić pewne opóźnienia w rozgrzaniu po okresach użytkowania. W przeciwieństwie do alokowanej warstwy obliczeniowej jest cena, która została zoptymalizowana pod kątem pojedynczej bazy danych lub wielu baz danych w pulach elastycznych z wyższym średnim zużyciem, które nie może zapewnić żadnego opóźnienia w rozgrzewaniu wydajności.

### <a name="scenarios-well-suited-for-serverless-compute"></a>Scenariusze odpowiednie dla obliczeń bezserwerowych

- Pojedyncze bazy danych z sporadycznymi, nieprzewidywalnymi wzorcami użycia przeplatanymi z okresami braku aktywności i niższym średnim wykorzystaniem obliczeń w czasie.
- Pojedyncze bazy danych w warstwie obliczeniowej, które są często zmieniane, i Klienci, którzy wolą do delegowania ponownego skalowania obliczeń do usługi.
- Nowe pojedyncze bazy danych bez historii użycia, w których rozmiary obliczeń są trudne lub niemożliwe do oszacowania przed wdrożeniem w SQL Database.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Scenariusze dla zainicjowanych obliczeń

- Pojedyncze bazy danych z bardziej regularnymi, przewidywalnymi wzorcami użycia i wyższym średnim wykorzystaniem obliczeń w czasie.
- Bazy danych, które nie mogą tolerować zwiększania wydajności, wynikających z większej częstej przycinania pamięci lub opóźnień w przypadku autowznawiania ze stanu wstrzymania.
- Wiele baz danych z sporadycznymi, nieprzewidywalnymi wzorcami użycia, które można skonsolidować do pul elastycznych w celu zapewnienia lepszej optymalizacji wydajności.

## <a name="comparison-with-provisioned-compute-tier"></a>Porównanie z zainicjowaną warstwą obliczeniową

W poniższej tabeli zestawiono różnice między warstwą obliczeniową bezserwerową i zainicjowaną warstwą obliczeniową:

| | **Obliczenia bezserwerowe** | **Zainicjowane obliczenie** |
|:---|:---|:---|
|**Wzorzec użycia bazy danych**| Sporadyczne, nieprzewidywalne użycie z niższym średnim wykorzystaniem obliczeń w czasie. |  Bardziej regularne wzorce użycia z wyższym średnim wykorzystaniem obliczeń w czasie lub wielu bazach danych korzystających z pul elastycznych.|
| **Nakład pracy zarządzania wydajnością** |Dołu|Wyższa|
|**Skalowanie obliczeniowe**|Automatyczne|Ręczne|
|**Czas odpowiedzi obliczeń**|Poniżej nieaktywnych okresów|Natychmiastowe|
|**Stopień szczegółowości rozliczeń**|Na sekundę|Za godzinę|

## <a name="purchasing-model-and-service-tier"></a>Model zakupów i warstwa usług

SQL Database bezserwerowe jest obecnie obsługiwane tylko w warstwie Ogólnego przeznaczenia na sprzęcie generacji 5 w modelu zakupu rdzeń wirtualny.

## <a name="autoscaling"></a>Skalowanie automatyczne

### <a name="scaling-responsiveness"></a>Skalowanie czasu odpowiedzi

Ogólnie rzecz biorąc, bezserwerowe bazy danych są uruchamiane na komputerze z wystarczającą pojemnością w celu zaspokojenia zapotrzebowania na zasoby bez przeszkód dla każdej ilości obliczeń wymaganych w ramach limitów ustawionych przez maksymalną wartość rdzeni wirtualnych. Czasami Równoważenie obciążenia odbywa się automatycznie, jeśli komputer nie może spełnić wymagań dotyczących zasobów w ciągu kilku minut. Na przykład jeśli zapotrzebowanie na zasoby wynosi 4 rdzeni wirtualnych, ale dostępne są tylko 2 rdzeni wirtualnych, obciążenie równoważenia obciążenia może potrwać kilka minut. Baza danych pozostanie w trybie online podczas równoważenia obciążenia, z wyjątkiem krótkiego okresu na końcu operacji, gdy połączenia są porzucane.

### <a name="memory-management"></a>Zarządzanie pamięcią

Pamięć dla baz danych bezserwerowych jest odzyskiwana częściej niż dla zainicjowanych baz danych obliczeniowych. Takie zachowanie ma na celu kontrolę kosztów w bezserwerowym i może mieć wpływ na wydajność.

#### <a name="cache-reclamation"></a>Odzyskiwanie pamięci podręcznej

W przeciwieństwie do baz danych obliczeniowych, pamięć z pamięci podręcznej SQL jest odzyskiwana z bazy danych bezserwerowej, gdy użycie procesora lub pamięci podręcznej jest niskie.

- Użycie pamięci podręcznej jest uznawane za niskie, gdy łączny rozmiar ostatnio używanych wpisów w pamięci podręcznej spadnie poniżej wartości progowej przez pewien czas.
- Gdy odzyskiwanie pamięci podręcznej jest wyzwalane, rozmiar docelowej pamięci podręcznej jest zmniejszany przyrostowo do ułamka poprzedniego rozmiaru i odzyskiwanie odbywa się tylko w przypadku, gdy użycie będzie niskie.
- Gdy następuje odzyskiwanie pamięci podręcznej, zasady wyboru wpisów pamięci podręcznej do wykluczenia są takie same jak w przypadku zainicjowanych baz danych obliczeniowych, gdy wykorzystanie pamięci jest wysokie.
- Rozmiar pamięci podręcznej nigdy nie jest mniejszy niż minimalny limit pamięci określony przez minimalną rdzeni wirtualnych, który można skonfigurować.

W przypadku baz danych obliczeniowych bezserwerowych i inicjowanych, wpisy pamięci podręcznej mogą zostać wykluczone, jeśli jest używana cała dostępna pamięć.

#### <a name="cache-hydration"></a>Odwodnienie pamięci podręcznej

Pamięć podręczna SQL powiększa się jak dane są pobierane z dysku w taki sam sposób i z taką samą szybkością jak dla zainicjowanych baz danych. Gdy baza danych jest zajęta, pamięć podręczna może być zwiększana o nieograniczony limit pamięci.

## <a name="autopausing-and-autoresuming"></a>Autowstrzymywanie i autowznawianie

### <a name="autopausing"></a>Trwa autowstrzymywanie

Autowstrzymywanie jest wyzwalane, jeśli wszystkie poniższe warunki są spełnione przez czas opóźnienia autowstrzymania:

- Liczba sesji = 0
- Procesor CPU = 0 dla obciążenia użytkownika działającego w puli użytkowników

Opcja umożliwia wyłączenie autowstrzymywanie w razie potrzeby.

Następujące funkcje nie obsługują autowstrzymywanie.  Oznacza to, że jeśli są używane jakiekolwiek z następujących funkcji, baza danych pozostanie w trybie online, niezależnie od czasu trwania nieaktywności bazy danych:

- Replikacja geograficzna (aktywna replikacja geograficzna i grupy autotrybu failover).
- Długoterminowe przechowywanie kopii zapasowych (LTR).
- Baza danych synchronizacji używana w usłudze SQL Data Sync.  W przeciwieństwie do baz danych synchronizacji, bazy danych Hub i elementów członkowskich obsługują autowstrzymywanie.
- Baza danych zadań używana w zadaniach elastycznych.

Autowstrzymywanie jest tymczasowo uniemożliwiane podczas wdrażania niektórych aktualizacji usługi, które wymagają, aby baza danych była w trybie online.  W takich przypadkach autowstrzymywanie zostanie ponownie dozwolone po zakończeniu aktualizacji usługi.

### <a name="autoresuming"></a>Trwa autowznawianie

Autowznawianie jest wyzwalane, jeśli w dowolnym momencie spełniony jest którykolwiek z następujących warunków:

|Cecha|Wyzwalacz autowznawiania|
|---|---|
|Uwierzytelnianie i autoryzacja|Zaloguj|
|Wykrywanie zagrożeń|Włączanie/wyłączanie ustawień wykrywania zagrożeń na poziomie bazy danych lub serwera.<br>Modyfikowanie ustawień wykrywania zagrożeń na poziomie bazy danych lub serwera.|
|Odnajdowanie i klasyfikacja danych|Dodawanie, modyfikowanie, usuwanie lub wyświetlanie etykiet czułości|
|Inspekcja|Wyświetlanie rekordów inspekcji.<br>Aktualizowanie lub przeglądanie zasad inspekcji.|
|Maskowanie danych|Dodawanie, modyfikowanie, usuwanie lub wyświetlanie reguł maskowania danych|
|Transparent Data Encryption|Wyświetlanie stanu lub stanu przezroczystego szyfrowania danych|
|Zapytanie (wydajność) — magazyn danych|Modyfikowanie lub wyświetlanie ustawień magazynu zapytań; Dostrajanie automatyczne|
|Autodostrajanie|Aplikacja i weryfikacja zalecenia autodostrajania, takie jak indeksowanie automatycznego|
|Kopiowanie bazy danych|Utwórz bazę danych jako kopię.<br>Eksportuj do pliku BACPAC.|
|Synchronizacja danych SQL|Synchronizacja między bazami danych centrum i elementami członkowskimi, które są uruchamiane w konfigurowalnym harmonogramie lub są wykonywane ręcznie|
|Modyfikowanie niektórych metadanych bazy danych|Dodawanie nowych tagów bazy danych.<br>Zmiana maksymalnego opóźnienia rdzeni wirtualnych, minimum rdzeni wirtualnych lub AutoPause.|
|SQL Server Management Studio (SSMS)|Użycie programu SSMS w wersji wcześniejszej niż 18,1 i otwarcie nowego okna zapytania dla każdej bazy danych na serwerze spowoduje wznowienie wszystkich autowstrzymanych baz danych na tym samym serwerze. Takie zachowanie nie występuje, jeśli używany jest program SSMS w wersji 18,1 lub nowszej.|

Autowznawianie jest również wyzwalane podczas wdrażania niektórych aktualizacji usługi, które wymagają, aby baza danych była w trybie online.

### <a name="connectivity"></a>Łączność

Jeśli bezserwerowa baza danych jest wstrzymana, pierwsze logowanie spowoduje wznowienie działania bazy danych i zwrócenie błędu informującego, że baza danych jest niedostępna z kodem błędu 40613. Po wznowieniu bazy danych logowanie musi zostać ponowione w celu nawiązania połączenia. Klientów bazy danych z logiką ponawiania połączenia nie należy modyfikować.

### <a name="latency"></a>Opóźnienie

Opóźnienie autowznawiania i autowstrzymanie bazy danych bezserwerowych jest zazwyczaj kolejnością od 1 minuty do autowznawiania i 1-10 minut do autowstrzymywania.

## <a name="onboarding-into-serverless-compute-tier"></a>Dołączanie do warstwy obliczeń bezserwerowych

Tworzenie nowej bazy danych lub przeniesienie istniejącej bazy danych do warstwy obliczeniowej bezserwerowej jest zgodne z tym samym wzorcem, co Tworzenie nowej bazy danych w warstwie obliczeniowej zainicjowanej i obejmuje następujące dwa kroki.

1. Określ nazwę celu usługi. Cel usługi określa warstwę usług, generowanie sprzętu i maksymalną rdzeni wirtualnych. W poniższej tabeli przedstawiono opcje celu usługi:

   |Nazwa celu usługi|Warstwa usług|Generowanie sprzętu|Maksymalna rdzeni wirtualnych|
   |---|---|---|---|
   |GP_S_Gen5_1|Ogólne zastosowanie|5\. generacji|1|
   |GP_S_Gen5_2|Ogólne zastosowanie|5\. generacji|2|
   |GP_S_Gen5_4|Ogólne zastosowanie|5\. generacji|4|

2. Opcjonalnie można określić opóźnienie rdzeni wirtualnych i pauzę, aby zmienić wartości domyślne. W poniższej tabeli przedstawiono dostępne wartości tych parametrów.

   |Parametr|Opcje wartości|Wartość domyślna|
   |---|---|---|---|
   |Min rdzeni wirtualnych|Dowolny z {0,5, 1, 2, 4} nie przekracza maksymalnej rdzeni wirtualnych|0,5 rdzeni wirtualnych|
   |Opóźnienie AutoPause|Minimalny 60 minut (1 godzina)<br>Długość 10080 minut (7 dni)<br>Zwiększa 60 minut<br>Wyłącz autowstrzymywanie:-1|60 minut|

> [!NOTE]
> Używanie języka T-SQL do przenoszenia istniejącej bazy danych do bezserwerowej lub zmiany jej rozmiaru nie jest obecnie obsługiwane, ale można ją wykonać za pośrednictwem Azure Portal lub PowerShell.

### <a name="create-new-database-in-serverless-compute-tier"></a>Utwórz nową bazę danych w warstwie obliczeniowej bezserwerowej 

#### <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal

Zobacz [Szybki start: Utwórz pojedynczą bazę danych w Azure SQL Database przy użyciu](sql-database-single-database-get-started.md)Azure Portal.

#### <a name="use-powershell"></a>Korzystanie z programu PowerShell

Poniższy przykład tworzy nową bazę danych w warstwie obliczeniowej bezserwerowej.  W tym przykładzie jawnie określono opóźnienie rdzeni wirtualnych, maks rdzeni wirtualnych i AutoPause.

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
  -AutoPauseDelayInMinutes 720
```

### <a name="move-database-from-provisioned-compute-tier-into-serverless-compute-tier"></a>Przenoszenie bazy danych ze wstępnie zainicjowanej warstwy obliczeniowej do warstwy obliczeń bezserwerowych

#### <a name="use-powershell"></a>Korzystanie z programu PowerShell

Poniższy przykład przenosi bazę danych z zainicjowanej warstwy obliczeniowej do warstwy obliczeń bezserwerowych. W tym przykładzie jawnie określono opóźnienie rdzeni wirtualnych, maks rdzeni wirtualnych i AutoPause.

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
  -AutoPauseDelayInMinutes 1440
```

### <a name="move-database-from-serverless-compute-tier-into-provisioned-compute-tier"></a>Przenoszenie bazy danych z warstwy obliczeń bezserwerowej do alokowanej warstwy obliczeniowej

Bezserwerowa baza danych może zostać przeniesiona do warstwy obliczeń aprowizacji w taki sam sposób jak w przypadku przenoszenia zainicjowanej bazy danych obliczeń do warstwy obliczeń bezserwerowych.

## <a name="modifying-serverless-configuration"></a>Modyfikowanie konfiguracji bezserwerowej

### <a name="maximum-vcores"></a>Maksymalna liczba rdzeni wirtualnych

#### <a name="use-powershell"></a>Korzystanie z programu PowerShell

Modyfikowanie maksymalnej rdzeni wirtualnych jest wykonywane przy użyciu polecenia [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) w programie PowerShell przy użyciu `MaxVcore` argumentu.

### <a name="minimum-vcores"></a>Minimalna liczba rdzeni wirtualnych

#### <a name="use-powershell"></a>Korzystanie z programu PowerShell

Modyfikacja minimalnej rdzeni wirtualnych jest wykonywana za pomocą polecenia [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) w programie PowerShell przy użyciu `MinVcore` argumentu.

### <a name="autopause-delay"></a>Opóźnienie AutoPause

#### <a name="use-powershell"></a>Korzystanie z programu PowerShell

Modyfikowanie opóźnienia AutoPause jest wykonywane przy użyciu polecenia [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) w programie PowerShell przy użyciu `AutoPauseDelayInMinutes` argumentu.

## <a name="monitoring"></a>Monitorowanie

### <a name="resources-used-and-billed"></a>Zasoby używane i rozliczane

Zasoby bazy danych bezserwerowej są hermetyzowane przez pakiet aplikacji, wystąpienie SQL i jednostki puli zasobów użytkownika.

#### <a name="app-package"></a>Pakiet aplikacji

Pakiet aplikacji to zewnętrzna granica zarządzania zasobami dla bazy danych, niezależnie od tego, czy baza danych znajduje się w warstwie obliczeniowej bez serwera, czy administracyjna. Pakiet aplikacji zawiera wystąpienie SQL i usługi zewnętrzne, które razem mają zasięg wszystkich zasobów użytkowników i systemu używanych przez bazę danych w SQL Database. Przykłady usług zewnętrznych obejmują wyszukiwanie w języku R i pełnotekstowe. Wystąpienie programu SQL zazwyczaj przeważa nad ogólnym wykorzystaniem zasobów w ramach pakietu aplikacji.

#### <a name="user-resource-pool"></a>Pula zasobów użytkownika

Pula zasobów użytkowników jest wewnętrzną największą granicą zarządzania zasobami dla bazy danych, niezależnie od tego, czy baza danych znajduje się w warstwie obliczeniowej bez użycia serwera, czy administracyjna. Pula zasobów użytkowników umożliwia użycie procesora CPU i operacji we/wy dla obciążeń użytkowników generowanych przez zapytania DDL, takich jak tworzenie i modyfikowanie i wykonywanie zapytań DML, takich jak SELECT, INSERT, UPDATE i DELETE. Te zapytania zazwyczaj reprezentują największą część użycia w pakiecie aplikacji.

### <a name="metrics"></a>Metryki

Metryki monitorowania użycia zasobów pakietu aplikacji i puli użytkowników bazy danych bezserwerowych są wymienione w poniższej tabeli:

|Jednostka|Metryka|Opis|Jednostki|
|---|---|---|---|
|Pakiet aplikacji|app_cpu_percent|Procent rdzeni wirtualnych używany przez aplikację względem maksymalnej rdzeni wirtualnych dozwolony dla aplikacji.|Wartość procentowa|
|Pakiet aplikacji|app_cpu_billed|Kwota obliczeń rozliczanych dla aplikacji w okresie raportowania. Kwota płacona w tym okresie jest iloczynem tej metryki i ceny jednostkowej rdzeń wirtualny. <br><br>Wartości tej metryki są określane przez agregowanie w czasie, gdy jest używana wartość maksymalna procesora CPU i używana pamięć. Jeśli użyta kwota jest mniejsza niż minimalna ilość określona przez minimalną rdzeni wirtualnych i minimalną pamięć, jest naliczana opłata w wysokości minimalnej. Aby porównać procesor z pamięcią na potrzeby rozliczeń, pamięć jest znormalizowana do jednostek rdzeni wirtualnych przez ponowne skalowanie ilości pamięci w GB przez 3 GB na rdzeń wirtualny.|Rdzeń wirtualny sekund|
|Pakiet aplikacji|app_memory_percent|Procent pamięci używanej przez aplikację względem maksymalnej pamięci dozwolonej dla aplikacji.|Wartość procentowa|
|Pula użytkowników|cpu_percent|Procent rdzeni wirtualnych używany przez obciążenie użytkowników względem maksymalnej rdzeni wirtualnych dozwolony dla obciążenia użytkownika.|Wartość procentowa|
|Pula użytkowników|data_IO_percent|Procent operacji we/wy danych używanych przez obciążenie użytkownikami względem maksymalnej liczby operacji we/wy na sekundę dozwolonych dla obciążenia użytkownikami.|Wartość procentowa|
|Pula użytkowników|log_IO_percent|Procent zdarzeń dzienników używanych przez obciążenie użytkowników względem maksymalnej liczby MB dzienników/s dozwolony dla obciążenia użytkownika.|Wartość procentowa|
|Pula użytkowników|workers_percent|Procent procesów roboczych używanych przez obciążenie użytkowników względem maksymalnej liczby procesów roboczych dozwolonych dla obciążenia użytkownikami.|Wartość procentowa|
|Pula użytkowników|sessions_percent|Procent sesji używanych przez obciążenie użytkownikami względem maksymalnej liczby sesji dozwolonych dla obciążenia użytkownika.|Wartość procentowa|

### <a name="pause-and-resume-status"></a>Stan wstrzymania i wznowienia

W Azure Portal stan bazy danych jest wyświetlany w okienku Przegląd serwera zawierającego listę baz danych, które zawiera. Stan bazy danych jest również wyświetlany w okienku Przegląd dla bazy danych programu.

Aby wykonać zapytanie o stan wstrzymania i wznowienia bazy danych za pomocą następującego polecenia programu PowerShell:

```powershell
Get-AzSqlDatabase `
  -ResourceGroupName $resourcegroupname `
  -ServerName $servername `
  -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

## <a name="resource-limits"></a>Limity zasobów

W przypadku limitów zasobów zapoznaj się z tematem [warstwa obliczeń Bezserwerowych](sql-database-vCore-resource-limits-single-databases.md#general-purpose-service-tier-for-serverless-compute).

## <a name="billing"></a>Rozliczenia

Kwota naliczanych obliczeń jest wartością maksymalnego użycia procesora CPU i używanej pamięci w każdej sekundzie. Jeśli ilość używanej procesora CPU i używanej pamięci jest mniejsza niż minimalna ilość przywidziana dla każdej z nich, zostanie naliczona opłata za zainicjowaną kwotę. Aby porównać procesor z pamięcią na potrzeby rozliczeń, pamięć jest znormalizowana do jednostek rdzeni wirtualnych przez ponowne skalowanie ilości pamięci w GB przez 3 GB na rdzeń wirtualny.

- **Zasoby rozliczane**: Procesor i pamięć
- **Kwota rozliczana**: rdzeń wirtualny cena jednostkowa * Max (min rdzeni wirtualnych, rdzeni wirtualnych użyte, min pamięci gb * 1/3, użycie pamięci gb * 1/3) 
- **Częstotliwość rozliczeń**: Na sekundę

Cena jednostkowa rdzeń wirtualny jest kosztem za rdzeń wirtualny na sekundę. Zapoznaj się ze [stroną cennika Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/) w przypadku określonych cen jednostkowych w danym regionie.

Kwota naliczanych obliczeń jest uwidaczniana przez następującą metrykę:

- **Metric**: App_cpu_billed (rdzeń wirtualny s)
- **Definicja**: max (min rdzeni wirtualnych, rdzeni wirtualnychd, min pamięci gb * 1/3, użyto pamięci gb * 1/3)
- **Częstotliwość raportowania**: Za minutę

Ta ilość jest obliczana na sekundę i agregowana w ciągu 1 minuty.

Rozważ użycie bezserwerowej bazy danych skonfigurowanej z 1 min rdzeń wirtualny i 4 maks rdzeni wirtualnych.  Odnosi się to do około 3 GB pamięci minimalnej i 12 GB pamięci maksymalnej.  Załóżmy, że opóźnienie autopauzy jest ustawione na 6 godzin, a obciążenie bazy danych jest aktywne w ciągu pierwszych 2 godzin okresu 24-godzinnego i w inny sposób nieaktywne.    

W takim przypadku baza danych jest rozliczana za obliczenia i przechowywanie w ciągu pierwszych 8 godzin.  Mimo że baza danych jest nieaktywna, rozpoczynająca się po drugiej godzinie, nadal jest rozliczana za obliczenia w ciągu następnych 6 godzin na podstawie minimalnej liczby obliczeń, która została zainicjowana, gdy baza danych jest w trybie online.  Tylko magazyn jest rozliczany w pozostałej części 24-godzinnego okresu, gdy baza danych jest wstrzymana.

Dokładniejszy rachunek obliczeń w tym przykładzie jest obliczany w następujący sposób:

|Interwał czasu|Rdzeni wirtualnych używane w każdej sekundzie|GB używanych w każdej sekundzie|Wymiar obliczeniowy rozliczany|Rdzeń wirtualny s rozliczane w przedziale czasu|
|---|---|---|---|---|
|0:00-1:00|4|9|Rdzeni wirtualnych używane|4 rdzeni wirtualnych * 3600 sekund = 14400 rdzeń wirtualny s|
|1:00-2:00|1|12|Używana pamięć|12 GB * 1/3 * 3600 sekund = 14400 rdzeń wirtualny s|
|2:00-8:00|0|0|Minimalna ilość pamięci zainicjowanej|3 GB * 1/3 * 21600 sekund = 21600 rdzeń wirtualny s|
|8:00-24:00|0|0|Nie jest naliczana żadna stawka w trakcie wstrzymania|0 rdzeń wirtualny sekund|
|Łącznie rdzeń wirtualny s rozliczane w ciągu 24 godzin||||50400 rdzeń wirtualny sekund|

Załóżmy, że cena jednostkowa obliczeń to $0.000073/rdzeń wirtualny/s.  Następnie obliczenia naliczane za ten 24-godzinny okres jest iloczynem ceny jednostkowej obliczeń i rdzeń wirtualny s rozliczane: $0.000073/rdzeń wirtualny/sekundę * 50400 rdzeń wirtualny sekund = $3,68

## <a name="available-regions"></a>Dostępne regiony

Warstwa obliczeń bezserwerowych jest dostępna na całym świecie, z wyjątkiem następujących regionów: Australia Środkowa, Chiny Wschodnie, Chiny Północne, Francja Południowa, Niemcy środkowe, Niemcy Wschodnie, Indie Zachodnie, Korea południowo-Południowa, Zachodnia Republika Południowej Afryki, Północne Zjednoczone Królestwo, Południowe Zjednoczone Królestwo, Zachodnie Zjednoczone Królestwo i zachodnio-środkowe stany USA.

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć, zobacz [szybki start: Utwórz pojedynczą bazę danych w Azure SQL Database przy użyciu](sql-database-single-database-get-started.md)Azure Portal.
- W przypadku limitów zasobów zobacz [limity zasobów warstwy obliczeń Bezserwerowych](sql-database-vCore-resource-limits-single-databases.md#general-purpose-service-tier-for-serverless-compute).
