---
title: Składniki systemu Azure informacji i granice
description: Ten artykuł zawiera ogólny opis architektury Microsoft Azure i zarządzania.
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
ms.openlocfilehash: 8db1dce5fcc56c229d1fdd746bafbd2fae2c9bad
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102542"
---
# <a name="azure-information-system-components-and-boundaries"></a>Składniki systemu Azure informacji i granice
Ten artykuł zawiera ogólny opis architektury Microsoft Azure i zarządzania. Środowisko systemu Azure składa się z następującymi sieciami:

- Microsoft Azure środowiska produkcyjnego (sieć platformy Azure)
- Microsoft Corporate sieci (Corpnet)

Oddzielne zespoły IT jest odpowiedzialny za operacje i obsługa sieci platformy Azure i sieci CorpNet.

## <a name="azure-architecture"></a>Architektura platformy Azure
Microsoft Azure to chmury obliczeniowej platformy i infrastrukturę do tworzenia, wdrażania i zarządzanie aplikacji i usług za pośrednictwem sieci centrów danych zarządzanych firmy Microsoft. Na podstawie liczby zasobów, określony przez klientów, platforma Azure tworzy maszyny wirtualne, w zależności od potrzeb zasobów. Te maszyny wirtualne, uruchom przy użyciu programu Microsoft Azure funkcji Hypervisor, który jest przeznaczony dla użycia w chmurze i nie jest dostępny publicznie.

W każdym węźle Azure serwera fizycznego Brak funkcji Hypervisor, która uruchamia bezpośrednio przez sprzęt. Funkcja hypervisor dzieli węzła zmienną liczbę maszyn wirtualnych gościa (maszyny wirtualne). Każdy węzeł ma również jeden specjalne "Root" maszyny Wirtualnej, w którym jest uruchomiona systemu operacyjnego hosta. Zapora systemu Windows jest włączona na każdej maszynie Wirtualnej. Tylko porty otwarte i mogą być adresowane, wewnętrznie i zewnętrznie, są porty jawnie zdefiniowane w pliku definicji usługi skonfigurowane przez klienta. Całego ruchu i dostęp do dysku i sieci jest przenoszone przez przez funkcję Hypervisor i głównego systemu operacyjnego.

W warstwie hosta maszyn wirtualnych platformy Azure z wersją dostosowane i ze wzmocnionymi zabezpieczeniami najnowszych systemu Windows Server. Microsoft Azure wykorzystuje stripped-down wersję systemu Windows Server, zawierający tylko te składniki, które są niezbędne do hostów maszyn wirtualnych. Odbywa się do zwiększenia wydajności i zmniejszyć obszar ataków. Granice komputera są wymuszane przez funkcję Hypervisor, która nie jest zależny od zabezpieczeń systemu operacyjnego.

**Zarządzania platformy Azure przez administratorów sieci szkieletowej (FCs)**: W Azure, maszyn wirtualnych działających na serwerach fizycznych (bloki/węzłami) są zgrupowane w "klastry" około 1000. Składnik oprogramowania skalowalnych w poziomie oraz nadmiarowe platformy o nazwie FC niezależnie zarządza maszyn wirtualnych.

Każdy FC umożliwia zarządzanie cyklem życia aplikacji uruchomionych w klastrze oraz postanowienia i monitoruje kondycję sprzętu pod jej kontrolą. Wykonuje on zarówno autonomiczne operacji, takich jak reincarnating wystąpień maszyn wirtualnych w dobrej kondycji serwerów, gdy ustali, że serwer nie powiodło się. FC wykonuje również operacje zarządzania aplikacjami, takie jak wdrażanie, uaktualnianie i skalowanie aplikacji.

Centrum danych jest podzielona na klastrach. Klastry wyizolować błędy na poziomie FC i uniemożliwić niektórych klas błędy wpływu na serwery poza klastrem, w którym występują. FCs służących określonym klastrze platformy Azure są zgrupowane w klastrze FC.

