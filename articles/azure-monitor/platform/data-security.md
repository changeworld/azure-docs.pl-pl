---
title: Bezpieczeństwo danych analizy dziennika | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o ochronę prywatności i zabezpiecza dane usługi Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/04/2019
ms.openlocfilehash: 0ac169060f7ba0e58aeb3e36e3af1629b6453fc1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79274868"
---
# <a name="log-analytics-data-security"></a>Bezpieczeństwo danych analizy dziennika
Ten dokument ma na celu dostarczenie informacji specyficznych dla Log Analytics, które są funkcją Azure Monitor, aby uzupełnić informacje o [Centrum zaufania Azure](../../security/fundamentals/trust-center.md).  

W tym artykule wyjaśniono sposób zbierania, przetwarzania i zabezpieczania danych przez usługę Log Analytics. Korzystania z agentów, aby nawiązać połączenie z usługą sieci web, użyć programu System Center Operations Manager do zbierania danych operacyjnych lub pobierać dane z usługi Diagnostyka Azure do użycia przez usługę Log Analytics. 

Usługa Log Analytics bezpiecznie zarządza dane oparte na chmurze przy użyciu następujących metod:

* Podział danych
* Przechowywanie danych
* Zabezpieczenia fizyczne
* Zarządzanie zdarzeniami
* Zgodność
* Certyfikaty standardów zabezpieczeń

Skontaktuj się z nami, podając wszelkie pytania, sugestie lub problemy dotyczące dowolnych z poniższych informacji, w tym naszych zasad zabezpieczeń w [opcjach pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/).

## <a name="sending-data-securely-using-tls-12"></a>Wysyłanie danych przy użyciu protokołu TLS 1.2 

Na ułatwieniu zapewnienia bezpieczeństwa danych przesyłanych do usługi Log Analytics, zdecydowanie zachęcamy do konfigurowania agenta do użycia w co najmniej zabezpieczeń TLS (Transport Layer) 1.2. Starsza wersja protokołu TLS/SSL (SSL) została uznana za narażoną, a mimo to nadal pracują w celu zapewnienia zgodności z poprzednimi wersjami, nie jest to **zalecane**, a branża szybko przenosi się do porzucenia, aby uzyskać pomoc techniczną dla tych starszych protokołów. 

