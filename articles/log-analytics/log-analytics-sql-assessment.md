---
title: Optymalizuj środowisko programu SQL Server z Azure Log Analytics | Dokumentacja firmy Microsoft
description: Z usługi Analiza dzienników Azure rozwiązania SQL sprawdzania kondycji służy do oceny ryzyka i kondycji środowisk w regularnych odstępach czasu.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: e297eb57-1718-4cfe-a241-b9e84b2c42ac
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 47f912932f9a0e1383ef5f82d1d3e33b5ababa9c
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="optimize-your-sql-environment-with-the-sql-server-health-check-solution-in-log-analytics"></a>Optymalizuj środowisko SQL z rozwiązaniem sprawdzania kondycji serwera SQL w analizy dzienników

![Sprawdzanie kondycji SQL symbolu](./media/log-analytics-sql-assessment/sql-assessment-symbol.png)

Sprawdzanie kondycji SQL rozwiązania służy do oceny ryzyka i kondycji środowisk serwera w regularnych odstępach czasu. Ten artykuł pomoże Ci zainstalować rozwiązania, dzięki czemu można wykonać działania naprawcze potencjalnych problemów.

To rozwiązanie zapewnia priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożone. Zalecenia są podzielone na sześć obszarów fokus, które pomagają w szybkim świadomość ryzyka i podjęcia działań naprawczych.

Zalecenia są oparte na wiedzy i doświadczenia przez pracownicy firmy Microsoft z tysięcy wizyt klienta. Każde zalecenie znajdują się wskazówki dotyczące przyczyny problemu może być uwzględniana i implementowania sugerowane zmiany.

Można wybrać obszarach zainteresowań, które są najbardziej ważne dla organizacji i śledzić postęp w kierunku uruchomionym środowiskiem ryzyka bezpłatne i działa prawidłowo.

Po dodaniu rozwiązania i ocenę jest zakończone, podsumowanie informacji o obszarach zainteresowań jest wyświetlany na **SQL kondycji Sprawdź** pulpitu nawigacyjnego dla infrastruktury w danym środowisku. W poniższych sekcjach opisano sposób użyć informacji na temat **SQL kondycji Sprawdź** pulpitu nawigacyjnego, gdzie można przeglądać i wykonaj zalecane akcje dotyczące infrastruktury programu SQL Server.

![Obraz sprawdzania kondycji SQL kafelka](./media/log-analytics-sql-assessment/sql-healthcheck-summary-tile.png)

