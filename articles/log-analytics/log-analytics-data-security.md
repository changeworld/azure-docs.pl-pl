---
title: Rejestrować dane Analytics | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu analizy dzienników prywatności i zabezpiecza dane.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: a33bb05d-b310-4f2c-8f76-f627e600c8e7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: magoedte
ms.openlocfilehash: 8558b3b1f5a9036f1134ddce3302211b41f57c05
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37025913"
---
# <a name="log-analytics-data-security"></a>Rejestrować analizy danych
Ten dokument jest przeznaczony do dostarczania informacji Azure Log Analytics załączyć na [Centrum zaufania Azure](../security/security-microsoft-trust-center.md).  

W tym artykule opisano, jak dane są zbierane, przetwarzane i zabezpieczane przez usługi Analiza dzienników. Korzystania z agentów, aby połączyć się z usługą sieci web, użyj programu System Center Operations Manager do zbierania danych operacyjnych lub pobrać danych diagnostycznych platformy Azure na potrzeby używania przez analizy dzienników. 

Usługi analizy dzienników bezpiecznie zarządza danych oparte na chmurze za pomocą następujących metod:

* Podział danych
* Przechowywanie danych
* Zabezpieczenia fizyczne
* Zarządzanie zdarzeniami
* Zgodność
* Certyfikaty standardów zabezpieczeń

