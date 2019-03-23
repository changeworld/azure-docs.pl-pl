---
title: Optymalizuj środowisko programu SQL Server z usługą Azure Log Analytics | Dokumentacja firmy Microsoft
description: Za pomocą usługi Azure Log Analytics można użyć rozwiązania SQL Health Check do oceny ryzyka i kondycji środowisk w regularnych odstępach czasu.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: e297eb57-1718-4cfe-a241-b9e84b2c42ac
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: magoedte
ms.openlocfilehash: e8c06f0a3a33133c7b1595db52204d15b03d6aab
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372475"
---
# <a name="optimize-your-sql-environment-with-the-sql-server-health-check-solution-in-log-analytics"></a>Optymalizowanie środowiska SQL za pomocą rozwiązania sprawdzanie kondycji serwera SQL w usłudze Log Analytics

![Sprawdzanie kondycji SQL symboli](./media/sql-assessment/sql-assessment-symbol.png)

Rozwiązanie SQL Health Check można użyć do oceny ryzyka i kondycji środowisk serwera w regularnych odstępach czasu. Ten artykuł pomoże można zainstalować rozwiązania, można wykonać akcje naprawcze o potencjalnych problemach.

To rozwiązanie zapewnia priorytetową listą zalecenia dotyczące infrastruktury serwerowej wdrożone. Zalecenia są podzielone w sześciu obszarach zainteresowań, które pomagają w szybkim zrozumieć ryzyko i podejmij działania naprawcze.

Zalecenia są oparte na wiedzy i doświadczenia zdobyte przez inżynierów firmy Microsoft z tysięcy wizyt klienta. Każdy zalecenie zawiera wskazówki na temat przyczyny problemu może być ważne i jak implementować sugerowane zmiany.

Możesz wybrać obszary koncentracji uwagi, które są najważniejsze dla Twojej organizacji i śledzenie postępów uruchamiania środowiska ryzyka bezpłatne i w dobrej kondycji.

Po dodaniu rozwiązania i ocena jest zakończone, podsumowanie informacje o obszarach zainteresowań jest wyświetlany na **SQL Health Check** pulpit nawigacyjny dla infrastruktury w danym środowisku. W poniższych sekcjach opisano sposób użycia informacji o **SQL Health Check** pulpitu nawigacyjnego, w którym można wyświetlać i następnie podjąć zalecane akcje dla infrastruktury programu SQL Server.

![Obraz przedstawiający Kafelek SQL Health Check](./media/sql-assessment/sql-healthcheck-summary-tile.png)

