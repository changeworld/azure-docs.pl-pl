---
title: Często zadawane pytania — rozwiązanie VMware przez CloudSimple
description: Często zadawane pytania dotyczące rozwiązań VMware platformy Azure według CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 05/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 80380a1c33927029e000e59a5834f297340f5be3
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816227"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>Często zadawane pytania dotyczące rozwiązania VMware według CloudSimple

Często zadawane pytania i odpowiedzi dotyczące rozwiązań VMware platformy Azure według CloudSimple, które ułatwiają zrozumienie usługi i sposobu korzystania z niej. Pytania i odpowiedzi są uporządkowane według następujących kategorii:

* Usługa CloudSimple
* Łączność
* Networking
* Bezpieczeństwo
* Wystąpienia obliczeniowe
* Magazyn
* VMware
* Integracja z platformą Azure
 
## <a name="cloudsimple-service"></a>Usługa CloudSimple

**Co to jest rozwiązanie Azure VMware przez CloudSimple?**

Rozwiązanie VMware firmy Azure według CloudSimple przekształca i rozszerza obciążenia VMware do prywatnych, dedykowanych chmur na platformie Azure w ciągu kilku minut. Rozwiązanie stanowi problem, zarządza infrastrukturą i organizuje obciążenia między środowiskiem lokalnym i platformą Azure. Ponieważ aplikacje działają dokładnie tak samo lokalnie i na platformie Azure, możesz skorzystać z elastyczności i usług w chmurze bez konieczności ponownego tworzenia architektury aplikacji. CloudSimple obniża łączny koszt posiadania przy użyciu modelu zużycia w chmurze, który zapewnia aprowizacji na żądanie, płatność zgodnie z oczekiwaniami i optymalizację pojemności. Aby poznać funkcje, korzyści i scenariusze, zobacz [co to jest rozwiązanie Azure VMware według CloudSimple?](cloudsimple-vmware-solutions-overview.md).

**Co to jest chmura prywatna CloudSimple?**

Zastrzegasz prywatną, dedykowaną chmurę, która składa się ze środowiska obliczeniowego, magazynu i infrastruktury o wysokiej wydajności wdrożonego na Microsoft Azure infrastrukturze (w obszarze sprzętowym i w centrum danych) w lokalizacjach platformy Azure. Chmura prywatna zapewnia natywną platformę VMware jako usługę. W programie VMware, każda Chmura prywatna zawiera dokładnie jedno wystąpienie vCenter Server. VCenter Server zarządza wieloma węzłami ESXi zawartymi w jednym lub większej liczbie klastrów vSphere, wraz z odpowiednim magazynem sieci vSAN. Usługa CloudSimple może zawierać wiele chmur prywatnych w ramach subskrypcji platformy Azure. Aby uzyskać więcej informacji na temat chmur prywatnych, zobacz [Omówienie chmury prywatnej](cloudsimple-private-cloud.md).

**Gdzie jest dostępna usługa CloudSimple?**

CloudSimple jest dostępna w regionach Wschodnie stany USA i zachodnie stany USA.

**Jak mogę włączyć moją subskrypcję usługi CloudSimple?**

Skontaktuj się z przedstawicielem [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) konto Microsoft, aby włączyć subskrypcję usługi CloudSimple. Podaj identyfikator subskrypcji w wiadomości e-mail, dla której usługa CloudSimple ma być włączona. 

**Jak mogę uzyskać dostęp do portalu CloudSimple?**