[Rada normy zabezpieczeń PCI](https://www.pcisecuritystandards.org/) ustawił [termin 30 czerwca 2018,](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) aby wyłączyć starsze wersje protokołu TLS/SSL i uaktualnić je do bezpieczniejsze protokoły. Gdy starsze obsługuje jest już w Azure, jeśli Twoi agenci nie mogą komunikować się za pośrednictwem co najmniej protokołu TLS 1.2, nie będzie mogła wysyłać dane do usługi Log Analytics. 

Firma Microsoft nie zaleca się jawne ustawienie agenta można używać tylko protokołu TLS 1.2, chyba że absolutnie konieczne, ponieważ może przerwać funkcji zabezpieczenia na poziomie platformy, które pozwalają na automatyczne wykrywanie i korzystać z nowsze protokoły bezpieczniejsze, gdy tylko staną się dostępne, na przykład jako TLS 1.3. 

### <a name="platform-specific-guidance"></a>Ze wskazówek specyficznych dla platformy

|Języka/platformy | Pomoc techniczna | Więcej informacji |
| --- | --- | --- |
|Linux | Dystrybucje systemu Linux zależą od [OpenSSL](https://www.openssl.org) obsługi TLS 1,2.  | Sprawdź [Dziennik zmian OpenSSL](https://www.openssl.org/news/changelog.html) , aby potwierdzić, że wersja OpenSSL jest obsługiwana.|
| Windows 8.0 10 | Obsługiwane i domyślnie włączona. | , Aby upewnić się, że nadal używasz [ustawień domyślnych](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings).  |
| System Windows Server 2012 2016 | Obsługiwane i domyślnie włączona. | Aby potwierdzić, że nadal używasz [ustawień domyślnych](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) |
| Windows 7 z dodatkiem SP1 i Windows Server 2008 R2 z dodatkiem SP1 | Obsługiwane, ale nie jest włączony domyślnie. | Aby uzyskać szczegółowe informacje na temat włączania, zobacz stronę [Ustawienia rejestru Transport Layer Security (TLS)](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) .  |

## <a name="data-segregation"></a>Podział danych
Po Twoje dane są pozyskiwane przez usługę Log Analytics, dane są logicznie oddzielone dla każdego składnika w całej usłudze. Wszystkie dane są otagowane obszaru roboczego. To tagowanie jest obecne przez cały cykl życia danych i jest wymuszane w każdej warstwie usługi. Dane są przechowywane w dedykowany bazy danych w klastrze magazynu w regionie, który wybrano.

## <a name="data-retention"></a>Przechowywanie danych
Indeksowane dane wyszukiwania dziennika są przechowywane i przechowywane zgodnie z planem cennika. Aby uzyskać więcej informacji, zobacz [Cennik usługi log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

W ramach [umowy subskrypcyjnej](https://azure.microsoft.com/support/legal/subscription-agreement/)firma Microsoft zachowa Twoje dane zgodnie z warunkami umowy.  Po usunięciu danych klienta nie dysków fizycznych są niszczone.  

W poniższej tabeli przedstawiono niektóre z dostępnych rozwiązań i zawiera przykłady typu danych, które pobierają.

| **Rozwiązanie** | **Typy danych** |
| --- | --- |
| Pojemność i wydajność |Dane dotyczące wydajności i metadane |
| Zarządzanie aktualizacjami |Metadane i dane dotyczące stanu |
| Zarządzanie dziennikami |Zdefiniowane przez użytkownika dzienniki zdarzeń, dzienniki zdarzeń Windows i/lub dzienniki programu IIS |
| Śledzenie zmian |Spis oprogramowania, usługi Windows i metadanych demonów systemu Linux i metadanych systemu Windows/Linux |
| Program SQL oraz oceny usługi Active Directory |Dane usługi WMI, dane rejestru, dane dotyczące wydajności i dynamicznego zarządzania programu SQL Server należy wyświetlić wyniki |

W poniższej tabeli przedstawiono przykłady typów danych:

| **Typ danych** | **Pola** |
| --- | --- |
| Alerty |Zgłoś alert, nazwa, opis alertu, BaseManagedEntityId, identyfikator problemu, IsMonitorAlert, RuleId, stanu rozpoznania, priorytetu, ważności, kategorii, właściciel, ResolvedBy, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved, RepeatCount TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, |
| Konfiguracja |CustomerID, identyfikator agenta, EntityID, ManagedTypeID ManagedTypePropertyID, CurrentValue, ChangeDate |
| Wydarzenie |EventId, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, Number, Category, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded <br>**Uwaga:** Gdy zapisujesz zdarzenia z polami niestandardowymi w dzienniku zdarzeń systemu Windows, Log Analytics je gromadzić. |
| Metadane |BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Address, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime |
| Wydajność |ObjectName, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| Stan |StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>Zabezpieczenia fizyczne
Usługa Log Analytics jest zarządzana przez personel firmy Microsoft, a wszystkie działania są rejestrowane i podlega inspekcji. Usługa log Analytics jest świadczona jako usługa platformy Azure i spełnia wszystkie wymagania dotyczące zgodności platformy Azure i zabezpieczeń. Szczegółowe informacje o zabezpieczeniach fizycznych zasobów platformy Azure można wyświetlić na stronie 18 [przeglądu zabezpieczeń Microsoft Azure](https://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf). Prawa fizyczny dostęp do bezpiecznego obszary są zmieniane w ciągu jednego dnia, dla każdego, kto nie ma już odpowiedzialność za usługi Log Analytics, w tym transfer i kończenie działania. Możesz zapoznać się z globalną infrastrukturą fizyczną używaną w [centrach danych firmy Microsoft](https://azure.microsoft.com/global-infrastructure/).

## <a name="incident-management"></a>Zarządzanie zdarzeniami
Usługa log Analytics ma procesu zarządzania zdarzeniami, które przestrzegać wszystkich usług firmy Microsoft. Aby podsumować, firma Microsoft:

* Korzystają z modelu odpowiedzialności, gdzie części odpowiedzialność zabezpieczeń należy do firmy Microsoft i fragment należy do klienta
* Zarządzanie zdarzeniami zabezpieczeń platformy Azure:
  * Rozpocznij badanie po wykryciu zdarzenia
  * Ocena wpływu i ważność zdarzenia przez członka zespołu reagowania na wywołanie. Na podstawie dowodów, ocena może lub nie może powodować dalsze podwyższenia poziomu zabezpieczeń zespół reagowania.
  * Diagnozowanie zdarzenie zabezpieczeń odpowiedzi ekspertów, Przeprowadź badanie techniczne lub śledczej, określ zawierania, ograniczania ryzyka oraz możliwych obejść. Jeśli zespół ds. zabezpieczeń uważa, że dane klienta może być narażony na osobę lub, równoległe wykonywanie procesu powiadomienie o zdarzeniu klienta rozpocznie się równolegle.  
  * Stabilizowaniu i odzyskać incydentu. Zespół reagowania na zdarzenia tworzy plan odzyskiwania, aby rozwiązać ten problem. Czynności zawierania kryzysu, takie jak poddawanie kwarantannie, których to dotyczy systemów może wystąpić, natychmiast i równolegle z diagnostyki. Dłuższy okres środków zaradczych może być planowane występujące po upływie bezpośrednie ryzyko.  
  * Zamknięcie incydentu i przeprowadzanie późniejszej analizy. Zespół reagowania na zdarzenia tworzy późniejszej, zawierający szczegóły zdarzenia, z zamiarem Popraw zasady, procedury i procesy, aby uniknąć ponownego wystąpienia zdarzenia.
* Powiadom klientów związane z bezpieczeństwem:
  * Określanie zakresu klientów, których to dotyczy i w celu zapewnienia każdy, kto ma wpływ na tak szczegółowe powiadomienia, jak to możliwe
  * Utwórz powiadomienie, aby zapewnić klientom szczegółowych wystarczającą ilość informacji, aby mogły one przeprowadzania szczegółowej analizy, po ich stronie i spełnia wszelkie zobowiązania, wprowadzone do jego usług użytkownicy końcowi podczas nadmiernie nie opóźniania proces powiadamiania.
  * Potwierdź i zadeklarować zdarzenia, zgodnie z potrzebami.
  * Powiadom klientów o zdarzenia powiadomień niezwłocznie nieuzasadnione i zgodnie z żadnych zobowiązań prawnych lub umownych. Powiadomienia związane z bezpieczeństwem są dostarczane do co najmniej jeden z administratorów klienta za pomocą jakichkolwiek środków, które wybierze firmy Microsoft, w tym za pośrednictwem poczty e-mail.
* Należy przeprowadzić gotowości zespołu i szkoleniowe:
  * Personel firmy Microsoft są wymagane w celu ukończenia zabezpieczeń i szkolenia świadomości, co ułatwia ich identyfikacji i zgłosić problemy z zabezpieczeniami potencjalnie złośliwych programów.  
  * Operatory pracy w usłudze Microsoft Azure mają obowiązki szkolenia dodanie otaczającego ich dostęp do ważnych systemów, hostowanie danych klienta.
  * Personel odpowiedzi zabezpieczeń firmy Microsoft otrzymywać specjalistyczne szkolenie dla ich ról

W przypadku utraty żadnych danych klientów powiadomimy każdego klienta w ciągu jednego dnia. Jednak klient nastąpiła utrata danych nigdy nie z usługą. 

Aby uzyskać więcej informacji o tym, jak firma Microsoft reaguje na zdarzenia związane z zabezpieczeniami, zobacz [Microsoft Azure Security Response w chmurze](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/4/Microsoft%20Azure%20Security%20Response%20in%20the%20cloud.pdf).

## <a name="compliance"></a>Zgodność
Program Log Analytics tworzenia oprogramowania i ochrony informacji zespołu usług wspiera jego wymagania biznesowe i stosuje przepisy zgodnie z opisem w artykule [Microsoft Azure Centrum zaufania](https://azure.microsoft.com/support/trust-center/) i [zgodność Centrum zaufania firmy Microsoft](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx). Jak usługa Log Analytics ustanawia wymagań dotyczących zabezpieczeń, identyfikuje środki kontroli bezpieczeństwa, zarządza i monitoruje zagrożenia są także opisane istnieje. Co roku, firma Microsoft Przejrzyj zasady, normy, procedury i wytyczne.

Każdy członek zespołu rozwoju odbiera szkolenia formalnego aplikacji w zakresie zabezpieczeń. Wewnętrznie system kontroli wersji służy do tworzenia oprogramowania. Każdy projekt oprogramowania jest chroniona przez system kontroli wersji.

Firma Microsoft ma zespołu zabezpieczeń i zgodności, nadzoruje, która ocenia wszystkich usług firmy Microsoft. Specjaliści ds. bezpieczeństwa informacji tworzą zespół i nie są oni skojarzeni z zespołami inżynierów opracowuje usługi Log Analytics. Specjaliści ds. bezpieczeństwa mają własne łańcuch zarządzania i jednostkach niezależnych produktów i usług w celu zapewnienia bezpieczeństwa i zgodności.

Zarząd firmy Microsoft jest powiadamiany przez roczny raport o wszystkich programów bezpieczeństwa informacji w firmie Microsoft.

Opracowywania oprogramowania w usłudze Log Analytics i zespołu usług trwają intensywne prace ze zespołów Legal firmy Microsoft i zgodności i innych partnerów z branży można uzyskać różne certyfikaty.

## <a name="certifications-and-attestations"></a>Certyfikatami i aprobatami
Usługa Azure Log Analytics spełnia następujące wymagania:

* [ISO/IEC 27001](https://www.iso.org/iso/home/standards/management-standards/iso27001.htm)
* [ISO/IEC 27018:2014](https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498)
* [ISO 22301](https://azure.microsoft.com/blog/iso22301/)
* [Standardowa karta płatnicza (zgodna z normą PCI) Data Security Standard (PCI DSS)](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI) przez Radę standardu zabezpieczeń PCI.
* [Kontrolki organizacji usług (SOC) 1 typ 1 i SOC 2 typu 1](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2)
* [HIPAA i HITECH](https://www.microsoft.com/en-us/TrustCenter/Compliance/hipaa) dla firm, które mają umowę HIPAA Business Partner Agreement
* Typowe kryteria Engineering Windows
* Wiarygodne technologie komputerowe firmy Microsoft (witryna może być w języku angielskim)
* Jako usługa platformy Azure składników, które korzysta z usługi Log Analytics spełnić wymagania dotyczące zgodności platformy Azure. Więcej informacji można znaleźć w artykule [zgodność Centrum zaufania firmy Microsoft](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx).

> [!NOTE]
> W niektórych certyfikatach/zaświadczeniu Log Analytics wymieniony poniżej jego imię *Operational Insights*.
>
>

## <a name="cloud-computing-security-data-flow"></a>Chmura obliczeniowa przepływ danych zabezpieczeń
Na poniższym diagramie przedstawiono architekturę zabezpieczeń chmury jako przepływ informacji z Twojej firmy i jak jest zabezpieczony, ponieważ jest przenoszony do usługi Log Analytics, ostatecznie widoczne przez użytkownika w witrynie Azure portal. Więcej informacji na temat każdego kroku następuje diagramu.

![Obraz kolekcji danych usługi Log Analytics i zabezpieczeń](./media/data-security/log-analytics-data-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. Zarejestruj się, aby uzyskać Log Analytics i zebrać dane
Dla Twojej organizacji w celu wysyłania danych do usługi Log Analytics można skonfigurować agenta Windows lub Linux uruchomiony na maszynach wirtualnych platformy Azure lub na komputerach wirtualnych lub fizycznych, w środowisku lub innych dostawców chmury.  Jeśli używasz programu Operations Manager, z grupy zarządzania można skonfigurować agenta programu Operations Manager. Użytkownicy, (które mogą być, inne poszczególnych użytkowników lub grupy osób) utworzyć jeden lub więcej obszarów roboczych usługi Log Analytics, a następnie zarejestrować agentów przy użyciu jednej z następujących kont:

* [Identyfikator organizacji](../../active-directory/fundamentals/sign-up-organization.md)
* [Konto Microsoft — Outlook, Office Live, MSN](https://account.microsoft.com/account)

Obszar roboczy usługi Log Analytics to, gdzie dane są zbierane, agregowane, analizowane i przedstawiane. Obszar roboczy jest używany głównie jako środek do partycjonowania danych, a każdy obszar roboczy jest unikatowy. Na przykład możesz chcieć mieć danych produkcyjnych zarządzanych za pomocą jednego obszaru roboczego i dane zarządzanych za pomocą innego obszaru roboczego. Obszary robocze również pomóc administratora kontroli dostępu użytkownika do danych. Każdy obszar roboczy może mieć wiele kont użytkowników skojarzonych z nim, a wszystkie konta użytkowników mogą uzyskiwać dostęp do wielu obszarów roboczych usługi Log Analytics. Możesz utworzyć obszary robocze oparte na region centrum danych.

Dla programu Operations Manager grupy zarządzania programu Operations Manager ustanawia połączenie z usługą Log Analytics. Następnie należy skonfigurować systemów zarządzanych przez agentów w grupie zarządzania mogą wysyłają i zbierają dane do usługi. W zależności od rozwiązania zostało włączone, dane z tych rozwiązań są albo wysyłane bezpośrednio z serwera zarządzania programu Operations Manager do usługi Log Analytics lub ze względu na ilość danych zebranych przez system zarządzany przez agenta są wysyłane bezpośrednio z agent z usługą. W systemach, które nie są monitorowane przez program Operations Manager każdy bezpiecznie łączy się z usługi Log Analytics bezpośrednio.

Cała komunikacja między połączone systemy i usługi Log Analytics jest szyfrowana. Protokół TLS (HTTPS) jest używany do szyfrowania.  Proces SDL firmy Microsoft jest zakończony, aby upewnić się, że usługi Log Analytics jest aktualny i najnowsze osiągnięcia w protokołów kryptograficznych.

Każdy rodzaj agenta służy do zbierania danych usługi Log Analytics. Typ danych, które są zbierane to zależy od typów rozwiązań używane. Możesz zobaczyć podsumowanie zbierania danych w [obszarze dodawanie log Analytics rozwiązań z Galeria rozwiązań](../../azure-monitor/insights/solutions.md). Ponadto bardziej szczegółowe informacje o kolekcji jest dostępna w przypadku większości rozwiązań. Rozwiązanie to pakiet wstępnie zdefiniowanych widoków, zapytań funkcji przeszukiwania dzienników, reguł zbierania danych i logiki przetwarzania. Tylko administratorzy mogą używać usługi Log Analytics, aby zaimportować rozwiązania. Po zaimportowaniu rozwiązania jest przenoszony do serwerów zarządzania programu Operations Manager (jeśli jest używany), a następnie do agentów, które zostały wybrane. W efekcie agentów zbierania danych.

## <a name="2-send-data-from-agents"></a>2. Wyślij dane z agentów
Zarejestruj wszystkie typy agenta przy użyciu klucza rejestracji i ustanowieniu bezpiecznego połączenia między agentem i usługę Log Analytics przy użyciu uwierzytelniania opartego na certyfikatach oraz protokołu SSL przy użyciu portu 443. Usługi log Analytics używa magazynu wpisów tajnych, aby wygenerować i Obsługa kluczy. Klucze prywatne są obracane co 90 dni i są przechowywane na platformie Azure i są zarządzane przy użyciu operacji platformy Azure, którzy postępuj zgodnie z ograniczeniami rozwiązania w zakresie przepisów i zgodności.

Za pomocą programu Operations Manager grupy zarządzania zarejestrowane przy użyciu obszaru roboczego usługi Log Analytics ustanawia bezpiecznego połączenia HTTPS z serwerem zarządzania programu Operations Manager.

Dla Windows lub Linux agentów na maszynach wirtualnych platformy Azure klucza magazynu tylko do odczytu jest używany do odczytu zdarzeń diagnostycznych w tabelach platformy Azure.  

Za pomocą dowolnego agentów raportujących do grupy zarządzania programu Operations Manager, który jest zintegrowany z usługą Log Analytics Jeśli serwer zarządzania nie może komunikować się z usługi z jakiegokolwiek powodu, zebrane dane są przechowywane lokalnie w tymczasowej pamięci podręcznej na zarządzanie serwer.   Użytkownik próbuje ponownie wysłać dane co ośmiu minut przez dwie godziny.  Dla danych, które pomija serwera zarządzania i są wysyłane bezpośrednio do usługi Log Analytics zachowanie jest zgodne z agentem Windows.  

Windows lub dane pamięci podręcznej agenta serwer zarządzania są chronione przez Magazyn poświadczeń systemu operacyjnego. Jeśli usługa nie może przetwarzać dane po dwóch godzinach, dane do kolejki agentów. Jeśli kolejka zostanie zapełniona, agent uruchamia porzucenie typów danych, rozpoczynając od danych dotyczących wydajności. Ograniczenie kolejki agenta jest klucz rejestru, dzięki czemu można je zmodyfikować, jeśli to konieczne. Zebranych danych jest skompresowany i wysyłane do usługi, z pominięciem bazy danych grupy zarządzania programu Operations Manager, dzięki czemu obciążenie nie są dodawane do nich. Po wysłaniu zebranych danych, zostanie ono usunięte z pamięci podręcznej.

Zgodnie z powyższym opisem z serwera zarządzania lub agenci połączone bezpośrednio przesyłane za pośrednictwem protokołu SSL do centrach danych platformy Microsoft Azure. Opcjonalnie można użyć usługi ExpressRoute, można zapewnić większe bezpieczeństwo dla danych. Usługa ExpressRoute jest sposób połączyć się bezpośrednio na platformie Azure z istniejącej sieci WAN, takich jak wiele protokołów etykiety przełączania (Switching) VPN, udostępnianej przez dostawcę usług sieciowych. Aby uzyskać więcej informacji, zobacz [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. Usługa Log Analytics odbiera i przetwarza dane
Usługa Log Analytics zapewnia dane przychodzące z zaufanego źródła, sprawdzając poprawność certyfikatów i integralności danych za pomocą uwierzytelniania platformy Azure. Nieprzetworzone dane pierwotne, następnie znajduje się w Centrum zdarzeń platformy Azure w regionie, dane będą przechowywane po pewnym czasie w stanie spoczynku. Typ danych przechowywanych zależy od typów rozwiązań, które zostały zaimportowane, a następnie używane do zbierania danych. Następnie usługi Log Analytics usługi procesy nieprzetworzone dane i pozyskuje go do bazy danych.

Okres przechowywania zebranych danych przechowywanych w bazie danych, zależy od wybranego planu cenowego. W przypadku warstwy *bezpłatna* zebrane dane są dostępne przez siedem dni. W przypadku warstwy *Płatne* zebrane dane są domyślnie dostępne przez 31 dni, ale można je przedłużyć do 730 dni. Dane są przechowywane, szyfrowane, gdy w usłudze Azure storage, aby zapewnić poufność danych, a dane są replikowane w regionie lokalnym przy użyciu magazynu lokalnie nadmiarowego (LRS). Ostatnie dwa tygodnie danych również są przechowywane w pamięci podręcznej opartej na dyskach SSD, a ta pamięć podręczna jest zaszyfrowana.

## <a name="4-use-log-analytics-to-access-the-data"></a>4. Użyj Log Analytics, aby uzyskać dostęp do danych
Aby uzyskać dostęp do obszaru roboczego usługi Log Analytics, możesz zarejestrować się do witryny Azure portal przy użyciu konta organizacji lub konta Microsoft, które wcześniej. Cały ruch między portalem i usługą Log Analytics są wysyłane za pośrednictwem bezpiecznego kanału protokołu HTTPS. Korzystając z portalu, identyfikator sesji jest generowany na kliencie użytkownika (przeglądarka sieci web), a dane są przechowywane w lokalnej pamięci podręcznej, dopóki sesja zostanie zakończona. Gdy zakończone, pamięci podręcznej zostaną usunięte. Pliki cookie po stronie klienta, które nie zawierają danych osobowych, nie są automatycznie usuwane. Pliki cookie dotyczące sesji są oznaczane HTTPOnly i są chronione. Po upływie wstępnie ustaloną bezczynności sesji portalu platformy Azure zostanie zakończony.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak zbierać dane za pomocą Log Analytics dla maszyn wirtualnych platformy Azure po [rozszybkim samouczku maszyny wirtualnej platformy Azure](../../azure-monitor/learn/quick-collect-azurevm.md).  

*  Jeśli chcesz zbierać dane z komputerów fizycznych lub wirtualnych z systemami Windows lub Linux w środowisku, zobacz [Przewodnik Szybki Start dla](../../azure-monitor/learn/quick-collect-linux-computer.md) komputerów z systemem Linux lub [Szybki Start dla komputerów z systemem Windows](../../azure-monitor/learn/quick-collect-windows-computer.md)

