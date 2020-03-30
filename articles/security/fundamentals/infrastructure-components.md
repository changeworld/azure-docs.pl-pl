---
title: Składniki i granice systemu informacyjnego platformy Azure
description: Ten artykuł zawiera ogólny opis architektury i zarządzania platformy Microsoft Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 68535f70507e7a81d217f4148314a3d76ec832ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68727213"
---
# <a name="azure-information-system-components-and-boundaries"></a>Składniki i granice systemu informacyjnego platformy Azure
Ten artykuł zawiera ogólny opis architektury platformy Azure i zarządzania. Środowisko systemowe platformy Azure składa się z następujących sieci:

- Sieć produkcyjna platformy Microsoft Azure (sieć platformy Azure)
- Sieć firmowa firmy Microsoft (corpnet)

Oddzielne zespoły IT są odpowiedzialne za operacje i utrzymanie tych sieci.

## <a name="azure-architecture"></a>Architektura platformy Azure
Platforma Azure to platforma przetwarzania w chmurze i infrastruktura do tworzenia, wdrażania i zarządzania aplikacjami i usługami za pośrednictwem sieci centrów danych. Firma Microsoft zarządza tymi centrami danych. Na podstawie liczby zasobów, które określisz, platforma Azure tworzy maszyny wirtualne (maszyny wirtualne) na podstawie zapotrzebowania na zasoby. Te maszyny wirtualne są uruchamiane na hipernadzorcy platformy Azure, który jest przeznaczony do użytku w chmurze i nie jest dostępny publicznie.

W każdym węźle serwera fizycznego platformy Azure istnieje funkcja hypervisor, która działa bezpośrednio za pomocą sprzętu. Funkcja hypervisor dzieli węzeł na zmienną liczbę maszyn wirtualnych gościa. Każdy węzeł ma również jedną główną maszynę wirtualną, która uruchamia system operacyjny hosta. Zapora systemu Windows jest włączona na każdej maszynie wirtualnej. Można zdefiniować, które porty są adresowalne, konfigurując plik definicji usługi. Porty te są jedynymi otwartymi i adresowalnymi, wewnętrznie lub zewnętrznie. Cały ruch i dostęp do dysku i sieci jest pośredniczy przez hipernadzorcy i główny system operacyjny.

W warstwie hosta maszyny wirtualne platformy Azure uruchamiają dostosowaną i wzmocnioną wersję najnowszego systemu Windows Server. Platforma Azure używa wersji systemu Windows Server, która zawiera tylko te składniki niezbędne do obsługi maszyn wirtualnych. Zwiększa to wydajność i zmniejsza powierzchnię ataku. Granice komputera są wymuszane przez hipernadzorcę, co nie zależy od zabezpieczeń systemu operacyjnego.

### <a name="azure-management-by-fabric-controllers"></a>Zarządzanie platformą Azure przez kontrolery sieci szkieletowej

Na platformie Azure maszyny wirtualne działające na serwerach fizycznych (bloków/węzłów) są pogrupowane w klastry o około 1000. Maszyny wirtualne są niezależnie zarządzane przez skalowany w poziomie i nadmiarowy składnik oprogramowania platformy o nazwie kontroler sieci szkieletowej (FC).

Każdy FC zarządza cyklem życia aplikacji uruchomionych w jego klastrze i aprowekuje i monitoruje kondycję sprzętu pod jego kontrolą. Uruchamia operacje autonomiczne, takie jak ponowne wskrzeszenie wystąpień maszyn wirtualnych na serwerach w dobrej kondycji, gdy stwierdzi, że serwer nie powiódł się. FC wykonuje również operacje zarządzania aplikacjami, takie jak wdrażanie, aktualizowanie i skalowanie w poziomie aplikacji.

Centrum danych jest podzielone na klastry. Klastry izolują błędy na poziomie FC i zapobiegają wpływaniu niektórych klas błędów na serwery poza klastrem, w którym występują. Kontrolery domeny obsługujące określony klaster platformy Azure są grupowane w klastrze FC.

### <a name="hardware-inventory"></a>Spis sprzętu

