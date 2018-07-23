---
title: Wykrywanie zagrożeń w usłudze Azure advanced | Dokumentacja firmy Microsoft
description: Więcej informacji na temat usługi Azure AD Identity Protection i jej możliwości.
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 9b688ca6faaa7e0d84dff0ae28e2a9b8b8279490
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2018
ms.locfileid: "37856882"
---
# <a name="azure-advanced-threat-detection"></a>Usługa Azure zaawansowane wykrywanie zagrożeń

Platforma Azure oferuje wbudowane funkcje wykrywania zaawansowanych zagrożeń za pośrednictwem usług, takich jak Azure Active Directory (Azure AD), Azure Operations Management Suite (OMS) i usługi Azure Security Center. Ta kolekcja usług zabezpieczeń i możliwości zapewnia prosty i szybki sposób zorientować się w ramach wdrożeń na platformie Azure.

System Azure oferuje szeroką gamę opcji, aby skonfigurować i dostosować zabezpieczeń, aby spełnić wymagania wdrożenia aplikacji. W tym artykule omówiono sposób spełniają te wymagania.

## <a name="azure-active-directory-identity-protection"></a>Ochrona tożsamości w usłudze Azure Active Directory

[Usługa Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) jest [usługi Azure Active Directory Premium P2](https://docs.microsoft.com/azure/active-directory/active-directory-editions) funkcja w wersji, która zapewnia Przegląd zdarzeń o podwyższonym ryzyku i potencjalnych luk w zabezpieczeniach, które mogą mieć wpływ na tożsamości w organizacji. Usługa Identity Protection używa istniejące możliwości wykrywania anomalii w usłudze Azure AD, które są dostępne za pośrednictwem [usługi Azure AD, nietypowe raporty aktywności](https://docs.microsoft.com/azure/active-directory/active-directory-view-access-usage-reports#anomalous-activity-reports)i wprowadza nowe typy zdarzeń o podwyższonym ryzyku, które mogą wykrywać anomalie w czasie rzeczywistym.

![Diagram usługi Azure AD Identity Protection](./media/azure-threat-detection/azure-threat-detection-fig1.png)

Usługa Identity Protection używa adaptacyjnych algorytmów uczenia maszynowego i algorytmy heurystyczne do wykrywania anomalii i podejrzanych zdarzeń, które mogą wskazywać, że tożsamość bezpieczeństwo zostało naruszone. Przy użyciu tych danych, Identity Protection generuje raporty i alerty, dzięki czemu mogą zbadania tych zdarzeń ryzyka i podjąć odpowiednie środki zaradcze lub Akcja ograniczenia.

Usługa Azure Active Directory Identity Protection jest większa niż monitorowania i raportowania narzędzia. Oparte na zdarzenia o podwyższonym ryzyku, Identity Protection oblicza poziomie ryzyka użytkownika, dla każdego użytkownika, tak że można skonfigurować zasady oparte na ryzyku, aby automatycznie chronić tożsamości organizacji.

Te zasady na podstawie ryzyka, oprócz innych [kontroli dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) są dostarczane przez usługę Azure Active Directory i [EMS](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access), można automatycznie Blokuj lub oferują działania korygujące adaptacyjne, obejmują resetowania hasła, jak i wymuszanie uwierzytelniania wieloskładnikowego.

### <a name="identity-protection-capabilities"></a>Funkcje ochrony tożsamości

Usługa Azure Active Directory Identity Protection jest większa niż monitorowania i raportowania narzędzia. W celu ochrony tożsamości w organizacji, można skonfigurować opartych na ryzykach zasady, które automatycznie odpowiadać na wykryte problemy, jeśli osiągnięty poziom ryzyka określony. Te zasady, oprócz innych kontrolek dostępu warunkowego, dostarczone przez usługę Azure Active Directory i usług EMS można automatycznie blokować lub zainicjować akcji adaptacyjne korygowania, które resetowania haseł w tym i wymuszanie uwierzytelniania wieloskładnikowego.

Przykłady niektóre metody, które mogą ułatwić Azure Identity Protection Zabezpieczanie kont i tożsamości obejmują:

[Wykrywanie zdarzeń o podwyższonym ryzyku i ryzykowne kont](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#detection)
-   Wykryj sześć typów zdarzeń o podwyższonym ryzyku, przy użyciu uczenia maszynowego i reguł heurystyczne.
-   Oblicz poziomy ryzyka użytkownika.
-   Podaj niestandardowy zalecenia, aby poprawić ogólny stan zabezpieczeń przez wyróżnianie luk w zabezpieczeniach.

[Badanie zdarzeń o podwyższonym ryzyku](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#investigation)
-   Wyślij powiadomienia do zdarzeń o podwyższonym ryzyku.
-   Badanie zdarzeń o podwyższonym ryzyku, korzystając z informacji istotnych i kontekstowych.
-   Podaj podstawowych przepływów pracy do śledzenia dochodzenia.
-   Zapewniają łatwy dostęp do akcji korygowania, takie jak resetowanie haseł.

[Zasady na podstawie ryzyka, dostęp warunkowy](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#risky-sign-ins)
-   Wyeliminowanie ryzykowne logowania za pomocą blokowania logowania bądź wymagają takiej wezwań do uwierzytelnienia Multi-Factor Authentication.
-   Blokowanie lub Zabezpieczanie kont użytkowników ryzykowne.
-   Wymagać od użytkowników rejestracji do uwierzytelniania wieloskładnikowego.

### <a name="azure-ad-privileged-identity-management"></a>Usługa Azure AD Privileged Identity Management

Za pomocą [usługi Azure Active Directory Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure), można zarządzać, kontrolę i monitorowanie dostępu w organizacji. Ta funkcja obejmuje dostęp do zasobów w usłudze Azure AD i innych usług Microsoft online services, takich jak usługi Office 365 lub Microsoft Intune.

![Diagram usługi Azure AD Privileged Identity Management](./media/azure-threat-detection/azure-threat-detection-fig2.png)

Usługa PIM pomoże Ci:

-   Pobierz alerty i raporty dotyczące administratorów usługi Azure AD i just-in-time (JIT) dostęp administracyjny do usług Microsoft online services, takich jak Office 365 i Intune.

-   Uzyskaj raporty dotyczące historii dostępu administratora i zmian w przypisaniach administratora.

-   Uzyskiwanie alertów dotyczących dostępu do ról uprzywilejowanych.

## <a name="operations-management-suite"></a>Operations Management Suite

[Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) jest firmy Microsoft oparte na chmurze rozwiązanie zarządzania IT, która ułatwia zarządzanie i chronić lokalne i infrastruktury chmury. Ponieważ OMS jest zaimplementowane jako usługa w chmurze, możesz mieć ona działa szybko przy minimalnych inwestycjach w usługi infrastruktury. Nowe funkcje zabezpieczeń są dostarczane automatycznie, co rutynowej konserwacji i uaktualniania kosztów.

Oprócz dostarcza wartościowych usług swój własny, OMS można zintegrować z składników programu System Center, takich jak [System Center Operations Manager](https://blogs.technet.microsoft.com/cbernier/2013/10/23/monitoring-windows-azure-with-system-center-operations-manager-2012-get-me-started/), aby rozszerzyć istniejące bezpieczeństwa zarządzania inwestycji do chmury. System Center i pakiet OMS mogą pracować razem, zapewniając pełnię możliwości hybrydowego zarządzania.

### <a name="holistic-security-and-compliance-posture"></a>Całościowe poziom zabezpieczeń i zgodności

[Pulpit nawigacyjny pakietu OMS, zabezpieczenia i inspekcja](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) zapewnia kompleksowy wgląd w Twojej organizacji stan zabezpieczeń IT, za pomocą wbudowanych zapytań wyszukujących istotne problemy, które wymagają Twojej uwagi. Pulpit nawigacyjny zabezpieczenia i inspekcja jest ekran domowy dla wszystko, co jest związane z zabezpieczeniami w pakiecie OMS. Zawiera ogólne informacje o stanie zabezpieczeń komputerów. Można również wyświetlić wszystkie zdarzenia w ciągu ostatnich 24 godzin, 7 dni lub dowolnego innego niestandardowego przedziału czasu.

Pulpity nawigacyjne usługi OMS pomóc szybko i łatwo zrozumieć ogólny stan zabezpieczeń w każdym środowisku, wszystkie w kontekście operacji IT, łącznie z oprogramowaniem aktualizacji, ocena, ocena ochrony przed złośliwym oprogramowaniem i linii bazowych konfiguracji. Dane dziennika zabezpieczeń jest łatwo dostępne, aby uprościć procesy inspekcji zabezpieczeń i zgodności.

![Na pulpicie nawigacyjnym pakietu OMS, zabezpieczenia i inspekcja](./media/azure-threat-detection/azure-threat-detection-fig3.jpg)

Na pulpicie nawigacyjnym pakietu OMS, zabezpieczenia i inspekcja jest podzielony na cztery główne kategorie:

-   **Domeny zabezpieczeń**: umożliwia dalsze możesz eksplorować rekordy zabezpieczeń w czasie; oceny złośliwego oprogramowania dostępu; ocen aktualizacji zabezpieczeń sieci w widoku, tożsamość i uzyskiwać dostęp do informacji; Wyświetlanie komputerów ze zdarzeniami bezpieczeństwa; i szybko uzyskać dostęp do platformy Azure Pulpit nawigacyjny usługi Security Center.

-   **Problemy godne uwagi**: umożliwia szybką identyfikację liczby aktywnych problemów i ważności problemów.

-   **Wykrycia (Podgląd)**: umożliwia zidentyfikowanie wzorców ataków, wyświetlając alerty zabezpieczeń w miarę ich występowania dotyczących poszczególnych zasobów.

-   **Analiza zagrożeń**: umożliwia zidentyfikowanie wzorców ataków, wyświetlając całkowita liczba serwerów z wychodzącym złośliwym ruchem IP, typu złośliwych zagrożeń oraz mapę lokalizacji adresów IP.

-   **Typowe zapytania dotyczące zabezpieczeń**: zawiera listę najbardziej typowych zapytań zabezpieczeń, które służą do monitorowania środowiska. Po wybraniu dowolnego zapytania, w okienku wyszukiwania otwiera i wyświetla wyniki tego zapytania.

### <a name="insight-and-analytics"></a>Usługa Insight and analytics
W środku [usługi Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) jest repozytorium pakietu OMS, która jest hostowana przez platformę Azure.

![Diagram usługi Insight and analytics](./media/azure-threat-detection/azure-threat-detection-fig4.png)

Możesz zbierać dane do repozytorium z połączonych źródeł przez konfigurowanie źródeł danych i dodawanie rozwiązań do subskrypcji.

![Na pulpicie nawigacyjnym pakietu OMS ](./media/azure-threat-detection/azure-threat-detection-fig5.png)

Źródeł danych, jak i rozwiązań należy utworzyć oddzielny rekord typy posiadają osobny zestaw właściwości, ale nadal możesz przeanalizować je razem w zapytaniach do repozytorium. Można użyć tych samych narzędzi i metod do pracy z różnymi rodzajami danych zbieranych przez różne źródła.


Większość interakcji z usługą Log Analytics jest za pośrednictwem portalu pakietu OMS, która działa w dowolnej przeglądarce i zapewnia dostęp do ustawień konfiguracji oraz wielu narzędzi służących do analizowania i działania dotyczące zebranych danych. Z poziomu portalu możesz użyć:
* [Przeszukiwanie dzienników](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-searches) gdzie można tworzyć zapytania służące do analizowania zebranych danych.
* [Pulpity nawigacyjne](https://docs.microsoft.com/azure/log-analytics/log-analytics-dashboards), które można dostosować za pomocą widoków graficznych dotyczących najbardziej wartościowych wyszukiwań.
* [Rozwiązania](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions), które zapewniają dodatkowe funkcje i narzędzia analizy.

![Narzędzia do analizy](./media/azure-threat-detection/azure-threat-detection-fig6.png)

Rozwiązania powodują dodawanie funkcji do usługi Log Analytics. Przede wszystkim działają w chmurze i zapewniają analizę danych zebranych w repozytorium pakietu OMS. Rozwiązania mogą również definiować nowe typy rekordów do pobrania, które mogą być analizowane za pomocą funkcji wyszukiwań lub przy użyciu dodatkowego interfejsu użytkownika udostępniająca rozwiązania na pulpicie nawigacyjnym pakietu OMS.

Pulpit nawigacyjny zabezpieczenia i inspekcja znajduje się przykład tego typu rozwiązań.

### <a name="automation-and-control-alert-on-security-configuration-drifts"></a>Automatyzacja i kontrola: drifts Alert dla konfiguracji zabezpieczeń

Usługa Azure Automation pozwala zautomatyzować procesy administracyjne za pomocą elementów runbook, które są oparte na programie PowerShell i działają w chmurze. Elementy Runbook mogą być również wykonywane na serwerze w lokalnym centrum danych w celu zarządzania zasobami lokalnymi. Usługa Azure Automation udostępnia funkcję zarządzania konfiguracją za pomocą programu PowerShell Desired State Configuration (DSC).

![Usługa Azure Automation diagramu](./media/azure-threat-detection/azure-threat-detection-fig7.png)

Można tworzyć i zarządzać zasobów DSC, które są hostowane na platformie Azure i zastosować je do chmury i lokalnych systemów. Wykonując, dzięki czemu można zdefiniować i automatycznego wymuszania ich konfiguracji lub uzyskaj raporty dotyczące odejściem od tego stanu w celu zapewnienia zabezpieczenia konfiguracje pozostają w ramach zasad.

## <a name="azure-security-center"></a>Azure Security Center

Usługa Azure Security Center pomaga w ochronie Twoich zasobów platformy Azure. Zapewnia zabezpieczenia zintegrowane monitorowanie i zarządzanie zasadami subskrypcji platformy Azure. W ramach usługi, można zdefiniować zasady dla obu subskrypcji platformy Azure i [grup zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) dla większy poziom szczegółowości.

![Diagram usługi Azure Security Center](./media/azure-threat-detection/azure-threat-detection-fig8.png)

Pracownicy naukowo-badawczy firmy Microsoft stale poszukują nowych zagrożeń. Mają oni dostęp do szerokiej gamy rozwiązań telemetrycznych uzyskanych dzięki obecności firmy Microsoft na całym świecie zarówno w chmurze, jak i lokalnie. Bogata i różnorodna kolekcja zestawów danych pozwala firmie Microsoft odnajdywać nowe trendy i wzorce ataków, które pojawiają się w jej lokalnych produktach dla firm i osób prywatnych, a także usługach online.

W związku z tym usługa Security Center może szybko zaktualizować swoje algorytmy wykrywania, jak atakujący wprowadzają nowe i coraz bardziej zaawansowanymi luki w zabezpieczeniach. Takie podejście pomaga zmieniającego się środowiska zagrożenia zmieniających.

![Wykrywanie zagrożeń w usłudze Security Center](./media/azure-threat-detection/azure-threat-detection-fig9.jpg)

Wykrywanie zagrożeń za pomocą usługi Security Center polega na automatycznym zbieraniu informacji o zabezpieczeniach uzyskanych z zasobów platformy Azure, sieci i powiązanych rozwiązań partnerskich. Analizuje ona te informacje, zestawiając informacje z wielu źródeł, aby zidentyfikować zagrożenia.

Alerty zabezpieczeń wraz z zaleceniami dotyczącymi usuwania zagrożeń są traktowane przez usługę Security Center priorytetowo.

Usługa Security Center wykorzystuje zaawansowane narzędzia analizy zabezpieczeń, które wykraczają daleko poza metody bazujące na sygnaturze. Przełomowe, danych big Data i [uczenia maszynowego](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) technologie są używane do oceny zdarzeń w całej sieci szkieletowej chmury. Zaawansowane funkcje analityczne potrafi wykrywać zagrożenia, które byłyby niemożliwe do wykrycia przy użyciu ręcznych metod i prognozowanie ewolucji ataków. Te typy analizy zabezpieczeń zostały omówione w kolejnych sekcjach.

### <a name="threat-intelligence"></a>Analiza zagrożeń

Firma Microsoft ma dostęp do ogromną ilość globalnej analizy zagrożeń.

Dane telemetryczne przepływu w ramach z wielu źródeł, takich jak Azure, Office 365, Microsoft CRM online, Microsoft Dynamics AX, outlook.com, MSN.com, jednostki Microsoft Digital crimes Unit (DCU) i Microsoft Security Response Center (MSRC).

![Wyniki analizy zagrożeń](./media/azure-threat-detection/azure-threat-detection-fig10.jpg)

Pracownicy naukowi otrzymują również informacje dotyczące analizy zagrożeń, który jest udostępniany między dostawcami usług w chmurze i zasubskrybowaniu źródeł analizy zagrożeń pochodzące od innych firm. Usługa Azure Security Center może użyć tych informacji, aby ostrzec użytkownika przed zagrożeniami ze strony znanych złośliwych podmiotów. Oto niektóre przykłady:

-   **Wykorzystywanie możliwości usługi machine learning**: Usługa Azure Security Center ma dostęp do ogromnej ilości danych dotyczących aktywności sieciowej w chmurze, która może służyć do wykrywania zagrożeń atakujących wdrożeń platformy Azure.

-   **Wykrywanie ataków siłowych**: Machine learning służy do tworzenia historycznych wzorzec prób dostępu zdalnego, co umożliwia wykrywanie ataków siłowych na porty protokołu Secure Shell (SSH), protokołu RDP (Remote Desktop) i SQL.

-   **Wychodzące wykrywanie ataków DDoS i botnet**: wspólny cel ataków, przeznaczonych dla zasobów w chmurze jest użycie moc obliczeniową tych zasobów do wykonania innych ataków.

-   **Nowe serwery analizy behawioralnej i maszyny wirtualne**: po serwer lub maszyna wirtualna zostanie naruszony, osoby atakujące stosować szerokiej gamy techniki, aby wykonać złośliwy kod w tym systemie podczas unikanie wykrywania, zapewnienia trwałości i eliminuje Opcje zabezpieczeń.

-   **Wykrywanie zagrożeń usługi Azure SQL Database**: zagrożeń wykrywania usługi Azure SQL Database, który identyfikuje nietypowe działania bazy danych wskazujące na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu lub wykorzystania baz danych.

### <a name="behavioral-analytics"></a>Analiza behawioralna

Analiza behawioralna to metoda, która polega na analizie danych i porównywaniu ich z kolekcją znanych wzorców. Wzorce te nie są jednak prostymi sygnaturami. Określa się je za pośrednictwem złożonych algorytmów uczenia maszynowego, które są stosowane w przypadku wielkich zestawów danych.

![Wyniki analizy behawioralnej](./media/azure-threat-detection/azure-threat-detection-fig11.jpg)

Wzorce są też określane przy użyciu dokładnej analizy złośliwych zachowań przez doświadczonych analityków. Usługa Azure Security Center może użyć analizy behawioralnej w celu zidentyfikowania zaatakowanych zasobów na podstawie analizy dzienników maszyny wirtualnej, dzienników urządzenia sieci wirtualnej, dzienników sieci szkieletowej, zrzutów awaryjnych i innych źródeł.

Ponadto wzorce są powiązane z innymi sygnałami dowody szeroko zakrojonej kampanii ataku. Ta korelacja pomaga w identyfikacji zdarzeń, które są zgodne ze sprawdzonymi wskaźnikami naruszenia bezpieczeństwa.

Oto niektóre przykłady:
-   **Wykonanie podejrzanego procesu**: osoby atakujące stosują kilka metod, które pozwalają na niezauważalne uruchomienie złośliwego oprogramowania. Na przykład osoba atakująca może nadać złośliwemu oprogramowaniu takie same nazwy co pliki w legalnym systemie, ale umieścić te pliki w innej lokalizacji, użyj nazwy, która jest podobny do nazwy niezłośliwego pliku lub zamaskować prawdziwe rozszerzenie tego pliku. Modeli usługi Security Center przetwarzać zachowania i wykonania procesu monitora wykryć elementy odstające, takie jak te.

-   **Ukryte złośliwe oprogramowanie i próby eksploatacji**: Zaawansowane złośliwe oprogramowanie może uniknięcia produktów tradycyjnych ochrony przed złośliwym oprogramowaniem, rezygnując z zapisu na dysku lub szyfrując składniki oprogramowania zapisywane na dysku. Jednak złośliwe oprogramowanie może zostać wykryte za pomocą analizy pamięci, ponieważ musi zostawiać ślady w pamięci do funkcji. Jeśli wystąpi awaria oprogramowania, zrzut awaryjny przechwytuje część pamięci w chwili pojawienia się awarii. Analizując pamięć w zrzucie awaryjnym, usługa Azure Security Center może wykryć metody użyte w celu wykorzystania luk w zabezpieczeniach oprogramowania, dostęp do poufnych danych i dyskretnego pozostania na zainfekowanym komputerze bez wywierania wpływu na wydajność sieci maszyny.

-   **Penetracja sieci i wewnętrzny Rekonesans**: aby utrwalić w sieci, w których bezpieczeństwo zostało naruszone, a następnie znajdź i zebrać cenne dane, osoby atakujące często usiłują przemieszczają się z zagrożoną maszynę do innych użytkowników w ramach tej samej sieci. Usługa Security Center monitoruje działania procesów i logowania do odnajdywania prób zdobycia przez osobę atakującą przyczółków w sieci, takich jak wykonanie polecenia zdalnego, sondowania sieci i wyliczania kont.

-   **Złośliwe skrypty programu PowerShell**: PowerShell może służyć przez osoby atakujące do wykonać złośliwy kod na docelowych maszynach wirtualnych do różnych celów. Usługa Security Center sprawdza działanie programu PowerShell w poszukiwaniu dowodów na podejrzaną aktywność.

-   **Ataki wychodzące**: dla osób atakujących częstym celem są zasoby w chmurze, ponieważ mogą one być użyte do przeprowadzenia dodatkowych ataków. Zagrożonych maszyn wirtualnych, na przykład może służyć do przeprowadzania ataków siłowych na inne maszyny wirtualne, wysyłania spamu lub skanowania otwartych portów i innych urządzeń w Internecie. Dzięki zastosowaniu uczenia maszynowego do ruchu sieciowego usługa Security Center może wykryć, kiedy wychodząca komunikacja sieciowa przekracza normy. Po wykryciu spamu usługa Security Center zestawia nietypowy ruch wiadomości e-mail z danymi analizy usługi Office 365 w celu ustalenia, czy wiadomość e-mail jest prawdopodobnie szkodliwa lub wynikiem uzasadnionej kampanii e-mail.

### <a name="anomaly-detection"></a>Wykrywanie anomalii

Usługa Azure Security Center używa również wykrywania anomalii do identyfikowania zagrożeń. W przeciwieństwie do analizy behawioralnej (która zależy od znanych wzorców wyprowadzonych z dużych zestawów danych), wykrywanie anomalii jest bardziej „spersonalizowane” i koncentruje się na liniach bazowych, które są specyficzne dla przeprowadzonych wdrożeń. Uczenie maszynowe jest stosowane do określania normalnego działania wdrożeń, a następnie generowania reguł definiujących odstające warunki, które mogą reprezentować zdarzenie związane z zabezpieczeniami. Oto przykład:

-   **Przychodzące ataki siłowe RDP/SSH**: wdrożenia może być zajęty maszyn wirtualnych przy użyciu wielu logowania każdego dnia lub innych maszyn wirtualnych, które mają kilka, nazwy logowania. Usługa Azure Security Center można ustalić linię bazową aktywności logowania dla tych maszyn wirtualnych i używać uczenia maszynowego, aby zdefiniować wokół działaniami normalnych logowania. W przypadku niezgodności z linią bazową zdefiniowana dla właściwości skojarzone z logowaniem, może zostać wygenerowany alert. To uczenie maszynowe określa co jest istotne.

### <a name="continuous-threat-intelligence-monitoring"></a>Ciągłe monitorowanie analizy zagrożeń

Usługa Azure Security Center działa zespołom zabezpieczeń badania i danych do analizy w całym świecie, które stale monitorują zmiany w przepisach zagrożeń. Obejmuje to następujące inicjatywy:

-   **Monitorowanie analizy zagrożeń**: analiza zagrożeń obejmuje mechanizmy, wskaźniki, konsekwencje i wiarygodne porady dotyczące istniejących lub pojawiających się zagrożeń. Te informacje są udostępniane w branży zabezpieczeń, a firma Microsoft stale monitoruje zagrożenia płynące z wewnętrznych i zewnętrznych źródeł.

-   **Udostępnianie sygnału**: wgląd w zabezpieczenia zespołów korzystających z szerokim portfolio firmy Microsoft w chmurze i lokalnych usług, serwerów i klientów urządzeń punktu końcowego są udostępniane i analizowane.

-   **Specjaliści ds. zabezpieczeń firmy Microsoft**: ciągłe zaangażowanie z zespołów firmy Microsoft, które działają w polach wyspecjalizowanych zabezpieczeń, takich jak analiza śledcza i wykrywanie ataków w sieci web.

-   **Dostrajanie wykrywania zagrożeń**: algorytmy są uruchamiane na rzeczywistych zestawach danych klienta i badacze zabezpieczeń pracują z klientami, aby zweryfikować wyniki. Wyniki prawdziwie i fałszywie dodatnie są używane w celu udoskonalania algorytmów uczenia maszynowego.

Wszystkie te połączone wysiłki skutkują nowe i ulepszone funkcje wykrywania, których można korzystać od razu. Nie ma akcji robić.

## <a name="advanced-threat-detection-features-other-azure-services"></a>Zaawansowanych funkcjach wykrywania zagrożeń: inne usługi systemu Azure

### <a name="virtual-machines-microsoft-antimalware"></a>Maszyny wirtualne: firmy Microsoft chroniąca przed złośliwym kodem

[Firmy Microsoft chroniąca przed złośliwym kodem](https://docs.microsoft.com/azure/security/azure-security-antimalware) for Azure to rozwiązanie jednego agenta do aplikacji i środowisk dzierżawy, zaprojektowane do uruchamiania w tle bez interwencji człowieka. Można wdrożyć ochrony odpowiednio do potrzeb obciążeń aplikacji, za pomocą obu podstawowe secure domyślnie lub zaawansowanych konfiguracji niestandardowej, włącznie z monitorowaniem ochrony przed złośliwym oprogramowaniem. Azure ochrony przed złośliwym kodem jest opcja zabezpieczeń maszyn wirtualnych platformy Azure, która jest automatycznie instalowany na wszystkich maszynach wirtualnych w modelu PaaS platformy Azure.

#### <a name="microsoft-antimalware-core-features"></a>Podstawowe funkcje w bazie wiedzy firmy Microsoft chroniące przed złośliwym kodem

Poniżej przedstawiono funkcje systemu Azure, wdrażanie i włączanie ochrony przed złośliwym oprogramowaniem firmy Microsoft dla aplikacji, które:

-   **Ochrona w czasie rzeczywistym**: monitoruje aktywność w usługach w chmurze i na maszynach wirtualnych, aby wykrywać i blokować wykonywanie złośliwego oprogramowania.

-   **Zaplanowane skanowanie**: okresowo wykonuje skanowanie docelowych do wykrywania złośliwego oprogramowania, w tym aktywnie uruchomione programy.

-   **Korygowaniem złośliwego oprogramowania**: automatycznie działa na wykryte złośliwe oprogramowanie, takie jak usuwanie lub poddawania kwarantannie złośliwych plików i czyszczenie wpisy rejestru złośliwe.

-   **Sygnatury aktualizacji**: automatycznie instaluje najnowszą podpisy ochrony (definicje wirusów) w celu zapewnienia ochrony na bieżąco z wstępnie ustaloną częstotliwością.

-   **Aktualizacje aparatu ochrony przed złośliwym oprogramowaniem**: automatycznie aktualizuje Microsoft Antimalware Engine.

-   **Aktualizacje platformy ochrony przed złośliwym oprogramowaniem**: automatycznie aktualizuje Platforma ochrony przed złośliwym oprogramowaniem firmy Microsoft.

-   **Aktywnej ochrony**: raporty telemetrii metadane dotyczące wykrytych zagrożeń i podejrzane zasoby platformy Microsoft Azure, aby zapewnić szybką odpowiedź do stale rosnących potrzeb świat zagrożeń, umożliwiając dostarczanie w czasie rzeczywistym podpisu synchroniczne za pośrednictwem Microsoft active protection systemu.

-   **Przykłady raportowania**: umożliwia i raportów przykłady do usługi ochrony przed złośliwym oprogramowaniem firmy Microsoft w celu pomocy usługi analizy i umożliwiają rozwiązywanie problemów.

-   **Wykluczenia**: umożliwia aplikacji i administratorów usług, aby skonfigurować niektóre pliki, procesów i dyski do wykluczenia z ochrony i skanowanie pod kątem wydajności i innych powodów.

-   **Zbieranie zdarzeń ochrony przed złośliwym oprogramowaniem**: rejestruje kondycję usługi ochrony przed złośliwym oprogramowaniem, podejrzanych działań i działania korygujące w dzienniku zdarzeń systemu operacyjnego i zbiera je na konto magazynu platformy Azure przez klienta.

### <a name="azure-sql-database-threat-detection"></a>Wykrywanie zagrożeń w usłudze Azure SQL Database

[Wykrywanie zagrożeń usługi Azure SQL Database](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/) to nowa funkcja analizy zabezpieczeń wbudowaną w usługę Azure SQL Database. Wykrywanie zagrożeń w usłudze Azure SQL Database działa przez całą dobę, aby dowiedzieć się, profilowanie i wykrywać nietypowe działania bazy danych, i identyfikuje reagowanie na potencjalne zagrożenia bazy danych.

Specjaliści ds. bezpieczeństwa lub innymi wyznaczeni administratorzy mogą uzyskać natychmiastowego wysłania powiadomienia o podejrzanych działaniach bazy danych, w jakiej występują. Każde powiadomienie zawiera szczegółowe informacje o podejrzanych działaniach i zaleca zastosowanie jak dalszego zbadania i wyeliminowania zagrożenia.

Obecnie usługa wykrywania zagrożeń bazy danych SQL Azure wykrywa potencjalnych luk w zabezpieczeniach oraz atakami polegającymi na iniekcji SQL i bazy danych nietypowe wzorce dostępu.

Po odebraniu powiadomienia e-mail wykrywania zagrożeń, użytkownicy mogą przejść i wyświetlanie rekordów inspekcji odpowiednie za pośrednictwem linku bezpośredniego w wiadomości e-mail. Łącze otwiera podgląd inspekcji lub wstępnie skonfigurowane inspekcji szablon programu Excel, który pokazuje rekordy inspekcji odpowiednie zbliżonym do czasu wystąpienia podejrzanego zdarzenia zgodnie z następujących czynności:

-   Inspekcja magazynu dla serwera bazy danych/za pomocą nietypowe działania bazy danych.

-   Inspekcji odpowiednie tabeli magazynu, który został użyty w czasie zdarzenia do zapisu w dzienniku inspekcji.

-   Rekordy godziny natychmiast po wystąpieniu zdarzeń inspekcji.

-   Inspekcji przy użyciu podobnych identyfikator zdarzenia w czasie zdarzenia (opcjonalny w przypadku niektórych detektory).

Detektory zagrożeń bazy danych SQL, użyj jednej z następujących metod wykrywania:

-   **Wykrywanie deterministyczne**: wykrywa podejrzane wzorce (na podstawie reguły) w zapytania klienta SQL, które odpowiadają znane ataki. Tej metody ma wykrywania wysokie i niskie pokrycie fałszywie dodatnie, ale ograniczone, ponieważ jego mieści się w kategorii "wykrywania niepodzielne."

-   **Wykrywanie zachowań**: wykrywa nietypowe działania, które są nietypowe zachowanie w bazie danych, które nie występowało w ciągu ostatnich 30 dni. Przykłady nietypowego działania klienta SQL może być wzrost nieudanych prób logowania lub zapytania, duże ilości danych wyodrębniania, nietypowe canonical zapytania lub nieznanego adresy IP używane do dostępu do bazy danych.

### <a name="application-gateway-web-application-firewall"></a>Zapory aplikacji sieci Web usługi Application Gateway

[Zapory aplikacji (WAF) w sieci Web](../app-service/environment/app-service-app-service-environment-web-application-firewall.md) jest funkcją [usługi Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-webapplicationfirewall-overview) zapewniająca ochronę aplikacjom sieci web korzystających z bramy aplikacji standardu [kontroli dostarczania aplikacji](https://kemptechnologies.com/in/application-delivery-controllers) funkcji. Zapora aplikacji sieci Web robi to, aby chronić je przed większością z [Otwórz sieci Web aplikacji Security Project (OWASP) top 10 najpopularniejszych luk w zabezpieczeniach z sieci web](https://www.owasp.org/index.php/Top_10_2010-Main).

![Diagram zapory aplikacji sieci Web bramy aplikacji](./media/azure-threat-detection/azure-threat-detection-fig13.png)

Zabezpieczenia obejmują:

-   Ochrona iniekcji SQL.

-   Obejmujące wiele lokacji skryptów ochrony.

-   Typowe sieci Web przed atakami ochrony, takie jak polegające na iniekcji poleceń, przemycaniu odpowiedzi HTTP, rozdzielaniu żądań HTTP i ataku dołączenie pliku zdalnego.

-   Ochrona przed naruszeniami protokołu HTTP.

-   Ochrona przed anomalii protokołu HTTP, takich jak brakujące hosta agenta użytkownika ani nagłówków accept.

-   Zapobieganie atakom robotów, przeszukiwarek i skanerów.

-   Wykrywanie typowych błędów konfiguracji aplikacji (czyli Apache, usługi IIS i tak dalej).

Konfigurowanie zapory aplikacji sieci Web w Twojej bramy application gateway zapewnia następujące korzyści:

-   Chroni aplikację sieci web z sieci web luk w zabezpieczeniach oraz atakami niewymagającymi modyfikacji kodu zaplecza.

-   Ochrona wielu aplikacji sieci web, w tym samym czasie za bramą aplikacji. Aplikacji umożliwia hosting maksymalnie 20 witryn sieci Web.

-   Monitoruje aplikacje przed atakami sieci web za pomocą raportów w czasie rzeczywistym, które są generowane przez dzienniki zapory aplikacji sieci Web bramy aplikacji.

-   Ułatwia spełnianie wymagań dotyczących zgodności. Niektóre formanty zgodności wymagają wszystkie połączone z Internetem punkty końcowe mają być chronione przez rozwiązanie zapory aplikacji sieci Web.

### <a name="anomaly-detection-api-built-with-azure-machine-learning"></a>Interfejs API wykrywania anomalii: Utworzonych za pomocą usługi Azure Machine Learning

Interfejs API wykrywania anomalii jest interfejs API, który ułatwia wykrywanie różnych nietypowe wzorce w danych szeregów czasowych. Interfejs API przypisuje wyniku anomalii do każdego punktu danych w szeregu czasowym, która może służyć do generowania alertów, monitorowania na pulpitach nawigacyjnych i nawiązywania łączności z systemami biletów.

[Interfejsu API wykrywania anomalii](https://docs.microsoft.com/azure/machine-learning/machine-learning-apps-anomaly-detection-api) może wykryć następujące rodzaje anomalii w danych szeregów czasowych:

-   **Gwałtowne wzrosty i spadki**: podczas monitorowania liczby nieudanych prób zalogowania do usługi lub liczbę wyewidencjonowań w witrynie handlu elektronicznego, nietypowego wzrostu lub spadku może oznaczać lub przerw w działaniu usługi.

-   **Trendy pozytywne i negatywne**: przypadku monitorowania użycia pamięci w przypadku komputerów, zmniejszanie wolnej pamięci rozmiar wskazuje Potencjalny przeciek pamięci. Długość kolejki usługi monitorowania stały trend wzrostowy może oznaczać problem z oprogramowaniem.

-   **Zmiany poziomów i zmiany w dynamicznym zakresie wartości**: zmian poziomów opóźnienia działania usługi po jej uaktualnieniu lub niższego poziomu wyjątków po uaktualnieniu może okazać się monitorowanie.

Maszyny oparte na nauce maszynowej interfejs API umożliwia:

-   **Elastyczna i niezawodna wykrywania**: modeli wykrywania anomalii Zezwól użytkownikom na ustawień ważność i wykrywać anomalie między zestawami danych sezonowych, jak i sezonowym. Użytkownik może zmienić model wykrywania anomalii, aby ułatwić wykrywanie interfejsu API z mniej lub więcej liter zgodnie ze swoimi potrzebami. Oznacza to, czy wykrywanie anomalii widocznych mniej lub więcej danych z i bez wzorców sezonowym.

-   **Skalowalna i terminowe wykrywania**: tradycyjnego monitorowania za pomocą progów obecne ustawione przez ekspertów znajomość domeny są kosztowne i nie jest skalowalna do milionów dynamicznie zmieniające się zestawów danych. Modele wykrywania anomalii, w tym interfejsie API są rozpoznawane i automatycznie specjalnie modeli z danych historycznych i czasu rzeczywistego.

-   **Proaktywne i możliwością wykrywania**: powolne trendów i wykrywanie zmiany poziomu mogą być stosowane umożliwiająca wczesne wykrywanie anomalii. Wczesne sygnały nietypowe, które są wykrywane może służyć do kierowania ludzi, aby zbadać i zajmującym się obszarów problemów. Ponadto główna przyczyna modele analizy i narzędzia alertów mogą być tworzone na podstawie tej usługi interfejsu API wykrywania anomalii.

Interfejs API wykrywania anomalii jest skuteczny i wydajny rozwiązanie służące do szerokiej gamy scenariuszy, takich jak usługa kondycji i kluczowych wskaźników wydajności, monitorowania, IoT, monitorowanie wydajności i monitorowanie ruchu sieciowego. Poniżej przedstawiono niektóre popularne scenariusze, w którym ten interfejs API może być przydatne:

- Działy IT potrzebują narzędzi do zdarzenia śledzenia, kod błędu, dziennika użycia i wydajności (procesora CPU, pamięci i tak dalej) w odpowiednim czasie.

-   Witryny handlu online chcą śledzić działania klienta, wyświetleń, kliknięć i tak dalej.

-   Narzędzie firmy mają być śledzone zużycie wody, gaz, energii elektrycznej i innych zasobów.

-   Funkcji lub tworzenie usług zarządzania chcesz monitorować temperaturę, wilgotność, ruchu i tak dalej.

-   IoT/producentów chcesz użyć danych z czujników w szeregach czasowych do przepływu pracy monitorowania, jakość i tak dalej.

-   Dostawcy usług, takich jak biura obsługi potrzebne do monitorowania usługi żądanie trend, liczba zdarzeń, długość kolejki oczekiwania i tak dalej.

-   Grup analiz biznesowych ma być monitorowany biznesowe wskaźniki KPI dotyczące (np. wielkość sprzedaży, opinie klientów lub cennik) nietypowy ruch w czasie rzeczywistym.

### <a name="cloud-app-security"></a>Cloud App Security

[Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) to kluczowy składnik stosu usługi Microsoft Cloud Security. To kompleksowe rozwiązanie, które mogą pomóc Twojej organizacji, po przeniesieniu do w pełni korzystać z prawdziwą aplikacji w chmurze. Jego przechowuje kontroli dzięki ulepszonemu wglądowi w działanie. Pomaga również zwiększyć ochronę kluczowych danych w aplikacjach w chmurze.

Za pomocą narzędzi, które ułatwiają odkrywanie działań IT, ocenie ryzyka, wymuszanie zasad, badaniu działań i zatrzymywaniu zagrożeń Twoja organizacja może bezpieczniej przenieść do chmury przy zachowaniu kontroli nad danymi krytycznymi.

| | |
|---|---|
| Wykrywanie | Odkrywanie działań IT z usługą Cloud App Security. Zyskaj widoczność przez odnajdywanie aplikacji, działań, użytkowników, danych i plików w Twoim środowisku chmury. Odkryj aplikacje innych firm, które są połączone z Twoją chmurą.|
|Zbadaj | Badaj aplikacje w chmurze przy użyciu narzędzi dowodowych chmury w celu dokładnego przeanalizowania ryzykownych aplikacji, konkretnych użytkowników i plików w Twojej sieci. Znajduj wzorce w danych zebranych z chmury. Generuj raporty do monitorowania chmury. |
| Kontrola | Ogranicz ryzyko przez skonfigurowanie zasad i alertów, aby osiągnąć maksymalną kontrolę ruchu sieciowego w chmurze. Usługa Cloud App Security umożliwia migrowanie użytkowników do chmury, bezpieczne alternatywnych, oficjalnie zaakceptowanych aplikacji. |
| Ochrona | Usługa Cloud App Security umożliwia oficjalne akceptowanie lub ze Stanów Zjednoczonych zabraniają aplikacje, wymuszać zapobieganie utracie danych, kontrolować uprawnienia i udostępnianie oraz generować raporty niestandardowe i alerty. |
| Kontrola | Ogranicz ryzyko przez skonfigurowanie zasad i alertów, aby osiągnąć maksymalną kontrolę ruchu sieciowego w chmurze. Usługa Cloud App Security umożliwia migrowanie użytkowników do chmury, bezpieczne alternatywnych, oficjalnie zaakceptowanych aplikacji. |
| | |


![Cloud App Security diagramu](./media/azure-threat-detection/azure-threat-detection-fig14.png)

Usługa cloud App Security integruje widoczność z Twoją chmurą przez:

-   Za pomocą rozwiązania Cloud Discovery w celu zamapowania i zidentyfikowania środowiska chmury oraz aplikacji w chmurze Twoją organizację.

-   Oficjalne akceptowanie i zabronienia aplikacji w chmurze.

-   Przy użyciu łączników łatwych do wdrożenia aplikacji, które korzystają z interfejsów API dostawców w celu zapewnienia wglądu i nadzoru aplikacji łączących się z.

-   Pomaganie w ciągłej kontroli przez ustawienie i stałe dostrajanie zasad.

Na zbieranie danych z tych źródeł, usługa Cloud App Security uruchamia zaawansowane analizy na ich temat. Natychmiast go ostrzega o nietypowych działaniach i daje szczegółowy wgląd w Twoim środowisku chmury. Można skonfigurować zasady usługi Cloud App Security i użyć jej do ochrony wszystkiego w środowisku chmury.

## <a name="third-party-advanced-threat-detection-capabilities-through-the-azure-marketplace"></a>Funkcje zaawansowane wykrywanie zagrożeń innych firm w witrynie Azure Marketplace

### <a name="web-application-firewall"></a>Zapora aplikacji internetowej

Zapora aplikacji sieci Web sprawdza przychodzący ruch internetowy i blokuje SQL wstrzyknięć kodu, skryptów między witrynami i przekazywaniu złośliwym oprogramowaniem, atakami DDoS aplikacji oraz innych ataków ukierunkowanych na aplikacje sieci web. Sprawdza ono również odpowiedzi z serwerów sieci web zaplecza dla ochrony przed utratą danych (DLP). Aparat kontroli dostępu zintegrowanej umożliwia administratorom tworzenie dostępu szczegółowej kontroli zasad uwierzytelniania i autoryzacji, i ewidencjonowanie aktywności (AAA), która umożliwia organizacjom silnego uwierzytelniania i użytkownik kontroli.

Zapora aplikacji sieci Web zapewnia następujące korzyści:

-   Wykrywa i blokuje SQL wstrzyknięć kodu, Cross-Site Scripting, przekazywanie złośliwego oprogramowania, ataki DDOS na aplikacje lub inne ataki, twoją aplikację.

-   Uwierzytelnianie i kontrola dostępu.

-   Skanuje ruchu wychodzącego do wykrywania poufnych danych i mogą zamaskować lub Blokuj informacje z wyciek.

-   Przyspiesza dostarczanie zawartości aplikacji sieci web, korzystając z możliwości, takich jak pamięci podręcznej, kompresji i inne optymalizacje ruchu.

Aby uzyskać przykłady zapory aplikacji sieci web, które są dostępne w witrynie Azure Marketplace, zobacz [zapory aplikacji internetowych Barracuda, zapory aplikacji sieci web wirtualne (vWAF) Brocade, Imperva SecureSphere i zapory adresów IP ThreatSTOP](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.waf).

## <a name="next-steps"></a>Kolejne kroki

- [Funkcje wykrywania usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities): ułatwia identyfikację aktywnych zagrożeń, docelowe zasoby platformy Azure, które dają wgląd, musisz szybko reagować.

- [Wykrywanie zagrożeń usługi Azure SQL Database](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/): pomaga rozwiązać problemów dotyczących potencjalnych zagrożeń dla baz danych.
