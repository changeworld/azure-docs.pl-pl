---
title: Urządzenia platformy Azure, lokalne i zabezpieczenia fizyczne | Dokumentacja firmy Microsoft
description: Artykuł opisuje centrach danych platformy Azure, w tym infrastruktury fizycznej, zabezpieczeń i zgodności oferty.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: a6a9b1d6e12dabb09cde684c34481b4b3442c1b8
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102470"
---
# <a name="azure-facilities-premises-and-physical-security"></a>Urządzenia platformy Azure, lokalne i zabezpieczenia fizyczne
Firmy Microsoft w chmurze składa się z [infrastruktury centrum danych globalnie rozproszone](https://azure.microsoft.com/global-infrastructure/) obsługujący tysiące usług online i wysoce obejmujące więcej niż 100 zabezpieczyć urządzenia na całym świecie.

Infrastruktura zaprojektowano w celu dostosowania bliżej aplikacji dla użytkowników na świecie, zachowując dane siedzibę i oferuje kompleksowe opcje zgodności i odporność dla klientów. Azure ma 52 regionów na całym świecie i jest dostępny w 140 krajach.

Region jest zestawem centrów danych, które są połączone ze sobą za pośrednictwem sieci masowych i odporność. Domyślnie sieci zawiera dystrybucji zawartości, równoważenia obciążenia, nadmiarowości i szyfrowania. Ponieważ platforma Azure ma więcej regionów globalnych niż którykolwiek inny dostawca chmury, umożliwia klientom wdrażanie aplikacji tam, gdzie tego potrzebują.

Regiony świadczenia usługi Azure są zorganizowane w lokalizacje geograficzne. Lokalizacja geograficzna platformy Azure zapewnia, że wymagania z zakresu odporności, zgodności, niezależności i rezydencji danych są honorowane w granicach geograficznych.

Lokalizacje geograficzne pozwalają klientom z konkretnymi potrzebami w zakresie zgodności i rezydencji danych na przechowywanie swoich danych i aplikacji w bliskiej odległości. Lokalizacji geograficznych są odpornej na uszkodzenia wytrzymać awarii pełną region za pośrednictwem połączenia dedykowanej infrastruktury sieci dużej pojemności.

Strefy dostępności to fizycznie oddzielone lokalizacje w regionie świadczenia usługi Azure. Każda strefa dostępności obejmuje co najmniej jedno centrum danych wyposażone w niezależne zasilanie i chłodzenie oraz niezależną sieć. Strefy dostępności umożliwiają klientom uruchamianie kluczowych dla działalności aplikacji z wysoką dostępnością i replikacją o małych opóźnieniach.

Na poniższej ilustracji przedstawiono infrastruktury platformy Azure globalne parowaniem regionu i strefy dostępności w ramach tej samej granicy siedziby danych wysokiej dostępności, odzyskiwania po awarii i tworzenie kopii zapasowych.

![Granic siedziby danych][1]

Duże zużycie rozproszonej geograficzne centrów danych umożliwia firmie Microsoft jest bliski klientów w celu zmniejszenia opóźnienia sieci i umożliwia geograficznie nadmiarowego kopii zapasowych i pracy awaryjnej.

## <a name="physical-security"></a>Zabezpieczenia fizyczne
Firma Microsoft projektuje, tworzy i działa centrów danych w taki sposób, aby ściśle określa fizyczny dostęp do obszarów, w którym są przechowywane dane klienta. Firma Microsoft rozumie, ważność ochrony danych klienta i są zaangażowane ochroną centrach danych, która zawiera dane. Mamy dzielenia całej firmy Microsoft przeznaczone na projektowanie, tworzenie i obsługę urządzenia fizyczne Obsługa platformy Azure. Zespół ten jest zainwestowały w zachowaniu zabezpieczeń fizycznych stanu grafiki.

Microsoft przyjmuje warstwowego podejścia do zabezpieczeń fizycznych w celu ograniczenia ryzyka nieautoryzowanego fizyczny dostęp do danych i zasobów Centrum danych. Zarządzany przez firmę Microsoft centrów danych ma szeroką gamę warstw ochrony: dostęp do zatwierdzenia w obwodzie placówki w obwodzie budynku, wewnątrz budynku i piętra centrum danych. Warstwy zabezpieczeń fizycznych są:

- Żądanie dostępu i zatwierdzania — należy zażądać dostępu przed dotarciem do centrum danych. Musisz podać prawidłową biznesowego wyjaśnienia wizycie klienta, takich jak zgodność lub inspekcji. Wszystkie żądania są zatwierdzane w zasadach dostępu konieczne przez pracowników firmy Microsoft. Podstawa dostępu konieczne pomaga w zapewnieniu liczba osób wymaganego do ukończenia zadania w centrach danych do minimum systemu od zera. Po udzieleniu uprawnień osoba ma dostęp tylko do obszaru odrębny centrum danych oparte na zatwierdzonych biznesowego wyjaśnienia. Uprawnienia są ograniczone do okresu czasu i wygasa po upływie okresu czasu dozwolony.

- Obwodowej placówki — po przyjeździe centrum danych, możesz wymagane przechodzić przez punkt dostępu dobrze zdefiniowany. Zazwyczaj wysokość ogrodzenia stali i konkretnych obejmują każdy cal obwodu. Brak kamer wokół centrach danych, z zespołem zabezpieczeń monitorowania plików wideo 24/7 i 365 dni w roku.

- Kompilowanie wejściu — wejście centrum danych jest sprzęt funkcjonariusze professional, które zostały poddane rygorystyczne kontroli szkolenia i tła. Te funkcjonariusze również regularnie patrolowania centrum danych, a także monitorować wideo kamery wewnątrz centrum danych 24/7 i 365 dni w roku.

- Budynku — po wprowadzeniu budynku, należy przekazać uwierzytelniania dwuskładnikowego z rozwiązań biometrycznych, aby kontynuować, Nawigacja po centrum danych. Jeśli jest zweryfikować swoją tożsamość, możesz wprowadzić część centrum danych, które zostały zatwierdzone dostęp do. Można tam pozostać tylko na czas trwania czas zatwierdzenia.

- Floor Datacenter — dozwolonych jest tylko do floor że są zatwierdzeni wprowadzenia. Jest wymagane do przekazywania kontroli wykrywania metali pełną treść. Aby zmniejszyć ryzyko nieautoryzowanego danych wprowadzanych lub opuszczających centrum danych bez wiedzy naszych, tylko zatwierdzone urządzenia można przekształcić ich sposób floor centrum danych. Ponadto monitor wideo kamery przodu i z powrotem każdego serwera w stojaku. Pełna treść wykrywania metali kontroli jest powtarzany przy zamykaniu floor centrum danych. Aby opuścić centrum danych, musisz przechodzą przez skanowanie dodatkowe zabezpieczenia.

Osoby odwiedzające są wymagane do przekazania identyfikatory po wyjściu z dowolnego obiektu Microsoft.

## <a name="physical-security-reviews"></a>Inspekcje zabezpieczeń fizycznych
Inspekcje zabezpieczeń fizycznych urządzeń są wykonywane okresowo zapewnienie centrach danych, prawidłowo wymagania dotyczące zabezpieczeń Microsoft Azure adresów. Centrum danych personelu dostawcy hostingu nie udostępniają zarządzania usługi Microsoft Azure. Personel nie mają dostępu logowania do platformy Azure systemów lub fizyczny dostęp do miejsca kolokacji Azure i klatek.

## <a name="data-bearing-devices"></a>Dane wpływu urządzeń
Firma Microsoft korzysta z najlepszych rozwiązań procedury i wiping rozwiązania, które jest [NIST 800-88 zgodne](https://csrc.nist.gov/publications/detail/sp/800-88/archive/2006-09-01). W przypadku dysków twardych, które nie mogą być czyszczone proces zniszczenie niszczy go i renderuje odzyskiwania informacji niemożliwe jest używany. Zniszczenie procesu może być rozpaść, "Podrasowane", rozpylać lub spalania. Odpowiednie środki usuwania jest określana przez typ zasobu. Rejestruje zniszczenie zostaną zachowane.  

## <a name="equipment-disposal"></a>Usuwanie urządzenia
Microsoft Azure implementuje tę zasadę w imieniu klientów. Na system z wycofanych operacyjne personel firmy Microsoft należy wykonać procedury usuwania sprzętu, aby mieć pewność, że sprzęt zawierający dane klienta nie będą dostępne dla niezaufane i obsługi danych rygorystyczne. Podejście Secure wymazać nastąpią (za pomocą oprogramowania układowego dysku twardego) dla dysków, które obsługują tę. W przypadku dysków twardych, które nie mogą być czyszczone proces zniszczenie niszczy go i renderuje odzyskiwania informacji niemożliwe jest używany. Zniszczenie procesu może być rozpaść, "Podrasowane", rozpylać lub spalania. Odpowiednie środki usuwania jest określana przez typ zasobu. Rejestruje zniszczenie zostaną zachowane. Wszystkie usługi Microsoft Azure za pomocą usług zarządzania przechowywania i usuwania zatwierdzonych nośnika.

## <a name="compliance"></a>Zgodność
Infrastruktury platformy Azure został zaprojektowany i zarządzane w celu spełnienia standardów zgodności międzynarodowych i branżowe, takie jak ISO 27001, HIPAA FedRAMP, SOC 1 i SOC 2 szeroki. Standardy określonego kraju również są spełnione, tym IRAP Australii, UK G-chmury i MTCS Singapuru. Rygorystyczne audyty przeprowadzane przez podmioty zewnętrzne, takie jak British Standards Institute, potwierdzają, że na platformie Azure są stosowane rygorystyczne kontrole zabezpieczeń wymagane w tych standardach.

Zobacz [ofert zgodności](https://www.microsoft.com/trustcenter/compliance/complianceofferings) dla pełną listę standardów zgodności przestrzegane przez platformę Azure.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat firmy Microsoft jest zapewnienie infrastruktury platformy Azure, zobacz:

- [Dostępność infrastruktury platformy Azure](azure-infrastructure-availability.md)
- [Składniki systemu Azure informacji i granice](azure-infrastructure-components.md)
- [Architektura sieci platformy Azure](azure-infrastructure-network.md)
- [Sieci Azure środowiska produkcyjnego](azure-production-network.md)
- [Funkcje zabezpieczeń bazy danych SQL Azure firmy Microsoft](azure-infrastructure-sql.md)
- [Operacje Azure środowiska produkcyjnego i zarządzania](azure-infrastructure-operations.md)
- [Monitorowanie infrastruktury platformy Azure](azure-infrastructure-monitoring.md)
- [Integralność infrastruktury platformy Azure](azure-infrastructure-integrity.md)
- [Ochrona danych klienta na platformie Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-physical-security/data-residency-boundary.png
