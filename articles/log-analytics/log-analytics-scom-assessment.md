---
title: Optymalizacja środowiskiem programu System Center Operations Manager na platformie Azure Log Analytics | Dokumentacja firmy Microsoft
description: Sprawdź System Center Operations Manager kondycji rozwiązania można użyć do oceny ryzyka i kondycji środowisk w regularnych odstępach czasu.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: tysonn
ms.assetid: 49aad8b1-3e05-4588-956c-6fdd7715cda1
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2017
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 86484ca2bc7dc14035f48b8f7b1514a4fc471b74
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2018
ms.locfileid: "29180040"
---
# <a name="optimize-your-environment-with-the-system-center-operations-manager-health-check-preview-solution"></a>Optymalizuj środowisko z rozwiązań System Center Operations Manager sprawdzanie kondycji (wersja zapoznawcza)

![System Center Operations Manager kondycji Sprawdź symbol](./media/log-analytics-scom-assessment/scom-assessment-symbol.png)

Sprawdź System Center Operations Manager kondycji rozwiązania można użyć do oceny ryzyka i kondycji grupy zarządzania programu System Center Operations Manager w regularnych odstępach czasu. Ten artykuł pomaga zainstalować, skonfigurować i użycie rozwiązania, dzięki czemu można wykonać działania naprawcze potencjalnych problemów.

To rozwiązanie zapewnia priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożone. Zalecenia są podzielone na cztery obszarach zainteresowań, które pomagają w szybkim świadomość ryzyka i podejmowanie działań naprawczych.

Zalecenia są oparte na wiedzy i doświadczenia przez pracownicy firmy Microsoft z tysięcy wizyt klienta. Każde zalecenie znajdują się wskazówki dotyczące przyczyny problemu może być uwzględniana i implementowania sugerowane zmiany.

Można wybrać obszarach zainteresowań, które są najbardziej ważne dla organizacji i śledzić postęp w kierunku uruchomionym środowiskiem ryzyka bezpłatne i działa prawidłowo.

Po dodaniu rozwiązania i ocenę jest wykonywane, podsumowanie informacji o obszarach zainteresowań jest wyświetlany na **Sprawdź System Center Operations Manager kondycji** pulpitu nawigacyjnego dla infrastruktury. W poniższych sekcjach opisano sposób użyć informacji na temat **Sprawdź System Center Operations Manager kondycji** pulpitu nawigacyjnego, gdzie można przeglądać i wykonaj zalecane akcje dotyczące środowiska programu Operations Manager.

![Kafelek rozwiązania programu System Center Operations Manager](./media/log-analytics-scom-assessment/log-analytics-scom-healthcheck-tile.png)

![Pulpit nawigacyjny System Center Operations Manager kondycji Sprawdź](./media/log-analytics-scom-assessment/log-analytics-scom-healthcheck-dashboard-01.png)

## <a name="installing-and-configuring-the-solution"></a>Instalowanie i konfigurowanie rozwiązania

Rozwiązanie działa z programu Microsoft System Operations Manager 2012 Service Pack (SP) 1 i 2012 R2.

Skorzystaj z poniższych informacji, aby zainstalować i skonfigurować rozwiązania.

 - Zanim użyjesz rozwiązania sprawdzania kondycji w analizy dzienników, musi mieć zainstalowane oprogramowanie. Instalowanie rozwiązania z [witrynę Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SCOMAssessmentOMS?tab=Overview).

 - Po dodaniu rozwiązania do obszaru roboczego **Sprawdź System Center Operations Manager kondycji** kafelka na pulpicie nawigacyjnym zostanie wyświetlony komunikat wymagana dodatkowa konfiguracja. Kliknij Kafelek i wykonaj kroki konfiguracji wymienione na stronie

 ![Kafelka pulpitu nawigacyjnego programu System Center Operations Manager](./media/log-analytics-scom-assessment/scom-configrequired-tile.png)

> [!NOTE]
> Konfiguracji programu System Center Operations Manager można zrobić za pomocą skryptu, wykonując następujące kroki wymienione na stronie Konfiguracja rozwiązania w analizy dzienników.

 Aby skonfigurować oceny za pomocą konsoli operacje programu Operations Manager, należy wykonać poniższe czynności w następującej kolejności:
1. [Ustaw konto Uruchom jako dla Sprawdź System Center Operations Manager kondycji](#operations-manager-run-as-accounts-for-log-analytics)  
2. [Skonfiguruj regułę Sprawdź System Center Operations Manager kondycji](#configure-the-assessment-rule)

## <a name="system-center-operations-manager-assessment-data-collection-details"></a>Szczegóły danych oceny System Center Operations Manager w kolekcji

Ocena programu System Center Operations Manager zbiera dane z następujących źródeł:

* Rejestr
* Instrumentacja zarządzania Windows (WMI)
* Dziennik zdarzeń
* Plik danych
* Bezpośrednio z programu Operations Manager za pomocą zapytań SQL i programu PowerShell, z serwerem zarządzania, które zostały określone.  

Dane są zbierane na serwerze zarządzania i przekazywane do analizy dzienników co siedem dni.  

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Uruchom jako konta programu Operations Manager dla analizy dzienników

Kompilacje analizy dziennika od pakietów administracyjnych dla obciążeń w celu zapewnienia wartość — Dodawanie usług. Poszczególnych obciążeń wymaga specyficznego dla obciążenia uprawnień do uruchamiania pakietów administracyjnych w innym kontekście zabezpieczeń, takich jak konta użytkownika domeny. Skonfiguruj konto Operations Manager uruchom jako z poświadczeń dla konta uprzywilejowanego. Aby uzyskać dodatkowe informacje, zobacz [sposobu tworzenia konta Uruchom jako](https://technet.microsoft.com/library/hh321655(v=sc.12).aspx) w dokumentacji programu Operations Manager.

Skorzystaj z poniższych informacji, aby ustawić konto Operations Manager uruchom jako dla Sprawdź System Center Operations Manager kondycji.

### <a name="set-the-run-as-account"></a>Ustawianie konta Uruchom jako

Konto Uruchom jako musi spełniać następujące wymagania przed kontynuowaniem:

* Konto użytkownika domeny należącego do lokalnej grupy administratorów na wszystkich serwerach obsługujących dowolnej roli programu Operations Manager — serwer zarządzania, serwera SQL Server z operacyjną, Magazyn danych i bazy danych ACS, raportowanie, sieci Web konsoli, a serwer bramy.
* Rola administratora menedżera operacji dla grupy zarządzania są oceniane
* Jeśli konto nie ma prawa administratora systemu SQL, wykonujących [skryptu](#sql-script-to-grant-granular-permissions-to-the-run-as-account) udzielenia szczegółowe uprawnienia do konta na każde wystąpienie programu SQL Server obsługującego jednego lub wszystkich baz danych programu Operations Manager.

1. W konsoli programu Operations Manager, wybierz **administracji** przycisk nawigacji.
2. W obszarze **Konfiguracja Uruchom jako**, kliknij przycisk **kont**.
3. W **Utwórz konto Uruchom jako** kreatora na **wprowadzenie** kliknij **dalej**.
4. Na **właściwości ogólne** wybierz pozycję **Windows** w **typ konta Uruchom jako:** listy.
5. Wpisz nazwę wyświetlaną w **nazwy wyświetlanej** tekst pola i opcjonalnie wpisać opis w **opis** , a następnie kliknij przycisk **dalej**.
6. Na **zabezpieczenia dystrybucji** wybierz pozycję **bezpieczniejsze**.
7. Kliknij przycisk **Utwórz**.  

Teraz, gdy utworzone konto Uruchom jako jest potrzebuje do docelowych serwerów zarządzania w grupie zarządzania i skojarzone z wstępnie zdefiniowanego profilu Uruchom jako, tak przepływ pracy zostanie uruchomiony przy użyciu poświadczeń.  

1. W obszarze **Konfiguracja Uruchom jako**, **kont**, w okienku wyników kliknij dwukrotnie utworzonego wcześniej konta.
2. Na **dystrybucji** , kliknij pozycję **Dodaj** dla **komputery wybrane** i dodać na dystrybucję konta na serwerze zarządzania.  Kliknij przycisk **OK** dwa razy, aby zapisać zmiany.
3. W obszarze **Konfiguracja Uruchom jako**, kliknij przycisk **profile**.
4. Wyszukaj *SCOM oceny profilu*.
5. Nazwa profilu powinna być: *programu Microsoft System Center Advisor SCOM oceny profilu Uruchom jako*.
6. Kliknij prawym przyciskiem myszy i zaktualizować jej właściwości i dodać ostatnio utworzone konto Uruchom jako, utworzony wcześniej.

### <a name="sql-script-to-grant-granular-permissions-to-the-run-as-account"></a>Skrypt SQL, aby udzielić szczegółowe uprawnienia do konta Uruchom jako

Uruchom następujący skrypt SQL można udzielić wymaganych uprawnień do konta Uruchom jako w wystąpieniu programu SQL Server używany przez hosting operacyjnej programu Operations Manager, Magazyn danych i bazy danych ACS.

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

### <a name="configure-the-health-check-rule"></a>Skonfiguruj reguły sprawdzania kondycji

Pakiet administracyjny rozwiązania System Center Operations Manager kondycji sprawdzania zawiera reguły o nazwie *Microsoft System Center Advisor SCOM oceny Uruchom oceny reguły*. Ta reguła jest odpowiada za uruchamianie sprawdzania kondycji. Aby włączyć reguły i skonfigurować częstotliwość, użyj poniższej procedury.

Domyślnie program Microsoft System Center Advisor SCOM oceny Uruchom oceny reguła jest wyłączona. Aby uruchomić sprawdzenie kondycji, należy włączyć reguły na serwerze zarządzania. Wykonaj następujące kroki.

#### <a name="enable-the-rule-for-a-specific-management-server"></a>Włącz regułę dla serwera zarządzania określonymi

1. W **tworzenie** obszaru roboczego w konsoli operacje programu Operations Manager, wyszukaj regułę *Microsoft System Center Advisor SCOM oceny Uruchom oceny reguły* w **reguły** okienka.
2. W wynikach wyszukiwania wybierz jedną, która zawiera tekst *typu: serwer zarządzania*.
3. Kliknij prawym przyciskiem myszy reguły, a następnie kliknij przycisk **zastępuje** > **dla konkretnego obiektu klasy: serwer zarządzania**.
4.  Na liście serwerów dostępnych zarządzania wybierz serwer zarządzania, w którym reguła ma zostać uruchomiona.  Powinno to być tego samego serwera zarządzania, przez skonfigurowane wcześniej do skojarzenia z konta Uruchom jako.
5.  Zmień wartość zastąpienia **True** dla **włączone** wartość parametru.<br><br> ![zastąpienie parametru](./media/log-analytics-scom-assessment/rule.png)

    Gdy nadal w tym oknie skonfiguruj częstotliwość uruchamiania, wykonując następną procedurę.

#### <a name="configure-the-run-frequency"></a>Skonfiguruj częstotliwość uruchamiania

Ocena jest domyślnie konfigurowana do uruchamiania co 10 080 minut (lub siedem dni). Można zastąpić wartość minimalną wartość 1440 minut (lub jeden dzień). Wartość reprezentuje przerwę minimalny czas między oszacowanie działa. Aby zastąpić interwał, skorzystaj z poniższych czynności.

1. W **tworzenie** obszaru roboczego w konsoli programu Operations Manager, wyszukaj regułę *Microsoft System Center Advisor SCOM oceny Uruchom oceny reguły* w **reguły** sekcja.
2. W wynikach wyszukiwania wybierz jedną, która zawiera tekst *typu: serwer zarządzania*.
3. Kliknij prawym przyciskiem myszy reguły, a następnie kliknij przycisk **Zastąp zasadę** > **dla wszystkich obiektów klasy: serwer zarządzania**.
4. Zmień **interwał** wartość parametru na wartość dla żądanego interwału. W poniższym przykładzie wartość jest równa 1440 minut (jeden dzień).<br><br> ![Parametr interwał](./media/log-analytics-scom-assessment/interval.png)<br>  

    Jeśli wartość jest mniejsza niż 1440 minut, gdy zasada jest wykonywana w interwale jeden dzień. W tym przykładzie reguła ignoruje wartość interwału i jest uruchamiany z częstotliwością jeden dzień.


## <a name="understanding-how-recommendations-are-prioritized"></a>Opis sposobu mają pierwszeństwo zalecenia

Każdy zalecenie wprowadzone otrzymuje wartość wagi, która identyfikuje względnego zalecenia. 10 najważniejszych zalecenia są wyświetlane.

### <a name="how-weights-are-calculated"></a>Jak są obliczane wagi

Wagi są wartości zagregowanych oparte na trzech kluczowych czynników:

- *Prawdopodobieństwo* czy problemu może powodować problemy. Większe prawdopodobieństwo równa większych ogólny wynik dla zalecenia.
- *Wpływ* problemu w Twojej organizacji, jeśli spowodować problem. Większy wpływ równa większych ogólny wynik dla zalecenia.
- *Nakładu* wymagane do wykonania zalecenia. Wyższy nakładu równa mniejszych ogólny wynik dla zalecenia.

Wagi dla każde zalecenie jest wyrażona jako procent całkowitej wynik dostępne dla każdego obszaru fokus. Na przykład jeśli zalecenia w obszarze fokus dostępność i ciągłość prowadzenia działalności biznesowej ma wynik % 5, wdrażanie tego zalecenia zwiększa z ogólną % wynik przez 5 dostępność i ciągłość prowadzenia działalności biznesowej.

### <a name="focus-areas"></a>Obszarach zainteresowań

**Dostępność i ciągłość prowadzenia działalności biznesowej** — w tym obszarze fokus zawiera zalecenia dotyczące odporności infrastruktury i ochronę biznesowej, dostępności usług.

**Wydajność i skalowalność** — w tym obszarze fokus zawiera zalecenia ułatwiające organizacji infrastruktury IT zwiększania, upewnij się, że spełnia bieżące wymagania dotyczące wydajności środowiska informatycznego i jest w stanie reagować na zmieniające się infrastruktury wymagania związane z.

**Uaktualnienie, migrację i wdrażanie** — w tym obszarze fokus zawiera zalecenia ułatwiające uaktualniania, migracji i wdrożenia programu SQL Server w istniejącej infrastrukturze.

**Operacje i monitorowanie** — w tym obszarze fokus zawiera zalecenia ułatwiające usprawnić operacji IT, wdrożenia program prewencyjnej konserwacji i zmaksymalizować wydajność.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Należy dążyć wynik 100% w każdym obszarze fokus?

Niekoniecznie. Zalecenia są oparte na wiedzy i doświadczeń zdobytych Microsoft inżynierowie między tysięcy wizyt klienta. Jednak nie infrastruktury serwerowe są takie same i szczegółowe zalecenia mogą być bardziej lub mniej istotne dla Ciebie. Na przykład niektóre zalecenia dotyczące zabezpieczeń może być mniej ważne, jeśli maszyny wirtualne nie są widoczne w Internecie. Kilka zaleceń dostępności może być mniej istotne dla usług, które umożliwiają zbieranie danych ad hoc — niski priorytet i raportowania. Problemy, które są ważne dla dorosłych firm może być mniej ważne do rozruchu. Można zidentyfikować obszary fokus, które mają zebranych i przyjrzyj się jak zmienić wyniki wraz z upływem czasu.

Każdy zalecenie obejmuje wskazówek dotyczących Dlaczego ważne jest. Skorzystać z poniższych wskazówek do oceny, czy wdrażanie zalecenie jest odpowiednie dla Ciebie, biorąc pod uwagę rodzaj usług informatycznych i potrzeb biznesowych w organizacji.

## <a name="use-health-check-focus-area-recommendations"></a>Sprawdzanie kondycji Użyj zaleceń obszaru fokus

Zanim użyjesz funkcję sprawdzania kondycji w analizy dzienników, musi mieć zainstalowane oprogramowanie. Aby uzyskać więcej informacji na temat instalowania rozwiązań, zobacz [rozwiązań dodać analizy dzienników z galerii rozwiązań](log-analytics-add-solutions.md). Po zakończeniu instalacji można wyświetlić podsumowanie zaleceń za pomocą kafelka Sprawdź System Center Operations Manager kondycji na stronie Przegląd w portalu OMS.

Wyświetl oceny zgodności podsumowania dla Twojej infrastruktury, a następnie wejdź do zalecenia.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Aby wyświetlić zalecenia dla obszaru fokus i podjąć działania naprawcze
1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
2. W witrynie Azure Portal kliknij pozycję **Więcej usług** w lewym dolnym rogu. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Log Analytics**.
3. W okienku subskrypcje analizy dzienników, wybierz obszar roboczy, a następnie kliknij przycisk **portalu OMS** kafelka.  
4. Na **omówienie** kliknij przycisk **Sprawdź System Center Operations Manager kondycji** kafelka.
5. Na **Sprawdź System Center Operations Manager kondycji** strony, sprawdź informacje w jednym z bloków obszaru fokus, a następnie kliknij przycisk jedna, aby wyświetlić zalecenia dla tego obszaru fokus.
6. Na wszystkich stronach obszaru fokus można wyświetlić priorytetową zalecenia dla danego środowiska. Kliknij zalecenie, w obszarze **dotyczy obiektów** Aby wyświetlić szczegóły dotyczące Dlaczego tworzone są zalecenia.<br><br> ![obszar fokus](./media/log-analytics-scom-assessment/log-analytics-scom-healthcheck-dashboard-02.png)<br>
7. Należy wykonać działania naprawcze sugerowane w **sugerowanych akcji**. Jeśli element został rozwiązany, ocen nowsze zarejestruje które zalecane akcje zostały pobrane i zwiększa wynik zgodności. Poprawione elementy są wyświetlane jako **przekazany obiektów**.

## <a name="ignore-recommendations"></a>Ignoruj zalecenia

Jeśli masz zaleceń, które chcesz zignorować, można utworzyć pliku tekstowego, który używa analizy dzienników zapobiegające zaleceń znajdujących się w wynikach oceny.

[!INCLUDE[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

### <a name="to-identify-recommendations-that-you-want-to-ignore"></a>Aby zidentyfikować zaleceń, które chcesz zignorować
1. W portalu Azure na stronie obszaru roboczego analizy dzienników dla Twojego wybranego obszaru roboczego kliknij **wyszukiwania dziennika** kafelka.
2. Użyj następującego zapytania do listy zaleceń, które nie powiodły, na komputerach w danym środowisku.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Jeśli obszaru roboczego został uaktualniony do [języka zapytań nowe analizy dzienników](log-analytics-log-search-upgrade.md), a następnie zmienić powyższym zapytaniu następujące.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    Poniżej przedstawiono zrzut ekranu przedstawiający zapytania wyszukiwania dziennika:<br><br> ![przeszukiwanie dzienników](./media/log-analytics-scom-assessment/scom-log-search.png)<br>

3. Wybierz zaleceń, które chcesz zignorować. Użyjesz wartości RecommendationId w następnej procedurze.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Tworzenie i używanie IgnoreRecommendations.txt pliku tekstowego

1. Utwórz plik o nazwie IgnoreRecommendations.txt.
2. Wklej lub wpisz każdego RecommendationId dla każde zalecenie, które mają analizy dzienników można zignorować w osobnym wierszu, a następnie zapisz i zamknij plik.
3. Umieść plik w następującym folderze na każdym komputerze, w którym ma analizy dzienników zignorowanie zalecenia.
4. Na serwerze zarządzania programu Operations Manager — *SystemDrive*: System Center 2012 R2\Operations Manager\Server \Program Files\Microsoft.

### <a name="to-verify-that-recommendations-are-ignored"></a>Aby zweryfikować, że zalecenia są ignorowane

1. Po na następną zaplanowaną ocenę działa, domyślnie co siedem dni, określonego zalecenia są oznaczane ignorowany i nie będą wyświetlane na pulpicie nawigacyjnym kondycji wyboru.
2. Następujące zapytania wyszukiwania dziennika służy do tworzenia listy wszystkich zignorowane zalecenia.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    >[!NOTE]
    > Jeśli obszaru roboczego został uaktualniony do [języka zapytań nowe analizy dzienników](log-analytics-log-search-upgrade.md), a następnie zmienić powyższym zapytaniu następujące.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Ignore" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

3. Jeśli później zdecydujesz chcesz zobaczyć zignorowane zalecenia dotyczące, Usuń wszystkie pliki IgnoreRecommendations.txt lub RecommendationIDs można usunąć z nich.

## <a name="system-center-operations-manager-health-check-solution-faq"></a>System Center Operations Manager kondycji Sprawdź rozwiązania — często zadawane pytania

*Sprawdzanie kondycji rozwiązań po dodaniu do mojego obszaru roboczego analizy dzienników. Nie widzę zalecenia. Dlaczego nie?* Po dodaniu rozwiązania, użyj następującego widoku kroki zaleceń na pulpicie nawigacyjnym analizy dzienników.  

- [Ustaw konto Uruchom jako dla Sprawdź System Center Operations Manager kondycji](#operations-manager-run-as-accounts-for-log-analytics)  
- [Skonfiguruj regułę Sprawdź System Center Operations Manager kondycji](#configure-the-health-check-rule)


*Czy istnieje sposób konfigurowania, jak często kontrola nie zostanie uruchomiona?* Tak. Zobacz [skonfiguruj częstotliwość uruchamiania](#configure-the-run-frequency).

*Jeśli inny serwer zostanie odnaleziony po rozwiązaniu System Center Operations Manager oceny zostały dodane, będzie on sprawdzany?* Tak, po odnajdywaniu, który jest następnie zaznaczone domyślnie co siedem dni.

*Co to jest nazwa procesu, który wykonuje zbierania danych?* AdvisorAssessment.exe

*Gdy jest uruchamiany proces AdvisorAssessment.exe?* AdvisorAssessment.exe uruchamiana procesu usługi kondycji serwera zarządzania, w których włączono reguła sprawdzania kondycji. Za pomocą tego procesu, odnajdywania całego środowiska odbywa się za pośrednictwem zdalne zbieranie danych.

*Jak długo trwa zbierania danych?* Zbieranie danych na serwerze trwa około jednej godziny. Może trwać dłużej w środowiskach mających wiele wystąpień programu Operations Manager lub baz danych.

*Co zrobić, jeśli do mniej niż 1440 minut ustawić interwał oceny?* Ocena jest wstępnie skonfigurowana do uruchamiania na maksymalnie raz dziennie. Jeśli zastąpić wartość interwału do mniej niż 1440 minut, następnie oceny używa 1440 minut jako wartość interwału.

*Jak sprawdzić, czy istnieją błędy?* Jeśli nie widzisz wyników uruchomienia sprawdzania kondycji, następnie jest prawdopodobne, że niektóre do sprawdzania wymagań wstępnych nie powiodło się. Można wykonać zapytania: `Operation Solution=SCOMAssessment` i `SCOMAssessmentRecommendation FocusArea=Prerequisites` w dzienniku Wyszukaj, aby zobaczyć wymagań wstępnych nie powiodło się.

*Brak `Failed to connect to the SQL Instance (….).` komunikat błędy wymagań wstępnych. Co to jest problem?* AdvisorAssessment.exe, proces, która gromadzi dane, jest uruchamiana procesu usługi kondycji na serwerze zarządzania. W ramach sprawdzania kondycji proces próbuje połączyć się z serwerem SQL, gdzie występuje bazy danych programu Operations Manager. Ten błąd może wystąpić, jeśli reguły zapory blokowanie połączenia z wystąpieniem programu SQL Server.

*Jakiego typu dane są zbierane?* Zbierane są następujące typy danych: danych programu Operations Manager — dane usługi WMI — rejestru danych — dane w dzienniku zdarzeń — za pośrednictwem plików, zapytania SQL i programu Windows PowerShell moduł zbierający informacje o.

*Dlaczego trzeba skonfigurować konto Uruchom jako?* Z programem Operations Manager są uruchamiane różne zapytania SQL. Aby je uruchomić należy użyć konto Uruchom jako z niezbędne uprawnienia. Ponadto aby kwerenda WMI wymagane są poświadczenia administratora lokalnego.

*Dlaczego są wyświetlane tylko zalecenia 10 pierwszych?* Zamiast daje wyczerpująca, utrudnione listę zadań, zaleca się skupić się na najpierw adresowania priorytetową zalecenia. Po adresu im dodatkowe zalecenia staną się dostępne. Jeśli wolisz wyświetlić listę szczegółowych, można wyświetlić wszystkie zalecenia za pomocą wyszukiwania dziennika.

*Czy istnieje sposób, aby zignorować zalecenie?* Tak, zobacz [zignorowanie zalecenia](#Ignore-recommendations).


## <a name="next-steps"></a>Kolejne kroki

- [Wyszukaj dzienniki](log-analytics-log-searches.md) informacje na temat analizowania szczegółowych danych sprawdź System Center Operations Manager kondycji i zaleceń.
