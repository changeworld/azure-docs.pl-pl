---
title: Praca bezserwerowa
description: W tym artykule opisano nową warstwę obliczeniową bez serwera i porównuje ją z istniejącą warstwą obliczeniową aprowizowana
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: test
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein, carlrab
ms.date: 3/11/2020
ms.openlocfilehash: 8a72d3dc7f7b3fddf66df8111b1e92116a62883a
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474027"
---
# <a name="azure-sql-database-serverless"></a>Bezserwerowa baza danych SQL azure

Usługa Azure SQL Database serverless to warstwa obliczeniowa dla pojedynczych baz danych, która automatycznie skaluje obliczenia na podstawie zapotrzebowania na obciążenia i rachunków za ilość obliczeń używanych na sekundę. Warstwa obliczeniowa bezserwerowa automatycznie wstrzymuje również bazy danych w okresach nieaktywnych, gdy rozliczany jest tylko magazyn i automatycznie wznawia bazy danych po powrocie aktywności.

## <a name="serverless-compute-tier"></a>Warstwa bezserwerowych usług obliczeniowych

Warstwa obliczeniowa bezserwerowa dla pojedynczej bazy danych jest parametryzowana przez zakres skalowania automatycznego obliczeń i opóźnienie autopause.  Konfiguracja tych parametrów kształtuje wydajność bazy danych i koszt obliczeń.

