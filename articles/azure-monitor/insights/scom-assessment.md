---
title: Optymalizowanie środowiska programu System Center Operations Manager z usługą Azure Log Analytics | Dokumentacja firmy Microsoft
description: Rozwiązania System Center Operations Manager Health Check można użyć do oceny ryzyka i kondycji środowisk w regularnych odstępach czasu.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 49aad8b1-3e05-4588-956c-6fdd7715cda1
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: magoedte
ms.openlocfilehash: 27b55af74a713c51655891df8c852ff44cd3744a
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58621774"
---
# <a name="optimize-your-environment-with-the-system-center-operations-manager-health-check-preview-solution"></a>Optymalizowanie środowiska za pomocą rozwiązania System Center Operations Manager Health Check (wersja zapoznawcza)

![System Center Operations Manager Health Check symboli](./media/scom-assessment/scom-assessment-symbol.png)

Rozwiązania System Center Operations Manager Health Check można użyć do oceny ryzyka i kondycji grupy zarządzania programu System Center Operations Manager w regularnych odstępach czasu. Ten artykuł pomoże Ci instalowanie, konfigurowanie i korzystanie z odpowiedniego rozwiązania, tak, aby można podjąć akcje naprawcze o potencjalnych problemach.

To rozwiązanie zapewnia priorytetową listą zalecenia dotyczące infrastruktury serwerowej wdrożone. Zalecenia są podzielone na cztery obszary koncentracji uwagi, które pomagają w szybkim zrozumieć ryzyko i podejmij działania naprawcze.

Zalecenia są oparte na wiedzy i doświadczenia zdobyte przez inżynierów firmy Microsoft z tysięcy wizyt klienta. Każdy zalecenie zawiera wskazówki na temat przyczyny problemu może być ważne i jak implementować sugerowane zmiany.

Możesz wybrać obszary koncentracji uwagi, które są najważniejsze dla Twojej organizacji i śledzenie postępów uruchamiania środowiska ryzyka bezpłatne i w dobrej kondycji.

Po dodaniu rozwiązania i oceny jest wykonywane Podsumowanie informacje o obszarach zainteresowań jest wyświetlany na **System Center Operations Manager Health Check** pulpitu nawigacyjnego w danej infrastrukturze. W poniższych sekcjach opisano sposób użycia informacji o **System Center Operations Manager Health Check** pulpitu nawigacyjnego, w którym można wyświetlać i następnie podjąć zalecane akcje dla środowiska programu Operations Manager.

![Kafelek rozwiązania programu System Center Operations Manager](./media/scom-assessment/log-analytics-scom-healthcheck-tile.png)

![System Center Operations Manager Health Check w pulpicie nawigacyjnym](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-01.png)

## <a name="installing-and-configuring-the-solution"></a>Instalowanie i konfigurowanie rozwiązania

To rozwiązanie działa przy użyciu programu Microsoft System Center 2012 Operations Manager Service Pack 1, Microsoft System Center 2012 R2 Operations Manager, Microsoft System Center 2016 Operations Manager, Microsoft System Center 2016 Operations Manager i System firmy Microsoft Centrum Operations Manager 1807

Skorzystaj z poniższych informacji, aby zainstalować i skonfigurować rozwiązanie.

- Zanim użyjesz sprawdzanie kondycji rozwiązania w usłudze Log Analytics, musi mieć zainstalowane oprogramowanie. Instalowanie rozwiązania z [portalu Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SCOMAssessmentOMS?tab=Overview).

- Po dodaniu rozwiązania do obszaru roboczego **System Center Operations Manager Health Check** kafelka na pulpicie nawigacyjnym wyświetli komunikat wymagana dodatkowa konfiguracja. Kliknij Kafelek, a następnie wykonaj kroki konfiguracji opisane na stronie

  ![Kafelek pulpitu nawigacyjnego programu System Center Operations Manager](./media/scom-assessment/scom-configrequired-tile.png)

> [!NOTE]
> Konfiguracji programu System Center Operations Manager można zrobić za pomocą skryptu, wykonując kroki opisane na stronie konfiguracji rozwiązania w usłudze Log Analytics.

 Aby skonfigurować ocenę za pomocą konsoli operacje programu Operations Manager, wykonaj poniższe kroki w następującej kolejności:
1. [Ustaw konto Uruchom jako dla System Center Operations Manager Health Check](#operations-manager-run-as-accounts-for-log-analytics)  
2. Skonfiguruj regułę System Center Operations Manager Health Check

## <a name="system-center-operations-manager-health-check-data-collection-details"></a>Szczegółowe informacje kolekcji danych System Center Operations Manager Health Check

Rozwiązania System Center Operations Manager Health Check zbiera dane z następujących źródeł:

* Rejestr
* Instrumentacja zarządzania Windows (WMI)
* Dziennik zdarzeń
* Dane pliku
* Bezpośrednio z programu Operations Manager przy użyciu programu PowerShell i zapytań SQL, z serwera zarządzania, który określono.  

Dane są zbierane na serwerze zarządzania i przekazywane do usługi Log Analytics, co siedem dni.  

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Uruchom jako konta programu Operations Manager dla usługi Log Analytics

Log Analytics kompilacji od pakietów administracyjnych dla obciążeń w celu zapewnienia wartości — Dodawanie usług. Każde obciążenie wymaga uprawnień specyficznego dla obciążenia do uruchamiania pakietów administracyjnych w innym kontekście bezpieczeństwa, takie jak konto użytkownika domeny. Skonfiguruj konto Operations Manager uruchom jako przy użyciu poświadczeń konta uprzywilejowanego. Aby uzyskać więcej informacji, zobacz [sposób tworzenia konta Uruchom jako](https://technet.microsoft.com/library/hh321655(v=sc.12).aspx) w dokumentacji programu Operations Manager.

Skorzystaj z poniższych informacji, aby ustawić konto Operations Manager uruchom jako dla System Center Operations Manager Health Check.

### <a name="set-the-run-as-account"></a>Ustaw konto Uruchom jako

Konto Uruchom jako muszą spełniać następujące wymagania przed kontynuowaniem:

* Konto użytkownika domeny, który jest członkiem lokalnej grupy Administratorzy na wszystkich serwerach, które obsługuje wszystkie role programu Operations Manager — serwer zarządzania, serwera SQL Server hostującego operacyjną, magazynu danych i baza danych usług ACS, raportowanie, internetowej konsoli i serwera bramy.
* Rola Administrator menedżera operacji dla grupy zarządzania oceniane
* Jeśli konto nie ma prawa administratora systemu SQL, a następnie wykonaj [skryptu](#sql-script-to-grant-granular-permissions-to-the-run-as-account) celu przyznania szczegółowych uprawnień do konta w każdym wystąpieniu programu SQL Server hostującym jednego lub wszystkich baz danych programu Operations Manager.

1. W konsoli programu Operations Manager wybierz **administracji** przycisk nawigacji.
2. W obszarze **Konfiguracja Uruchom jako**, kliknij przycisk **kont**.
3. W **Utwórz konto Uruchom jako** kreatora na **wprowadzenie** kliknij **dalej**.
4. Na **ogólne właściwości** wybierz opcję **Windows** w **typ konta Uruchom jako:** listy.
5. Wpisz nazwę wyświetlaną w **nazwy wyświetlanej** tekstu pole, a następnie opcjonalnie wpisać opis w **opis** , a następnie kliknij przycisk **dalej**.
6. Na **zabezpieczenia dystrybucji** wybierz opcję **bezpieczniejsze**.
7. Kliknij pozycję **Utwórz**.  

Teraz, gdy konto Uruchom jako jest tworzone, musi na docelowych serwerach zarządzania w grupie zarządzania i skojarzonych z wstępnie zdefiniowanych profilu Uruchom jako, więc przepływy pracy zostanie uruchomiona przy użyciu poświadczeń.  

1. W obszarze **Konfiguracja Uruchom jako**, **kont**, w okienku wyników kliknij dwukrotnie konto, została utworzona wcześniej.
2. Na **dystrybucji** kliknij pozycję **Dodaj** dla **wybrane komputery** pole, a następnie dodać serwer zarządzania do konta w celu dystrybucji.  Kliknij przycisk **OK** dwa razy, aby zapisać zmiany.
3. W obszarze **Konfiguracja Uruchom jako**, kliknij przycisk **profile**.
4. Wyszukaj *profilu Ocena oprogramowania SCOM*.
5. Nazwa profilu, powinny być: *Microsoft System Center Operations Manager Health Check profil Uruchom jako*.
6. Kliknij prawym przyciskiem myszy i zaktualizuj jego właściwości i dodać ostatnio utworzone konto Uruchom jako utworzone wcześniej.

### <a name="sql-script-to-grant-granular-permissions-to-the-run-as-account"></a>Skrypt SQL w celu przyznania szczegółowych uprawnień do konta Uruchom jako

Uruchom następujący skrypt SQL udzielenia wymaganych uprawnień do konta Uruchom jako w wystąpieniu programu SQL Server używany przez menedżera operacji, które hostuje operacyjną, magazynu danych i baza danych usług ACS.

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

### <a name="configure-the-health-check-rule"></a>Skonfiguruj regułę sprawdzania kondycji

Pakiet administracyjny rozwiązania System Center Operations Manager Health Check zawiera reguły o nazwie *Microsoft systemu Centrum Operations Manager uruchom Sprawdź Reguła kondycji*. Ta reguła jest odpowiedzialny za działanie sprawdzania kondycji. Aby włączyć regułę i skonfiguruj częstotliwość, użyj poniższej procedury.

Domyślnie program Microsoft System Centrum Operations Manager uruchom Sprawdź reguły kondycji jest wyłączona. Aby uruchomić sprawdzenie kondycji, należy włączyć reguły na serwerze zarządzania. Wykonaj następujące kroki.

#### <a name="enable-the-rule-for-a-specific-management-server"></a>Włącz regułę dla określonego serwera zarządzania

1. W **tworzenie** obszar roboczy w konsoli operacje programu Operations Manager, wyszukaj regułę *Microsoft systemu Centrum Operations Manager uruchom Sprawdź Reguła kondycji* w **reguły** okienka.
2. W wynikach wyszukiwania, wybierz jedną, która zawiera tekst *typu: Serwer zarządzania*.
3. Kliknij prawym przyciskiem myszy regułę, a następnie kliknij przycisk **zastępuje** > **dla konkretnego obiektu klasy: Serwer zarządzania**.
4.  Na liście serwerów dostępnych zarządzania wybierz serwer zarządzania, w którym reguła ma zostać uruchomiona.  Powinna to być ten sam serwer zarządzania, przypisując mu skonfigurowane wcześniej skojarzyć konto Uruchom jako z.
5.  Upewnij się, że możesz zmienić wartość zastąpienia na **True** dla **włączone** wartość parametru.<br><br> ![Zastąp parametr](./media/scom-assessment/rule.png)

    Nadal w tym oknie, należy skonfigurować częstotliwość uruchamiania, wykonując następną procedurę.

#### <a name="configure-the-run-frequency"></a>Skonfiguruj częstotliwość wykonywania

Ocena jest domyślnie skonfigurowana do uruchamiania co 10 080 minut (lub siedmiu dni). Możesz zastąpić wartość minimalna wartość 1440 minut (lub jeden dzień). Wartość reprezentuje przerwy minimalny czas między oceny kolejnych przebiegów. Aby zastąpić interwału, użyj kroków poniżej.

1. W **tworzenie** obszar roboczy w konsoli programu Operations Manager, wyszukaj regułę *Microsoft systemu Centrum Operations Manager uruchom Sprawdź Reguła kondycji* w **reguły** sekcja.
2. W wynikach wyszukiwania, wybierz jedną, która zawiera tekst *typu: Serwer zarządzania*.
3. Kliknij prawym przyciskiem myszy regułę, a następnie kliknij przycisk **Zastąp zasadę** > **dla wszystkich obiektów klasy: Serwer zarządzania**.
4. Zmiana **interwał** wartość parametru na wartość żądany przedział. W poniższym przykładzie wartość jest równa 1440 minut (jeden dzień).<br><br> ![Parametr interwału](./media/scom-assessment/interval.png)<br>  

    Jeśli wartość jest ustawiona na mniej niż 1440 minut, gdy zasada jest wykonywana w odstępach czasu w ciągu jednego dnia. W tym przykładzie reguła ignoruje wartość interwału i jest uruchamiany z częstotliwością jeden dzień.


## <a name="understanding-how-recommendations-are-prioritized"></a>Zrozumienie, jak zalecenia są uszeregowane według priorytetów

Każdy zalecenie wprowadzone otrzymuje wartość wagi, która identyfikuje względnego zalecenia. 10 najważniejszych zalecenia są wyświetlane.

### <a name="how-weights-are-calculated"></a>Jak są obliczane wagi

Wagi są wartości zagregowane, oparte na trzech kluczowych czynników:

- *Prawdopodobieństwo* czy problemu może powodować problemy. Większe prawdopodobieństwo równa większe, ogólny wynik zalecenia.
- *Wpływ* problemu w Twojej organizacji, jeśli to spowodować problem. Większy wpływ równa większe, ogólny wynik zalecenia.
- *Nakład pracy* wymagane do wykonania zalecenia. Wyższe nakład pracy jest równa mniejszych, ogólny wynik zalecenia.

Wagi dla każde zalecenie jest wyrażona jako procent łączny wynik dostępne dla każdego obszaru. Na przykład jeśli zalecenie w obszarze fokus dostępność i ciągłość prowadzenia działalności biznesowej ma wynik % 5, zaimplementowaniu tej rekomendacji zwiększa ogólną dostępność i ciągłość prowadzenia działalności biznesowej % ocena, 5.

### <a name="focus-areas"></a>Obszarach zainteresowań

**Dostępność i ciągłość prowadzenia działalności biznesowej** — ten obszar koncentracji uwagi przedstawiono zalecenia dotyczące dostępności usług, odporności infrastruktury i ochrony działalności.

**Wydajność i skalowalność** — ten obszar koncentracji uwagi zawiera zalecenia, aby pomóc Twojej organizacji infrastruktury IT, rozwoju, upewnij się, że środowisko IT spełnia bieżące wymagania dotyczące wydajności i może reagować na zmieniające się infrastruktury wymagania związane z.

**Uaktualnianie, migracja i wdrażanie** — ten obszar koncentracji uwagi zawiera zalecenia ułatwiające uaktualnienie i migrację oraz wdrożenie programu SQL Server w istniejącej infrastrukturze.

**Operacje i monitorowanie** — ten obszar koncentracji uwagi zawiera zalecenia, aby usprawnić operacji IT, implementować konserwacji zapobiegawczej i zwiększyć wydajność.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Należy dążyć do oceniania 100% w każdym obszarze koncentracji uwagi?

Niekoniecznie. Zalecenia są oparte na wiedzy i doświadczenia zebrane przez inżynierów firmy Microsoft w tysiącach wizyt klienta. Nie dwóch serwerów infrastruktury, są takie same i zalecenia może być bardziej lub mniej istotne dla Ciebie. Na przykład niektóre zalecenia dotyczące zabezpieczeń może być mniej istotne, jeśli maszyny wirtualne nie są połączone z Internetem. Niektóre zalecenia dotyczące dostępności może być mniej istotne dla usługi, które zapewniają zbierania danych ad hoc — niski priorytet i raportowania. Problemy, które są ważne do dojrzałego może być mniej ważne uruchomieniowy. Można zidentyfikować obszary koncentracji uwagi, które mają priorytetów i przyjrzyj się jak wyniki zmieniają się wraz z upływem czasu.

Każdy zalecenie obejmuje wskazówki na temat ważnych. Skorzystać z poniższych wskazówek, aby ocenić, czy implementacja zalecenie jest odpowiednia dla Ciebie, biorąc pod uwagę rodzaj usług IT i potrzeby biznesowe Twojej organizacji.

## <a name="use-health-check-focus-area-recommendations"></a>Sprawdzanie kondycji Użyj zaleceń obszar koncentracji uwagi

Korzystać z rozwiązania sprawdzania kondycji, w usłudze Log Analytics, musisz mieć zainstalowane oprogramowanie. Aby dowiedzieć się więcej o instalowaniu rozwiązania, zobacz [zainstalować rozwiązanie do zarządzania](../../azure-monitor/insights/solutions.md). Po jego zainstalowaniu, można wyświetlić podsumowanie zalecenia za pomocą kafelka System Center Operations Manager Health Check na **Przegląd** strony dla obszaru roboczego w witrynie Azure portal.

Wyświetlanie ocen zgodności podsumowania dla Twojej infrastruktury, a następnie wejdź do zalecenia.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Aby wyświetlić zalecenia dotyczące obszar koncentracji uwagi i podejmij działania naprawcze
1. Zaloguj się do witryny Azure Portal na stronie [https://portal.azure.com](https://portal.azure.com).
2. W witrynie Azure Portal kliknij pozycję **Więcej usług** w lewym dolnym rogu. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Log Analytics**.
3. W okienku subskrypcji usługi Log Analytics wybierz obszar roboczy, a następnie kliknij przycisk **podsumowanie obszaru roboczego** elementu menu.  
4. Na **Przegląd** kliknij **System Center Operations Manager Health Check** kafelka.
5. Na **System Center Operations Manager Health Check** strony, przejrzyj dane podsumowania w jednym z bloków obszaru fokus, a następnie kliknij jedną, aby wyświetlić zalecenia dla tego obszaru.
6. Na wszystkich stronach obszar koncentracji uwagi można wyświetlać zaleceń z priorytetami wprowadzone dla danego środowiska. Kliknij przycisk rekomendacji w obszarze **wpływ na obiekty** Aby wyświetlić szczegóły dotyczące Dlaczego tworzone są zalecenia.<br><br> ![obszar koncentracji](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-02.png)<br>
7. Można wykonać akcje naprawcze sugerowane w **sugerowane akcje**. Jeśli element został rozwiązany, oceny nowszych zarejestruje które zalecane akcje zostały wykonane i zwiększy ocenę zgodności. Poprawiony elementy są wyświetlane jako **obiektów przekazywane**.

## <a name="ignore-recommendations"></a>Zignoruj zalecenia

Jeśli masz zaleceń, które chcesz zignorować, można utworzyć plik tekstowy, który używa usługi Log Analytics, aby zapobiegać zaleceń znajdujących się w wynikach oceny.

### <a name="to-identify-recommendations-that-you-want-to-ignore"></a>Aby zidentyfikować zaleceń, które chcesz zignorować
1. W witrynie Azure portal na stronie obszaru roboczego usługi Log Analytics dla wybranego obszaru roboczego kliknij **wyszukiwanie w dzienniku** elementu menu.
2. Użyj następującego zapytania do zaleceń listy, które nie powiodły, na komputerach w danym środowisku.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Jeśli obszar roboczy został uaktualniony do [nowych zapytań usługi Log Analytics język](../../azure-monitor/log-query/log-query-overview.md), a następnie powyższe zapytania zmienią się następujące czynności.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    Poniżej przedstawiono zrzut ekranu przedstawiający zapytaniu przeszukiwania dzienników:<br><br> ![przeszukiwanie dzienników](./media/scom-assessment/scom-log-search.png)<br>

3. Wybierz zaleceń, które chcesz zignorować. Użyjesz wartości RecommendationId w następnej procedurze.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Tworzenie i używanie pliku tekstowego IgnoreRecommendations.txt

1. Utwórz plik o nazwie IgnoreRecommendations.txt.
2. Wklej lub wpisz każdy RecommendationId dla każdego zalecenia mają Log Analytics, aby zignorować w osobnym wierszu i następnie zapisz i zamknij plik.
3. Umieść plik w następującym folderze na każdym komputerze, w której chcesz zignorować zalecenia w usłudze Log Analytics.
4. Na serwerze zarządzania programu Operations Manager — *SystemDrive*: System Center 2012 R2\Operations Manager\Server \Program Files\Microsoft.

### <a name="to-verify-that-recommendations-are-ignored"></a>Aby zweryfikować, że zalecenia są ignorowane

1. Po następnego zaplanowanego uruchomienia oceny domyślnie co siedem dni, określonego zalecenia są oznaczone jako ignorowane i nie będą wyświetlane na pulpicie nawigacyjnym sprawdzania kondycji.
2. Aby wyświetlić listę wszystkich zaleceń dotyczących zignorowane, można użyć następujących zapytań przeszukiwania dzienników.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    >[!NOTE]
    > Jeśli obszar roboczy został uaktualniony do [nowych zapytań usługi Log Analytics język](../../azure-monitor/log-query/log-query-overview.md), a następnie powyższe zapytania zmienią się następujące czynności.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Ignore" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

3. Jeśli użytkownik zdecyduje później chcesz wyświetlone zalecenia zignorowane, Usuń wszystkie pliki IgnoreRecommendations.txt lub RecommendationIDs można usunąć z nich.

## <a name="system-center-operations-manager-health-check-solution-faq"></a>Rozwiązania System Center Operations Manager Health Check — często zadawane pytania

*Sprawdzanie kondycji rozwiązania I dodane do mojego obszaru roboczego usługi Log Analytics. Nie widzę zaleceń. Dlaczego nie?* Po dodaniu rozwiązania, użyj następującego widoku kroki zaleceń na pulpicie nawigacyjnym usługi Log Analytics.  

- [Ustaw konto Uruchom jako dla System Center Operations Manager Health Check](#operations-manager-run-as-accounts-for-log-analytics)  
- [Skonfiguruj regułę System Center Operations Manager Health Check](#configure-the-health-check-rule)


*Czy istnieje sposób, aby skonfigurować częstotliwość uruchamiania wyboru?* Tak. Zobacz [skonfigurować częstotliwość uruchamiania](#configure-the-run-frequency).

*Jeśli inny serwer został odnaleziony, po rozwiązania System Center Operations Manager Health Check zostały dodane, zostanie on sprawdzony?* Tak, po odnajdywaniu, który jest sprawdzany od tego momentu domyślnie co siedem dni.

*Co to jest nazwa procesu, który wykonuje zbierania danych?* AdvisorAssessment.exe

*Gdzie uruchomiony proces AdvisorAssessment.exe?* AdvisorAssessment.exe uruchamiana procesu usługi kondycji serwera zarządzania, w której włączono regułę sprawdzania kondycji. Za pomocą tego procesu, odnajdywanie całego środowiska odbywa się za pośrednictwem zdalne zbieranie danych.

*Jak długo trwa do zbierania danych?* Zbieranie danych na serwerze, trwa około jednej godziny. Może trwać dłużej w środowiskach, które mają wiele wystąpień programu Operations Manager lub baz danych.

*Co zrobić, jeśli ustawić interwał oceny do mniej niż 1440 minut?* Ocena jest wstępnie skonfigurowana do uruchamiania w więcej niż raz dziennie. Jeśli zastąpisz wartość interwału do wartości mniejszej niż 1440 minut oceny używa 1440 minut jako wartość interwału.

*Jak sprawdzić, czy istnieją błędy?* Jeśli uruchomiono sprawdzania kondycji i nie zostaną wyświetlone wyniki, wówczas jest prawdopodobne, że niektóre wymagania wstępne dotyczące wyboru nie powiodło się. Można wykonać zapytania: `Operation Solution=SCOMAssessment` i `SCOMAssessmentRecommendation FocusArea=Prerequisites` podczas wyszukiwania dziennika, aby zobaczyć wymagań wstępnych nie powiodło się.

*Brak `Failed to connect to the SQL Instance (….).` komunikat błędy wymagań wstępnych. Na czym polega problem?* AdvisorAssessment.exe, proces, który służy do zbierania danych, działa w ramach procesu usługi kondycji na serwerze zarządzania. W ramach kontroli kondycji proces próbuje nawiązać połączenie z serwerem SQL, w którym znajduje się baza danych programu Operations Manager. Ten błąd może wystąpić, jeśli reguły zapory na blokowanie połączenie z wystąpieniem programu SQL Server.

*Jakiego typu dane są zbierane?* Zbierane są następujące typy danych: - WMI - rejestru danych — danych w dzienniku zdarzeń — Operations Manager danych za pomocą programu Windows PowerShell, zapytania SQL oraz plik moduł zbierający informacje o.

*Dlaczego trzeba skonfigurować konto Uruchom jako?* Za pomocą programu Operations Manager działają różne zapytania SQL. W kolejności ich do uruchomienia należy użyć konta Uruchom jako przy użyciu wystarczających uprawnień. Ponadto poświadczenia administratora lokalnego są wymagane do wykonywania zapytań usługi WMI.

*Dlaczego są wyświetlane tylko 10 najlepszych zaleceń?* Zamiast daje wyczerpująca, przytłaczająca listę zadań, zaleca się skupić się na temat zaleceń z priorytetami najpierw. Po ich wykonaniu, dodatkowe zalecenia staną się dostępne. Jeśli wolisz wyświetlić szczegółową listę, możesz wyświetlić wszystkie zalecenia za pomocą wyszukiwania w dziennikach.

*Czy istnieje sposób, aby zignorować zalecenie?* Tak, zobacz [Zignoruj zalecenia](#ignore-recommendations).


## <a name="next-steps"></a>Kolejne kroki

- [Przeszukiwanie dzienników](../../azure-monitor/log-query/log-query-overview.md) Aby dowiedzieć się, jak analizować szczegółowe dane usługi System Center Operations Manager Health Check i zalecenia.
