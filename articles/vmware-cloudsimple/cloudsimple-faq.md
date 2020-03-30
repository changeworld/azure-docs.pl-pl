---
title: Często zadawane pytania — rozwiązanie Azure VMware firmy CloudSimple
description: Często zadawane pytania dotyczące rozwiązania Azure VMware firmy CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 95d8c2974ea372dd59d15a9f2cc31ed171acd932
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025065"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>Często zadawane pytania dotyczące rozwiązania VMware firmy CloudSimple

## <a name="cloudsimple-service"></a>Usługa CloudSimple

**Co to jest rozwiązanie Azure VMware firmy CloudSimple?**

Rozwiązanie Azure VMware firmy CloudSimple przekształca i rozszerza obciążenia VMware na prywatne, dedykowane chmury na platformie Azure w ciągu kilku minut. CloudSimple zajmuje się inicjowania obsługi administracyjnej, zarządzania infrastrukturą i organizowania obciążeń między lokalnymi i platformy Azure. Ponieważ aplikacje działają dokładnie tak samo lokalnie i na platformie Azure, możesz korzystać z elastyczności i usług chmury bez złożoności rearchitecting aplikacji. CloudSimple obniża całkowity koszt posiadania dzięki modelowi zużycia chmury, który zapewnia aprowizowanie na żądanie, zwiększanie płatności zgodnie z rzeczywistym użyciem i optymalizację pojemności.  Zobacz [Co to jest rozwiązanie VMware na platformie Azure przez CloudSimple](cloudsimple-vmware-solutions-overview.md) dla funkcji, korzyści i scenariuszy.

**Co to jest CloudSimple Private Cloud?**

CloudSimple Private Cloud to prywatna, dedykowana chmura, która składa się z wysokowydajnego środowiska obliczeniowego, magazynu i sieci wdrożonego w infrastrukturze platformy Microsoft Azure (sprzęt i przestrzeń centrum danych) w lokalizacjach platformy Azure.  Private Cloud zapewnia natywną platformę VMware jako usługę. W warunkach VMware każda chmura prywatna zawiera dokładnie jedno wystąpienie serwera vCenter. Serwer vCenter zarządza wieloma węzłami ESXi zawartymi w co najmniej jednym klastrze vSphere, wraz z odpowiednim magazynem virtual SAN (vSAN). Usługa CloudSimple może zawierać wiele chmur prywatnych w ramach subskrypcji platformy Azure.  Aby uzyskać więcej informacji, zobacz [Omówienie chmury prywatnej](cloudsimple-private-cloud.md).

**Gdzie jest dostępna usługa CloudSimple?**

CloudSimple jest dostępny w regionach Wschodnich stanów USA, Zachodnich Stanów Zjednoczonych i Europy Zachodniej, a wkrótce pojawią się dodatkowe regiony.

**Jak włączyć subskrypcję cloudsimple?**

Możesz skontaktować się z [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) przedstawicielem konta Microsoft, aby włączyć subskrypcję usługi CloudSimple. Podaj swój identyfikator subskrypcji w wiadomości e-mail, dla której chcesz włączyć usługę CloudSimple.  

**Jak uzyskać dostęp do portalu CloudSimple?**

Dostęp do portalu CloudSimple z witryny Azure portal.  Aby uzyskać szczegółowe informacje, zobacz [Dostęp do rozwiązania VMware przez portal CloudSimple z witryny Azure portal.](access-cloudsimple-portal.md)

**Jak zwiększyć pojemność chmury prywatnej?**

Aby zwiększyć pojemność, zakup dodatkowych węzłów z witryny Azure portal, a następnie użyj węzłów, aby rozwinąć chmurę prywatną z portalu CloudSimple.  Można dodać dodatkowe węzły do istniejącego klastra vSphere lub dodać je do nowego klastra vSphere.  Aby uzyskać szczegółowe informacje, zobacz [Rozszerzanie chmury prywatnej CloudSimple](expand-private-cloud.md).

**Co dzieje się z moją chmurą prywatną podczas konserwacji?**

CloudSimple zapewnia powiadomienie na kilka dni przed zaplanowanym interwałem konserwacji.  Konserwacja odbywa się w sposób nieuniknięty, aby zapewnić dostępność chmury prywatnej.  Konserwacja może być następujących typów:

