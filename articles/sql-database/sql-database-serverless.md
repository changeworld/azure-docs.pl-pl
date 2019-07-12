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
ms.date: 07/05/2019
ms.openlocfilehash: 5a1a5ea39c9c0ed8973e1ecfa46977d2d06f83e7
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603611"
---
# <a name="azure-sql-database-serverless-preview"></a>Usługa Azure SQL Database bez użycia serwera (wersja zapoznawcza)

Usługa Azure SQL Database, bez użycia serwera (wersja zapoznawcza) to warstwa wystąpień obliczeniowych dla pojedynczych baz danych, automatycznie skalowanych obliczeniowych, w zależności od potrzeb obciążenia rachunków ilości obliczeń jest używany na sekundę. Warstwa bezserwerowe środowisko obliczeniowe również automatycznie wstrzymuje baz danych okresach nieaktywny, gdy tylko magazynu jest rozliczana i automatycznie wznawia działanie bazy danych, jeśli działanie zwróci.

## <a name="serverless-compute-tier"></a>Warstwa bezserwerowych usług obliczeniowych

W warstwie bezserwerowe środowisko obliczeniowe dla pojedynczej bazy danych jest sparametryzowany przez zakres automatyczne skalowanie obliczeń i opóźnienie autopause.  Konfiguracja z tych parametrów kształt środowisko wydajności bazy danych i koszt zasobów obliczeniowych.

