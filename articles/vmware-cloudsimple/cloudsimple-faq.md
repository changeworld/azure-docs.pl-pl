---
title: Często zadawane pytania — Azure VMware Solutions (Automatyczna synchronizacja)
description: Często zadawane pytania dotyczące rozwiązań VMware platformy Azure (Automatyczna synchronizacja)
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c3808491c84f6c76a51c914aac6ee5e5ee370970
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025065"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-avs"></a>Często zadawane pytania dotyczące rozwiązania VMware dzięki automatycznej synchronizacji

## <a name="avs-service"></a>Usługa automatycznej synchronizacji

**Co to jest Azure VMware Solutions (Automatyczna synchronizacja)?**

Usługa Azure VMware Solutions (Automatyczna synchronizacja) przekształca i rozszerza obciążenia oprogramowania VMware do prywatnych, dedykowanych chmur na platformie Azure w ciągu kilku minut. Automatyczna synchronizacja jest przeznaczona do obsługi, zarządzania infrastrukturą i organizowania obciążeń między środowiskiem lokalnym i platformą Azure. Ponieważ aplikacje działają dokładnie tak samo lokalnie i na platformie Azure, możesz skorzystać z elastyczności i usług w chmurze bez konieczności ponownego tworzenia architektury aplikacji. Automatyczna synchronizacja pozwala obniżyć całkowity koszt posiadania przy użyciu modelu zużycia w chmurze, który zapewnia zainicjowanie obsługi na żądanie, płatność zgodnie z oczekiwaniami i optymalizację pojemności. Zapoznaj [się z artykułem co to jest rozwiązanie VMware na platformie Azure](cloudsimple-vmware-solutions-overview.md) , aby uzyskać informacje na temat funkcji, korzyści i scenariuszy.

**Co to jest chmura prywatna w wersji zaautomatycznej?**

Chmura prywatna w wersji zaciągającej jest prywatną, dedykowaną chmurą obejmującą środowisko obliczeniowe, magazynowe i sieciowe o wysokiej wydajności wdrożone w infrastrukturze Microsoft Azure (sprzęt i przestrzeń w centrum danych) w lokalizacjach platformy Azure. Chmura prywatna w wersji załadunkowej zapewnia natywną platformę VMware jako usługę. W programie VMware każda Chmura prywatna w wersji zapoznawczych zawiera dokładnie jedno wystąpienie vCenter Server. VCenter Server zarządza wieloma węzłami ESXi zawartymi w jednym lub większej liczbie klastrów vSphere wraz z odpowiednim magazynem wirtualnej sieci SAN (sieci vSAN). Usługa automatycznej synchronizacji może zawierać wiele chmur prywatnych w ramach subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [Omówienie automatycznej synchronizacji chmury prywatnej](cloudsimple-private-cloud.md).

**Gdzie jest dostępna usługa automatycznej synchronizacji?**

Automatyczna synchronizacja jest dostępna w regionach Wschodnie stany USA, zachodnie stany USA i Europa Zachodnia z dodatkowymi regionami.

**Jak mogę włączyć subskrypcję na potrzeby automatycznej synchronizacji?**

Możesz skontaktować się z przedstawicielem konto Microsoft w [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) , aby włączyć subskrypcję usługi automatycznej synchronizacji. Podaj identyfikator subskrypcji w wiadomości e-mail, dla której ma być włączona usługa automatycznej synchronizacji. 

**Jak mogę uzyskać dostęp do portalu automatycznej synchronizacji?**

Dostęp do portalu automatycznej synchronizacji można uzyskać z Azure Portal. Aby uzyskać szczegółowe informacje, zobacz [dostęp do portalu VMware Solutions (Automatyczna synchronizacja) z Azure Portal](access-cloudsimple-portal.md).

**Jak mogę zwiększyć pojemność chmury prywatnej w wersji zaautomatycznej?**