* **Infrastruktura CloudSimple**.  Infrastruktura CloudSimple została zaprojektowana tak, aby była dostępna.  W tym rodzaju interwału konserwacji nadmiarowe komponenty są aktualizowane po jednym naraz, aby uniknąć przerw w świadczeniu usług. Użytkownik zachowuje dostęp do usługi Private Cloud vCenter, wszystkich maszyn wirtualnych, połączenia internetowego z chmury prywatnej oraz połączeń z lokalnymi lub platformą Azure.
* **CloudSimple portal**. Podczas tego typu interwału konserwacji niektóre funkcje portalu CloudSimple mogą być wyłączone lub niedostępne.  Powiadomienie przed przerwą międzyobsługową zawiera szczegółowe informacje na temat ograniczeń funkcji podczas konserwacji.

## <a name="connectivity"></a>Łączność

**Jakie są moje opcje łączności z siecią regionu CloudSimple?**

CloudSimple udostępnia następujące opcje łączności, aby połączyć się z siecią regionu CloudSimple. Można użyć wielu opcji w tym samym czasie.

* **Połączenie usługi ExpressRoute z lokalnego centrum danych z siecią regionu CloudSimple**. Jest to szybkie, o małym opóźnieniu bezpieczne połączenie prywatne, które używa globalnego zasięgu do mostkowego obwodu usługi ExpressRoute lokalnego do obwodu CloudSimple ExpressRoute. Aby uzyskać instrukcje dotyczące konfigurowania połączenia, zobacz [Łączenie się z lokalnego do CloudSimple przy użyciu usługi ExpressRoute](on-premises-connection.md).
* **Połączenie usługi ExpressRoute z sieci wirtualnej platformy Azure do sieci regionu CloudSimple**. Jest to szybkie, o małym opóźnieniu bezpieczne połączenie prywatne, które używa bram sieci wirtualnej do mostka sieci wirtualnej na platformie Azure do obwodu CloudSimple ExpressRoute. Aby uzyskać instrukcje dotyczące konfigurowania połączenia, zobacz [Łączenie środowiska cloudsimple private cloud z siecią wirtualną platformy Azure przy użyciu usługi ExpressRoute](azure-expressroute-connection.md).
* **Połączenie sieci VPN między lokacjami z lokalnego centrum danych do sieci regionu CloudSimple**. Jest to bezpieczna wirtualna sieć prywatna z lokalnego urządzenia sieci VPN do regionu CloudSimple Private Cloud.  Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie bram sieci VPN w sieci CloudSimple](vpn-gateway.md).

**Jak połączyć się z chmurą prywatną?**

Szczegółowe informacje o chmurze prywatnej można wyświetlić w portalu CloudSimple. Aby połączyć się z centrum vCenter odpowiadającym chmurze prywatnej, najpierw sprawdź, czy połączenie sieciowe zostało nawiązane przy użyciu sieci VPN typu lokacja, sieci VPN typu punkt-lokacja lub usługi ExpressRoute. Następnie uruchom portal CloudSimple z witryny Azure portal i kliknij przycisk **Uruchom klienta vSphere** na stronie głównej lub na stronie szczegóły chmury prywatnej.

**Jaka jest zaleta obwodów usługi ExpressRoute?**

Obwód usługi Azure ExpressRoute to bezpieczne połączenie o dużej szybkości i małych opóźnieniach.  CloudSimple udostępnia dedykowany obwód usługi ExpressRoute na region na klienta.  Za pomocą tego obwodu można ustanowić bezpieczne połączenie z lokalnego lub subskrypcji platformy Azure.

**Jakie są koszty połączenia z usługą CloudSimple?  Czy opłaty za ruch wychodzący mają zastosowanie między CloudSimple i Azure lub między regionami?**

Nie ma opłaty CloudSimple dla wyjścia sieciowego.  Standardowe stawki platformy Azure mają zastosowanie do ruchu wychodzącego z sieci wirtualnej lub lokalnego obwodu usługi ExpressRoute.

## <a name="networking"></a>Obsługa sieci

**Jakie funkcje sieciowe są dostępne dla mojej chmury prywatnej?**

Można aprowizować sieci VLAN (i ich podsieci) i tabel zapory oraz przypisywać publiczne adresy IP mapowane do maszyny wirtualnej uruchomionej w chmurze prywatnej. Aby uzyskać szczegółowe informacje na temat funkcji sieciowych, zobacz [omówienie sieci VPN i podsieci,](cloudsimple-vlans-subnets.md) [omówienie tabel zapory](cloudsimple-firewall-tables.md)i [omówienie publicznego adresu IP.](cloudsimple-public-ip-address.md)

