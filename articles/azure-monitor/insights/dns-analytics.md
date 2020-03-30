---
title: Rozwiązanie analizy DNS w usłudze Azure Monitor | Dokumenty firmy Microsoft
description: Skonfiguruj i korzystaj z rozwiązania DNS Analytics w usłudze Azure Monitor, aby zbierać szczegółowe informacje na temat infrastruktury DNS na temat zabezpieczeń, wydajności i operacji.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2018
ms.openlocfilehash: 2471c29f559df5c347c62ceb4c7fd9b4ae1e5eec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657337"
---
# <a name="gather-insights-about-your-dns-infrastructure-with-the-dns-analytics-preview-solution"></a>Zbieranie szczegółowych informacji o infrastrukturze DNS za pomocą rozwiązania DNS Analytics Preview

![Symbol analizy DNS](./media/dns-analytics/dns-analytics-symbol.png)

W tym artykule opisano sposób konfigurowania i używania rozwiązania usługi Azure DNS Analytics w usłudze Azure Monitor w celu zebrania szczegółowych informacji na temat infrastruktury DNS dotyczących zabezpieczeń, wydajności i operacji.

Usługa DNS Analytics pomaga:

- Identyfikowanie klientów, którzy próbują rozpoznać złośliwe nazwy domen.
- Identyfikowanie starych rekordów zasobów.
- Identyfikowanie często wyszukiwanych nazw domen i rozmownych klientów DNS.
- Wyświetl obciążenie żądania na serwerach DNS.
- Wyświetlanie dynamicznych błędów rejestracji DNS.

Rozwiązanie zbiera, analizuje i koreluje dzienniki analityczne i inspekcji DNS systemu Windows oraz inne powiązane dane z serwerów DNS.

## <a name="connected-sources"></a>Połączone źródła

W poniższej tabeli opisano połączone źródła, które są obsługiwane przez to rozwiązanie:

| **Połączone źródło** | **Pomoc techniczna** | **Opis** |
| --- | --- | --- |
| [Agenci dla systemu Windows](../platform/agent-windows.md) | Tak | Rozwiązanie zbiera informacje DNS od agentów systemu Windows. |
| [Agenci dla systemu Linux](../learn/quick-collect-linux-computer.md) | Nie | Rozwiązanie nie zbiera informacji DNS od bezpośrednich agentów systemu Linux. |
| [Grupa zarządzania programu System Center Operations Manager](../platform/om-agents.md) | Tak | Rozwiązanie zbiera informacje DNS od agentów w połączonej grupie zarządzania programu Operations Manager. Bezpośrednie połączenie z agentem programu Operations Manager z usługą Azure Monitor nie jest wymagane. Dane są przekazywane z grupy zarządzania do obszaru roboczego usługi Log Analytics. |
| [Konto magazynu platformy Azure](../platform/collect-azure-metrics-logs.md) | Nie | Usługa Azure Storage nie jest używana przez rozwiązanie. |

### <a name="data-collection-details"></a>Szczegóły gromadzenia danych

