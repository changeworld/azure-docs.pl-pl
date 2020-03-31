---
title: Bezpieczeństwo danych usługi Log Analytics | Dokumenty firmy Microsoft
description: Dowiedz się, jak usługa Log Analytics chroni twoją prywatność i zabezpiecza Twoje dane.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/04/2019
ms.openlocfilehash: 63d8d8d3701a9adca4bd01e6e061877f5d0bd245
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80333358"
---
# <a name="log-analytics-data-security"></a>Bezpieczeństwo danych usługi Log Analytics
Ten dokument ma na celu dostarczenie informacji specyficznych dla usługi Log Analytics, która jest funkcją usługi Azure Monitor, w celu uzupełnienia informacji w [usłudze Azure Trust Center.](../../security/fundamentals/trust-center.md)  

W tym artykule wyjaśniono sposób zbierania, przetwarzania i zabezpieczania danych przez usługę Log Analytics. Za pomocą agentów można łączyć się z usługą sieci web, używać programu System Center Operations Manager do zbierania danych operacyjnych lub pobierać dane z diagnostyki platformy Azure do użytku przez usługę Log Analytics. 

Usługa log analytics bezpiecznie zarządza danymi w chmurze przy użyciu następujących metod:

* Podział danych
* Przechowywanie danych
* Zabezpieczenia fizyczne
* Zarządzanie zdarzeniami
* Zgodność
* Certyfikaty norm bezpieczeństwa

Skontaktuj się z nami w przypadku pytań, sugestii lub problemów dotyczących następujących informacji, w tym naszych zasad zabezpieczeń dostępnych w [opcjach pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/options/)

## <a name="sending-data-securely-using-tls-12"></a>Bezpieczne wysyłanie danych za pomocą protokołu TLS 1.2 

Aby ubezpieczyć bezpieczeństwo danych przesyłanych do usługi Log Analytics, zdecydowanie zachęcamy do skonfigurowania agenta do używania co najmniej usługi Transport Layer Security (TLS) 1.2. Starsze wersje TLS/Secure Sockets Layer (SSL) okazały się podatne na ataki i chociaż nadal działają, aby umożliwić wsteczną kompatybilność, nie są **zalecane,** a branża szybko rezygnuje z obsługi tych starszych protokołów. 