![rozliczenia bez użycia serwera](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance-configuration"></a>Konfiguracja wydajności

- **Minimalne rdzeni wirtualnych** i **maksymalna rdzeni wirtualnych** są konfigurowalne parametry, które określają zakres możliwości obliczeniowych dostępnych dla bazy danych. Limity pamięci i we/wy są proporcjonalne do określony zakres (rdzeń wirtualny).  
- **Opóźnienie autopause** jest konfigurowalne parametr, który definiuje czas bazy danych musi być nieaktywne, zanim automatycznie zostało wstrzymane. Bazy danych zostanie automatycznie wznowione podczas następnego logowania lub inne działanie.  Alternatywnie można wyłączyć autopausing.

### <a name="cost"></a>Koszty

- Koszt bezserwerowa baza danych jest sumą koszt obliczeń oraz kosztów magazynowania.
- Gdy wykorzystanie mocy obliczeniowej jest w zakresie między minimalną i maksymalnych limitów skonfigurowane, koszt obliczeń opiera się na wartość elementów vCore i używanej pamięci.
- Gdy wykorzystanie mocy obliczeniowej jest poniżej granic min skonfigurowane, koszt obliczeń opiera się na min rdzeni wirtualnych i minimalna ilość pamięci skonfigurowane.
- Gdy baza danych jest wstrzymany, koszt obliczeń wynosi zero, i są naliczane tylko koszty magazynowania.
- Koszt magazynu jest określana w taki sam sposób jak w warstwie zainicjowanych zasobów obliczeniowych.

Aby uzyskać więcej informacji kosztów, zobacz [rozliczeń](sql-database-serverless.md#billing).

## <a name="scenarios"></a>Scenariusze

Aplikacje niewymagające użycia serwera jest ceny do wydajności zoptymalizowana pod kątem pojedynczych baz danych o wzorcach użycia sporadycznie, nieprzewidywalne, które akceptowalny pewne opóźnienie w rozgrzewania obliczeń po okresów bezczynności użycia. Z kolei warstwa zainicjowanych zasobów obliczeniowych jest ceny do wydajności zoptymalizowana pod kątem pojedynczych baz danych lub wiele baz danych w elastycznej puli z większym użyciem średnia, że wyłączenie byłoby niepożądane wszelkich opóźnień rozgrzewania obliczeń.

### <a name="scenarios-well-suited-for-serverless-compute"></a>Scenariusze nadają się do bezserwerowe środowisko obliczeniowe

- Pojedyncze bazy danych z tymczasowymi, nieprzewidywalnych wzorców grupową okresy braku aktywności i niższych wykorzystanie średni obliczeniowych wraz z upływem czasu.
- Pojedyncze bazy danych w warstwie zainicjowanych zasobów obliczeniowych, które często jest przeskalowywany w ten sposób i klientów, którzy wolą delegować obliczeń podczas ponownego skalowania do usługi.
- Nowe pojedynczych baz danych bez historii użycia, gdzie obliczeń rozmiaru jest trudne lub niemożliwe do oszacowania przed ich wdrożeniem w bazie danych SQL.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Scenariusze odpowiednie dla zainicjowanych zasobów obliczeniowych

- Pojedyncze bazy danych z wzorców użycia bardziej regularnych stała, przewidywalna i wyższych średnia obliczeń użycia wraz z upływem czasu.
- Bazy danych, które nie tolerują wydajności wad i zalet wynikające z kilku częste odzyskiwanie pamięci przycinanie lub opóźnienie w autoresuming ze stanu wstrzymania.
- Wiele baz danych z tymczasowymi, nieprzewidywalnych wzorców, które mogą być konsolidowane do pul elastycznych w celu uzyskania lepszej optymalizacji ceny do wydajności.

## <a name="comparison-with-provisioned-compute-tier"></a>Porównanie z warstwą zainicjowanych zasobów obliczeniowych

W poniższej tabeli przedstawiono różnice między warstwami bezserwerowe środowisko obliczeniowe i zainicjowanych zasobów obliczeniowych:

| | **Bezserwerowe środowisko obliczeniowe** | **Zainicjowanych zasobów obliczeniowych** |
|:---|:---|:---|
|**Wzorzec korzystania z bazy danych**| Sporadycznie, nieprzewidywalne użycia za pomocą dolnej wykorzystanie średni obliczeniowych wraz z upływem czasu. |  Bardziej regularnych wzorce użycia ze średnią wyższe obliczeń wykorzystania w czasie lub w wielu bazach danych przy użyciu elastycznych pul.|
| **Wysiłek związany z zarządzaniem wydajnością** |Niższy|Wyższe|
|**Obliczenia, skalowanie**|Automatyczne|Ręcznie|
|**Obliczenia czasu odpowiedzi**|Niższe po okresach nieaktywne|Natychmiastowe|
|**Poziom szczegółowości rozliczeń**|Na sekundę|Za godzinę|

## <a name="purchasing-model-and-service-tier"></a>Zakup modelu i warstwy usług

Bez użycia serwera bazy danych SQL jest obecnie obsługiwany tylko w warstwie ogólnego przeznaczenia 5 generacji sprzętu w rdzeniach wirtualnych zakupem modelu.

## <a name="autoscaling"></a>Skalowanie automatyczne

### <a name="scaling-responsiveness"></a>Skalowanie czasu odpowiedzi

Ogólnie rzecz biorąc bez użycia serwera baz danych są uruchamiane na komputerze z wystarczającą pojemność do zaspokojenia zapotrzebowania bez zakłóceń dla dowolnej ilości zasobów obliczeniowych, żądane w ramach limitów przez wartość maksymalna rdzeni wirtualnych. Od czasu do czasu automatycznie równoważenia obciążenia występuje, gdy komputer jest w stanie spełnić zapotrzebowanie na zasoby w ciągu kilku minut. Na przykład jeśli żądanie zasobu jest 4 rdzenie wirtualne, ale dostępne są tylko 2 rdzenie wirtualne, następnie może potrwać kilka minut do równoważenia obciążenia, zanim 4 rdzenie wirtualne są dostarczane. Baza danych pozostaje w trybie online podczas równoważenia obciążenia, z wyjątkiem przez pewien okres, po zakończeniu operacji gdy połączenia zostaną przerwane.

### <a name="memory-management"></a>Zarządzanie pamięcią

Pamięć dla baz danych bez użycia serwera są odzyskiwane ponad często w przypadku baz danych zainicjowanych zasobów obliczeniowych. To zachowanie, ważne jest, aby kontrolować koszty w bez użycia serwera i może wpłynąć na wydajność.

#### <a name="cache-reclamation"></a>Odzyskiwanie pamięci podręcznej

W przeciwieństwie do baz danych zainicjowanych zasobów obliczeniowych pamięci z pamięci podręcznej SQL jest odzyskać z bez użycia serwera bazy danych, gdy wykorzystanie procesora CPU lub pamięci podręcznej jest za mało.

- Wykorzystanie pamięci podręcznej jest uznawany za niska, gdy łączny rozmiar najczęściej ostatnio używane w pamięci podręcznej wpisy spadnie poniżej wartości progowej przez czas.
- Po wyzwoleniu odzyskiwanie pamięci podręcznej, rozmiar pamięci podręcznej docelowej zmniejsza się stopniowo ułamek poprzedniego rozmiaru i odzyskiwanie tylko kontynuuje, jeśli użycie pozostaje niski.
- W przypadku odzyskiwanie pamięci podręcznej zasada służąca do wybierania wpisy w pamięci podręcznej do wykluczenia, to te same zasady wyboru, jak w przypadku baz danych zainicjowanych zasobów obliczeniowych po wysokie wykorzystanie pamięci.
- Poniżej limitu pamięci min, zgodnie z definicją min rdzeni wirtualnych, które można skonfigurować nigdy nie zmniejszeniu rozmiaru pamięci podręcznej.

Zarówno bez użycia serwera, jak i elastycznie obliczeń baz danych, pamięci podręcznej, który może zostać wykluczony wpisów, jeśli całą dostępną pamięć jest używany.

#### <a name="cache-hydration"></a>Wypełniania pamięci podręcznej

Pamięć podręczna SQL zwiększa rozmiar pobieranych z dysków w taki sam sposób, jak i z tego samego szybkością, jak w przypadku aprowizowanych baz danych. Gdy baza danych jest zajęta, pamięci podręcznej może wzrosnąć nieograniczonego do limitu pamięci max.

## <a name="autopausing-and-autoresuming"></a>Autopausing i autoresuming

### <a name="autopausing"></a>Autopausing

Autopausing jest wyzwalany, jeśli wszystkie następujące warunki mają wartość true, czas trwania zwłoki autopause:

- Liczba sesji = 0
- Procesor CPU = 0 w przypadku obciążenia użytkownika w puli użytkownika

Opcja znajduje się wyłączyć autopausing, jeśli to konieczne.

Następujące funkcje nie obsługują autopausing.  Oznacza to jeśli używane są dowolne z następujących funkcji, następnie baza danych pozostaje w trybie online niezależnie od tego czasu trwania aktywności bazy danych:

- Replikacja geograficzna (aktywnej replikacji geograficznej i automatyczny tryb failover grupy).
- Długoterminowe przechowywanie kopii zapasowych (LTR).
- Synchronizacja bazy danych używanej w synchronizacji danych programu SQL.

Tymczasowo nie Autopausing podczas wdrażania niektórych aktualizacji usług, które wymagają, bazy danych w trybie online.  W takich przypadkach autopausing staje się dozwolone ponownie po ukończeniu aktualizacji usługi.

### <a name="autoresuming"></a>Autoresuming

Autoresuming jest wyzwalana w przypadku spełnienia dowolnego z następujących warunków w dowolnym momencie:

|Cecha|Autoresume wyzwalacza|
|---|---|
|Uwierzytelnianie i autoryzacja|Login|
|Wykrywanie zagrożeń|Włączanie/wyłączanie ustawień wykrywania zagrożeń na poziomie bazy danych ani serwera.<br>Modyfikowanie ustawień wykrywania zagrożeń na poziomie bazy danych ani serwera.|
|Odnajdowanie i klasyfikacja danych|Dodawanie, modyfikowanie lub wyświetlanie etykiet czułości|
|Inspekcja|Wyświetlanie rekordów inspekcji.<br>Aktualizowanie lub wyświetlanie zasady inspekcji.|
|Maskowanie danych|Dodawanie, modyfikowanie lub wyświetlanie reguł maskowania danych|
|Transparent Data Encryption|Wyświetl stan lub stan technologii transparent data encryption|
|Magazyn danych zapytań (wydajność)|Modyfikowanie lub wyświetlanie ustawień magazynu zapytań; Usługa dostrajania automatycznego|
|Automatycznego dostrajania|Stosowanie i weryfikacja zalecenia dotyczące automatycznego dostrajania, takich jak automatycznego indeksowania|
|Kopiowanie bazy danych|Utwórz bazę danych jako kopię.<br>Eksportuj do pliku BACPAC.|
|Synchronizacja danych SQL|Synchronizacja między bazami danych Centrum i elementów członkowskich, które działać zgodnie z konfigurowalnym harmonogramem albo są wykonywane ręcznie|
|Modyfikowanie niektórych metadanych bazy danych|Dodawanie nowych znaczników bazy danych.<br>Zmiana rdzeni max, min rdzeni wirtualnych lub opóźnienia autopause.|
|SQL Server Management Studio (SSMS)|Za pomocą programu SSMS wersja 18 lub i otworzyć nowe okno zapytania dla dowolnej bazy danych na serwerze zostanie wznowiona wszystkie wstrzymane automatycznie database w tym samym serwerze. To zachowanie nie występuje, jeśli za pomocą programu SSMS wersji 17.9.1, za pomocą funkcji IntelliSense — wyłączony.|

Autoresuming jest również wyzwalany, podczas wdrażania niektórych aktualizacji usług, które wymagają, bazy danych w trybie online.

### <a name="connectivity"></a>Łączność

Jeśli bezserwerowa baza danych jest wstrzymany, to pierwszy identyfikator logowania będzie wznowić bazy danych i zwrócić komunikat o błędzie informujący, że baza danych jest niedostępna z kodem błędu 40613. Po wznowieniu bazy danych logowania musi zostać powtórzone, można ustanowić łączności. Klienty baz danych przy użyciu logikę ponawiania próby połączenia nie powinni być modyfikowane.

### <a name="latency"></a>Opóźnienie

Czas oczekiwania na autoresume i autopause bezserwerowa baza danych jest ogólnie celu autopause wynoszącym 1 minutę, aby autoresume i 1 do 10 minut.

## <a name="onboarding-into-serverless-compute-tier"></a>Dołączanie do warstwy bezserwerowe środowisko obliczeniowe

Tworzenie nowej bazy danych lub przenoszenie że istniejącą bazę danych w warstwie bezserwerowe środowisko obliczeniowe zgodna z tym samym wzorcem używanym podczas tworzenia nowej bazy danych w elastycznie warstwa wystąpień obliczeniowych i obejmuje następujące dwa kroki.

1. Określona nazwa celu usługi. Cel usługi określa warstwę usługi, generacja sprzętu i maksymalna rdzeni wirtualnych. W poniższej tabeli przedstawiono opcje celu usługi:

   |Nazwa celu usługi|Warstwa usług|Generacja sprzętu|Maksymalna liczba rdzeni wirtualnych|
   |---|---|---|---|
   |GP_S_Gen5_1|Ogólne zastosowanie|5\. generacji|1|
   |GP_S_Gen5_2|Ogólne zastosowanie|5\. generacji|2|
   |GP_S_Gen5_4|Ogólne zastosowanie|5\. generacji|4|

2. Opcjonalnie można określić opóźnienia rdzeni wirtualnych i autopause min, aby zmienić wartości domyślne. W poniższej tabeli przedstawiono dostępne wartości dla tych parametrów.

   |Parametr|Wartość opcji|Wartość domyślna|
   |---|---|---|---|
   |Rdzenie wirtualne min|Dowolne {0,5, 1, 2, 4} nie przekracza maksymalny rdzeni wirtualnych|0,5 rdzeni wirtualnych|
   |Autopause opóźnienia|Minimalnie: 60 minut (1 godzina)<br>Maksimum: do 10 080 minut (7 dni)<br>Zwiększa: 60 minut<br>Wyłącz autopause: -1|60 minut|

> [!NOTE]
> Przy użyciu języka T-SQL, aby przenieść istniejącą bazę danych do bez użycia serwera lub zmienić jego rozmiar obliczeń nie jest obecnie obsługiwane, ale może odbywać się za pośrednictwem witryny Azure portal lub programu PowerShell.

### <a name="create-new-database-in-serverless-compute-tier"></a>Utwórz nową bazę danych w warstwie bezserwerowe środowisko obliczeniowe 

#### <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal

Zobacz [Szybki start: Tworzenie pojedynczej bazy danych w usłudze Azure SQL Database przy użyciu witryny Azure portal](sql-database-single-database-get-started.md).

#### <a name="use-powershell"></a>Korzystanie z programu PowerShell

Poniższy przykład tworzy nową bazę danych w warstwie bezserwerowe środowisko obliczeniowe.  Ten przykład jawnie określa rdzeni wirtualnych min, max rdzeni wirtualnych i autopause opóźnienia.

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

### <a name="move-database-from-provisioned-compute-tier-into-serverless-compute-tier"></a>Przeniesienie bazy danych z zainicjowanych zasobów obliczeniowych warstwy do warstwy bezserwerowe środowisko obliczeniowe

#### <a name="use-powershell"></a>Korzystanie z programu PowerShell

Poniższy przykład przenosi bazę danych z warstwy zainicjowanych zasobów obliczeniowych w warstwie bezserwerowe środowisko obliczeniowe. Ten przykład jawnie określa rdzeni wirtualnych min, max rdzeni wirtualnych i autopause opóźnienia.

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

### <a name="move-database-from-serverless-compute-tier-into-provisioned-compute-tier"></a>Przenoszenie bazy danych z warstwy bezserwerowe środowisko obliczeniowe w warstwie zainicjowanych zasobów obliczeniowych

Bezserwerowa baza danych mogą zostać przeniesione w warstwie zainicjowanych zasobów obliczeniowych, w taki sam sposób jak przeniesienie bazy danych zainicjowanych zasobów obliczeniowych w warstwie bezserwerowe środowisko obliczeniowe.

## <a name="modifying-serverless-configuration"></a>Modyfikowanie konfiguracji bez użycia serwera

### <a name="maximum-vcores"></a>Maksymalna liczba rdzeni wirtualnych

#### <a name="use-powershell"></a>Korzystanie z programu PowerShell

Modyfikowanie max rdzeni wirtualnych odbywa się za pomocą [AzSqlDatabase zestaw](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) polecenia przy użyciu programu PowerShell `MaxVcore` argumentu.

### <a name="minimum-vcores"></a>Minimalna liczba rdzeni wirtualnych

#### <a name="use-powershell"></a>Korzystanie z programu PowerShell

Modyfikowanie rdzeni wirtualnych min odbywa się za pomocą [AzSqlDatabase zestaw](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) polecenia przy użyciu programu PowerShell `MinVcore` argumentu.

### <a name="autopause-delay"></a>Autopause opóźnienia

#### <a name="use-powershell"></a>Korzystanie z programu PowerShell

Modyfikowanie opóźnienie autopause odbywa się za pomocą [AzSqlDatabase zestaw](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) polecenia przy użyciu programu PowerShell `AutoPauseDelayInMinutes` argumentu.

## <a name="monitoring"></a>Monitorowanie

### <a name="resources-used-and-billed"></a>Zasoby używane i rozliczane

Zasoby bez użycia serwera bazy danych są hermetyzowany pakiet aplikacji, wystąpienia SQL i jednostkami puli zasobów użytkownika.

#### <a name="app-package"></a>Pakiet aplikacji

Pakiet aplikacji jest zewnętrzne większość zasobów zarządzania granic dla bazy danych, niezależnie od tego, czy baza danych znajduje się w warstwie obliczeniowych bez użycia serwera lub udostępnione. Pakiet aplikacji zawiera wystąpienie programu SQL, i zewnętrzne usługi tego zakresu razem wszystkie zasoby użytkownika i systemowe używane przez bazę danych w bazie danych SQL. Przykładami usług zewnętrznych języków R i wyszukiwanie pełnotekstowe. Wystąpienie programu SQL zazwyczaj zajmuje większą część ekranu ogólnego użycia zasobów przez pakiet aplikacji.

#### <a name="user-resource-pool"></a>Pula zasobów użytkownika

Pula zasobów użytkownika jest wewnętrzny większość granic zarządzania zasobów dla bazy danych, niezależnie od tego, czy baza danych znajduje się w warstwie obliczeniowych bez użycia serwera lub udostępnione. Użytkownik zasobów zakresy puli procesora CPU i we/wy dla obciążenia użytkownikami, generowane przez kwerendy DDL, takie jak tworzenie i ALTER i DML zapytania, takie jak wybieranie, wstawianie, AKTUALIZOWANIE i usuwanie. Te zapytania zazwyczaj reprezentują najbardziej znacznej wykorzystanie w ramach pakietu aplikacji.

### <a name="metrics"></a>Metryki

Metryki do monitorowania użycia zasobów w puli aplikacji pakietu i użytkowników bez użycia serwera bazy danych są wymienione w poniższej tabeli:

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
- **Kwota rachunku**: cena jednostkowa (rdzeń wirtualny) * max (min rdzeni wirtualnych, rdzenie wirtualne używane, minimalna ilość pamięci GB * pamięci 1/3 GB używane * 1/3) 
- **Częstotliwość rozliczeń**: Na sekundę

Cena jednostkowa — rdzeń wirtualny w koszt na sekundę (rdzeń wirtualny). Zapoznaj się [usługi Azure SQL Database, cennik](https://azure.microsoft.com/pricing/details/sql-database/single/) dla określonej jednostki ceny w danym regionie.

Ilość zasobów obliczeniowych, naliczana jest uwidaczniany przez następujące metryki:

- **Metryka**: app_cpu_billed (rdzeń wirtualny w sekundach)
- **Definicja**: maksymalny (min rdzeni wirtualnych, rdzenie wirtualne używane, minimalna ilość pamięci GB * pamięci 1/3 GB używane * 1/3)
- **Częstotliwość raportowania**: Za minutę

Ta ilość jest obliczane co sekundę i zagregowane ponad 1 minutę.

Należy wziąć pod uwagę bez użycia serwera bazy danych skonfigurowano wartość elementów vCore 1 min i 4 rdzenie wirtualne max.  Odpowiada to około 3 GB, minimalna ilość pamięci i pamięci max 12 GB.  Załóżmy, że ustawiono opóźnienie automatycznego wstrzymywania do 6 godzin, a obciążenie bazy danych jest 2 godzinach pierwszego okresu 24-godzinnego i w przeciwnym razie nieaktywnych.    

W takim przypadku baza danych jest naliczana za zasoby obliczeniowe i Magazyn podczas pierwszego 8 godzin.  Mimo, że baza danych znajduje się nieaktywne uruchamianie po godzinie, nadal jest rozliczana za obliczenia w kolejnych 6 godzin, oparte na obliczenia minimalne zainicjowano obsługę administracyjną, gdy baza danych jest w trybie online.  Tylko magazyn jest rozliczana w pozostałej części 24-godzinnego okresu, gdy baza danych jest wstrzymany.

Mówiąc ściślej na rachunku obliczeniowych, w tym przykładzie jest obliczana w następujący sposób:

|Przedział czasu|rdzenie wirtualne używane w każdej sekundzie|Używane w każdej sekundzie GB|Obliczenia wymiarów rozliczane|rozliczane przedziałach czasu w sekundach (rdzeń wirtualny)|
|---|---|---|---|---|
|0:00-1:00|4|9|rdzenie wirtualne używane|4 rdzenie wirtualne * 3600 sekund = 14400 (rdzeń wirtualny) sekund|
|1:00-2:00|1|12|Pamięć użyta|12 GB * 1/3 * 3600 sekund = 14400 sekund (rdzeń wirtualny)|
|2:00-8:00|0|0|Minimalna ilość pamięci aprowizacji|3 GB * 1/3 * 21600 sekundy = 21600 sekund (rdzeń wirtualny)|
|8:00-24:00|0|0|Nie obliczeniowe naliczane podczas wstrzymania|0 — rdzeń wirtualny w sekundach|
|Łączna liczba (rdzeń wirtualny) sekund naliczane przez 24 godziny||||50400 — rdzeń wirtualny w sekundach|

Załóżmy, że cena jednostki obliczeniowej jest $0.000073/vCore/second.  Zasoby obliczeniowe naliczane w tym okresie 24-godzinnym jest wynikiem sekund ceny i rdzeń wirtualny jednostki obliczeniowe naliczane: $0.000073/vCore/second * 50400 sekund — rdzeń wirtualny = 3.68 $

## <a name="available-regions"></a>Dostępne regiony

Warstwa bezserwerowe środowisko obliczeniowe jest dostępna na całym świecie z wyjątkiem następujących regionów: Australia Środkowa, Chiny wschodnie, Chiny Północne, Francja Południowa, Niemcy środkowe, Niemcy północno-wschodnie, Indie Zachodnie, Korea Południowa, Zachodnia RPA, Północne Zjednoczone Królestwo, południowe Zjednoczone Królestwo, zachodnie Zjednoczone Królestwo i zachodnio środkowe stany USA.

## <a name="next-steps"></a>Kolejne kroki

- Aby rozpocząć pracę, zobacz [Szybki Start: Tworzenie pojedynczej bazy danych w usłudze Azure SQL Database przy użyciu witryny Azure portal](sql-database-single-database-get-started.md).
- Limity zasobów, zobacz [aplikacje niewymagające użycia serwera obliczenia limity zasobów warstwy](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier).