Rozwiązanie zbiera dane związane z spisem dns i zdarzeniami DNS z serwerów DNS, na których jest zainstalowany agent usługi Log Analytics. Te dane są następnie przekazywane do usługi Azure Monitor i wyświetlane na pulpicie nawigacyjnym rozwiązania. Dane związane z zapasami, takie jak liczba serwerów DNS, stref i rekordów zasobów, są zbierane przez uruchomienie poleceń cmdlet programu DNS PowerShell. Dane są aktualizowane co dwa dni. Dane związane ze zdarzeniami są zbierane w czasie zbliżonym do rzeczywistego z [dzienników analitycznych i inspekcji dostarczanych](https://technet.microsoft.com/library/dn800669.aspx#enhanc) przez ulepszone rejestrowanie i diagnostykę DNS w systemie Windows Server 2012 R2.

## <a name="configuration"></a>Konfigurowanie

Aby skonfigurować rozwiązanie, użyj następujących informacji:

- Na każdym serwerze DNS, który chcesz monitorować, na każdym serwerze DNS musi znajdować się agent [systemu Windows](../platform/agent-windows.md) lub programu [Operations Manager.](../platform/om-agents.md)
- Rozwiązanie dns analytics można dodać do obszaru roboczego usługi Log Analytics z [portalu Azure Marketplace](https://aka.ms/dnsanalyticsazuremarketplace). Można również użyć procesu opisanego w [add Azure Monitor solutions from the Solutions Gallery](solutions.md).

Rozwiązanie rozpoczyna zbieranie danych bez konieczności dalszej konfiguracji. Jednak można użyć następującej konfiguracji, aby dostosować zbieranie danych.

### <a name="configure-the-solution"></a>Konfigurowanie rozwiązania

Na pulpicie nawigacyjnym rozwiązania kliknij pozycję **Konfiguracja,** aby otworzyć stronę Konfiguracja usługi DNS Analytics. Istnieją dwa typy zmian konfiguracji, które można wprowadzić:

- **Nazwy domen umieszczonych na białej liście**. Rozwiązanie nie przetwarza wszystkich zapytań odnośnych. Utrzymuje białą listę sufiksów nazw domen. Kwerendy odnośnika, które rozwiązują nazwy domen, które pasują do sufiksów nazwy domeny na tej białej liście, nie są przetwarzane przez rozwiązanie. Nie przetwarzanie nazw domen umieszczonych na białej liście pomaga zoptymalizować dane wysyłane do usługi Azure Monitor. Domyślna biała lista zawiera popularne nazwy domen publicznych, takie jak www.google.com i www.facebook.com. Pełną listę domyślną można wyświetlić, przewijając.

  Można zmodyfikować listę, aby dodać dowolny sufiks nazwy domeny, dla którego chcesz wyświetlić szczegółowe informacje o odnośniku. Możesz również usunąć wszystkie sufiksy nazwy domeny, dla których nie chcesz wyświetlać statystyk odnośniki.

- **Talkative Próg klienta**. Klienci DNS, którzy przekraczają próg liczby żądań odnośnych, są wyróżnione w bloku **Klienci DNS.** Domyślny próg wynosi 1000. Próg można edytować.

    ![Nazwy domen umieszczonych na białej liście](./media/dns-analytics/dns-config.png)

## <a name="management-packs"></a>Pakiety administracyjne

Jeśli do nawiązania połączenia z obszarem roboczym usługi Log Analytics jest używany agent monitorowania firmy Microsoft, zostanie zainstalowany następujący pakiet administracyjny:

- Pakiet analizy modułu zbierającego dane DNS firmy Microsoft (Microsoft.IntelligencePacks.Dns)

Jeśli grupa zarządzania programu Operations Manager jest połączona z obszarem roboczym usługi Log Analytics, następujące pakiety administracyjne są instalowane w programie Operations Manager po dodaniu tego rozwiązania. Nie ma wymaganej konfiguracji ani konserwacji tych pakietów administracyjnych:

- Pakiet analizy modułu zbierającego dane DNS firmy Microsoft (Microsoft.IntelligencePacks.Dns)
- Konfiguracja analizy DNS klasy microsoft system center Advisor (Microsoft.IntelligencePack.Dns.Configuration)

Aby uzyskać więcej informacji na temat aktualizowania pakietów administracyjnych rozwiązania, zobacz artykuł [Connect Operations Manager to Log Analytics](../platform/om-agents.md) (Połączenie programu Operations Manager z usługą Log Analytics).

## <a name="use-the-dns-analytics-solution"></a>Korzystanie z rozwiązania DNS Analytics

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]


Kafelek DNS zawiera liczbę serwerów DNS, na których zbierane są dane. Obejmuje również liczbę żądań złożonych przez klientów w celu rozwiązania złośliwych domen w ciągu ostatnich 24 godzin. Po kliknięciu kafelka zostanie otwarty pulpit nawigacyjny rozwiązania.

![Kafelek analizy DNS](./media/dns-analytics/dns-tile.png)

### <a name="solution-dashboard"></a>Pulpit nawigacyjny rozwiązania

Pulpit nawigacyjny rozwiązania zawiera podsumowane informacje dotyczące różnych funkcji rozwiązania. Zawiera również linki do szczegółowego widoku analizy kryminalistycznej i diagnostyki. Domyślnie dane są wyświetlane dla ostatnich siedmiu dni. Zakres daty i godziny można zmienić za pomocą **formantu wyboru data-godzina**, jak pokazano na poniższej ilustracji:

![Kontrola wyboru czasu](./media/dns-analytics/dns-time.png)

Pulpit nawigacyjny rozwiązania zawiera następujące ostrza:

**Zabezpieczenia DNS**. Raportuje klientów DNS, którzy próbują komunikować się ze złośliwymi domenami. Korzystając z źródeł danych analizy zagrożeń firmy Microsoft, usługa DNS Analytics może wykrywać adresy IP klienta, które próbują uzyskać dostęp do złośliwych domen. W wielu przypadkach zainfekowane złośliwym oprogramowaniem urządzenia "wybierają" centrum "dowodzenia i kontroli" złośliwej domeny, rozwiązując nazwę domeny złośliwego oprogramowania.

![Blok zabezpieczeń DNS](./media/dns-analytics/dns-security-blade.png)

Po kliknięciu adresu IP klienta na liście wyszukiwanie dzienników otwiera się i pokazuje szczegóły wyszukiwania odpowiedniej kwerendy. W poniższym przykładzie usługa DNS Analytics wykryła, że komunikacja została wykonana za pomocą [IRCbota:](https://www.microsoft.com/en-us/wdsi/threats/malware-encyclopedia-description?Name=Backdoor:Win32/IRCbot&threatId=2621)

![Rejestrowanie wyników wyszukiwania z ircbotem](./media/dns-analytics/ircbot.png)

Informacje te pomagają zidentyfikować:

- Adres IP klienta, który zainicjował komunikację.
- Nazwa domeny, która jest rozpoznawana na złośliwy adres IP.
- Adresy IP, których nazwa domeny jest rozpoznawana.
- Złośliwy adres IP.
- Powaga problemu.
- Powód czarnej listy złośliwego adresu IP.
- Czas wykrywania.

**Domeny, których dotyczy kwerenda**. Zawiera najczęściej wyszukiwane nazwy domen przez klientów DNS w twoim środowisku. Można wyświetlić listę wszystkich wyszukiwanych nazw domen. Można również przejść do szczegółów żądania odnośnika określonej nazwy domeny w wyszukiwaniu dziennika.

![Bloku dotyczy domeny, których dotyczy kwerenda](./media/dns-analytics/domains-queried-blade.png)

**Klienci DNS**. Zgłasza klientów *przekroczenie progu* dla liczby zapytań w wybranym okresie czasu. Można wyświetlić listę wszystkich klientów DNS i szczegóły zapytań wykonanych przez nich w wyszukiwaniu dzienników.

![Blok Klienci DNS](./media/dns-analytics/dns-clients-blade.png)

**Dynamiczne rejestracje DNS**. Raporty błędów rejestracji nazw. Wszystkie błędy rejestracji dla [rekordów zasobów adresów](https://en.wikipedia.org/wiki/List_of_DNS_record_types) (typu A i AAAA) są wyróżnione wraz z adresami IP klienta, który złożył żądania rejestracji. Następnie można użyć tych informacji, aby znaleźć główną przyczynę niepowodzenia rejestracji, wykonując następujące kroki:

1. Znajdź strefę, która jest autorytatywna dla nazwy, którą klient próbuje zaktualizować.

1. Użyj rozwiązania, aby sprawdzić informacje o zapasach tej strefy.

1. Sprawdź, czy aktualizacja dynamiczna strefy jest włączona.

1. Sprawdź, czy strefa jest skonfigurowana do bezpiecznej aktualizacji dynamicznej, czy nie.

    ![Blok Dynamiczne rejestracje DNS](./media/dns-analytics/dynamic-dns-reg-blade.png)

**Prośby o rejestrację nazw**. Górny kafelek pokazuje linię trendu pomyślnych i nieudanych żądań aktualizacji dynamicznej DNS. Dolny kafelek zawiera listę 10 najlepszych klientów, którzy wysyłają nieudane żądania aktualizacji DNS do serwerów DNS, posortowane według liczby niepowodzeń.

![Ostrze żądań rejestracji nazw](./media/dns-analytics/name-reg-req-blade.png)

**Przykładowe zapytania analizy DDI**. Zawiera listę najczęstszych zapytań wyszukiwania, które pobierają dane analizy surowej bezpośrednio.


![Przykładowe zapytania](./media/dns-analytics/queries.png)

Można użyć tych zapytań jako punkt wyjścia do tworzenia własnych zapytań do niestandardowych raportowania. Kwerendy łączą się ze stroną Wyszukiwania dzienników usługi DNS Analytics, na której są wyświetlane wyniki:

- **Lista serwerów DNS**. Pokazuje listę wszystkich serwerów DNS z skojarzoną nazwą FQDN, nazwą domeny, nazwą lasu i adresami IP serwera.
- **Lista stref DNS**. Pokazuje listę wszystkich stref DNS o skojarzonej nazwie strefy, stanie aktualizacji dynamicznej, serwerach nazw i stanie podpisywania DNSSEC.
- **Nieużywane rekordy zasobów**. Pokazuje listę wszystkich nieużywanych/starych rekordów zasobów. Ta lista zawiera nazwę rekordu zasobu, typ rekordu zasobu, skojarzony serwer DNS, czas generowania rekordów i nazwę strefy. Za pomocą tej listy można zidentyfikować rekordy zasobów DNS, które nie są już używane. Na podstawie tych informacji można następnie usunąć te wpisy z serwerów DNS.
- **Ładowanie kwerend serwerów DNS**. Pokazuje informacje, dzięki czemu można uzyskać perspektywę obciążenia DNS na serwerach DNS. Te informacje mogą pomóc w zaplanować pojemność serwerów. Możesz przejść do karty **Metryki,** aby zmienić widok na wizualizację graficzną. Ten widok pomaga zrozumieć, jak obciążenie DNS jest rozmieszczane na serwerach DNS. Pokazuje trendy szybkości zapytań DNS dla każdego serwera.

    ![Wyniki wyszukiwania dziennika zapytań serwerów DNS](./media/dns-analytics/dns-servers-query-load.png)

- **Załadowanie kwerendy stref DNS**. Pokazuje statystyki dns zone-query na sekundę wszystkich stref na serwerach DNS zarządzanych przez rozwiązanie. Kliknij kartę **Metryki,** aby zmienić widok ze szczegółowych rekordów na graficzną wizualizację wyników.
- **Zdarzenia konfiguracji**. Pokazuje wszystkie zdarzenia zmiany konfiguracji DNS i skojarzone komunikaty. Następnie można filtrować te zdarzenia na podstawie czasu zdarzenia, identyfikatora zdarzenia, serwera DNS lub kategorii zadań. Dane mogą pomóc w inspekcji zmian wprowadzonych na określonych serwerach DNS w określonych godzinach.
- **Dziennik analityczny DNS**. Pokazuje wszystkie zdarzenia analityczne na wszystkich serwerach DNS zarządzanych przez rozwiązanie. Następnie można filtrować te zdarzenia na podstawie czasu zdarzenia, identyfikatora zdarzenia, serwera DNS, adresu IP klienta, który stworzył kwerendę odnośnika, oraz kategorii zadań typu kwerendy. Zdarzenia analityczne serwera DNS umożliwiają śledzenie aktywności na serwerze DNS. Zdarzenie analityczne jest rejestrowane za każdym razem, gdy serwer wysyła lub odbiera informacje DNS.

### <a name="search-by-using-dns-analytics-log-search"></a>Wyszukiwanie przy użyciu wyszukiwania dzienników usługi DNS Analytics

Na stronie Wyszukiwanie dzienników można utworzyć kwerendę. Wyniki wyszukiwania można filtrować za pomocą kontrolek aspektu. Można również tworzyć zaawansowane zapytania do przekształcania, filtrowania i raportowania wyników. Zacznij od następujących zapytań:

1. W **polu kwerendy** `DnsEvents` wyszukiwania wpisz, aby wyświetlić wszystkie zdarzenia DNS generowane przez serwery DNS zarządzane przez rozwiązanie. Wyniki wyświetlają listę danych dziennika dla wszystkich zdarzeń związanych z zapytaniami odnośników, rejestracjami dynamicznymi i zmianami konfiguracji.

    ![Wyszukiwanie dziennika DnsEvents](./media/dns-analytics/log-search-dnsevents.png)  

    a. Aby wyświetlić dane dziennika dla kwerend odnośnych, wybierz **opcję SzukajQuery** jako filtr **Podtyp** z formantu aspektu po lewej stronie. Zostanie wyświetlona tabela zawierająca listę wszystkich zdarzeń kwerendy odnośnika dla wybranego okresu.

    b. Aby wyświetlić dane dziennika dla rejestracji dynamicznych, wybierz **DynamicRegistration** jako filtr **Podtyp** z formantu aspektu po lewej stronie. Zostanie wyświetlona tabela zawierająca listę wszystkich zdarzeń rejestracji dynamicznej dla wybranego okresu.

    d. Aby wyświetlić dane dziennika dla zmian konfiguracji, wybierz **ConfigurationChange** jako filtr **Podtyp** z formantu aspektu po lewej stronie. Zostanie wyświetlona tabela zawierająca listę wszystkich zdarzeń zmiany konfiguracji dla wybranego okresu.

1. W **polu kwerendy** `DnsInventory` wyszukiwania wpisz, aby wyświetlić wszystkie dane związane z zapasami DNS dla serwerów DNS zarządzanych przez rozwiązanie. Wyniki wyświetlają listę danych dziennika dla serwerów DNS, stref DNS i rekordów zasobów.

    ![Wyszukiwanie dziennika DnsInventory](./media/dns-analytics/log-search-dnsinventory.png)
    
## <a name="troubleshooting"></a>Rozwiązywanie problemów

Typowe kroki rozwiązywania problemów:

1. Brak danych wyszukiwania DNS — aby rozwiązać ten problem, spróbuj zresetować konfigurację lub po prostu ładować stronę konfiguracji raz w portalu. W celu zresetowania wystarczy zmienić ustawienie na inną wartość, a następnie zmienić go z powrotem na oryginalną wartość i zapisać konfigurację.

## <a name="feedback"></a>Opinia

Aby przekazać opinię, odwiedź [stronę UserVoice usługi Log Analytics,](https://aka.ms/dnsanalyticsuservoice) aby opublikować pomysły dotyczące funkcji usługi DNS Analytics, nad którymi mają pracować. 

## <a name="next-steps"></a>Następne kroki

[Dzienniki kwerendy](../log-query/log-query-overview.md) w celu wyświetlenia szczegółowych rekordów dziennika DNS.