FC przygotowuje spis sprzętu platformy Azure i urządzeń sieciowych podczas procesu konfiguracji bootstrap. Wszystkie nowe składniki sprzętu i sieci wchodzące w środowisko produkcyjne platformy Azure muszą być zgodne z procesem konfiguracji bootstrap. Fc jest odpowiedzialny za zarządzanie całym zapasem wymienionym w pliku konfiguracyjnym datacenter.xml.

### <a name="fc-managed-operating-system-images"></a>Obrazy systemu operacyjnego zarządzanego przez FC

Zespół systemu operacyjnego udostępnia obrazy w postaci wirtualnych dysków twardych, wdrożone na wszystkich maszynach wirtualnych hosta i gościa w środowisku produkcyjnym platformy Azure. Zespół konstruuje te obrazy podstawowe za pomocą zautomatyzowanego procesu kompilacji w trybie offline. Obraz podstawowy jest wersją systemu operacyjnego, w której jądro i inne podstawowe składniki zostały zmodyfikowane i zoptymalizowane pod kątem obsługi środowiska platformy Azure.

Istnieją trzy typy obrazów systemu operacyjnego zarządzanego przez sieci szkieletowe:

- Host: dostosowany system operacyjny, który działa na maszynach wirtualnych hosta.
- Natywny: natywny system operacyjny, który działa na dzierżawców (na przykład usługi Azure Storage). Ten system operacyjny nie ma żadnego hypervisor.
- Gość: system operacyjny gościa, który działa na maszynach wirtualnych gościa.

Host i natywne systemy operacyjne zarządzane przez FC są przeznaczone do użytku w chmurze i nie są publicznie dostępne.

#### <a name="host-and-native-operating-systems"></a>Host i natywne systemy operacyjne

Host i natywne są wzmocnione obrazy systemu operacyjnego, które obsługują agentów sieci szkieletowej i uruchomić na węźle obliczeniowym (działa jako pierwsza maszyna wirtualna w węźle) i węzłów magazynu. Zaletą używania zoptymalizowanych obrazów bazowych hosta i natywnych jest to, że zmniejsza powierzchnię narażoną przez interfejsy API lub nieużywane składniki. Mogą one stwarzać wysokie zagrożenia dla bezpieczeństwa i zwiększać zasięg systemu operacyjnego. Systemy operacyjne o zmniejszonym rozmiarze obejmują tylko składniki niezbędne do platformy Azure.

#### <a name="guest-operating-system"></a>System operacyjny gościa

Wewnętrzne składniki platformy Azure uruchomione na maszynach wirtualnych systemu operacyjnego gościa nie mają możliwości uruchamiania protokołu pulpitu zdalnego. Wszelkie zmiany w ustawieniach konfiguracji linii bazowej muszą przejść przez proces zarządzania zmianami i wydaniami.

## <a name="azure-datacenters"></a>Centra danych platformy Azure
Zespół Microsoft Cloud Infrastructure and Operations (MCIO) zarządza infrastrukturą fizyczną i urządzeniami centrów danych dla wszystkich usług online firmy Microsoft. MCIO jest przede wszystkim odpowiedzialny za zarządzanie fizycznymi i środowiskowymi kontrolami w centrach danych, a także za zarządzanie zewnętrznymi urządzeniami sieciowymi obwodowymi (takimi jak routery brzegowe i routery centrów danych). McIO jest również odpowiedzialny za konfigurowanie gołego minimalnego sprzętu serwera na stojakach w centrum danych. Klienci nie mają bezpośredniej interakcji z platformą Azure.

## <a name="service-management-and-service-teams"></a>Zespoły zarządzania serwisem i serwisowe
Różne grupy inżynierów, znane jako zespoły usług, zarządzają pomocą usługi platformy Azure. Każdy zespół serwisowy jest odpowiedzialny za obszar pomocy technicznej dla platformy Azure. Każdy zespół serwisowy musi udostępnić inżyniera 24x7, aby zbadać i rozwiązać błędy w usłudze. Zespoły usług domyślnie nie mają fizycznego dostępu do sprzętu działającego na platformie Azure.

Zespoły serwisowe to:

