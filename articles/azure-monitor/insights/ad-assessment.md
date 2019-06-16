---
title: Optymalizuj środowisko usługi Active Directory z usługą Azure Monitor | Dokumentacja firmy Microsoft
description: Rozwiązanie kondycja Sprawdzanie usługi Active Directory można użyć do oceny ryzyka i kondycji środowisk w regularnych odstępach czasu.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 81eb41b8-eb62-4eb2-9f7b-fde5c89c9b47
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/27/2017
ms.author: magoedte
ms.openlocfilehash: 3b5da6c9046fc694bd5eb0f55cf031b82b6d0103
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60919834"
---
# <a name="optimize-your-active-directory-environment-with-the-active-directory-health-check-solution-in-azure-monitor"></a>Optymalizuj środowisko usługi Active Directory za pomocą rozwiązania Active Directory Health Check w usłudze Azure Monitor

![Symbol sprawdzanie kondycji usługi AD](./media/ad-assessment/ad-assessment-symbol.png)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Rozwiązanie kondycja Sprawdzanie usługi Active Directory umożliwia oceniaj ryzyko i kondycję środowisk serwera w regularnych odstępach czasu. Ten artykuł pomoże Ci Instalowanie i korzystanie z odpowiedniego rozwiązania, tak, aby można podjąć akcje naprawcze o potencjalnych problemach.

To rozwiązanie zapewnia priorytetową listą zalecenia dotyczące infrastruktury serwerowej wdrożone. Zalecenia są podzielone na cztery obszary koncentracji uwagi, które pomagają w szybkim zrozumieć ryzyko i podjąć działania.

Zalecenia są oparte na wiedzy i doświadczenia zdobyte przez inżynierów firmy Microsoft z tysięcy wizyt klienta. Każdy zalecenie zawiera wskazówki na temat przyczyny problemu może być ważne i jak implementować sugerowane zmiany.

Możesz wybrać obszary koncentracji uwagi, które są najważniejsze dla Twojej organizacji i śledzenie postępów uruchamiania środowiska ryzyka bezpłatne i w dobrej kondycji.

Po dodaniu rozwiązania i kontrola jest zakończone, podsumowanie informacje o obszarach zainteresowań jest wyświetlany na **sprawdzanie kondycji AD** pulpit nawigacyjny dla infrastruktury w danym środowisku. W poniższych sekcjach opisano sposób użycia informacji o **sprawdzanie kondycji AD** pulpitu nawigacyjnego, w którym można wyświetlać i następnie podjąć zalecane akcje dla infrastruktury serwera usługi Active Directory.  

![Obraz przedstawiający Kafelek sprawdzanie kondycji usługi AD](./media/ad-assessment/ad-healthcheck-summary-tile.png)