Aby zwiększyć pojemność, Kup dodatkowe węzły z Azure Portal a następnie użyj węzłów, aby rozwinąć chmurę prywatną w wersji zastosowanej w portalu automatycznej synchronizacji. Można dodać dodatkowe węzły do istniejącego klastra vSphere lub dodać je do nowego klastra vSphere. Aby uzyskać szczegółowe informacje, zobacz [Rozwiń chmurę prywatną](expand-private-cloud.md).

**Co się stanie z moją chmurą prywatną podczas konserwacji?**

Automatyczna synchronizacja zapewnia powiadomienia kilka dni przed upływem zaplanowanego interwału konserwacji. Konserwacja odbywa się w niezakłóconym stopniu, aby zapewnić dostępność chmury prywatnej automatycznej synchronizacji. Konserwacja może być następujących typów:

* **Infrastruktura automatycznej synchronizacji**. Infrastruktura automatycznej dostępności została zaprojektowana tak, aby była wysoce dostępna. W trakcie tego typu interwału konserwacji nadmiarowe składniki są aktualizowane pojedynczo, aby uniknąć przerw w świadczeniu usług. Zapewniasz dostęp do wersji zawieszeń prywatnych chmury, wszystkich maszyn wirtualnych, połączenia internetowego z chmury prywatnej do automatycznej synchronizacji oraz połączeń z lokalnym lub platformą Azure.
* **Portal automatycznej synchronizacji**. W trakcie tego typu interwału konserwacji niektóre funkcje portalu automatycznej konfiguracji mogą być wyłączone lub niedostępne. Powiadomienie przed interwałem konserwacji zawiera szczegółowe informacje o ograniczeniach funkcji, gdy odbywa się konserwacja.

## <a name="connectivity"></a>Łączność

**Jakie są moje opcje łączności w sieci regionu automatycznej synchronizacji?**

Automatyczna synchronizacja zapewnia następujące opcje łączności, aby połączyć się z siecią regionu automatycznej synchronizacji. W tym samym czasie można używać wielu opcji.

* **ExpressRoute połączenie z lokalnego centrum danych do sieci regionu z**obsługą synchronizacji. Jest to bardzo szybkie, bezpieczne połączenie prywatne, które używa Global Reach do mostkowania lokalnego obwodu ExpressRoute do obwodu usługi ExpressRoute. Aby uzyskać instrukcje dotyczące konfigurowania połączenia, zobacz [łączenie się z lokalnego narzędzia do automatycznej synchronizacji przy użyciu ExpressRoute](on-premises-connection.md).
* **ExpressRoute połączenie z sieci wirtualnej platformy Azure z siecią regionu automatycznej synchronizacji**. Jest to bardzo szybkie i bezpieczne połączenie prywatne, które korzysta z bram sieci wirtualnej do mostkowania sieci wirtualnej na platformie Azure do obwodu usługi synchronizacji ExpressRoute. Aby uzyskać instrukcje dotyczące konfigurowania połączenia, zobacz temat [łączenie środowiska chmury prywatnej automatycznej konfiguracji z usługą Azure Virtual Network za pomocą ExpressRoute](azure-expressroute-connection.md).
* **Połączenie sieci VPN typu lokacja-lokacja z lokalnego centrum danych z siecią regionu automatycznej synchronizacji**. Jest to bezpieczna wirtualna sieć prywatna z lokalnego urządzenia sieci VPN w regionie chmury prywatnej automatycznej synchronizacji. Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie bram sieci VPN w sieci automatycznej synchronizacji](vpn-gateway.md).

**Jak mogę połączyć się z chmurą prywatną do automatycznej synchronizacji?**

Szczegóły chmury prywatnej automatycznej synchronizacji można wyświetlić w portalu automatycznej wersji zapoznawczej. Aby nawiązać połączenie z programem vCenter, który odpowiada chmurze prywatnej, należy najpierw sprawdzić, czy połączenie sieciowe jest nawiązywane przy użyciu sieci VPN typu lokacja-lokacja, sieci VPN typu punkt-lokacja lub ExpressRoute. Następnie uruchom Portal automatycznej synchronizacji z Azure Portal, a następnie kliknij pozycję **Uruchom klienta vSphere** na stronie głównej lub na stronie szczegółów chmury prywatnej dotyczącej automatycznej synchronizacji.

