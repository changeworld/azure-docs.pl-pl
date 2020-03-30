---
title: Ocena programu System Center Operations Manager za pomocą usługi Azure Monitor
description: Za pomocą rozwiązania System Center Operations Manager Health Check można regularnie oceniać ryzyko i kondycję środowisk.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2018
ms.openlocfilehash: 94251dfa2d9fa732912ed20d825e64f542d79188
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055408"
---
# <a name="optimize-your-environment-with-the-system-center-operations-manager-health-check-preview-solution"></a>Optymalizowanie środowiska za pomocą rozwiązania System Center Operations Manager Health Check (wersja zapoznawcza)

![Symbol sprawdzania kondycji programu System Center Operations Manager](./media/scom-assessment/scom-assessment-symbol.png)

Za pomocą rozwiązania System Center Operations Manager Health Check można regularnie oceniać ryzyko i kondycję grupy zarządzania programu System Center Operations Manager. Ten artykuł ułatwia instalowanie, konfigurowanie i używanie rozwiązania, dzięki czemu można podjąć działania naprawcze w przypadku potencjalnych problemów.

To rozwiązanie zawiera priorytetową listę zaleceń specyficznych dla wdrożonej infrastruktury serwera. Zalecenia są podzielone na cztery obszary fokusowe, które pomagają szybko zrozumieć ryzyko i podjąć działania naprawcze.

Zalecenia są oparte na wiedzy i doświadczeniu zdobytym przez inżynierów firmy Microsoft podczas tysięcy wizyt klientów. Każde zalecenie zawiera wskazówki dotyczące przyczyn, które mogą mieć znaczenie dla Ciebie i jak zaimplementować sugerowane zmiany.

Możesz wybrać obszary fokusu, które są najważniejsze dla Twojej organizacji i śledzić swoje postępy w prowadzeniu środowiska wolnego od ryzyka i zdrowego.

Po dodaniu rozwiązania i wykonaniu oceny informacje podsumowujące dotyczące obszarów fokusu są wyświetlane na pulpicie nawigacyjnym **oceny kondycji programu System Center Operations Manager** dla infrastruktury. W poniższych sekcjach opisano sposób używania informacji na pulpicie nawigacyjnym programu System Center Operations Manager health **check,** na którym można wyświetlać, a następnie podejmować zalecane działania dla środowiska programu Operations Manager.

![Kafelek rozwiązania programu System Center Operations Manager](./media/scom-assessment/log-analytics-scom-healthcheck-tile.png)

![Panel kontroli kondycji programu System Center Operations Manager](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-01.png)

## <a name="installing-and-configuring-the-solution"></a>Instalowanie i konfigurowanie rozwiązania

Rozwiązanie współpracuje z programem Microsoft System Center 2012 Operations Manager z dodatkiem Service Pack 1, microsoft system center 2012 R2 Operations Manager, programem Microsoft System Center 2016 Operations Manager, programem Microsoft System Center 2016 Operations Manager i programem Microsoft System System Centrum Operations Manager 1807. Obsługiwana wersja programu .NET Framework 4.6.2 musi być zainstalowana na każdym serwerze zarządzania.

Skorzystaj z poniższych informacji, aby zainstalować i skonfigurować rozwiązanie.

- Aby można było użyć rozwiązania health check w usłudze Log Analytics, należy zainstalować rozwiązanie. Zainstaluj rozwiązanie z [witryny Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SCOMAssessmentOMS?tab=Overview).

- Po dodaniu rozwiązania do obszaru roboczego kafelek **Sprawdzanie kondycji programu System Center Operations Manager** na pulpicie nawigacyjnym wyświetla dodatkowy komunikat wymagany do konfiguracji. Kliknij kafelek i postępuj zgodnie z instrukcjami konfiguracji wymienionymi na stronie

  ![Kafelek pulpitu nawigacyjnego programu System Center Operations Manager](./media/scom-assessment/scom-configrequired-tile.png)

