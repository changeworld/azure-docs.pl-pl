---
title: Optymalizuj środowisko usługi Active Directory za pomocą usługi Azure Monitor | Dokumenty firmy Microsoft
description: Za pomocą rozwiązania Sprawdzania kondycji usługi Active Directory można regularnie oceniać ryzyko i kondycję środowisk.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2019
ms.openlocfilehash: 57c474c8391168702154b71e0c454253ab921dc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667231"
---
# <a name="optimize-your-active-directory-environment-with-the-active-directory-health-check-solution-in-azure-monitor"></a>Optymalizowanie środowiska usługi Active Directory za pomocą rozwiązania Active Directory Health Check w usłudze Azure Monitor

![Symbol sprawdzania kondycji usługi AD](./media/ad-assessment/ad-assessment-symbol.png)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Za pomocą rozwiązania Sprawdzania kondycji usługi Active Directory można regularnie oceniać ryzyko i kondycję środowisk serwerowych. Ten artykuł ułatwia instalowanie i używanie rozwiązania, dzięki czemu można podjąć działania naprawcze w przypadku potencjalnych problemów.

To rozwiązanie zawiera priorytetową listę zaleceń specyficznych dla wdrożonej infrastruktury serwera. Zalecenia są podzielone na cztery obszary fokusowe, które pomagają szybko zrozumieć ryzyko i podjąć działania.

Zalecenia są oparte na wiedzy i doświadczeniu zdobytym przez inżynierów firmy Microsoft podczas tysięcy wizyt klientów. Każde zalecenie zawiera wskazówki dotyczące przyczyn, które mogą mieć znaczenie dla Ciebie i jak zaimplementować sugerowane zmiany.

Możesz wybrać obszary fokusu, które są najważniejsze dla Twojej organizacji i śledzić swoje postępy w prowadzeniu środowiska wolnego od ryzyka i zdrowego.

Po dodaniu rozwiązania i zakończeniu sprawdzania informacje podsumowujące obszary fokusu są wyświetlane na pulpicie nawigacyjnym **sprawdzania kondycji usługi AD** dla infrastruktury w twoim środowisku. W poniższych sekcjach opisano sposób używania informacji na pulpicie nawigacyjnym **sprawdzania kondycji usługi AD,** na którym można wyświetlać, a następnie podejmować zalecane akcje dla infrastruktury serwera usługi Active Directory.  

![obraz kafelka sprawdzanie kondycji usługi AD](./media/ad-assessment/ad-healthcheck-summary-tile.png)