[Rada Standardów Bezpieczeństwa PCI](https://www.pcisecuritystandards.org/) wyznaczyła [termin 30 czerwca 2018 r.](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) na wyłączenie starszych wersji protokołu TLS/SSL i uaktualnienie do bezpieczniejszych protokołów. Gdy platforma Azure porzuci starszą obsługę, jeśli twoi agenci nie mogą komunikować się za pomocą co najmniej protokołu TLS 1.2, nie będzie można wysyłać danych do usługi Log Analytics. 

Nie zalecamy jawnego ustawiania agenta tylko do korzystania z protokołu TLS 1.2, chyba że jest to absolutnie konieczne, ponieważ może on złamać funkcje zabezpieczeń na poziomie platformy, które pozwalają automatycznie wykrywać i korzystać z nowszych, bezpieczniejszych protokołów, gdy tylko staną się dostępne, takie jak jak TLS 1.3. 

### <a name="platform-specific-guidance"></a>Wytyczne dotyczące poszczególnych platform

|Platforma/język | Pomoc techniczna | Więcej informacji |
| --- | --- | --- |
|Linux | Dystrybucje Linuksa zwykle polegają na [OpenSSL](https://www.openssl.org) dla obsługi TLS 1.2.  | Sprawdź [OpenSSL Changelog,](https://www.openssl.org/news/changelog.html) aby potwierdzić, że twoja wersja OpenSSL jest obsługiwana.|
| Windows 8.0 - 10 | Obsługiwane i domyślnie włączone. | Aby potwierdzić, że nadal używasz [ustawień domyślnych](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings).  |
| Windows Server 2012 - 2016 | Obsługiwane i domyślnie włączone. | Aby potwierdzić, że nadal używasz [ustawień domyślnych](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) |
| Windows 7 z dodatku SP1 i windows server 2008 R2 z dodatku SP1 | Obsługiwane, ale domyślnie nie włączone. | Szczegółowe informacje na temat włączania można znaleźć na stronie [ustawień rejestru TLS (Transport Layer Security).](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings)  |

## <a name="data-segregation"></a>Podział danych
Po połknięciu danych przez usługę usługi usługi Log Analytics dane są logicznie oddzielone od każdego składnika w całej usłudze. Wszystkie dane są oznaczane na obszar roboczy. To tagowanie jest obecne przez cały cykl życia danych i jest wymuszane w każdej warstwie usługi. Dane są przechowywane w dedykowanej bazie danych w klastrze magazynu w wybranym regionie.

## <a name="data-retention"></a>Przechowywanie danych
Indeksowane dane wyszukiwania dziennika są przechowywane i przechowywane zgodnie z planem cenowym. Aby uzyskać więcej informacji, zobacz [Cennik analizy dzienników](https://azure.microsoft.com/pricing/details/log-analytics/).

W ramach [umowy subskrypcyjnej](https://azure.microsoft.com/support/legal/subscription-agreement/)firma Microsoft będzie przechowywać dane użytkownika zgodnie z warunkami umowy.  Po usunięciu danych klienta żadne dyski fizyczne nie są niszczone.  

W poniższej tabeli wymieniono niektóre z dostępnych rozwiązań i przedstawiono przykłady typu gromadzonych danych.

| **Rozwiązanie** | **Typy danych** |
| --- | --- |
| Pojemność i wydajność |Dane dotyczące wydajności i metadane |
| Zarządzanie aktualizacjami |Metadane i dane o stanie |
| Zarządzanie dziennikami |Dzienniki zdarzeń zdefiniowane przez użytkownika, dzienniki zdarzeń systemu Windows i/lub dzienniki IIS |
| Śledzenie zmian |Spis oprogramowania, metadane demonów systemu Windows i systemu Linux oraz metadane plików systemu Windows/Linux |
| Ocena SQL i usługi Active Directory |Wyniki widoku zarządzania dynamicznego usługi WMI, danych rejestru, danych wydajności i widoku zarządzania dynamicznego programu SQL Server |

W poniższej tabeli przedstawiono przykłady typów danych:

| **Typ danych** | **Pola** |
| --- | --- |
| Alerty |Nazwa alertu, Opis alertu, BaseManagedEntityId, Identyfikator problemu, IsMonitorAlert, RuleId, ResolutionState, Priority, Severity, Category, Owner, ResolvedBy, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount |
| Konfigurowanie |Identyfikator klienta, identyfikator agenta, identyfikator encji, identyfikator typu zarządzanego, identyfikator managedtypepropertyid, wartość bieżąca, zmiana dytu |
| Wydarzenie |EventId, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, Number, Category, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded <br>**Uwaga:** Podczas pisania zdarzeń z polami niestandardowymi w dzienniku zdarzeń systemu Windows usługa Log Analytics je zbiera. |
| Metadane |BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Adres, NetbiosDomainName, Logiczneprocesory, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime |
| Wydajność |ObjectName, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| Stan |StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>Zabezpieczenia fizyczne
Usługa log analytics jest zarządzana przez personel firmy Microsoft i wszystkie działania są rejestrowane i mogą być poddane inspekcji. Usługa Log Analytics jest obsługiwana jako usługa Azure i spełnia wszystkie wymagania dotyczące zgodności i zabezpieczeń platformy Azure. Szczegółowe informacje na temat fizycznego bezpieczeństwa zasobów platformy Azure można wyświetlić na stronie 18 [przeglądu zabezpieczeń platformy Microsoft Azure](https://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf). Fizyczne prawa dostępu do bezpiecznych obszarów są zmieniane w ciągu jednego dnia roboczego dla każdego, kto nie ponosi już odpowiedzialności za usługę Log Analytics, w tym za transfer i zakończenie. Możesz przeczytać o globalnej infrastrukturze fizycznej, której używamy w [centrach danych firmy Microsoft](https://azure.microsoft.com/global-infrastructure/).

## <a name="incident-management"></a>Zarządzanie zdarzeniami
Usługa Log Analytics ma proces zarządzania zdarzeniami, który są przestrzegane przez wszystkie usługi firmy Microsoft. Podsumowując,:

* Użyj modelu współużytkowania, w którym część odpowiedzialności za bezpieczeństwo należy do firmy Microsoft, a część należy do klienta
* Zarządzanie zdarzeniami zabezpieczeń platformy Azure:
  * Wszczęcie dochodzenia po wykryciu incydentu
  * Oceń wpływ i dotkliwość incydentu przez członka zespołu reagowania na incydent podczas rozmowy. Na podstawie dowodów ocena może, ale nie musi, prowadzić do dalszej eskalacji do zespołu reagowania na zabezpieczenia.
  * Diagnozuj incydent przez ekspertów od reagowania na zagrożenia, aby przeprowadzić dochodzenie techniczne lub kryminalistyczne, zidentyfikować strategie zamknięcia, łagodzenia i obejścia problemu. Jeśli zespół bezpieczeństwa uważa, że dane klienta mogły zostać narażone na działanie osoby niezgodnej z prawem lub nieautoryzowanej osoby, równoległe wykonanie procesu powiadamiania o zdarzeniu klienta rozpoczyna się równolegle.  
  * Ustabilizuj się i odzyskaj po incydencie. Zespół reagowania na incydenty tworzy plan odzyskiwania w celu złagodzenia problemu. Kroki zamknięcia kryzysowego, takie jak kłótnie systemów, których dotyczy problem, mogą wystąpić natychmiast i równolegle z diagnozą. Można zaplanować długoterminowe środki zaradcze, które występują po przejściu bezpośredniego ryzyka.  
  * Zamknij incydent i przepoj post-mortem. Zespół reagowania na incydenty tworzy sekcję zwłok, która przedstawia szczegóły zdarzenia, z zamiarem zmiany zasad, procedur i procesów, aby zapobiec powtórzeniu się zdarzenia.
* Powiadamiaj klientów o zdarzeniach związanych z bezpieczeństwem:
  * Określenie zakresu klientów, na które ma wpływ, i dostarczenie każdemu, kto ma wpływ, jak najduszniejsze powiadomienie
  * Utwórz powiadomienie, aby dostarczyć klientom wystarczająco szczegółowych informacji, aby mogli przeprowadzić dochodzenie po ich zakończeniu i wywiązać się z wszelkich zobowiązań, które podjęli wobec swoich użytkowników końcowych, nie opóźniając nadmiernie procesu powiadamiania.
  * W razie potrzeby potwierdź i zadeklaruj incydent.
  * Powiadamiaj klientów o zdarzeniu bez nieuzasadnionej zwłoki i zgodnie z wszelkimi zobowiązaniami prawnymi lub umownymi. Powiadomienia o zdarzeniach związanych z bezpieczeństwem są dostarczane do jednego lub kilku administratorów klienta za pomocą dowolnego wyboru przez firmę Microsoft, w tym za pośrednictwem poczty e-mail.
* Prowadzenie gotowości zespołu i szkolenia:
  * Pracownicy firmy Microsoft są zobowiązani do ukończenia szkolenia w zakresie zabezpieczeń i świadomości, które pomaga im identyfikować i zgłaszać podejrzewane problemy z zabezpieczeniami.  
  * Operatorzy pracujący nad usługą Microsoft Azure mają dodatkowe obowiązki szkoleniowe dotyczące dostępu do poufnych systemów obsługujących dane klientów.
  * Pracownicy firmy Microsoft otrzymują specjalistyczne szkolenia dotyczące swoich ról

W przypadku utraty jakichkolwiek danych klienta, powiadamiamy każdego klienta w ciągu jednego dnia. Jednak utrata danych klienta nigdy nie wystąpiła z usługą. 

Aby uzyskać więcej informacji o tym, jak firma Microsoft reaguje na zdarzenia związane z zabezpieczeniami, zobacz [Microsoft Azure Security Response in the Cloud](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/4/Microsoft%20Azure%20Security%20Response%20in%20the%20cloud.pdf).

## <a name="compliance"></a>Zgodność
Program bezpieczeństwa i zarządzania informacjami zespołu Log Analytics i service team obsługuje jego wymagania biznesowe i przestrzega przepisów i regulacji opisanych w [Centrum zaufania platformy Microsoft Azure](https://azure.microsoft.com/support/trust-center/) i zgodności z centrum zaufania firmy [Microsoft.](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx) Jak usługa Log Analytics ustanawia wymagania dotyczące zabezpieczeń, identyfikuje mechanizmy kontroli zabezpieczeń, zarządza i monitoruje zagrożenia są również opisane w tym zakresie. Co roku dokonujemy przeglądu bezpieczeństwa, standardów, procedur i wytycznych.

Każdy członek zespołu deweloperów otrzymuje formalne szkolenie w zakresie zabezpieczeń aplikacji. Wewnętrznie używamy systemu kontroli wersji do tworzenia oprogramowania. Każdy projekt oprogramowania jest chroniony przez system kontroli wersji.

Firma Microsoft ma zespół zabezpieczeń i zgodności, który nadzoruje i ocenia wszystkie usługi w firmie Microsoft. Oficerowie bezpieczeństwa informacji tworzą zespół i nie są związani z zespołami inżynierskimi, które rozwijają usługi Log Analytics. Funkcjonariusze ochrony mają własny łańcuch zarządzania i przeprowadzają niezależne oceny produktów i usług w celu zapewnienia bezpieczeństwa i zgodności.

Zarząd firmy Microsoft jest powiadamiany przez roczny raport o wszystkich programach bezpieczeństwa informacji w firmie Microsoft.

Zespół zajmujący się tworzeniem oprogramowania i usługą Log Analytics aktywnie współpracuje z zespołami microsoftu i zgodności oraz innymi partnerami branżowymi w celu uzyskania różnych certyfikatów.

## <a name="certifications-and-attestations"></a>Certyfikaty i świadectwa
Usługa Azure Log Analytics spełnia następujące wymagania:

* [ISO/IEC 27001](https://www.iso.org/iso/home/standards/management-standards/iso27001.htm)
* [ISO/IEC 27018:2014](https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498)
* [ISO 22301](https://azure.microsoft.com/blog/iso22301/)
* [Payment Card Industry (PCI Compliant) Data Security Standard (PCI DSS)](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI) przez Radę Standardów Bezpieczeństwa PCI.
* [Zgodność z normami SoC (SOC) 1 Typu 1 i SOC 2 Type 1](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2)
* [HIPAA i HITECH](https://www.microsoft.com/en-us/TrustCenter/Compliance/hipaa) dla firm, które mają HIPAA Business Associate Agreement
* Typowe kryteria inżynieryjne systemu Windows
* Microsoft Godne zaufania computing
* Jako usługa platformy Azure składniki, których używa usługa Log Analytics, są zgodne z wymaganiami dotyczącymi zgodności platformy Azure. Więcej informacji można przeczytać na stronie [Microsoft Trust Center Compliance](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx).

> [!NOTE]
> W niektórych certyfikatach/certyfikatach usługa Log Analytics jest wyświetlana pod dawną nazwą *Operational Insights*.
>
>

## <a name="cloud-computing-security-data-flow"></a>Przepływ danych dotyczących bezpieczeństwa przetwarzania w chmurze
Na poniższym diagramie przedstawiono architekturę zabezpieczeń w chmurze jako przepływ informacji z firmy i sposób, w jaki jest ona zabezpieczona w miarę przenoszenia do usługi analizy dzienników, ostatecznie widziana przez Użytkownika w witrynie Azure portal. Więcej informacji na temat każdego kroku następuje po diagramie.

![Obraz zbierania i zabezpieczeń danych usługi Log Analytics](./media/data-security/log-analytics-data-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. Zarejestruj się w log analytics i zbieraj dane
Aby twoja organizacja wysyłała dane do usługi Log Analytics, konfigurujesz agenta systemu Windows lub Systemu Linux działającego na maszynach wirtualnych platformy Azure lub na komputerach wirtualnych lub fizycznych w danym środowisku lub innym dostawcy chmury.  Jeśli używasz programu Operations Manager, z grupy zarządzania można skonfigurować agenta programu Operations Manager. Użytkownicy (którym może być Ty, inni poszczególni użytkownicy lub grupa osób) tworzą co najmniej jeden obszar roboczy usługi Log Analytics i rejestrują agentów przy użyciu jednego z następujących kont:

* [Identyfikator organizacyjny](../../active-directory/fundamentals/sign-up-organization.md)
* [Konto Microsoft — Outlook, Office Live, MSN](https://account.microsoft.com/account)

Obszar roboczy usługi Log Analytics to miejsce, w którym dane są zbierane, agregowane, analizowane i prezentowane. Obszar roboczy jest używany głównie jako środek do partycjonowania danych, a każdy obszar roboczy jest unikatowy. Na przykład można mieć dane produkcyjne zarządzane za pomocą jednego obszaru roboczego i danych testowych zarządzanych za pomocą innego obszaru roboczego. Obszary robocze pomagają również administratorowi kontrolować dostęp użytkownika do danych. Każdy obszar roboczy może mieć skojarzone z nim wiele kont użytkowników, a każde konto użytkownika może uzyskiwać dostęp do wielu obszarów roboczych usługi Log Analytics. Obszary robocze są tworzą na podstawie regionu centrum danych.

W przypadku programu Operations Manager grupa zarządzania programem Operations Manager nawiązuje połączenie z usługą analizy dzienników. Następnie można skonfigurować, które systemy zarządzane przez agenta w grupie zarządzania mogą zbierać i wysyłać dane do usługi. W zależności od włączonego rozwiązania dane z tych rozwiązań są wysyłane bezpośrednio z serwera zarządzania programu Operations Manager do usługi Log Analytics lub ze względu na ilość danych zebranych przez system zarządzany przez agenta są wysyłane bezpośrednio z agenta do serwisu. W przypadku systemów, które nie są monitorowane przez program Operations Manager, każdy łączy się bezpiecznie bezpośrednio z usługą analizy dzienników.

Cała komunikacja między połączonymi systemami a usługą Log Analytics jest szyfrowana. Protokół TLS (HTTPS) jest używany do szyfrowania.  Proces Microsoft SDL jest śledzony, aby upewnić się, że usługa Log Analytics jest aktualna z najnowszymi postępami w protokołach kryptograficznych.

Każdy typ agenta zbiera dane dla usługi Log Analytics. Typ gromadzonych danych zależy od typów używanych rozwiązań. Podsumowanie zbierania danych można zobaczyć w [galerii rozwiązań Add Log Analytics w Galerii rozwiązań](../../azure-monitor/insights/solutions.md). Ponadto dla większości rozwiązań dostępne są bardziej szczegółowe informacje o zbierania. Rozwiązanie to pakiet wstępnie zdefiniowanych widoków, zapytań wyszukiwania dziennika, reguł zbierania danych i logiki przetwarzania. Tylko administratorzy mogą za pomocą usługi Log Analytics zaimportować rozwiązanie. Po zaimportowaniu rozwiązania jest ono przenoszone na serwery zarządzania programu Operations Manager (jeśli są używane), a następnie do wszystkich wybranych agentów. Następnie agenci zbierają dane.

## <a name="2-send-data-from-agents"></a>2. Wysyłanie danych od agentów
Rejestrujesz wszystkie typy agentów z kluczem rejestracji i nawiązywano bezpieczne połączenie między agentem a usługą usługi Log Analytics przy użyciu uwierzytelniania opartego na certyfikatach i protokołu TLS z portem 443. Usługa Log Analytics używa tajnego magazynu do generowania i obsługi kluczy. Klucze prywatne są obracane co 90 dni i są przechowywane na platformie Azure i są zarządzane przez operacje platformy Azure, które przestrzegają ścisłych zasad regulacyjnych i zgodności.

W programie Operations Manager grupa zarządzania zarejestrowana w obszarze roboczym usługi Log Analytics ustanawia bezpieczne połączenie HTTPS z serwerem zarządzania programu Operations Manager.

W przypadku agentów systemu Windows lub systemu Linux działających na maszynach wirtualnych platformy Azure klucz magazynu tylko do odczytu jest używany do odczytywania zdarzeń diagnostycznych w tabelach platformy Azure.  

W przypadku każdego agenta raportowanego z grupą zarządzania programu Operations Manager, która jest zintegrowana z usługą Log Analytics, jeśli serwer zarządzania nie może komunikować się z usługą z jakiegokolwiek powodu, zebrane dane są przechowywane lokalnie w tymczasowej pamięci podręcznej w zarządzaniu Serwera.   Próbują ponownie przesyłać dane co osiem minut przez dwie godziny.  W przypadku danych, które omijają serwer zarządzania i są wysyłane bezpośrednio do usługi Log Analytics, zachowanie jest zgodne z agentem systemu Windows.  

Dane buforowane agenta systemu Windows lub serwera zarządzania są chronione przez magazyn poświadczeń systemu operacyjnego. Jeśli usługa nie może przetworzyć danych po dwóch godzinach, agenci będą kolejkować dane. Jeśli kolejka zostanie zapełniona, agent rozpoczyna upuszczanie typów danych, począwszy od danych wydajności. Limit kolejki agenta jest kluczem rejestru, dzięki czemu można go zmodyfikować, jeśli to konieczne. Zebrane dane są kompresowane i wysyłane do usługi, z pominięciem baz danych grup zarządzania programu Operations Manager, dzięki czemu nie dodaje do nich żadnego obciążenia. Po wysłaniu zebranych danych są usuwane z pamięci podręcznej.

Jak opisano powyżej, dane z serwera zarządzania lub agentów połączonych bezpośrednio są wysyłane za pomocą protokołu TLS do centrów danych platformy Microsoft Azure. Opcjonalnie można użyć usługi ExpressRoute, aby zapewnić dodatkowe zabezpieczenia danych. Usługa ExpressRoute to sposób bezpośredniego łączenia się z platformą Azure z istniejącej sieci WAN, takiej jak sieć VPN przełączania etykiet wieloprotokołowych (MPLS), świadczona przez dostawcę usług sieciowych. Aby uzyskać więcej informacji, zobacz [Usługa ExpressRoute](https://azure.microsoft.com/services/expressroute/).

## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. Usługa Log Analytics odbiera i przetwarza dane
Usługa usługi Log Analytics zapewnia, że przychodzące dane pochodzą z zaufanego źródła, sprawdzając certyfikaty i integralność danych za pomocą uwierzytelniania platformy Azure. Nieprzetworzone nieprzetworzone dane pierwotne są następnie przechowywane w usłudze Azure Event Hub w regionie, w tym czasie dane będą ostatecznie przechowywane w spoczynku. Typ przechowywanych danych zależy od typów rozwiązań, które zostały zaimportowane i używane do zbierania danych. Następnie usługa usługi Log Analytics przetwarza nieprzetworzone dane i pochocieje go do bazy danych.

Okres przechowywania zebranych danych przechowywanych w bazie danych zależy od wybranego planu cenowego. W warstwie *Bezpłatna* zebrane dane są dostępne przez siedem dni. W warstwie *Płatne* zebrane dane są domyślnie dostępne przez 31 dni, ale można je przedłużyć do 730 dni. Dane są przechowywane zaszyfrowane w spoczynku w magazynie platformy Azure, aby zapewnić poufność danych, a dane są replikowane w regionie lokalnym przy użyciu lokalnie nadmiarowego magazynu (LRS). Ostatnie dwa tygodnie danych są również przechowywane w pamięci podręcznej opartej na dyskach SSD i ta pamięć podręczna jest szyfrowana.

## <a name="4-use-log-analytics-to-access-the-data"></a>4. Użyj analizy dzienników, aby uzyskać dostęp do danych
Aby uzyskać dostęp do obszaru roboczego usługi Log Analytics, należy zalogować się do witryny Azure Portal przy użyciu konta instytucji lub konta Microsoft, które zostało wcześniej skonfigurowane. Cały ruch między portalem a usługą usługi Log Analytics jest wysyłany za pośrednictwem bezpiecznego kanału HTTPS. Podczas korzystania z portalu identyfikator sesji jest generowany na kliencie użytkownika (przeglądarce sieci web), a dane są przechowywane w lokalnej pamięci podręcznej do czasu zakończenia sesji. Po zakończeniu pamięci podręcznej jest usuwany. Pliki cookie po stronie klienta, które nie zawierają danych osobowych, nie są automatycznie usuwane. Sesyjne pliki cookie są oznaczone httponly i są zabezpieczone. Po określonym z góry okresie bezczynnym sesja portalu Azure jest kończona.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak zbierać dane za pomocą usługi Log Analytics dla maszyn wirtualnych platformy Azure po przewodniku [Szybki start maszyny Wirtualnej platformy Azure.](../../azure-monitor/learn/quick-collect-azurevm.md)  

*  Jeśli chcesz zbierać dane z fizycznych lub wirtualnych komputerów z systemem Windows lub Linux w danym środowisku, zobacz [Szybki start dla komputerów z systemem Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) lub Szybki start dla [komputerów](../../azure-monitor/learn/quick-collect-windows-computer.md) z systemem Windows