**Spis sprzętu**: spis Azure urządzeń sprzętowych i sieciowych jest przygotowany w procesie inicjowania konfiguracji i rejestrowane w pliku konfiguracji datacenter.xml. Wszystkie nowe urządzenia i składniki sieciowe wprowadzania środowiska Azure środowiska produkcyjnego, wykonaj proces inicjowania konfiguracji. FC jest odpowiedzialny za zarządzanie całego spisu wymienione w pliku konfiguracyjnym datacenter.xml.

**Zarządzane FC OS**: team system operacyjny udostępnia obrazów systemu operacyjnego w formularzu z wirtualnych dysków twardych (VHD), które zostały wdrożone na wszystkich hostów i maszyny wirtualne gości w środowisku produkcyjnym Azure. Team system operacyjny tworzy, te "Obrazy Base" za pośrednictwem procesu automatycznego kompilacji w trybie offline. Obraz podstawowy jest wersja systemu operacyjnego, w którym jądra i inne składniki podstawowe zostały zmodyfikowane i zoptymalizowane pod kątem obsługi środowiska platformy Azure.

Istnieją trzy typy obrazów zarządzane w sieci szkieletowej systemu operacyjnego:

- System operacyjny hosta — systemu operacyjnego hosta jest dostosowane systemu operacyjnego, który działa na hoście maszyn wirtualnych
- Macierzysty systemu operacyjnego — natywnego systemu operacyjnego uruchomionego w dzierżawców (na przykład usługi Azure Storage) niezawierający dowolnej funkcji Hypervisor
- System operacyjny gościa — systemu operacyjnego gościa, który działa na maszynach wirtualnych gościa

Host i zarządzane przez macierzystego FC systemów operacyjnych są przeznaczone do użycia w chmurze i nie są publicznie.

**Hosta i natywnego systemu operacyjnego**: systemu operacyjnego hosta i natywnego systemu operacyjnego są obrazy systemu operacyjnego ze wzmocnionymi zabezpieczeniami obsługujących agentów sieci szkieletowej (FA) i uruchom na węźle obliczeń (działa jako pierwsza maszyna wirtualna na węźle) i węzłów magazynu. Korzyści wynikające ze stosowania zoptymalizowany Base obrazy hosta i natywnego systemu operacyjnego jest zmniejsza powierzchni udostępnianych przez interfejsy API lub nieużywane składniki, które stanowią wysokiej zagrożenia bezpieczeństwa i zwiększyć rozmiaru systemu operacyjnego. Te systemy operacyjne mniejsze zużycie zawierają tylko składniki niezbędne do platformy Azure. Zwiększa wydajność i zmniejsza możliwości ataku.

**System operacyjny gościa**: Azure wewnętrznych składników działające na maszynach wirtualnych systemu operacyjnego gościa ma możliwość uruchamiania protokołu RDP (Remote Desktop), w odróżnieniu od klientów zewnętrznych. Wszelkie zmiany ustawień konfiguracji w linii bazowej będzie potrzebować go za pośrednictwem zmiany i wersji procesu zarządzania.

## <a name="azure-datacenters"></a>Centra danych platformy Azure
Zespół infrastruktury chmury firmy Microsoft i operacji (MCIO) zarządza firmy Microsoft fizycznej infrastruktury centrum danych urządzenia i wszystkich usług Microsoft online Services. MCIO jest głównie odpowiedzialny za zarządzanie fizyczne i środowiskowe formantów w centrach danych, a także zarządzanie i obsługuje urządzenia sieci obwodowej zewnętrzne (routery brzegowe i routery centrum danych). MCIO również jest odpowiedzialna za skonfigurowanie sprzętu bez systemu operacyjnego serwera minimalną na stojakami w centrum danych. Klienci mają bezpośredniej interakcji z platformy Azure.

## <a name="service-management--service-teams"></a>Zarządzanie usługami i zespoły usługi
Obsługa usługi Azure jest zarządzany przez wiele grup engineering znana jako "Zespoły usługi". Każdy zespoły obsługi jest odpowiedzialny za obszar wsparcia dla platformy Azure. Każdy zespół usługi należy inżyniera dostępne 24 x 7 do zbadania i rozwiązania błędów w usłudze. Zespoły obsługi, domyślnie ma dostęp fizyczny do sprzętowych działających na platformie Azure.

