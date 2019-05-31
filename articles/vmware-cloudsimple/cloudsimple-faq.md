---
title: Często zadawane pytania — VMware Solution by CloudSimple
description: Często zadawane pytania dotyczące usługi Azure VMware Solution by CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 05/24/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e727398e1b7bfa406166574ab40320c68dac5709
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358531"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>Często zadawane pytania dotyczące programu VMware Solution by CloudSimple

Często zadawanych pytań i odpowiedzi na pytania dotyczące programu VMware Solution by CloudSimple, które pomagają zrozumieć, usługi i jak z niej korzystać.  Pytania i odpowiedzi są rozmieszczone w następujących kategoriach.

* CloudSimple Service
* Łączność
* Networking
* Bezpieczeństwo
* Wystąpienia obliczeniowe
* Magazyn
* VMware
* Integracja z platformą Azure
  
## <a name="cloudsimple-service"></a>CloudSimple Service

**Co to jest VMware Solution by CloudSimple?**

**VMware Solution by CloudSimple** przekształca i rozszerza obciążeń oprogramowania VMware do chmur prywatnych, dedykowanych na platformie Azure w ciągu kilku minut. My zajmujemy się inicjowanie obsługi administracyjnej, zarządzanie infrastrukturą i organizowanie obciążeń między lokalną i platformą Azure. Ponieważ Twoje aplikacje działają dokładnie tych samych lokalne i na platformie Azure możesz korzystać z elastyczności i usług, bez złożoności transformować swoje aplikacje w chmurze. CloudSimple zmniejsza całkowity koszt posiadania za pomocą modelu użycia chmury, który uwzględnia na żądanie aprowizacji, płatność jako — liczbę i optymalizacji wydajności.  Zobacz [co to jest rozwiązanie VMware na platformie Azure przez CloudSimple](cloudsimple-vmware-solutions-overview.md) do scenariuszy, korzyści i funkcje.

**Co to jest Chmura prywatna CloudSimple?**

Możesz aprowizować prywatnym, dedykowanym chmury składający się z obliczenia o wysokiej wydajności, Magazyn i sieci środowiska wdrożone w infrastrukturze Azure firmy Microsoft (miejsca sprzętu i centrum danych) w lokalizacji platformy Azure.  Chmura prywatna zapewnia natywne platformę programu VMware "as-a-service". W warunkach VMware każdej chmury prywatnej zawiera dokładnie jedno wystąpienie programu vCenter Server. Serwer vCenter Server zarządza wieloma węzłami ESXi zawarte w co najmniej jeden klaster vSphere, wraz z odpowiedniego magazynu z wirtualną siecią SAN (vSAN). Usługa CloudSimple może zawierać wiele chmur prywatnych w subskrypcji platformy Azure.  Aby uzyskać więcej informacji na temat chmury prywatne, zobacz [omówienie chmury prywatnej](cloudsimple-private-cloud.md).

**Gdzie jest dostępna usługa CloudSimple?**

CloudSimple jest dostępna w regionach wschodnie stany USA i zachodnie stany USA.

**Jak włączyć subskrypcję dla CloudSimple?**

Możesz skontaktować się ze swoim przedstawicielem firmy Microsoft w [ azurevmwaresales@microsoft.com ](mailto:azurevmwaresales@microsoft.com) włączenie subskrypcji usługi CloudSimple. Prześlij identyfikator swojej subskrypcji w taki sposób, w wiadomości e-mail, dla którego ma zostać włączona usługa CloudSimple.  

**Jak uzyskać dostęp do portalu CloudSimple?**

