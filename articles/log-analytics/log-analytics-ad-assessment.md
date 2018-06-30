---
title: Optymalizacja środowiskiem usługi Active Directory na platformie Azure Log Analytics | Dokumentacja firmy Microsoft
description: Active Directory kondycji Sprawdź rozwiązania można użyć do oceny ryzyka i kondycji środowisk w regularnych odstępach czasu.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 81eb41b8-eb62-4eb2-9f7b-fde5c89c9b47
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/27/2017
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 5b9bd5bf21f378f92b7dd6e027d1bb0c05620dd6
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128847"
---
# <a name="optimize-your-active-directory-environment-with-the-active-directory-health-check-solution-in-log-analytics"></a>Optymalizowanie środowiska usługi Active Directory z rozwiązaniem Active Directory kondycji Sprawdź w analizy dzienników

![Sprawdzanie kondycji AD — symbol](./media/log-analytics-ad-assessment/ad-assessment-symbol.png)

Active Directory kondycji Sprawdź rozwiązania można użyć do oceny ryzyka i kondycji środowisk serwera w regularnych odstępach czasu. Ten artykuł pomoże Ci zainstalować i używać rozwiązania, dzięki czemu można podjąć działania naprawcze potencjalnych problemów z.

To rozwiązanie zapewnia priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożone. Zalecenia są podzielone na cztery obszarach zainteresowań, które pomagają w szybkim świadomość ryzyka i podejmij akcję.

Zalecenia są oparte na wiedzy i doświadczenia przez pracownicy firmy Microsoft z tysięcy wizyt klienta. Każde zalecenie znajdują się wskazówki dotyczące przyczyny problemu może być uwzględniana i implementowania sugerowane zmiany.

Można wybrać obszarach zainteresowań, które są najbardziej ważne dla organizacji i śledzić postęp w kierunku uruchomionym środowiskiem ryzyka bezpłatne i działa prawidłowo.

Po dodaniu rozwiązania i sprawdzenie jest zakończone, podsumowanie informacji o obszarach zainteresowań jest wyświetlany na **sprawdzania kondycji AD** pulpitu nawigacyjnego dla infrastruktury w danym środowisku. W poniższych sekcjach opisano sposób użyć informacji na temat **sprawdzania kondycji AD** pulpitu nawigacyjnego, gdzie można przeglądać i wykonaj zalecane akcje dotyczące infrastruktury serwera usługi Active Directory.  

![Obraz sprawdzania kondycji AD kafelka](./media/log-analytics-ad-assessment/ad-healthcheck-summary-tile.png)