![Obraz przedstawiający pulpit nawigacyjny z sprawdzanie kondycji usługi AD](./media/ad-assessment/ad-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Wymagania wstępne

* Rozwiązanie kondycja Sprawdzanie usługi Active Directory wymaga obsługiwanej wersji programu .NET Framework 4.5.2 lub powyżej zainstalowane na każdym komputerze, który zawiera program Microsoft Monitoring Agent (MMA) zainstalowane.  MMA agent jest używana przez System Center 2016 — Operations Manager i Operations Manager 2012 R2 oraz usługi Azure Monitor.
* Rozwiązanie obsługuje kontrolery domeny z systemem Windows Server 2008 i 2008 R2, Windows Server 2012 i 2012 R2 i Windows Server 2016.
* Obszar roboczy usługi Log Analytics można dodać rozwiązania Active Directory Health Check z witryny Azure marketplace w witrynie Azure portal.  Nie są wymagane żadne dalsze czynności konfiguracyjne.

  > [!NOTE]
  > Po dodaniu rozwiązania pliku AdvisorAssessment.exe jest dodawane do serwerów przy użyciu agentów. Dane konfiguracji jest odczytać i następnie wysyłane do usługi Azure Monitor w chmurze do przetwarzania. Logika jest stosowana do odebranych danych i usługi w chmurze rejestruje dane.
  >
  >

Aby sprawdzić kondycję na kontrolerach domeny, które są członkami domeny, która ma zostać obliczone, wymagają agenta i łączność z usługi Azure Monitor, przy użyciu jednej z następujących obsługiwanych metod:

1. Zainstaluj [Microsoft Monitoring Agent (MMA)](../../azure-monitor/platform/agent-windows.md) Jeśli kontroler domeny nie jest już monitorowane przez program System Center 2016 — Operations Manager lub Operations Manager 2012 R2.
2. Jeśli grupa zarządzania nie jest zintegrowana z usługą Azure Monitor jest monitorowana przy użyciu programu System Center 2016 — Operations Manager lub Operations Manager 2012 R2, kontroler domeny może być wieloadresowych z usługą Azure Monitor do zbierania danych i przekazywać je do usługi i nadal monitorowane przez program Operations Manager.  
3. W przeciwnym razie, jeśli grupa zarządzania programu Operations Manager jest zintegrowany z usługą, należy dodać kontrolerów domeny do zbierania danych przez usługę, wykonaj czynności w ramach [dodać komputery zarządzane z wykorzystaniem agentów](../../azure-monitor/platform/om-agents.md#connecting-operations-manager-to-azure-monitor) po włączeniu rozwiązania w Twoim obszarze roboczym.  

Agenta na kontrolerze domeny, które raporty z grupą zarządzania programu Operations Manager służy do zbierania danych, przekazuje do jego serwera zarządzania w przypisanej, a następnie będą wysyłane bezpośrednio z serwera zarządzania do usługi Azure Monitor.  Dane nie są zapisywane w bazach danych programu Operations Manager.  

## <a name="active-directory-health-check-data-collection-details"></a>Active Directory Health Check danych kolekcji uzyskać szczegółowe informacje

Active Directory Health Check zbiera dane z następujących źródeł przy użyciu agenta, które mają włączone:

- Rejestr
- LDAP
- .NET Framework
- Dziennik zdarzeń
- Usługa Active Directory interfejsy (ADSI)
- Windows PowerShell
- Dane pliku
- Instrumentacja zarządzania Windows (WMI)
- Narzędzie DCDIAG interfejsu API
- Interfejs API usługi (NTFRS) replikacji plików
- Kod niestandardowy C#

Dane są zbierane na kontrolerze domeny i przekazywane do usługi Azure Monitor co siedem dni.  

## <a name="understanding-how-recommendations-are-prioritized"></a>Zrozumienie, jak zalecenia są uszeregowane według priorytetów
Każdy zalecenie wprowadzone otrzymuje wartość wagi, która identyfikuje względnego zalecenia. 10 najważniejszych zalecenia są wyświetlane.

### <a name="how-weights-are-calculated"></a>Jak są obliczane wagi
Wagi są wartości zagregowane, oparte na trzech kluczowych czynników:

* *Prawdopodobieństwo* czy problemu powoduje występowanie problemów. Większe prawdopodobieństwo równa większe, ogólny wynik zalecenia.
* *Wpływ* problemu w Twojej organizacji, jeśli to spowodować problem. Większy wpływ równa większe, ogólny wynik zalecenia.
* *Nakład pracy* wymagane do wykonania zalecenia. Wyższe nakład pracy jest równa mniejszych, ogólny wynik zalecenia.

Wagi dla każde zalecenie jest wyrażona jako procent łączny wynik dostępne dla każdego obszaru. Na przykład jeśli zalecenie w obszarze zabezpieczeń i zgodności fokus ma wynik % 5, zaimplementowaniu tej rekomendacji zwiększa ogólną % ocena, 5 zabezpieczeń i zgodności.

### <a name="focus-areas"></a>Obszarach zainteresowań
**Zabezpieczenia i zgodność** — ten obszar koncentracji uwagi przedstawiono zalecenia dotyczące potencjalne zagrożenia bezpieczeństwa i naruszeń zasad firmowych i wymagania techniczne, prawne i przepisami zgodności.

**Dostępność i ciągłość prowadzenia działalności biznesowej** — ten obszar koncentracji uwagi przedstawiono zalecenia dotyczące dostępności usług, odporności infrastruktury i ochrony działalności.

**Wydajność i skalowalność** — ten obszar koncentracji uwagi zawiera zalecenia, aby pomóc Twojej organizacji infrastruktury IT, rozwoju, upewnij się, że środowisko IT spełnia bieżące wymagania dotyczące wydajności i może reagować na zmieniające się infrastruktury wymagania związane z.

**Uaktualnianie, wdrażania i migracji** — ten obszar koncentracji uwagi zawiera zalecenia ułatwiające uaktualniania, migracji i wdrożyć istniejącą infrastrukturę usługi Active Directory.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Należy dążyć do oceniania 100% w każdym obszarze koncentracji uwagi?
Niekoniecznie. Zalecenia są oparte na wiedzy i doświadczenia zebrane przez inżynierów firmy Microsoft w tysiącach wizyt klienta. Nie dwóch serwerów infrastruktury, są takie same i zalecenia może być bardziej lub mniej istotne dla Ciebie. Na przykład niektóre zalecenia dotyczące zabezpieczeń może być mniej istotne, jeśli maszyny wirtualne nie są połączone z Internetem. Niektóre zalecenia dotyczące dostępności może być mniej istotne dla usługi, które zapewniają zbierania danych ad hoc — niski priorytet i raportowania. Problemy, które są ważne do dojrzałego może być mniej ważne uruchomieniowy. Można zidentyfikować obszary koncentracji uwagi, które mają priorytetów i przyjrzyj się jak wyniki zmieniają się wraz z upływem czasu.

Każdy zalecenie obejmuje wskazówki na temat ważnych. Do oceny, czy implementacja zalecenie jest odpowiednia dla Ciebie, biorąc pod uwagę rodzaj usług IT i potrzeby biznesowe Twojej organizacji, należy skorzystać z poniższych wskazówek.

## <a name="use-health-check-focus-area-recommendations"></a>Sprawdzanie kondycji Użyj zaleceń obszar koncentracji uwagi
Po jego zainstalowaniu, możesz wyświetlić podsumowanie zalecenia za pomocą kafelka sprawdzanie kondycji na stronie rozwiązania w witrynie Azure portal.

Wyświetlanie ocen zgodności podsumowania dla Twojej infrastruktury, a następnie wejdź do zalecenia.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Aby wyświetlić zalecenia dotyczące obszar koncentracji uwagi i podejmij działania naprawcze
[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

1. Na **Przegląd** kliknij **Active Directory Health Check** kafelka.
1. Na **sprawdzanie kondycji** strony, przejrzyj dane podsumowania w jednym z bloków obszaru fokus, a następnie kliknij jedną, aby wyświetlić zalecenia dla tego obszaru.
1. Na wszystkich stronach obszar koncentracji uwagi można wyświetlać zaleceń z priorytetami wprowadzone dla danego środowiska. Kliknij przycisk rekomendacji w obszarze **wpływ na obiekty** Aby wyświetlić szczegóły dotyczące Dlaczego tworzone są zalecenia.<br><br> ![Obraz przedstawiający sprawdzanie kondycji zalecenia](./media/ad-assessment/ad-healthcheck-dashboard-02.png)
1. Można wykonać akcje naprawcze sugerowane w **sugerowane akcje**. Jeśli element został rozwiązany, nowsze rekordy ocen, które są zalecane akcje zostały wykonane i zwiększy ocenę zgodności. Poprawiony elementy są wyświetlane jako **obiektów przekazywane**.

## <a name="ignore-recommendations"></a>Zignoruj zalecenia
Jeśli masz zaleceń, które chcesz zignorować, można utworzyć plik tekstowy, który będzie użyć usługi Azure Monitor, aby zapobiec zaleceń znajdujących się w wynikach oceny.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Aby zidentyfikować zaleceń, które będzie ignorować
[!INCLUDE [azure-monitor-log-queries](../../../includes/azure-monitor-log-queries.md)]

Użyj następującego zapytania do zaleceń listy, które nie powiodły, na komputerach w danym środowisku.

```
ADAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
```

Poniżej przedstawiono zrzut ekranu przedstawiający zapytanie dziennika:<br><br> ![zalecenia nie powiodło się](media/ad-assessment/ad-failed-recommendations.png)

Wybierz zaleceń, które chcesz zignorować. Użyjesz wartości RecommendationId w następnej procedurze.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Tworzenie i używanie pliku tekstowego IgnoreRecommendations.txt
1. Utwórz plik o nazwie IgnoreRecommendations.txt.
2. Wklej lub wpisz każdy RecommendationId dla każdego zalecenia mają usługi Azure Monitor, aby zignorować w osobnym wierszu i następnie zapisz i zamknij plik.
3. Umieść plik w następującym folderze na każdym komputerze, którego usługi Azure Monitor, aby zignorować zalecenia.
   * Na komputerach za pomocą programu Microsoft Monitoring Agent (połączenie bezpośrednio lub za pośrednictwem programu Operations Manager) - *SystemDrive*: \Program Files\Microsoft Monitoring Agent\Agent
   * Na serwerze zarządzania programu Operations Manager 2012 R2 — *SystemDrive*: System Center 2012 R2\Operations Manager\Server \Program Files\Microsoft
   * Na serwerze zarządzania programu Operations Manager 2016 - *SystemDrive*: \Program Files\Microsoft programu System Center 2016\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Aby zweryfikować, że zalecenia są ignorowane
Po następnego zaplanowanego uruchomienia sprawdzania kondycji, domyślnie co siedem dni, są oznaczone określonym zalecenia *ignorowane* i nie będą widoczne na pulpicie nawigacyjnym.

1. Aby wyświetlić listę wszystkich zaleceń dotyczących zignorowane, można użyć następujących zapytań log.

    ```
    ADAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```

2. Jeśli użytkownik zdecyduje później chcesz wyświetlone zalecenia zignorowane, Usuń wszystkie pliki IgnoreRecommendations.txt lub RecommendationIDs można usunąć z nich.

## <a name="ad-health-check-solutions-faq"></a>AD Health — często zadawane pytania do wyboru rozwiązania
*Jak często kontrola kondycji czy działa?*

* Test jest uruchamiany co siedem dni.

*Czy istnieje sposób, aby skonfigurować częstotliwość uruchamiania sprawdzenie kondycji?*

* Nie w tej chwili.

*Jeśli inny serwer zostaje odnaleziony po zostały dodane jako rozwiązanie do kontroli kondycji, będzie można sprawdzić*

* Tak, gdy okaże się, że jest zaznaczona od czasu, co siedem dni.

*Jeśli zlikwidować serwer gdy zostanie ono zostać usunięte z kontroli kondycji?*

* Jeśli serwer nie przedstawi danych dla 3 tygodnie, jego usunięcie.

*Co to jest nazwa procesu, który wykonuje zbierania danych?*

* AdvisorAssessment.exe

*Jak długo trwa dla danych, które mają być zbierane?*

* Kolekcja rzeczywistych danych na serwerze zajmuje około godzinę. Może trwać dłużej na serwerach, które mają dużą liczbę serwerów usługi Active Directory.

*Czy istnieje sposób, aby skonfigurować po zebraniu danych?*

* Nie w tej chwili.

*Dlaczego są wyświetlane tylko 10 najlepszych zaleceń?*

* Zamiast daje wyczerpujący przytłaczająca listę zadań, zaleca się skupić się na temat zaleceń z priorytetami najpierw. Po ich wykonaniu, dodatkowe zalecenia staną się dostępne. Jeśli wolisz wyświetlić szczegółową listę, możesz wyświetlić wszystkie zalecenia za pomocą zapytania dziennika.

*Czy istnieje sposób, aby zignorować zalecenie?*

* Tak, zobacz [Zignoruj zalecenia](#ignore-recommendations) powyższej sekcji.

## <a name="next-steps"></a>Kolejne kroki
* Użyj [zapytań dzienników usługi Azure Monitor](../log-query/log-query-overview.md) Aby dowiedzieć się, jak analizować szczegółowe sprawdzanie kondycji AD danych i zalecenia.