**Jakie jest zalety obwody usługi ExpressRoute?**

Obwód usługi Azure ExpressRoute to szybkie i bezpieczne połączenie o małych opóźnieniach. Automatyczna synchronizacja zapewnia dedykowany obwód ExpressRoute na region na klienta. Za pomocą tego obwodu można nawiązać bezpieczne połączenie z poziomu lokalnej lub subskrypcji platformy Azure.

**Jakie koszty sieciowe mają być połączone z programem automatyczna synchronizacja? Czy opłaty za ruch wychodzący są naliczane między programem automatyczna synchronizacja i platformą Azure, czy między regionami?**

Nie ma opłat za usługę automatycznej synchronizacji w przypadku ruchu wychodzącego w sieci. Stawki standardowe platformy Azure dotyczą wszelkich ruchu wychodzącego z sieci wirtualnej lub lokalnego obwodu usługi ExpressRoute.

## <a name="networking"></a>Networking

**Jakie funkcje sieciowe są dostępne dla mojej chmury prywatnej "Automatyczna synchronizacja"?**

Można udostępniać sieci VLAN (i ich podsieci) oraz tabele zapory i przypisywać publiczne adresy IP, które są mapowane na maszynę wirtualną działającą w chmurze prywatnej synchronizacji. Aby uzyskać szczegółowe informacje o funkcjach sieciowych, zobacz Omówienie [sieci VLAN i podsieci](cloudsimple-vlans-subnets.md), omówienie [tabel zapory](cloudsimple-firewall-tables.md)i [publiczny adres IP](cloudsimple-public-ip-address.md).

**Jak mogę skonfigurować różne podsieci dla aplikacji w mojej chmurze prywatnej automatycznej synchronizacji?**

Możesz tworzyć sieci VLAN w chmurze prywatnej automatycznej synchronizacji w portalu automatycznej synchronizacji. Po utworzeniu sieci VLAN można utworzyć rozproszoną grupę portów na potrzeby narzędzia do automatycznej synchronizacji w chmurze prywatnej w programie vCenter przy użyciu sieci VLAN i utworzyć maszyny wirtualne połączone z grupą portów rozproszonych. Można włączyć tabele zapory dla sieci VLAN/podsieci i zdefiniować reguły zapory w celu zabezpieczenia ruchu sieciowego.

**Jakie ustawienia zapory są dostępne dla chmur prywatnych?**

Można skonfigurować reguły dla ruchu północ-południe i wschód-zachód. Reguły są zdefiniowane w tabeli zapory. Tabelę zapory można dołączyć do sieci VLAN w chmurze prywatnej automatycznej synchronizacji. Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie tabel zapory i reguł na potrzeby automatycznej synchronizacji chmur prywatnych](firewall.md).

**Czy można przypisywać publiczne adresy IP do maszyn wirtualnych w środowisku chmury prywatnej do automatycznej synchronizacji?**

W portalu automatycznej synchronizacji można przydzielić nowy publiczny adres IP i skojarzyć go z prywatnym adresem IP maszyny wirtualnej lub urządzenia. Możesz również utworzyć nowe reguły zapory lub zastosować istniejące reguły zapory, aby zezwolić na ruch z określonych portów i adresów IP w portalu. Aby uzyskać szczegółowe informacje, zobacz [przydzielanie publicznych adresów IP na potrzeby automatycznej synchronizacji środowiska chmury prywatnej](public-ips.md).

## <a name="security"></a>Zabezpieczenia

**Jakie są opcje zabezpieczeń dotyczące automatycznej synchronizacji?**

Funkcja automatycznej synchronizacji zapewnia następujące funkcje zabezpieczeń w celu zapewnienia bezpieczeństwa środowiska chmury prywatnej na potrzeby automatycznej synchronizacji:

* **Szyfrowanie danych w czasie spoczynku**. Możesz zaszyfrować dane przechowywane w magazynie sieci vSAN w chmurze prywatnej automatycznej synchronizacji. program sieci vSAN obsługuje zewnętrzne serwery zarządzania kluczami, które można wdrożyć w sieci wirtualnej platformy Azure lub w środowisku lokalnym. Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie szyfrowania sieci vSAN w chmurze prywatnej automatycznej](vsan-encryption.md).
* **Zabezpieczenia sieciowe**. Sterowanie przepływem ruchu sieciowego za pomocą reguł zapory, które są stosowane między chmurą prywatną i Internetem, swoją reprocesową chmurą i środowiskiem lokalnym oraz w podsieciach chmury prywatnej automatycznej synchronizacji.
* **Bezpieczne połączenie prywatne**. Bezpieczne połączenie prywatne jest nawiązywane między siecią lokalną i subskrypcją platformy Azure.

## <a name="compute"></a>Compute

**Jakiego rodzaju hosty są dostępne?**

Automatyczna synchronizacja oferuje następujące typy hostów:

* **Węzeł CS28:** Procesor: 2,2 2 GHz, łącznie 28 rdzeni, 48 HT.  PAMIĘĆ RAM: 256 GB.  Magazyn: pamięć podręczna interfejsu NVMe 1600 GB, dane 5760 GB (wszystkie-Flash). Sieć: karta sieciowa 4x25Gbe
* **Węzeł CS36:** Procesor 2,3 2 GHz, łączne rdzenie 36, 72 HT.  PAMIĘĆ RAM: 512 GB.  Magazyn: 3200 GB pamięci podręcznej interfejsu NVMe 11520 GB danych (wszystkie-Flash).  Sieć: karta sieciowa 4x25Gbe
* **Węzeł CS36m:** Procesor 2,3 2 GHz, łączne rdzenie 36, 72 HT.  PAMIĘĆ RAM: 576 GB.  Magazyn: 3200 GB pamięci podręcznej interfejsu NVMe 13360 GB danych (wszystkie-Flash).  Sieć: karta sieciowa 4x25Gbe

**Jak są obsługiwane błędy sprzętu?**

Cała infrastruktura automatycznej synchronizacji jest stale monitorowana przez platformę automatycznej synchronizacji i naszych zespołów operacyjnych usługi. Jeśli awaria sprzętowa zostanie wykryta, do chmury prywatnej automatycznej synchronizacji zostanie dodany nowy węzeł i zostanie usunięty węzeł zakończony niepowodzeniem.

## <a name="storage"></a>Usługa Storage

**Jakiego typu magazyn jest obsługiwany w chmurze prywatnej automatycznej synchronizacji?**

