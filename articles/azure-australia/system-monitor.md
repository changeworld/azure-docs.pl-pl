---
title: Monitorowanie systemu pod kątem zabezpieczeń w Australii platformy Azure
description: Wskazówki dotyczące konfigurowania monitorowania systemu w regionach australijskich w celu spełnienia określonych wymagań australijskich zasad, przepisów i przepisów obowiązujących dla instytucji rządowych.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: f7f78dbde9810c8786e2344555444efabcc989b0
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779320"
---
# <a name="system-monitoring-for-security-in-azure-australia"></a>Monitorowanie systemu pod kątem zabezpieczeń w Australii platformy Azure

Posiadanie niezawodnych strategii zabezpieczeń, które obejmują monitorowanie w czasie rzeczywistym i rutynowe oceny zabezpieczeń, mają kluczowe znaczenie dla poprawy codziennych zabezpieczeń operacyjnych w środowiskach IT, w tym w chmurze.

Bezpieczeństwo chmury to wspólne wysiłki między klientem a dostawcą chmury. Istnieją cztery usługi, które Microsoft Azure zapewnia, aby ułatwić przestrzeganie tych wymagań, z uwzględnieniem zaleceń zawartych w [cybernetycznymi (ACSC) z informacjami o zabezpieczeniach ręcznych kontroli zabezpieczeń](https://acsc.gov.au/infosec/ism/index.htm) (ISM), w przypadku wdrażania scentralizowanego rejestrowania zdarzeń, inspekcji dzienników zdarzeń i oceny luk w zabezpieczeniach oraz zarządzania nimi. Microsoft Azure usługi to:

* Azure Security Center
* Azure Monitor
* Azure Advisor
* Azure Policy

ACSC zaleca korzystanie z tych usług na potrzeby **chronionych** danych. Korzystając z tych usług, możesz proaktywnie monitorować i analizować środowiska IT oraz podejmować świadome decyzje dotyczące tego, gdzie najlepiej przydzielić zasoby w celu zwiększenia bezpieczeństwa. Każda z tych usług jest częścią połączonego rozwiązania, które zapewnia najlepsze informacje, zalecenia i ochronę.

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) zapewnia ujednoliconą konsolę zarządzania zabezpieczeniami, która służy do monitorowania i zwiększania bezpieczeństwa zasobów platformy Azure i hostowanych danych. Azure Security Center zawiera zabezpieczony wynik, ocenę na podstawie analizy stanu konfiguracji najlepszych rozwiązań z Azure Advisor i ogólnej zgodności Azure Policy.

Azure Security Center zapewnia klientom platformy Azure następujące funkcje:

* Zasady zabezpieczeń, ocena i rekomendacje
* Zbieranie i wyszukiwanie zdarzeń zabezpieczeń
* Kontrola dostępu i aplikacji
* Zaawansowane wykrywanie zagrożeń
* Kontrola dostępu just in Time Virtual Machines
* Bezpieczeństwo hybrydowe

Zakres zasobów monitorowanych przez Azure Security Center może być rozszerzony w celu uwzględnienia obsługiwanych zasobów lokalnych w środowisku chmury hybrydowej. Obejmuje to lokalne zasoby, które są aktualnie monitorowane przez obsługiwaną wersję System Center Operations Manager.

