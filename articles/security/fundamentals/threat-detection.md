---
title: Zaawansowane wykrywanie zagrożeń na platformie Azure | Dokumenty firmy Microsoft
description: Dowiedz się więcej o usłudze Azure AD Identity Protection i jej możliwościach.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 3c1c385a87fc302d180729ec2e4bcd1c4a315f6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981465"
---
# <a name="azure-advanced-threat-detection"></a>Zaawansowane wykrywanie zagrożeń na platformie Azure

Platforma Azure oferuje wbudowane zaawansowane funkcje wykrywania zagrożeń za pośrednictwem usług, takich jak usługa Azure Active Directory (Azure AD), dzienniki usługi Azure Monitor i Usługa Azure Security Center. Ta kolekcja usług zabezpieczeń i możliwości zapewnia prosty i szybki sposób, aby zrozumieć, co dzieje się w ramach wdrożeń platformy Azure.

Platforma Azure oferuje szeroką gamę opcji konfigurowania i dostosowywania zabezpieczeń w celu spełnienia wymagań wdrożeń aplikacji. W tym artykule omówiono sposób spełnienia tych wymagań.

## <a name="azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection

[Usługa Azure AD Identity Protection](../../active-directory/identity-protection/overview.md) to funkcja usługi Azure Active [Directory Premium P2,](../../active-directory/active-directory-whatis.md) która zawiera omówienie wykrywania ryzyka i potencjalnych luk w zabezpieczeniach, które mogą mieć wpływ na tożsamość organizacji. Usługa Identity Protection korzysta z istniejących funkcji wykrywania anomalii usługi Azure AD, które są dostępne za pośrednictwem [raportów aktywności anomalii usługi Azure AD,](../../active-directory/active-directory-reporting-azure-portal.md)i wprowadza nowe typy wykrywania ryzyka, które mogą wykrywać anomalie w czasie rzeczywistym.

![Diagram usługi Azure AD Identity Protection](./media/threat-detection/azure-threat-detection-fig1.png)

Usługa Identity Protection używa adaptacyjnych algorytmów uczenia maszynowego i heurystyki do wykrywania anomalii i wykrywania ryzyka, które mogą wskazywać, że tożsamość została naruszona. Korzystając z tych danych, ochrona tożsamości generuje raporty i alerty, dzięki czemu można zbadać te wykrywanie ryzyka i podjąć odpowiednie działania naprawcze lub łagodzące.

Usługa Azure Active Directory Identity Protection to coś więcej niż narzędzie do monitorowania i raportowania. Na podstawie wykrywania ryzyka usługa Identity Protection oblicza poziom ryzyka użytkownika dla każdego użytkownika, dzięki czemu można skonfigurować zasady oparte na ryzyku, aby automatycznie chronić tożsamości organizacji.

Te zasady oparte na ryzyku, oprócz innych [formantów dostępu warunkowego,](../../active-directory/active-directory-conditional-access-azure-portal.md) które są dostarczane przez usługi Azure Active Directory i [EMS,](../../active-directory/active-directory-conditional-access-azure-portal.md)można automatycznie blokować lub oferować adaptacyjne akcje korygowania, które obejmują resetowanie hasła i wymuszanie uwierzytelniania wieloskładnikowego.

### <a name="identity-protection-capabilities"></a>Funkcje ochrony tożsamości

Usługa Azure Active Directory Identity Protection to coś więcej niż narzędzie do monitorowania i raportowania. Aby chronić tożsamości organizacji, można skonfigurować zasady oparte na ryzyku, które automatycznie reagują na wykryte problemy po osiągnięciu określonego poziomu ryzyka. Te zasady, oprócz innych kontroli dostępu warunkowego dostarczonych przez usługę Azure Active Directory i EMS, można automatycznie blokować lub inicjować adaptacyjne akcje korygowania, w tym resetowanie haseł i wymuszanie uwierzytelniania wieloskładnikowego.

Przykłady niektórych sposobów, które usługa Azure Identity Protection może pomóc w zabezpieczeniu kont i tożsamości:

[Wykrywanie wykrywania zagrożeń i ryzykownych kont](../../active-directory/identity-protection/overview.md)
-   Wykryj sześć typów wykrywania ryzyka przy użyciu uczenia maszynowego i reguł heurystycznych.
-   Oblicz poziomy ryzyka użytkownika.
-   Podaj niestandardowe zalecenia, aby poprawić ogólną postawę zabezpieczeń, wyróżniając luki w zabezpieczeniach.

[Badanie wykrywania zagrożeń](../../active-directory/identity-protection/overview.md)
-   Wysyłaj powiadomienia o wykrywaniu zagrożeń.
-   Zbadaj wykrywanie ryzyka przy użyciu istotnych i kontekstowych informacji.
-   Zapewnij podstawowe przepływy pracy do śledzenia dochodzeń.
-   Zapewnij łatwy dostęp do akcji korygowania, takich jak resetowanie hasła.

[Oparte na ryzyku zasady dostępu warunkowego](../../active-directory/identity-protection/overview.md)
-   Ograniczaj ryzykowne logowania, blokując logowania lub wymagając problemów z uwierzytelnianiem wieloskładnikowym.
-   Blokowanie lub zabezpieczanie ryzykownych kont użytkowników.
-   Wymagaj od użytkowników rejestrowania się w celu uwierzytelniania wieloskładnikowego.

### <a name="azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management

Za [pomocą usługi Azure Active Directory Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md)można zarządzać, kontrolować i monitorować dostęp w organizacji. Ta funkcja obejmuje dostęp do zasobów w usłudze Azure AD i innych usługach online firmy Microsoft, takich jak Office 365 lub Microsoft Intune.

