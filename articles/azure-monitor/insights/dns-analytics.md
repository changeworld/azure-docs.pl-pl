---
title: DNS Analytics rozwiązanie w Azure Monitor | Microsoft Docs
description: Skonfiguruj rozwiązanie DNS Analytics w Azure Monitor, aby zebrać szczegółowe informacje dotyczące infrastruktury DNS w zakresie zabezpieczeń, wydajności i operacji.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: f44a40c4-820a-406e-8c40-70bd8dc67ae7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/20/2018
ms.author: magoedte
ms.openlocfilehash: ad61743751ace9ca0c7eba12ffcea5f15e1157d5
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316185"
---
# <a name="gather-insights-about-your-dns-infrastructure-with-the-dns-analytics-preview-solution"></a>Zbierz informacje na temat infrastruktury DNS za pomocą rozwiązania DNS Analytics w wersji zapoznawczej

![Symbol analiza DNS](./media/dns-analytics/dns-analytics-symbol.png)

W tym artykule opisano sposób konfigurowania i używania rozwiązania Azure DNS Analytics w Azure Monitor w celu zbierania szczegółowych informacji o infrastrukturze DNS w zakresie zabezpieczeń, wydajności i operacji.

Analiza DNS umożliwia:

- Zidentyfikuj klientów próbujących rozpoznać nazwy domen złośliwe.
- Identyfikowanie starych rekordów.
- Identyfikuj domeny często poszukiwanych nazw i próg klientów DNS.
- Widok obciążenie żądaniami na serwerach DNS.
- Widok dynamiczny niepowodzenia rejestracji DNS.

Rozwiązanie zbiera, analizuje i koreluje analityczne DNS Windows i dzienniki inspekcji i inne powiązane dane z serwerów DNS.

## <a name="connected-sources"></a>Połączone źródła

W poniższej tabeli opisano połączone źródła, które są obsługiwane przez to rozwiązanie:

| **Połączone źródło** | **Pomoc techniczna** | **Opis** |
| --- | --- | --- |
| [Agenci dla systemu Windows](../platform/agent-windows.md) | Yes | Rozwiązanie zbiera informacje DNS z agentów dla Windows. |
| [Agenci dla systemu Linux](../learn/quick-collect-linux-computer.md) | Nie | Rozwiązanie zbiera informacje DNS z bezpośrednich agentów systemu Linux. |
| [Grupa zarządzania programu System Center Operations Manager](../platform/om-agents.md) | Yes | Rozwiązanie zbiera informacje DNS z agentów w połączonej grupie zarządzania programu Operations Manager. Bezpośrednie połączenie z agenta Operations Manager do Azure Monitor nie jest wymagane. Dane są przekazywane z grupy zarządzania do obszaru roboczego usługi Log Analytics. |
| [Konto usługi Azure Storage](../platform/collect-azure-metrics-logs.md) | Nie | Usługa Azure storage nie jest używana przez to rozwiązanie. |

### <a name="data-collection-details"></a>Szczegóły dotyczące zbierania danych