Warstwa "Standardowa" Security Center obejmuje również kontrole zabezpieczeń oparte na chmurze wymagane przez narzędzie [ASD Essentials 8](https://acsc.gov.au/publications/protect/essential-eight-explained.htm). Obejmują one listy dozwolonych aplikacji i ograniczanie uprawnień administracyjnych przy użyciu dostępu just in Time.

### <a name="azure-monitor"></a>Azure Monitor

[Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) to scentralizowane rozwiązanie rejestrowania dla wszystkich zasobów platformy Azure, a także Log Analytics i Application Insights. Dwa kluczowe typy danych są zbierane z zasobów platformy Azure: dzienniki i metryki. Po zebraniu w Azure Monitor informacje o rejestrowaniu mogą być używane przez szeroką gamę narzędzi i w różnych celach.

![Azure Monitor — przegląd](media/overview.png)

Azure Monitor obejmuje również "Dziennik aktywności platformy Azure". Dziennik SActivity przechowuje wszystkie zdarzenia poziomu subskrypcji, które wystąpiły w ramach platformy Azure. Dzięki temu klienci platformy Azure mogą zobaczyć "kto, co i kiedy" zaległe operacje związane z zasobami platformy Azure. Rejestrowanie na podstawie zasobów wysyłane do Azure Monitor i zdarzeń dziennika aktywności platformy Azure można analizować przy użyciu wbudowanego języka zapytań Kusto. Dzienniki te można następnie wyeksportować, użyć do tworzenia niestandardowych pulpitów nawigacyjnych i widoków oraz skonfigurować do wyzwalania alertów i powiadomień.

### <a name="azure-advisor"></a>Azure Advisor

[Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview) analizy obsługują zasoby platformy Azure, pliki dziennika generowane przez system i bieżące konfiguracje zasobów w ramach subskrypcji platformy Azure. Analiza udostępniona w Azure Advisor jest generowana w czasie rzeczywistym i oparta na zalecanych przez firmę Microsoft najlepszych rozwiązaniach. Wszystkie obsługiwane zasoby platformy Azure dodane do Twojego środowiska zostaną przeanalizowane i zostaną dostarczone odpowiednie zalecenia. Zalecenia dotyczące Azure Advisor są podzielone na cztery kategorie najlepszych rozwiązań:

* Bezpieczeństwo
* Wysoka dostępność
* Wydajność
* Koszt

Zalecenia dotyczące zabezpieczeń wygenerowane przez Azure Advisor stanowią część ogólnej analizy zabezpieczeń podanej przez Azure Security Center.

Informacje zbierane przez Azure Advisor oferują administratorów:

* Wgląd w konfigurację zasobów, która nie spełnia zalecanych najlepszych rozwiązań
* Wskazówki dotyczące konkretnych działań naprawczych do podjęcia
* Rankingi wskazujące, które akcje naprawcze powinny być podejmowane jako wysoki priorytet

### <a name="azure-policy"></a>Azure Policy

[Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview) zapewnia możliwość zastosowania reguł rządzących typami zasobów platformy Azure i ich dozwolonymi konfiguracjami. Zasady mogą służyć do kontrolowania tworzenia i konfigurowania zasobów lub mogą służyć do inspekcji ustawień konfiguracji w środowisku. Te wyniki inspekcji mogą służyć do tworzenia podstaw działań zaradczych. Azure Policy różni się od kontroli dostępu opartej na rolach (RBAC); Azure Policy służy do ograniczania zasobów i ich konfiguracji, jest używana do ograniczania uprzywilejowanego dostępu do użytkowników platformy Azure.

Bez względu na to, czy określone zasady są wymuszane, czy też ma być przeprowadzana inspekcja zasad, zgodność zasad jest stale monitorowana, a ogólne i szczegółowe informacje o zgodności dotyczące zasobów są udostępniane administratorom. Azure Policy dane zgodności są udostępniane Azure Security Center i stanowią część bezpiecznego wyniku.

## <a name="key-design-considerations"></a>Podstawowe zagadnienia dotyczące projektowania

Podczas wdrażania strategii dziennika zdarzeń ACSC ISM wyróżnia następujące zagadnienia:

* Scentralizowane funkcje rejestrowania
* Określone zdarzenia do zarejestrowania
* Ochrona dziennika zdarzeń
* Przechowywanie dziennika zdarzeń
* Inspekcja dziennika zdarzeń

Dodatkowo w celu zbierania dzienników i zarządzania nimi, ISM również zaleca rutynowe oceny luk w zabezpieczeniach w środowisku INFORMATYCZNym organizacji.

### <a name="centralised-logging"></a>Scentralizowane rejestrowanie

Każde rozwiązanie do rejestrowania powinno mieć miejsce, gdy jest to możliwe, konsolidowanie przechwyconych dzienników do jednego repozytorium danych. To nie tylko zmniejsza złożoność operacyjną i uniemożliwia tworzenie wielu silosów danych, dzięki czemu można analizować dane zbierane z wielu źródeł, umożliwiając zidentyfikowanie wszelkich skorelowanych zdarzeń. Ma to kluczowe znaczenie dla wykrywania i zarządzania zasięgiem wszelkich cybernetycznymiych zdarzeń związanych z bezpieczeństwem.

To wymaganie jest spełnione dla wszystkich klientów platformy Azure z Azure Monitor. Ta oferta nie tylko udostępnia scentralizowane repozytorium rejestrowania na platformie Azure dla wszystkich zasobów platformy Azure, a także umożliwia przesyłanie strumieniowe danych do centrum zdarzeń platformy Azure. Usługa Azure Event Hubs zapewnia w pełni zarządzaną usługę pozyskiwania danych w czasie rzeczywistym. Gdy Azure Monitor dane są przesyłane strumieniowo do centrum zdarzeń platformy Azure, dane mogą być również łatwo połączone z istniejącymi repozytoriami informacji o zabezpieczeniach (SIEM) i dodatkowymi narzędziami do monitorowania zdarzeń innych firm.

Firma Microsoft oferuje także własne, natywne rozwiązanie platformy Azure SIEM, wskaźnik platformy Azure. System Azure Wskaźnikowanie obsługuje szeroką gamę łączników danych i może służyć do monitorowania zdarzeń zabezpieczeń w całym przedsiębiorstwie. Łącząc dane z obsługiwanych łączników [danych](https://docs.microsoft.com/azure/sentinel/connect-data-sources), wbudowane Uczenie maszynowe platformy Azure oraz język zapytań Kusto, administratorzy zabezpieczeń są dostarczani z jednym rozwiązaniem do wykrywania alertów, widocznością zagrożeń, polowania aktywnego, i reagowanie na zagrożenia. Wskaźnik kontroli obejmuje również funkcję polowania i notesu, która pozwala administratorom zabezpieczeń rejestrować wszystkie kroki podejmowane w ramach badania zabezpieczeń w ramach element PlayBook, które mogą być współużytkowane w organizacji. Administratorzy zabezpieczeń mogą nawet użyć wbudowanej [analizy użytkownika](https://docs.microsoft.com/azure/sentinel/user-analytics) do zbadania działań pojedynczego mianowanego użytkownika.

### <a name="logged-events-and-log-detail"></a>Zarejestrowane zdarzenia i szczegóły dziennika

ISM zawiera szczegółową listę typów dzienników zdarzeń, które powinny być zawarte w strategii rejestrowania. Wszystkie przechwycone dzienniki muszą zawierać wystarczające szczegóły, aby można było korzystać z analiz i badań.

Dzienniki zebrane na platformie Azure należą do jednej z następujących trzech kategorii:

* **Dzienniki kontroli i zarządzania**: Te dzienniki zawierają informacje dotyczące Azure Resource Manager operacji tworzenia, aktualizowania i usuwania.

* **Dzienniki płaszczyzny danych**: Zawierają one zdarzenia zgłoszone w ramach użycia zasobów platformy Azure. Obejmuje źródła, takie jak dzienniki zdarzeń systemu Windows, w tym Dzienniki systemu, zabezpieczeń i aplikacji.

* **Przetworzone zdarzenia**: Zdarzenia te zawierają informacje o zdarzeniach i alertach, które zostały automatycznie przetworzone w imieniu klienta przez platformę Azure. Przykładem zdarzenia przetworzonego jest alert Azure Security Center.

Monitorowanie maszyn wirtualnych platformy Azure jest rozszerzane przez wdrożenie agenta maszyny wirtualnej zarówno dla systemu Windows, jak i Linux. Znacząco zwiększa to zebrane informacje o rejestrowaniu. Wdrożenie tego agenta można skonfigurować tak, aby odbywało się automatycznie za pośrednictwem Azure Security Center.

Firma Microsoft udostępnia szczegółowe informacje o dziennikach specyficznych dla zasobów [](https://docs.microsoft.com/azure/security/fundamentals/log-audit)platformy Azure i ich schematach.

### <a name="log-retention-and-protection"></a>Przechowywanie i ochrona dzienników

 Dzienniki zdarzeń muszą być przechowywane bezpiecznie przez wymagany okres przechowywania. ISM powiadamia, że dzienniki są przechowywane przez co najmniej siedem lat. Platforma Azure zapewnia wiele środków, aby zapewnić długi okres istnienia zebranych dzienników. Domyślnie zdarzenia dziennika platformy Azure są przechowywane przez 90 dni. Dane dziennika przechwycone przez Azure Monitor można przenieść i zapisać na koncie usługi Azure Storage jako wymagane do długoterminowego przechowywania. Dzienniki aktywności przechowywane na koncie usługi Azure Storage mogą być przechowywane przez określoną liczbę dni lub w nieokreślony sposób, w razie potrzeby.

Konta usługi Azure Storage używane do przechowywania zdarzeń dziennika platformy Azure mogą być wprowadzane geograficznie i można utworzyć ich kopię zapasową przy użyciu Azure Backup. Po przechwyceniu przez Azure Backup wszystkie operacje usuwania kopii zapasowych zawierające dzienniki wymagają zatwierdzenia administracyjnego, a kopie zapasowe oznaczone do usunięcia nadal są przechowywane przez 14 dni, co pozwala na odzyskiwanie. Azure Backup umożliwia tworzenie kopii zapasowej chronionego wystąpienia na 9999, co pozwala na ponad 27 lat wykonywania codziennych zadań.

Access Control oparte na rolach powinny służyć do kontrolowania dostępu do zasobów używanych na potrzeby rejestrowania na platformie Azure. Azure Monitor, konta usługi Azure Storage i kopie zapasowe platformy Azure powinny być skonfigurowane przy użyciu kontroli dostępu opartej na rolach w celu zapewnienia bezpieczeństwa danych zawartych w dziennikach.

### <a name="log-auditing"></a>Inspekcja dziennika

Rzeczywista wartość dzienników jest realizowana po analizie. Używanie zarówno zautomatyzowanej, jak i ręcznej analizy oraz znajomości dostępnych narzędzi pomoże Ci wykrywać naruszenia zasad zabezpieczeń organizacji i zarządzać nimi oraz cybernetycznymi zdarzenia związane z bezpieczeństwem. Azure Monitor oferuje bogaty zestaw narzędzi do analizowania zebranych dzienników. Wynik tej analizy można następnie udostępnić między systemami, visualised lub rozpowszechniać w wielu formatach.

Dane dziennika przechowywane w Azure Monitor są przechowywane w Log Analytics obszarze roboczym. Wszystkie analizy zaczynają się od zapytania. Zapytania Azure Monitor są zapisywane w języku zapytań Kusto. Zapytania stanowią podstawę wszystkich danych wyjściowych z Azure Monitor z pulpitów nawigacyjnych platformy Azure do reguł alertów.

![Omówienie zapytań dziennika platformy Azure](media/queries-overview.png)

Inspekcja dzienników można rozszerzyć za pomocą rozwiązań do monitorowania. Są to wstępnie spakowane rozwiązania zawierające logikę kolekcji, zapytania i widoki wizualizacji danych. Firma Microsoft [udostępnia](https://docs.microsoft.com/azure/azure-monitor/insights/solutions-inventory) wiele rozwiązań do monitorowania oraz dodatkowe rozwiązania od dostawców produktów, które można znaleźć w portalu Azure Marketplace.

### <a name="vulnerability-assessment-and-management"></a>Ocena luk w zabezpieczeniach i zarządzanie nimi

Informacje o tym, że rutynowe oceny luk w zabezpieczeniach i zarządzanie nimi są istotne. Środowisko INFORMATYCZNe ciągle się zmienia, a ryzyko związane z bezpieczeństwem zewnętrznym jest nieograniczone. Za pomocą Azure Security Center można przeprowadzić zautomatyzowane oceny luk w zabezpieczeniach i uzyskać wskazówki dotyczące planowania i wykonywania działań naprawczych.

Wynik zabezpieczony w Azure Security Center zawiera listę zaleceń, które w przypadku zastosowania spowodują zwiększenie bezpieczeństwa środowiska. Lista jest posortowana według wpływu na ogólny wynik bezpieczny od najwyższego do najniższego. Uporządkowanie listy według wpływu umożliwia skoncentrowanie się na zaleceniach o najwyższym priorytecie, które są najbardziej związane z zwiększeniem zabezpieczeń.

Azure Policy również odgrywa kluczową część w aktualnej ocenie luk w zabezpieczeniach. Typy zasad dostępne w Azure Policy zakres od wymuszania tagów zasobów i wartości, aby ograniczyć regiony platformy Azure, w których można utworzyć zasoby, aby całkowicie zablokować tworzenie konkretnych typów zasobów. Zestaw zasad platformy Azure można podzielić na inicjatywy. Inicjatywy służą do stosowania pokrewnych zasad platformy Azure, które są stosowane razem jako Grupa, stanowią podstawę określonego celu zabezpieczeń lub zgodności.

Azure Policy ma bibliotekę wbudowanych definicji zasad, które stale rośnie. Azure Portal udostępnia również opcję tworzenia własnych niestandardowych definicji Azure Policy. Po znalezieniu zasad w istniejącej bibliotece lub utworzeniu nowej można przypisać zasady do zasobów platformy Azure. Te przydziały mogą być [ograniczone do zakresu](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage) na różnych poziomach w hierarchii zarządzania zasobami. Przypisanie zasad jest dziedziczone, co oznacza, że wszystkie zasoby podrzędne w zakresie otrzymują to samo przypisanie zasad. Zasoby można również wykluczać z przypisania zasad z określonym zakresem, zgodnie z potrzebami.

Wszystkie wdrożone zasady platformy Azure przyczyniają się do bezpiecznego oceny organizacji. W środowisku wysoce Bespoke można tworzyć i wdrażać niestandardowe definicje Azure Policy, aby zapewnić informacje o inspekcji dostosowane do konkretnych obciążeń.

## <a name="getting-started"></a>Wprowadzenie

Aby rozpocząć pracę z Azure Security Center i korzystać z usługi Azure Monitor, Advisor i zasad, firma Microsoft zaleca następujące etapy wstępne:

* Włącz Azure Security Center
* Uaktualnij do warstwy Standardowa
* Włącz automatyczną obsługę administracyjną Microsoft Monitoring Agent na obsługiwanych Virtual Machines platformy Azure
* Przeglądanie, ustalanie priorytetów i łagodzenie zaleceń i alertów dotyczących zabezpieczeń na pulpicie nawigacyjnym Centrum zabezpieczeń

## <a name="next-steps"></a>Kolejne kroki

Przeczytaj [Azure Policy i plany platformy Azure,](azure-policy.md) Aby uzyskać szczegółowe informacje na temat implementowania nadzoru i kontroli nad zasobami w Australii platformy Azure w celu zapewnienia zgodności zasad i przepisów.