![Obraz sprawdzania kondycji AD pulpitu nawigacyjnego](./media/log-analytics-ad-assessment/ad-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Wymagania wstępne

* Active Directory kondycji Sprawdź rozwiązanie wymaga obsługiwanej wersji programu .NET Framework 4.5.2 lub powyżej zainstalowane na każdym komputerze, który ma Microsoft Monitoring Agent (MMA) zainstalowany.  MMA agent jest używany przez System Center 2016 - Operations Manager i Operations Manager 2012 R2 i usługą analizy dzienników.
* Rozwiązanie obsługuje kontrolery domeny z systemem Windows Server 2008 i 2008 R2, Windows Server 2012 i 2012 R2 i Windows Server 2016.
* Obszar roboczy analizy dzienników można dodać z poziomu portalu Azure marketplace w portalu Azure Active Directory kondycji Sprawdź rozwiązania.  Nie są wymagane żadne dalsze czynności konfiguracyjne.

  > [!NOTE]
  > Po dodaniu rozwiązania, plik AdvisorAssessment.exe jest dodawany do serwerów z agentami. Dane konfiguracji jest do odczytu i następnie wysyłane do analizy dzienników usługi w chmurze do przetwarzania. Logika jest stosowany do odebranych danych i usługi w chmurze rejestruje dane.
  >
  >

Aby przeprowadzić sprawdzenie kondycji na kontrolerach domeny, które są członkami domeny, która ma zostać obliczone, wymagają one agenta i łączności do analizy dzienników przy użyciu jednej z następujących obsługiwanych metod:

1. Zainstaluj [Microsoft Monitoring Agent (MMA)](log-analytics-windows-agent.md) Jeśli kontroler domeny nie jest już monitorowany przez System Center 2016 - Operations Manager lub programu Operations Manager 2012 R2.
2. Jeśli grupa zarządzania nie jest zintegrowany z usługą analizy dzienników jest monitorowany z System Center 2016 - Operations Manager lub programu Operations Manager 2012 R2, kontroler domeny może być wieloadresowego z analizy dzienników do zbierania danych i przekazywać je do usługi i nadal monitorowane przez program Operations Manager.  
3. W przeciwnym razie, jeśli grupę zarządzania programu Operations Manager jest zintegrowany z usługą, należy dodać kontrolerów domeny na potrzeby zbierania danych przez usługę następujące kroki w obszarze [dodać komputery zarządzane z wykorzystaniem agentów](log-analytics-om-agents.md#connecting-operations-manager-to-log-analytics) po włączeniu rozwiązanie w obszarze roboczym.  

Agenta na kontrolerze domeny, które raporty z grupą zarządzania programu Operations Manager umożliwia zbieranie danych, przekazuje do serwera zarządzania przypisany, a następnie wysyłane bezpośrednio z serwerem zarządzania z usługą analizy dzienników.  Dane nie są zapisywane do baz danych programu Operations Manager.  

## <a name="active-directory-health-check-data-collection-details"></a>Active Directory kondycji Sprawdź szczegóły danych w kolekcji

Active Directory kondycji Sprawdź zbiera dane z następujących źródeł przy użyciu agenta, która została włączona:

- Rejestr
- LDAP
- .NET Framework
- Dziennik zdarzeń
- Usługa Active Directory interfejsy (ADSI)
- Windows PowerShell
- Plik danych
- Instrumentacja zarządzania Windows (WMI)
- Narzędzie DCDIAG interfejsu API
- Interfejs API usługi (NTFRS) replikacji plików
- Kod niestandardowy C#

Dane są zbierane na kontrolerze domeny i przekazywane do analizy dzienników co siedem dni.  

## <a name="understanding-how-recommendations-are-prioritized"></a>Opis sposobu mają pierwszeństwo zalecenia
Każdy zalecenie wprowadzone otrzymuje wartość wagi, która identyfikuje względnego zalecenia. 10 najważniejszych zalecenia są wyświetlane.

### <a name="how-weights-are-calculated"></a>Jak są obliczane wagi
Wagi są wartości zagregowanych oparte na trzech kluczowych czynników:

* *Prawdopodobieństwo* czy problemu powoduje występowanie problemów. Większe prawdopodobieństwo równa większych ogólny wynik dla zalecenia.
* *Wpływ* problemu w Twojej organizacji, jeśli spowodować problem. Większy wpływ równa większych ogólny wynik dla zalecenia.
* *Nakładu* wymagane do wykonania zalecenia. Wyższy nakładu równa mniejszych ogólny wynik dla zalecenia.

Wagi dla każde zalecenie jest wyrażona jako procent całkowitej wynik dostępne dla każdego obszaru fokus. Na przykład jeśli zalecenia w obszarze fokus zabezpieczeń i zgodności ma wynik % 5, wdrażanie tego zalecenia zwiększa z ogólną % wynik przez 5 zabezpieczeń i zgodności.

### <a name="focus-areas"></a>Obszarach zainteresowań
**Zabezpieczeń i zgodności** — w tym obszarze fokus zawiera zalecenia dotyczące potencjalnego zagrożenia bezpieczeństwa i naruszeń, zasad firmowych i wymagania techniczne, prawne i przepisami zgodności.

**Dostępność i ciągłość prowadzenia działalności biznesowej** — w tym obszarze fokus zawiera zalecenia dotyczące odporności infrastruktury i ochronę biznesowej, dostępności usług.

**Wydajność i skalowalność** — w tym obszarze fokus zawiera zalecenia ułatwiające organizacji infrastruktury IT zwiększania, upewnij się, że spełnia bieżące wymagania dotyczące wydajności środowiska informatycznego i jest w stanie reagować na zmieniające się infrastruktury wymagania związane z.

**Uaktualniania, wdrażania i migracji** — w tym obszarze fokus zawiera zalecenia ułatwiające uaktualniania, migracji i wdrożyć istniejącą infrastrukturę usługi Active Directory.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Należy dążyć wynik 100% w każdym obszarze fokus?
Niekoniecznie. Zalecenia są oparte na wiedzy i doświadczeń zdobytych Microsoft inżynierowie między tysięcy wizyt klienta. Jednak nie infrastruktury serwerowe są takie same i szczegółowe zalecenia mogą być bardziej lub mniej istotne dla Ciebie. Na przykład niektóre zalecenia dotyczące zabezpieczeń może być mniej ważne, jeśli maszyny wirtualne nie są widoczne w Internecie. Kilka zaleceń dostępności może być mniej istotne dla usług, które umożliwiają zbieranie danych ad hoc — niski priorytet i raportowania. Problemy, które są ważne dla dorosłych firm może być mniej ważne do rozruchu. Można zidentyfikować obszary fokus, które mają zebranych i przyjrzyj się jak zmienić wyniki wraz z upływem czasu.

Każdy zalecenie obejmuje wskazówek dotyczących Dlaczego ważne jest. Do oceny, czy wdrażanie zalecenie jest odpowiednie dla Ciebie, biorąc pod uwagę rodzaj usług informatycznych i potrzeb biznesowych w organizacji, należy skorzystać z poniższych wskazówek.

## <a name="use-health-check-focus-area-recommendations"></a>Sprawdzanie kondycji Użyj zaleceń obszaru fokus
Po jego zainstalowaniu, można wyświetlić podsumowanie zaleceń za pomocą kafelka kondycji Sprawdź na stronie rozwiązania w portalu Azure.

Wyświetl oceny zgodności podsumowania dla Twojej infrastruktury, a następnie wejdź do zalecenia.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Aby wyświetlić zalecenia dla obszaru fokus i podjąć działania naprawcze
3. Kliknij przycisk **omówienie** kafelku obszaru roboczego analizy dzienników w portalu Azure.
4. Na **omówienie** kliknij przycisk **Active Directory kondycji Sprawdź** kafelka.
5. Na **sprawdzania kondycji** strony, sprawdź informacje w jednym z bloków obszaru fokus, a następnie kliknij przycisk jedna, aby wyświetlić zalecenia dla tego obszaru fokus.
6. Na wszystkich stronach obszaru fokus można wyświetlić priorytetową zalecenia dla danego środowiska. Kliknij zalecenie, w obszarze **dotyczy obiektów** Aby wyświetlić szczegóły dotyczące Dlaczego tworzone są zalecenia.<br><br> ![obraz kondycji Sprawdź zalecenia](./media/log-analytics-ad-assessment/ad-healthcheck-dashboard-02.png)
7. Należy wykonać działania naprawcze sugerowane w **sugerowanych akcji**. Jeśli element został rozwiązany, nowsze rekordy oceny zalecane akcje zostały pobrane i zwiększy wynik zgodności. Poprawione elementy są wyświetlane jako **przekazany obiektów**.

## <a name="ignore-recommendations"></a>Ignoruj zalecenia
Jeśli masz zaleceń, które chcesz zignorować, można utworzyć pliku tekstowego, który będzie użyć analizy dzienników, aby zapobiec zaleceń znajdujących się w wynikach oceny.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Aby zidentyfikować zaleceń, które będzie ignorować
1. W portalu Azure na stronie obszaru roboczego analizy dzienników dla Twojego wybranego obszaru roboczego kliknij **wyszukiwania dziennika** kafelka.
2. Użyj następującego zapytania do listy zaleceń, które nie powiodły, na komputerach w danym środowisku.

    ```
    ADAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
    Poniżej przedstawiono zrzut ekranu przedstawiający zapytania wyszukiwania dziennika:<br><br> ![zalecenia nie powiodło się](./media/log-analytics-ad-assessment/ad-failed-recommendations.png)

3. Wybierz zaleceń, które chcesz zignorować. Użyjesz wartości RecommendationId w następnej procedurze.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Tworzenie i używanie IgnoreRecommendations.txt pliku tekstowego
1. Utwórz plik o nazwie IgnoreRecommendations.txt.
2. Wklej lub wpisz każdego RecommendationId dla każde zalecenie, które mają analizy dzienników można zignorować w osobnym wierszu, a następnie zapisz i zamknij plik.
3. Umieść plik w następującym folderze na każdym komputerze, w którym ma analizy dzienników zignorowanie zalecenia.
   * Na komputerach z usługą Microsoft Monitoring Agent (połączony bezpośrednio lub za pośrednictwem programu Operations Manager) - *SystemDrive*: \Program Files\Microsoft Agent\Agent monitorowania
   * Na serwerze zarządzania programu Operations Manager 2012 R2 — *SystemDrive*: System Center 2012 R2\Operations Manager\Server \Program Files\Microsoft
   * Na serwerze zarządzania programu Operations Manager 2016 - *SystemDrive*: \Program Files\Microsoft Manager\Server 2016\Operations programu System Center

### <a name="to-verify-that-recommendations-are-ignored"></a>Aby zweryfikować, że zalecenia są ignorowane
Po Następne zaplanowane przebiegów sprawdzania kondycji, domyślnie co siedem dni, określonego zalecenia są oznaczone *zignorowane* i nie będą wyświetlane na pulpicie nawigacyjnym.

1. Następujące zapytania wyszukiwania dziennika służy do tworzenia listy wszystkich zignorowane zalecenia.

    ```
    ADAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```

2. Jeśli później zdecydujesz chcesz zobaczyć zignorowane zalecenia dotyczące, Usuń wszystkie pliki IgnoreRecommendations.txt lub RecommendationIDs można usunąć z nich.

## <a name="ad-health-check-solutions-faq"></a>Kondycji AD wyboru rozwiązań — często zadawane pytania
*Częstotliwość sprawdzania kondycji czy działa?*

* Test jest uruchamiany co siedem dni.

*Czy istnieje sposób skonfigurować częstotliwość sprawdzania kondycji działa?*

* Nie w tej chwili.

*Jeśli inny serwer zostanie odnaleziony po zostały dodane jako rozwiązanie do sprawdzania kondycji, będzie można sprawdzić*

* Tak, gdy okaże się, że jest zaznaczona z następnie, co siedem dni.

*Jeśli serwer jest likwidowany, gdy zostanie ono zostać usunięte z sprawdzenie kondycji?*

* Jeśli serwer nie przedstawi danych do 3 tygodni, zostanie ono usunięte.

*Co to jest nazwa procesu, który wykonuje zbierania danych?*

* AdvisorAssessment.exe

*Jak długo trwa do zebrania danych?*

* Kolekcja rzeczywistymi danymi na serwerze trwa około 1 godziny. Może trwać dłużej na serwerach, które mają wiele serwerów usługi Active Directory.

*Czy istnieje sposób konfigurowania, gdy dane są zbierane?*

* Nie w tej chwili.

*Dlaczego są wyświetlane tylko zalecenia 10 pierwszych?*

* Zamiast daje utrudnione kompletnej zadań, zaleca się skupić się na najpierw adresowania priorytetową zalecenia. Po adresu im dodatkowe zalecenia staną się dostępne. Jeśli wolisz wyświetlić listę szczegółowych, można wyświetlić wszystkie zalecenia za pomocą wyszukiwania dziennika.

*Czy istnieje sposób, aby zignorować zalecenie?*

* Tak, zobacz [zignorowanie zalecenia](#ignore-recommendations) powyższej sekcji.

## <a name="next-steps"></a>Kolejne kroki
* Użyj [Zaloguj wyszukiwania analizy dzienników](log-analytics-log-searches.md) Aby dowiedzieć się, jak analizować szczegółowe dane AD sprawdzania kondycji i zaleceń.