> [!NOTE]
> Konfigurację programu System Center Operations Manager można wykonać za pomocą skryptu, wykonując kroki wymienione na stronie konfiguracji rozwiązania w usłudze Log Analytics.

 Aby skonfigurować ocenę za pomocą konsoli Operations Manager, wykonaj poniższe czynności w następującej kolejności:
1. [Ustawianie konta Uruchom jako dla sprawdzania kondycji programu System Center Operations Manager](#operations-manager-run-as-accounts-for-log-analytics)  
2. Konfigurowanie reguły sprawdzania kondycji programu System Center Operations Manager

## <a name="system-center-operations-manager-health-check-data-collection-details"></a>Szczegóły zbierania danych w rozwiązaniu System Center Operations Manager Health Check

Rozwiązanie System Center Operations Manager Health Check zbiera dane z następujących źródeł:

* Rejestr
* Instrumentacja zarządzania Windows (WMI)
* Dziennik zdarzeń
* Dane pliku
* Bezpośrednio z programu Operations Manager przy użyciu programów PowerShell i zapytań SQL, z serwera zarządzania, który został określony.  

Dane są zbierane na serwerze zarządzania i przekazywane do usługi Log Analytics co siedem dni.  

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Konta Uruchom jako programu Operations Manager dla usługi Log Analytics

Usługa Log Analytics opiera się na pakietach administracyjnych dla obciążeń w celu świadczenia usług o wartości dodanej. Każde obciążenie wymaga uprawnień specyficznych dla obciążenia, aby uruchamiać pakiety administracyjne w innym kontekście zabezpieczeń, takim jak konto użytkownika domeny. Skonfiguruj konto Uruchom jako program Operations Manager z uprzywilejowanymi poświadczeniami. Aby uzyskać dodatkowe informacje, zobacz [Jak utworzyć konto Uruchom jako](https://technet.microsoft.com/library/hh321655(v=sc.12).aspx) w dokumentacji programu Operations Manager.

Poniższe informacje można użyć, aby ustawić konto Uruchom jako dla programu System Center Operations Manager Health Check.

### <a name="set-the-run-as-account"></a>Ustawianie konta Uruchom jako

Konto Uruchom jako musi spełniać następujące wymagania przed kontynuowaniem:

* Konto użytkownika domeny, które jest członkiem lokalnej grupy Administratorzy na wszystkich serwerach obsługujących dowolną rolę programu Operations Manager — serwer zarządzania, program SQL Server obsługujący operacyjną, magazyn danych i bazę danych ACS, raportowanie, konsolę sieci Web i serwer bramy.
* Rola administratora menedżera operacji dla ocenianej grupy zarządzania
* Jeśli konto nie ma praw do sysadmin SQL, wykonaj [skrypt,](#sql-script-to-grant-granular-permissions-to-the-run-as-account) aby udzielić szczegółowych uprawnień do konta w każdym wystąpieniu programu SQL Server obsługującym jedną lub wszystkie bazy danych programu Operations Manager.

1. W konsoli programu Operations Manager wybierz przycisk nawigacji **administracyjnej.**
2. W obszarze **Uruchom jako konfigurację**kliknij pozycję **Konta**.
3. W Kreatorze **tworzenia uruchom jako konto** na stronie **Wprowadzenie** kliknij przycisk **Dalej**.
4. Na stronie **Właściwości ogólne** wybierz pozycję **Windows** na liście Uruchom **jako konto:** lista.
5. Wpisz nazwę wyświetlaną w polu tekstowym **Nazwa wyświetlana** i opcjonalnie wpisz opis w polu **Opis,** a następnie kliknij przycisk **Dalej**.
6. Na stronie **Zabezpieczenia dystrybucji** wybierz pozycję **Bezpieczniejsze**.
7. Kliknij przycisk **Utwórz**.  

Teraz, gdy konto Uruchom jako jest tworzony, musi kierować serwery zarządzania w grupie zarządzania i skojarzone ze wstępnie zdefiniowanym profilu Uruchom jako więc przepływy pracy będą uruchamiane przy użyciu poświadczeń.  

1. W obszarze **Uruchom jako konfigurację** **, Konta**, w okienku wyników kliknij dwukrotnie utworzone konto wcześniej.
2. Na karcie **Dystrybucja** kliknij pozycję **Dodaj** dla **wybranych komputerów** i dodaj serwer zarządzania, do na który ma być dystrybuowane konto.  Kliknij **przycisk OK** dwa razy, aby zapisać zmiany.
3. W obszarze **Uruchom jako konfigurację**kliknij pozycję **Profile**.
4. Wyszukaj *profil oceny SCOM*.
5. Nazwa profilu powinna być następująca: *Sprawdzanie kondycji programu Microsoft System Center Operations Manager uruchom jako profil*.
6. Kliknij prawym przyciskiem myszy i zaktualizuj jego właściwości oraz dodaj ostatnio utworzone konto Uruchom jako konto utworzone wcześniej.

### <a name="sql-script-to-grant-granular-permissions-to-the-run-as-account"></a>Skrypt SQL umożliwiający przyznanie szczegółowych uprawnień do konta Uruchom jako

Wykonaj następujący skrypt SQL, aby udzielić wymaganych uprawnień do konta Uruchom jako w wystąpieniu programu SQL Server używanym przez program Operations Manager obsługujący operacyjną, magazyn danych i bazę danych ACS.

```
-- Replace <UserName> with the actual user name being used as Run As Account.
USE master

-- Create login for the user, comment this line if login is already created.
CREATE LOGIN [UserName] FROM WINDOWS


--GRANT permissions to user.
GRANT VIEW SERVER STATE TO [UserName]
GRANT VIEW ANY DEFINITION TO [UserName]
GRANT VIEW ANY DATABASE TO [UserName]

-- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
-- NOTE: This command must be run anytime new databases are added to SQL Server instances.
EXEC sp_msforeachdb N'USE [?]; CREATE USER [UserName] FOR LOGIN [UserName];'

Use msdb
GRANT SELECT To [UserName]
Go

--Give SELECT permission on all Operations Manager related Databases

--Replace the Operations Manager database name with the one in your environment
Use [OperationsManager];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager DatawareHouse database name with the one in your environment
Use [OperationsManagerDW];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager Audit Collection database name with the one in your environment
Use [OperationsManagerAC];
GRANT SELECT To [UserName]
GO

--Give db_owner on [OperationsManager] DB
--Replace the Operations Manager database name with the one in your environment
USE [OperationsManager]
GO
ALTER ROLE [db_owner] ADD MEMBER [UserName]

```

### <a name="configure-the-health-check-rule"></a>Konfigurowanie reguły kontroli kondycji

Pakiet administracyjny rozwiązania System Center Operations Manager Health Check zawiera regułę o nazwie *Reguła sprawdzania kondycji uruchamiania*programu Microsoft System Center Operations Manager . Ta reguła jest odpowiedzialna za uruchomienie kontroli kondycji. Aby włączyć regułę i skonfigurować częstotliwość, należy użyć poniższych procedur.

Domyślnie reguła sprawdzania kondycji uruchamiania programu Microsoft System Center Operations Manager jest wyłączona. Aby uruchomić sprawdzanie kondycji, należy włączyć regułę na serwerze zarządzania. Wykonaj następujące kroki.

#### <a name="enable-the-rule-for-a-specific-management-server"></a>Włączanie reguły dla określonego serwera zarządzania

1. W obszarze **roboczym Tworzenie** konsoli Operacyjna programu Operations Manager wyszukaj regułę sprawdzania kondycji programu *Microsoft System Center Operations Manager* w okienku **Reguły.**
2. W wynikach wyszukiwania wybierz tekst *Typ: Serwer zarządzania*.
3. Kliknij prawym przyciskiem myszy regułę, a następnie kliknij polecenie **Zadłuszanie** > **dla określonego obiektu klasy: Serwer zarządzania**.
4.  Na liście dostępnych serwerów zarządzania wybierz serwer zarządzania, na którym reguła ma być uruchamiana.  Powinien to być ten sam serwer zarządzania skonfigurowany wcześniej do skojarzenia konta Uruchom jako.
5.  Upewnij się, że wartość zastępowania została zmieniona na **Wartość True** dla wartości parametru **Włączone.**<br><br> ![zastępowanie parametru](./media/scom-assessment/rule.png)

    Podczas gdy w tym oknie, skonfigurować częstotliwość uruchamiania przy użyciu następnej procedury.

#### <a name="configure-the-run-frequency"></a>Konfigurowanie częstotliwości uruchamiania

Ocena jest skonfigurowana do uruchamiania co 10 080 minut (lub siedem dni) domyślnie. Można zastąpić wartość do minimalnej wartości 1440 minut (lub jeden dzień). Wartość reprezentuje minimalną różnicę czasu wymaganą między kolejnymi przebiegami oceny. Aby zastąpić interwał, użyj poniższych kroków.

1. W obszarze **roboczym Tworzenie** konsoli programu Operations Manager wyszukaj *regułę sprawdzania kondycji* programu Microsoft System Center Operations Center w sekcji **Reguły reguły** sprawdzania kondycji.
2. W wynikach wyszukiwania wybierz tekst *Typ: Serwer zarządzania*.
3. Kliknij prawym przyciskiem myszy regułę, a następnie kliknij polecenie **Zastąp regułę** > **dla wszystkich obiektów klasy: Serwer zarządzania**.
4. Zmień wartość parametru **Interval** na żądaną wartość interwału. W poniższym przykładzie wartość jest ustawiona na 1440 minut (jeden dzień).<br><br> ![parametr interwału](./media/scom-assessment/interval.png)<br>  

    Jeśli wartość jest ustawiona na mniej niż 1440 minut, reguła jest uruchamiana w odstępie jednego dnia. W tym przykładzie reguła ignoruje wartość interwału i jest uruchamiana z częstotliwością jednego dnia.


## <a name="understanding-how-recommendations-are-prioritized"></a>Opis priorytetyzacji zaleceń

Każde wydane zalecenie otrzymuje wartość ważącą, która określa względne znaczenie zalecenia. Pokazano tylko 10 najważniejszych zaleceń.

### <a name="how-weights-are-calculated"></a>Sposób obliczania wag

Współczynniki kory'u są wartościami zagregowanymi na podstawie trzech kluczowych czynników:

- *Prawdopodobieństwo,* że zidentyfikowany problem spowoduje problemy. Większe prawdopodobieństwo równa się większe ogólny wynik dla zalecenia.
- *Wpływ* problemu na organizację, jeśli powoduje to problem. Większy wpływ oznacza większy ogólny wynik dla zalecenia.
- *Wysiłek* niezbędny do wdrożenia zalecenia. Większy wysiłek oznacza mniejszy ogólny wynik zalecenia.

Waga dla każdego zalecenia jest wyrażona jako procent całkowitego wyniku dostępnego dla każdego obszaru ostrości. Jeśli na przykład zalecenie w obszarze dostępności i ciągłości działania ma wynik 5%, wdrożenie tego zalecenia zwiększa ogólny wynik dostępności i ciągłości biznesowej o 5%.

### <a name="focus-areas"></a>Obszary fokusu

**Dostępność i ciągłość działania** — ten obszar ostrości zawiera zalecenia dotyczące dostępności usług, odporności infrastruktury i ochrony biznesowej.

**Wydajność i skalowalność** — ten obszar ostrości zawiera zalecenia, które pomogą w rozwoju infrastruktury IT w organizacji, upewnią się, że środowisko IT spełnia bieżące wymagania dotyczące wydajności i jest w stanie reagować na zmieniające się potrzeby infrastrukturalne.

**Uaktualnianie, migracja i wdrażanie** — w tym obszarze fokusu przedstawiono zalecenia ułatwiające uaktualnianie, migrację i wdrażanie programu SQL Server w istniejącej infrastrukturze.

**Operacje i monitorowanie** — ten obszar ostrości zawiera zalecenia ułatwiające usprawnienie operacji IT, wdrożenie konserwacji zapobiegawczej i maksymalizację wydajności.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Czy należy dążyć do osiągnięcia wyniku 100% w każdym obszarze fokusu?

Niekoniecznie. Zalecenia są oparte na wiedzy i doświadczeniach zdobytych przez inżynierów firmy Microsoft podczas tysięcy wizyt klientów. Jednak nie dwie infrastruktury serwera są takie same, a konkretne zalecenia mogą być mniej lub bardziej istotne dla Ciebie. Na przykład niektóre zalecenia dotyczące zabezpieczeń mogą być mniej istotne, jeśli maszyny wirtualne nie są udostępniane w Internecie. Niektóre zalecenia dotyczące dostępności mogą być mniej istotne dla usług, które zapewniają gromadzenie i raportowanie danych ad hoc o niskim priorytecie. Kwestie, które są ważne dla dojrzałego biznesu, mogą być mniej ważne dla start-upu. Możesz określić, które obszary fokusu są Twoimi priorytetami, a następnie sprawdzić, jak twoje wyniki zmieniają się w czasie.

Każde zalecenie zawiera wskazówki, dlaczego jest to ważne. Skorzystaj z tych wskazówek, aby ocenić, czy wdrożenie zalecenia jest odpowiednie dla użytkownika, biorąc pod uwagę charakter usług IT i potrzeby biznesowe organizacji.

## <a name="use-health-check-focus-area-recommendations"></a>Korzystanie z zaleceń dotyczących obszaru fokusu sprawdzania kondycji

Aby można było użyć rozwiązania sprawdzania kondycji w usłudze Log Analytics, należy zainstalować rozwiązanie. Aby dowiedzieć się więcej o instalowaniu rozwiązań, zobacz [Instalowanie rozwiązania do zarządzania](../../azure-monitor/insights/solutions.md). Po zainstalowaniu można wyświetlić podsumowanie zaleceń przy użyciu kafelka Sprawdzanie kondycji programu System Center Operations Manager na stronie **Przegląd** obszaru roboczego w witrynie Azure portal.

Wyświetl podsumowane oceny zgodności dla infrastruktury, a następnie przejdź do szczegółów zaleceń.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Aby wyświetlić zalecenia dotyczące obszaru fokusu i podjąć działania naprawcze
1. Zaloguj się do portalu [https://portal.azure.com](https://portal.azure.com)Azure w .
2. W witrynie Azure Portal kliknij pozycję **Więcej usług** w lewym dolnym rogu. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Log Analytics**.
3. W okienku subskrypcje usługi Log Analytics wybierz obszar roboczy, a następnie kliknij element menu **podsumowania obszaru roboczego.**  
4. Na stronie **Przegląd** kliknij kafelek **Sprawdzanie kondycji programu System Center Operations Manager.**
5. Na stronie **Sprawdzanie kondycji programu System Center Operations Manager** przejrzyj informacje podsumowujące w jednym z bloków obszaru fokusu, a następnie kliknij jeden z nich, aby wyświetlić zalecenia dotyczące tego obszaru fokusu.
6. Na dowolnej stronie obszaru fokusu można wyświetlić priorytetowe zalecenia dla środowiska. Kliknij zalecenie w obszarze **Obiekty, których dotyczy problem,** aby wyświetlić szczegółowe informacje o tym, dlaczego zalecenie zostało sformułowane.<br><br> ![obszar ostrości](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-02.png)<br>
7. W obszarze **Sugerowane działania**można podjąć działania naprawcze sugerowane . Po zajmaniu się elementem późniejsze oceny będą rejestrować, że zalecono zalecane działania, a wynik zgodności wzrośnie. Poprawione elementy są wyświetlane jako **przekazane obiekty**.

## <a name="ignore-recommendations"></a>Ignorowanie zaleceń

Jeśli masz zalecenia, które chcesz zignorować, możesz utworzyć plik tekstowy używany przez usługi Log Analytics, aby zapobiec pojawianiu się zaleceń w wynikach oceny.

### <a name="to-identify-recommendations-that-you-want-to-ignore"></a>Aby zidentyfikować zalecenia, które chcesz zignorować
1. W witrynie Azure portal na stronie obszaru roboczego usługi Log Analytics dla wybranego obszaru roboczego kliknij element menu **Wyszukiwanie dzienników.**
2. Poniższa kwerenda służy do listy zaleceń, które nie powiodły się dla komputerów w twoim środowisku.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Jeśli obszar roboczy został uaktualniony do [nowego języka zapytań usługi Log Analytics,](../../azure-monitor/log-query/log-query-overview.md)powyższe zapytanie zmieni się na następujące.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    Oto zrzut ekranu przedstawiający zapytanie wyszukiwania dzienników:<br><br> ![przeszukiwanie dzienników](./media/scom-assessment/scom-log-search.png)<br>

3. Wybierz zalecenia, które chcesz zignorować. Użyjesz wartości dla RecommendationId w następnej procedurze.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Aby utworzyć plik tekstowy IgnoreRecommenddations.txt i używać go

1. Utwórz plik o nazwie IgnoreRecommendations.txt.
2. Wklej lub wpisz każdy recommendationId dla każdej rekomendacji, którą chcesz, aby usługa Log Analytics zignorowała w osobnym wierszu, a następnie zapisz i zamknij plik.
3. Umieść plik w następującym folderze na każdym komputerze, na którym chcesz, aby usługa Log Analytics ignorowała zalecenia.
4. Na serwerze zarządzania programu Operations Manager — *SystemDrive*:\Program Files\Microsoft System Center 2012 R2\Operations Manager\Server.

### <a name="to-verify-that-recommendations-are-ignored"></a>Aby sprawdzić, czy zalecenia są ignorowane

1. Po uruchomieniu następnej zaplanowanej oceny, domyślnie co siedem dni, określone zalecenia są oznaczone jako Ignorowane i nie pojawią się na pulpicie nawigacyjnym sprawdzania kondycji.
2. Za pomocą następujących zapytań wyszukiwania dziennika można wyświetlić listę wszystkich zignorowanych zaleceń.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    >[!NOTE]
    > Jeśli obszar roboczy został uaktualniony do [nowego języka zapytań usługi Log Analytics,](../../azure-monitor/log-query/log-query-overview.md)powyższe zapytanie zmieni się na następujące.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Ignore" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

3. Jeśli później zdecydujesz, że chcesz zobaczyć zignorowane zalecenia, usuń wszystkie pliki IgnoreRecommendations.txt lub możesz usunąć z nich identyfikatory rekomendacji.

## <a name="system-center-operations-manager-health-check-solution-faq"></a>Rozwiązanie System Center Operations Manager Health Check — często zadawane pytania

*Dodano rozwiązanie health check do mojego obszaru roboczego usługi Log Analytics. Ale nie widzę zaleceń. Dlaczego nie?* Po dodaniu rozwiązania, należy użyć następujących kroków wyświetlić zalecenia na pulpicie nawigacyjnym usługi Log Analytics.  

- [Ustawianie konta Uruchom jako dla sprawdzania kondycji programu System Center Operations Manager](#operations-manager-run-as-accounts-for-log-analytics)  
- [Konfigurowanie reguły sprawdzania kondycji programu System Center Operations Manager](#configure-the-health-check-rule)


*Czy istnieje sposób, aby skonfigurować, jak często sprawdzane?* Tak. Zobacz [Konfigurowanie częstotliwości uruchamiania](#configure-the-run-frequency).

*Jeśli po dodaniu rozwiązania health check programu System Center Operations Manager zostanie odnaleziony inny serwer, zostanie ono sprawdzone?* Tak, po odnajdowaniu jest sprawdzany od tego czasu, domyślnie co siedem dni.

*Jaka jest nazwa procesu, który zbiera dane?* DoradcaAssessment.exe

*Gdzie działa proces AdvisorAssessment.exe?* AdvisorAssessment.exe jest uruchamiany w ramach procesu HealthService serwera zarządzania, na którym jest włączona reguła sprawdzania kondycji. Za pomocą tego procesu odnajdowanie całego środowiska odbywa się za pomocą zdalnego zbierania danych.

*Jak długo trwa zbieranie danych?* Zbieranie danych na serwerze trwa około godziny. Może to potrwać dłużej w środowiskach, które mają wiele wystąpień lub baz danych programu Operations Manager.

*Co się stanie, jeśli ustawię interwał oceny na mniej niż 1440 minut?* Ocena jest wstępnie skonfigurowana do uruchamiania maksymalnie raz dziennie. Jeśli wartość interwału zostanie zastąpiona wartością mniejszą niż 1440 minut, ocena użyje 1440 minut jako wartości interwału.

*Jak dowiedzieć się, czy występują błędy wstępne?* Jeśli kontrola kondycji została uruchomiony i nie widać wyników, to jest prawdopodobne, że niektóre z wymagań wstępnych dla kontroli nie powiodło się. Można wykonać `Operation Solution=SCOMAssessment` kwerendy: `SCOMAssessmentRecommendation FocusArea=Prerequisites` i w dzienniku wyszukiwania, aby wyświetlić nie powiodło się wymagania wstępne.

*Istnieje `Failed to connect to the SQL Instance (….).` komunikat w niepowodzeniach warunek wstępny. Na czym polega problem?* AdvisorAssessment.exe, proces, który zbiera dane, jest uruchamiany w ramach procesu HealthService na serwerze zarządzania. W ramach sprawdzania kondycji proces próbuje połączyć się z programem SQL Server, w którym znajduje się baza danych programu Operations Manager. Ten błąd może wystąpić, gdy reguły zapory blokują połączenie z wystąpieniem programu SQL Server.

*Jakiego rodzaju dane są zbierane?* Zbierane są następujące typy danych: — dane WMI — dane rejestru — dane EventLog - Dane programu Operations Manager za pośrednictwem programu Windows PowerShell, kwerendy SQL i moduł zbierający informacje o plikach.

*Dlaczego muszę skonfigurować konto Uruchom jako konto?* W programie Operations Manager są uruchamiane różne zapytania SQL. Aby można było je uruchomić, należy użyć uruchom jako konto z niezbędnymi uprawnieniami. Ponadto poświadczenia administratora lokalnego są wymagane do wykonywania zapytań w u usług WMI.

*Dlaczego wyświetlać tylko 10 najlepszych rekomendacji?* Zamiast podawać wyczerpującą, przytłaczającą listę zadań, zalecamy skupienie się najpierw na rozwiązaniu priorytetowych zaleceń. Po ich rozwiązaniu dostępne będą dodatkowe zalecenia. Jeśli wolisz zobaczyć szczegółową listę, możesz wyświetlić wszystkie rekomendacje za pomocą wyszukiwania w dzienniku.

*Czy istnieje sposób, aby zignorować zalecenie?* Tak, zobacz [zalecenia ignoruj](#ignore-recommendations).


## <a name="next-steps"></a>Następne kroki

- [Dzienniki wyszukiwania,](../../azure-monitor/log-query/log-query-overview.md) aby dowiedzieć się, jak analizować szczegółowe dane i zalecenia dotyczące sprawdzania kondycji programu System Center Operations Manager.