Dostęp do portalu CloudSimple można uzyskać z Azure Portal. Aby uzyskać informacje na temat uzyskiwania dostępu do portalu CloudSimple, zobacz [dostęp do portalu VMware przez CloudSimple, z poziomu Azure Portal](https://docs.azure.cloudsimple.com/access-cloudsimple-portal).

**Jak mogę zwiększyć pojemność chmury prywatnej?**

Węzły są inicjowane z poziomu Azure Portal i rozszerzają chmurę prywatną z portalu CloudSimple. Chmurę prywatną można rozszerzyć, dodając węzły do istniejącego klastra vSphere lub tworząc nowy klaster vSphere. Aby uzyskać informacje na temat tej procedury, zobacz [Rozwiń chmurę prywatną CloudSimple](https://docs.azure.cloudsimple.com/expand-private-cloud).

**Co się stanie z moją chmurą prywatną podczas konserwacji?**

CloudSimple udostępnia okresowe powiadomienia w dniach przed zaplanowaną konserwacją. Konserwacja odbywa się w niezakłóconym stopniu, aby zapewnić dostępność chmury prywatnej. Konserwacja może być następujących typów:

- **Infrastruktura CloudSimple**: Infrastruktura CloudSimple została zaprojektowana tak, aby była wysoce dostępna. Podczas konserwacji, łączność i dostępność chmury prywatnej jest zapewniana przez aktualizację nadmiarowych składników pojedynczo bez żadnego wpływu. Masz dostęp do prywatnej chmury vCenter, wszystkich maszyn wirtualnych, połączenia internetowego z chmury prywatnej oraz połączeń z lokalnym lub platformą Azure.
- **Portal CloudSimple**: Podczas konserwacji niektóre funkcje w portalu CloudSimple mogą być niedostępne lub wyłączone. Powiadomienie o konserwacji zawiera informacje o tym, co można wykonać w portalu.

## <a name="connectivity"></a>Łączność

**Jakie są moje opcje łączności z moją siecią CloudSimple region?**

CloudSimple udostępnia trzy różne opcje łączności do łączenia się z siecią regionu CloudSimple. Wszystkie trzy opcje mogą być używane razem:

- Połączenie z usługą Azure ExpressRoute z lokalnego centrum danych do sieci regionu CloudSimple: Wysoce szybkie bezpieczne połączenie prywatne, które mostkuje lokalny obwód usługi ExpressRoute z obwodem usługi CloudSimple ExpressRoute przy użyciu Global Reach. Aby skonfigurować połączenie, zobacz [łączenie się z lokalnego do CloudSimple przy użyciu ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection).
- ExpressRoute połączenie z sieci wirtualnej platformy Azure do sieci regionu CloudSimple: Wysokie, bezpieczne połączenie prywatne z małym opóźnieniem, które mostkuje sieć wirtualną na platformie Azure z obwodem usługi CloudSimple ExpressRoute przy użyciu bram sieci wirtualnej. Aby skonfigurować połączenie, zobacz [łączenie środowiska chmury prywatnej CloudSimple z siecią wirtualną platformy Azure za pomocą ExpressRoute](https://docs.azure.cloudsimple.com/azure-expressroute-connection).
- Połączenie sieci VPN typu lokacja-lokacja z lokalnego centrum danych do sieci regionu CloudSimple: Bezpieczna wirtualna sieć prywatna z lokalnego urządzenia sieci VPN do regionu prywatnej chmury CloudSimple. Aby skonfigurować połączenie, zobacz [Konfigurowanie połączenia sieci VPN między siecią lokalną i chmurą prywatną CloudSimple](https://docs.azure.cloudsimple.com/set-up-vpn).

**Jak mogę połączyć się z chmurą prywatną?**

Szczegóły chmury prywatnej można wyświetlić w portalu CloudSimple. Aby nawiązać połączenie z programem vCenter, który odpowiada chmurze prywatnej, upewnij się, że połączenie sieciowe zostało nawiązane przy użyciu typu lokacja-lokacja, punkt-lokacja lub ExpressRoute. Następnie uruchom Portal CloudSimple z Azure Portal. Wybierz pozycję **Uruchom klienta vSphere** na stronie głównej lub na stronie szczegółów chmury prywatnej.

**Jakie jest zalety obwodu ExpressRoute?**

Obwód usługi Azure ExpressRoute zapewnia bezpieczne, wysokie połączenie o małych opóźnieniach. CloudSimple zapewnia dedykowany obwód ExpressRoute na region na klienta. Za pomocą tego obwodu można nawiązać bezpieczne połączenie z poziomu lokalnej i subskrypcji platformy Azure.

**Jakie koszty sieciowe mają być połączone z usługą CloudSimple? Czy istnieją jakiekolwiek opłaty za ruch wychodzący z i z CloudSimple do platformy Azure? Czy istnieją jakiekolwiek opłaty za ruch wychodzący między regionami?**

Za ruch wychodzący w sieci nie jest naliczana opłata. Stawki standardowe platformy Azure dotyczą wszelkich ruchu wychodzącego z sieci wirtualnej lub lokalnego obwodu ExpressRoute.

## <a name="networking"></a>Networking

**Jakie funkcje sieciowe są dostępne dla mojej chmury prywatnej?**

Można udostępnić sieci VLAN i ich podsieci oraz tabele zapory. Można przypisywać publiczne adresy IP i mapować je na maszynę wirtualną działającą w chmurze prywatnej. Aby uzyskać więcej informacji, zobacz Omówienie [sieci VLAN i podsieci](cloudsimple-vlans-subnets.md), omówienie [tabel zapory](cloudsimple-firewall-tables.md)i [publiczny adres IP](cloudsimple-public-ip-address.md).

**Jak mogę skonfigurować różne podsieci dla aplikacji w mojej chmurze prywatnej?**

Sieci VLAN można tworzyć w chmurze prywatnej z poziomu portalu CloudSimple. Po utworzeniu sieci VLAN można utworzyć rozproszoną grupę portów w chmurze prywatnej vCenter przy użyciu sieci VLAN i utworzyć maszyny wirtualne połączone z grupą portów rozproszonych. Można włączyć tabelę zapory dla sieci VLAN lub podsieci i zdefiniować reguły zapory w celu zabezpieczenia ruchu sieciowego.

**Jakie ustawienia zapory są dostępne dla moich chmur prywatnych?**

Można skonfigurować reguły dla ruchu północ-południe i wschód-zachód. Reguły są zdefiniowane w tabeli zapory. Tabelę zapory można dołączyć do sieci VLAN w chmurze prywatnej. Aby zapoznać się z procedurą instalacji, zobacz [Konfigurowanie tabel zapory i reguł dla chmur prywatnych](https://docs.azure.cloudsimple.com/firewall).

**Czy mogę przypisywać publiczne adresy IP dla maszyn wirtualnych w środowisku chmury prywatnej?**

W portalu CloudSimple można łatwo przydzielić nowy publiczny adres IP i skojarzyć go z prywatnym adresem IP maszyny wirtualnej lub urządzenia. Możesz również utworzyć nowe reguły zapory lub zastosować istniejące reguły zapory, aby zezwolić na ruch z określonych portów i określonych zestawów adresów IP w portalu. Aby zapoznać się z procedurą instalacji, zobacz [przydzielanie publicznych adresów IP dla środowiska chmury prywatnej](https://docs.azure.cloudsimple.com/public-ips).

## <a name="security"></a>Bezpieczeństwo

**Jakie są moje opcje zabezpieczeń w witrynie CloudSimple?**

Chmura prywatna CloudSimple zapewnia następujące funkcje zabezpieczeń w celu zabezpieczenia środowiska chmury prywatnej:

- **Dane przechowywane podczas szyfrowania:** Możesz zaszyfrować dane przechowywane w magazynie sieci vSAN w chmurze prywatnej. Sieci vSAN obsługuje zewnętrzny serwer zarządzania kluczami, który można wdrożyć w sieci wirtualnej platformy Azure lub w środowisku lokalnym. Aby uzyskać więcej informacji, zobacz [Konfigurowanie szyfrowania sieci vSAN dla prywatnej chmury CloudSimple](https://docs.azure.cloudsimple.com/vsan-encryption).
- **Zabezpieczenia sieci:** Sterowanie przepływem ruchu sieciowego z i do chmury prywatnej z Internetu, lokalnego i w obrębie podsieci chmury prywatnej za pomocą reguł zapory.
- **Bezpieczne połączenie prywatne:** Bezpieczne połączenie prywatne między siecią lokalną i subskrypcją platformy Azure.

## <a name="compute"></a>Wystąpienia obliczeniowe

**Jakiego rodzaju hosty są dostępne?**

CloudSimple oferuje dwa typy hostów:

* **Węzeł CS28**: Procesor: 2,2 2 GHz, łącznie 28 rdzeni, 48 HT. Pamięć RAM: 256 GB. Storage: 1600 GB pamięci podręcznej interfejsu NVMe, 5760-GB danych (wszystko-Flash). NFS 2x25Gbe NIC.
* **Węzeł CS36**: Procesor 2,3 2 GHz, łączne rdzenie 36, 72 HT. Pamięć RAM: 512 GB. Storage: 3200 – GB pamięci podręcznej interfejsu NVMe 11 520-GB danych (wszystkie-Flash). NFS 2x25Gbe NIC.

**Jak są obsługiwane błędy sprzętu?**

Wszystkie infrastruktury CloudSimple są stale monitorowane przez platformę CloudSimple i zespoły operacji usługi. Jeśli awaria sprzętowa zostanie wykryta, do chmury prywatnej zostanie dodany nowy węzeł. Węzeł, który uległ awarii, został usunięty w celu zapewnienia wysokiej dostępności chmury prywatnej.

## <a name="storage"></a>Magazyn

**Jakiego typu magazyn jest obsługiwany w chmurze prywatnej?**

Usługa CloudSimple oferuje **wszystkie-Flash usługi VMware sieci vSAN Storage** z każdą chmurą prywatną. Każdy vSphere jest tworzony z własnym magazynem danych sieci vSAN. Aby uzyskać więcej informacji, zobacz [prywatne składniki VMware Components-sieci vSAN Storage](https://docs.azure.cloudsimple.com/vmware-components/#vsan-storage).

**Czy jest obsługiwane szyfrowanie danych?**
Tak. Można skonfigurować magazyn sieci vSAN w chmurze prywatnej w taki sposób, aby korzystał z serwera zarządzania kluczami (KMS), który jest wdrażany lokalnie lub na platformie Azure na potrzeby szyfrowania danych przechowywanych w usłudze sieci vSAN.

**Jak są obsługiwane dyski z błędami?**

Monitorowanie CloudSimple stale monitoruje wszystkie składniki sprzętowe chmury prywatnej. Jeśli awaria dysku zostanie wykryta lub dysk zostanie zidentyfikowany jako niepowodzenie oparty na algorytmach heurystycznych, nowy węzeł zostanie automatycznie dodany do chmury prywatnej. Węzeł z uszkodzonym lub nieprawidłowym dyskiem zostanie usunięty z chmury prywatnej.

## <a name="vmware"></a>VMware

**Jak mogę wykonywać duże skalowanie i migrację aplikacji i danych z lokalnego systemu?**

CloudSimple zapewnia natywne rozwiązanie VMware vSphere. Wszystkie narzędzia używane do migracji danych zbiorczych mogą być używane z chmurą prywatną CloudSimple. Dostępne są następujące opcje:

- Oprogramowanie VMware HCX na potrzeby migracji zbiorczej danych.
- Zimna migracja danych przy użyciu usługi Storage vMotion z lokalnego do CloudSimple.

**Czy mogę zainstalować dowolne narzędzia VMware?**

CloudSimple zapewnia natywne rozwiązanie VMware vSphere. Wszystkie narzędzia służące do zarządzania środowiskiem vSphere lokalnie można używać w CloudSimple. CloudSimple obsługuje model do przynoszenia własnych licencji (BYOL) służący do instalowania narzędzi VMware.

**Jak są zarządzane aktualizacje i uaktualnienia?**

CloudSimple zarządza i aktualizuje wszystkie składniki infrastruktury chmury prywatnej w niezakłócony sposób. Aktualizacja aktualizacji lub poprawek zabezpieczeń wydana przez dostawców oprogramowania VMware lub infrastruktury jest zaplanowana do aktualizacji, gdy tylko zostanie zakwalifikowana przez CloudSimple.

CloudSimple nie wykonuje uaktualnień ani aktualizacji aplikacji zainstalowanych w chmurze prywatnej.

## <a name="azure-integration"></a>Integracja z platformą Azure

**Jakie usługi platformy Azure są obsługiwane?**

Usługa CloudSimple udostępnia usługę Azure ExpressRoute z subskrypcją na platformie Azure. Wszystkie usługi działające w ramach subskrypcji mają łączność sieciową z chmurą prywatną i mogą łączyć się z chmurą prywatną. Przykłady:

- **Azure Active Directory**: Użyj Azure Active Directory jako źródła tożsamości dla programu CloudSimple vCenter.
- **Azure Storage**: Magazyn służy do przechowywania kopii zapasowych, obrazów i innych danych z chmury prywatnej.
- **Aplikacje hybrydowe**: Można utworzyć architekturę aplikacji, która obejmuje chmury publiczne i prywatne. Na przykład można utworzyć serwery sieci Web na platformie Azure, które uzyskują dostęp do serwerów aplikacji i baz danych w chmurze prywatnej CloudSimple.
- **Azure monitor** i **Azure Security Center**: W obciążeniu działającym w oprogramowaniu VMware można używać monitorowania i Security Center do rejestrowania, metryk wydajności i zarządzania zabezpieczeniami.

**Jak mogę mapowanie moich dzierżawców VMware na platformę Azure?**

Usługa CloudSimple zapewnia unikatową zdolność do zarządzania maszynami wirtualnymi VMware w chmurze prywatnej z poziomu Azure Portal. Pulę zasobów programu vCenter skonfigurowaną z ograniczeniami zasobów można zamapować na subskrypcję przez administratora globalnego. 

**Jakie korzyści z licencjonowania mogę uzyskać na platformie Azure?**

Korzystając z CloudSimple, możesz skorzystać z Korzyść użycia hybrydowego platformy Azure i zaoszczędzić nawet 90% na licencjach, aby zachować inwestycje w licencje firmy Microsoft i obniżyć całkowity koszt posiadania w porównaniu z innymi chmurami. Można również uzyskać rozszerzone aktualizacje zabezpieczeń dla systemów Windows Server 2008 i Microsoft SQL Server 2008. Dbaj o obniżenie kosztów dzięki BYOL w chmurze dla popularnych aplikacji, takich jak Veeam, Zerto i inne. 