**Jak skonfigurować różne podsieci dla moich aplikacji w chmurze prywatnej?**

Nazwy VLAN są tworzone w chmurze prywatnej z portalu CloudSimple.  Po utworzeniu sieci VLAN można utworzyć grupę portów rozproszonych w centrum vCenter private cloud przy użyciu sieci VLAN i utworzyć maszyny wirtualne, które są połączone z rozproszoną grupą portów.  Można włączyć tabele zapory dla sieci VLAN/podsieci i zdefiniować reguły zapory w celu zabezpieczenia ruchu sieciowego.

**Jakie ustawienia zapory są dostępne dla moich chmur prywatnych?**

Można skonfigurować reguły dla ruchu północ-południe i wschód-zachód.  Reguły są zdefiniowane w tabeli zapory.  Tabela zapory można podłączyć do sieci VLAN w chmurze prywatnej.  Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie tabel zapory i reguł dla chmur prywatnych](firewall.md).

**Czy mogę przypisać publiczne adresy IP do maszyn wirtualnych w moim środowisku private cloud?**

W portalu CloudSimple można przydzielić nowy publiczny adres IP i skojarzyć go z prywatnym adresem IP maszyny wirtualnej lub urządzenia.  Można również utworzyć nowe reguły zapory lub zastosować istniejące reguły zapory, aby zezwolić na ruch z określonych portów i adresów IP w portalu. Aby uzyskać szczegółowe informacje, zobacz [Przydzielanie publicznych adresów IP dla środowiska private cloud](public-ips.md).

## <a name="security"></a>Zabezpieczenia

**Jakie są moje opcje zabezpieczeń w usłudze CloudSimple?**

CloudSimple udostępnia następujące funkcje zabezpieczeń do zabezpieczania środowiska chmury prywatnej:

* **Szyfrowanie danych w spoczynku**. Możesz szyfrować dane w stanie spoczynku przebywającym w magazynie vSAN w chmurze prywatnej. VSAN obsługuje serwery zarządzania kluczami zewnętrznymi, które można wdrożyć w sieci wirtualnej platformy Azure lub środowisku lokalnym.  Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie szyfrowania vSAN dla chmury CloudSimple Private Cloud](vsan-encryption.md).
* **Bezpieczeństwo sieci**. Kontroluj przepływ ruchu sieciowego za pomocą reguł zapory, które mają zastosowanie między chmurą prywatną a Internetem, chmurą prywatną i środowiskiem lokalnym lub w podsieciach chmury prywatnej.
* **Bezpieczne, prywatne połączenie**. Ustanawia się bezpieczne, prywatne połączenie między siecią lokalną a subskrypcją platformy Azure.

## <a name="compute"></a>Wystąpienia obliczeniowe

**Jakie hosty są dostępne?**

CloudSimple oferuje następujące typy hostów:

* **Węzeł CS28:** CPU: 2x 2.2 GHz, łącznie 28 rdzeni, 48 HT.  RAM: 256 GB.  Pamięć masowa: 1600 GB pamięci podręcznej NVMe, dane 5760 GB (All-Flash). Sieć: karta sieciowa 4x25Gbe
* **Węzeł CS36:** CPU 2x 2.3 GHz, łącznie 36 rdzeni, 72 HT.  RAM: 512 GB.  Pamięć masowa: 3200 GB pamięci podręcznej NVMe 11520 GB danych (All-Flash).  Sieć: karta sieciowa 4x25Gbe
* **Węzeł CS36m:** CPU 2x 2.3 GHz, łącznie 36 rdzeni, 72 HT.  RAM: 576 GB.  Pamięć masowa: 3200 GB pamięci podręcznej NVMe 13360 GB danych (All-Flash).  Sieć: karta sieciowa 4x25Gbe

**Jak obsługiwane są wszelkie awarie sprzętu?**

Cała infrastruktura CloudSimple jest stale monitorowana przez platformę CloudSimple i nasze zespoły operacyjne usług.  Jeśli zostanie wykryty błąd sprzętowy, nowy węzeł zostanie dodany do chmury prywatnej i węzeł nie powiodło się zostanie usunięty.

## <a name="storage"></a>Magazyn

**Jaki typ magazynu jest obsługiwany w chmurze prywatnej?**