Możesz uzyskać dostęp do portalu CloudSimple z witryny Azure portal.  Zobacz [dostęp do rozwiązań VMware przez portal CloudSimple z witryny Azure portal](https://docs.azure.cloudsimple.com/access-cloudsimple-portal) artykuł, aby uzyskać szczegółowe informacje na temat uzyskiwania dostępu do portalu CloudSimple. 

**Jak zwiększyć pojemność chmury prywatnej?**

Kup węzłów z witryny Azure portal i rozwiń swoje chmurę prywatną na podstawie CloudSimple portal.  Chmury prywatnej można rozszerzyć przez dodanie dodatkowych węzłów do istniejącego klastra vSphere lub przez utworzenie nowego klastra vSphere.  Zobacz [rozwiń chmury prywatnej CloudSimple](https://docs.azure.cloudsimple.com/expand-private-cloud) artykułu w procedurze.

**Co się dzieje Moja Chmura prywatna podczas konserwacji?**

CloudSimple udostępnia okresowe powiadomienia o dni przed zaplanowaną konserwację.  Konserwacja odbywa się bez zakłóceń tak, aby zapewnić dostępność chmury prywatnej.  Konserwacji może być następujących typów:

1. **Infrastruktura CloudSimple:**  Infrastruktura CloudSimple została zaprojektowana by zapewnić wysoką dostępność.  Podczas konserwacji łączność i dostępność chmury prywatnej jest zapewniana przez aktualizowanie nadmiarowych składników w czasie, bez żadnego wpływu.  Masz dostęp do serwera vCenter chmury prywatnej, wszystkie maszyny wirtualne, połączenia internetowego z chmury prywatnej i połączeń w środowisku lokalnym lub na platformie Azure.
2. **CloudSimple Portal:** Podczas konserwacji niektóre funkcje, w portalu CloudSimple nie może być niedostępna lub wyłączone.  Powiadomienie o konserwacji będzie zawierać szczegółowe informacje o co można zrobić w portalu.

## <a name="connectivity"></a>Łączność

**Jakie są moje opcje łączności z siecią region CloudSimple?**

CloudSimple udostępnia trzy różne opcje łączności na nawiązanie połączenia z siecią CloudSimple regionu.  Wszystkie trzy mogą być używane razem.

1. Połączenia ExpressRoute z lokalnego centrum danych do CloudSimple region sieci — szybkie małe opóźnienia bezpiecznego połączenia prywatnego mostkowanie obwód usługi ExpressRoute w środowisku lokalnym przy użyciu obwodu usługi CloudSimple ExpressRoute przy użyciu zasięgu globalnym. Zobacz [nawiązywanie połączenia ze środowiska lokalnego przy użyciu usługi ExpressRoute CloudSimple](https://docs.azure.cloudsimple.com/on-premises-connection) artykuł dotyczący konfigurowania połączenia.
2. Połączenia usługi ExpressRoute z siecią wirtualną platformy Azure z siecią region CloudSimple - o dużej szybkości, małe opóźnienia bezpiecznego połączenia prywatnego łączenie sieci wirtualnej na platformie Azure przy użyciu obwodu usługi CloudSimple ExpressRoute przy użyciu bramy sieci wirtualnej.  Zobacz [środowiska chmury prywatnej CloudSimple nawiązać połączenie z sieci wirtualnej platformy Azure przy użyciu usługi ExpressRoute](https://docs.azure.cloudsimple.com/azure-expressroute-connection) artykuł dotyczący konfigurowania połączenia.
3. Połączenia sieci VPN lokacja-lokacja z lokalnego centrum danych z siecią region CloudSimple — zabezpieczanie wirtualnej sieci prywatnej z lokalnego urządzenia sieci VPN do chmury prywatnej CloudSimple regionu.  Zobacz [Konfigurowanie połączenia sieci VPN między siecią lokalną i chmurą prywatną CloudSimple] artykuł do konfigurowania połączeń sieci VPN.

**Jak połączyć do chmury prywatnej**

Możesz wyświetlić szczegółowe informacje o chmurze prywatnej, w portalu CloudSimple. Aby połączyć serwer vCenter, odpowiadający chmury prywatnej, upewnij się, że połączenie sieciowe jest ustanowionym przy użyciu lokacja-lokacja, punkt-lokacja lub ExpressRoute. Następnie uruchom portal CloudSimple w witrynie Azure portal, a następnie kliknij *Uruchom klienta vSphere* na stronie głównej lub na stronie szczegółów w chmur prywatnych.

**Jakie są zalety obwodu usługi ExpressRoute?**

Obwód usługi ExpressRoute platformy Azure udostępnia bezpieczne połączenie o dużej szybkości małymi opóźnieniami.  CloudSimple zapewnia dedykowanego obwodu usługi ExpressRoute na region na klienta.  Ten obwód można ustanowić bezpiecznego połączenia z lokalnych i/lub subskrypcji platformy Azure.

**Co to są koszty sieci, aby połączyć się z CloudSimple. Wszelkie opłaty za ruch wychodzący z CloudSimple na platformie Azure? W regionach?**

Nie ma opłat za wszelkie wychodzący ruch sieciowy.  Usługa Azure ze standardowymi stawkami na cały ruch wychodzący z sieci wirtualnej lub z obwodem usługi ExpressRoute w środowisku lokalnym.

## <a name="networking"></a>Networking

**Jakie funkcje sieciowe są dostępne dla mojego Chmura prywatna?**

Można inicjować obsługę sieci VLAN (i ich podsieci), zapory tabel i przydzielać publiczne adresy IP i mapowania na maszynę wirtualną działającą w chmurze prywatnej.  Aby uzyskać więcej informacji, zobacz [Przegląd podsieci/sieci VLAN](cloudsimple-vlans-subnets.md), [Omówienie zapory tabel](cloudsimple-firewall-tables.md), i [publiczny adres IP adres Przegląd](cloudsimple-public-ip-address.md) artykułów.

**Jak skonfigurować różne podsieci dla mojej aplikacji w mojej Chmura prywatna?**

Z poziomu portalu CloudSimple, można utworzyć sieci VLAN w chmurze prywatnej.  Po utworzeniu sieci VLAN, możesz utworzyć grupę portu rozproszone na vCenter chmury prywatnej, korzystając z sieci VLAN i utworzyć maszyny wirtualne podłączone do grupy portów rozproszonych.  Można włączyć zapory tabeli dla podsieci/sieci VLAN i zdefiniować reguły zapory, aby zabezpieczyć ruch sieciowy.

**Ustawienia zapory, które są dostępne dla chmur prywatnych?**

Można skonfigurować reguły dla ruchu wschód zachód i północ południe.  Reguły są definiowane w tabeli zapory.  Tabela zapory można dołączyć do VLAN(s) w chmurze prywatnej.  Zobacz [skonfigurować tabele zapory i reguł dla chmur prywatnych](https://docs.azure.cloudsimple.com/firewall) artykuł dotyczący procedury konfigurowania.

**Można przypisać publiczne adresy IP dla maszyn wirtualnych w środowisku Moja Chmura prywatna?**

W portalu CloudSimple łatwo można przydzielić nowego publicznego adresu IP i skojarzyć ją z prywatnego adresu IP maszyny wirtualnej lub urządzenie.  Można również utworzyć nowe reguły zapory lub zastosować istniejące reguły zapory do zezwalania na ruch z określonych portów i/lub określonych zbiór adresów IP w portalu. Zobacz [przydzielić publiczne adresy IP w środowisku chmury prywatnej](https://docs.azure.cloudsimple.com/public-ips) dla procedury konfigurowania.

## <a name="security"></a>Bezpieczeństwo

**Jakie są moje opcje zabezpieczeń na CloudSimple?**

Chmura prywatna CloudSimple zapewnia następujące funkcje zabezpieczeń do zabezpieczania środowiska chmury prywatnej:

1. **Dane szyfrowanie rest**: Umożliwia ona szyfrowanie danych magazynowanych znajdujących się w sieci vSAN magazynu w chmurze prywatnej. sieć vsan, która obsługuje zewnętrzne klucza serwera zarządzania, który można wdrożyć w środowisku platformy Azure sieci wirtualnej lub w środowisku lokalnym.  Zobacz [skonfigurować szyfrowanie sieci vSAN dla chmury prywatnej CloudSimple](https://docs.azure.cloudsimple.com/vsan-encryption) Aby uzyskać więcej informacji.
2. **Zabezpieczenia sieciowe**: Kontrola ruchu sieciowego od i do chmury prywatnej z sieci Internet, lokalnie i w podsieci prywatnej chmury przy użyciu reguł zapory.
3. **Zabezpieczanie połączenia prywatnego**: Bezpieczne prywatne połączenie między siecią lokalną i subskrypcji platformy Azure.

## <a name="compute"></a>Wystąpienia obliczeniowe

**Jakiego rodzaju hostów są dostępne?**

CloudSimple oferuje dwa typy hosta:

* **Węzeł CS28:** CPU:2 x 2,2 GHz, łączna liczba rdzeni 28, 48 zasobników tablicy skrótów.  Pamięć RAM: 256 GB.  Storage: 1600 GB NVMe pamięci podręcznej, 5760 GB danych (samych pamięci Flash). Network: 2x25Gbe NIC
* **Węzeł CS36:** Procesor CPU 2 x 2.3 GHz, 36 łączna liczba rdzeni, 72 zasobników tablicy skrótów.  Pamięć RAM: 512 GB.  Storage: 3200 GB NVMe 11,520 GB danych z pamięci podręcznej (samych pamięci Flash).  Network: 2x25Gbe NIC

**Sposób obsługi błędów sprzętu**

Cała infrastruktura CloudSimple jest stale monitorowane przez platformę CloudSimple i naszych zespołów operacyjnych usługi.  W przypadku wykrycia awarii sprzętu, nowy węzeł zostanie dodany do chmury prywatnej, a nie powiodło się węzeł zostanie usunięty, zapewniając wysoką dostępność chmury prywatnej.

## <a name="storage"></a>Magazyn

**Jakiego rodzaju magazynu jest obsługiwana w chmurze prywatnej?**

Oferuje CloudSimple **magazynu sieci vSAN firmy VMware z samych pamięci flash** z każdej chmury prywatnej.  Każdy vSphere jest tworzony z własną sieć vSAN magazynu danych.  Zobacz [składniki VMware chmury prywatnej — magazynu sieci vSAN](https://docs.azure.cloudsimple.com/vmware-components/#vsan-storage) artykuł, aby uzyskać więcej informacji.

**Czy szyfrowanie danych obsługiwane?**
Tak.  Możesz skonfigurować Magazyn sieci vSAN w chmurze prywatnej można użyć serwera zarządzania kluczami (KMS), który jest wdrożony lokalnie lub na platformie Azure do szyfrowania danych przechowywanych w sieci vSAN

**Sposób obsługi awarii dysków**

Monitorowanie CloudSimple stale monitoruje wszystkie składniki sprzętowe w chmurze prywatnej.  Jeśli wykryciu jakiekolwiek niepowodzenie dysku lub dowolnym dysku jest identyfikowany jako niepowodzenie (oparte na Algorytm heurystyczny), nowy węzeł jest automatycznie dodawany do chmury prywatnej.  Węzeł nie powiodło się lub awarie dysku jest usuwany z chmury prywatnej.

## <a name="vmware"></a>VMware

**Jak wykonać na dużą skalę przekazywania/migrować aplikacje i dane ze środowiska lokalnego?**

CloudSimple zapewnia natywne rozwiązanie vSphere firmy VMware.  Każde narzędzie używane do migracji danych zbiorczego może służyć za pomocą chmury prywatnej CloudSimple.  Niektóre dostępne opcje to:

1. HCX VMware do migracji zbiorcze danych.
2. Migracja zimnych danych przy użyciu narzędzia Storage vMotion ze środowiska lokalnego do CloudSimple.

**Czy można zainstalować narzędzi VMware?**

CloudSimple zapewnia natywne rozwiązanie vSphere firmy VMware.  Każde narzędzie używane do zarządzania vSphere środowisko lokalne może służyć w CloudSimple.  CloudSimple obsługuje model Bring-Your-Own-License (BYOL) dotyczące instalowania narzędzi VMware.

**Jak są aktualizacji i uaktualnień zarządzane?**

CloudSimple zarządza i aktualizuje wszystkie składniki infrastruktury chmury prywatnej w bezproblemowe bez zakłócania.  Wszelkie poprawki aktualizacji lub zabezpieczeń wydane przez dostawców VMware lub infrastruktury zostanie zaplanowana aktualizacja tak szybko, jak kwalifikuje się przez CloudSimple.

CloudSimple nie powoduje wykonania uaktualnienia lub aktualizacje aplikacji zainstalowanych na chmurę prywatną.

## <a name="azure-integration"></a>Integracja z platformą Azure

**Jakie usługi platformy Azure są obsługiwane?**

CloudSimple zapewnia połączenie Azure ExpressRoute subskrypcji na platformie Azure.  Wszystkie usługi działające w Twojej subskrypcji ma łączność sieciową do chmury prywatnej oraz może nawiązać połączenia z chmury prywatnej.  Przykłady:

1. **Usługa Azure Active Directory** jako źródła tożsamości dla serwera vCenter CloudSimple
2. **Usługa Azure storage** do przechowywania kopii zapasowych, obrazów i innych danych z chmury prywatnej
3. **Aplikacje hybrydowe** — można tworzyć architekturę aplikacji, która obejmuje chmur prywatnych i publicznych.  Na przykład można utworzyć serwery sieci Web na platformie Azure, uzyskać dostęp do aplikacji i serwerów baz danych w chmurze prywatnej CloudSimple.
4. **Usługa Azure monitor** i **Centrum zabezpieczeń Azure** — obciążenia działające w programie VMware można ich używać do rejestrowania, metryki wydajności i zarządzanie zabezpieczeniami.

**Sposób mapowania moich dzierżaw VMware na platformę Azure?**

CloudSimple oferuje unikatową funkcję Zarządzanie maszynami wirtualnymi VMware w chmurze prywatnej z witryny Azure portal.  Pula zasobów vCenter (skonfigurowane z ograniczeniami żądanego zasobu) mogą być mapowane do Twojej subskrypcji przez administratora globalnego.  

**Jakie korzyści związanych z licencjonowaniem uzyskać za pomocą platformy Azure?**

CloudSimple możesz skorzystać z korzyści użycia hybrydowego platformy Azure i zaoszczędzić do 90% licencje zachowywanie inwestycji w Licenses firmy Microsoft, zmniejszyć całkowity koszt posiadania w porównaniu do innych chmur. Ponadto Pobierz rozszerzone aktualizacje zabezpieczeń systemu Windows Server 2008 i Microsoft SQL Server 2008.  Niskich kosztów za pomocą Bring Your Own licencji (BYOL) do chmury na potrzeby typowych aplikacji, takich jak usługa Veeam firmy Zerto i inne.  