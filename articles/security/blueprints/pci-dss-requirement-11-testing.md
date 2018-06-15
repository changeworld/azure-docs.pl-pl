---
title: Azure plan przetwarzania płatności — wymagania dotyczące testowania
description: Wymaganie PCI DSS 11
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 9753853b-ad80-4be4-9416-2583b8fd2029
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 21cb854ba35adcf7576874e3c6c7149fbd33d998
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
ms.locfileid: "33894986"
---
# <a name="testing-requirements-for-pci-dss-compliant-environments"></a>Wymagania dotyczące środowiska standardem PCI DSS 
## <a name="pci-dss-requirement-11"></a>Wymaganie PCI DSS 11

**Regularnie testować zabezpieczeń systemy i procesy**

> [!NOTE]
> Te wymagania są definiowane przez [Radę standardów zabezpieczeń Payment Card Industry (PCI)](https://www.pcisecuritystandards.org/pci_security/) jako część [PCI danych zabezpieczeń Standard (DSS) w wersji 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Zapoznaj się PCI DSS, aby uzyskać informacje na temat testowania wskazówki dla każdego wymagania i procedury.

Luki w zabezpieczeniach są stale odnajdywane przez złośliwych użytkowników i pracowników naukowo-badawczych i wprowadzana przez nowego oprogramowania. Składniki systemowe, procesów i oprogramowanie niestandardowe powinny być testowane często do zapewnienia, że odzwierciedlić zmiany środowiska zabezpieczeń.

## <a name="pci-dss-requirement-111"></a>Wymaganie PCI DSS 11.1

**11.1** procesów wdrażania przetestować obecność punkty dostępu bezprzewodowego (802.11) i wykrywa i identyfikuje wszystkie autoryzowane i nieautoryzowane punkty dostępu bezprzewodowego, co kwartał.

> [!NOTE]
> Metody, które mogą być używane w procesie obejmują, ale nie są ograniczone do sieci bezprzewodowej sieci skanowania, logiczne/fizyczne inspekcji składników systemu i infrastruktury, kontroli dostępu do sieci (NAC) lub sieci bezprzewodowej Identyfikatory/adresów IP.
Niezależnie od metody są używane, muszą być wystarczające wykrywa i identyfikuje zarówno autoryzowanych, jak i nieautoryzowanego urządzenia.


**Obowiązki:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Azure nie zezwala na lub połączeń bezprzewodowych w środowisku sieci platformy Azure. Zespoły zabezpieczenia wewnętrzne regularnie skanuje w poszukiwaniu nieautoryzowanych sygnały sieci bezprzewodowej, co kwartał i sygnały nieautoryzowanego się zbadana i usunięte. Klienci nie są dozwolone wdrażania technologii bezprzewodowej w środowisku platformy Azure. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | W rozwiązaniu nie zostały zaimplementowane sieci bezprzewodowej i SNMP.|



### <a name="pci-dss-requirement-1111"></a>Wymaganie PCI DSS 11.1.1

**11.1.1** Prowadź rejestr punkty dostępu bezprzewodowego autoryzowanych w tym udokumentowane biznesowego wyjaśnienia.

**Obowiązki:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Zobacz sekcję "Microsoft Azure" dla [11.1 wymaganie](#pci-dss-requirement-11-1). |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | W rozwiązaniu nie zostały zaimplementowane sieci bezprzewodowej i SNMP.|



### <a name="pci-dss-requirement-1112"></a>Wymaganie PCI DSS 11.1.2

**11.1.2** wykonania procedur odpowiedzi na zdarzenia w przypadku wykrycia punktów nieautoryzowanego dostępu bezprzewodowego.


**Obowiązki:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Zobacz sekcję "Microsoft Azure" dla [11.1 wymaganie](#pci-dss-requirement-11-1). |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | W rozwiązaniu nie zostały zaimplementowane sieci bezprzewodowej i SNMP.|



## <a name="pci-dss-requirement-112"></a>Wymaganie PCI DSS 11.2

**11.2** luki w zabezpieczeniach sieci zewnętrznej skanowania co najmniej raz na kwartał i po znaczących zmian w sieci (na przykład nowe instalacje składników systemu, zmiany w topologii sieci, modyfikacje reguły zapory, produktu i uruchom wewnętrzny uaktualnia). 

> [!NOTE]
> Wiele raportów skanowania można łączyć na wskazują, że były skanowane wszystkie systemy i zostały rozwiązane wszystkie luki w zabezpieczeniach dotyczy procesu skanowania co kwartał. Dodatkową dokumentację może być konieczna Sprawdź, czy-skorygowane luk w zabezpieczeniach są właśnie adresowane.
> Dla początkowego PCI DSS zgodności, nie jest wymagane, że cztery kwartały przekazywanie skanowania jest możliwe, jeśli doświadczeniem sprawdza 1) skanowania przekazywanie został najnowszych wyników skanowania, 2) obiekt został udokumentowany zasady i procedury wymagające skanowania co kwartał i 3) jak pokazano w re-scan(s) luk w zabezpieczeniach zauważyć w wynikach skanowania zostały skorygowane. W kolejnych latach po początkowej PCI DSS przejrzeć, cztery kwartały przekazywanie skanowania musi wystąpiły.