CloudSimple oferuje całą pamięć masową VMware vSAN z każdą chmurą prywatną.  Każdy vSphere jest tworzony przy własnych vSAN magazynu danych.  Aby uzyskać szczegółowe informacje, zobacz [Private Cloud VMware components - vSAN storage](vmware-components.md#vsan-storage).

**Czy szyfrowanie danych jest obsługiwane?**
Tak.  Można skonfigurować magazyn vSAN w chmurze prywatnej, aby używać serwera zarządzania kluczami (KMS), który jest wdrażany lokalnie lub na platformie Azure do szyfrowania danych przechowywanych w usłudze vSAN.

**Jak obsługiwane są dyski, które uległy awarii?**

CloudSimple stale monitoruje wszystkie składniki sprzętowe chmury prywatnej.  Jeśli zostanie wykryty błąd dysku lub dysk zostanie zidentyfikowany jako awaryjny (na podstawie heurystyki), nowy węzeł jest automatycznie dodawany do chmury prywatnej.  Węzeł z dyskiem, który uległ awarii lub uległ awarii, zostanie usunięty z chmury prywatnej.

## <a name="vmware"></a>VMware

**Jak wykonać przekazywanie lub migrację aplikacji i danych na dużą skalę z lokalnego środowiska?**

CloudSimple zapewnia natywne rozwiązanie VMware vSphere.  Wszystkie narzędzia VMware do masowej migracji danych mogą być używane z chmurą prywatną.  Dostępne są następujące opcje:

* VMware HCX do masowej migracji danych.
* Migracja danych na zimno przy użyciu pamięci wirtualnej magazynu z lokalnego do CloudSimple.

**Czy mogę zainstalować dowolne narzędzia VMware?**

CloudSimple zapewnia natywne rozwiązanie VMware vSphere.  Wszystkie narzędzia VMware używane do zarządzania lokalnym środowiskiem vSphere mogą być używane w CloudSimple.  CloudSimple obsługuje model byol (bring-your-own-license) do instalowania narzędzi VMware.

**W jaki sposób zarządzane są aktualizacje i uaktualnienia?**

CloudSimple zarządza i aktualizuje wszystkie składniki infrastruktury chmury prywatnej w sposób bezproblemowy, nieunikłujący.  Wszystkie aktualizacje i poprawki zabezpieczeń wydane przez VMware lub dostawców infrastruktury są zaplanowane do aktualizacji, jak tylko są one kwalifikowane przez CloudSimple.

CloudSimple nie wykonuje uaktualnień ani aktualizacji aplikacji zainstalowanych w chmurze prywatnej.

## <a name="azure-integration"></a>Integracja z platformą Azure

**Jakie usługi platformy Azure są obsługiwane?**

CloudSimple zapewnia połączenie usługi Azure ExpressRoute do subskrypcji na platformie Azure.  Wszystkie usługi uruchomione w ramach subskrypcji mogą łączyć się z chmurą prywatną.  Przykłady:

* **Usługa Azure Active Directory** jako źródło tożsamości dla usługi CloudSimple vCenter.
* **Magazyn platformy Azure** do przechowywania kopii zapasowych, obrazów i innych danych z chmury prywatnej.
* **Aplikacje hybrydowe** z architekturą aplikacji obejmującą chmury publiczne i prywatne.  Na przykład można utworzyć serwery sieci Web na platformie Azure, które uzyskują dostęp do aplikacji i serwerów bazy danych w chmurze prywatnej.
* **Monitor platformy Azure** i **centrum zabezpieczeń platformy Azure** dla obciążeń działających na vmware obsługują rejestrowanie, metryki wydajności i zarządzanie zabezpieczeniami.

**Jak mapować dzierżawców VMware na platformę Azure?**

CloudSimple zapewnia unikatową możliwość zarządzania maszynami wirtualnymi VMware w chmurze prywatnej z witryny Azure portal.  Pula zasobów vCenter skonfigurowana z żądanymi ograniczeniami zasobów może być mapowana do subskrypcji przez administratora globalnego.  

**Jakie korzyści z licencjonowania mogę uzyskać dzięki platformie Azure?**

Dzięki CloudSimple możesz korzystać z korzyści użycia hybrydowego platformy Azure i zaoszczędzić do 90% na licencjach. Ta korzyść pozwala zachować inwestycje w licencje firmy Microsoft i obniża koszt posiadania w stosunku do innych rozwiązań w chmurze. Dostępne są również rozszerzone aktualizacje zabezpieczeń dla systemów Windows Server 2008 i Microsoft SQL Server 2008.  Model byol (bring-your-own-license) pomaga utrzymać niskie koszty popularnych aplikacji, takich jak Veeam i Zerto.  