![obraz pulpitu nawigacyjnego sprawdzania kondycji usługi AD](./media/ad-assessment/ad-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Wymagania wstępne

* Rozwiązanie do sprawdzania kondycji usługi Active Directory wymaga obsługiwanej wersji programu .NET Framework 4.6.2 lub nowszej zainstalowanej na każdym komputerze z zainstalowanym agentem usługi Log Analytics dla systemu Windows (zwanym również agentem microsoft monitoring agenta (MMA)).  Agent jest używany przez System Center 2016 — Operations Manager, Operations Manager 2012 R2 i Azure Monitor.
* Rozwiązanie obsługuje kontrolery domeny z systemami Windows Server 2008 i 2008 R2, Windows Server 2012 i 2012 R2 oraz Windows Server 2016.
* Obszar roboczy usługi Log Analytics w celu dodania rozwiązania do sprawdzania kondycji usługi Active Directory z portalu Azure w portalu Azure. Nie jest wymagana żadna dodatkowa konfiguracja.

  > [!NOTE]
  > Po dodaniu rozwiązania plik AdvisorAssessment.exe jest dodawany do serwerów z agentami. Dane konfiguracji są odczytywane, a następnie wysyłane do usługi Azure Monitor w chmurze do przetwarzania. Logika jest stosowana do odebranych danych, a usługa w chmurze rejestruje dane.
  >
  >

Aby przeprowadzić sprawdzanie kondycji kontrolerów domeny, które są członkami domeny, które mają zostać ocenione, każdy kontroler domeny w tej domenie wymaga agenta i łączności z usługą Azure Monitor przy użyciu jednej z następujących obsługiwanych metod:

1. Zainstaluj [agenta usługi Log Analytics dla systemu Windows,](../../azure-monitor/platform/agent-windows.md) jeśli kontroler domeny nie jest jeszcze monitorowany przez system Center 2016 — Program Operations Manager lub Program Operations Manager 2012 R2.
2. Jeśli jest monitorowany za pomocą programu System Center 2016 — Program Operations Manager lub Program Operations Manager 2012 R2, a grupa zarządzania nie jest zintegrowana z usługą Azure Monitor, kontroler domeny może być wielozadaniowy z usługą Azure Monitor w celu zbierania danych i przesyłania dalej do usługi i nadal monitorowane przez operations managera.  
3. W przeciwnym razie jeśli grupa zarządzania programu Operations Manager jest zintegrowana z usługą, należy dodać kontrolery domeny do zbierania danych przez usługę, wykonując kroki opisane w obszarze [dodawanie komputerów zarządzanych przez agenta](../../azure-monitor/platform/om-agents.md#connecting-operations-manager-to-azure-monitor) po włączeniu rozwiązania w obszarze roboczym.  

Agent na kontrolerze domeny, który raportuje grupę zarządzania programu Operations Manager, zbiera dane, przekazuje do przypisanego mu serwera zarządzania, a następnie jest wysyłany bezpośrednio z serwera zarządzania do usługi Azure Monitor.  Dane nie są zapisywane w bazach danych programu Operations Manager.  

## <a name="active-directory-health-check-data-collection-details"></a>Szczegóły zbierania danych w rozwiązaniu Active Directory Health Check

Sprawdzanie kondycji usługi Active Directory zbiera dane z następujących źródeł przy użyciu włączonego agenta:

- Rejestr
- LDAP
- .NET Framework
- Dziennik zdarzeń
- Interfejsy usługi Active Directory (ADSI)
- Windows PowerShell
- Dane pliku
- Instrumentacja zarządzania Windows (WMI)
- Interfejs API narzędzia DCDIAG
- Interfejs API usługi replikacji plików (NTFRS)
- Niestandardowy kod języka C#

Dane są zbierane na kontrolerze domeny i przekazywane do usługi Azure Monitor co siedem dni.  

## <a name="understanding-how-recommendations-are-prioritized"></a>Opis priorytetyzacji zaleceń

Każde wydane zalecenie otrzymuje wartość ważącą, która określa względne znaczenie zalecenia. Pokazano tylko 10 najważniejszych zaleceń.

### <a name="how-weights-are-calculated"></a>Sposób obliczania wag

Współczynniki kory'u są wartościami zagregowanymi na podstawie trzech kluczowych czynników:

* *Prawdopodobieństwo,* że zidentyfikowany problem powoduje problemy. Większe prawdopodobieństwo równa się większe ogólny wynik dla zalecenia.
* *Wpływ* problemu na organizację, jeśli powoduje to problem. Większy wpływ oznacza większy ogólny wynik dla zalecenia.
* *Wysiłek* niezbędny do wdrożenia zalecenia. Większy wysiłek oznacza mniejszy ogólny wynik zalecenia.

Waga dla każdego zalecenia jest wyrażona jako procent całkowitego wyniku dostępnego dla każdego obszaru ostrości. Jeśli na przykład zalecenie w obszarze fokus bezpieczeństwa i zgodności ma wynik 5%, wdrożenie tego zalecenia zwiększa ogólny wynik zabezpieczeń i zgodności o 5%.

### <a name="focus-areas"></a>Obszary fokusu

**Bezpieczeństwo i zgodność** — ten obszar ostrości zawiera zalecenia dotyczące potencjalnych zagrożeń i naruszeń zabezpieczeń, zasad firmowych oraz wymagań technicznych, prawnych i regulacyjnych dotyczących zgodności.

**Dostępność i ciągłość działania** — ten obszar ostrości zawiera zalecenia dotyczące dostępności usług, odporności infrastruktury i ochrony biznesowej.

**Wydajność i skalowalność** — ten obszar ostrości zawiera zalecenia, które pomogą w rozwoju infrastruktury IT w organizacji, upewnią się, że środowisko IT spełnia bieżące wymagania dotyczące wydajności i jest w stanie reagować na zmieniające się potrzeby infrastrukturalne.

**Uaktualnianie, migracja i wdrażanie** — w tym obszarze fokusu przedstawiono zalecenia ułatwiające uaktualnianie, migrację i wdrażanie usługi Active Directory w istniejącej infrastrukturze.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Czy należy dążyć do osiągnięcia wyniku 100% w każdym obszarze fokusu?

Niekoniecznie. Zalecenia są oparte na wiedzy i doświadczeniach zdobytych przez inżynierów firmy Microsoft podczas tysięcy wizyt klientów. Jednak nie dwie infrastruktury serwera są takie same, a konkretne zalecenia mogą być mniej lub bardziej istotne dla Ciebie. Na przykład niektóre zalecenia dotyczące zabezpieczeń mogą być mniej istotne, jeśli maszyny wirtualne nie są udostępniane w Internecie. Niektóre zalecenia dotyczące dostępności mogą być mniej istotne dla usług, które zapewniają gromadzenie i raportowanie danych ad hoc o niskim priorytecie. Kwestie, które są ważne dla dojrzałego biznesu, mogą być mniej ważne dla start-upu. Możesz określić, które obszary fokusu są Twoimi priorytetami, a następnie sprawdzić, jak twoje wyniki zmieniają się w czasie.

Każde zalecenie zawiera wskazówki, dlaczego jest to ważne. Należy użyć tych wskazówek, aby ocenić, czy wdrożenie zalecenia jest odpowiednie dla Ciebie, biorąc pod uwagę charakter usług IT i potrzeby biznesowe organizacji.

## <a name="use-health-check-focus-area-recommendations"></a>Korzystanie z zaleceń dotyczących obszaru fokusu sprawdzania kondycji

Po zainstalowaniu można wyświetlić podsumowanie zaleceń przy użyciu kafelka Sprawdzanie kondycji na stronie rozwiązania w witrynie Azure portal.

Wyświetl podsumowane oceny zgodności dla infrastruktury, a następnie przejdź do szczegółów zaleceń.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Aby wyświetlić zalecenia dotyczące obszaru fokusu i podjąć działania naprawcze

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

1. Na stronie **Przegląd** kliknij kafelek **Sprawdzanie kondycji usługi Active Directory.**

2. Na stronie **Sprawdzanie kondycji** przejrzyj informacje podsumowujące w jednym z bloków obszaru fokusu, a następnie kliknij jeden z nich, aby wyświetlić zalecenia dotyczące tego obszaru fokusu.

3. Na dowolnej stronie obszaru fokusu można wyświetlić priorytetowe zalecenia dla środowiska. Kliknij zalecenie w obszarze **Obiekty, których dotyczy problem,** aby wyświetlić szczegółowe informacje o tym, dlaczego zalecenie zostało sformułowane.

    ![obraz zaleceń dotyczących kontroli kondycji](./media/ad-assessment/ad-healthcheck-dashboard-02.png)

4. W obszarze **Sugerowane działania**można podjąć działania naprawcze sugerowane . Po zajmaniu się elementem, późniejsze rekordy ocen, które zostały podjęte zalecane działania i wynik zgodności wzrośnie. Poprawione elementy są wyświetlane jako **przekazane obiekty**.

## <a name="ignore-recommendations"></a>Ignorowanie zaleceń

Jeśli masz zalecenia, które chcesz zignorować, można utworzyć plik tekstowy, który będzie używany przez usługę Azure Monitor, aby zapobiec pojawianiu się zaleceń w wynikach oceny.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Aby zidentyfikować zalecenia, które zignorujesz

[!INCLUDE [azure-monitor-log-queries](../../../includes/azure-monitor-log-queries.md)]

Poniższa kwerenda służy do listy zaleceń, które nie powiodły się dla komputerów w twoim środowisku.

```
ADAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
```

Oto zrzut ekranu przedstawiający kwerendę dziennika:<

![nieudane zalecenia](media/ad-assessment/ad-failed-recommendations.png)

Wybierz zalecenia, które chcesz zignorować. Użyjesz wartości dla RecommendationId w następnej procedurze.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Aby utworzyć plik tekstowy IgnoreRecommenddations.txt i używać go

1. Utwórz plik o nazwie IgnoreRecommendations.txt.

2. Wklej lub wpisz każdy obiekt RecommendationId dla każdego zalecenia, które chcesz, aby usługa Azure Monitor zignorowała w osobnym wierszu, a następnie zapisz i zamknij plik.

3. Umieść plik w następującym folderze na każdym komputerze, na którym chcesz, aby usługa Azure Monitor ignorowała zalecenia.

   * Na komputerach z agentem microsoft monitoringu (połączonym bezpośrednio lub za pośrednictwem programu Operations Manager) — *SystemDrive*:\Program Files\Microsoft Monitoring Agent\Agent
   * Na serwerze zarządzania programu Operations Manager 2012 R2 — *SystemDrive*:\Program Files\Microsoft System Center 2012 R2\Operations Manager\Server
   * Na serwerze zarządzania programu Operations Manager 2016 — *SystemDrive*:\Program Files\Microsoft System Center 2016\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Aby sprawdzić, czy zalecenia są ignorowane

Po uruchomieniu następnego zaplanowanego sprawdzania kondycji, domyślnie co siedem dni, określone zalecenia są oznaczone *jako Ignorowane* i nie będą wyświetlane na pulpicie nawigacyjnym.

1. Za pomocą następujących kwerend dziennika można wyświetlić listę wszystkich zignorowanych zaleceń.

    ```
    ADAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```

2. Jeśli później zdecydujesz, że chcesz zobaczyć zignorowane zalecenia, usuń wszystkie pliki IgnoreRecommendations.txt lub możesz usunąć z nich identyfikatory rekomendacji.

## <a name="ad-health-check-solutions-faq"></a>Rozwiązania AD Health Check — często zadawane pytania

*Jakie kontrole są przeprowadzane przez rozwiązanie oceny usługi AD?*

* Następująca kwerenda zawiera opis wszystkich aktualnie wykonywanych kontroli:

```Kusto
ADAssessmentRecommendation
| distinct RecommendationId, FocusArea, ActionArea, Recommendation, Description
| sort by FocusArea,ActionArea, Recommendation
```
Wyniki można następnie wyeksportować do programu Excel w celu dalszego przeglądu.

*Jak często przebiega sprawdzanie kondycji?*

* Kontrola odbywa się co siedem dni.

*Czy istnieje sposób, aby skonfigurować, jak często działa sprawdzanie kondycji?*

* Nie w tej chwili.

*Jeśli po dodaniu rozwiązania do sprawdzania kondycji zostanie wykryty inny serwer, zostanie on sprawdzony*

* Tak, po odkryciu jest sprawdzany od tego czasu, co siedem dni.

*Jeśli serwer zostanie wycofany z eksploatacji, kiedy zostanie usunięty z kontroli kondycji?*

* Jeśli serwer nie przesyła danych przez 3 tygodnie, zostanie on usunięty.

*Jaka jest nazwa procesu, który zbiera dane?*

* DoradcaAssessment.exe

*Jak długo trwa gromadzenie danych?*

* Rzeczywiste zbieranie danych na serwerze trwa około 1 godziny. Może to potrwać dłużej na serwerach, które mają dużą liczbę serwerów usługi Active Directory.

*Czy istnieje sposób konfigurowania podczas zbierania danych?*

* Nie w tej chwili.

*Dlaczego wyświetlać tylko 10 najlepszych rekomendacji?*

* Zamiast podawać wyczerpującą przytłaczającą listę zadań, zalecamy skupienie się najpierw na rozwiązaniu priorytetowych zaleceń. Po ich rozwiązaniu dostępne będą dodatkowe zalecenia. Jeśli wolisz zobaczyć szczegółową listę, możesz wyświetlić wszystkie zalecenia za pomocą kwerendy dziennika.

*Czy istnieje sposób, aby zignorować zalecenie?*

* Tak, zobacz [Sekcję Ignoruj zalecenia](#ignore-recommendations) powyżej.

## <a name="next-steps"></a>Następne kroki

Użyj [zapytań dziennika usługi Azure Monitor,](../log-query/log-query-overview.md) aby dowiedzieć się, jak analizować szczegółowe dane i zalecenia dotyczące sprawdzania kondycji usługi AD.
