---
title: Optymalizuj swoje środowisko programu SQL Server za pomocą usługi Azure Monitor | Dokumenty firmy Microsoft
description: Za pomocą usługi Azure Monitor można użyć rozwiązania SQL Health Check do oceny ryzyka i kondycji środowisk w regularnych odstępach czasu.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/28/2019
ms.openlocfilehash: ceaed0800df01bf2c44fee13d98b01b6e726200d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662488"
---
# <a name="optimize-your-sql-environment-with-the-sql-server-health-check-solution-in-azure-monitor"></a>Optymalizuj swoje środowisko SQL za pomocą rozwiązania SQL Server Health Check w usłudze Azure Monitor

![Symbol sprawdzania kondycji SQL](./media/sql-assessment/sql-assessment-symbol.png)

Za pomocą rozwiązania SQL Health Check można ocenić ryzyko i kondycję środowisk serwera w regularnych odstępach czasu. Ten artykuł pomoże Ci zainstalować rozwiązanie, dzięki czemu można podjąć działania naprawcze w przypadku potencjalnych problemów.

To rozwiązanie zawiera priorytetową listę zaleceń specyficznych dla wdrożonej infrastruktury serwera. Zalecenia są podzielone na sześć obszarów ostrości, które pomagają szybko zrozumieć ryzyko i podjąć działania naprawcze.

Zalecenia są oparte na wiedzy i doświadczeniu zdobytym przez inżynierów firmy Microsoft podczas tysięcy wizyt klientów. Każde zalecenie zawiera wskazówki dotyczące przyczyn, które mogą mieć znaczenie dla Ciebie i jak zaimplementować sugerowane zmiany.

Możesz wybrać obszary fokusu, które są najważniejsze dla Twojej organizacji i śledzić swoje postępy w prowadzeniu środowiska wolnego od ryzyka i zdrowego.

Po dodaniu rozwiązania i zakończeniu oceny informacje podsumowujące dla obszarów fokusu są wyświetlane na pulpicie nawigacyjnym **sql health check** dla infrastruktury w twoim środowisku. W poniższych sekcjach opisano sposób używania informacji na pulpicie nawigacyjnym **sprawdzania kondycji SQL,** na którym można wyświetlać, a następnie podejmować zalecane akcje dla infrastruktury programu SQL Server.

![obraz kafelka SQL Health Check](./media/sql-assessment/sql-healthcheck-summary-tile.png)

