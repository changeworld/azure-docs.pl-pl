---
title: Rozwiązania analizy DNS w Azure Log Analytics | Dokumentacja firmy Microsoft
description: Konfigurowanie i korzystanie z rozwiązania analizy DNS w analizy dzienników można zebrać informacje dotyczące infrastruktury DNS dotyczące zabezpieczeń, wydajności i operacji.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: f44a40c4-820a-406e-8c40-70bd8dc67ae7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2018
ms.author: magoedte
ms.openlocfilehash: 6a59cf8b9444fe7cb197501c51d10dae81acb027
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
ms.locfileid: "30241949"
---
# <a name="gather-insights-about-your-dns-infrastructure-with-the-dns-analytics-preview-solution"></a>Zbieranie informacji na temat infrastruktury DNS z rozwiązaniem Podgląd Analytics DNS

![Symbol DNS Analytics](./media/log-analytics-dns/dns-analytics-symbol.png)

W tym artykule opisano sposób konfigurowania i używania rozwiązania Azure DNS analizy w Azure Log Analytics, aby zebrać informacje dotyczące infrastruktury DNS dotyczące zabezpieczeń, wydajności i operacji.

Analiza DNS ułatwia:

- Identyfikować klientów, którzy próbować rozpoznać nazw domen złośliwe.
- Zidentyfikuj starych rekordów.
- Określ nazwy domen często, którego dotyczy kwerenda i talkative klientów DNS.
- Wyświetl żądania obciążenia na serwerach DNS.
- Widok dynamiczny błędami rejestracji DNS.

Rozwiązanie gromadzi, analizuje i skorelowany analityczne DNS z systemem Windows i dzienników inspekcji i inne powiązane dane z serwerów DNS.

## <a name="connected-sources"></a>Połączone źródła

W poniższej tabeli opisano połączonych źródeł, które są obsługiwane przez to rozwiązanie:

| **Źródło połączenia** | **Pomoc techniczna** | **Opis** |
| --- | --- | --- |
| [Agenci dla systemu Windows](log-analytics-windows-agent.md) | Yes | Rozwiązanie zbiera informacje DNS z agentów systemu Windows. |
| [Agenci dla systemu Linux](log-analytics-linux-agents.md) | Nie | Rozwiązanie nie zbiera informacje DNS z bezpośredniego agentów systemu Linux. |
| [Grupa zarządzania programu System Center Operations Manager](log-analytics-om-agents.md) | Yes | Rozwiązanie zbiera informacje DNS z agentów w podłączonej grupy zarządzania programu Operations Manager. Połączenie bezpośrednie z agenta programu Operations Manager do usługi Operations Management Suite nie jest wymagane. Dane są przesyłane dalej z grupy zarządzania do repozytorium usługi Operations Management Suite. |
| [Konto usługi Azure Storage](log-analytics-azure-storage.md) | Nie | Magazyn Azure nie jest używany przez rozwiązanie. |

### <a name="data-collection-details"></a>Szczegóły danych kolekcji