- Platforma aplikacji
- Usługa Azure Active Directory
- Azure Compute
- Sieć platformy Azure
- Usługi inżynierii w chmurze
- ISSD: Bezpieczeństwo
- Uwierzytelnianie wieloskładnikowe
- Baza danych SQL
- Magazyn

## <a name="types-of-users"></a>Typy użytkowników
Pracownicy (lub wykonawcy) firmy Microsoft są uważani za użytkowników wewnętrznych. Wszyscy inni użytkownicy są uważani za użytkowników zewnętrznych. Wszyscy użytkownicy wewnętrzni platformy Azure mają status pracownika sklasyfikowany na poziomie czułości, który definiuje ich dostęp do danych klienta (dostęp lub brak dostępu). Uprawnienia użytkownika do platformy Azure (uprawnienie autoryzacji po uwierzytelnieniu) są opisane w poniższej tabeli:

| Rola | Wewnętrzne lub zewnętrzne | Poziom czułości | Autoryzowane uprawnienia i funkcje wykonywane | Typ dostępu
| --- | --- | --- | --- | --- |
| Inżynier centrum danych platformy Azure | Wewnętrzny | Brak dostępu do danych klienta | Zarządzanie fizycznym bezpieczeństwem pomieszczeń. Przeprowadzaj patrole w centrum danych i poza centrum danych oraz monitoruj wszystkie punkty wejścia. Eskortuj do i z centrum danych niektórych nierozliczonych pracowników, którzy świadczą ogólne usługi (takie jak jadalnia lub czyszczenie) lub it działają w centrum danych. Przeprowadzaj rutynowe monitorowanie i konserwację sprzętu sieciowego. Wykonuj zarządzanie zdarzeniami i pracę z poprawkami, korzystając z różnych narzędzi. Przeprowadzaj rutynowe monitorowanie i konserwację sprzętu fizycznego w centrach danych. Dostęp do środowiska na żądanie od właścicieli nieruchomości. Możliwość przeprowadzania dochodzeń kryminalistycznych, rejestrowania raportów o zdarzeniach i wymaga obowiązkowego szkolenia w zakresie zabezpieczeń i wymagań dotyczących zasad. Własność operacyjna i konserwacja krytycznych narzędzi zabezpieczeń, takich jak skanery i zbieranie dzienników. | Trwały dostęp do środowiska. |
| Triage zdarzeń platformy Azure (inżynierowie szybkiego reagowania) | Wewnętrzny | Dostęp do danych klientów | Zarządzaj komunikacją między zespołami MCIO, wsparciem i inżynierami. Zdarzenia platformy triage, problemy z wdrażaniem i żądania usług. | Dostęp just-in-time do środowiska, z ograniczonym trwałym dostępem do systemów niebędących klientami. |
| Inżynierowie wdrażania platformy Azure | Wewnętrzny | Dostęp do danych klientów | Wdrażanie i uaktualnianie składników platformy, oprogramowania i zaplanowanych zmian konfiguracji w pomocy technicznej platformy Azure. | Dostęp just-in-time do środowiska, z ograniczonym trwałym dostępem do systemów niebędących klientami. |
| Obsługa awarii klienta platformy Azure (dzierżawa) | Wewnętrzny | Dostęp do danych klientów | Debugowanie i diagnozowanie awarii platformy i błędów dla poszczególnych dzierżaw obliczeniowych i kont platformy Azure. Analizowanie usterek. Napęd krytycznych poprawek do platformy lub klienta, i napęd ulepszeń technicznych w całej pomocy technicznej. | Dostęp just-in-time do środowiska, z ograniczonym trwałym dostępem do systemów niebędących klientami. |
| Inżynierowie witryny azure live (inżynierowie monitorowania) i incydenty | Wewnętrzny | Dostęp do danych klientów | Diagnozowanie i łagodzenie kondycji platformy przy użyciu narzędzi diagnostycznych. Poprawki dysków dla sterowników woluminów, naprawiają elementy wynikające z awarii i pomagają w przywracaniu awarii. | Dostęp just-in-time do środowiska, z ograniczonym trwałym dostępem do systemów niebędących klientami. |
|Klienci platformy Azure | Zewnętrzna | Nie dotyczy | Nie dotyczy | Nie dotyczy |