![rozliczenia bezserwerowe](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance-configuration"></a>Konfiguracja wydajności

- **Minimalne pole wirtualne** i maksymalne pole wirtualne są konfigurowalnymi **parametrami,** które definiują zakres pojemności obliczeniowej dostępnej dla bazy danych. Limity pamięci i we/wy są proporcjonalne do określonego zakresu owo wirtualnego.  
- **Opóźnienie autopause** jest konfigurowalny parametr, który definiuje okres czasu bazy danych musi być nieaktywny, zanim zostanie automatycznie wstrzymana. Baza danych jest automatycznie wznawiana po wystąpieniu następnego logowania lub innej aktywności.  Alternatywnie, autopausing można wyłączyć.

### <a name="cost"></a>Koszty

- Koszt bazy danych bezserwerowej jest sumowaniem kosztów obliczeniowych i kosztów magazynu.
- Gdy użycie obliczeń jest między minimalnymi i maksymalnymi limitami skonfigurowanym, koszt obliczeń jest oparty na chowanego komputerze i pamięci.
- Gdy użycie obliczeń jest poniżej skonfigurowanych limitów min, koszt obliczeń jest oparty na minimalnych rach wirtualnych i pamięci minimalnej skonfigurowanej.
- Gdy baza danych jest wstrzymana, koszt obliczeń wynosi zero i ponoszone są tylko koszty magazynu.
- Koszt magazynu jest określany w taki sam sposób, jak w warstwie obliczeń aprowizowanych.

Aby uzyskać więcej informacji o kosztach, zobacz [Rozliczenia](sql-database-serverless.md#billing).

## <a name="scenarios"></a>Scenariusze

Bezserwerowa jest cena-wydajność zoptymalizowana dla pojedynczych baz danych z przerywane, nieprzewidywalne wzorce użycia, które mogą sobie pozwolić na pewne opóźnienia w rozgrzewce obliczeń po bezczynnych okresów użytkowania. Z drugiej strony aprowizowana warstwa obliczeniowa jest zoptymalizowana pod kątem wydajności cenowej dla pojedynczych baz danych lub wielu baz danych w pulach elastycznych o wyższym średnim użyciu, które nie mogą pozwolić sobie na żadne opóźnienia w rozgrzewaniu obliczeń.

### <a name="scenarios-well-suited-for-serverless-compute"></a>Scenariusze dobrze dostosowane do obliczeń bezserwerowych

- Pojedyncze bazy danych z przerywanymi, nieprzewidywalnymi wzorcami użycia przeplatane okresami braku aktywności i niższym średnim wykorzystaniem obliczeń w czasie.
- Pojedyncze bazy danych w warstwie obliczeniowej aprowizowanego, które są często przeskalowane, oraz klienci, którzy wolą delegować ponowne skalowanie obliczeń do usługi.
- Nowe pojedyncze bazy danych bez historii użycia, gdzie rozmiary obliczeń jest trudne lub nie można oszacować przed wdrożeniem w bazie danych SQL.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Scenariusze dobrze dostosowane do aprowizowanych obliczeń

- Pojedyncze bazy danych z bardziej regularnych, przewidywalne wzorce użycia i wyższe średnie wykorzystanie obliczeń w czasie.
- Bazy danych, które nie tolerują kompromisów wydajności wynikających z częstszego przycinania pamięci lub opóźnienia w autoresuming ze stanu wstrzymanego.
- Wiele baz danych z przerywanym, nieprzewidywalne wzorce użycia, które mogą być skonsolidowane w elastycznych pul dla lepszej optymalizacji ceny wydajności.

## <a name="comparison-with-provisioned-compute-tier"></a>Porównanie z aprowizowanym poziomem obliczeniowym

W poniższej tabeli podsumowano rozróżnienia między warstwą obliczeniową bez serwera a warstwą obliczeniową aprowizowana:

| | **Bezserwerowe usługi obliczeniowe** | **Obliczenia aprowizacji** |
|:---|:---|:---|
|**Wzorzec użycia bazy danych**| Sporadyczne, nieprzewidywalne użycie z niższym średnim wykorzystaniem obliczeń w czasie. |  Bardziej regularne wzorce użycia z wyższym średnim wykorzystaniem obliczeń w czasie lub wiele baz danych przy użyciu pul elastycznych.|
| **Nakłady na zarządzanie wydajnością** |Lower|Wyższe|
|**Skalowanie obliczeń**|Automatyczny|Ręcznie|
|**Obliczeń responsywności**|Niższe po nieaktywnych okresach|Natychmiastowe|
|**Szczegółowość rozliczeń**|Sekundę|Godzinę|

## <a name="purchasing-model-and-service-tier"></a>Model zakupu i warstwa usług

Baza danych SQL bez servera jest obecnie obsługiwana tylko w warstwie ogólnego przeznaczenia na sprzęcie generacji 5 w modelu zakupu vCore.

## <a name="autoscaling"></a>Skalowanie automatyczne

### <a name="scaling-responsiveness"></a>Skalowanie responsywności

Ogólnie rzecz biorąc bezserwerowe bazy danych są uruchamiane na komputerze o wystarczającej pojemności, aby zaspokoić zapotrzebowanie na zasoby bez przerwy dla dowolnej ilości obliczeń wymaganych w granicach określonych przez wartość max vCores. Od czasu do czasu równoważenie obciążenia występuje automatycznie, jeśli maszyna nie jest w stanie zaspokoić zapotrzebowania na zasoby w ciągu kilku minut. Na przykład jeśli zapotrzebowanie na zasoby wynosi 4 punów wirtualnych, ale dostępne są tylko 2 punów wirtualnych, może upłynąć do kilku minut, zanim zostaną dostarczone 4 vCore. Baza danych pozostaje w trybie online podczas równoważenia obciążenia, z wyjątkiem krótkiego okresu po zakończeniu operacji, gdy połączenia są odrzucane.

### <a name="memory-management"></a>Zarządzanie pamięcią

Pamięć dla baz danych bezserwerowych jest odzyskiwany częściej niż w przypadku baz danych obliczeniowych aprowizacji. To zachowanie jest ważne, aby kontrolować koszty w bezserwerowej i może mieć wpływ na wydajność.

#### <a name="cache-reclamation"></a>Rekultywacja pamięci podręcznej

W przeciwieństwie do aprowizacji baz danych obliczeniowych pamięci z pamięci podręcznej SQL jest odzyskiwał z bazy danych bezserwerowej, gdy wykorzystanie procesora CPU lub pamięci podręcznej jest niskie.

- Wykorzystanie pamięci podręcznej jest uważane za niskie, gdy całkowity rozmiar ostatnio używanych wpisów pamięci podręcznej spada poniżej progu przez pewien okres czasu.
- Po wyzwoleniu odzyskiwania pamięci podręcznej rozmiar docelowej pamięci podręcznej jest zmniejszany stopniowo do ułamka poprzedniego rozmiaru i odzyskiwanie jest kontynuowane tylko wtedy, gdy użycie pozostaje niskie.
- W przypadku rekultywacji pamięci podręcznej zasady wybierania wpisów pamięci podręcznej do eksmisji są takie same zasady wyboru, jak w przypadku aprowizacji baz danych obliczeniowych, gdy ciśnienie pamięci jest wysokie.
- Rozmiar pamięci podręcznej nigdy nie jest zmniejszany poniżej limitu pamięci min zdefiniowanego przez min- owe, które można skonfigurować.

W bazach danych obliczeniowych bez użycia serwera i aprowizowanych można eksmitować wpisy pamięci podręcznej, jeśli używana jest cała dostępna pamięć.

#### <a name="cache-hydration"></a>Nawodnienie pamięci podręcznej

Pamięć podręczna SQL rośnie w miarę pobierania danych z dysku w taki sam sposób i z taką samą szybkością, jak w przypadku aprowizacji baz danych. Gdy baza danych jest zajęta, pamięć podręczna może rosnąć nieograniczony do maksymalnego limitu pamięci.

## <a name="autopausing-and-autoresuming"></a>Automatycznepausing i autoresuming

### <a name="autopausing"></a>Automatycznepausing

Automatycznepausing jest wyzwalany, jeśli wszystkie następujące warunki są spełnione na czas trwania opóźnienia autopause:

- Liczba sesji = 0
- CPU = 0 dla obciążenia użytkownika uruchomionego w puli użytkowników

Dostępna jest opcja wyłączenia automatycznegopausingu w razie potrzeby.

Poniższe funkcje nie obsługują automatycznegopausingu.  Oznacza to, że jeśli jest używana którakolwiek z następujących funkcji, baza danych pozostaje w trybie online, niezależnie od czasu trwania braku aktywności bazy danych:

- Replikacja geograficzna (aktywne grupy replikacji geograficznej i automatycznego trybu failover).
- Długoterminowe przechowywanie kopii zapasowych (LTR).
- Baza danych synchronizacji używana w synchronizacji danych SQL.  W przeciwieństwie do baz danych synchronizacji, bazy danych centrum i członków obsługują automatycznepausing.
- Baza danych zadań używana w zadaniach elastycznych.

Automatycznepausing jest tymczasowo uniemożliwione podczas wdrażania niektórych aktualizacji usługi, które wymagają bazy danych w trybie online.  W takich przypadkach automatycznepausing staje się dozwolone ponownie po zakończeniu aktualizacji usługi.

### <a name="autoresuming"></a>Autoresuming

Autoresuming jest wyzwalany, jeśli którykolwiek z następujących warunków są spełnione w dowolnym momencie:

|Funkcja|Wyzwalacz Autoresume|
|---|---|
|Uwierzytelnianie i autoryzacja|Logowanie|
|Wykrywanie zagrożeń|Włączanie/wyłączanie ustawień wykrywania zagrożeń na poziomie bazy danych lub serwera.<br>Modyfikowanie ustawień wykrywania zagrożeń na poziomie bazy danych lub serwera.|
|Odnajdowanie i klasyfikacja danych|Dodawanie, modyfikowanie, usuwanie lub wyświetlanie etykiet czułości|
|Inspekcja|Wyświetlanie rekordów inspekcji.<br>Aktualizowanie lub wyświetlanie zasad inspekcji.|
|Maskowanie danych|Dodawanie, modyfikowanie, usuwanie lub wyświetlanie reguł maskowania danych|
|Transparent Data Encryption|Wyświetlanie stanu lub stanu przezroczystego szyfrowania danych|
|Magazyn danych kwerend (wydajność)|Modyfikowanie lub wyświetlanie ustawień magazynu zapytań|
|Autodostrajania|Stosowanie i weryfikacja zaleceń dotyczących automatycznego dostrajania, takich jak automatyczne indeksowanie|
|Kopiowanie bazy danych|Utwórz bazę danych jako kopię.<br>Eksportowanie do pliku BACPAC.|
|Synchronizacja danych SQL|Synchronizacja między bazami danych koncentratora i elementów członkowskich, które są uruchamiane zgodnie z konfigurowalnym harmonogramem lub są wykonywane ręcznie|
|Modyfikowanie niektórych metadanych bazy danych|Dodawanie nowych znaczników bazy danych.<br>Zmiana maksymalnej ilości ładowań wirtualnych, min. rów lub opóźnienia autopause.|
|SQL Server Management Studio (SSMS)|Przy użyciu wersji SSMS wcześniej niż 18.1 i otwarcie nowego okna kwerendy dla dowolnej bazy danych na serwerze spowoduje wznowienie dowolnej automatycznie wstrzymanej bazy danych na tym samym serwerze. To zachowanie nie występuje, jeśli przy użyciu usługi SSMS w wersji 18.1 lub nowszej.|

Autoresuming jest również wyzwalany podczas wdrażania niektórych aktualizacji usługi, które wymagają bazy danych w trybie online.

### <a name="connectivity"></a>Łączność

Jeśli baza danych bezserwerowa jest wstrzymana, pierwszy login zostanie wznowiony i zwróci błąd informujący, że baza danych jest niedostępna z kodem błędu 40613. Po wznowieniu bazy danych logowania należy ponowić, aby ustanowić łączność. Klienci bazy danych z logiką ponawiania połączeń nie powinny być modyfikowane.

### <a name="latency"></a>Opóźnienie

Opóźnienie autoresume i autopause bezserwerowej bazy danych jest zazwyczaj w kolejności 1 minut do autoresume i 1-10 minut do autopause.

### <a name="customer-managed-transparent-data-encryption-byok"></a>Zarządzane przez klienta przezroczyste szyfrowanie danych (BYOK)

Jeśli przy użyciu [zarządzanego przez klienta przezroczystego szyfrowania danych](transparent-data-encryption-byok-azure-sql.md) (BYOK) i bezserwerowej bazy danych jest automatycznie wstrzymana po usunięciu lub odwołaniu klucza, baza danych pozostaje w stanie automatycznego wstrzymania.  W takim przypadku po otwarciu bazy danych baza danych pozostaje w trybie online, dopóki jej stan nie przejdzie do niedostępnego po około 10 minutach lub mniej.  Gdy baza danych staje się niedostępna, proces odzyskiwania jest taki sam jak dla aprowizacji baz danych obliczeniowych.  Jeśli baza danych bezserwerowa jest w trybie online, gdy występuje usunięcie lub cofnięcie klucza, baza danych staje się również niedostępna po około 10 minutach lub mniej w taki sam sposób, jak w przypadku aprowizacji baz danych obliczeniowych.

## <a name="onboarding-into-serverless-compute-tier"></a>Dołączanie do warstwy obliczeniowej bezserwerowej

Tworzenie nowej bazy danych lub przenoszenie istniejącej bazy danych do warstwy obliczeniowej bezserwerowej jest zgodne z tym samym wzorcem, co tworzenie nowej bazy danych w warstwie obliczeniowej aprowizowana i obejmuje następujące dwa kroki.

1. Określ nazwę celu usługi. Cel usługi określa warstwę usług, generowanie sprzętu i max vCores. W poniższej tabeli przedstawiono opcje celu usługi:

   |Nazwa celu usługi|Warstwa usług|Generowanie sprzętu|Max vCores (Max vCores)|
   |---|---|---|---|
   |GP_S_Gen5_1|Ogólnego przeznaczenia|Gen5|1|
   |GP_S_Gen5_2|Ogólnego przeznaczenia|Gen5|2|
   |GP_S_Gen5_4|Ogólnego przeznaczenia|Gen5|4|
   |GP_S_Gen5_6|Ogólnego przeznaczenia|Gen5|6|
   |GP_S_Gen5_8|Ogólnego przeznaczenia|Gen5|8|
   |GP_S_Gen5_10|Ogólnego przeznaczenia|Gen5|10|
   |GP_S_Gen5_12|Ogólnego przeznaczenia|Gen5|12|
   |GP_S_Gen5_14|Ogólnego przeznaczenia|Gen5|14|
   |GP_S_Gen5_16|Ogólnego przeznaczenia|Gen5|16|

2. Opcjonalnie należy określić min-rów i opóźnienie autopause, aby zmienić ich wartości domyślne. W poniższej tabeli przedstawiono dostępne wartości dla tych parametrów.

   |Parametr|Wybór wartości|Wartość domyślna|
   |---|---|---|---|
   |Min.|Zależy od maksymalnej konfiguracji vCores - zobacz [limity zasobów](sql-database-vcore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5).|0,5 r. rw|
   |Opóźnienie autopause|Minimum: 60 minut (1 godzina)<br>Maksymalnie: 10080 minut (7 dni)<br>Przyrosty: 60 minut<br>Wyłącz autopause: -1|60 min.|


### <a name="create-new-database-in-serverless-compute-tier"></a>Tworzenie nowej bazy danych w warstwie obliczeniowej bezserwerowej 

Poniższe przykłady utworzyć nową bazę danych w warstwie obliczeniowej bez serwera. Przykłady jawnie określają min vCores, max vCores i opóźnienie autopause.

#### <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal

Zobacz [Szybki start: Tworzenie pojedynczej bazy danych w bazie danych SQL usługi Azure przy użyciu witryny Azure Portal](sql-database-single-database-get-started.md).


#### <a name="use-powershell"></a>Korzystanie z programu PowerShell

```powershell
New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -ComputeModel Serverless -Edition GeneralPurpose -ComputeGeneration Gen5 `
  -MinVcore 0.5 -MaxVcore 2 -AutoPauseDelayInMinutes 720
```
#### <a name="use-azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli
az sql db create -g $resourceGroupName -s $serverName -n $databaseName `
  -e GeneralPurpose -f Gen5 -min-capacity 0.5 -c 2 --compute-model Serverless --auto-pause-delay 720
```


#### <a name="use-transact-sql-t-sql"></a>Użyj usługi Transact-SQL (T-SQL)

Poniższy przykład tworzy nową bazę danych w warstwie obliczeniowej bez serwera.

```sql
CREATE DATABASE testdb
( EDITION = 'GeneralPurpose', SERVICE_OBJECTIVE = 'GP_S_Gen5_1' ) ;
```

Aby uzyskać szczegółowe informacje, zobacz [TWORZENIE BAZY DANYCH](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current).  

### <a name="move-database-from-provisioned-compute-tier-into-serverless-compute-tier"></a>Przenoszenie bazy danych z aprowizowanego poziomu obliczeniowego do warstwy obliczeniowej bezserwerowej

Poniższe przykłady przenieść bazę danych z aprowizowanego warstwy obliczeniowej do warstwy obliczeniowej bez serwera. Przykłady jawnie określają min vCores, max vCores i opóźnienie autopause.

#### <a name="use-powershell"></a>Korzystanie z programu PowerShell


```powershell
Set-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -Edition GeneralPurpose -ComputeModel Serverless -ComputeGeneration Gen5 `
  -MinVcore 1 -MaxVcore 4 -AutoPauseDelayInMinutes 1440
```

#### <a name="use-azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli
az sql db update -g $resourceGroupName -s $serverName -n $databaseName `
  --edition GeneralPurpose --min-capacity 1 --capacity 4 --family Gen5 --compute-model Serverless --auto-pause-delay 1440
```


#### <a name="use-transact-sql-t-sql"></a>Użyj usługi Transact-SQL (T-SQL)

Poniższy przykład przenosi bazę danych z aprowizowanego warstwy obliczeniowej do warstwy obliczeniowej bez serwera.

```sql
ALTER DATABASE testdb 
MODIFY ( SERVICE_OBJECTIVE = 'GP_S_Gen5_1') ;
```

Aby uzyskać szczegółowe informacje, zobacz [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current).

### <a name="move-database-from-serverless-compute-tier-into-provisioned-compute-tier"></a>Przenoszenie bazy danych z warstwy obliczeniowej bezserwerowej do aprowizowanego poziomu obliczeniowego

Bezserwerowa baza danych może zostać przeniesiona do aprowizowanego poziomu obliczeniowego w taki sam sposób, jak przeniesienie aprowizowana baza danych obliczeniowych do warstwy obliczeniowej bezserwerowej.

## <a name="modifying-serverless-configuration"></a>Modyfikowanie konfiguracji bezserwerowej

### <a name="use-powershell"></a>Korzystanie z programu PowerShell

Modyfikowanie maksymalnych lub minimalnych corów wirtualnych i opóźnienie autopauzy jest wykonywane przy użyciu polecenia [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) w programie PowerShell przy użyciu przycisku `MaxVcore`, `MinVcore`i `AutoPauseDelayInMinutes` argumentów.

### <a name="use-azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Modyfikowanie maksymalnych lub minimalnych pole wirtualnych i opóźnienie autopause jest wykonywane przy użyciu polecenia `capacity` `min-capacity` [az sql db update](/cli/azure/sql/db#az-sql-db-update) w usłudze Azure CLI przy użyciu argumentów , i `auto-pause-delay` argumentów.


## <a name="monitoring"></a>Monitorowanie

### <a name="resources-used-and-billed"></a>Zasoby używane i rozliczane

Zasoby bazy danych bez użycia serwera są hermetyzowane przez pakiet aplikacji, wystąpienie SQL i jednostki puli zasobów użytkownika.

#### <a name="app-package"></a>Pakiet aplikacji

Pakiet aplikacji jest najbardziej zewnętrzną granicą zarządzania zasobami dla bazy danych, niezależnie od tego, czy baza danych znajduje się w warstwie obliczeniowej bez użycia serwera, czy aprowizowana. Pakiet aplikacji zawiera wystąpienie SQL i usługi zewnętrzne, które razem zawierają zakres wszystkich zasobów użytkownika i systemu używanych przez bazę danych w bazie danych SQL. Przykłady usług zewnętrznych obejmują R i wyszukiwanie pełnotekstowe. Wystąpienie SQL zazwyczaj dominuje ogólne wykorzystanie zasobów w pakiecie aplikacji.

#### <a name="user-resource-pool"></a>Pula zasobów użytkownika

Pula zasobów użytkownika jest wewnętrzną granicą zarządzania zasobami dla bazy danych, niezależnie od tego, czy baza danych znajduje się w warstwie obliczeniowej bez użycia serwera, czy aprowizowana. Pula zasobów użytkownika zakresy CPU i We/Wy dla obciążenia użytkownika generowane przez kwerendy DDL, takich jak CREATE i ALTER i DML kwerend, takich jak SELECT, INSERT, UPDATE i DELETE. Te zapytania zazwyczaj reprezentują najbardziej znaczną część wykorzystania w pakiecie aplikacji.

### <a name="metrics"></a>Metryki

Metryki monitorowania użycia zasobów pakietu aplikacji i puli użytkowników bazy danych bez użycia serwera są wymienione w poniższej tabeli:

|Jednostka|Metryka|Opis|Jednostki|
|---|---|---|---|
|Pakiet aplikacji|app_cpu_percent|Procent owo wirtualnych używanych przez aplikację w stosunku do maksymalnej liczby korpów wirtualnych dozwolonych dla aplikacji.|Procentowe|
|Pakiet aplikacji|app_cpu_billed|Kwota obliczeń naliczonych dla aplikacji w okresie raportowania. Kwota zapłacona w tym okresie jest iloczynem tej metryki i ceny jednostkowej vCore. <br><br>Wartości tej metryki są określane przez agregowanie w czasie maksymalną używaną pamięć CPU i pamięci używanej co sekundę. Jeśli użyta kwota jest mniejsza niż minimalna kwota aprowizowana ustawiona przez min.Aby porównać procesor CPU z pamięcią do celów rozliczeniowych, pamięć jest znormalizowana do jednostek rdzeni wirtualnych przez przeskalowanie ilości pamięci w GB o 3 GB na rów wirtualny.|sekundy vCore|
|Pakiet aplikacji|app_memory_percent|Procent pamięci używanej przez aplikację w stosunku do maksymalnej pamięci dozwolonej dla aplikacji.|Procentowe|
|Pula użytkowników|cpu_percent|Procent owo wirtualnych używanych przez obciążenie użytkownika w stosunku do maksymalnej liczby korpów wirtualnych dozwolonych dla obciążenia użytkownika.|Procentowe|
|Pula użytkowników|data_IO_percent|Procent danych We/Wy używane przez obciążenie użytkownika w stosunku do maksymalnej liczby danych We/Wy dozwolone dla obciążenia użytkownika.|Procentowe|
|Pula użytkowników|log_IO_percent|Procent mb/ów dziennika używanego przez obciążenie użytkownika w stosunku do maksymalnej liczby mb/ów dziennika dozwolonych dla obciążenia użytkownika.|Procentowe|
|Pula użytkowników|workers_percent|Procent pracowników używanych przez obciążenie użytkownika w stosunku do maksymalnej liczby pracowników dozwolonych dla obciążenia użytkownika.|Procentowe|
|Pula użytkowników|sessions_percent|Procent sesji używanych przez obciążenie użytkownika w stosunku do maksymalnej liczby sesji dozwolonych dla obciążenia użytkownika.|Procentowe|

### <a name="pause-and-resume-status"></a>Stan wstrzymania i wznowienia

W witrynie Azure portal stan bazy danych jest wyświetlany w okienku przeglądu serwera, który zawiera listę baz danych, które zawiera. Stan bazy danych jest również wyświetlany w okienku przeglądu bazy danych.

Używanie następujących poleceń do wykonywania kwerendy o stanie wstrzymania i wznowienia bazy danych:

#### <a name="use-powershell"></a>Korzystanie z programu PowerShell

```powershell
Get-AzSqlDatabase -ResourceGroupName $resourcegroupname -ServerName $servername -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

#### <a name="use-azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli
az sql db show --name $databasename --resource-group $resourcegroupname --server $servername --query 'status' -o json
```


## <a name="resource-limits"></a>Limity zasobów

Aby zapoznać się z limitami zasobów, zobacz [warstwę obliczeniową bezserwerową](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5).

## <a name="billing"></a>Rozliczenia

Ilość obliczeń rozliczanych jest maksymalna używane procesora CPU i pamięci używanej co sekundę. Jeśli ilość używanego procesora CPU i użytej pamięci jest mniejsza niż minimalna kwota aprowizowana dla każdego, a następnie aprowizowana kwota jest rozliczana. Aby porównać procesor CPU z pamięcią do celów rozliczeniowych, pamięć jest znormalizowana do jednostek rdzeni wirtualnych przez przeskalowanie ilości pamięci w GB o 3 GB na rów wirtualny.

- **Rozliczane zasób:** procesor i pamięć
- **Zaliczona kwota:** cena jednostkowa vCore * max (min. vCore, używane vCore, min pamięci GB * 1/3, pamięć GB używana * 1/3) 
- **Częstotliwość rozliczeń**: Na sekundę

Cena jednostkowa vCore jest kosztem na r/r za sekundę. Informacje na temat określonych cen jednostkowych w danym regionie można znaleźć na [stronie cen usługi Azure SQL Database.](https://azure.microsoft.com/pricing/details/sql-database/single/)

Kwota obliczeń rozliczanych jest widoczna na następujące metryki:

- **Metryczne**: app_cpu_billed (vCore sekundy)
- **Definicja:** max (min. vCores, używane vCore, min pamięci GB * 1/3, pamięć GB używana * 1/3)
- **Częstotliwość raportowania**: Na minutę

Ilość ta jest obliczana co sekundę i agregowana przez 1 minutę.

Należy wziąć pod uwagę bezserwerową bazę danych skonfigurowaną z 1 min vCore i 4 max vCores.  Odpowiada to około 3 GB pamięci min i 12 GB pamięci maksymalnej.  Załóżmy, że opóźnienie automatycznej pauzy jest ustawione na 6 godzin, a obciążenie bazy danych jest aktywne w ciągu pierwszych 2 godzin okresu 24-godzinnego i w przeciwnym razie nieaktywne.    

W takim przypadku baza danych jest rozliczana za zasoby obliczeniowe i magazyn w ciągu pierwszych 8 godzin.  Mimo że baza danych jest nieaktywna począwszy od drugiej godziny, nadal jest rozliczana za obliczenia w kolejnych 6 godzin na podstawie minimalnej aprowizacji obliczeń, gdy baza danych jest w trybie online.  Tylko magazyn jest rozliczany w pozostałej części okresu 24-godzinnego, gdy baza danych jest wstrzymana.

Dokładniej, rachunek obliczeń w tym przykładzie jest obliczany w następujący sposób:

|Przedział czasu|VCore używane co sekundę|GB używane co sekundę|Rozliczany wymiar obliczeń|Sekundy vCore rozliczane w przedziale czasu|
|---|---|---|---|---|
|0:00-1:00|4|9|Używane vCore|4 vCores * 3600 sekund = 14400 vCore sekund|
|1:00-2:00|1|12|Używana pamięć|12 GB * 1/3 * 3600 sekund = 14400 sekund vCore|
|2:00-8:00|0|0|Minimalna aprowizowana pamięć|3 GB * 1/3 * 21600 sekund = 21600 sekund vCore|
|8:00-24:00|0|0|Brak naliczania obliczeń podczas wstrzymywania|0 sekund vCore|
|Łączna liczba sekund vCore rozliczonych w ciągu 24 godzin||||50400 vCore sekund|

Załóżmy, że obliczona cena jednostkowa wynosi 0,000145 USD/r/r/sekundę.  Następnie obliczenia rozliczane za ten 24-godzinny okres jest produktem obliczającej ceny jednostkowej i vCore sekundy rozliczane: $0.000145/vCore/second * 50400 vCore sekund ~ $7.31

### <a name="azure-hybrid-benefit-and-reserved-capacity"></a>Korzyści hybrydowe platformy Azure i zarezerwowana pojemność

Korzyści hybrydowe platformy Azure (AHB) i rabaty za rezerwowaną pojemność nie mają zastosowania do warstwy obliczeniowej bezserwerowej.

## <a name="available-regions"></a>Dostępne regiony

Warstwa obliczeniowa bezserwerowa jest dostępna na całym świecie z wyjątkiem następujących regionów: Chiny wschodnie, Chiny Północne, Niemcy Środkowe, Niemcy północno-wschodnie, Północ Wielkiej Brytanii, Wielka Brytania Południowa 2, Zachodnio-środkowe stany USA i Us Gov Central (Iowa).

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć, zobacz [Szybki start: Tworzenie pojedynczej bazy danych w bazie danych SQL azure przy użyciu witryny Azure portal](sql-database-single-database-get-started.md).
- Aby zapoznać się z limitami zasobów zasobów, zobacz [Limity zasobów warstwy obliczeniowej bez użycia serwera](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5).
