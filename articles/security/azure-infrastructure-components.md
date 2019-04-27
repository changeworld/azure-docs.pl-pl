---
title: Usługi Azure information składników systemu i granice
description: Ten artykuł zawiera ogólny opis architektury Microsoft Azure i zarządzania.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: b390dc9bd2b690837a85a5bab361a534b9c9d5a5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60587210"
---
# <a name="azure-information-system-components-and-boundaries"></a>Usługi Azure information składników systemu i granice
Ten artykuł zawiera ogólny opis architektury platformy Azure i zarządzania. Środowisko systemu Azure składa się z następującymi sieciami:

- Sieci produkcyjnej Microsoft Azure (sieć platformy Azure)
- Sieci firmy Microsoft (corpnet)

Oddzielne zespoły IT są odpowiedzialne za operacje i konserwacja tych sieci.

## <a name="azure-architecture"></a>Architektura usługi Azure
Azure to obliczeniowa platforma i infrastrukturę na potrzeby tworzenia, wdrażania i zarządzania aplikacji i usług za pośrednictwem sieci centrów danych w chmurze. Firma Microsoft zarządza tymi centrami danych. Na podstawie liczby zasobów, które określisz, platforma Azure tworzy maszyny wirtualne (VM) oparte na potrzeby zasobów. Te maszyny wirtualne na funkcja hypervisor platformy Azure, który został zaprojektowany do użycia w chmurze i nie jest dostępny publicznie.

W każdym węźle serwera fizycznego systemu Azure jest funkcji hypervisor, która jest uruchamiana bezpośrednio nad sprzętem. Funkcja hypervisor dzieli węzeł na zmienną liczbę maszyn wirtualnych gościa. Każdy węzeł ma również jednego katalogu głównego maszyny Wirtualnej, który jest uruchamiany system operacyjny hosta. Zapora Windows jest włączona na każdej maszynie Wirtualnej. Można zdefiniować, które porty są mogą być adresowane przez skonfigurowanie pliku definicji usługi. Te porty są jedynymi osobami otwarte i mogą być adresowane, wewnętrznie lub zewnętrznie. Całego ruchu i dostęp do dysku i sieci są przenoszone przez funkcji hypervisor i katalog główny system operacyjny.

W warstwie hosta maszyn wirtualnych platformy Azure z wersją dostosowanego i ze wzmocnionymi zabezpieczeniami najnowszych systemu Windows Server. Platforma Azure używa wersji systemu Windows Server, która zawiera tylko te składniki, które są niezbędne do hostów maszyn wirtualnych. To zwiększa wydajność i zmniejsza obszar narażony na ataki. Granice maszyny są wymuszane przez funkcję hypervisor, które nie są zależne od zabezpieczeń systemu operacyjnego.

### <a name="azure-management-by-fabric-controllers"></a>Zarządzania platformy Azure przez administratorów sieci szkieletowej

Na platformie Azure maszyny wirtualne uruchomione na serwerach fizycznych (bloki/węzły) są grupowane w klastrach około 1000. Maszyny wirtualne niezależnie są zarządzane przez platformę skalowanych w poziomie i nadmiarowe składnik oprogramowania o nazwie Kontroler sieci szkieletowej (FC).

Każdy FC Zarządzanie cyklem życia aplikacji uruchomionych w klastrze i przepisy i monitoruje kondycję sprzętu pod jego kontrolą. Jest ono uruchamiane autonomicznego operacje, takie jak reincarnating wystąpień maszyn wirtualnych na serwerach w dobrej kondycji, gdy ustali, że serwer nie powiodło się. FC wykonuje również operacje zarządzania aplikacjami, takie jak wdrażanie, aktualizowanie i skalowanie aplikacji.

Centrum danych jest podzielony na klastry. Klastry izolowania błędów na poziomie FC i zapobiec niektórych rodzajów błędów serwerów poza klastrem, w którym występują. FCs, który służyć w określonym klastrze platformy Azure są zgrupowane w klastrze FC.

### <a name="hardware-inventory"></a>Spis sprzętu