Platforma Azure używa unikatowych identyfikatorów do uwierzytelniania użytkowników i klientów organizacji (lub procesów działających w imieniu użytkowników organizacji). Dotyczy to wszystkich zasobów i urządzeń, które są częścią środowiska platformy Azure.

### <a name="azure-internal-authentication"></a>Uwierzytelnianie wewnętrzne platformy Azure

Komunikacja między wewnętrznymi składnikami platformy Azure jest chroniona za pomocą szyfrowania TLS. W większości przypadków certyfikaty X.509 są podpisywane samodzielnie. Certyfikaty z połączeniami, do których można uzyskać dostęp spoza sieci platformy Azure, są wyjątkiem, podobnie jak certyfikaty dla kontrolerów domeny. Kontrolery domeny mają certyfikaty wystawione przez certyfikat urzędu firmy Microsoft (CA), który jest wspierany przez zaufany główny urząd certyfikacji. Dzięki temu klucze publiczne FC mają być przerzucone łatwo. Ponadto narzędzia deweloperskie firmy Microsoft używają kluczy publicznych FC. Gdy deweloperzy przesyłają nowe obrazy aplikacji, obrazy są szyfrowane za pomocą klucza publicznego FC w celu ochrony wszelkich osadzonych wpisów tajnych.

### <a name="azure-hardware-device-authentication"></a>Uwierzytelnianie urządzeń sprzętowych platformy Azure

FC przechowuje zestaw poświadczeń (klucze i/lub hasła) używane do uwierzytelniania się do różnych urządzeń sprzętowych pod jego kontrolą. Firma Microsoft używa systemu, aby uniemożliwić dostęp do tych poświadczeń. W szczególności transport, trwałość i korzystanie z tych poświadczeń ma na celu uniemożliwienie deweloperom platformy Azure, administratorom i usługom tworzenia kopii zapasowych i pracownikom dostępu do poufnych, poufnych lub prywatnych informacji.

Firma Microsoft używa szyfrowania opartego na głównym kluczu tożsamości FC. Dzieje się tak w czasie konfiguracji FC i ponownej konfiguracji FC, aby przenieść poświadczenia używane do uzyskiwania dostępu do urządzeń sprzętowych sieciowych. Gdy FC potrzebuje poświadczeń, FC pobiera i odszyfrowuje je.

### <a name="network-devices"></a>Urządzenia sieciowe

Zespół sieci platformy Azure konfiguruje konta usługi sieciowej, aby umożliwić klientowi platformy Azure uwierzytelnianie na urządzeniach sieciowych (routerach, przełącznikach i modułach równoważenia obciążenia).

## <a name="secure-service-administration"></a>Bezpieczne administrowanie usługami
Personel operacyjny platformy Azure są wymagane do korzystania z bezpiecznych stacji roboczych administratora (SAWs). Klienci mogą zaimplementować podobne formanty przy użyciu stacji roboczych dostępu uprzywilejowanego. W przypadku pakietów SA personel administracyjny używa indywidualnie przypisanego konta administracyjnego, które jest niezależne od konta użytkownika standardowego. SAW opiera się na tej praktyce separacji kont, zapewniając godną zaufania stację roboczą dla tych poufnych kont.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o tym, co firma Microsoft robi, aby zabezpieczyć infrastrukturę platformy Azure, zobacz:

- [Obiekty platformy Azure, pomieszczenia i zabezpieczenia fizyczne](physical-security.md)
- [Dostępność infrastruktury platformy Azure](infrastructure-availability.md)
- [Architektura sieci platformy Azure](infrastructure-network.md)
- [Sieć produkcyjna platformy Azure](production-network.md)
- [Funkcje zabezpieczeń usługi Azure SQL Database](infrastructure-sql.md)
- [Operacje produkcyjne i zarządzanie platformą Azure](infrastructure-operations.md)
- [Monitorowanie infrastruktury platformy Azure](infrastructure-monitoring.md)
- [Integralność infrastruktury platformy Azure](infrastructure-integrity.md)
- [Ochrona danych klientów platformy Azure](protection-customer-data.md)