**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Azure wykonuje skanowanie co kwartał luki w zabezpieczeniach wewnętrznych i zewnętrznych. Skanowania są wykonywane przez wykwalifikowanych pracowników. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso został przetestowany pióra i skanowania luki w zabezpieczeniach w ramach działań zmierzających "jak jest". Wyniki testu pióra można duplikować, za pomocą wspólnych narzędzi, takich jak nmap lub pentest tools.com. Wyniki testu pióra zapewni niejednoznaczny ataku z kończona elementów. Ponadto [Centrum zabezpieczeń Azure](https://azure.microsoft.com/services/security-center/) i [Azure Advisor](/azure/advisor/advisor-security-recommendations) udostępniają informacje luki w zabezpieczeniach i korygowania.|



### <a name="pci-dss-requirement-1121"></a>Wymaganie PCI DSS 11.2.1

**11.2.1** skanowania co kwartał wewnętrzny luki w zabezpieczeniach. Adres luk w zabezpieczeniach i wykonaj ponownie skanuje, aby sprawdzić, czy wszystkie luki w zabezpieczeniach "wysokiego ryzyka" zostały rozwiązane zgodnie z luk w zabezpieczeniach jednostki klasyfikacji (na wymaganie 6.1). Musi zostać przeskanowane za wykwalifikowanych pracowników.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure wykonuje skanowanie w poszukiwaniu luk w infrastrukturze podstawowej-scope. Microsoft Azure wykonuje skanowanie na systemy operacyjne serwera baz danych i urządzeń sieciowych przy użyciu narzędzi do skanowania właściwe usterki luki w zabezpieczeniach. Aplikacje sieci web platformy Azure są skanowane z branży odpowiednie skanowania rozwiązania. Luki w zabezpieczeniach przeskanowane co kwartał.<br /><br />Skanuje są wykonywane zgodnie z potrzebami względem wszystkich systemów, do momentu rozwiązania wszystkich "wysokiego ryzyka" luk w zabezpieczeniach (określone w wymaganie 6.1). |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso został przetestowany pióra i skanowania luki w zabezpieczeniach w ramach działań zmierzających "jak jest". Wyniki testu pióra można duplikować, za pomocą wspólnych narzędzi, takich jak nmap lub pentest tools.com. Wyniki testu pióra zapewni niejednoznaczny ataku z kończona elementów. Ponadto [Centrum zabezpieczeń Azure](https://azure.microsoft.com/services/security-center/) i [Azure Advisor](/azure/advisor/advisor-security-recommendations) udostępniają informacje luki w zabezpieczeniach i korygowania.|



### <a name="pci-dss-requirement-1122"></a>Wymaganie PCI DSS 11.2.2

**11.2.2** skanowania co kwartał luki w zabezpieczeniach zewnętrznych, za pośrednictwem zatwierdzone skanowanie dostawcy (ASV) zatwierdzone przez płatności karty branżowych standardów Radę Bezpieczeństwa (PCI SSC). Wykonaj ponownie skanuje zgodnie z potrzebami, dopóki nie zostaną osiągnięte przekazywanie skanowania. 

> [!NOTE]
> Co kwartał luki w zabezpieczeniach zewnętrznego musi zostać przeskanowane za zatwierdzone skanowanie dostawcy (ASV), zatwierdzone przez płatności karty branżowych standardów Radę Bezpieczeństwa (PCI SSC).
> Można znaleźć w podręczniku programu ASV publikowane w witrynie internetowej PCI SSC skanowania obowiązki klienta, przygotowywania skanowania itd.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure wykonuje skanowanie zewnętrznych luk w infrastrukturze podstawowej-scope, który jest dostępny zewnętrznie. Skanowania są wykonywane przez zatwierdzone skanowania dostawcy (ASV).<br /><br />Microsoft Azure subskrybuje MSRC/OSSC comiesięcznych powiadomień poprawki i skanowanie w poszukiwaniu luk w zabezpieczeniach przynajmniej raz na kwartał. Luki w zabezpieczeniach zidentyfikowanych są oceniane i skorygowanym na osi ustalonych na podstawie poziomu ryzyka.<br /><br />Aby zidentyfikować luki w zabezpieczeniach odbywa się każdego kwartału docelowe kompleksowe bezpieczeństwo skanowane priorytetową składniki środowiska Microsoft Azure. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Podczas wdrażania Webstore firmy Contoso, klienci pokaz są zobowiązani do wykonywania co kwartał luki w zabezpieczeniach zewnętrznych skanowania i skanuje zgodnie z potrzebami względem wszystkich wystąpień PaaS w swoim środowisku danych posiadacza (CRP) przy użyciu zatwierdzonych skanowanie dostawcy (ASV) zatwierdzone przez Radę standardów zabezpieczeń branży karty płatności.<br /><br />|



### <a name="pci-dss-requirement-1123"></a>Wymaganie PCI DSS 11.2.3

**11.2.3** wykonywanie skanowania wewnętrznych i zewnętrznych i skanuje następnie, po znaczących zmian. Musi zostać przeskanowane za wykwalifikowanych pracowników.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Wyniki są zgłaszane osobom zainteresowanym i korygowania jest śledzony przez zespół zabezpieczeń Azure za pośrednictwem zamknięcia. Wyniki testu Azure mogą być udostępniane klientom pod rygorem umowy NDA. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do przeprowadzania skanowania co kwartał luki w zabezpieczeniach wewnętrznych i zewnętrznych i skanuje zgodnie z potrzebami względem wszystkich wystąpień PaaS w ich CRP. Powinny zostać przeskanowane po wprowadzeniu znaczących zmian w środowisku w zakresie.<br /><br />Musi zostać przeskanowane za ASV lub personel w sposób niezależny organizacji.|



## <a name="pci-dss-requirement-113"></a>Wymaganie PCI DSS 11.3

**11.3** zaimplementować metodologia testowania penetracji który obejmuje następujące elementy:
- Jest oparta na przyjęte w branży penetracji testowania metod (na przykład NIST SP800-115)
- Obejmuje pokrycia dla całego obwodowej CRP i krytyczne systemy
- Obejmuje testowania z oraz spoza sieci
- Zawiera testy w celu zweryfikowania wszelkie segmentacji i formantów w zakresie redukcji
- Definiuje testy penetracji warstwy aplikacji do uwzględnienia w co najmniej na liście 6.5 wymaganie luk w zabezpieczeniach
- Definiuje testy penetracji warstwy sieci, aby uwzględnić składniki obsługujące funkcje sieci, a także systemów operacyjnych
- Zawiera przegląd i rozważenia zagrożeń i luk w zabezpieczeniach wystąpił w ciągu ostatnich 12 miesięcy
- Określa przechowywania penetracji testowania wyniki i wyniki działań korygujących

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure weryfikuje usług przy użyciu innej penetracji testowania ustalane na podstawie OWASP (Otwórz projekt aplikacji sieci Web zabezpieczeń) 10 przy użyciu certyfikowane SZCZYTU testerów najważniejszych. Wyniki testów są śledzone za pomocą rejestru ryzyka, który jest sprawdzana i przejrzeć regularnie w celu zapewnienia zgodności do rozwiązania w zakresie zabezpieczeń. <br /><br />Firma Microsoft również korzysta z zespołu kart interfejsu sieciowego Red względem infrastruktury zarządzany przez firmę Microsoft, usług i aplikacji. Brak danych odbiorcy końcowego celowo podlega podczas czerwony zespołu kart interfejsu sieciowego i testowania penetracji działającą witrynę. Testy są sprzeczne z infrastruktury Microsoft Azure i platformy, a także aplikacji firmy Microsoft i danych. Nigdy nie są stosowane dzierżaw klienta, aplikacji i danych hostowana na platformie Azure.<br /><br />Microsoft Azure ma zastosować niezależne doświadczeniem aby opracować plan oceny systemu i przeprowadzenia oceny kontrolki. Formanty oceny są wykonywane co roku i wyniki przekazywane są odpowiednie osoby. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso został przetestowany pióra i skanowania luki w zabezpieczeniach w ramach działań zmierzających "jak jest". Wyniki testu pióra można duplikować, za pomocą wspólnych narzędzi, takich jak nmap lub pentest tools.com. Wyniki testu pióra zapewni niejednoznaczny ataku z kończona elementów. Ponadto [Centrum zabezpieczeń Azure](https://azure.microsoft.com/services/security-center/) i [Azure Advisor](/azure/advisor/advisor-security-recommendations) udostępniają informacje luki w zabezpieczeniach i korygowania.|



### <a name="pci-dss-requirement-1131"></a>Wymaganie PCI DSS 11.3.1

**11.3.1** wykonaj *zewnętrznych* penetracji testowania co najmniej raz i po żadnych znaczących infrastruktury lub uaktualniania aplikacji lub modyfikacji (takie jak uaktualnienia systemu operacyjnego, podrzędne sieci dodane do środowisko, lub serwer sieci web dodanych do środowiska).

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Zobacz sekcję "Microsoft Azure" dla [11.3 wymaganie](#pci-dss-requirement-11-3). |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso został przetestowany pióra i skanowania luki w zabezpieczeniach w ramach działań zmierzających "jak jest". Wyniki testu pióra można duplikować, za pomocą wspólnych narzędzi, takich jak nmap lub pentest tools.com. Wyniki testu pióra zapewni niejednoznaczny ataku z kończona elementów. Ponadto [Centrum zabezpieczeń Azure](https://azure.microsoft.com/services/security-center/) i [Azure Advisor](/azure/advisor/advisor-security-recommendations) udostępniają informacje luki w zabezpieczeniach i korygowania.|



### <a name="pci-dss-requirement-1132"></a>Wymaganie PCI DSS 11.3.2

**11.3.2** wykonaj *wewnętrzny* penetracji testowania co najmniej raz i po żadnych znaczących infrastruktury lub uaktualniania aplikacji lub modyfikacji (takie jak uaktualnienia systemu operacyjnego, podrzędne sieci dodane do środowisko, lub serwer sieci web dodanych do środowiska).

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Umowy Microsoft Azure z niezależnych ekspertów do testowania penetracji granic Microsoft Azure. Ćwiczenia zespołu czerwony również regularnie są wykonywane i wyników użyć w celu dokonania poprawek zabezpieczeń. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso został przetestowany pióra i skanowania luki w zabezpieczeniach w ramach działań zmierzających "jak jest". Wyniki testu pióra można duplikować, za pomocą wspólnych narzędzi, takich jak nmap lub pentest tools.com. Wyniki testu pióra zapewni niejednoznaczny ataku z kończona elementów. Ponadto [Centrum zabezpieczeń Azure](https://azure.microsoft.com/services/security-center/) i [Azure Advisor](/azure/advisor/advisor-security-recommendations) udostępniają informacje luki w zabezpieczeniach i korygowania.|



### <a name="pci-dss-requirement-1133"></a>Wymaganie PCI DSS 11.3.3

**11.3.3** kończona luk w zabezpieczeniach znaleziono podczas testowania penetracji są usuwane i testowania jest powtarzany, aby zweryfikować poprawki.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Procedury zostały utworzone w celu monitorowania składników platformy Microsoft Azure dla znane luki w zabezpieczeniach. <br /><br /><br /><br />Aby zidentyfikować luki w zabezpieczeniach odbywa się każdego kwartału docelowe kompleksowe bezpieczeństwo skanowane priorytetową składniki w środowisku Azure środowiska produkcyjnego. Wyniki są zgłaszane osobom zainteresowanym i korygowania jest śledzony przez zespół za pośrednictwem zamknięcia. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | [Centrum zabezpieczeń Azure](https://azure.microsoft.com/services/security-center/) i [Azure Advisor](/azure/advisor/advisor-security-recommendations), które dostarczają informacji luki w zabezpieczeniach i korygowania, zostały już użyte do zapewnienia, że wszystkie nierozwiązane problemy zostały skorygowana pod kątem pokaz Contoso magazynu sieci Web CRP.|



### <a name="pci-dss-requirement-1134"></a>Wymaganie PCI DSS 11.3.4

**11.3.4** Jeśli segmentacji jest używane do izolowania CRP z innymi sieciami, wykonywania testów penetracji co najmniej raz i po wszelkich zmianach segmentacji formanty/metody Sprawdź, czy metod segmentacji operacyjne i skuteczne, i odizoluj wszystkie systemy poza zakresem od systemów w CDE.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Procedury zostały utworzone w celu monitorowania składników platformy Microsoft Azure dla znane luki w zabezpieczeniach. <br /><br /><br /><br />Aby zidentyfikować luki w zabezpieczeniach odbywa się każdego kwartału docelowe kompleksowe bezpieczeństwo skanowane priorytetową składniki w środowisku Azure środowiska produkcyjnego. Wyniki są zgłaszane osobom zainteresowanym i korygowania jest śledzony przez zespół za pośrednictwem zamknięcia. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | [Centrum zabezpieczeń Azure](https://azure.microsoft.com/services/security-center/) i [Azure Advisor](/azure/advisor/advisor-security-recommendations), które dostarczają informacji luki w zabezpieczeniach i korygowania, zostały już użyte do zapewnienia, że wszystkie nierozwiązane problemy zostały skorygowana pod kątem pokaz Contoso magazynu sieci Web CRP.|



### <a name="pci-dss-requirement-11341"></a>Wymaganie PCI DSS 11.3.4.1

**11.3.4.1** *dodatkowych wymogów dotyczących tylko dostawców usług:* użycie segmentacji potwierdzić zakres PCI DSS, wykonując penetracji testowania na segmentacji kontroluje sześciu co najmniej raz, miesiące i po wszelkich zmianach Formanty segmentacji/metod.

> [!NOTE]
> To wymaganie jest najlepszym rozwiązaniem do 31 stycznia 2018, po którym staje się on wymagania.


**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Zobacz sekcję "Microsoft Azure" dla [wymaganie 11.3.4](#pci-dss-requirement-11-3-4). |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | [Centrum zabezpieczeń Azure](https://azure.microsoft.com/services/security-center/) i [Azure Advisor](/azure/advisor/advisor-security-recommendations), które dostarczają informacji luki w zabezpieczeniach i korygowania, zostały już użyte do zapewnienia, że wszystkie nierozwiązane problemy zostały skorygowana pod kątem pokaz Contoso magazynu sieci Web CRP.|



## <a name="pci-dss-requirement-114"></a>Wymaganie PCI DSS 11,4

**11,4** wykrycia i/lub uniemożliwia dostęp do sieci przy użyciu technik wykrywania nieautoryzowanego dostępu i/lub zapobiegania nieautoryzowanego dostępu. Cały ruch na obwodowej posiadacza środowiska danych, a także w punktach krytycznych w środowisku posiadacza danych monitorowania i alertów pracownikom kompromisów podejrzanych.
Aktualizuj wszystkie wykrywania nieautoryzowanego dostępu i zapobiegania aparatów, linii bazowych i podpisów.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure przeprowadza analizę w czasie rzeczywistym zdarzeń w jego środowisku operacyjnym i Identyfikatory systemy generują niemal w czasie rzeczywistym alertów dotyczących zdarzeń, które potencjalnie może naruszyć bezpieczeństwo systemu. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso jest usługą PaaS i nieautoryzowanego dostępu i zapobiegania sieci można znaleźć odpowiedzialność platformy Azure. [Centrum zabezpieczeń Azure](https://azure.microsoft.com/services/security-center/) i [Azure Advisor](/azure/advisor/advisor-security-recommendations) alerty nieautoryzowanego dostępu i korygowania.|



## <a name="pci-dss-requirement-115"></a>Wymaganie PCI DSS 11.5

**11.5** wdrażanie mechanizm wykrywania zmian (na przykład integralność pliku narzędzi do monitorowania) do personelu alertu nieuprawnione modyfikacje (w tym zmian, dodatkami i usunięć) krytycznych plików systemowych, plików konfiguracji i zawartości pliki; i skonfigurować oprogramowanie do wykonania porównania krytycznych plików co najmniej raz na tydzień. 

> [!NOTE]
> Na potrzeby wykrywania zmian krytycznych plików są zwykle osób, które nie należy zmieniać regularnie, ale może to wskazywać na modyfikację naruszenia systemu lub ryzyko ujawnienia. Mechanizmów wykrywania zmian, takich jak produkty monitorowania integralność pliku zazwyczaj mają wstępnie skonfigurowane krytycznych plików powiązanych systemu operacyjnego. Innych krytycznych plików, takich jak te dotyczące niestandardowych aplikacji, należy obliczyć i zdefiniowanych przez podmiot (oznacza to, że dostawca handlowe lub service).

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure obsługuje i powiadamia klientów o potencjalnych zmiany i zdarzeń, które mogą mieć wpływ na zabezpieczenia lub dostępności usług za pośrednictwem online pulpit nawigacyjny usługi. Zmiany zabezpieczeń zobowiązań i obowiązków zabezpieczeń Microsoft Azure klienci są aktualizowane w witrynie sieci Web Microsoft Azure w odpowiednim czasie.<br /><br />Instalacja lub zmian w oprogramowaniu Microsoft Azure jest ograniczony do środowiska produkcyjnego autoryzowane personelu administracji i następuje zmiana procedury zarządzania. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Wersja demonstracyjna dla magazynu sieci Web firmy Contoso to usługa PaaS i wykrywania zmian została zaimplementowana przy użyciu analizy dzienników. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - rozwiązania do zarządzania preinstalowany](payment-processing-blueprint.md#management-solutions).<br /><br />|



### <a name="pci-dss-requirement-1151"></a>Wymaganie PCI DSS 11.5.1

**11.5.1** należy wdrożyć proces odpowiadanie na alerty generowane przez rozwiązanie wykrywania zmian.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Azure reguły zdarzeń monitorowania zapewniają zwiększony poziom monitorowania dla operacji wysokiego ryzyka i zasoby. Urządzenia sieciowe zarządzane Azure są monitorowane pod kątem zgodności ze standardami ustalonych zabezpieczeń. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Alerty magazynu sieci Web firmy Contoso zmiany są dostarczonej przez implementację analizy dzienników. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - rozwiązania do zarządzania preinstalowany](payment-processing-blueprint.md#management-solutions).<br /><br /><br /><br />|



## <a name="pci-dss-requirement-116"></a>Wymaganie PCI DSS 11.6

**11.6** upewnij się, że zasady zabezpieczeń i procedur zabezpieczeń, monitorowania i testowania są udokumentowane w użyciu i znanych wszystkich zainteresowanych podmiotów.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Alerty magazynu sieci Web firmy Contoso zmiany są dostarczonej przez implementację analizy dzienników. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - rozwiązania do zarządzania preinstalowany](payment-processing-blueprint.md#management-solutions).<br /><br /><br /><br />|