FC przygotowuje spisu platformy Azure, sieci i urządzeń w procesie ładowania początkowego konfiguracji. Wszystkie nowe urządzenia i składników sieciowych, wprowadzając środowiska platformy Azure środowiska produkcyjnego, należy wykonać proces ładowania początkowego konfiguracji. FC jest odpowiedzialny za zarządzanie całą spisem, wymienione w pliku konfiguracyjnym datacenter.xml.

### <a name="fc-managed-operating-system-images"></a>Obrazy systemu operacyjnego zarządzane FC

Zespół systemu operacyjnego zawiera obrazy w formularzu z wirtualnych dysków twardych, wdrożone na wszystkich maszynach wirtualnych hosta i gościa w środowisku produkcyjnym platformy Azure. Zespół tworzy te obrazy podstawowe proces automatycznej kompilacji w trybie offline. Obraz podstawowy jest wersja systemu operacyjnego, w którym jądro i inne podstawowe składniki zostały zmodyfikowane i zoptymalizowana pod kątem obsługi środowiska platformy Azure.

Istnieją trzy typy obrazów systemu operacyjnego zarządzane w sieci szkieletowej:

- Host: Dostosowane systemu operacyjnego uruchomionego na hoście maszyn wirtualnych.
- Natywne: Natywne systemu operacyjnego uruchamianego na dzierżawy (na przykład Azure Storage). Ten system operacyjny nie ma żadnych funkcji hypervisor.
- Guest: System operacyjny gościa, które jest uruchamiane na maszynach wirtualnych gościa.

Host i systemy operacyjne zarządzanych FC natywnych są skonstruowane do użycia w chmurze, a nie są publicznie dostępne.

#### <a name="host-and-native-operating-systems"></a>Hosta i natywnego systemów operacyjnych

Hosta i natywnego są obrazy systemu operacyjnego ze wzmocnionymi zabezpieczeniami, które hosta agentów sieci szkieletowej i są uruchamiane w węźle obliczeniowym (działa jako pierwsza maszyna wirtualna na węźle) i węzły magazynu. Zaletą używania zoptymalizowane obrazy podstawowe, hosta i natywnego jest zmniejszenie obszaru powierzchni udostępnianych przez interfejsy API lub nieużywane składniki. One może stanowić zagrożenie bezpieczeństwa wysoki i zwiększyć zasięgowi systemu operacyjnego. Systemy operacyjne ograniczonych zawierają tylko składniki potrzebne do platformy Azure.

#### <a name="guest-operating-system"></a>System operacyjny gościa

Wewnętrzne składniki platformy Azure uruchomione na maszynach wirtualnych systemu operacyjnego gościa, ma możliwość uruchamiania protokołu Remote Desktop Protocol. Wszelkie zmiany ustawień konfiguracji punktu odniesienia, należy przejść przez zmianę i zarządzania procesu tworzenia wersji.

## <a name="azure-datacenters"></a>Centra danych platformy Azure
Zespół infrastruktury chmury firmy Microsoft i operacje (MCIO) zarządza fizycznych urządzeń infrastruktury i centrum danych dla wszystkich usług Microsoft online services. MCIO głównie odpowiada za zarządzanie fizyczna i środowiska kontrolek w obrębie centrów danych, jak również zarządzania nimi i oferowania zewnętrzne obwodowej urządzeń sieciowych (takich jak routery centrum danych i routerami granicznymi). MCIO jest również odpowiedzialny za konfigurowanie sprzętu kompletnego serwera minimalną na regałów w centrum danych. Klienci mają bez bezpośredniej interakcji z platformą Azure.

## <a name="service-management-and-service-teams"></a>Zarządzanie usługami i zespoły usług
Różnych grup inżynieryjnych, znane jako zespoły usług zarządzania pomocy technicznej usługi Azure. Każdy zespół usługi jest odpowiedzialny za obszar pomocy technicznej dla platformy Azure. Każdy zespół usługi musi zapewnić inżynier dostępny 24 x 7, aby zbadać i rozwiązać błędy w usłudze. Zespoły usług, domyślnie ma fizyczny dostęp do sprzętu, działające na platformie Azure.

Zespoły usług są:

- Platforma aplikacji
- Usługa Azure Active Directory
- Azure Compute
- Azure Net
- Usługi inżynieryjne chmury
- ISSD: Bezpieczeństwo
- Uwierzytelnianie wieloskładnikowe
- SQL Database
- Magazyn

## <a name="types-of-users"></a>Typy użytkowników
Pracownicy (lub wykonawcy) firmy Microsoft są uznawane za użytkowników wewnętrznych. Wszyscy pozostali użytkownicy są uznawane za użytkowników zewnętrznych. Wszystkie użytkownicy wewnętrzni platformy Azure ma ich stanu skategoryzowane przy użyciu poziomu ważności, który definiuje ich dostępu do danych klienta (dostępu lub Brak dostępu). W poniższej tabeli opisano uprawnienia użytkownika na platformie Azure (autoryzacji uprawnienia po uwierzytelnianie odbywa się):

| Rola | Wewnętrzne lub zewnętrzne | Poziom ważności | Uprawnienia autoryzowanego i funkcje realizowane | Typ dostępu
| --- | --- | --- | --- | --- |
| Inżynier ds. centrów danych platformy Azure | Wewnętrzny | Brak dostępu do danych klienta | Zarządzaj fizyczne zabezpieczenia lokalnego. Należy przeprowadzić patroli i centrum danych i monitorowania wszystkich punktów wejścia. Pracownicy konwojowanie do i z centrum danych pewnych-wyczyszczone, którzy zapewniają ogólne usługi (na przykład jadalni lub czyszczenie) lub IT pracę w centrum danych. Należy przeprowadzić procedury monitorowania i konserwacji sprzętu sieciowego. Wykonaj pracy zarządzania i naprawa zdarzenia przy użyciu różnych narzędzi. Należy przeprowadzić procedury monitorowania i konserwacji sprzętu fizycznego, w centrach danych. Dostęp do środowiska na żądanie z właścicieli nieruchomości. Możliwe do wykonania śledczej dochodzeń, zaloguj się raporty zdarzeń i wymagają wymagania dotyczące szkoleń i zasad obowiązkowych zabezpieczeń usługi. Własność operacyjną i obsługi narzędzi krytycznych, takich jak skanery i zbieranie danych dziennika. | Trwały dostęp do środowiska. |
| Klasyfikacja zdarzeń platformy Azure (inżynierów błyskawicznej) | Wewnętrzny | Dostęp do danych klienta | Zarządzaj komunikacją między MCIO, pomocy technicznej i zespoły inżynierów. Klasyfikacja platformy zdarzenia, problemy z wdrażaniem i żądania obsługi. | Dostęp just in time w środowisku o ograniczonym dostępie trwałego systemów nienależące do klientów. |
| Inżynierowie wdrażania na platformie Azure | Wewnętrzny | Dostęp do danych klienta | Wdrażanie i uaktualnianie składniki platformy, oprogramowania i zmian w konfiguracji zaplanowane w odniesieniu do platformy Azure. | Dostęp just in time w środowisku o ograniczonym dostępie trwałego systemów nienależące do klientów. |
| Obsługa awarii klientów platformy Azure (dzierżawcy) | Wewnętrzny | Dostęp do danych klienta | Debugowanie i diagnozowanie błędów i awarii platformy dla dzierżaw poszczególnych obliczeń i kont platformy Azure. Analizowanie błędów. Dysk krytycznych poprawek platformy lub klientowi i korzystaj z pomocy technicznej ulepszeń technicznych. | Dostęp just in time w środowisku o ograniczonym dostępie trwałego systemów nienależące do klientów. |
| Azure inżynierów (monitorowanie engineers) i zdarzenia | Wewnętrzny | Dostęp do danych klienta | Zdiagnozować i rozwiązać kondycji platformy za pomocą narzędzi diagnostycznych. Poprawki dla sterowników woluminów dysków, naprawy elementów wynikających z awarii i pomóc akcje odzyskiwania awarii. | Dostęp just in time w środowisku o ograniczonym dostępie trwałego systemów nienależące do klientów. |
|Klienci platformy Azure | Zewnętrzna | ND | ND | ND |

