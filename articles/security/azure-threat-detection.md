---
title: Wykrywanie zagrożeń zaawansowane Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat ochrony tożsamości i jego możliwości.
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
ms.openlocfilehash: eb1bf9f6465a8a94fd2de75f581817887c333289
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="azure-advanced-threat-detection"></a>Wykrywanie zagrożeń zaawansowane Azure
## <a name="introduction"></a>Wprowadzenie

### <a name="overview"></a>Przegląd

Firma Microsoft opracowała szereg oficjalne dokumenty, omówienie zabezpieczeń najlepsze rozwiązania i listy kontrolne ułatwiają klientów platformy Azure o różnych funkcjach zabezpieczeń dostępnych w i otaczającego platformy Azure. Tematy zakresu pod względem szerokości i głębokość i są okresowo aktualizowane. Ten dokument jest częścią tej serii, zgodnie z opisem w poniższej sekcji abstrakcyjny.

### <a name="azure-platform"></a>Platformy Azure

Azure to platforma usługi chmury publicznej, która obsługuje najszerszych wybór systemów operacyjnych, programowania języków, struktury, narzędzia, baz danych i urządzeń.
Obsługuje następujące języki programowania:
-   Uruchom kontenery Linux z integracją rozwiązania Docker.
-   Tworzenie aplikacji za pomocą języka JavaScript, Python, .NET, PHP, Java i Node.js
-   Kompilacja zapleczy dla systemu iOS, Android i Windows urządzeń.

Usługi w chmurze publicznej Azure obsługuje te same technologie miliony deweloperów i informatyków już zależne i zaufania.

Podczas migrowania do chmury publicznej w organizacji, czy organizacja jest odpowiedzialny do ochrony danych i zapewnienia bezpieczeństwa i nadzór nad całym systemie.

Infrastruktura platformy Azure została zaprojektowana kompleksowo, począwszy od obiektu po aplikacje hostujące jednocześnie miliony klientów, i zapewnia wiarygodną podstawę zaspokajania potrzeb firm w zakresie bezpieczeństwa. Platforma Azure oferuje szeroką gamę opcji, aby skonfigurować i dostosować zabezpieczeń w celu spełnienia wymagań stanu wdrożenia aplikacji. Ten dokument pomaga spełnić te wymagania.

### <a name="abstract"></a>Abstrakcyjny

Microsoft Azure oferuje wbudowane funkcje wykrywania advanced threat za pośrednictwem usług, takich jak Azure Active Directory, Azure Operations Management Suite (OMS) i Centrum zabezpieczeń Azure. Ta kolekcja usług zabezpieczeń i możliwości umożliwia proste i szybkie zorientować się w ramach wdrożeń platformy Azure.

Ten dokument zawiera informacje pomocne "Microsoft Azure zbliża się do" pod kątem luk w zabezpieczeniach w przypadku zagrożenia diagnostycznych i analizy ryzyko związane z złośliwe działania przeznaczone dla serwerów i innych zasobów platformy Azure. Pomaga w identyfikacji metod identyfikacji i luk w zabezpieczeniach zarządzanie za pomocą zoptymalizowanego rozwiązania przez platformy Azure i usługi zabezpieczeń i technologie skierowane do klienta.

Ten dokument koncentruje się na technologii platformy Azure i kontrolek uwzględniającym klienta i nie jest podejmowana próba adres SLA cennik modeli i zagadnienia dotyczące praktyki DevOps.

## <a name="azure-active-directory-identity-protection"></a>Ochrona tożsamości w usłudze Azure Active Directory

![Ochrona tożsamości w usłudze Azure Active Directory](./media/azure-threat-detection/azure-threat-detection-fig1.png)


[Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) jest funkcją [Azure AD Premium P2](https://docs.microsoft.com/azure/active-directory/active-directory-editions) edition, który umożliwia przegląd zdarzeń o podwyższonym ryzyku i potencjalnych luk w zabezpieczeniach wpływających na tożsamości organizacji. Microsoft ma został zabezpieczających tożsamości opartej na chmurze za pośrednictwem dekadę i przy użyciu usługi Azure AD Identity Protection, Microsoft jest udostępnianie tych samych systemów ochrony do przedsiębiorstwa. Identity Protection korzysta z możliwości wykrywania anomalii istniejącej usługi Azure AD w dostępne za pośrednictwem [raporty dotyczące usługi Azure AD nietypowych działań](https://docs.microsoft.com/azure/active-directory/active-directory-view-access-usage-reports#anomalous-activity-reports)i wprowadza nowe typy zdarzeń ryzyka, które umożliwia wykrywanie anomalii w czasie rzeczywistym.

Usługa Identity Protection używa adaptacyjnych algorytmów uczenia maszynowego i algorytmów heurystycznych na potrzeby wykrywania anomalii i podejrzanych zdarzeń, które mogą wskazywać na naruszenia tożsamości. Przy użyciu tych danych, Identity Protection generuje raporty i alerty, które można zbadać zdarzenia o podwyższonym ryzyku i podjąć odpowiednie środki zaradcze lub ograniczenie akcji.

Ale Azure Active Directory Identity Protection jest większa niż przy użyciu narzędzi monitorowania i raportowania. Oparte na zdarzenia o podwyższonym ryzyku, Identity Protection oblicza poziom ryzyka użytkownika, dla każdego użytkownika, dzięki któremu można konfigurować zasady ryzyka, aby automatycznie chronić tożsamości organizacji.

Te zasady na podstawie ryzyka, oprócz innych [kontroli dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) udostępniane przez usługi Azure Active Directory i [EMS](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access), można automatycznie blokować lub akcji korygowania adaptacyjną, które obejmują ofert Resetowanie haseł i wymuszania uwierzytelnianie wieloskładnikowe.

### <a name="identity-protections-capabilities"></a>Funkcje ochrony tożsamości

Azure Active Directory Identity Protection jest większa niż monitorowania i raportowania narzędzia. Aby chronić tożsamości organizacji, umożliwiają konfigurowanie zasad ryzyka automatycznie odpowiadać na wykryte błędy, jeśli został osiągnięty poziom określonego ryzyka. Te zasady, oprócz innych kontroli dostępu warunkowego zapewniane przez usługę Azure Active Directory i EMS, można automatycznie blokować lub zainicjować akcji korygowania adaptacyjną, które resetowania haseł włącznie i wymuszania uwierzytelnianie wieloskładnikowe.

Przykłady niektórych metod, które mogą ułatwić ochronę tożsamości usługi Azure zabezpieczenia konta i tożsamości obejmują:

[Wykrywanie zdarzenia o podwyższonym ryzyku i ryzykowne kont:](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#detection)
-   Wykrywanie sześć typów zdarzeń ryzykiem przy użyciu uczenia maszynowego i reguł heurystyczne
-   Obliczanie poziomów ryzyka użytkownika
-   Zapewnianie niestandardowych zalecenia dotyczące poprawy ogólny stan zabezpieczeń przez wyróżnianie luk w zabezpieczeniach

[Badanie zdarzenia ryzyka:](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#investigation)
-   Wysyłanie powiadomienia o zdarzeniach ryzyka
-   Badania ryzyka zdarzeń za pomocą odpowiednich i kontekstowych informacji
-   Zapewnienie podstawowych przepływów pracy do śledzenia kontroli
-   Zapewniając łatwy dostęp do akcji korygowania, takich jak Resetowanie hasła

[Zasady dostępu warunkowego opartego na ryzyka:](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#risky-sign-ins)
-   Zasady złagodzić ryzykowne logowania przez blokowanie logowania lub wymaganie uwierzytelniania wieloskładnikowego wyzwania.
-   Zasady na wartość Blokuj lub kont użytkowników ryzykowne bezpieczne
-   Zasady, aby wymagać od użytkowników rejestracji w usłudze Multi-Factor authentication

### <a name="azure-ad-privileged-identity-management-pim"></a>Usługa Azure AD Privileged Identity Management (PIM)

Z [usługi Azure Active Directory (AD) Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure),

![Azure AD Privileged Identity Management](./media/azure-threat-detection/azure-threat-detection-fig2.png)

Możesz zarządzanie, sterowanie i monitorowanie dostępu w organizacji. Dotyczy to również dostępu do zasobów usługi Azure AD i innych usług online firmy Microsoft, takich jak Office 365 lub Microsoft Intune.

Azure AD Privileged Identity Management pomaga:

-   Alert zostanie wygenerowany i raport dotyczący administratorów usługi Azure AD i "just in time" dostęp administracyjny do usługi Online firmy Microsoft, takich jak usługi Office 365 i Intune

-   Uzyskaj raporty dotyczące historii dostępu administratora i zmian w przypisaniach administratora

-   Uzyskiwanie alertów dotyczących dostępu do ról uprzywilejowanych

## <a name="microsoft-operations-management-suite-oms"></a>Microsoft Operations Management Suite (OMS)

[Microsoft Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) firmy Microsoft w chmurze IT rozwiązanie do zarządzania ułatwiające zarządzanie i ochrona lokalnej infrastruktury w chmurze. Ponieważ oprogramowanie OMS jest zaimplementowane jako usługa w chmurze, może być dostępne szybko przy minimalnym poziomie inwestycji w usługi infrastruktury. Nowe funkcje zabezpieczeń są przeprowadzane automatycznie, zapisywanie rutynowej konserwacji i zaktualizuj kosztów.

Oprócz zapewnienia usług cenne samodzielnie, OMS można zintegrować z składników programu System Center takich jak [System Center Operations Manager](https://blogs.technet.microsoft.com/cbernier/2013/10/23/monitoring-windows-azure-with-system-center-operations-manager-2012-get-me-started/) rozszerzenie zabezpieczeń istniejących inwestycji zarządzania w chmurze. System Center i pakiet OMS mogą pracować razem, zapewniając pełnię możliwości hybrydowego zarządzania.

### <a name="holistic-security-and-compliance-posture"></a>Kompleksowe zabezpieczeń i stan zgodności

[OMS zabezpieczeń i inspekcji pulpitu nawigacyjnego](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) daje szczegółowy wgląd w Twojej organizacji IT stan zabezpieczeń z wbudowaną zapytania dotyczące godne uwagi problemy wymagające uwagi. Pulpit nawigacyjny zabezpieczeń i inspekcji jest ekranu głównego dla wszystkich powiązanych z zabezpieczeniami w OMS. Zawiera ogólne informacje o stanie zabezpieczeń komputerów. Daje również możliwość wyświetlenia wszystkich zdarzeń z ostatnich 24 godzin, 7 dni lub dowolnego innego przedziału czasu.

Pulpity nawigacyjne OMS pomóc szybko i łatwo zrozumieć stan ogólne bezpieczeństwo w innych środowiskach, w kontekście operacji IT, w tym: oceny aktualizacji oprogramowania, oceny ochrony przed złośliwym oprogramowaniem i linie bazowe konfiguracji. Ponadto danych dziennika zabezpieczeń jest łatwo dostępne, aby usprawnić procesy inspekcji zabezpieczeń i zgodności.

Pulpit nawigacyjny Zabezpieczenia i inspekcja w pakiecie OMS jest podzielony na cztery główne kategorie:

![Pulpit nawigacyjny Zabezpieczenia i inspekcja w pakiecie OMS](./media/azure-threat-detection/azure-threat-detection-fig3.jpg)

-   **Zabezpieczeń domeny:** w tym obszarze będzie dalszą analizę rekordy zabezpieczeń w czasie, dostęp do oceny złośliwego oprogramowania, aktualizacji oceny, zabezpieczenia sieci, informacje o tożsamości i dostępu, komputery ze zdarzeniami zabezpieczeń i szybko uzyskiwać dostęp do pulpitu nawigacyjnego Centrum zabezpieczeń Azure.

-   **Godne uwagi problemy:** ta opcja umożliwia szybką identyfikację liczba aktywnych problemów i ważność tych problemów.

-   **Wykryć (wersja zapoznawcza):** umożliwia identyfikowanie wzorców ataków przez wizualizacja alerty zabezpieczeń, zgodnie z ich odbywać się z zasobami.

-   **Analizy zagrożeń:** umożliwia identyfikowanie wzorców ataków przez wizualizacja łącznej liczby serwerów z wychodzącym ruchem złośliwego oprogramowania IP, typ złośliwe oprogramowanie i mapy, pokazujący, gdzie pochodzą z tych adresów IP.

-   **Typowe zapytania zabezpieczeń:** tę opcję, zawiera listę typowych zapytań zabezpieczeń, które służy do monitorowania środowiska. Po kliknięciu w jednym z tych zapytań zostanie otwarty blok wyszukiwania z wyników dla tego zapytania.

### <a name="insight-and-analytics"></a>Wgląd w dane i analizy
W Centrum [analizy dzienników](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) repozytorium OMS, która jest hostowana w chmurze Azure.

![Wgląd w dane i analizy](./media/azure-threat-detection/azure-threat-detection-fig4.png)

Dane są zbierane do repozytorium z połączonych źródeł przez konfigurowanie źródeł danych i dodawanie rozwiązań do subskrypcji.

![subskrypcja](./media/azure-threat-detection/azure-threat-detection-fig5.png)

Za pomocą źródeł danych i rozwiązań będą tworzone różne typy rekordów mających własne zestawy właściwości, ale które mogą być analizowane razem w zapytaniach do repozytorium. Pozwala to korzystać z tych samych narzędzi i metod do pracy z różnymi rodzajami danych zbieranych przez różne źródła.


W większości współpracy z analizy dzienników jest za pośrednictwem portalu OMS, która działa w dowolnej przeglądarce i zapewnia dostęp do ustawień konfiguracji i wiele narzędzi do analizowania i działa na zebranych danych. Z portalu, możesz użyć [dziennika wyszukiwania](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-searches) w przypadku, gdy tworzenia zapytań do analizowania danych zebranych [pulpity nawigacyjne](https://docs.microsoft.com/azure/log-analytics/log-analytics-dashboards), który można dostosować za pomocą graficznego widoków najbardziej przydatne wyszukiwania i [rozwiązań](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions), które zapewniają dodatkowe narzędzia funkcjonalność i analizy.

![narzędzia do analizy](./media/azure-threat-detection/azure-threat-detection-fig6.png)

Rozwiązania powodują dodawanie funkcji do usługi Log Analytics. Działają one głównie w chmurze i zapewniają analizę danych zebranych w repozytorium pakietu OMS. Mogą również definiować nowe typy rekordów do pobrania, które można przeanalizować za pomocą funkcji wyszukiwań w dziennikach lub przy użyciu dodatkowego interfejsu użytkownika udostępnionego przez rozwiązanie w ramach pulpitu nawigacyjnego pakietu OMS.
Bezpieczeństwo i inspekcji jest przykładem tego rodzaju rozwiązań.



### <a name="automation--control-alert-on-security-configuration-drifts"></a>Automatyzacja i kontrola: drifts alertu w konfiguracji zabezpieczeń

Automatyzacja Azure pozwala zautomatyzować procesów administracyjnych z elementami runbook, które są oparte na programie PowerShell i uruchom w chmurze Azure. Elementy Runbook mogą być również wykonywane na serwerze w lokalnym centrum danych w celu zarządzania zasobami lokalnymi. Usługi Automatyzacja Azure umożliwia zarządzanie konfiguracją w usłudze Konfiguracja DSC środowiska PowerShell (konfiguracji żądanego stanu).

![Azure Automation](./media/azure-threat-detection/azure-threat-detection-fig7.png)

Można tworzyć i zarządzanie zasobami DSC hostowana na platformie Azure i zastosować je do chmury oraz systemów lokalnych, aby zdefiniować i automatycznie wymuszać ich konfiguracji lub pobrać raporty na kilka ułatwia zapewnienie pozostawienie konfiguracjach zabezpieczeń w ramach zasad.

## <a name="azure-security-center"></a>Azure Security Center

Centrum zabezpieczeń Azure ułatwia ochronę zasobów platformy Azure. Zapewnia zabezpieczenia zintegrowane monitorowanie i zarządzanie zasadami subskrypcji platformy Azure. W ramach usługi, to można zdefiniować zasady nie tylko z subskrypcjami platformy Azure, ale także przed [grup zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal), dlatego może być bardziej szczegółowego.

![Azure Security Center](./media/azure-threat-detection/azure-threat-detection-fig8.png)

Pracownicy naukowo-badawczy firmy Microsoft stale poszukują nowych zagrożeń. Mają oni dostęp do szerokiej gamy rozwiązań telemetrycznych uzyskanych dzięki obecności firmy Microsoft na całym świecie zarówno w chmurze, jak i lokalnie. Bogata i różnorodna kolekcja zestawów danych pozwala firmie Microsoft odnajdywać nowe trendy i wzorce ataków, które pojawiają się w jej lokalnych produktach dla firm i osób prywatnych, a także usługach online.

W związku z tym Centrum zabezpieczeń można szybko aktualizować algorytmy wykrywania, ponieważ osoby atakujące wersji nowy, coraz bardziej zaawansowany luki w zabezpieczeniach. Takie podejście ułatwia sprostać środowisku przenoszenia fast zagrożeń.

![Security Center](./media/azure-threat-detection/azure-threat-detection-fig9.jpg)

Wykrywanie zagrożeń za pomocą usługi Security Center polega na automatycznym zbieraniu informacji o zabezpieczeniach uzyskanych z zasobów platformy Azure, sieci i powiązanych rozwiązań partnerskich.  Analizę te informacje korelowanie informacji z wielu źródeł, aby zidentyfikować zagrożeń.
Alerty zabezpieczeń wraz z zaleceniami dotyczącymi usuwania zagrożeń są traktowane przez usługę Security Center priorytetowo.

Usługa Security Center wykorzystuje zaawansowane narzędzia analizy zabezpieczeń, które wykraczają daleko poza metody bazujące na sygnaturze. Osiągnięć w danych big data i [uczenia maszynowego](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) technologii są używane do analizowania zdarzenia w chmurze całej sieci szkieletowej — wykrywanie zagrożenia, które nie można wysyłać do identyfikacji przy użyciu metod ręcznych i prognozowanie rozwoju ataki. Te analizy zabezpieczeń obejmuje następujące elementy.

### <a name="threat-intelligence"></a>Analiza zagrożeń

Firma Microsoft dysponuje ogromną ilością danych do globalnej analizy zagrożeń.
Dane telemetryczne przechodzi w z wielu źródeł, takich jak Azure, Office 365, Microsoft CRM online, Microsoft Dynamics AX, outlook.com, MSN.com, Microsoft jednostki ds. przestępstw cyfrowych (DCU) i Microsoft Security odpowiedzi Center (MSRC).

![Analiza zagrożeń](./media/azure-threat-detection/azure-threat-detection-fig10.jpg)

Pracownicy naukowi otrzymują również informacje dotyczące analizy zagrożeń, które są wymieniane między największymi dostawcami usług w chmurze oraz subskrypcje źródeł analizy zagrożeń od innych firm. Usługa Azure Security Center może użyć tych informacji, aby ostrzec użytkownika przed zagrożeniami ze strony znanych złośliwych podmiotów. Oto niektóre przykłady:

-   **Wykorzystanie mocy Machine Learning -** Centrum zabezpieczeń Azure ma dostęp do dużych ilości danych o aktywność w chmurze sieci, która może służyć do wykrywania zagrożeń przeznaczonych dla platformy Azure wdrożeń. Na przykład:

-   **Atak wykryć Force -** uczenia maszynowego służy do tworzenia historycznych wzorzec prób dostępu zdalnego, co umożliwia wykrywanie ataków siłowych wobec portów SSH, RDP i SQL.

-   **Wychodzące DDoS i wykrywania Botnet** — typowe celem ataków przeznaczonych dla zasobów w chmurze jest użyć moc obliczeniową tych zasobów, aby wykonać inne ataki.

-   **Nowe serwery analizy behawioralnej i maszyn wirtualnych -** po serwera lub maszyny wirtualnej zostanie naruszony, osoby atakujące stosować różnych technik w celu wykonania złośliwego kodu w tym systemie podczas unikanie wykrywania, zapewnienia trwałości i jak Opcje zabezpieczeń.

-   **Wykrywanie zagrożeń bazy danych Azure SQL -** wykrywanie zagrożeń bazy danych SQL Azure, który identyfikuje nietypowe działania bazy danych wskazujący nietypowe i potencjalnie szkodliwe próbuje uzyskać dostęp lub wykorzystać baz danych.

### <a name="behavioral-analytics"></a>Analiza behawioralna

Analiza behawioralna to metoda, która polega na analizie danych i porównywaniu ich z kolekcją znanych wzorców. Wzorce te nie są jednak prostymi sygnaturami. Określa się je za pośrednictwem złożonych algorytmów uczenia maszynowego, które są stosowane w przypadku wielkich zestawów danych.

![Analiza behawioralna](./media/azure-threat-detection/azure-threat-detection-fig11.jpg)


Są one również określane przez specjalistów od analizy, którzy dokonują dokładnej analizy złośliwych zachowań. Centrum zabezpieczeń Azure umożliwia analizy behawioralnej Zidentyfikuj złamany zasoby oparte na analizie dzienników maszyny wirtualnej, dzienniki urządzeń sieci wirtualnej, dzienniki sieci szkieletowej, zrzuty awaryjne i innych źródeł.

Ponadto istnieje korelacja z innymi sygnałami, które wykrywają dowody potwierdzające istnienie szeroko zakrojonej kampanii ataku. Ta korelacja pomaga w identyfikacji zdarzeń, które są zgodne ze sprawdzonymi wskaźnikami naruszenia bezpieczeństwa.

Oto niektóre przykłady:
-   **Wykonanie procesu podejrzane:** osoby atakujące stosować kilka technik w celu wykonania złośliwego oprogramowania bez wykrywania. Na przykład osoba atakująca może zapewniają tych samych nazw pliki systemowe uzasadnionych złośliwego oprogramowania, ale umieścić te pliki w innej lokalizacji, użyj nazwy, która jest bardzo podobna niegroźne pliku lub zamaskować rozszerzenie wartość true. Usługa Security Center modeluje zachowania procesów oraz monitoruje wykonanie procesu, aby wykryć elementy odstające, takie jak w przykładzie.

-   **Ukryte przed złośliwym oprogramowaniem i wykorzystywania prób:** zaawansowane złośliwego oprogramowania można uniknięcia produktów tradycyjnych ochrony przed złośliwym kodem, nigdy nie zapisu na dysku lub szyfrowania składników oprogramowania przechowywane na dysku. Jednak takie złośliwego oprogramowania mogą być wykrywane przy użyciu analizy pamięci, jak złośliwe oprogramowanie musi pozostać dane śledzenia w pamięci funkcji. Jeśli wystąpi awaria oprogramowania, zrzut awaryjny przechwytuje część pamięci w chwili pojawienia się awarii. Analizując pamięci w zrzutu awaryjnego, Centrum zabezpieczeń Azure wykrywa techniki wykorzystują luki w zabezpieczeniach oprogramowania, dostęp do poufnych danych i utrwalić ukryty sposób na maszynie z naruszonymi zabezpieczeniami bez wpływu na wydajność sieci maszyny.

-   **Penetracji przepływu i Rekonesans wewnętrzny:** Aby trwale w złamany sieci i zlokalizuj zbiorów cennych danych, osoby atakujące często usiłują penetrację z naruszonymi zabezpieczeniami maszyny do innych użytkowników w tej samej sieci. Centrum zabezpieczeń monitoruje działania procesu i logowania do odnajdywania próbuje rozwiń pozycję stopień osoba atakująca w ramach sieci, takie jak zdalne wykonywanie polecenia, sondowanie sieci i wyliczania kont.

-   **Złośliwych skryptów środowiska PowerShell:** programu PowerShell mogą posłużyć osobom atakującym do wykonania złośliwego kodu na docelowych maszyn wirtualnych do różnych celów. Usługa Security Center sprawdza działanie programu PowerShell w poszukiwaniu dowodów na podejrzaną aktywność.

-   **Wychodzące ataków:** osoby atakujące często docelowych zasobów w chmurze w celu użycia tych zasobów, aby zainstalować dodatkowe ataków. Zagrożonych maszyn wirtualnych, na przykład może służyć do uruchamiania ataków siłowych wobec innych maszyn wirtualnych, wysyłania SPAMU lub skanowania otwartych portów i innych urządzeń w Internecie. Dzięki zastosowaniu uczenia maszynowego do ruchu sieciowego usługa Security Center może wykryć, kiedy wychodząca komunikacja sieciowa przekracza normy. Podczas wysyłania SPAMU, Centrum zabezpieczeń również są powiązane z ruchu nietypowe wiadomości e-mail z informacjami z usługi Office 365, aby ustalić, czy wiadomość jest prawdopodobnie nefarious lub wynik kampanii uzasadnionych poczty e-mail.

### <a name="anomaly-detection"></a>Wykrywanie anomalii

Usługa Azure Security Center używa również wykrywania anomalii do identyfikowania zagrożeń. W przeciwieństwie do analizy behawioralnej (która zależy od znanych wzorców wyprowadzonych z dużych zestawów danych), wykrywanie anomalii jest bardziej „spersonalizowane” i koncentruje się na liniach bazowych, które są specyficzne dla przeprowadzonych wdrożeń. Uczenie maszynowe jest stosowane do określania normalnego działania wdrożeń, a następnie generowania reguł definiujących odstające warunki, które mogą reprezentować zdarzenie związane z zabezpieczeniami. Oto przykład:

-   **Ruchu przychodzącego protokołu RDP/SSH ataków siłowych:** wdrożeń może być zajęta maszyn wirtualnych z wielu logowania do każdego dnia i innych maszyn wirtualnych, które mają niektórych lub żadnych uprawnień do logowania. Centrum zabezpieczeń Azure można ustalić aktywność logowania linii bazowej dla tych maszyn wirtualnych i używać machine learning do definiowania wokół logowania normalnego działania. W przypadku niezgodności z linią bazową zdefiniowany dla właściwości, skojarzone z logowaniem, alert może zostać wygenerowany. To uczenie maszynowe określa co jest istotne.

### <a name="continuous-threat-intelligence-monitoring"></a>Analizy zagrożeń ciągłego monitorowania

Centrum zabezpieczeń Azure działa z zabezpieczeń zespoły badawczo -danych nauki na świecie które na stałe monitorowanie zmian w zagrożeń. Obejmuje to następujące inicjatywy:

-   **Monitorowanie analizy zagrożeń:** zagrożeń analizy zawiera mechanizmy, wskaźników, wpływ i możliwością porady dotyczące istniejących lub nowych zagrożeń. Te informacje są udostępniane w branży zabezpieczeń, a firma Microsoft stale monitoruje zagrożenia płynące z wewnętrznych i zewnętrznych źródeł.

-   **Udostępnianie sygnału:** wgląd w dane dotyczące zabezpieczeń zespołów całej szerokie oferty firmy Microsoft w chmurze i lokalnych usług, serwerów i klientów urządzeń programu endpoint są udostępniane i przeanalizowane.

-   **Specjalistów zabezpieczeń firmy Microsoft:** trwającą interakcji użytkowników z zespołów przez Microsoft działające w polach zabezpieczeń specjalnych, takich jak danych dowodowych i wykrywanie ataków w sieci web.

-   **Dostrajanie wykrywania:** algorytmy są uruchamiane w odniesieniu do zestawów danych rzeczywistych klienta i bezpieczeństwa pracowników naukowo-badawczych współpracuje z klientami, aby zweryfikować wyniki. Wyniki prawdziwie i fałszywie dodatnie są używane w celu udoskonalania algorytmów uczenia maszynowego.

Wszystkie te połączone wysiłki skutkują nowymi i ulepszonymi sposobami wykrywania zagrożeń, z których można korzystać od razu — użytkownik nie musi w tym kierunku nic robić.

## <a name="advanced-threat-detection-features---other-azure-services"></a>Funkcje wykrywania Advanced Threat — innymi usługami platformy Azure

### <a name="virtual-machine-microsoft-antimalware"></a>Maszyna wirtualna: Microsoft chroniące przed złośliwym kodem

[Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) Azure to rozwiązanie jednego agenta do aplikacji i środowisk dzierżawy, przeznaczony do pracy w tle bez udziału człowieka. Można wdrożyć ochrony oparte na potrzeby obciążeń aplikacji, z albo podstawowe secure domyślnie lub Zaawansowane Konfiguracja niestandardowa, w tym monitorowania ochrony przed złośliwym oprogramowaniem. Azure ochrony przed złośliwym kodem jest opcja zabezpieczeń dla maszyn wirtualnych platformy Azure i jest automatycznie instalowany na wszystkich maszynach wirtualnych Azure PaaS.

**Funkcje platformy Azure, aby wdrożyć i włączyć Antimalware firmy Microsoft dla aplikacji**

#### <a name="microsoft-antimalware-core-features"></a>Funkcje podstawowe ochrony przed złośliwym oprogramowaniem firmy Microsoft

-   **Ochrona w czasie rzeczywistym -** monitoruje aktywność usług w chmurze i maszyn wirtualnych do wykrywania i blokowania złośliwego oprogramowania wykonywania.

-   **Zaplanowane skanowanie -** okresowo wykonuje skanowanie docelowe do wykrywania złośliwego oprogramowania, włącznie z aktywnie działającymi programy.

-   **Korygowania złośliwego oprogramowania -** automatycznie pobiera wykrytego złośliwego oprogramowania, takich jak usuwanie lub poddawania złośliwych plików i czyszczenie wpisy rejestru złośliwych działań.

-   **Aktualizacje podpisu —** automatycznie instaluje najnowsze podpisy ochrony (definicje wirusów) do ochrony znajduje się aktualny na wstępnie określoną częstotliwością.

-   **Aktualizacje aparatu ochrony przed złośliwym kodem —** automatycznie aktualizuje aparat Antimalware firmy Microsoft.

-   **Aktualizacje platformy ochrony przed złośliwym kodem —** automatycznie aktualizuje platformy Microsoft Antimalware.

-   **Active protection -** raporty telemetrii metadanych o wykrytych zagrożeń i podejrzane zasobów do systemu Microsoft Azure w celu zapewnienia szybkiego odpowiedzi na zmieniające się zagrożeń i włączenie dostarczania w czasie rzeczywistym synchroniczne podpisu za pomocą Systemu Microsoft Active Protection (MAPS).

-   **Przykłady raportowanie —** zapewnia i raporty próbek do usługi Microsoft Antimalware ułatwia uściślić usługi i włączyć rozwiązywania problemów.

-   **Wykluczenia —** pozwala na konfigurowanie niektórych plików procesów, aplikacji i Administratorzy usługi i dyski, aby wykluczyć je z ochrony i skanowanie w poszukiwaniu wydajności i/lub innych powodów.

-   **Zbieranie zdarzeń ochrony przed złośliwym kodem —** rejestruje kondycję usługi ochrony przed złośliwym kodem, podejrzanych działań i korygowania akcje wykonywane w dzienniku zdarzeń systemu operacyjnego i zbiera je do konta usługi Azure Storage klienta.

### <a name="azure-sql-database-threat-detection"></a>Wykrywanie zagrożeń bazy danych Azure SQL

[Wykrywanie zagrożeń bazy danych w usłudze Azure SQL](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/) to nowa funkcja analizy zabezpieczeń wbudowanych w usłudze Azure SQL Database. Wykrywanie zagrożeń bazy danych SQL Azure działa przez całą dobę, aby dowiedzieć się, profile i wykrywa nietypowe działania bazy danych, identyfikuje potencjalne zagrożenia bazy danych.

Funkcjonariusze lub innych administratorów wyznaczonych można uzyskać natychmiastowego wysłania powiadomienia o bazy danych podejrzanych działaniach miarę ich występowania. Każde powiadomienie zawiera szczegółowe informacje o podejrzanych działaniach i zaleca jak do dalszego zbadania i ograniczyć zagrożenie.

Obecnie wykrywanie zagrożeń bazy danych SQL Azure wykrywa potencjalnych luk w zabezpieczeniach i ataki i wzorce dostępu nietypowych bazy danych.

Po otrzymaniu powiadomienia e-mail wykrywania zagrożeń, użytkownicy są możliwe do nawigowania i wyświetlanie rekordów inspekcji odpowiednich przy użyciu głębokie łącze w wiadomości e-mail, którego kliknięcie spowoduje otwarcie podglądu inspekcji i/lub wstępnie skonfigurowane, szablon programu Excel, pokazujący rekordów inspekcji odpowiednich wokół inspekcji czas podejrzane zdarzenia zgodnie z następujących czynności:
-   Magazyn danych inspekcji dla serwera bazy danych/z nietypowe działania bazy danych

-   Tabeli magazynu inspekcji odpowiednich użytej w czasie zdarzenia można zapisać dziennik inspekcji

-   Rejestruje następujące godziny inspekcji, ponieważ występuje zdarzenie.

-   Rekordy inspekcji podobne zdarzenia o identyfikatorze w czasie zdarzenia (opcjonalny w przypadku niektórych detektory)

Detektory zagrożeń bazy danych SQL, użyj jednej z następujących metod wykrywania:

-   **Wykrywanie deterministyczne —** wykrywa podejrzanych wzorców (na podstawie reguły) w zapytaniach klienta SQL zgodne znane ataki. Tej metody ma wykrywania wysoki i niski wynik fałszywie dodatni, jednak ograniczony pokrycia, ponieważ znajduje się w kategorii "wykryć atomic".

-   **Wykrywanie zachowań —** wady nietypowych działań, które jest nietypowe zachowanie bazy danych, która nie była widoczna w ciągu ostatnich 30 dni.  Przykład nietypowych działań klienta SQL mogą być kolekcji nieudanych logowań/kwerendy, duża liczba wyodrębniania danych, nietypowe canonical zapytania i nieznane adresy IP używane do dostępu do bazy danych

### <a name="application-gateway-web-application-firewall"></a>Zapora aplikacji sieci Web bramy aplikacji

[Zapora aplikacji sieci Web](../app-service/environment/app-service-app-service-environment-web-application-firewall.md) jest funkcją [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-webapplicationfirewall-overview) który zapewnia ochronę do aplikacji sieci web, które używają bramy aplikacji dla standardowej [formant dostarczania aplikacji](https://kemptechnologies.com/in/application-delivery-controllers)funkcji. Zapora aplikacji sieci Web dzieje się tak, aby chronić je przed większość [OWASP pierwszych 10 znanych luk w sieci web](https://www.owasp.org/index.php/Top_10_2010-Main)

![Firewally aplikacji sieci Web bramy aplikacji](./media/azure-threat-detection/azure-threat-detection-fig13.png)

-   Ochrona przed atakami polegającymi na iniekcji SQL

-   Ochrona przed atakami z użyciem skryptów wykorzystywanych w obrębie wielu witryn

-   Częste ataki w ramach sieci Web polegające na iniekcji poleceń, przemycaniu żądań HTTP, rozdzielaniu odpowiedzi HTTP i zdalnym dołączaniu plików

-   Ochrona przed naruszeniami protokołu HTTP

-   Ochrona przed nieprawidłowościami protokołu HTTP, takimi jak brakujące powiązania agenta i użytkownika hosta oraz akceptowanie nagłówków

-   Zapobieganie atakom z użyciem robotów, przeszukiwarek i skanerów

-   Wykrycie typowych błędów konfiguracji aplikacji (czyli Apache usług IIS, itp.)

Konfigurowanie zapory aplikacji sieci Web w aplikacji bramy miejsce następujące korzyści:

-   Ochrona aplikacji sieci Web przed atakami wykorzystującymi luki w zabezpieczeniach oraz atakami niewymagającymi modyfikacji kodu zaplecza.

-   Jednoczesna ochrona wielu aplikacji sieci Web za bramą aplikacji. Pojedyncza brama aplikacji umożliwia hosting maksymalnie 20 witryn sieci Web. Wszystkie witryny za bramą są chronione przed atakami z sieci Web.

-   Monitorowanie aplikacji sieci Web pod kątem ataków przy użyciu raportu w czasie rzeczywistym generowanego przez dzienniki zapory aplikacji sieci Web bramy aplikacji.

-   Niektóre środki kontrolne mające na celu zapewnienie zgodności wymagają, aby wszystkie punkty końcowe umożliwiające dostęp do Internetu były chronione z użyciem zapory aplikacji sieci Web. Korzystając z bramy aplikacji z zaporą aplikacji sieci Web, można spełnić te wymagania zgodności.

### <a name="anomaly-detection--an-api-built-with-azure-machine-learning"></a>Wykrywanie anomalii — interfejs API skompilowanej za pomocą usługi Azure Machine Learning

Wykrywanie anomalii to interfejs API skompilowanej za pomocą usługi Azure Machine Learning jest przydatne w przypadku wykrywania różnych typów nietypowe wzorce w danych serii czasu. Interfejs API przypisuje wynik anomalii do każdego punktu danych w szeregu czasowym, która może służyć do generowania alertów, monitorowanie za pomocą pulpitów nawigacyjnych lub nawiązać połączenie z systemu obsługi biletów.

[API wykrywania anomalii](https://docs.microsoft.com/azure/machine-learning/machine-learning-apps-anomaly-detection-api) może wykrywać następujące typy anomalii w danych w serii. czas:

-   **Wzrósł i DIP:** na przykład podczas monitorowania, liczba niepowodzeń logowania do usługi lub liczbę wyewidencjonowań w witrynie handlu elektronicznego, nietypowych wzrostów DIP może wskazywać ataki lub zakłócenia w świadczeniu usługi.

-   **Dodatnie i ujemne trendów:** podczas monitorowania użycia pamięci w przypadku komputerów, na przykład zmniejszanie rozmiaru wolnej pamięci jest wskaźnikiem potencjalny wyciek pamięci; podczas monitorowania usługi długość kolejki, trwałe tendencję może wskazywać podstawowej problem z oprogramowaniem.

-   **Poziom zmiany i dynamicznych spoza zakresu:** na przykład poziom zmiany w opóźnienia usługi po usługi uaktualniania lub niższe poziomy wyjątków, po uaktualnieniu może być interesujące do monitorowania.

Uczenie maszynowe oparte interfejsu API umożliwia:

-   **Wykrywanie elastyczna i niezawodna:** modeli wykrywania anomalii Zezwól użytkownikom na konfigurowanie ustawień czułości i wykrywania anomalii między zestawami danych okresach i okresach. Użytkownik może zmienić model wykrywania anomalii dokonanie wykrywania interfejsu API mniej lub więcej liter zgodnie z potrzebami. Oznacza to wykrywanie anomalii widocznych mniej lub więcej danych z włączonymi i wyłączonymi okresach wzorce.

-   **Skalowalna i odpowiednim wykrywania:** tradycyjny sposób monitorowania z progami obecne określone przez ekspertów wiedzy domeny są kosztowne i nie skalowalne do milionów dynamicznie zmieniające się zestawów danych. Modele wykrywania anomalii w tym interfejsie API są rozpoznawane i modele są automatycznie dopasowane dane historyczne jak w czasie rzeczywistym.

-   **Aktywne i można wykonać wykrywania:** trend powolne i wykrywania zmiany poziomu mogą być stosowane do wczesnego wykrywania anomalii. Wczesne sygnały nietypowe wykryto może służyć do kierowania ludzi, aby zbadać i korzystania z obszarów problemów.  Ponadto Przyczyna modeli analizy i alertów narzędzia mogą być opracowane na podstawie to wykrywanie anomalii usługi interfejsu API.

Wykrywanie anomalii interfejsu API to rozwiązanie wydajny i efektywny dla szerokiego zakresu scenariuszy, takich jak kondycja usługi & kluczowego wskaźnika wydajności monitorowania IoT, monitorowanie wydajności i monitorowanie ruchu sieciowego. Poniżej przedstawiono kilka scenariuszy popularnych, w którym ten interfejs API może być przydatna:
- Działy IT muszą narzędzia umożliwiające śledzenie zdarzeń, kod błędu dziennika użycia i wydajności (Procesora, pamięci i tak dalej), w odpowiednim czasie.

-   Witryn handlu online mają być śledzone działania klientów, liczba wyświetleń strony, kliknięć i tak dalej.

-   Narzędzie firmy mają być śledzone zużycia wody, gazu, energii elektrycznej i innych zasobów.

-   Zakładzie/budynku usług zarządzania ma być monitorowany temperatury, wilgotności, ruchu i tak dalej.

-   IoT/producentów mają być używane dane czujników w szeregi czasowe do przepływu pracy monitora, jakości i tak dalej.

-   Dostawców usług, takich jak biura obsługi muszą monitorować trend żądanie usługi, liczba zdarzeń, poczekaj długość kolejki i tak dalej.

-   Grup analiz biznesowych, chcesz monitorować biznesowe wskaźniki KPI dotyczące (takie jak sprzedaż, opinie klientów, cennik) nietypowe ruchu w czasie rzeczywistym.

### <a name="cloud-app-security"></a>Cloud App Security

[Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) jest składnikiem krytycznym stosu zabezpieczeń w chmurze firmy Microsoft. To kompleksowe rozwiązanie, które mogą pomóc Twojej organizacji, w przypadku przejścia do w pełni korzystać z promise aplikacji w chmurze, ale można przechowywać w kontroli dzięki ulepszonemu wglądowi w działanie. Pomaga również zwiększenia ochrony danych krytycznych w aplikacjach w chmurze.

Z narzędzia, które ułatwiają odkrywanie tle IT, ocenie ryzyka egzekwowanie zasad, badaniu działań i zatrzymywaniu zagrożeń, organizacji można bardziej bezpiecznie przenieść się do chmury, zachowując kontrolę nad danymi krytycznymi.

<table style="width:100%">
 <tr>
   <td>Wykrywanie</td>
   <td>Odkrywanie tle działu informatycznego dysponujące Cloud App Security. Zyskaj widoczność przez odnajdywanie aplikacji, działań, użytkowników, danych i plików w środowisku chmury. Odnajdywanie aplikacji innych firm, które są podłączone do chmury.</td>
 </tr>
 <tr>
   <td>Zbadaj</td>
   <td>Zbadaj aplikacji w chmurze przy użyciu narzędzi dowodowych chmury do szczegółowo w ryzykowne aplikacje, konkretnych użytkowników i plików w sieci. Znajdź wzorce w danych zebranych z chmury. Generuj raporty do monitorowania chmury.</td>

 </tr>
 <tr>
   <td>Kontrola</td>
   <td>Ogranicz ryzyko przez skonfigurowanie zasad i alertów, aby osiągnąć maksymalną kontrolę ruchu sieciowego w chmurze. Usługa Cloud App Security umożliwia migrować użytkowników do chmury bezpiecznej, oficjalnie zaakceptowanych aplikacji alternatyw.</td>

 </tr>
 <tr>
   <td>Ochrona</td>
   <td>Umożliwiają Cloud App Security oficjalne akceptowanie lub zabrania aplikacje, wymuszać zapobieganie utracie danych, uprawnienia kontroli i udostępnianie oraz generować raporty niestandardowe i alerty.</td>

 </tr>
 <tr>
   <td>Kontrola</td>
   <td>Ogranicz ryzyko przez skonfigurowanie zasad i alertów, aby osiągnąć maksymalną kontrolę ruchu sieciowego w chmurze. Usługa Cloud App Security umożliwia migrować użytkowników do chmury bezpiecznej, oficjalnie zaakceptowanych aplikacji alternatyw.</td>

 </tr>
</table>


![Cloud App Security](./media/azure-threat-detection/azure-threat-detection-fig14.png)

Usługa cloud App Security integruje widoczności z chmurą przez

-   Przy użyciu funkcji Cloud Discovery w celu zamapowania i zidentyfikowania środowiska chmury i aplikacje w chmurze używa Twojej organizacji.


-   Oficjalne akceptowanie lub zabronienia aplikacji w chmurze.



-   Używanie łączników łatwa do wdrożenia aplikacji, które korzystają z dostawcy interfejsów API, widoczność i ładu aplikacji łączących się z.

-   Pomaga ma ciągła kontrola ustawienie i stałe dostrajanie, zasad.

Na zbieranie danych z tych źródeł, Cloud App Security uruchamia zaawansowane analizy danych. Natychmiast go ostrzega o nietypowych działań i umożliwia dokładną widoczność w środowisku chmury. Możesz skonfigurować zasady w usłudze Cloud App Security i użyć jej do ochrony wszystkich elementów w środowisku chmury.

## <a name="third-party-atd-capabilities-through-azure-marketplace"></a>Możliwości ATD innej firmy za pomocą portalu Azure Marketplace

### <a name="web-application-firewall"></a>Zapora aplikacji sieci Web

Zapora aplikacji sieci Web sprawdza sieci web dla ruchu przychodzącego ruchu i bloki iniekcji SQL, Cross-Site skryptów, złośliwego oprogramowania przekazywania & aplikacji przed atakami DDoS oraz inne ataki celem aplikacji sieci web. Sprawdza również odpowiedzi z serwerów sieci web zaplecza dla zapobiegania utracie danych (DLP). Aparat kontroli dostępu zintegrowane umożliwia administratorom tworzenie zasad kontroli dostępu szczegółowego dla uwierzytelniania, autoryzacji i Ewidencjonowanie, która umożliwia organizacjom silnego uwierzytelniania i kontroli użytkownika.

**Najważniejsze funkcje:**
-   Wykrywa i blokuje SQL iniekcji, skryptów krzyżowych, przekazywanie złośliwego oprogramowania, aplikacji przed atakami DDoS lub inne ataki aplikacji.

-   Uwierzytelnianie i kontrola dostępu.

-   Skanuje ruch wychodzący do wykrywania poufnych danych i można zamaskować lub blokowanie wyciek.

-   Przyspiesza dostarczanie zawartości aplikacji sieci web, za pomocą funkcji, takich jak pamięci podręcznej, kompresji i inne optymalizacje ruchu.

Poniżej przedstawiono przykład dostępne w usłudze Azure rynek zapory aplikacji sieci Web:

[Zapora aplikacji sieci Web barracuda, Zapora Brocade do aplikacji wirtualnych sieci Web (Brocade vWAF), Imperva SecureSphere i ThreatSTOP IP zapory.](https://azure.microsoft.com/marketplace/partners/brocade_communications/brocade-virtual-web-application-firewall-templatevtmcluster/)

## <a name="next-steps"></a>Następne kroki

- [Funkcje wykrywania usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities)

Możliwości wykrywania zaawansowanych Centrum zabezpieczeń Azure pomaga w identyfikacji active zagrożenia przeznaczonych dla zasobów platformy Microsoft Azure i udostępnia szczegółowe informacje, konieczne jest szybko reagować.

- [Wykrywanie zagrożeń bazy danych Azure SQL](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/)

Wykrywanie zagrożeń bazy danych w usłudze Azure SQL pomogła rozwiązania ich problemów dotyczących potencjalnych zagrożeń do ich bazy danych.