Zespoły obsługi są:

- Platforma aplikacji
- Usługa Azure Active Directory
- Azure Compute
- Sieć platformy Azure
- Usługi w chmurze zespołu inżynieryjnego
- ISSD: zabezpieczeń
- Uwierzytelnianie wieloskładnikowe
- SQL Database
- Magazyn

## <a name="types-of-users"></a>Typy użytkowników
Wszyscy użytkownicy wewnętrzny Azure mają ich stanu skategoryzowane charakterystyki, który definiuje ich dostęp do danych klientów (dostępu lub Brak dostępu). Pracownicy (lub wykonawców) firmy Microsoft są traktowane jako użytkowników wewnętrznych. Wszyscy użytkownicy są traktowane jako użytkowników zewnętrznych. W poniższej tabeli opisano uprawnienia użytkownika do platformy Azure (autoryzacji uprawnienia po uwierzytelnianie odbywa się):

| Rola | Wewnętrzne lub zewnętrzne | Charakterystyki | Autoryzowany uprawnienia i funkcje realizowane | Typ dostępu
| --- | --- | --- | --- | --- |
| Inżynier centrum danych Azure | Wewnętrzny | Brak dostępu do danych klienta | Zarządzanie zabezpieczenia fizyczne lokalnym; Należy przeprowadzić patroli i centrum danych i monitorowania wszystkich punktów wejścia; Wykonaj konwojowanie usług do i z personelu Centrum danych dla niektórych z systemem innym niż wyczyszczone, które świadczą usługi ogólne (jadalni, czyszczenie) lub pracy IT w centrum danych; Przeprowadzenie procedur monitorowania oraz konserwacji sprzętu sieciowego; Zarządzanie zdarzeniami i naprawa w razie awarii pracy przy użyciu różnych narzędzi; Przeprowadzenie procedur monitorowania i konserwacji sprzętu fizycznego w centrach danych; Dostęp do środowiska na żądanie z właścicieli. Do wykonania śledczej dochodzeń, rejestrowanie zdarzeń raportu i wymagają szkoleń obowiązkowych zabezpieczeń i wymagania dotyczące zasad; Własność operacyjne i konserwacja krytycznych narzędzi, takich jak skanery i zbierania dzienników. | Trwały dostęp do środowiska |
| Klasyfikacja zdarzenia platformy Microsoft Azure (szybkie odpowiedzi Engineers) | Wewnętrzny | Dostęp do danych klienta | Zarządzania komunikacją między zespołami operacje infrastruktury, pomocy technicznej i Azure Engineering; Klasyfikowanie platformy zdarzenia, problemy z wdrażaniem i żądania obsługi. | Na wszelki dostęp do środowiska - o ograniczonym dostępie trwałe systemów innych niż klienta |
| Inżynierowie wdrożenia platformy Microsoft Azure | Wewnętrzny | Dostęp do danych klienta | Wykonaj wdrażania/uaktualniania składników platformy, oprogramowania i zmiany konfiguracji zaplanowane wesprzeć Microsoft Azure. | Na wszelki dostęp do środowiska - o ograniczonym dostępie trwałe systemów innych niż klienta |
| Awaria techniczną platformy Microsoft Azure (dzierżawcy) | Wewnętrzny | Dostęp do danych klienta | Debugowanie i diagnozowanie platformy awarie i błędy dzierżawcom obliczeniowe poszczególnych i kont Microsoft Azure; Analizowanie błędów i dysków krytycznych poprawek do platformy/klienta, dysku ulepszeń technicznych między pomocy technicznej. | Na wszelki dostęp do środowiska - o ograniczonym dostępie trwałe systemów innych niż klienta |
| Inżynierowie działającą witrynę Microsoft Azure (monitorowanie Engineers) & zdarzenia | Wewnętrzny | Dostęp do danych klienta | Odpowiedzialny za diagnozowanie i łagodzenia platformy kondycji za pomocą narzędzi diagnostycznych; Poprawki dla sterowników wolumin dysku, napraw elementów, w wyniku awarii i assist akcje odzyskiwania awarii. | Na wszelki dostęp do środowiska - o ograniczonym dostępie trwałe systemów innych niż klienta |
|Klienci usługi Microsoft Azure | Zewnętrzne | ND | ND | ND |