Platforma Azure używa unikatowych identyfikatorów do uwierzytelniania użytkowników w organizacji i klientów (lub procesów działających w imieniu użytkowników w organizacji). Dotyczy to wszystkich zasobów i urządzeń, które są częścią środowiska platformy Azure.

### <a name="azure-internal-authentication"></a>Uwierzytelnianie wewnętrzne platformy Azure

Komunikacja między składnikami wewnętrznych platformy Azure są chronione przy użyciu szyfrowania TLS. W większości przypadków podpisem własnym to certyfikaty X.509. Certyfikatów za pomocą połączeń, które są dostępne z spoza sieci platformy Azure są wyjątek, to certyfikaty dla FCs. FCs mają certyfikaty wystawione przez Microsoft Certificate z urzędu certyfikacji, która jest wspierana przez zaufany główny urząd certyfikacji. Dzięki temu klucze publiczne FC być przewijane w prosty sposób. Ponadto narzędziach deweloperskich firmy Microsoft za pomocą kluczy publicznych FC. Deweloperzy przesłać nowe obrazy aplikacji, obrazy będą szyfrowane przy użyciu klucza publicznego FC w celu ochrony wszystkich osadzonych danych poufnych.

### <a name="azure-hardware-device-authentication"></a>Uwierzytelnianie urządzeń sprzętowych platformy Azure

FC obsługuje zestaw poświadczeń (klucze i/lub hasła) pozwala uwierzytelniać się do różnych urządzeń sprzętowych pod jego kontrolą. Firma Microsoft używa systemu, aby uniemożliwić dostęp do tych poświadczeń. W szczególności transportu, trwałość i korzystanie z tych poświadczeń podsłuchem deweloperów platformy Azure, administratorów i tworzenia kopii zapasowej usług i personelu dostęp do informacji poufnych, poufne lub prywatne.

Firma Microsoft wykorzystuje szyfrowanie oparte na klucz publiczny tożsamości wzorca FC. Dzieje się w instalacji FC i FC ponownej konfiguracji razy, aby przenieść poświadczenia używane do dostępu do sieci urządzeń sprzętowych. Gdy FC wymaga poświadczeń, FC pobiera i odszyfrowuje je.

### <a name="network-devices"></a>Urządzenia sieciowe

Zespół sieci platformy Azure umożliwia skonfigurowanie konta usług sieciowych, aby włączyć klienta platformy Azure do uwierzytelniania urządzeń sieciowych (routery, przełączniki i moduły równoważenia obciążenia).

## <a name="secure-service-administration"></a>Zabezpieczanie administrowania usługą
Personel operacji na platformie Azure są wymagane do użycia bezpieczne administracyjne stacje robocze (z stacje SAW). Klienci mogą zaimplementować podobne formantów, korzystając z stacje robocze dostępu uprzywilejowanego. Z stacje SAW członkowie personelu administracyjnego Użyj indywidualnie przypisane konto administracyjne, który jest oddzielony od konta użytkownika standardowego. SAW opiera się na tej praktyce rozdzielenia kont, zapewniając wiarygodną stację roboczą dla kont poufnych.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się, co firma Microsoft używa do zabezpieczania infrastruktury platformy Azure, zobacz:

- [Urządzenia platformy Azure, lokalnie i zabezpieczenia fizyczne](azure-physical-security.md)
- [Dostępność infrastruktury platformy Azure](azure-infrastructure-availability.md)
- [Architektura sieci platformy Azure](azure-infrastructure-network.md)
- [Sieć platformy Azure środowiska produkcyjnego](azure-production-network.md)
- [Funkcje zabezpieczeń w usłudze Azure SQL Database](azure-infrastructure-sql.md)
- [Operacje platformy Azure środowiska produkcyjnego i zarządzanie](azure-infrastructure-operations.md)
- [Monitorowanie infrastruktury platformy Azure](azure-infrastructure-monitoring.md)
- [Integralność infrastruktury platformy Azure](azure-infrastructure-integrity.md)
- [Ochrona danych klientów platformy Azure](azure-protection-of-customer-data.md)