Skontaktuj się z nami w przypadku jakichkolwiek pytań, sugestie lub problemy związane z dowolnej z poniższych informacji, w tym nasze zasady zabezpieczeń w [opcje pomocy technicznej platformy Azure](http://azure.microsoft.com/support/options/).

## <a name="data-segregation"></a>Podział danych
Po danych jest pozyskanych przez usługę Analiza dzienników, dane są przechowywane logicznie oddzielnie dla każdego składnika w całej usługi. Wszystkie dane, jest oznaczone na obszar roboczy. To tagowanie jest obecne przez cały cykl życia danych i jest wymuszane w każdej warstwie usługi. Dane są przechowywane w bazie danych dedykowanego w klastrze magazynu w regionie, który wybrano.

## <a name="data-retention"></a>Przechowywanie danych
Indeksowane dziennik wyszukiwania danych są przechowywane i przechowywane zgodnie z planem cenową. Aby uzyskać więcej informacji, zobacz [cennik analizy dziennika](https://azure.microsoft.com/pricing/details/log-analytics/).

W ramach Twojej [umowy subskrypcyjnej dotyczącej usług](https://azure.microsoft.com/support/legal/subscription-agreement/), firma Microsoft będzie przechowywać dane zgodnie z postanowieniami Umowy.  Dane zostaną usunięte, będziemy również usunięcie konta magazynu Azure, w którym znajdują się dane.  Dane klienta zostanie usunięty, nie ma dysków fizycznych zostaną zniszczone.  

Poniższa tabela przedstawia niektóre z dostępnych rozwiązań oraz przykłady typu danych, które pobierają.

| **Rozwiązanie** | **Typy danych** |
| --- | --- |
| Pojemność i wydajność |Dane dotyczące wydajności i metadane |
| Ocena oprogramowania chroniącego przed złośliwym kodem |Dane konfiguracji i metadane |
| Zarządzanie aktualizacjami |Metadane i stan danych. |
| Zarządzanie dziennikami |Zdefiniowane przez użytkownika dzienniki zdarzeń, dzienniki zdarzeń systemu Windows i/lub dzienniki programu IIS |
| Śledzenie zmian |Spisu oprogramowania, usługa systemu Windows i Linux demon metadanych i metadanych pliku systemu Windows i Linux |
| SQL i oceny usługi Active Directory |Wyniki danych usługi WMI, dane rejestru dane dotyczące wydajności i dynamicznego zarządzania programu SQL Server |

W poniższej tabeli przedstawiono przykłady typów danych:

| **Typ danych** | **Pola** |
| --- | --- |
| Alerty |Alert nazwa, opis alertu, identyfikatorze BaseManagedEntityId, identyfikator problemu, IsMonitorAlert, RuleId, ResolutionState, priorytet, ważność, kategoria, właściciel, ResolvedBy, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved, RepeatCount TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, |
| Konfigurowanie |CustomerID, identyfikator agenta, identyfikator jednostki, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate |
| Wydarzenie |EventId, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, Number, Category, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded <br>**Uwaga:** podczas zapisywania zdarzenia za pomocą pola niestandardowe w dzienniku zdarzeń systemu Windows OMS zbiera je. |
| Metadane |BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Address, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime |
| Wydajność |ObjectName, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| Stan |StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>Zabezpieczenia fizyczne
Usługi analizy dzienników jest zarządzana przez personel firmy Microsoft i wszystkich działań są rejestrowane i może być sprawdzona. Analiza dzienników działa jako usługa Azure i spełnia wszystkie wymagania Azure zgodności i zabezpieczeń. Możliwość wyświetlania szczegółów dotyczących zabezpieczeń fizycznych zasobów platformy Azure na stronie 18 [Przegląd zabezpieczeń systemu Microsoft Azure](http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf). Prawa fizyczny dostęp do bezpiecznego obszarów są zmieniane w ciągu jednego dnia roboczego dla każdego, kto nie ma już odpowiedzialność za usługę, w tym transfer i kończenie działania. Informacje o globalnej infrastruktury fizycznej używamy w [Datacenters Microsoft](https://azure.microsoft.com/en-us/global-infrastructure/).

## <a name="incident-management"></a>Zarządzanie zdarzeniami
OMS ma procesu zarządzania zdarzeniami, które wszystkich usług firmy Microsoft jest zgodna. Podsumowując, firma Microsoft:

* Korzystają z modelu udostępnionego odpowiedzialność, gdzie część odpowiedzialność zabezpieczeń należy do firmy Microsoft i części należy do klienta
* Zarządzanie zdarzeniami zabezpieczeń platformy Azure:
  * Uruchom postępowania w przypadku wykrycia zdarzenia
  * Oceń wpływ i ważność incydentu przez członków zespołu na wywołanie odpowiedzi na zdarzenia. Oparte na dowód, oceny może lub nie może powodować dalszych eskalacji do zespołu odpowiedź zabezpieczeń.
  * Diagnozowanie zdarzenia przez ekspertów odpowiedź zabezpieczeń do przeprowadzenia badania techniczne lub śledczej, określania strategii zawierania, ograniczenia i obejście. Jeśli zespół zabezpieczeń uznaje się, że dane klienta może stać się widoczne dla bezprawnego lub nieautoryzowane osoby, równolegle rozpocznie się wykonywanie równoległe procesu powiadomienie o zdarzeniu klienta.  
  * Ustabilizowania i odzyskać incydentu. Odpowiedzi na zdarzenia zespołu tworzy plan odzyskiwania, aby ograniczyć ten problem. Kryzysami zawierania kroków, takich jak poddawania wpływ na systemy mogą wystąpić natychmiast i równolegle z diagnostyki. Dłuższy okres środki zaradcze mogą być planowane występujące po upływie bezpośrednie ryzyko.  
  * Zamknięcie incydentu i przeprowadzanie których post. Odpowiedzi na zdarzenia zespołu tworzy zwłok, który zawiera szczegółowe informacje o zdarzeniu, o zamiar Popraw zasady, procedury i procesy w celu zapobiegania ponownemu zdarzenia.
* Powiadamia klientów o przypadki naruszenia zabezpieczeń:
  * Określ zakres wpływ na klientów i zapewnienie każdy, kto jest w pełni funkcjonalne tak szczegółowe powiadomienia, jak to możliwe
  * Utwórz powiadomienie, aby zapewnić klientom szczegółowe informacje o za mało, aby mogli wykonywać dochodzenia ich end i spełniać żadnych zobowiązań, wprowadzone do użytkownikom końcowym podczas nie opóźnienia proces powiadamiania.
  * Potwierdź i zadeklarować zdarzenia, w razie potrzeby.
  * Powiadamia klientów o zdarzenia powiadomienia niezwłocznie nieuzasadnione i zgodnie z żadnych zobowiązań prawnych lub umownych. Powiadomienia zdarzenia zabezpieczeń są dostarczane do jednej lub kilku administratorów klienta w jakikolwiek sposób, który wybiera firmy Microsoft, w tym za pośrednictwem poczty e-mail.
* Należy przeprowadzić gotowości zespołu i szkolenia:
  * Personel firmy Microsoft są wymagane do ukończenia zabezpieczeń i szkolenia świadomości, co ułatwia ich do identyfikacji i zgłaszać problemy z zabezpieczeniami podejrzane.  
  * Operatorzy pracy z usługą Microsoft Azure mają zobowiązań szkolenia dodanie otaczającego ich dostęp do ważnych systemów hosting danych klienta.
  * Specjalne szkolenia dotyczące ich ról odbierania personel odpowiedź zabezpieczeń firmy Microsoft

Gdy dojdzie do utraty danych klienta, powiadomimy każdego klienta w ciągu jednego dnia. Jednak klient wystąpiła utrata danych nigdy nie w usłudze. 

Aby uzyskać więcej informacji dotyczących sposobu Microsoft reaguje na przypadki naruszenia zabezpieczeń, zobacz [Response zabezpieczeń firmy Microsoft Azure w chmurze](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/4/Microsoft%20Azure%20Security%20Response%20in%20the%20cloud.pdf).

## <a name="compliance"></a>Zgodność
Program zabezpieczeń i nadzór nad informacji analizy dzienników oprogramowania rozwoju i usługi zespołu obsługuje jej wymagania biznesowe i stosuje przepisom eksportowym obowiązującym zgodnie z opisem w [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/) i [ Centrum zaufania Microsoft zgodności](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx). Jak analizy dzienników ustanawia wymagania dotyczące zabezpieczeń, identyfikuje kontroli zabezpieczeń zarządza i monitoruje ryzyka są także opisane istnieje. Co rok, możemy przeglądu zasady, normy, procedury i wskazówek.

Każdego członka zespołu programowanie odbiera szkolenia formalnego aplikacji w zakresie zabezpieczeń. Wewnętrznie używamy system kontroli wersji dla rozwoju oprogramowania. Każdy projekt oprogramowania jest chroniona przez system kontroli wersji.

Firma Microsoft ma zabezpieczeń i zgodności zespołu nadzoruje i ocenia wszystkich usług firmy Microsoft. Zabezpieczenia informatyków uzupełnić zespołu i nie są one powiązane z działów engineering, które rozwija analizy dzienników. Biuro zabezpieczeń ma swoje własne łańcuch zarządzania i przeprowadzanie niezależnych ocen produktów i usług w celu zapewnienia bezpieczeństwa i zgodności.

Zarząd firmy Microsoft jest powiadamiany o coroczny raport o wszystkie programy zabezpieczeń informacji w firmie Microsoft.

Zespół rozwoju i usługi oprogramowania analizy dzienników aktywnie współpracuje z zespołami Legal firmy Microsoft i zgodności i innymi partnerami z branży można uzyskać różne certyfikaty.

## <a name="certifications-and-attestations"></a>Certyfikaty i poświadczenia
Analiza dzienników Azure spełnia następujące wymagania:

* [ISO/IEC 27001](http://www.iso.org/iso/home/standards/management-standards/iso27001.htm)
* [ISO/IEC 27018:2014](http://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498)
* [ISO 22301](https://azure.microsoft.com/blog/iso22301/)
* [Standard zabezpieczeń danych Industry (PCI zgodne) karta płatności (PCI DSS)](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI) przez Radę standardów PCI zabezpieczeń.
* [Typ usługi organizacji formantów (SOC) 1 1 i SOC 2 typu 1](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2) zgodne
* [HIPAA i HITECH](https://www.microsoft.com/en-us/TrustCenter/Compliance/hipaa) dla firm, które ma umowy skojarzyć HIPAA biznesowe
* Wspólne kryteria Engineering systemu Windows
* Wiarygodne technologie komputerowe firmy Microsoft (witryna może być w języku angielskim)
* Jako usługi Azure składniki używane przez usługi Analiza dzienników spełniać wymagania zgodności platformy Azure. Więcej w [zgodności Centrum zaufania Microsoft](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx).

> [!NOTE]
> W niektórych certyfikaty/poświadczenia, analizy dzienników jest wyświetlana w obszarze jego poprzednią nazwę *usługi Operational Insights*.
>
>

## <a name="cloud-computing-security-data-flow"></a>Chmura obliczeniowa zabezpieczeń przepływu danych
Na poniższym diagramie przedstawiono architekturę zabezpieczeń chmury jako przepływ informacji w firmie i jak jest zabezpieczony, ponieważ jest przenoszony z usługą analizy dzienników ostatecznie odebrane przez użytkownika w portalu Azure lub klasycznego portalu OMS. Więcej informacji na temat każdego kroku następuje diagramu.

![Obraz kolekcji danych analizy dzienników i zabezpieczeń](./media/log-analytics-data-security/log-analytics-data-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. Zarejestruj się, aby zbieranie danych i analizy dzienników
Dla Twojej organizacji w celu wysyłania danych do analizy dzienników można skonfigurować agenta systemu Windows lub Linux uruchomionych na maszynach wirtualnych platformy Azure lub na komputerach wirtualnych lub fizycznych w Twoim środowisku lub innych dostawcy chmury.  Jeśli używasz programu Operations Manager z grupy zarządzania można skonfigurować agenta programu Operations Manager. Użytkownicy, (które mogą być, inne poszczególnych użytkowników lub grupy osób) utworzenie co najmniej jeden analizy dzienników obszarów roboczych i zarejestrować agentów przy użyciu jednej z następujących kont:

* [Identyfikator organizacji](../active-directory/fundamentals/sign-up-organization.md)
* [Konto Microsoft - Outlook pakietu Office na żywo, MSN](https://account.microsoft.com/account)

Obszar roboczy analizy dzienników jest gdzie dane są zbierane, zagregowane, poddane analizie i przedstawiony. Obszar roboczy służy głównie w celu danych partycji i każdego obszaru roboczego jest unikatowa. Na przykład możesz chcieć zarządzanych za pomocą jednego obszaru roboczego danych produkcyjnych i badanie danych zarządzanych za pomocą innego obszaru roboczego. Obszary robocze również pomóc administratora kontroli dostępu użytkownika do danych. Każdy obszar roboczy może mieć wiele kont użytkowników skojarzonych z nim, a wszystkie konta użytkowników mogą uzyskiwać dostęp do wielu obszarów roboczych analizy dzienników. Możesz utworzyć obszarów roboczych na podstawie regionu centrum danych. Każdego obszaru roboczego są replikowane do innych centrów danych w regionie, przede wszystkim dotyczące dostępności usług analizy dzienników.

Dla programu Operations Manager grupy zarządzania programu Operations Manager, ustanawia połączenie z usługą analizy dzienników. Następnie skonfiguruj, które systemy zarządzane z wykorzystaniem agentów w grupie zarządzania mogą wysyłają i zbierają dane z usługą. W zależności od rozwiązania zostało włączone, dane z tych rozwiązań są albo wysyłane bezpośrednio z serwera zarządzania programu Operations Manager z usługą analizy dzienników lub ze względu na ilość danych zebranych przez system zarządzane z wykorzystaniem agentów są wysyłane bezpośrednio z agent z usługą. W systemach, które nie są monitorowane przez program Operations Manager każda łączy bezpiecznie z usługą analizy dzienników bezpośrednio.

Cała komunikacja między połączonych systemów i usługą analizy dzienników jest zaszyfrowany.  Protokół TLS (HTTPS) jest używany do szyfrowania.  Proces SDL firmy Microsoft jest zachowana w celu upewnij się, że analizy dzienników jest aktualny i najnowsze osiągnięcia w protokołów kryptograficznych.

Każdy typ agenta zbiera dane analizy dziennika. Typ danych zbieranych jest zależy od typów rozwiązań używane. Wyświetlane podsumowanie zbierania danych w [rozwiązań dodać analizy dzienników z galerii rozwiązań](log-analytics-add-solutions.md). Ponadto bardziej szczegółowe informacje o kolekcji jest dostępna w przypadku większości rozwiązań. Rozwiązanie to pakiet wstępnie zdefiniowanych widoków, zapytania wyszukiwania dziennika zasady zbierania danych i przetwarzania logiki. Tylko administratorzy mogą używać analizy dzienników do importowania rozwiązania. Po zaimportowaniu rozwiązania jest przenoszony do serwerów zarządzania programu Operations Manager (jeśli jest używany), a następnie do wszystkich wybranych agentów. W efekcie agentów zbierania danych.

## <a name="2-send-data-from-agents"></a>2. Wysyłanie danych z agentów
Zarejestruj wszystkie typy agenta przy użyciu klucza rejestracji i jest ustanowić bezpiecznego połączenia między agentem i usługą analizy dzienników przy użyciu uwierzytelniania opartego na certyfikatach oraz protokołu SSL z portem 443. Analiza dzienników używa tajnego magazynu do generowania i Obsługa kluczy. Klucze prywatne są obracane co 90 dni i są przechowywane na platformie Azure i są zarządzane przez operacje platformy Azure, którzy postępuj zgodnie z ograniczeniami praktyk przepisami i zgodności.

Z programem Operations Manager grupy zarządzania w zarejestrowany z obszaru roboczego analizy dzienników ustanawia bezpiecznego połączenia HTTPS z serwerem zarządzania programu Operations Manager.

Dla systemu Windows lub Linux agentów uruchomionych na maszynach wirtualnych platformy Azure klucz magazynu tylko do odczytu jest używany do odczytu zdarzeń diagnostycznych w tabelach platformy Azure.  

Z dowolnej agenta raportowania do grupy zarządzania programu Operations Manager, który jest zintegrowany z analizy dzienników Jeśli serwer zarządzania nie może nawiązać połączenia z usługi z jakiejkolwiek przyczyny, zebranych danych jest przechowywany lokalnie w tymczasowej pamięci podręcznej na zarządzanie serwer.   Próby wysłania danych co osiem minut przez 2 godziny.  W przypadku danych pomija serwera zarządzania, który jest wysyłany bezpośrednio do analizy dzienników zachowanie jest zgodne z agenta systemu Windows.  

Systemu Windows lub dane w pamięci podręcznej agenta serwer zarządzania jest chroniona przez system operacyjny magazynu poświadczeń. Jeśli usługa nie może przetworzyć danych po dwóch godzinach, agentów może umieścić w kolejce danych. Jeśli zapełnienia kolejki agenta uruchamia porzucenie typy danych, począwszy od danych dotyczących wydajności. Limit kolejki agenta jest klucz rejestru, można je zmodyfikować, w razie potrzeby. Zebrane dane jest skompresowany i wysyłane do usługi, pomijanie bazy danych grupy zarządzania programu Operations Manager, więc nie dodaje żadnych obciążenia do nich. Po wysłaniu zebranych danych, zostanie ono usunięte z pamięci podręcznej.

Zgodnie z powyższym opisem dane z serwera zarządzania lub podłączony bezpośrednio agenci są wysyłane za pośrednictwem protokołu SSL do centrów danych Microsoft Azure. Opcjonalnie można używać usługi ExpressRoute, aby zapewnić dodatkową ochronę danych. ExpressRoute to sposób bezpośredniego połączenia z platformą Azure od istniejącej sieci WAN, takich jak wiele protokołów etykietę przełączania sieci VPN (MPLS), pochodzącymi z dostawcą usługi sieciowej. Aby uzyskać więcej informacji, zobacz [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. Usługi analizy dzienników odbiera i przetwarza dane
Usługi analizy dzienników zapewnia, że dane przychodzące jest z zaufanego źródła, sprawdzając poprawność certyfikatów i integralności danych za pomocą uwierzytelniania systemu Azure. Nieprzetworzone dane pierwotne następnie znajduje się w Centrum zdarzeń Azure w regionie, który ostatecznie dane będą przechowywane w stanie spoczynku. Typ danych przechowywanych zależy od typów rozwiązań, które zostały zaimportowane i używane do zbierania danych. Następnie analizy dzienników usługi procesów nieprzetworzone dane i wysyła strumień go do bazy danych.

Okres przechowywania zbieranych danych przechowywanych w bazie danych zależy od wybranego planu cenowego. Aby uzyskać *wolne* warstwa, zebranych danych jest dostępna przez 7 dni. Aby uzyskać *zapłacone* warstwy, zebranych danych jest dostępna przez 31 dni domyślnie, ale może zostać rozszerzony do 720 dni. Dane są przechowywane szyfrowane, gdy w magazynie Azure, aby zapewnić poufności danych. Dane z ostatnich dwóch tygodni są także przechowywane w pamięci podręcznej z na dyskach SSD i ta pamięć podręczna jest aktualnie niezaszyfrowane.  Firma Microsoft planuje obsługuje takie szyfrowania w późniejszym połowie 2018.  

## <a name="4-use-log-analytics-to-access-the-data"></a>4. Umożliwia dostęp do danych analizy dzienników
Aby uzyskać dostęp do obszaru roboczego analizy dzienników, należy zalogować się do portalu Azure za pomocą konta organizacyjnego lub konta Microsoft, które należy wcześniej skonfigurować. Cały ruch między portalem i usługą analizy dzienników są wysyłane za pośrednictwem bezpiecznego kanału HTTPS. Korzystając z portalu, identyfikator sesji jest generowany na kliencie użytkownika (przeglądarki sieci web), a dane są przechowywane w lokalnej pamięci podręcznej, dopóki sesja zostanie zakończona. Zakończone, pamięci podręcznej są usuwane. Pliki cookie po stronie klienta, które nie zawierają informacji umożliwiających identyfikację użytkownika, nie są automatycznie usuwane. Pliki cookie dotyczące sesji są oznaczane HTTPOnly i są zabezpieczone. Po upływie wstępnie określoną bezczynności sesji portalu Azure, zostanie zakończony.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak zbierać dane z analizy dzienników dla sieci maszyn wirtualnych platformy Azure następujące [Szybki Start Azure VM](log-analytics-quick-collect-azurevm.md).  

*  Jeśli szukasz zbierania danych z komputerów fizycznych i wirtualnych systemu Windows lub Linux w danym środowisku, zobacz [szybkiego startu dla komputerów z systemem Linux](log-analytics-quick-collect-linux-computer.md) lub [komputerów szybkiego startu dla systemu Windows](log-analytics-quick-collect-windows-computer.md)