Funkcja automatycznej synchronizacji oferuje wszystkie funkcje programu VMware sieci vSAN Storage z każdą funkcją automatycznej synchronizacji w chmurze prywatnej. Każdy vSphere jest tworzony z własnym magazynem danych sieci vSAN. Aby uzyskać szczegółowe informacje, zobacz artykuł [Automatyczna synchronizacja chmurowa chmury Private Components-sieci vSAN Storage](vmware-components.md#vsan-storage).

**Czy jest obsługiwane szyfrowanie danych?**
Tak. Można skonfigurować magazyn sieci vSAN w chmurze prywatnej automatycznej synchronizacji, aby używać serwera zarządzania kluczami (KMS) wdrożonego lokalnie lub na platformie Azure w celu szyfrowania danych przechowywanych w usłudze sieci vSAN.

**Jak są obsługiwane dyski z błędami?**

Funkcja automatycznej synchronizacji stale monitoruje wszystkie składniki sprzętowe chmury prywatnej automatycznej synchronizacji. Jeśli awaria dysku zostanie wykryta lub dysk zostanie zidentyfikowany jako niepowodzenie (w oparciu o algorytmy heurystyczne), nowy węzeł zostanie automatycznie dodany do chmury prywatnej automatycznej synchronizacji. Węzeł z dyskiem zakończonym niepowodzeniem lub niepowodzeniem zostanie usunięty z chmury prywatnej automatycznej synchronizacji.

## <a name="vmware"></a>VMware

**Jak mogę wykonywać na dużą skalę przekazywanie lub migrację aplikacji i danych z lokalnego systemu?**

Narzędzie do automatycznej synchronizacji oferuje natywne rozwiązanie VMware vSphere. Wszystkie narzędzia VMware do migracji danych zbiorczych mogą być używane razem z chmurą prywatną. Dostępne są następujące opcje:

* Oprogramowanie VMware HCX na potrzeby migracji zbiorczej danych.
* Zimna migracja danych przy użyciu usługi Storage vMotion z wersji lokalnej do automatycznej synchronizacji.

**Czy mogę zainstalować dowolne narzędzia VMware?**

Narzędzie do automatycznej synchronizacji oferuje natywne rozwiązanie VMware vSphere. Wszystkie narzędzia VMware używane do zarządzania lokalnym środowiskiem vSphere mogą być używane na potrzeby automatycznej synchronizacji. Narzędzie do automatycznej obsługi wersji obsługuje model "BYOL" do instalowania narzędzi VMware.

**Jak są zarządzane aktualizacje i uaktualnienia?**

Funkcja automatycznej synchronizacji zarządza i aktualizuje wszystkie składniki infrastruktury chmury prywatnej automatycznej synchronizacji w niezakłócony sposób. Wszystkie aktualizacje i poprawki zabezpieczeń wydane przez dostawców oprogramowania VMware lub infrastruktury są planowane do aktualizacji, gdy tylko zakwalifikują się do automatycznej synchronizacji.

Funkcja automatycznej synchronizacji nie wykonuje uaktualnień ani aktualizacji aplikacji zainstalowanych w chmurze prywatnej automatycznej synchronizacji.

## <a name="azure-integration"></a>Integracja z platformą Azure

**Jakie usługi platformy Azure są obsługiwane?**

Automatyczna synchronizacja zapewnia połączenie usługi Azure ExpressRoute z subskrypcją na platformie Azure. Wszystkie usługi działające w ramach subskrypcji mogą łączyć się z chmurą prywatną. Przykłady obejmują:

* **Azure Active Directory** jako źródło tożsamości dla serwera vCenter do automatycznej synchronizacji.
* **Usługa Azure Storage** do przechowywania kopii zapasowych, obrazów i innych danych z chmury prywatnej automatycznej synchronizacji.
* **Aplikacje hybrydowe** z architekturą aplikacji, która obejmuje prywatne i automatyczna synchronizacja chmur prywatnych. Na przykład można utworzyć serwery WebServer na platformie Azure, które uzyskują dostęp do serwerów aplikacji i baz danych w chmurze prywatnej automatycznej synchronizacji.
* **Usługa Azure monitor** i **usługa Azure Security Center** dla obciążeń działających na potrzeby rejestrowania, metryk wydajności i zarządzania zabezpieczeniami w oprogramowaniu VMware.

**Jak mogę mapowanie moich dzierżawców VMware na platformę Azure?**

Funkcja automatycznej synchronizacji zapewnia unikatową możliwość zarządzania maszynami wirtualnymi VMware w ramach automatycznej synchronizacji chmury prywatnej na podstawie Azure Portal. Pulę zasobów programu vCenter skonfigurowaną z żądanymi ograniczeniami zasobów można zamapować na subskrypcję przez administratora globalnego. 

**Jakie korzyści z licencjonowania mogę uzyskać na platformie Azure?**

Dzięki funkcji automatycznej synchronizacji możesz skorzystać z korzyści użycia hybrydowego platformy Azure i zaoszczędzić do 90% na licencji. Ta korzyść zachowuje inwestycję w licencje firmy Microsoft i obniża całkowity koszt posiadania względem innych rozwiązań w chmurze. Można również uzyskać rozszerzone aktualizacje zabezpieczeń dla systemów Windows Server 2008 i Microsoft SQL Server 2008. Model "BYOL" umożliwia zachowanie niskich kosztów dla typowych aplikacji, takich jak Veeam i Zerto. 