![obraz pulpitu nawigacyjnego SQL Health Check](./media/sql-assessment/sql-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Wymagania wstępne

* Rozwiązanie sql health check wymaga obsługiwanej wersji programu .NET Framework 4.6.2 zainstalowanej na każdym komputerze z zainstalowanym programem Microsoft Monitoring Agent (MMA).  Agent MMA jest używany przez System Center 2016 — Menedżer operacji i program Operations Manager 2012 R2 i azure monitor.  
* Rozwiązanie obsługuje program SQL Server w wersji 2012, 2014 i 2016.
* Obszar roboczy usługi Log Analytics, aby dodać rozwiązanie SQL Health Check z portalu Azure w portalu Azure.  Aby zainstalować rozwiązanie, musisz być administratorem lub współautorem w subskrypcji platformy Azure.

  > [!NOTE]
  > Po dodaniu rozwiązania plik AdvisorAssessment.exe jest dodawany do serwerów z agentami. Dane konfiguracji są odczytywane, a następnie wysyłane do usługi Azure Monitor w chmurze do przetwarzania. Logika jest stosowana do odebranych danych, a usługa w chmurze rejestruje dane.
  >
  >

Aby przeprowadzić sprawdzanie kondycji serwerów programu SQL Server, wymagają one agenta i łączności z usługą Azure Monitor przy użyciu jednej z następujących obsługiwanych metod:

1. Zainstaluj [agenta monitorowania firmy Microsoft (MMA),](../../azure-monitor/platform/agent-windows.md) jeśli serwer nie jest jeszcze monitorowany przez system Center 2016 — Program Operations Manager lub Program Operations Manager 2012 R2.
2. Jeśli jest monitorowany za pomocą programu System Center 2016 — Program Operations Manager lub Operations Manager 2012 R2, a grupa zarządzania nie jest zintegrowana z usługą Azure Monitor, serwer może być multi-homed z usługą Log Analytics do zbierania danych i przesyłania dalej do usługi i nadal być monitorowane przez operations managera.  
3. W przeciwnym razie jeśli grupa zarządzania programu Operations Manager jest zintegrowana z usługą, należy dodać kontrolery domeny do zbierania danych przez usługę, wykonując kroki opisane w obszarze [dodawanie komputerów zarządzanych przez agenta](../../azure-monitor/platform/om-agents.md#connecting-operations-manager-to-azure-monitor) po włączeniu rozwiązania w obszarze roboczym.  

Agent w programie SQL Server, który raportuje grupę zarządzania programu Operations Manager, zbiera dane, przekazuje do przypisanego mu serwera zarządzania, a następnie jest wysyłany bezpośrednio z serwera zarządzania do usługi Azure Monitor.  Dane nie są zapisywane w bazach danych programu Operations Manager.  

Jeśli program SQL Server jest monitorowany przez program Operations Manager, należy skonfigurować konto uruchom jako program Operations Manager. Zobacz [konta uruchamiane programu Operations Manager dla usługi Azure Monitor](#operations-manager-run-as-accounts-for-log-analytics) poniżej, aby uzyskać więcej informacji.

## <a name="sql-health-check-data-collection-details"></a>Szczegóły zbierania danych w rozwiązaniu SQL Health Check
Sql Health Check zbiera dane z następujących źródeł przy użyciu włączonego agenta:

* Instrumentacja zarządzania Windows (WMI)
* Rejestr
* Liczniki wydajności
* Wyniki dynamicznego widoku zarządzania programu SQL Server

Dane są zbierane na serwerze SQL Server i przekazywane do usługi Log Analytics co siedem dni.

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Konta Uruchom jako programu Operations Manager dla usługi Log Analytics
Usługa Log Analytics używa agenta programu Operations Manager i grupy zarządzania do zbierania i wysyłania danych do usługi usługi Usługi analizy dzienników. Usługa Log Analytics opiera się na pakietach administracyjnych dla obciążeń w celu świadczenia usług o wartości dodanej. Każde obciążenie wymaga uprawnień specyficznych dla obciążenia, aby uruchamiać pakiety administracyjne w innym kontekście zabezpieczeń, takim jak konto użytkownika domeny. Należy podać informacje o poświadczeniach, konfigurując konto Uruchom jako program Operations Manager.

Użyj następujących informacji, aby ustawić konto Uruchom jako program Operations Manager dla sprawdzania kondycji sql.

### <a name="set-the-run-as-account-for-sql-health-check"></a>Ustawianie konta Uruchom jako dla rozwiązania SQL Health Check
 Jeśli używasz już pakietu administracyjnego programu SQL Server, należy użyć tej konfiguracji Uruchom jako.

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>Aby skonfigurować konto SQL Uruchom jako w konsoli Operacje
> [!NOTE]
> Domyślnie przepływy pracy w pakiecie administracyjnym są uruchamiane w kontekście zabezpieczeń konta system lokalny. Jeśli używasz agenta monitorowania firmy Microsoft połączonego bezpośrednio z usługą, a nie raportowania bezpośrednio do grupy zarządzania programu Operations Manager, pomiń kroki 1-5 poniżej i uruchom próbkę T-SQL lub PowerShell, określając NT AUTHORITY\SYSTEM jako nazwę użytkownika.
>
>

1. W programie Operations Manager otwórz konsolę Operacje, a następnie kliknij pozycję **Administracja**.
2. W obszarze **Uruchom jako konfigurację**kliknij pozycję **Profile**i otwórz program SQL Assessment Run **As Profile**.
3. Na stronie **Konta Uruchom jako** kliknij pozycję **Dodaj**.
4. Wybierz konto Uruchom jako system Windows, które zawiera poświadczenia potrzebne dla programu SQL Server, lub kliknij przycisk **Nowy,** aby je utworzyć.

   > [!NOTE]
   > Typem konta Uruchom jako musi być system Windows. Konto Uruchom jako musi również należeć do grupy Administratorzy lokalni na wszystkich serwerach systemu Windows obsługujących wystąpienia programu SQL Server.
   >
   >
5. Kliknij przycisk **Zapisz**.
6. Zmodyfikuj, a następnie wykonaj następujący przykład T-SQL w każdym wystąpieniu programu SQL Server, aby udzielić minimalnych uprawnień wymaganych dla konta Uruchom jako do wykonania sprawdzenia kondycji. Jednak nie trzeba tego robić, jeśli uruchom jako konto jest już częścią roli serwera sysadmin w wystąpieniach programu SQL Server.

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

#### <a name="to-configure-the-sql-run-as-account-using-windows-powershell"></a>Aby skonfigurować konto SQL Uruchom jako przy użyciu programu Windows PowerShell
Otwórz okno programu PowerShell i uruchom następujący skrypt po zaktualizowaniu go o informacje:

```
    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"

    $profile = Get-SCOMRunAsProfile -DisplayName "SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## <a name="understanding-how-recommendations-are-prioritized"></a>Opis priorytetyzacji zaleceń
Każde wydane zalecenie otrzymuje wartość ważącą, która określa względne znaczenie zalecenia. Pokazano tylko dziesięć najważniejszych zaleceń.

### <a name="how-weights-are-calculated"></a>Sposób obliczania wag
Współczynniki kory'u są wartościami zagregowanymi na podstawie trzech kluczowych czynników:

* *Prawdopodobieństwo,* że zidentyfikowany problem spowoduje problemy. Większe prawdopodobieństwo równa się większe ogólny wynik dla zalecenia.
* *Wpływ* problemu na organizację, jeśli powoduje to problem. Większy wpływ oznacza większy ogólny wynik dla zalecenia.
* *Wysiłek* niezbędny do wdrożenia zalecenia. Większy wysiłek oznacza mniejszy ogólny wynik zalecenia.

Waga dla każdego zalecenia jest wyrażona jako procent całkowitego wyniku dostępnego dla każdego obszaru ostrości. Jeśli na przykład zalecenie w obszarze fokus bezpieczeństwa i zgodności ma wynik 5%, wdrożenie tego zalecenia zwiększy ogólny wynik zabezpieczeń i zgodności o 5%.

### <a name="focus-areas"></a>Obszary fokusu
**Bezpieczeństwo i zgodność** — ten obszar ostrości zawiera zalecenia dotyczące potencjalnych zagrożeń i naruszeń zabezpieczeń, zasad firmowych oraz wymagań technicznych, prawnych i regulacyjnych dotyczących zgodności.

**Dostępność i ciągłość działania** — ten obszar ostrości zawiera zalecenia dotyczące dostępności usług, odporności infrastruktury i ochrony biznesowej.

**Wydajność i skalowalność** — ten obszar ostrości zawiera zalecenia, które pomogą w rozwoju infrastruktury IT w organizacji, upewnią się, że środowisko IT spełnia bieżące wymagania dotyczące wydajności i jest w stanie reagować na zmieniające się potrzeby infrastrukturalne.

**Uaktualnianie, migracja i wdrażanie** — w tym obszarze fokusu przedstawiono zalecenia ułatwiające uaktualnianie, migrację i wdrażanie programu SQL Server w istniejącej infrastrukturze.

**Operacje i monitorowanie** — ten obszar ostrości zawiera zalecenia ułatwiające usprawnienie operacji IT, wdrożenie konserwacji zapobiegawczej i maksymalizację wydajności.

**Zarządzanie zmianami i konfiguracją** — ten obszar fokusu zawiera zalecenia mające na celu ochronę codziennych operacji, zapewnienie, że zmiany nie wpływają negatywnie na infrastrukturę, ustanawia procedury kontroli zmian oraz śledzenie i inspekcję konfiguracji systemu.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Czy należy dążyć do osiągnięcia wyniku 100% w każdym obszarze fokusu?
Niekoniecznie. Zalecenia są oparte na wiedzy i doświadczeniach zdobytych przez inżynierów firmy Microsoft podczas tysięcy wizyt klientów. Jednak nie dwie infrastruktury serwera są takie same, a konkretne zalecenia mogą być mniej lub bardziej istotne dla Ciebie. Na przykład niektóre zalecenia dotyczące zabezpieczeń mogą być mniej istotne, jeśli maszyny wirtualne nie są udostępniane w Internecie. Niektóre zalecenia dotyczące dostępności mogą być mniej istotne dla usług, które zapewniają gromadzenie i raportowanie danych ad hoc o niskim priorytecie. Kwestie, które są ważne dla dojrzałego biznesu, mogą być mniej ważne dla start-upu. Możesz określić, które obszary fokusu są Twoimi priorytetami, a następnie sprawdzić, jak twoje wyniki zmieniają się w czasie.

Każde zalecenie zawiera wskazówki, dlaczego jest to ważne. Należy użyć tych wskazówek, aby ocenić, czy wdrożenie zalecenia jest odpowiednie dla Ciebie, biorąc pod uwagę charakter usług IT i potrzeby biznesowe organizacji.

## <a name="use-health-check-focus-area-recommendations"></a>Korzystanie z zaleceń dotyczących obszaru fokusu kontroli kondycji
Aby można było użyć rozwiązania do oceny w usłudze Azure Monitor, należy mieć zainstalowane rozwiązanie.  Po zainstalowaniu można wyświetlić podsumowanie zaleceń przy użyciu kafelka sprawdzanie kondycji SQL na stronie **Przegląd** usługi Azure Monitor w witrynie Azure Portal.

Wyświetl podsumowane oceny zgodności dla infrastruktury, a następnie przejdź do szczegółów zaleceń.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Aby wyświetlić zalecenia dotyczące obszaru fokusu i podjąć działania naprawcze
1. Zaloguj się do witryny Azure portal w [https://portal.azure.com](https://portal.azure.com).
2. W witrynie Azure Portal kliknij pozycję **Więcej usług** w lewym dolnym rogu. Na liście zasobów wpisz **Monitor**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Monitor**.
3. W sekcji **Insights** menu wybierz pozycję **Więcej**.  
4. Na stronie **Przegląd** kliknij kafelek **Sprawdzanie kondycji sql.**
5. Na stronie **Sprawdzanie kondycji** przejrzyj informacje podsumowujące w jednym z bloków obszaru fokusu, a następnie kliknij jeden z nich, aby wyświetlić zalecenia dotyczące tego obszaru fokusu.
6. Na dowolnej stronie obszaru fokusu można wyświetlić priorytetowe zalecenia dla środowiska. Kliknij zalecenie w obszarze **Obiekty, których dotyczy problem,** aby wyświetlić szczegółowe informacje o tym, dlaczego zalecenie zostało sformułowane.<br><br> ![obraz zaleceń dotyczących sprawdzania kondycji SQL](./media/sql-assessment/sql-healthcheck-dashboard-02.png)<br>
7. W obszarze **Sugerowane działania**można podjąć działania naprawcze sugerowane . Po zajmaniu się elementem późniejsze oceny będą rejestrować, że zalecono zalecane działania, a wynik zgodności wzrośnie. Poprawione elementy są wyświetlane jako **przekazane obiekty**.

## <a name="ignore-recommendations"></a>Ignorowanie zaleceń
Jeśli masz zalecenia, które chcesz zignorować, można utworzyć plik tekstowy, który będzie używany przez usługę Azure Monitor, aby zapobiec pojawianiu się zaleceń w wynikach oceny.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Aby zidentyfikować zalecenia, które zignorujesz
1. W menu Monitor platformy Azure kliknij polecenie **Dzienniki**.
2. Poniższa kwerenda służy do listy zaleceń, które nie powiodły się dla komputerów w twoim środowisku.

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
    Oto zrzut ekranu przedstawiający kwerendę dziennika:<br><br> ![nieudane zalecenia](./media/sql-assessment/sql-assess-failed-recommendations.png)<br>

3. Wybierz zalecenia, które chcesz zignorować. Użyjesz wartości dla RecommendationId w następnej procedurze.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Aby utworzyć plik tekstowy IgnoreRecommenddations.txt i używać go
1. Utwórz plik o nazwie IgnoreRecommendations.txt.
2. Wklej lub wpisz każdy obiekt RecommendationId dla każdego zalecenia, które chcesz, aby usługa Azure Monitor zignorowała w osobnym wierszu, a następnie zapisz i zamknij plik.
3. Umieść plik w następującym folderze na każdym komputerze, na którym chcesz, aby usługa Azure Monitor ignorowała zalecenia.
   * Na komputerach z agentem microsoft monitoringu (połączonym bezpośrednio lub za pośrednictwem programu Operations Manager) — *SystemDrive*:\Program Files\Microsoft Monitoring Agent\Agent
   * Na serwerze zarządzania programu Operations Manager — *SystemDrive*:\Program Files\Microsoft System Center 2012 R2\Operations Manager\Server
   * Na serwerze zarządzania programu Operations Manager 2016 — *SystemDrive*:\Program Files\Microsoft System Center 2016\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Aby sprawdzić, czy zalecenia są ignorowane
1. Po uruchomieniu następnej zaplanowanej oceny, domyślnie co 7 dni, określone zalecenia są oznaczone jako Ignorowane i nie będą wyświetlane na pulpicie nawigacyjnym oceny.
2. Za pomocą następujących zapytań wyszukiwania dziennika można wyświetlić listę wszystkich zignorowanych zaleceń.

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
3. Jeśli później zdecydujesz, że chcesz zobaczyć zignorowane zalecenia, usuń wszystkie pliki IgnoreRecommendations.txt lub możesz usunąć z nich identyfikatory rekomendacji.

## <a name="sql-health-check-solution-faq"></a>Rozwiązanie SQL Health Check — często zadawane pytania

*Jakie kontrole są wykonywane przez rozwiązanie oceny SQL?*

* Następująca kwerenda zawiera opis wszystkich aktualnie wykonywanych kontroli:

```Kusto
SQLAssessmentRecommendation
| distinct RecommendationId, FocusArea, ActionArea, Recommendation, Description
| sort by FocusArea,ActionArea, Recommendation
```
Wyniki można następnie wyeksportować do programu Excel w celu dalszego przeglądu.


*Jak często przebiega sprawdzanie kondycji?*

* Kontrola odbywa się co siedem dni.

*Czy istnieje sposób, aby skonfigurować, jak często sprawdzane?*

* Nie w tej chwili.

*Jeśli inny serwer zostanie wykryty po dodaniu rozwiązania SQL Health Check, czy zostanie ono sprawdzone?*

* Tak, po odkryciu jest sprawdzany od tego czasu, co siedem dni.

*Jeśli serwer zostanie wycofany z eksploatacji, kiedy zostanie usunięty z kontroli kondycji?*

* Jeśli serwer nie przesyła danych przez 3 tygodnie, zostanie on usunięty.

*Jaka jest nazwa procesu, który zbiera dane?*

* DoradcaAssessment.exe

*Jak długo trwa gromadzenie danych?*

* Rzeczywiste zbieranie danych na serwerze trwa około 1 godziny. Może to potrwać dłużej na serwerach, które mają dużą liczbę wystąpień SQL lub baz danych.

*Jakiego rodzaju dane są zbierane?*

* Gromadzone są następujące typy danych:
  * WMI
  * Rejestr
  * Liczniki wydajności
  * Widoki dynamicznego zarządzania SQL (DMV).

*Czy istnieje sposób konfigurowania podczas zbierania danych?*

* Nie w tej chwili.

*Dlaczego muszę skonfigurować konto Uruchom jako konto?*

* W przypadku programu SQL Server uruchamiana jest niewielka liczba zapytań SQL. Aby można było je uruchomić, należy użyć uruchom jako konto z uprawnieniami VIEW SERVER STATE do SQL.  Ponadto w celu wykonywania zapytań w umi wymagane są poświadczenia administratora lokalnego.

*Dlaczego wyświetlać tylko 10 najlepszych rekomendacji?*

* Zamiast podawać wyczerpującą przytłaczającą listę zadań, zalecamy skupienie się najpierw na rozwiązaniu priorytetowych zaleceń. Po ich rozwiązaniu dostępne będą dodatkowe zalecenia. Jeśli wolisz zobaczyć szczegółową listę, możesz wyświetlić wszystkie zalecenia za pomocą wyszukiwania dziennika usługi Log Analytics.

*Czy istnieje sposób, aby zignorować zalecenie?*

* Tak, zobacz [Sekcję Ignoruj zalecenia](#ignore-recommendations) powyżej.

## <a name="next-steps"></a>Następne kroki
* [Rejestrowanie zapytań,](../log-query/log-query-overview.md) aby dowiedzieć się, jak analizować szczegółowe dane i zalecenia dotyczące sprawdzania kondycji sql.