![Obraz sprawdzania kondycji SQL pulpitu nawigacyjnego](./media/log-analytics-sql-assessment/sql-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Wymagania wstępne

* Sprawdzanie kondycji SQL rozwiązania wymaga obsługiwanej wersji programu .NET Framework 4 zainstalowane na każdym komputerze, który ma Microsoft Monitoring Agent (MMA) zainstalowany.  MMA agent jest używany przez System Center 2016 - Operations Manager i Operations Manager 2012 R2 i usługą analizy dzienników.  
* Rozwiązanie obsługuje programu SQL Server 2012, 2014 oraz 2016.
* Obszar roboczy analizy dzienników można dodać rozwiązanie SQL sprawdzania kondycji z poziomu portalu Azure marketplace w portalu Azure.  Aby można było zainstalować rozwiązania, musi być administratorem lub współautora w subskrypcji platformy Azure.

  > [!NOTE]
  > Po dodaniu rozwiązania, plik AdvisorAssessment.exe jest dodawany do serwerów z agentami. Dane konfiguracji jest do odczytu i następnie wysyłane do analizy dzienników usługi w chmurze do przetwarzania. Logika jest stosowany do odebranych danych i usługi w chmurze rejestruje dane.
  >
  >

Aby przeprowadzić sprawdzenie kondycji na serwerach programu SQL Server, wymagają one agenta i łączności do analizy dzienników przy użyciu jednej z następujących obsługiwanych metod:

1. Zainstaluj [Microsoft Monitoring Agent (MMA)](log-analytics-windows-agent.md) Jeśli serwer nie jest już monitorowany przez System Center 2016 - Operations Manager lub programu Operations Manager 2012 R2.
2. Jeśli grupa zarządzania nie jest zintegrowany z usługą analizy dzienników jest monitorowany z System Center 2016 - Operations Manager lub programu Operations Manager 2012 R2, serwer może być wieloadresowego z analizy dzienników do zbierania danych i przekazywać je do usługi i jest nadal monitorowane przez program Operations Manager.  
3. W przeciwnym razie, jeśli grupę zarządzania programu Operations Manager jest zintegrowany z usługą, należy dodać kontrolerów domeny na potrzeby zbierania danych przez usługę następujące kroki w obszarze [dodać komputery zarządzane z wykorzystaniem agentów](log-analytics-om-agents.md#connecting-operations-manager-to-log-analytics) po włączeniu rozwiązanie w obszarze roboczym.  

Agent na serwerze SQL, które raporty z grupą zarządzania programu Operations Manager umożliwia zbieranie danych, przekazuje do serwera zarządzania w przypisanej, a następnie wysyłane bezpośrednio z serwerem zarządzania z usługą analizy dzienników.  Dane nie są zapisywane do baz danych programu Operations Manager.  

Jeśli serwer SQL jest monitorowane przez program Operations Manager, należy skonfigurować konto Operations Manager uruchom jako. Zobacz [konta programu Operations Manager, Uruchom jako dla analizy dzienników](#operations-manager-run-as-accounts-for-log-analytics) poniżej Aby uzyskać więcej informacji.

## <a name="sql-health-check-data-collection-details"></a>Sprawdzanie kondycji SQL szczegóły zbierania danych
Sprawdzanie kondycji SQL zbiera dane z następujących źródeł przy użyciu agenta, która została włączona:

* Instrumentacja zarządzania Windows (WMI)
* Rejestr
* Liczniki wydajności
* Wyniki widoku dynamicznego zarządzania programu SQL Server

Dane są zbierane w programie SQL Server i przekazywane do analizy dzienników co siedem dni.

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Uruchom jako konta programu Operations Manager dla analizy dzienników
Analiza dzienników używa grupy zarządzania i agent programu Operations Manager w celu wysyłają i zbierają dane z usługą analizy dzienników. Kompilacje analizy dziennika na pakiety administracyjne dla obciążeń w celu zapewnienia wartość — Dodawanie usług. Poszczególnych obciążeń wymaga specyficznego dla obciążenia uprawnień do uruchamiania pakietów administracyjnych w innym kontekście zabezpieczeń, takich jak konta użytkownika domeny. Musisz podać poświadczenia informacji przez skonfigurowanie konta Operations Manager uruchom jako.

Skorzystaj z poniższych informacji, można ustawić konta Operations Manager uruchom jako dla SQL sprawdzania kondycji.

### <a name="set-the-run-as-account-for-sql-health-check"></a>Ustaw konto Uruchom jako dla sprawdzania kondycji SQL
 Jeśli korzystasz już z pakietu administracyjnego programu SQL Server, należy użyć tego konfiguracja Uruchom jako.

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>Aby skonfigurować konto Uruchom jako SQL w konsoli operacje
> [!NOTE]
> Domyślnie przepływy pracy w pakiecie administracyjnym jest uruchamiany w kontekście zabezpieczeń konta systemu lokalnego. Jeśli używasz podłączone bezpośrednio do usługi Microsoft Monitoring Agent zamiast raportowania bezpośrednio do grupy zarządzania programu Operations Manager, pomiń kroki 1 – 5 poniżej i uruchomić T-SQL lub programu PowerShell, określając NT AUTHORITY\SYSTEM jako Nazwa użytkownika.
>
>

1. W programie Operations Manager, otwórz konsolę operacje, a następnie kliknij przycisk **administracji**.
2. W obszarze **Konfiguracja Uruchom jako**, kliknij przycisk **profile**i Otwórz **OMS SQL oceny profilu Uruchom jako**.
3. Na **konta Uruchom jako** kliknij przycisk **Dodaj**.
4. Wybierz konto systemu Windows uruchom jako, które zawiera poświadczenia wymagane dla programu SQL Server, lub kliknij przycisk **nowy** go utworzyć.

   > [!NOTE]
   > Typ konta Uruchom jako musi być systemu Windows. Konta Uruchom jako musi także być częścią lokalnej grupy administratorów na wszystkich serwerach systemu Windows obsługującego wystąpienia programu SQL Server.
   >
   >
5. Kliknij pozycję **Zapisz**.
6. Modyfikuj, a następnie wykonaj w poniższym przykładzie T-SQL w każdym wystąpieniu programu SQL Server, można przyznać minimalne uprawnienia wymagane dla konta Uruchom jako można przeprowadzić sprawdzenia kondycji. Jednak nie należy to zrobić, jeśli konto Uruchom jako jest już częścią roli serwera sysadmin w wystąpieniach programu SQL Server.

```
    ---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master

    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS

    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]

    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```

#### <a name="to-configure-the-sql-run-as-account-using-windows-powershell"></a>Aby skonfigurować konto Uruchom jako SQL przy użyciu programu Windows PowerShell
Otwórz okno programu PowerShell i uruchom następujący skrypt po zaktualizowaniu z informacjami:

```
    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"

    $profile = Get-SCOMRunAsProfile -DisplayName "OMS SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## <a name="understanding-how-recommendations-are-prioritized"></a>Opis sposobu mają pierwszeństwo zalecenia
Każdy zalecenie wprowadzone otrzymuje wartość wagi, która identyfikuje względnego zalecenia. 10 najważniejszych zalecenia są wyświetlane.

### <a name="how-weights-are-calculated"></a>Jak są obliczane wagi
Wagi są wartości zagregowanych oparte na trzech kluczowych czynników:

* *Prawdopodobieństwo* czy problemu może powodować problemy. Większe prawdopodobieństwo równa większych ogólny wynik dla zalecenia.
* *Wpływ* problemu w Twojej organizacji, jeśli spowodować problem. Większy wpływ równa większych ogólny wynik dla zalecenia.
* *Nakładu* wymagane do wykonania zalecenia. Wyższy nakładu równa mniejszych ogólny wynik dla zalecenia.

Wagi dla każde zalecenie jest wyrażona jako procent całkowitej wynik dostępne dla każdego obszaru fokus. Na przykład jeśli zalecenia w obszarze fokus zabezpieczeń i zgodności ma wynik % 5, wdrażanie tego zalecenia spowoduje zwiększenie ogólnej % wynik przez 5 zabezpieczeń i zgodności.

### <a name="focus-areas"></a>Obszarach zainteresowań
**Zabezpieczeń i zgodności** — w tym obszarze fokus zawiera zalecenia dotyczące potencjalnego zagrożenia bezpieczeństwa i naruszeń, zasad firmowych i wymagania techniczne, prawne i przepisami zgodności.

**Dostępność i ciągłość prowadzenia działalności biznesowej** — w tym obszarze fokus zawiera zalecenia dotyczące odporności infrastruktury i ochronę biznesowej, dostępności usług.

**Wydajność i skalowalność** — w tym obszarze fokus zawiera zalecenia ułatwiające organizacji infrastruktury IT zwiększania, upewnij się, że spełnia bieżące wymagania dotyczące wydajności środowiska informatycznego i jest w stanie reagować na zmieniające się infrastruktury wymagania związane z.

**Uaktualniania, wdrażania i migracji** — w tym obszarze fokus zawiera zalecenia ułatwiające uaktualniania, migracji i wdrożenia programu SQL Server w istniejącej infrastrukturze.

**Operacje i monitorowanie** — w tym obszarze fokus zawiera zalecenia ułatwiające usprawnić operacji IT, wdrożenia program prewencyjnej konserwacji i zmaksymalizować wydajność.

**Zarządzanie zmianami i konfiguracją** — w tym obszarze fokus zawiera zalecenia dotyczące ochrony codziennej pracy, należy zapewnić, że zmiany nie mieć negatywny wpływ na infrastrukturę, ustanowić procedury sterowania zmianami oraz do śledzenia i inspekcji konfiguracje systemu.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Należy dążyć wynik 100% w każdym obszarze fokus?
Niekoniecznie. Zalecenia są oparte na wiedzy i doświadczeń zdobytych Microsoft inżynierowie między tysięcy wizyt klienta. Jednak nie infrastruktury serwerowe są takie same i szczegółowe zalecenia mogą być bardziej lub mniej istotne dla Ciebie. Na przykład niektóre zalecenia dotyczące zabezpieczeń może być mniej ważne, jeśli maszyny wirtualne nie są widoczne w Internecie. Kilka zaleceń dostępności może być mniej istotne dla usług, które umożliwiają zbieranie danych ad hoc — niski priorytet i raportowania. Problemy, które są ważne dla dorosłych firm może być mniej ważne do rozruchu. Można zidentyfikować obszary fokus, które mają zebranych i przyjrzyj się jak zmienić wyniki wraz z upływem czasu.

Każdy zalecenie obejmuje wskazówek dotyczących Dlaczego ważne jest. Do oceny, czy wdrażanie zalecenie jest odpowiednie dla Ciebie, biorąc pod uwagę rodzaj usług informatycznych i potrzeb biznesowych w organizacji, należy skorzystać z poniższych wskazówek.

## <a name="use-health-check-focus-area-recommendations"></a>Użyj kondycji Sprawdź zalecenia obszaru fokus
Zanim użyjesz rozwiązanie do oceny w analizy dzienników, musi mieć zainstalowane oprogramowanie.  Po zakończeniu instalacji można wyświetlić podsumowanie zaleceń za pomocą kafelka SQL kondycji Sprawdź na stronie rozwiązania w portalu Azure.

Wyświetl oceny zgodności podsumowania dla Twojej infrastruktury, a następnie wejdź do zalecenia.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Aby wyświetlić zalecenia dla obszaru fokus i podjąć działania naprawcze
1. Zaloguj się do witryny Azure Portal na stronie [https://portal.azure.com](https://portal.azure.com).
2. W witrynie Azure Portal kliknij pozycję **Więcej usług** w lewym dolnym rogu. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Log Analytics**.
3. W okienku subskrypcje analizy dzienników, wybierz obszar roboczy, a następnie kliknij przycisk **omówienie** kafelka.  
4. Na **omówienie** kliknij przycisk **SQL kondycji Sprawdź** kafelka.
5. Na **sprawdzania kondycji** strony, sprawdź informacje w jednym z bloków obszaru fokus, a następnie kliknij przycisk jedna, aby wyświetlić zalecenia dla tego obszaru fokus.
6. Na wszystkich stronach obszaru fokus można wyświetlić priorytetową zalecenia dla danego środowiska. Kliknij zalecenie, w obszarze **dotyczy obiektów** Aby wyświetlić szczegóły dotyczące Dlaczego tworzone są zalecenia.<br><br> ![Obraz sprawdzania kondycji SQL zalecenia](./media/log-analytics-sql-assessment/sql-healthcheck-dashboard-02.png)<br>
7. Należy wykonać działania naprawcze sugerowane w **sugerowanych akcji**. Jeśli element został rozwiązany, ocen nowsze zarejestruje które zalecane akcje zostały pobrane i zwiększa wynik zgodności. Poprawione elementy są wyświetlane jako **przekazany obiektów**.

## <a name="ignore-recommendations"></a>Ignoruj zalecenia
Jeśli masz zaleceń, które chcesz zignorować, można utworzyć pliku tekstowego, który będzie użyć analizy dzienników, aby zapobiec zaleceń znajdujących się w wynikach oceny.

[!INCLUDE[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Aby zidentyfikować zaleceń, które będzie ignorować
1. W portalu Azure na stronie obszaru roboczego analizy dzienników dla Twojego wybranego obszaru roboczego kliknij **wyszukiwania dziennika** kafelka.
2. Użyj następującego zapytania do listy zaleceń, które nie powiodły, na komputerach w danym środowisku.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Jeśli obszaru roboczego został uaktualniony do [języka zapytań nowe analizy dzienników](log-analytics-log-search-upgrade.md), a następnie zmienić powyższym zapytaniu następujące.
    >
    > `SQLAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    Poniżej przedstawiono zrzut ekranu przedstawiający zapytania wyszukiwania dziennika:<br><br> ![zalecenia nie powiodło się](./media/log-analytics-sql-assessment/sql-assess-failed-recommendations.png)<br>

3. Wybierz zaleceń, które chcesz zignorować. Użyjesz wartości RecommendationId w następnej procedurze.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Tworzenie i używanie IgnoreRecommendations.txt pliku tekstowego
1. Utwórz plik o nazwie IgnoreRecommendations.txt.
2. Wklej lub wpisz każdego RecommendationId dla każde zalecenie, które mają analizy dzienników można zignorować w osobnym wierszu, a następnie zapisz i zamknij plik.
3. Umieść plik w następującym folderze na każdym komputerze, w którym ma analizy dzienników zignorowanie zalecenia.
   * Na komputerach z usługą Microsoft Monitoring Agent (połączony bezpośrednio lub za pośrednictwem programu Operations Manager) - *SystemDrive*: \Program Files\Microsoft Agent\Agent monitorowania
   * Na serwerze zarządzania programu Operations Manager — *SystemDrive*: System Center 2012 R2\Operations Manager\Server \Program Files\Microsoft
   * Na serwerze zarządzania programu Operations Manager 2016 - *SystemDrive*: \Program Files\Microsoft Manager\Server 2016\Operations programu System Center

### <a name="to-verify-that-recommendations-are-ignored"></a>Aby zweryfikować, że zalecenia są ignorowane
1. Po na następną zaplanowaną ocenę działa, domyślnie co 7 dni, określonego zalecenia są oznaczane ignorowany i nie będą wyświetlane na pulpicie nawigacyjnym oceny.
2. Następujące zapytania wyszukiwania dziennika służy do tworzenia listy wszystkich zignorowane zalecenia.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Ignored | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Jeśli obszaru roboczego został uaktualniony do [języka zapytań nowe analizy dzienników](log-analytics-log-search-upgrade.md), a następnie zmienić powyższym zapytaniu następujące.
    >
    > `SQLAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

3. Jeśli później zdecydujesz chcesz zobaczyć zignorowane zalecenia dotyczące, Usuń wszystkie pliki IgnoreRecommendations.txt lub RecommendationIDs można usunąć z nich.

## <a name="sql-health-check-solution-faq"></a>Sprawdzanie kondycji SQL rozwiązania — często zadawane pytania
*Częstotliwość sprawdzania kondycji czy działa?*

* Test jest uruchamiany co siedem dni.

*Czy istnieje sposób konfigurowania, jak często kontrola nie zostanie uruchomiona?*

* Nie w tej chwili.

*Jeśli inny serwer zostanie odnaleziony po rozwiązania sprawdzania kondycji SQL zostały dodane, będzie on sprawdzany?*

* Tak, gdy okaże się, że jest zaznaczona z następnie, co siedem dni.

*Jeśli serwer jest likwidowany, gdy zostanie ono zostać usunięte z sprawdzenie kondycji?*

* Jeśli serwer nie przedstawi danych do 3 tygodni, zostanie ono usunięte.

*Co to jest nazwa procesu, który wykonuje zbierania danych?*

* AdvisorAssessment.exe

*Jak długo trwa do zebrania danych?*

* Kolekcja rzeczywistymi danymi na serwerze trwa około 1 godziny. Może trwać dłużej na serwerach, które mają wiele wystąpień programu SQL lub bazy danych.

*Jakiego typu dane są zbierane?*

* Zbierane są następujące typy danych:
  * WMI
  * Rejestr
  * Liczniki wydajności
  * SQL dynamicznych widoków zarządzania (DMV).

*Czy istnieje sposób konfigurowania, gdy dane są zbierane?*

* Nie w tej chwili.

*Dlaczego trzeba skonfigurować konto Uruchom jako?*

* Dla programu SQL Server są uruchamiane niewielkiej liczby zapytania SQL. Aby je uruchomić należy użyć konto Uruchom jako z uprawnieniami VIEW SERVER STATE do bazy danych SQL.  Ponadto aby można było wykonać kwerendę usługi WMI, wymagane są poświadczenia administratora lokalnego.

*Dlaczego są wyświetlane tylko zalecenia 10 pierwszych?*

* Zamiast daje utrudnione kompletnej zadań, zaleca się skupić się na najpierw adresowania priorytetową zalecenia. Po adresu im dodatkowe zalecenia staną się dostępne. Jeśli wolisz wyświetlić listę szczegółowych, można wyświetlić wszystkie zalecenia, używając przycisku Wyszukaj dziennika analizy dzienników.

*Czy istnieje sposób, aby zignorować zalecenie?*

* Tak, zobacz [zignorowanie zalecenia](#ignore-recommendations) powyższej sekcji.

## <a name="next-steps"></a>Kolejne kroki
* [Wyszukaj dzienniki](log-analytics-log-searches.md) informacje na temat analizowania szczegółowych danych SQL sprawdzania kondycji i zaleceń.