![Diagram zarządzania tożsamościami uprzywilejowanymi usługi Azure AD](./media/threat-detection/azure-threat-detection-fig2.png)

Pim pomaga:

-   Otrzymuj alerty i raporty dotyczące administratorów usługi Azure AD i dostępu administracyjnego just-in-time (JIT) do usług online firmy Microsoft, takich jak Office 365 i Intune.

-   Otrzymywać raporty dotyczące historii dostępu administratora i zmian w przypisaniach administratorów.

-   Otrzymuj alerty dotyczące dostępu do roli uprzywilejowanej.

## <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor

[Dzienniki usługi Azure Monitor](../../azure-monitor/index.yml) to oparte na chmurze rozwiązanie do zarządzania it firmy Microsoft, które pomaga zarządzać infrastrukturą lokalną i chmurową oraz chronić ją. Ponieważ dzienniki usługi Azure Monitor są implementowane jako usługa oparta na chmurze, można je szybko uruchomić przy minimalnych inwestycjach w usługi infrastrukturalne. Nowe funkcje zabezpieczeń są dostarczane automatycznie, co pozwala zaoszczędzić bieżące koszty konserwacji i modernizacji.

Oprócz samodzielnego świadczenia cennych usług dzienniki usługi Azure Monitor można zintegrować ze składnikami programu System Center, takimi jak [Program System Center Operations Manager,](https://blogs.technet.microsoft.com/cbernier/2013/10/23/monitoring-windows-azure-with-system-center-operations-manager-2012-get-me-started/)aby rozszerzyć istniejące inwestycje w zarządzanie zabezpieczeniami w chmurze. Dzienniki system center i usługi Azure Monitor mogą współpracować w celu zapewnienia pełnego środowiska zarządzania hybrydowego.

### <a name="holistic-security-and-compliance-posture"></a>Holistyczne bezpieczeństwo i zgodność z przepisami

[Pulpit nawigacyjny bezpieczeństwa i inspekcji usługi Log Analytics](../../security-center/security-center-intro.md) zapewnia kompleksowy widok na postawę bezpieczeństwa IT w organizacji z wbudowanymi zapytaniami wyszukiwania dla istotnych problemów, które wymagają uwagi. Pulpit nawigacyjny zabezpieczeń i inspekcji jest ekranem głównym dla wszystkiego, co związane z zabezpieczeniami w dziennikach usługi Azure Monitor. Zawiera ogólne informacje o stanie zabezpieczeń komputerów. Możesz też wyświetlić wszystkie zdarzenia z ostatnich 24 godzin, 7 dni lub innych niestandardowych ram czasowych.

Dzienniki usługi Azure Monitor pomagają szybko i łatwo zrozumieć ogólną postawę zabezpieczeń dowolnego środowiska, a wszystko to w kontekście operacji IT, w tym oceny aktualizacji oprogramowania, oceny ochrony przed złośliwym oprogramowaniem i linii bazowych konfiguracji. Dane dziennika zabezpieczeń są łatwo dostępne, aby usprawnić procesy inspekcji zabezpieczeń i zgodności.

![Pulpit nawigacyjny zabezpieczeń i inspekcji usługi Log Analytics](./media/threat-detection/azure-threat-detection-fig3.jpg)

Pulpit nawigacyjny bezpieczeństwa i inspekcji usługi Log Analytics jest podzielony na cztery główne kategorie:

-   **Domeny zabezpieczeń:** Umożliwia dalsze eksplorowanie rekordów zabezpieczeń w czasie; dostęp do ocen złośliwego oprogramowania; oceny aktualizacji; wyświetlanie informacji o zabezpieczeniach sieci, tożsamości i dostępie; wyświetlanie komputerów ze zdarzeniami zabezpieczeń; i szybko uzyskać dostęp do pulpitu nawigacyjnego usługi Azure Security Center.

-   **Istotne problemy:** Umożliwia szybkie zidentyfikowanie liczby aktywnych problemów i powagi problemów.

-   **Wykrywania (wersja zapoznawcza)**: Umożliwia identyfikowanie wzorców ataków przez wyświetlanie alertów zabezpieczeń, które występują w odniesieniu do zasobów.

-   **Analiza zagrożeń:** Umożliwia identyfikowanie wzorców ataków przez wyświetlanie całkowitej liczby serwerów z wychodzącym złośliwym ruchem IP, typu złośliwego zagrożenia i mapy lokalizacji adresów IP.

-   **Typowe zapytania dotyczące zabezpieczeń:** wyświetla listę najczęstszych zapytań dotyczących zabezpieczeń, których można używać do monitorowania środowiska. Po wybraniu dowolnej kwerendy zostanie otwarte okienko Wyszukiwania i wyświetlone są wyniki dla tej kwerendy.

### <a name="insight-and-analytics"></a>Wgląd i analityka
W centrum [dzienników usługi Azure Monitor](../../log-analytics/log-analytics-queries.md) znajduje się repozytorium, które jest hostowane przez platformę Azure.

![Diagram wglądu i analizy](./media/threat-detection/azure-threat-detection-fig4.png)

Zbierasz dane do repozytorium z połączonych źródeł, konfigurując źródła danych i dodając rozwiązania do subskrypcji.

![Pulpit nawigacyjny dziennika usługi Azure Monitor](./media/threat-detection/azure-threat-detection-fig5.png)

Źródła danych i rozwiązania tworzą oddzielne typy rekordów z własnym zestawem właściwości, ale nadal można je analizować razem w kwerendach do repozytorium. Można użyć tych samych narzędzi i metod do pracy z różnych danych, które są zbierane przez różne źródła.


Większość interakcji z dziennikami usługi Azure Monitor odbywa się za pośrednictwem witryny Azure Portal, która działa w dowolnej przeglądarce i zapewnia dostęp do ustawień konfiguracji i wielu narzędzi do analizowania zebranych danych i działania na ich podstawie. Z portalu można użyć:
* [Wyszukiwanie dziennika,](../../log-analytics/log-analytics-queries.md) gdzie można konstruować kwerendy do analizowania zebranych danych.
* [Pulpity nawigacyjne](../../azure-monitor/learn/tutorial-logs-dashboards.md), które można dostosować za pomocą graficznych widoków najcenniejszych wyszukiwań.
* [Rozwiązania,](../../monitoring/monitoring-solutions.md)które zapewniają dodatkowe funkcje i narzędzia analityczne.

![Narzędzia analityczne](./media/threat-detection/azure-threat-detection-fig6.png)

Rozwiązania dodają funkcje do dzienników usługi Azure Monitor. Działają one przede wszystkim w chmurze i zapewniają analizę danych, które są zbierane w repozytorium analizy dziennika. Rozwiązania mogą również definiować nowe typy rekordów do zebrania, które mogą być analizowane za pomocą wyszukiwania w dzienniku lub przy użyciu dodatkowego interfejsu użytkownika, który rozwiązanie udostępnia na pulpicie nawigacyjnym analizy dziennika.

Pulpit nawigacyjny zabezpieczeń i inspekcji jest przykładem tego typu rozwiązań.

### <a name="automation-and-control-alert-on-security-configuration-drifts"></a>Automatyzacja i kontrola: Alert o dryfach konfiguracji zabezpieczeń

Usługa Azure Automation automatyzuje procesy administracyjne za pomocą śmiób ekshemuernych opartych na programie PowerShell i uruchamianych w chmurze. Elementy Runbook mogą być również wykonywane na serwerze w lokalnym centrum danych w celu zarządzania zasobami lokalnymi. Usługa Azure Automation zapewnia zarządzanie konfiguracją za pomocą konfiguracji żądanego stanu programu PowerShell (DSC).

![Diagram automatyzacji platformy Azure](./media/threat-detection/azure-threat-detection-fig7.png)

Można tworzyć zasoby DSC i zarządzać nimi, które są hostowane na platformie Azure i stosować je do systemów w chmurze i lokalnych. W ten sposób można zdefiniować i automatycznie wymusić ich konfigurację lub uzyskać raporty dotyczące dryfu, aby upewnić się, że konfiguracje zabezpieczeń pozostają w zasadach.

## <a name="azure-security-center"></a>Azure Security Center

Usługa Azure Security Center pomaga chronić zasoby platformy Azure. Zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami w ramach subskrypcji platformy Azure. W ramach usługi można zdefiniować zasady dotyczące zarówno subskrypcji platformy Azure, jak i [grup zasobów,](../../azure-resource-manager/management/manage-resources-portal.md) aby uzyskać większą szczegółowość.

![Diagram usługi Azure Security Center](./media/threat-detection/azure-threat-detection-fig8.png)

Pracownicy naukowo-badawczy firmy Microsoft stale poszukują nowych zagrożeń. Mają oni dostęp do szerokiej gamy rozwiązań telemetrycznych uzyskanych dzięki obecności firmy Microsoft na całym świecie zarówno w chmurze, jak i lokalnie. Bogata i różnorodna kolekcja zestawów danych pozwala firmie Microsoft odnajdywać nowe trendy i wzorce ataków, które pojawiają się w jej lokalnych produktach dla firm i osób prywatnych, a także usługach online.

W ten sposób usługa Security Center może szybko zaktualizować swoje algorytmy wykrywania, ponieważ atakujący publikuje nowe i coraz bardziej zaawansowane exploity. Takie podejście pomaga dotrzymać kroku szybko zmieniającemu się środowisku zagrożeń.

![Wykrywanie zagrożeń w centrum zabezpieczeń](./media/threat-detection/azure-threat-detection-fig9.jpg)

Wykrywanie zagrożeń za pomocą usługi Security Center polega na automatycznym zbieraniu informacji o zabezpieczeniach uzyskanych z zasobów platformy Azure, sieci i powiązanych rozwiązań partnerskich. Analizuje te informacje, korelując informacje z wielu źródeł, aby zidentyfikować zagrożenia.

Alerty zabezpieczeń wraz z zaleceniami dotyczącymi usuwania zagrożeń są traktowane przez usługę Security Center priorytetowo.

Usługa Security Center wykorzystuje zaawansowane narzędzia analizy zabezpieczeń, które wykraczają daleko poza metody bazujące na sygnaturze. Przełomowe rozwiązania w zakresie technologii dużych zbiorów danych i [uczenia maszynowego](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) służą do oceny zdarzeń w całej sieci szkieletowej chmury. Zaawansowana analiza może wykrywać zagrożenia, których nie można zidentyfikować za pomocą ręcznego podejścia i przewidywania ewolucji ataków. Te typy analizy zabezpieczeń są omówione w następnych sekcjach.

### <a name="threat-intelligence"></a>Analiza zagrożeń

Firma Microsoft ma dostęp do ogromnej ilości globalnej analizy zagrożeń.

Dane telemetryczne są przepływane z wielu źródeł, takich jak Azure, Office 365, Microsoft CRM online, Microsoft Dynamics AX, outlook.com, MSN.com, Microsoft Digital Crimes Unit (DCU) i Microsoft Security Response Center (MSRC).

![Wyniki analizy zagrożeń](./media/threat-detection/azure-threat-detection-fig10.jpg)

Naukowcy otrzymują również informacje o analizie zagrożeń, które są udostępniane głównym dostawcom usług w chmurze i subskrybują kanały analizy zagrożeń od stron trzecich. Usługa Azure Security Center może użyć tych informacji, aby ostrzec użytkownika przed zagrożeniami ze strony znanych złośliwych podmiotów. Oto niektóre przykłady:

-   **Wykorzystanie możliwości uczenia maszynowego:** Usługa Azure Security Center ma dostęp do ogromnej ilości danych dotyczących aktywności w sieci w chmurze, które mogą służyć do wykrywania zagrożeń kierowanych na wdrożenia platformy Azure.

-   **Wykrywanie sił brute: Uczenie**maszynowe służy do tworzenia historycznego wzorca prób dostępu zdalnego, który pozwala na wykrywanie ataków siłowych przeciwko secure shell (SSH), protokołowi RDP (Remote Desktop Protocol) i portom SQL.

-   **Wychodzące wykrywanie DDoS i botnetu:** Typowym celem ataków ukierunkowanych na zasoby w chmurze jest użycie mocy obliczeniowej tych zasobów do wykonywania innych ataków.

-   **Nowe serwery analizy behawioralnej i maszyny wirtualne:** Po zaatakowaniu serwera lub maszyny wirtualnej osoby atakujące stosują szeroką gamę technik do wykonywania złośliwego kodu w tym systemie, unikając wykrywania, zapewniając trwałość i unikając kontroli zabezpieczeń.

-   **Wykrywanie zagrożeń bazy danych SQL azure:** wykrywanie zagrożeń dla usługi Azure SQL Database, która identyfikuje nietypowe działania bazy danych, które wskazują na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania.

### <a name="behavioral-analytics"></a>Analiza behawioralna

Analiza behawioralna to metoda, która polega na analizie danych i porównywaniu ich z kolekcją znanych wzorców. Wzorce te nie są jednak prostymi sygnaturami. Określa się je za pośrednictwem złożonych algorytmów uczenia maszynowego, które są stosowane w przypadku wielkich zestawów danych.

![Wyniki analizy behawioralnej](./media/threat-detection/azure-threat-detection-fig11.jpg)

Wzorce są również określane przez staranną analizę złośliwych zachowań przez ekspertów analityków. Usługa Azure Security Center może używać analizy behawioralnej do identyfikowania zasobów, których bezpieczeństwo zostało naruszone, na podstawie analizy dzienników maszyn wirtualnych, dzienników urządzeń sieci wirtualnej, dzienników sieci szkieletowej, zrzutów awaryjnych i innych źródeł.

Ponadto wzorce są skorelowane z innymi sygnałami, aby sprawdzić, czy nie ma dowodów potwierdzających szeroko rozpowszechnioną kampanię. Ta korelacja pomaga w identyfikacji zdarzeń, które są zgodne ze sprawdzonymi wskaźnikami naruszenia bezpieczeństwa.

Oto niektóre przykłady:
-   **Wykonanie podejrzanego procesu**: osoby atakujące stosują kilka metod, które pozwalają na niezauważalne uruchomienie złośliwego oprogramowania. Na przykład osoba atakująca może nadać złośliwemu oprogramowaniu takie same nazwy jak legalnym plikom systemowym, ale umieścić te pliki w alternatywnej lokalizacji, użyć nazwy podobnej do nazwy pliku łagodnego lub zamaskować prawdziwe rozszerzenie pliku. Usługa Security Center modeluje zachowania procesów i monitoruje wykonywanie procesów w celu wykrywania wartości odstałych, takich jak te.

-   **Ukryte złośliwe oprogramowanie i próby wykorzystania:** Zaawansowane złośliwe oprogramowanie może uniknąć tradycyjnych produktów chroniących przed złośliwym oprogramowaniem, nigdy nie zapisując na dysk lub szyfrując składników oprogramowania przechowywanych na dysku. Jednak takie złośliwe oprogramowanie można wykryć przy użyciu analizy pamięci, ponieważ złośliwe oprogramowanie musi pozostawić ślady w pamięci do działania. Jeśli wystąpi awaria oprogramowania, zrzut awaryjny przechwytuje część pamięci w chwili pojawienia się awarii. Analizując pamięć w zrzucie awaryjnym, usługa Azure Security Center może wykrywać techniki używane do wykorzystywania luk w oprogramowaniu, uzyskiwania dostępu do poufnych danych i ukradkiem utrwalać się na zainfekowanym komputerze bez wpływu na wydajność Maszyny.

-   **Ruch boczny i rekonesans wewnętrzny**: Aby utrzymać się w zagrożonej sieci i zlokalizować i zebrać cenne dane, atakujący często próbują przenieść się bocznie z zainfekowanego komputera do innych osób w tej samej sieci. Usługa Security Center monitoruje działania związane z procesem i logowaniem w celu wykrycia prób rozszerzenia przyczółka osoby atakującej w sieci, takich jak zdalne wykonywanie poleceń, sondowanie sieci i wyliczanie konta.

-   **Złośliwe skrypty programu PowerShell:** Program PowerShell może być używany przez osoby atakujące do wykonywania złośliwego kodu na docelowych maszynach wirtualnych do różnych celów. Usługa Security Center sprawdza działanie programu PowerShell w poszukiwaniu dowodów na podejrzaną aktywność.

-   **Ataki wychodzące**: dla osób atakujących częstym celem są zasoby w chmurze, ponieważ mogą one być użyte do przeprowadzenia dodatkowych ataków. Na przykład maszyny wirtualne, których bezpieczeństwo zostało naruszone, mogą być używane do przeprowadzania ataków siłowych na inne maszyny wirtualne, wysyłania spamu lub skanowania otwartych portów i innych urządzeń w Internecie. Dzięki zastosowaniu uczenia maszynowego do ruchu sieciowego usługa Security Center może wykryć, kiedy wychodząca komunikacja sieciowa przekracza normy. Po wykryciu spamu usługa Security Center koreluje również nietypowy ruch poczty e-mail z analizą z usługi Office 365 w celu ustalenia, czy poczta jest prawdopodobnie nikczemna, czy też jest wynikiem legalnej kampanii e-mail.

### <a name="anomaly-detection"></a>Wykrywanie anomalii

Usługa Azure Security Center używa również wykrywania anomalii do identyfikowania zagrożeń. W przeciwieństwie do analizy behawioralnej (która zależy od znanych wzorców wyprowadzonych z dużych zestawów danych), wykrywanie anomalii jest bardziej „spersonalizowane” i koncentruje się na liniach bazowych, które są specyficzne dla przeprowadzonych wdrożeń. Uczenie maszynowe jest stosowane do określenia normalnej aktywności dla wdrożeń, a następnie reguły są generowane w celu zdefiniowania warunków odstających, które mogą reprezentować zdarzenie zabezpieczeń. Oto przykład:

-   **Przychodzące ataki RDP/SSH brute force:** Wdrożenia mogą mieć zajęte maszyny wirtualne z wieloma logowaniami każdego dnia i innymi maszynami wirtualnymi, które mają niewiele, jeśli w ogóle, logowania. Usługa Azure Security Center może określić aktywność logowania według planu bazowego dla tych maszyn wirtualnych i używać uczenia maszynowego do definiowania wokół normalnych działań logowania. Jeśli istnieje jakakolwiek rozbieżność z linią bazową zdefiniowaną dla cech związanych z logowaniem, może zostać wygenerowany alert. To uczenie maszynowe określa co jest istotne.

### <a name="continuous-threat-intelligence-monitoring"></a>Ciągłe monitorowanie analizy zagrożeń

Usługa Azure Security Center współpracuje z zespołami zajmującymi się badaniami nad zabezpieczeniami i analityką danych na całym świecie, które stale monitorują zmiany w krajobrazie zagrożeń. Obejmuje to następujące inicjatywy:

-   **Monitorowanie analizy zagrożeń:** Analiza zagrożeń obejmuje mechanizmy, wskaźniki, implikacje i porady dotyczące istniejących lub pojawiających się zagrożeń. Te informacje są udostępniane w społeczności zabezpieczeń, a firma Microsoft stale monitoruje źródła analizy zagrożeń ze źródeł wewnętrznych i zewnętrznych.

-   **Udostępnianie sygnału:** Informacje od zespołów zabezpieczeń w całym portfolio usług chmurowych i lokalnych firmy Microsoft, serwery i urządzenia końcowe klienta są udostępniane i analizowane.

-   **Specjaliści od zabezpieczeń firmy Microsoft**: Ciągłe współpraca z zespołami firmy Microsoft, które pracują w wyspecjalizowanych dziedzinach zabezpieczeń, takich jak kryminalistyka i wykrywanie ataków internetowych.

-   **Dostrajanie wykrywania:** Algorytmy są uruchamiane na tle rzeczywistych zestawów danych klientów, a badacze bezpieczeństwa współpracują z klientami w celu zweryfikowania wyników. Wyniki prawdziwie i fałszywie dodatnie są używane w celu udoskonalania algorytmów uczenia maszynowego.

Te połączone wysiłki kończą się nowymi i ulepszonymi wykrywaniami, z których możesz korzystać natychmiast. Nie ma żadnych działań do podjęcia.

## <a name="advanced-threat-detection-features-other-azure-services"></a>Zaawansowane funkcje wykrywania zagrożeń: inne usługi platformy Azure

### <a name="virtual-machines-microsoft-antimalware"></a>Maszyny wirtualne: ochrona przed złośliwym oprogramowaniem firmy Microsoft

[Ochrona przed złośliwym oprogramowaniem](antimalware.md) firmy Microsoft dla platformy Azure to rozwiązanie jednoagentowe dla aplikacji i środowisk dzierżawy, zaprojektowane do uruchamiania w tle bez interwencji człowieka. Ochronę można wdrożyć na podstawie potrzeb obciążeń aplikacji, przy zastosowaniu podstawowej bezpiecznej domyślnej lub zaawansowanej konfiguracji niestandardowej, w tym monitorowania ochrony przed złośliwym oprogramowaniem. Ochrona przed złośliwym oprogramowaniem platformy Azure to opcja zabezpieczeń dla maszyn wirtualnych platformy Azure, która jest automatycznie instalowana na wszystkich maszynach wirtualnych usługi Azure PaaS.

#### <a name="microsoft-antimalware-core-features"></a>Podstawowe funkcje ochrony przed złośliwym oprogramowaniem firmy Microsoft

Oto funkcje platformy Azure, które wdrażają i włączają oprogramowanie antywirusowe firmy Microsoft dla aplikacji:

-   **Ochrona w czasie rzeczywistym:** monitoruje aktywność w usługach w chmurze i na maszynach wirtualnych w celu wykrywania i blokowania wykonywania złośliwego oprogramowania.

-   **Zaplanowane skanowanie**: Okresowo wykonuje ukierunkowane skanowanie w celu wykrycia złośliwego oprogramowania, w tym aktywnie uruchamianych programów.

-   **Usuwanie złośliwego oprogramowania:** Automatycznie działa na wykryte złośliwe oprogramowanie, takie jak usuwanie lub kłótnie złośliwych plików i czyszczenie złośliwych wpisów rejestru.

-   **Aktualizacje podpisów:** Automatycznie instaluje najnowsze sygnatury ochrony (definicje wirusów), aby zapewnić aktualność ochrony z wcześniej określoną częstotliwością.

-   **Aktualizacje aparatu ochrony przed złośliwym oprogramowaniem:** Automatycznie aktualizuje aparat ochrony przed złośliwym oprogramowaniem firmy Microsoft.

-   **Aktualizacje platformy ochrony przed złośliwym oprogramowaniem:** Automatycznie aktualizuje platformę microsoft antimalware.

-   **Aktywna ochrona:** Raportuje metadane telemetrii o wykrytych zagrożeniach i podejrzanych zasobach na platformie Microsoft Azure, aby zapewnić szybką reakcję na zmieniający się krajobraz zagrożeń, umożliwiając synchronizowanie podpisów w czasie rzeczywistym za pośrednictwem aktywnego systemu ochrony firmy Microsoft.

-   **Raportowanie przykładów:** udostępnia i raportuje próbki do usługi ochrony przed złośliwym oprogramowaniem firmy Microsoft, aby uściślić usługę i włączyć rozwiązywanie problemów.

-   **Wykluczenia:** Umożliwia administratorom aplikacji i usług konfigurowanie niektórych plików, procesów i dysków w celu wykluczenia z ochrony i skanowania ze względu na wydajność i inne powody.

-   **Zbieranie zdarzeń ochrony przed złośliwym oprogramowaniem:** rejestruje kondycję usługi ochrony przed złośliwym oprogramowaniem, podejrzane działania i działania naprawcze podjęte w dzienniku zdarzeń systemu operacyjnego i zbiera je na koncie magazynu platformy Azure klienta.

### <a name="azure-sql-database-threat-detection"></a>Wykrywanie zagrożeń bazy danych SQL platformy Azure

[Wykrywanie zagrożeń bazy danych SQL azure](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/) to nowa funkcja analizy zabezpieczeń wbudowana w usługę Azure SQL Database. Pracując przez całą dobę, aby dowiedzieć się, profilować i wykrywać nietypowe działania bazy danych, wykrywanie zagrożeń bazy danych SQL sql identyfikuje potencjalne zagrożenia dla bazy danych.

Funkcjonariusze ochrony lub inni wyznaczeni administratorzy mogą uzyskać natychmiastowe powiadomienie o podejrzanych działaniach w bazie danych w miarę ich występowania. Każde powiadomienie zawiera szczegółowe informacje o podejrzanych działaniach i zaleca, jak dalej badać i zmniejszać zagrożenie.

Obecnie funkcja wykrywania zagrożeń bazy danych SQL wykryła potencjalne luki w zabezpieczeniach i ataki iniekcji SQL oraz nietypowe wzorce dostępu do bazy danych.

Po otrzymaniu powiadomienia e-mail z wykrywaniem zagrożeń użytkownicy mogą nawigować i wyświetlać odpowiednie rekordy inspekcji za pośrednictwem głębokiego łącza w wiadomości e-mail. Łącze otwiera przeglądarkę inspekcji lub wstępnie skonfigurowany szablon programu Excel inspekcji, który pokazuje odpowiednie rekordy inspekcji w czasie podejrzanego zdarzenia, zgodnie z następującymi zasadami:

-   Inspekcja magazynu dla bazy danych/serwera z nietypowych działań bazy danych.

-   Odpowiednia tabela magazynu inspekcji, która została użyta w czasie zdarzenia do zapisania dziennika inspekcji.

-   Inspekcja rejestruje godzinę bezpośrednio po wystąpieniu zdarzenia.

-   Inspekcja rekordów o podobnym identyfikatorze zdarzenia w momencie zdarzenia (opcjonalnie dla niektórych detektorów).

Detektory zagrożeń bazy danych SQL używają jednej z następujących metod wykrywania:

-   **Deterministic wykrywania:** Wykrywa podejrzane wzorce (reguły oparte) w zapytaniach klientów SQL, które pasują do znanych ataków. Metodologia ta ma wysokie wykrywanie i niski wynik fałszywie dodatni, ale ograniczony zasięg, ponieważ należy do kategorii "wykrywania atomowego".

-   **Wykrywanie zachowań:** Wykrywa nietypowe działanie, które jest nieprawidłowe zachowanie w bazie danych, które nie było widoczne w ciągu ostatnich 30 dni. Przykładami nietypowej aktywności klienta SQL może być skok nieudanych logowania lub kwerend, duża ilość wyodrębnianych danych, nietypowe zapytania kanoniczne lub nieznane adresy IP używane do uzyskiwania dostępu do bazy danych.

### <a name="application-gateway-web-application-firewall"></a>Zapora aplikacji bramy aplikacji sieci Web

[Zapora aplikacji sieci Web (WAF)](../../app-service/environment/app-service-app-service-environment-web-application-firewall.md) jest funkcją [usługi Azure Application Gateway,](../../application-gateway/application-gateway-web-application-firewall-overview.md) która zapewnia ochronę aplikacjom sieci Web, które używają bramy aplikacji dla standardowych funkcji [kontroli dostarczania aplikacji.](https://kemptechnologies.com/in/application-delivery-controllers) Zapora aplikacji sieci Web robi to, chroniąc je przed większością [10 typowych luk w zabezpieczeniach sieci Web projektu Open Web Application Security Project (OWASP).](https://www.owasp.org/index.php/Top_10_2010-Main)

![Diagram Zapory aplikacji bramy aplikacji sieci Web](./media/threat-detection/azure-threat-detection-fig13.png)

Zabezpieczenia obejmują:

-   ochrona przed wstrzyknięciem SQL.

-   Ochrona skryptów między witrynami.

-   Typowe zabezpieczenia przed atakami w sieci Web, takie jak intrygowanie poleceń, przemycanie żądań HTTP, dzielenie odpowiedzi HTTP i zdalny atak dołączania plików.

-   Ochrona przed naruszeniami protokołu HTTP.

-   Ochrona przed anomaliami protokołu HTTP, takimi jak brakujący agent użytkownika-agenta hosta i nagłówki akceptowania.

-   Zapobieganie botom, robotom i skanerom.

-   Wykrywanie typowych błędnych konfiguracji aplikacji (czyli Apache, IIS i tak dalej).

Konfigurowanie narzędzia WAF w bramie aplikacji zapewnia następujące korzyści:

-   Chroni aplikację sieci web przed lukami w zabezpieczeniach sieci Web i atakami bez modyfikacji kodu zaplecza.

-   Chroni wiele aplikacji sieci web w tym samym czasie za bramą aplikacji. Brama aplikacji obsługuje hosting do 20 stron internetowych.

-   Monitoruje aplikacje internetowe przed atakami przy użyciu raportów w czasie rzeczywistym, które są generowane przez dzienniki WAF bramy aplikacji.

-   Pomaga spełnić wymagania dotyczące zgodności. Niektóre kontrole zgodności wymagają, aby wszystkie punkty końcowe związane z Internetem były chronione przez rozwiązanie WAF.

### <a name="anomaly-detection-api-built-with-azure-machine-learning"></a>Interfejs API wykrywania anomalii: zbudowany przy usłudze Azure Machine Learning

Interfejs API wykrywania anomalii jest interfejsem API, który jest przydatny do wykrywania różnych nietypowych wzorców w danych szeregów czasowych. Interfejs API przypisuje wynik anomalii do każdego punktu danych w szeregach czasowych, który może służyć do generowania alertów, monitorowania za pośrednictwem pulpitów nawigacyjnych lub łączenia się z systemami biletowymi.

[Interfejs API wykrywania anomalii](../../machine-learning/team-data-science-process/apps-anomaly-detection-api.md) może wykryć następujące typy anomalii w danych szeregów czasowych:

-   **Skoki i spadki:** podczas monitorowania liczby błędów logowania do usługi lub liczby transakcji w witrynie e-commerce, nietypowe skoki lub spadki mogą wskazywać na ataki bezpieczeństwa lub przerwy w działaniu usługi.

-   **Pozytywne i negatywne trendy:** Podczas monitorowania użycia pamięci w komputerach, zmniejszenie rozmiaru wolnej pamięci wskazuje na potencjalny wyciek pamięci. W przypadku monitorowania długości kolejki usług trwały trend wzrostowy może wskazywać na podstawowy problem z oprogramowaniem.

-   **Zmiany poziomu i zmiany w zakresie dynamicznym wartości:** Zmiany poziomu opóźnień usługi po uaktualnieniu usługi lub niższych poziomach wyjątków po uaktualnieniu mogą być interesujące do monitorowania.

Interfejs API oparty na uczeniu maszynowym umożliwia:

-   **Elastyczne i niezawodne wykrywanie:** modele wykrywania anomalii umożliwiają użytkownikom konfigurowanie ustawień czułości i wykrywanie anomalii wśród sezonowych i niesezonowych zestawów danych. Użytkownicy mogą dostosować model wykrywania anomalii, aby interfejs API wykrywania był mniej lub bardziej czuły zgodnie z ich potrzebami. Oznaczałoby to wykrycie mniej lub bardziej widocznych anomalii w danych z sezonowymi wzorcami i bez ich wzorców.

-   **Skalowalne i terminowe wykrywanie:** tradycyjny sposób monitorowania z obecnymi progami ustalonymi przez wiedzę ekspertów w zakresie domeny jest kosztowny i nie skalowalny dla milionów dynamicznie zmieniających się zestawów danych. Modele wykrywania anomalii w tym interfejsie API są poznawane, a modele są automatycznie dostrajane z danych historycznych i w czasie rzeczywistym.

-   **Proaktywne i użyteczne wykrywanie:** Powolne wykrywanie trendów i zmian poziomu można zastosować do wczesnego wykrywania anomalii. Wczesne nieprawidłowe sygnały, które są wykrywane, mogą być wykorzystane do kierowania ludzi do badania i działania na obszarach problemowych. Ponadto modele analizy głównej przyczyny i narzędzia alertów można opracować na podstawie tej usługi interfejsu API wykrywania anomalii.

Interfejs API wykrywania anomalii jest skutecznym i wydajnym rozwiązaniem dla szerokiego zakresu scenariuszy, takich jak monitorowanie kondycji usługi i kluczowych wskaźników wydajności, IoT, monitorowanie wydajności i monitorowanie ruchu sieciowego. Oto kilka popularnych scenariuszy, w których ten interfejs API może być przydatny:

- Działy IT potrzebują narzędzi do śledzenia zdarzeń, kodu błędu, dziennika użycia i wydajności (procesora CPU, pamięci itd.) w odpowiednim czasie.

-   Witryny handlu internetowego chcą śledzić działania klientów, wyświetlenia strony, kliknięcia itd.

-   Przedsiębiorstwa użyteczności publicznej chcą śledzić zużycie wody, gazu, energii elektrycznej i innych zasobów.

-   Usługi zarządzania obiektami lub budynkami chcą monitorować temperaturę, wilgotność, ruch i tak dalej.

-   IoT/producenci chcą używać danych z czujników w szeregach czasowych do monitorowania przepływu pracy, jakości i tak dalej.

-   Dostawcy usług, takich jak centra obsługi, muszą monitorować trend popytu na usługi, wielkość incydentów, długość kolejki oczekiwania i tak dalej.

-   Grupy analityki biznesowej chcą monitorować nieprawidłowy ruch kluczowych wskaźników wydajności firmy (takich jak wielkość sprzedaży, nastroje klientów lub ceny) w czasie rzeczywistym.

### <a name="cloud-app-security"></a>Cloud App Security

[Usługa Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) jest krytycznym składnikiem stosu microsoft cloud security. Jest to kompleksowe rozwiązanie, które może pomóc twojej organizacji w yjdaszu, aby w pełni wykorzystać obietnicę aplikacji w chmurze. Zapewnia kontrolę dzięki lepszemu wglądowi w aktywność. Pomaga też zwiększyć ochronę kluczowych danych przechowywanych w aplikacjach w chmurze.

Organizacja może bezpieczniej przenieść się do chmury i jednocześnie utrzymać kontrolę nad danymi krytycznymi za pomocą narzędzi pomagających w ujawnianiu zasobów informatycznych niezatwierdzonych przez dział IT, ocenie ryzyka, egzekwowaniu zasad, badaniu działań i zatrzymywaniu zagrożeń.

| | |
|---|---|
| Odnajdź | Ujawniaj zasoby informatyczne niezatwierdzone przez dział IT za pomocą usługi Cloud App Security. Zyskaj widoczność przez odnajdywanie aplikacji, działań, użytkowników, danych i plików w Twoim środowisku chmury. Odnajduj aplikacje innych firm, które są połączone z Twoją chmurą.|
|Zbadaj | Badaj aplikacje w chmurze, używając narzędzi śledczych dla chmury, aby z bliska przyjrzeć się ryzykownym aplikacjom, konkretnym użytkownikom i plikom w Twojej sieci. Znajduj wzorce w danych zebranych z chmury. Generuj raporty w celu monitorowania chmury. |
| Kontrola | Ogranicz ryzyko przez skonfigurowanie zasad i alertów, aby osiągnąć maksymalną kontrolę ruchu sieciowego w chmurze. Użyj usługi Cloud App Security, aby przenieść użytkowników do bezpiecznych, alternatywnych, oficjalnie zaakceptowanych aplikacji w chmurze. |
| Ochrona | Usługa Cloud App Security umożliwia nakładanie sankcji na aplikacje lub zakazywanie ich stosowania, wymuszanie zapobiegania utracie danych, kontrolowanie uprawnień i udostępnianie oraz generowanie niestandardowych raportów i alertów. |
| Kontrola | Ogranicz ryzyko przez skonfigurowanie zasad i alertów, aby osiągnąć maksymalną kontrolę ruchu sieciowego w chmurze. Użyj usługi Cloud App Security, aby przenieść użytkowników do bezpiecznych, alternatywnych, oficjalnie zaakceptowanych aplikacji w chmurze. |
| | |


![Diagram zabezpieczeń aplikacji w chmurze](./media/threat-detection/azure-threat-detection-fig14.png)

Usługa Cloud App Security integruje widoczność z chmurą:

-   Za pomocą cloud discovery do mapowania i identyfikowania środowiska chmury i aplikacji w chmurze, z które korzysta organizacja.

-   Sankcjonowanie i zakazywanie aplikacji w chmurze.

-   Korzystanie z łatwych do wdrożenia łączników aplikacji, które korzystają z interfejsów API dostawcy, do widoczności i nadzoru aplikacji, z którymi się łączysz.

-   Pomaga mieć ciągłą kontrolę przez ustawienie, a następnie ciągłe dostosowywanie zasad.

Po zbieraniu danych z tych źródeł usługa Cloud App Security uruchamia na niej zaawansowaną analizę. Natychmiast generuje alerty o nietypowych działaniach i daje szczegółowy wgląd w środowisko chmury. W usłudze Cloud App Security możesz skonfigurować zasady i używać ich do ochrony wszystkiego w środowisku chmury.

## <a name="third-party-advanced-threat-detection-capabilities-through-the-azure-marketplace"></a>Zaawansowane funkcje wykrywania zagrożeń innych firm za pośrednictwem portalu Azure Marketplace

### <a name="web-application-firewall"></a>Zapora aplikacji internetowej

Zapora aplikacji sieci Web sprawdza przychodzący ruch internetowy i blokuje insekcje SQL, skrypty między witrynami, przesyłanie złośliwego oprogramowania, ataki DDoS aplikacji i inne ataki ukierunkowane na aplikacje internetowe. Sprawdza również odpowiedzi z serwerów sieci web zaplecza w celu zapobiegania utracie danych (DLP). Zintegrowany aparat kontroli dostępu umożliwia administratorom tworzenie szczegółowych zasad kontroli dostępu do uwierzytelniania, autoryzacji i księgowania (AAA), co zapewnia organizacjom silne uwierzytelnianie i kontrolę nad użytkownikami.

Zapora aplikacji sieci Web zapewnia następujące korzyści:

-   Wykrywa i blokuje zastrzyki SQL, skrypty między witrynami, przesyłanie złośliwego oprogramowania, ddos aplikacji lub inne ataki na aplikację.

-   Uwierzytelnianie i kontrola dostępu.

-   Skanuje ruch wychodzący w celu wykrycia poufnych danych i może maskować lub blokować wyciek informacji.

-   Przyspiesza dostarczanie zawartości aplikacji sieci web przy użyciu funkcji, takich jak buforowanie, kompresja i inne optymalizacje ruchu.

Aby zapoznać się z przykładami zapór aplikacji sieci web dostępnych w portalu Azure Marketplace, zobacz [Barracuda WAF, Zapora wirtualnej aplikacji sieci Web (vWAF), Imperva SecureSphere i zapora IP ThreatSTOP](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.waf).

## <a name="next-steps"></a>Następne kroki

- [Reagowanie na dzisiejsze zagrożenia:](../../security-center/security-center-alerts-overview.md#respond-threats)pomaga zidentyfikować aktywne zagrożenia, które są kierowane na zasoby platformy Azure i zapewnia szczegółowe informacje, które należy szybko reagować.

- [Wykrywanie zagrożeń bazy danych SQL azure:](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/)pomaga rozwiązać problemy dotyczące potencjalnych zagrożeń dla baz danych.