Rozwiązanie zbiera DNS zapasów i dane dotyczące zdarzeń DNS z serwerów DNS z zainstalowanym agentem analizy dzienników. Dane te są następnie przekazywane do analizy dzienników i wyświetlane na pulpicie nawigacyjnym rozwiązania. Dane dotyczące zapasów, takie jak liczba serwerów DNS, strefy i rekordy zasobów są zbierane przez uruchomienie polecenia cmdlet programu DNS PowerShell. Dane są aktualizowane raz na dwa dni. Zbierane są dane dotyczące zdarzeń niemal w czasie rzeczywistym z [analityczne i dzienniki inspekcji](https://technet.microsoft.com/library/dn800669.aspx#enhanc) zapewniane przez udoskonalone rejestrowanie DNS i Diagnostyka systemu Windows Server 2012 R2.

## <a name="configuration"></a>Konfigurowanie

Poniższe informacje umożliwiają skonfigurowanie rozwiązania:

- Musi mieć [Windows](log-analytics-windows-agent.md) lub [programu Operations Manager](log-analytics-om-agents.md) agenta na każdym serwerze DNS, który chcesz monitorować.
- Rozwiązania analizy DNS można dodać do swojego obszaru roboczego usługi Operations Management Suite z [portalu Azure Marketplace](https://aka.ms/dnsanalyticsazuremarketplace). Można również użyć procesu opisanego w [rozwiązań dodać analizy dzienników z galerii rozwiązań](log-analytics-add-solutions.md).

Rozwiązanie rozpoczyna zbieranie danych bez konieczności dalszej konfiguracji. Jednak można użyć następującej konfiguracji, aby dostosować zbieranie danych.

### <a name="configure-the-solution"></a>Konfigurowanie rozwiązania

Na pulpicie nawigacyjnym rozwiązania kliknij **konfiguracji** aby otworzyć stronę konfiguracji analizy DNS. Istnieją dwa typy zmian konfiguracji, które mogą ułatwić:

- **Nazwy domen białej**. Rozwiązanie nie przetwarza wszystkie zapytania wyszukiwania. Ta funkcja obsługuje dozwolonych sufiksów nazw domen. Zapytania wyszukiwania, które rozpoznać nazwy domen, które odpowiada sufiksy nazw domen w tym listy dozwolonych adresów IP nie są przetwarzane przez to rozwiązanie. Nie przetwarza nazwy domen białej ułatwia Optymalizuj dane wysłane do analizy dzienników. Dozwolonych domyślna obejmuje nazw popularnych domeny publicznej, takich jak www.google.com i www.facebook.com. Można wyświetlić domyślną pełną listę, przewijając widok.

 Umożliwia zmodyfikowanie listy można dodać do sufiks nazwy domeny, który chcesz wyświetlić informacje na temat technologii wyszukiwania dla. Można również usunąć wszystkie sufiks nazwy domeny, których nie chcesz, aby wyświetlić informacje na temat technologii wyszukiwania dla.

- **Próg talkative klienta**. Klienci DNS, które przekracza wartość progową dla liczba żądań wyszukiwania są wyróżnione na **klientów DNS** bloku. Domyślny próg to 1000. Można edytować wartość progową.

    ![Nazwy domen białej](./media/log-analytics-dns/dns-config.png)

## <a name="management-packs"></a>Pakiety administracyjne

Jeśli używasz programu Microsoft Monitoring Agent można nawiązać połączenia z obszaru roboczego usługi Operations Management Suite, jest zainstalowana następujące pakiety administracyjne:

- Pakiet analizy modułu zbierającego dane DNS firmy Microsoft (Microsft.IntelligencePacks.Dns)

Jeśli grupę zarządzania programu Operations Manager jest podłączony do swojego obszaru roboczego usługi Operations Management Suite, następujące pakiety administracyjne są zainstalowane w programie Operations Manager podczas dodawania tego rozwiązania. Nie jest wymagana konfiguracja ani obsługi tych pakietów administracyjnych:

- Pakiet analizy modułu zbierającego dane DNS firmy Microsoft (Microsft.IntelligencePacks.Dns)
- Microsoft System Center Advisor DNS analizy konfiguracji (Microsoft.IntelligencePack.Dns.Configuration)

Aby uzyskać więcej informacji na temat aktualizowania pakietów administracyjnych rozwiązania, zobacz artykuł [Connect Operations Manager to Log Analytics](log-analytics-om-agents.md) (Połączenie programu Operations Manager z usługą Log Analytics).

## <a name="use-the-dns-analytics-solution"></a>Użyj rozwiązania analizy DNS

W tej sekcji opisano pulpitu nawigacyjnego funkcje i sposobu ich używania.

Po dodaniu rozwiązania do swojego obszaru roboczego, Kafelek rozwiązania na stronie Operations Management Suite — Przegląd zawiera krótkie podsumowanie infrastruktury DNS. Obejmuje on liczba serwerów DNS, w którym są zbierane dane. Zawiera również liczbę żądań wysyłanych przez klientów do rozpoznawania złośliwego domen w ciągu ostatnich 24 godzin. Po kliknięciu kafelka otwiera pulpitu nawigacyjnego rozwiązania.

![Kafelek DNS Analytics](./media/log-analytics-dns/dns-tile.png)

### <a name="solution-dashboard"></a>Pulpit nawigacyjny rozwiązania

Pulpit nawigacyjny rozwiązania zawiera podsumowanie informacji dla różnych funkcji rozwiązania. Zawiera również linki do szczegółowych widoku dla analizy śledczej i diagnostyki. Domyślnie dane są wyświetlane w ciągu ostatnich siedmiu dni. Zakres dat i czasu można zmienić za pomocą **formant wyboru daty i godziny**, jak pokazano na poniższej ilustracji:

![Formant wyboru godziny](./media/log-analytics-dns/dns-time.png)

Pulpit nawigacyjny rozwiązania zawiera następujące karty:

**Zabezpieczenia systemu DNS**. Raporty klientów DNS, które próbujesz nawiązać połączenia z domenami złośliwe. Przy użyciu źródła analizy zagrożeń firmy Microsoft, DNS Analytics może wykryć adresów IP, który próbujesz uzyskać dostęp do domen złośliwego klienta. W wielu przypadkach urządzeń zainfekowanych złośliwym oprogramowaniem "telefonowania" Center "command and control" złośliwe domeny przez przetłumaczenie nazwy domeny przed złośliwym oprogramowaniem.

![Blok zabezpieczenia systemu DNS](./media/log-analytics-dns/dns-security-blade.png)

Po kliknięciu IP klienta, na liście wyszukiwania dziennika zostanie otwarty i szczegóły odpowiednich zapytania wyszukiwania. W poniższym przykładzie DNS Analytics wykrył, że komunikacja została wykonana z [IRCbot](https://www.microsoft.com/security/portal/threat/encyclopedia/entry.aspx?Name=Win32/IRCbot):

![Wyniki wyszukiwania dziennika przedstawiający ircbot](./media/log-analytics-dns/ircbot.png)

Informacje ułatwiające identyfikację:

- Klient IP, który zainicjował komunikacji.
- Nazwa domeny, który jest rozpoznawany jako adres IP złośliwego.
- Adresy IP, które rozpoznawany jako nazwy domeny.
- Złośliwe adres IP.
- Ważność problemu.
- Przyczyna list dozwolonych złośliwego adres IP.
- Czas wykrycia.

**Zapytanie domen**. Zawiera najczęściej używane nazwy domen odpytywane przez klientów DNS w danym środowisku. Można wyświetlić listę nazw domen dotyczy zapytanie. Możesz również można przejść do szczegółów żądania wyszukiwania nazwy domeny określonej w dzienniku wyszukiwania.

![Blok Odpytano domen](./media/log-analytics-dns/domains-queried-blade.png)

**Klienci DNS**. Raporty klientów *naruszenie progu* liczby zapytań w wybranym okresie. Można wyświetlić listę wszystkich klientów DNS i Szczegóły zapytania wprowadzone przez ich w dzienniku wyszukiwania.

![Klienci DNS bloku](./media/log-analytics-dns/dns-clients-blade.png)

**Rejestracje dynamicznych DNS**. Raporty nazw błędami rejestracji. Wszystkie błędy rejestracji dla adresu [rekordy zasobów](https://en.wikipedia.org/wiki/List_of_DNS_record_types) (typ A i AAAA) są wyróżniane wraz z adresów IP, który zgłosił żądań rejestracji klienta. Te informacje można użyć, aby znaleźć przyczynę niepowodzenia rejestracji, wykonaj następujące czynności:

1. Znajdź strefy, który jest serwerem autorytatywnym dla nazwy, który klient próbuje zaktualizować.

2. Użyj rozwiązania, aby sprawdzić informacje spisu z tej strefy.

3. Sprawdź, czy jest włączona funkcja aktualizacji dynamicznej dla strefy.

4. Sprawdź, czy strefa jest konfigurowana zabezpieczone aktualizacje dynamiczne, czy nie.

    ![Dynamiczne bloku rejestracji DNS](./media/log-analytics-dns/dynamic-dns-reg-blade.png)

**Nazwa żądania rejestracji**. Górny Kafelek zawiera linię trendu udane i nieudane żądania dynamicznej aktualizacji DNS. Niższe kafelka wymieniono top 10 klientów, którzy wysyłają aktualizacji DNS nieudanych żądań do serwerów DNS, posortowane według liczby awarii.

![Nazwa rejestrowania żądań bloku ](./media/log-analytics-dns/name-reg-req-blade.png)

**Przykładowe zapytania analityczne Interfejsu**. Zawiera listę typowych zapytania wyszukiwania, które pobierają dane pierwotne analytics bezpośrednio.


![Przykładowe zapytania](./media/log-analytics-dns/queries.png)

Te zapytania można użyć jako punktu wyjścia do tworzenia zapytań niestandardowych raportowania. Zapytania łącze do strony wyszukiwania dziennika Analytics DNS, w którym są wyświetlane wyniki:

- **Lista serwerów DNS**. Przedstawia listę wszystkich serwerów DNS z ich skojarzone nazwy FQDN, nazwy domeny, nazwa lasu i serwera adresów IP.
- **Lista stref DNS**. Przedstawia listę wszystkich stref DNS z nazwą strefy skojarzony, stan aktualizacji dynamicznych, serwery nazw i stan podpisania DNSSEC.
- **Rekordy zasobów nieużywane**. Przedstawia listę wszystkich rekordów zasobów nieużywane/nieodświeżona. Ta lista zawiera nazwę w rekordzie zasobu, typ rekordu zasobu, skojarzone serwerem DNS, czas generowania rekordów i nazwę strefy. Ta lista służy do identyfikowania rekordów zasobów DNS, które nie są już w użyciu. Na podstawie tych informacji, można następnie usunąć te wpisy z serwerów DNS.
- **Serwery DNS zapytania obciążenia**. Przedstawia informacje, dzięki czemu można uzyskać punktu widzenia obciążenia DNS na serwerach DNS. Te informacje ułatwiają planowanie pojemności dla serwerów. Można przejść do **metryki** kartę, aby zmienić widok na wizualizację graficznego. Ten widok pomaga w zrozumieniu rozkład obciążenia DNS na serwerach DNS. Przedstawia on zapytanie DNS trendów szybkości dla każdego serwera.

    ![Wyniki wyszukiwania dziennika zapytań serwerów DNS](./media/log-analytics-dns/dns-servers-query-load.png)

- **Strefy DNS zapytania obciążenia**. Wyświetla statystyki strefy zapytania na sekundę DNS wszystkich stref na serwerach DNS zarządzanych przez rozwiązanie. Kliknij przycisk **metryki** kartę, aby zmienić widok szczegółowy rekordów do graficznego wizualizacja wyników.
- **Zdarzenia konfiguracji**. Pokazuje wszystkie zdarzenia zmiany konfiguracji DNS i skojarzonych komunikatów. Następnie możesz filtrować te zdarzenia na podstawie czasu zdarzeń, identyfikator zdarzenia, serwer DNS, lub kategorii zadań. Dane można łatwiej inspekcji zmian wprowadzonych do określonych serwerów DNS w określonym czasie.
- **Dziennik analityczne DNS**. Pokazuje wszystkie zdarzenia analityczne na wszystkich serwerach DNS są zarządzane przez rozwiązanie. Następnie możesz filtrować te zdarzenia na podstawie czasu zdarzeń, identyfikator zdarzenia, serwer DNS, adresu IP klienta, zgłaszający zapytania wyszukiwania i kategorii zadań typu zapytania. Zdarzenia analityczne DNS serwera Włącz działania śledzenia na serwerze DNS. Analityczne zdarzenie jest rejestrowane za każdym razem serwer wysyła lub odbiera informacje dotyczące systemu DNS.

### <a name="search-by-using-dns-analytics-log-search"></a>Wyszukiwanie za pomocą wyszukiwania dziennika analizy DNS

Na stronie dziennik wyszukiwania można utworzyć kwerendę. Wyniki wyszukiwania można filtrować za pomocą formantów zestawu reguł. Można też utworzyć zaawansowanych zapytań przekształcenia filtru i raportu na wyniki. Uruchom przy użyciu następujące zapytania:

1. W **pola zapytania wyszukiwania**, typ `DnsEvents` do wyświetlenia wszystkich zdarzeń DNS generowane przez serwery DNS są zarządzane przez rozwiązanie. Wyniki listy dane dziennika dla wszystkich zdarzeń związanych z zapytania wyszukiwania, dynamicznej rejestracji i zmiany konfiguracji.

    ![DnsEvents dziennik wyszukiwania](./media/log-analytics-dns/log-search-dnsevents.png)  

    a. Zaznacz, aby wyświetlić dane dziennika dla zapytania wyszukiwania **LookUpQuery** jako **podtypu** filtr z kontroli aspektu po lewej stronie. Tabela zawierająca wszystkie zdarzenia zapytania wyszukiwania dla wybranego okresu zostanie wyświetlony.

    b. Zaznacz, aby wyświetlić dane dziennika dla rejestracji dynamicznych **DynamicRegistration** jako **podtypu** filtr z kontroli aspektu po lewej stronie. Tabela zawierająca wszystkie zdarzenia dynamicznej rejestracji dla wybranego okresu zostanie wyświetlony.

    c. Aby wyświetlić dane dziennika dla zmian konfiguracji, wybierz **Zmianakonfiguracji** jako **podtypu** filtr z kontroli aspektu po lewej stronie. Listę wszystkich zdarzeń zmian konfiguracji dla wybranego okresu zostanie wyświetlony.

2. W **pola zapytania wyszukiwania**, typ `DnsInventory` do wyświetlania wszystkich DNS związane z magazynu danych do serwerów DNS, które są zarządzane przez rozwiązanie. Wyniki listy dane dziennika dla serwerów DNS strefy DNS i rekordów zasobów.

    ![DnsInventory dziennik wyszukiwania](./media/log-analytics-dns/log-search-dnsinventory.png)

## <a name="feedback"></a>Opinia

Istnieją dwa sposoby, które można przesłać opinię:

- **UserVoice**. Po pomysły dotyczące funkcji DNS Analytics na. Odwiedź stronę [strony Operations Management Suite UserVoice](https://aka.ms/dnsanalyticsuservoice).
- **Dołącz do naszych kohorty**. Zawsze możemy interesują Cię o nowych klientów, Dołącz do naszych stado uzyskać wczesny dostęp do nowych funkcji i pomóc nam w ulepszaniu DNS Analytics. Jeśli interesuje Cię w dołączenie naszych stado, wypełnij [szybkie badanie](https://aka.ms/dnsanalyticssurvey).

## <a name="next-steps"></a>Kolejne kroki

[Wyszukaj dzienniki](log-analytics-log-searches.md) Aby wyświetlić szczegółowe rekordy dziennika DNS.