Azure używa unikatowych identyfikatorów do uwierzytelniania użytkowników w organizacji i klientów (lub procesów działających w imieniu użytkowników w organizacji) do wszystkich zasobów/urządzeń, które są częścią środowiska platformy Azure.

**Uwierzytelnianie wewnętrzne platformy Microsoft Azure**: komunikacji między składnikami wewnętrzny Azure są chronione przy użyciu szyfrowania TLS. W większości przypadków podpisem własnym to certyfikaty X.509. Wyłączenia dla certyfikatów przy użyciu połączenia, które mogą być używane z spoza sieci platformy Azure i programu. FCs mają certyfikaty wydane przez Microsoft Certificate z urzędu certyfikacji, który nie jest obsługiwana przez zaufany główny urząd certyfikacji. Dzięki temu FC klucze publiczne można łatwo przerzuceniem. Ponadto FC kluczy publicznych są używane przez narzędzi dla deweloperów, dzięki czemu podczas deweloperzy przesłać nowe obrazy aplikacji, są szyfrowane przy użyciu klucza publicznego FC w Aby chronić wszystkie osadzone kluczy tajnych.

**Uwierzytelnianie platformy Microsoft Azure sprzętu urządzeń**: FC obsługuje zestaw poświadczeń (klucze i/lub hasła) używanych do samodzielnego uwierzytelnienia różnych urządzeń sprzętowych pod jej kontrolą. System używany do transportu, przechowywanie i używanie tych poświadczeń zaprojektowano w celu uniknąć deweloperów platformy Azure, Administratorzy i usługi tworzenia kopii zapasowej/personel dostępu do informacji poufnych, poufne lub prywatnej.

Szyfrowanie oparte na tożsamości wzorca FC, klucz publiczny jest używana w instalacji FC i ponownej konfiguracji FC razy, aby przenieść poświadczenia umożliwiające dostęp do urządzeń sieciowych. Poświadczenia są pobierane i odszyfrować za pomocą FC, gdy musi je.

**Urządzenia sieciowe**: konta usługi sieci są skonfigurowane przez zespół sieci Azure, aby włączyć klienta Microsoft Azure do uwierzytelniania urządzeń sieciowych (routery, przełączniki i moduły równoważenia obciążenia).

## <a name="secure-service-administration"></a>Zabezpieczanie administracji usługi
Personel firmy Microsoft Azure muszą korzystać z bezpiecznej administracyjnej stacji roboczych (piły; klienci mogą wdrożyć podobnych formantów przy użyciu uprzywilejowanego dostępu do stacji roboczych lub łapy). Podejście PIŁA jest rozszerzeniem ustalonym zalecaną praktyką użyć oddzielnych administratora i konta użytkowników dla personelu administracyjnego. Takie rozwiązanie wykorzystuje konto administratora indywidualnie przypisane, która różni się od konta użytkowników standardowych. Zapewniając zaufanego stacji roboczej dla tych kont poufnych był WYŚWIETLANY kompilacji w praktyce separacji tego konta.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat firmy Microsoft jest zapewnienie infrastruktury platformy Azure, zobacz:

- [Urządzenia platformy Azure, lokalne i zabezpieczenia fizyczne](azure-physical-security.md)
- [Dostępność infrastruktury platformy Azure](azure-infrastructure-availability.md)
- [Architektura sieci platformy Azure](azure-infrastructure-network.md)
- [Sieci Azure środowiska produkcyjnego](azure-production-network.md)
- [Funkcje zabezpieczeń bazy danych SQL Azure firmy Microsoft](azure-infrastructure-sql.md)
- [Operacje Azure środowiska produkcyjnego i zarządzania](azure-infrastructure-operations.md)
- [Monitorowanie infrastruktury platformy Azure](azure-infrastructure-monitoring.md)
- [Integralność infrastruktury platformy Azure](azure-infrastructure-integrity.md)
- [Ochrona danych klienta na platformie Azure](azure-protection-of-customer-data.md)