Rozwiązanie zbiera spis DNS i dane dotyczące zdarzeń DNS z serwerów DNS, którym jest zainstalowany agent usługi Log Analytics. Te dane są następnie przekazywane do Azure Monitor i wyświetlane na pulpicie nawigacyjnym rozwiązania. Dane dotyczące zapasów, takie jak liczba serwerów DNS, strefy i rekordy zasobów są zbierane przez uruchomienie polecenia cmdlet programu DNS PowerShell. Dane są aktualizowane co dwa dni. Dane dotyczące zdarzeń są zbierane w czasie rzeczywistym z [analitycznych i dzienników inspekcji](https://technet.microsoft.com/library/dn800669.aspx#enhanc) podał rozszerzonych DNS rejestrowania i diagnostyki w systemie Windows Server 2012 R2.

## <a name="configuration"></a>Konfigurowanie

Skorzystaj z poniższych informacji, aby skonfigurować rozwiązania:

- Konieczne jest posiadanie [Windows](../platform/agent-windows.md) lub [programu Operations Manager](../platform/om-agents.md) agenta na każdym serwerze DNS, który chcesz monitorować.
- Rozwiązanie analiza DNS można dodać do obszaru roboczego usługi Log Analytics z [portalu Azure Marketplace](https://aka.ms/dnsanalyticsazuremarketplace). Można również użyć procesu opisanego w temacie [Dodawanie rozwiązań Azure monitor z Galeria rozwiązań](solutions.md).

Rozwiązanie rozpoczyna zbieranie danych bez konieczności dalszej konfiguracji. Jednak można użyć następującej konfiguracji, aby dostosować zbieranie danych.

### <a name="configure-the-solution"></a>Skonfiguruj rozwiązanie

Na pulpicie nawigacyjnym rozwiązania kliknij **konfiguracji** aby otworzyć stronę konfiguracji analizy DNS. Istnieją dwa typy, które można wprowadzać zmian w konfiguracji:

- **Nazwy domen na liście dozwolonych**. Rozwiązanie nie przetwarza wszystkie zapytania wyszukiwania. Przechowuje go z listy dozwolonych sufiksów nazw domen. Zapytania dotyczące wyszukiwania rozpoznawane jako nazwy domen zgodne z sufiksami nazw domen na tej liście dozwolonych elementów nie są przetwarzane przez to rozwiązanie. Nie przetwarzanie nazw domen listy dozwolonych pomaga zoptymalizować dane wysyłane do Azure Monitor. Domyślna lista dozwolonych zawiera popularne publiczne nazwy domen, takie jak www.google.com i www.facebook.com. Możesz wyświetlić domyślną pełną listę, przewijając.

  Można zmodyfikować listę, aby dodać do sufiks nazwy domeny, który chcesz wyświetlić szczegółowe informacje wyszukiwania. Można również usunąć wszystkie sufiks nazwy domeny, których nie chcesz, aby wyświetlić szczegółowe informacje wyszukiwania.

- **Próg klienta z**. Klientów DNS, które przekraczają wartość progową, dla liczby żądań wyszukiwania zostaną wyróżnione w **klientów DNS** bloku. Domyślny próg wynosi 1000. Można edytować wartość progową.

    ![Nazwy domen na liście dozwolonych](./media/dns-analytics/dns-config.png)

## <a name="management-packs"></a>Pakiety administracyjne

Jeśli używasz programu Microsoft Monitoring Agent połączyć się z obszaru roboczego usługi Log Analytics, jest zainstalowane następujące pakiety administracyjne:

- Microsoft DNS Data Collector Intelligence Pack (Microsoft. IntelligencePacks. DNS)

Jeśli grupa zarządzania programu Operations Manager jest połączona z obszarem roboczym usługi Log Analytics, następujące pakiety administracyjne są instalowane w programie Operations Manager, po dodaniu tego rozwiązania. Nie ma wymaganej konfiguracji lub obsługi tych pakietów administracyjnych:

- Microsoft DNS Data Collector Intelligence Pack (Microsoft. IntelligencePacks. DNS)
- Analiza DNS Klasyfikator programu Microsoft System Center — konfiguracja (Microsoft.IntelligencePack.Dns.Configuration)

Aby uzyskać więcej informacji na temat aktualizowania pakietów administracyjnych rozwiązania, zobacz artykuł [Connect Operations Manager to Log Analytics](../platform/om-agents.md) (Połączenie programu Operations Manager z usługą Log Analytics).

## <a name="use-the-dns-analytics-solution"></a>Korzystanie z odpowiedniego rozwiązania analiza DNS

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]


Kafelek DNS zawiera liczbę serwerów DNS, na których są zbierane dane. Obejmuje również liczbę żądań wysyłanych przez klientów, aby rozwiązać złośliwe domeny w ciągu ostatnich 24 godzin. Kliknięcie kafelka spowoduje otwarcie pulpitu nawigacyjnego rozwiązania.

![Analiza DNS kafelka](./media/dns-analytics/dns-tile.png)

### <a name="solution-dashboard"></a>Pulpit nawigacyjny rozwiązania

Pulpit nawigacyjny rozwiązania przedstawia podsumowanie informacji dla różnych funkcji rozwiązania. Zawiera także łącza do szczegółowym widoku dla analizy śledczej i diagnostyki. Domyślnie dane są wyświetlane w ciągu ostatnich siedmiu dni. Można zmienić zakres dat i godzin za pomocą **formantu wyboru daty i godziny**, jak pokazano na poniższej ilustracji:

![Kontrolka wyboru czasu](./media/dns-analytics/dns-time.png)

Pulpit nawigacyjny rozwiązania przedstawia następujące bloki:

**Zabezpieczenia systemu DNS**. Raporty klientów DNS, które próbujesz nawiązać połączenia z usługą złośliwe domeny. Za pomocą źródła analizy zagrożeń firmy Microsoft, analiza DNS może wykryć adresów IP, który próbujesz uzyskać dostęp złośliwe domeny klienta. W wielu przypadkach urządzeń zainfekowanych złośliwym oprogramowaniem "" do połączenie Centrum "poleceń i kontroli" złośliwe domeny przez przetłumaczenie nazwy domeny przed złośliwym oprogramowaniem.

![Blok zabezpieczenia systemu DNS](./media/dns-analytics/dns-security-blade.png)

Po kliknięciu adres IP klienta, na liście, wyszukiwanie w dzienniku otwiera i pokazuje szczegóły wyszukiwania odpowiednich zapytania. W poniższym przykładzie analiza DNS wykryła, że komunikacja za pomocą [IRCbot](https://www.microsoft.com/en-us/wdsi/threats/malware-encyclopedia-description?Name=Backdoor:Win32/IRCbot):

![Wyświetlanie ircbot wyniki przeszukiwania dzienników](./media/dns-analytics/ircbot.png)

Informacje pomagają zidentyfikować:

- Klient IP, który zainicjował komunikacji.
- Nazwa domeny, który jest rozpoznawany jako złośliwy adres IP.
- Adresy IP, które rozpoznawany jako nazwy domeny.
- Złośliwy adres IP.
- Ważność problemu.
- Przyczyna blacklisting złośliwy adres IP.
- Czas wykrycia.

**Odpytywane domeny**. Zawiera najczęściej używane nazwy domen odpytywane przez klientów DNS w danym środowisku. Można wyświetlić listę wszystkich odpytywane nazwy domen. Możesz również przejść do szczegółów żądania wyszukiwania o nazwie określonej domeny, podczas wyszukiwania dziennika.

![Blok kwerendy domen](./media/dns-analytics/domains-queried-blade.png)

**Klienci DNS**. Raporty klientów *naruszenie progu* do liczby zapytań w wybranym okresie. Można wyświetlić listę wszystkich klientów DNS i szczegółowe informacje o zapytaniach dokonanych przez siebie podczas wyszukiwania dziennika.

![Klienci DNS bloku](./media/dns-analytics/dns-clients-blade.png)

**Rejestracje dynamicznego DNS**. Raporty nazwa błędy rejestracji. Wszystkie błędy rejestracji dla adresu [rekordy zasobów](https://en.wikipedia.org/wiki/List_of_DNS_record_types) (typ A i AAAA), zostały wyróżnione wraz z adresów IP, który zgłosił żądań rejestracji klienta. Te informacje można użyć, aby znaleźć przyczynę niepowodzenia rejestracji wykonaj następujące czynności:

1. Znajdź strefy, który jest autorytatywny dla nazwy, którego klient próbuje zaktualizować.

1. Korzystanie z odpowiedniego rozwiązania, aby sprawdzić informacje spisu tej strefy.

1. Sprawdź, czy aktualizacja dynamiczna strefy jest włączony.

1. Sprawdź, czy strefa jest skonfigurowany do zabezpieczonych aktualizacji dynamicznych, czy nie.

    ![Dynamiczne bloku rejestracje DNS](./media/dns-analytics/dynamic-dns-reg-blade.png)

**Nazwa żądania rejestracji**. Górny Kafelek pokazuje linię trendu udane i nieudane żądania dynamicznej aktualizacji DNS. Niższe fragment zawiera najważniejsze 10 klientów, które wysyłają żądania aktualizacji DNS nie powiodło się na serwerach DNS, posortowane według liczby awarii.

![Blok żądania rejestracji nazw](./media/dns-analytics/name-reg-req-blade.png)

**Przykładowe zapytania usługi Analytics DDI**. Zawiera listę najczęściej występujące zapytania wyszukiwania, które pobierają dane pierwotne analytics bezpośrednio.


![Przykładowe zapytania](./media/dns-analytics/queries.png)

Te zapytania można użyć jako punktu wyjścia do tworzenia zapytań niestandardowych raportowania. Zapytania link do strony przeszukiwania dzienników analiza DNS, w której są wyświetlane wyniki:

- **Lista serwerów DNS**. Wyświetla listę wszystkich serwerów DNS przy użyciu ich skojarzone nazwy FQDN, nazwy domeny, nazwa lasu i serwera adresów IP.
- **Lista stref DNS**. Wyświetla listę wszystkich stref DNS przy użyciu nazwy strefy skojarzony, stan aktualizacji dynamicznych, nazwy serwerów i stan podpisania DNSSEC.
- **Nieużywane rekordy zasobów**. Wyświetla listę wszystkich rekordów zasobów nieużywane/stałe. Ta lista zawiera nazwę w rekordzie zasobu, typ rekordu zasobu, skojarzonego serwera DNS, czas generowania rekordów i nazwę strefy. Ta lista służy do identyfikowania rekordów zasobów DNS, które nie są już w użyciu. Na podstawie tych informacji, możesz następnie usunąć te wpisy z serwerów DNS.
- **Obciążenie zapytaniami serwerów DNS**. Pokazuje informacje, tak aby mogli uzyskać widzenia obciążenia DNS na serwerach DNS. Te informacje mogą pomóc w planowanie pojemności dla serwerów. Możesz przejść do **metryki** kartę, aby zmienić widok na widok graficzny wizualizacji. Ten widok pomaga zrozumieć sposób dystrybucji obciążenia DNS na serwerach DNS. Zapytania DNS pokazuje trendy stawki dla każdego serwera.

    ![Wyniki wyszukiwania dziennika zapytań serwerów DNS](./media/dns-analytics/dns-servers-query-load.png)

- **Obciążenie zapytaniami stref DNS**. Pokazuje statystyki strefy zapytań na sekundę wszystkie strefy DNS na serwerach DNS zarządzanych przez rozwiązanie. Kliknij przycisk **metryki** kartę, aby zmienić widok z szczegółowe dane na graficzna wizualizacja wyników.
- **Zdarzenia konfiguracji**. Pokazuje wszystkie zdarzenia zmiany konfiguracji DNS i skojarzonych komunikatów. Następnie możesz filtrować te zdarzenia na podstawie czasu w serwera DNS, zdarzenia, identyfikator zdarzenia, lub kategorii zadanie. Dane mogą pomóc w inspekcji zmiany wprowadzone do określonych serwerów DNS w określonym czasie.
- **Dziennik analityczny DNS**. Pokazuje wszystkie zdarzenia analityczne na wszystkich serwerach DNS zarządzanych przez rozwiązanie. Następnie możesz filtrować te zdarzenia na podstawie czasu w serwera DNS, zdarzenia, identyfikator zdarzenia, adres IP klienta, który wprowadził zapytania wyszukiwania i kategorii zadań typu zapytania. Zdarzenia analityczne DNS serwerów włączyć działania śledzenia na serwerze DNS. Analityczne zdarzenie jest rejestrowane każdorazowo serwer wysyłane lub odbierane informacje dotyczące systemu DNS.

### <a name="search-by-using-dns-analytics-log-search"></a>Wyszukiwanie za pomocą wyszukiwania w dziennikach analiza DNS

Na stronie przeszukiwania dzienników można utworzyć kwerendę. Wyniki wyszukiwania można filtrować za pomocą kontrolek zestawu reguł. Można również utworzyć zaawansowanych zapytań do transformacji, filtrowania i raportów na wyniki. Rozpocznij, korzystając z następujących zapytań:

1. W **pola zapytania wyszukiwania**, typ `DnsEvents` Aby wyświetlić wszystkie zdarzenia DNS wygenerowane przez serwery DNS są zarządzane przez rozwiązanie. Wynikiem jest lista dane dziennika dla wszystkich zdarzeń związanych z zapytania dotyczące wyszukiwania, rejestracje dynamicznego i zmian w konfiguracji.

    ![Wyszukiwanie w dzienniku DnsEvents](./media/dns-analytics/log-search-dnsevents.png)  

    a. Aby wyświetlić dane dziennika dla kwerend wyszukiwania, wybierz **LookUpQuery** jako **podtypu** filtra z poziomu kontroli aspekt po lewej stronie. Zostanie wyświetlona tabela z listą wszystkich zdarzeń zapytań wyszukiwania dla wybranego okresu.

    b. Zaznacz, aby wyświetlić dane dziennika dla rejestracji dynamicznej **DynamicRegistration** jako **podtypu** filtra z poziomu kontroli aspekt po lewej stronie. Zostanie wyświetlona tabela z listą wszystkich zdarzeń dynamiczną rejestrację dla wybranego okresu.

    c. Aby wyświetlić dane dziennika zmian konfiguracji, wybierz **Zmianakonfiguracji** jako **podtypu** filtra z poziomu kontroli aspekt po lewej stronie. Zostanie wyświetlona tabela z listą wszystkich zdarzeń zmian konfiguracji dla wybranego okresu.

1. W **pola zapytania wyszukiwania**, typ `DnsInventory` do wyświetlania wszystkich DNS związane z magazynu danych w przypadku serwerów DNS zarządzanych przez rozwiązanie. Wynikiem jest lista dane dziennika dla serwerów DNS, strefy DNS i rekordów zasobów.

    ![Wyszukiwanie w dzienniku DnsInventory](./media/dns-analytics/log-search-dnsinventory.png)
    
## <a name="troubleshooting"></a>Rozwiązywanie problemów

Typowe kroki rozwiązywania problemów:

1. Brakujące dane wyszukiwania DNS — aby rozwiązać ten problem, spróbuj zresetować konfigurację lub po prostu Załaduj stronę konfiguracji raz w portalu. Na potrzeby resetowania po prostu zmień ustawienie na inną wartość, a następnie zmień je z powrotem na oryginalną wartość i Zapisz konfigurację.

## <a name="feedback"></a>Opinia

Aby przekazać opinię, odwiedź [stronę log Analytics UserVoice](https://aka.ms/dnsanalyticsuservoice) , aby opublikować pomysły dotyczące DNS Analyticsch funkcji. 

## <a name="next-steps"></a>Następne kroki

[Dzienniki zapytań](../log-query/log-query-overview.md) , aby wyświetlić szczegółowe rekordy dziennika DNS.