![Obraz przedstawiający pulpit nawigacyjny z SQL Health Check](./media/sql-assessment/sql-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Wymagania wstępne

* Rozwiązanie SQL Health Check wymaga obsługiwanej wersji programu .NET Framework 4, zainstalowane na każdym komputerze, który zawiera program Microsoft Monitoring Agent (MMA) zainstalowane.  MMA agent jest używana przez System Center 2016 — Operations Manager i Operations Manager 2012 R2 oraz usługi Log Analytics.  
* Rozwiązanie obsługuje program SQL Server w wersji 2012, 2014 i 2016.
* Obszar roboczy usługi Log Analytics można dodać rozwiązania SQL Health Check z witryny Azure marketplace w witrynie Azure portal.  Aby zainstalować rozwiązania, musi być administratorem lub współautorem w subskrypcji platformy Azure.

  > [!NOTE]
  > Po dodaniu rozwiązania pliku AdvisorAssessment.exe jest dodawane do serwerów przy użyciu agentów. Dane konfiguracji jest odczytać i następnie wysyłane do usługi Log Analytics w chmurze do przetwarzania. Logika jest stosowana do odebranych danych i usługi w chmurze rejestruje dane.
  >
  >

Aby przeprowadzić sprawdzenie kondycji serwerów programu SQL Server, wymagają agenta i łączności z usługą Log Analytics przy użyciu jednej z następujących obsługiwanych metod:

1. Zainstaluj [Microsoft Monitoring Agent (MMA)](../../azure-monitor/platform/agent-windows.md) Jeśli serwer nie jest już monitorowane przez program System Center 2016 — Operations Manager lub Operations Manager 2012 R2.
2. Jeśli grupa zarządzania nie jest zintegrowana z usługą Log Analytics jest monitorowana przy użyciu programu System Center 2016 — Operations Manager lub Operations Manager 2012 R2, serwer może być wieloadresowe z usługą Log Analytics, aby zbierać dane i przekazywać je do usługi i nadal monitorowane przez program Operations Manager.  
3. W przeciwnym razie, jeśli grupa zarządzania programu Operations Manager jest zintegrowany z usługą, należy dodać kontrolerów domeny do zbierania danych przez usługę, wykonaj czynności w ramach [dodać komputery zarządzane z wykorzystaniem agentów](../../azure-monitor/platform/om-agents.md#connecting-operations-manager-to-azure-monitor) po włączeniu rozwiązania w Twoim obszarze roboczym.  

Agent na serwerze SQL, które raporty z grupą zarządzania programu Operations Manager służy do zbierania danych, przekazuje do jego serwera zarządzania w przypisanej, a następnie będą wysyłane bezpośrednio z serwera zarządzania do usługi Log Analytics.  Dane nie są zapisywane w bazach danych programu Operations Manager.  

Jeśli program SQL Server jest monitorowane przez program Operations Manager, należy skonfigurować konto usługi Operations Manager uruchom jako. Zobacz [programu Operations Manager uruchom jako konta usługi Log Analytics](#operations-manager-run-as-accounts-for-log-analytics) poniżej Aby uzyskać więcej informacji.

## <a name="sql-health-check-data-collection-details"></a>Sprawdzanie kondycji SQL szczegółów kolekcji danych
Sprawdzanie kondycji SQL zbiera dane z następujących źródeł przy użyciu agenta, które mają włączone:

* Instrumentacja zarządzania Windows (WMI)
* Rejestr
* Liczniki wydajności
* Wyniki widoku dynamicznego zarządzania programu SQL Server

Dane są zbierane w programie SQL Server i przekazywane do usługi Log Analytics, co siedem dni.

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Uruchom jako konta programu Operations Manager dla usługi Log Analytics
Usługa log Analytics używa grupy zarządzania i agent programu Operations Manager do zbierania i wysyłania danych do usługi Log Analytics. Log Analytics kompilacji od pakietów administracyjnych dla obciążeń w celu zapewnienia wartości — Dodawanie usług. Każde obciążenie wymaga uprawnień specyficznego dla obciążenia do uruchamiania pakietów administracyjnych w innym kontekście bezpieczeństwa, takie jak konto użytkownika domeny. Konieczne jest zapewnienie informacji o poświadczeniach, konfigurując konto Operations Manager uruchom jako.

Skorzystaj z poniższych informacji, aby ustawić konto Operations Manager uruchom jako dla SQL Health Check.

### <a name="set-the-run-as-account-for-sql-health-check"></a>Ustaw konto Uruchom jako dla SQL Health Check
 Jeśli używane są pakiet administracyjny programu SQL Server, należy użyć tej konfiguracji Uruchom jako.

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>Aby skonfigurować konto SQL Uruchom jako w konsoli operacje
> [!NOTE]
> Domyślnie przepływy pracy w pakiecie administracyjnym jest uruchamiany w kontekście zabezpieczeń konta System lokalny. Jeśli używasz programu Microsoft Monitoring Agent podłączony bezpośrednio do usługi zamiast raportowania bezpośrednio do grupy zarządzania programu Operations Manager, należy pominąć kroki od 1 do 5 poniżej i uruchomić T-SQL lub programu PowerShell, określając NT AUTHORITY\SYSTEM jako Nazwa użytkownika.
>
>

1. W programie Operations Manager, otwórz konsolę operacje, a następnie kliknij przycisk **administracji**.
2. W obszarze **Konfiguracja Uruchom jako**, kliknij przycisk **profile**i Otwórz **SQL oceny profilu Uruchom jako**.
3. Na **konta Uruchom jako** kliknij **Dodaj**.
4. Wybierz konto Uruchom jako Windows, który zawiera poświadczenia wymagane dla programu SQL Server, lub kliknij przycisk **New** ją utworzyć.

   > [!NOTE]
   > Typ konta Uruchom jako musi być Windows. Konto Uruchom jako musi być również częścią lokalnej grupy administratorów na wszystkich serwerach Windows hostingu wystąpienia programu SQL Server.
   >
   >
5. Kliknij pozycję **Zapisz**.
6. Modyfikuj, a następnie wykonaj w poniższym przykładzie języka T-SQL w każdym wystąpieniu programu SQL Server do udzielania minimalne uprawnienia wymagane dla konta Uruchom jako do sprawdzania kondycji. Jednakże nie trzeba to zrobić, jeśli konto Uruchom jako jest już częścią roli serwera sysadmin w wystąpieniach programu SQL Server.

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
Otwórz okno programu PowerShell i uruchom następujący skrypt, po zaktualizowaniu z informacjami:

```
    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"

    $profile = Get-SCOMRunAsProfile -DisplayName "SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## <a name="understanding-how-recommendations-are-prioritized"></a>Zrozumienie, jak zalecenia są uszeregowane według priorytetów
Każdy zalecenie wprowadzone otrzymuje wartość wagi, która identyfikuje względnego zalecenia. Dziesięć najważniejszych zalecenia są wyświetlane.

### <a name="how-weights-are-calculated"></a>Jak są obliczane wagi
Wagi są wartości zagregowane, oparte na trzech kluczowych czynników:

* *Prawdopodobieństwo* czy problemu może powodować problemy. Większe prawdopodobieństwo równa większe, ogólny wynik zalecenia.
* *Wpływ* problemu w Twojej organizacji, jeśli to spowodować problem. Większy wpływ równa większe, ogólny wynik zalecenia.
* *Nakład pracy* wymagane do wykonania zalecenia. Wyższe nakład pracy jest równa mniejszych, ogólny wynik zalecenia.

Wagi dla każde zalecenie jest wyrażona jako procent łączny wynik dostępne dla każdego obszaru. Na przykład jeśli zalecenie w obszarze zabezpieczeń i zgodności fokus ma wynik % 5, zaimplementowaniu tej rekomendacji zwiększy ogólnej % ocena, 5 zabezpieczeń i zgodności.

### <a name="focus-areas"></a>Obszarach zainteresowań
**Zabezpieczenia i zgodność** — ten obszar koncentracji uwagi przedstawiono zalecenia dotyczące potencjalne zagrożenia bezpieczeństwa i naruszeń zasad firmowych i wymagania techniczne, prawne i przepisami zgodności.

**Dostępność i ciągłość prowadzenia działalności biznesowej** — ten obszar koncentracji uwagi przedstawiono zalecenia dotyczące dostępności usług, odporności infrastruktury i ochrony działalności.

**Wydajność i skalowalność** — ten obszar koncentracji uwagi zawiera zalecenia, aby pomóc Twojej organizacji infrastruktury IT, rozwoju, upewnij się, że środowisko IT spełnia bieżące wymagania dotyczące wydajności i może reagować na zmieniające się infrastruktury wymagania związane z.

**Uaktualnianie, wdrażania i migracji** — ten obszar koncentracji uwagi zawiera zalecenia ułatwiające uaktualnienie i migrację oraz wdrożenie programu SQL Server w istniejącej infrastrukturze.

**Operacje i monitorowanie** — ten obszar koncentracji uwagi zawiera zalecenia, aby usprawnić operacji IT, implementować konserwacji zapobiegawczej i zwiększyć wydajność.

**Zarządzanie zmianami i konfiguracją** — ten obszar koncentracji uwagi przedstawiono zalecenia dotyczące ochrony codziennej pracy, należy zapewnić, że zmiany nie negatywny mają wpływ na infrastrukturę, ustanowić procedury sterowania zmianami i do śledzenia i inspekcji konfiguracje systemu.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Należy dążyć do oceniania 100% w każdym obszarze koncentracji uwagi?
Niekoniecznie. Zalecenia są oparte na wiedzy i doświadczenia zebrane przez inżynierów firmy Microsoft w tysiącach wizyt klienta. Nie dwóch serwerów infrastruktury, są takie same i zalecenia może być bardziej lub mniej istotne dla Ciebie. Na przykład niektóre zalecenia dotyczące zabezpieczeń może być mniej istotne, jeśli maszyny wirtualne nie są połączone z Internetem. Niektóre zalecenia dotyczące dostępności może być mniej istotne dla usługi, które zapewniają zbierania danych ad hoc — niski priorytet i raportowania. Problemy, które są ważne do dojrzałego może być mniej ważne uruchomieniowy. Można zidentyfikować obszary koncentracji uwagi, które mają priorytetów i przyjrzyj się jak wyniki zmieniają się wraz z upływem czasu.

Każdy zalecenie obejmuje wskazówki na temat ważnych. Do oceny, czy implementacja zalecenie jest odpowiednia dla Ciebie, biorąc pod uwagę rodzaj usług IT i potrzeby biznesowe Twojej organizacji, należy skorzystać z poniższych wskazówek.

## <a name="use-health-check-focus-area-recommendations"></a>Sprawdzanie kondycji zalecenia obszar koncentracji uwagi
W usłudze Log Analytics można korzystać z rozwiązania do oceny, musisz mieć zainstalowane oprogramowanie.  Po jego zainstalowaniu, możesz wyświetlić podsumowanie zaleceń przy użyciu SQL Health Check kafelka na stronie rozwiązania w witrynie Azure portal.

Wyświetlanie ocen zgodności podsumowania dla Twojej infrastruktury, a następnie wejdź do zalecenia.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Aby wyświetlić zalecenia dotyczące obszar koncentracji uwagi i podejmij działania naprawcze
1. Zaloguj się do witryny Azure Portal na stronie [https://portal.azure.com](https://portal.azure.com).
2. W witrynie Azure Portal kliknij pozycję **Więcej usług** w lewym dolnym rogu. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Log Analytics**.
3. W okienku subskrypcji usługi Log Analytics wybierz obszar roboczy, a następnie kliknij przycisk **Przegląd** kafelka.  
4. Na **Przegląd** kliknij **SQL Health Check** kafelka.
5. Na **sprawdzanie kondycji** strony, przejrzyj dane podsumowania w jednym z bloków obszaru fokus, a następnie kliknij jedną, aby wyświetlić zalecenia dla tego obszaru.
6. Na wszystkich stronach obszar koncentracji uwagi można wyświetlać zaleceń z priorytetami wprowadzone dla danego środowiska. Kliknij przycisk rekomendacji w obszarze **wpływ na obiekty** Aby wyświetlić szczegóły dotyczące Dlaczego tworzone są zalecenia.<br><br> ![Obraz przedstawiający zalecenia SQL Health Check](./media/sql-assessment/sql-healthcheck-dashboard-02.png)<br>
7. Można wykonać akcje naprawcze sugerowane w **sugerowane akcje**. Jeśli element został rozwiązany, oceny nowszych zarejestruje które zalecane akcje zostały wykonane i zwiększy ocenę zgodności. Poprawiony elementy są wyświetlane jako **obiektów przekazywane**.

## <a name="ignore-recommendations"></a>Zignoruj zalecenia
Jeśli masz zaleceń, które chcesz zignorować, można utworzyć plik tekstowy, który usługi Log Analytics będzie używany do zapobiegania zaleceń znajdujących się w wynikach oceny.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Aby zidentyfikować zaleceń, które będzie ignorować
1. W witrynie Azure portal na stronie obszaru roboczego usługi Log Analytics dla wybranego obszaru roboczego kliknij **wyszukiwanie w dzienniku** kafelka.
2. Użyj następującego zapytania do zaleceń listy, które nie powiodły, na komputerach w danym środowisku.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Jeśli obszar roboczy został uaktualniony do [nowych zapytań usługi Log Analytics język](../../azure-monitor/log-query/log-query-overview.md), a następnie powyższe zapytania zmienią się następujące czynności.
    >
    > `SQLAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    Poniżej przedstawiono zrzut ekranu przedstawiający zapytaniu przeszukiwania dzienników:<br><br> ![zalecenia nie powiodło się](./media/sql-assessment/sql-assess-failed-recommendations.png)<br>

3. Wybierz zaleceń, które chcesz zignorować. Użyjesz wartości RecommendationId w następnej procedurze.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Tworzenie i używanie pliku tekstowego IgnoreRecommendations.txt
1. Utwórz plik o nazwie IgnoreRecommendations.txt.
2. Wklej lub wpisz każdy RecommendationId dla każdego zalecenia mają Log Analytics, aby zignorować w osobnym wierszu i następnie zapisz i zamknij plik.
3. Umieść plik w następującym folderze na każdym komputerze, w której chcesz zignorować zalecenia w usłudze Log Analytics.
   * Na komputerach za pomocą programu Microsoft Monitoring Agent (połączenie bezpośrednio lub za pośrednictwem programu Operations Manager) - *SystemDrive*: \Program Files\Microsoft Monitoring Agent\Agent
   * Na serwerze zarządzania programu Operations Manager — *SystemDrive*: System Center 2012 R2\Operations Manager\Server \Program Files\Microsoft
   * Na serwerze zarządzania programu Operations Manager 2016 - *SystemDrive*: \Program Files\Microsoft programu System Center 2016\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Aby zweryfikować, że zalecenia są ignorowane
1. Po następnego zaplanowanego uruchomienia oceny domyślnie co 7 dni, określonego zalecenia są oznaczone jako ignorowane i nie będą wyświetlane na pulpicie nawigacyjnym oceny.
2. Aby wyświetlić listę wszystkich zaleceń dotyczących zignorowane, można użyć następujących zapytań przeszukiwania dzienników.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Ignored | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Jeśli obszar roboczy został uaktualniony do [nowych zapytań usługi Log Analytics język](../../azure-monitor/log-query/log-query-overview.md), a następnie powyższe zapytania zmienią się następujące czynności.
    >
    > `SQLAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

3. Jeśli użytkownik zdecyduje później chcesz wyświetlone zalecenia zignorowane, Usuń wszystkie pliki IgnoreRecommendations.txt lub RecommendationIDs można usunąć z nich.

## <a name="sql-health-check-solution-faq"></a>Rozwiązanie SQL Health Check — często zadawane pytania
*Jak często kontrola kondycji czy działa?*

* Test jest uruchamiany co siedem dni.

*Czy istnieje sposób, aby skonfigurować częstotliwość uruchamiania wyboru?*

* Nie w tej chwili.

*Jeśli inny serwer został odnaleziony, po rozwiązania SQL Health Check zostały dodane, zostanie on sprawdzony?*

* Tak, gdy okaże się, że jest zaznaczona od czasu, co siedem dni.

*Jeśli zlikwidować serwer gdy zostanie ono zostać usunięte z kontroli kondycji?*

* Jeśli serwer nie przedstawi danych dla 3 tygodnie, jego usunięcie.

*Co to jest nazwa procesu, który wykonuje zbierania danych?*

* AdvisorAssessment.exe

*Jak długo trwa dla danych, które mają być zbierane?*

* Kolekcja rzeczywistych danych na serwerze zajmuje około godzinę. Może trwać dłużej na serwerach, które mają dużą liczbę baz danych lub wystąpienia programu SQL.

*Jakiego typu dane są zbierane?*

* Zbierane są następujące typy danych:
  * WMI
  * Rejestr
  * Liczniki wydajności
  * SQL dynamicznych widoków zarządzania (DMV).

*Czy istnieje sposób, aby skonfigurować po zebraniu danych?*

* Nie w tej chwili.

*Dlaczego trzeba skonfigurować konto Uruchom jako?*

* Dla programu SQL Server są uruchamiane niewielkiej liczby zapytań SQL. Aby je uruchomić należy użyć konta Uruchom jako z uprawnieniami VIEW SERVER STATE do bazy danych SQL.  Ponadto aby kwerendy WMI, wymagane są poświadczenia administratora lokalnego.

*Dlaczego są wyświetlane tylko 10 najlepszych zaleceń?*

* Zamiast daje wyczerpujący przytłaczająca listę zadań, zaleca się skupić się na temat zaleceń z priorytetami najpierw. Po ich wykonaniu, dodatkowe zalecenia staną się dostępne. Jeśli wolisz wyświetlić szczegółową listę, możesz wyświetlić wszystkie zalecenia przy użyciu przeszukiwania dzienników usługi Log Analytics.

*Czy istnieje sposób, aby zignorować zalecenie?*

* Tak, zobacz [Zignoruj zalecenia](#ignore-recommendations) powyższej sekcji.

## <a name="next-steps"></a>Kolejne kroki
* [Przeszukiwanie dzienników](../../azure-monitor/log-query/log-query-overview.md) Aby dowiedzieć się, jak analizować szczegółowe dane SQL Health Check i zalecenia.
