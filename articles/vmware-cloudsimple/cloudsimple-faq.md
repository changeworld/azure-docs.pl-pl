---
title: Często zadawane pytania — VMware Solution by CloudSimple
description: Często zadawane pytania dotyczące usługi Azure VMware Solution by CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 05/24/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e187a4a75a27e206a632388f1e20a94da032eb08
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595340"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>Często zadawane pytania dotyczące programu VMware Solution by CloudSimple

Często zadawanych pytań i odpowiedzi na pytania dotyczące usługi Azure VMware Solution by CloudSimple, które pomagają zrozumieć, usługi i jak z niej korzystać. Pytania i odpowiedzi są rozmieszczone w następujących kategoriach:

* Usługa CloudSimple
* Łączność
* Networking
* Bezpieczeństwo
* Wystąpienia obliczeniowe
* Magazyn
* VMware
* Integracja z platformą Azure
 
## <a name="cloudsimple-service"></a>Usługa CloudSimple

**Co to jest Azure VMware Solution by CloudSimple?**

Rozwiązanie programu VMware na platformie Azure przez CloudSimple przekształca i rozszerza obciążeń oprogramowania VMware do chmur prywatnych, dedykowanych na platformie Azure w ciągu kilku minut. Rozwiązanie obsługuje zarządza infrastrukturą i organizuje obciążeń między lokalną i platformą Azure. Ponieważ Twoje aplikacje działają dokładnie tych samych lokalne i na platformie Azure możesz korzystać z elastyczności i usług, bez złożoności transformować swoje aplikacje w chmurze. CloudSimple zmniejsza całkowity koszt posiadania za pomocą modelu użycia chmury, który uwzględnia na żądanie aprowizacji, płatność jako — liczbę i optymalizacji wydajności. Funkcje, korzyści i scenariuszy, zobacz [co to jest Azure VMware Solution by CloudSimple?](cloudsimple-vmware-solutions-overview.md).

**Co to jest Chmura prywatna CloudSimple?**

Możesz aprowizować prywatnym, dedykowanym chmury, który składa się z wysokowydajnych obliczeń, magazynu i środowisko sieciowe, wdrożone w infrastrukturze Azure firmy Microsoft (miejsca sprzętu i centrum danych) w lokalizacji platformy Azure. Chmura prywatna zapewnia natywne platformie VMware jako usługa. W warunkach VMware każdej chmury prywatnej zawiera dokładnie jedno wystąpienie programu vCenter Server. Serwer vCenter Server zarządza wiele węzłów ESXi zawarte w co najmniej jeden klaster vSphere, wraz z odpowiedniego magazynu sieci vSAN. Usługa CloudSimple może zawierać wiele chmur prywatnych w subskrypcji platformy Azure. Aby uzyskać więcej informacji na temat chmury prywatne, zobacz [omówienie chmury prywatnej](cloudsimple-private-cloud.md).

**Gdzie jest dostępna usługa CloudSimple?**

CloudSimple jest dostępna w regionach wschodnie stany USA i zachodnie stany USA.

**Jak włączyć subskrypcję dla CloudSimple?**

Skontaktuj się z przedstawicielem firmy Microsoft w [ azurevmwaresales@microsoft.com ](mailto:azurevmwaresales@microsoft.com) włączenie subskrypcji usługi CloudSimple. Prześlij identyfikator swojej subskrypcji w taki sposób, w wiadomości e-mail, dla którego ma zostać włączona usługa CloudSimple. 

**Jak uzyskać dostęp do portalu CloudSimple?**

Możesz uzyskać dostęp do portalu CloudSimple w witrynie Azure portal. Aby uzyskać informacje o tym, jak uzyskać dostęp do portalu CloudSimple, zobacz [dostęp do rozwiązań VMware przez portal CloudSimple w witrynie Azure portal](https://docs.azure.cloudsimple.com/access-cloudsimple-portal).

**Jak zwiększyć pojemność chmury prywatnej?**

Aprowizowanie węzłów w witrynie Azure portal i rozwiń chmury prywatnej z poziomu portalu CloudSimple. Chmury prywatnej można rozszerzyć, dodając węzły w istniejącym klastrze vSphere lub tworzenia nowego klastra vSphere. Aby uzyskać informacji na temat procedury, zobacz [rozwiń chmury prywatnej CloudSimple](https://docs.azure.cloudsimple.com/expand-private-cloud).

**Co się dzieje Moja Chmura prywatna podczas konserwacji?**

CloudSimple udostępnia powiadomienia cykliczne dni przed zaplanowaną konserwację. Konserwacja odbywa się bez tak, aby zapewnić dostępność chmury prywatnej. Konserwacji może być następujących typów:

- **Infrastruktura CloudSimple**: Infrastruktura CloudSimple została zaprojektowana by zapewnić wysoką dostępność. Podczas konserwacji łączność i dostępność chmury prywatnej jest zapewniana przez aktualizowanie nadmiarowych składników w czasie, bez żadnego wpływu. Masz dostęp do serwera vCenter chmury prywatnej, wszystkie maszyny wirtualne, połączenia internetowego z chmury prywatnej i połączeń w środowisku lokalnym lub na platformie Azure.
- **CloudSimple portal**: Podczas konserwacji niektóre funkcje, w portalu CloudSimple nie mogą być niedostępne lub mogą być wyłączone. Powiadomienie o konserwacji zawiera informacje na temat co można zrobić w portalu.

## <a name="connectivity"></a>Łączność

**Jakie są moje opcje łączności sieci region CloudSimple?**

CloudSimple udostępnia trzy różne opcje łączności na nawiązanie połączenia z siecią CloudSimple regionu. Wszystkie trzy opcje mogą być używane razem:

- Połączenia usługi Azure ExpressRoute z centrum danych lokalnych z siecią region CloudSimple: Szybkie o małych opóźnieniach bezpieczne połączenie prywatne pomost pomiędzy aplikacją obwód usługi ExpressRoute w środowisku lokalnym za pomocą obwód usługi CloudSimple ExpressRoute przy użyciu zasięgu globalnym. Aby skonfigurować połączenie, zobacz [nawiązywanie połączenia ze środowiska lokalnego CloudSimple przy użyciu usługi ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection).
- Połączenie usługi ExpressRoute z siecią wirtualną platformy Azure do sieci CloudSimple regionu: O dużej szybkości i niskim opóźnieniu bezpieczne połączenie prywatne pomost pomiędzy aplikacją sieci wirtualnej na platformie Azure przy użyciu obwodu usługi CloudSimple ExpressRoute przy użyciu bramy sieci wirtualnej. Aby skonfigurować połączenie, zobacz [nawiązywanie CloudSimple środowiska chmury prywatnej sieci wirtualnej platformy Azure przy użyciu usługi ExpressRoute](https://docs.azure.cloudsimple.com/azure-expressroute-connection).
- Lokacja lokacja połączenie sieci VPN z lokalnego centrum danych do sieci CloudSimple regionu: Bezpiecznej wirtualnej sieci prywatnej z lokalnego urządzenia sieci VPN do regionu CloudSimple chmury prywatnej. Aby skonfigurować połączenie, zobacz [Konfigurowanie połączenia sieci VPN między siecią lokalną i chmurą prywatną CloudSimple](https://docs.azure.cloudsimple.com/set-up-vpn).

**Jak połączyć do chmury prywatnej**

Możesz wyświetlić szczegółowe informacje o chmurze prywatnej, w portalu CloudSimple. Aby połączyć z programem vCenter, który odnosi się do chmury prywatnej, upewnij się, że nawiązano połączenie z siecią przy użyciu lokacja lokacja, punkt lokacja lub ExpressRoute. Następnie uruchom portal CloudSimple w witrynie Azure portal. Wybierz **Uruchom klienta vSphere** na stronie głównej lub na stronie szczegółów w chmurze prywatnej.

**Jakie są zalety obwodu usługi ExpressRoute?**

Obwód usługi ExpressRoute systemu Azure udostępnia bezpieczne połączenie o dużej szybkości i małych opóźnieniach. CloudSimple zapewnia dedykowanego obwodu usługi ExpressRoute na region na klienta. Ten obwód można ustanowić bezpiecznego połączenia z magazynami lokalnymi i subskrypcji platformy Azure.

**Co to są koszty sieci i nawiąż połączenie z CloudSimple? Czy istnieją wszystkie opłaty za ruch wychodzący do i z CloudSimple na platformie Azure? Czy istnieją wszystkie opłaty za ruch wychodzący w regionach?**

Nie ma opłat za wychodzący ruch sieciowy. Usługa Azure ze standardowymi stawkami na cały ruch wychodzący z sieci wirtualnej lub z obwodem usługi ExpressRoute w środowisku lokalnym.

## <a name="networking"></a>Networking

**Jakie funkcje sieciowe są dostępne dla mojego chmury prywatnej?**

Możesz aprowizować sieci VLAN i podsieci oraz tabele zapory. Możesz przydzielać publiczne adresy IP i mapowania na maszynę wirtualną, która działa w chmurze prywatnej. Aby uzyskać więcej informacji, zobacz [omówienie sieci VLAN i podsieci](cloudsimple-vlans-subnets.md), [Omówienie tabel zapory](cloudsimple-firewall-tables.md), i [Przegląd adres publiczny adres IP](cloudsimple-public-ip-address.md).

**Jak skonfigurować różne podsieci dla mojej aplikacji w mojej chmury prywatnej?**

Z poziomu portalu CloudSimple, można utworzyć sieci VLAN w chmurze prywatnej. Po utworzeniu sieci VLAN, można utworzyć grupę portu rozproszone na vCenter chmury prywatnej za pomocą sieci VLAN i utworzyć maszyny wirtualne podłączone do grupy portów rozproszonych. Można włączyć tabeli zapory dla sieci VLAN i podsieci i zdefiniowania reguł zapory, aby zabezpieczyć ruch sieciowy.

**Jakie ustawienia zapory są dostępne dla mojego chmur prywatnych?**

Można skonfigurować reguły dla ruchu wschód zachód i północ południe. Reguły są definiowane w tabeli zapory. Tabela zapory można dołączyć do sieci VLAN w chmurze prywatnej. Procedura konfiguracji w temacie [Konfigurowanie zapory tabel i reguły dla chmur prywatnych](https://docs.azure.cloudsimple.com/firewall).

**Można przypisać publiczne adresy IP dla maszyn wirtualnych w środowisku chmury prywatnej, Moje?**

W portalu CloudSimple łatwo można przydzielić nowego publicznego adresu IP i skojarzyć ją z prywatnego adresu IP maszyny wirtualnej lub urządzenie. Możesz również utworzyć nowe reguły zapory lub Zastosuj istniejące reguły zapory zezwalające na ruch do określonych portów i określone zestawy adresów IP w portalu. Procedura konfiguracji w temacie [przydzielić publiczne adresy IP w środowisku chmury prywatnej](https://docs.azure.cloudsimple.com/public-ips).

## <a name="security"></a>Bezpieczeństwo

**Jakie są moje opcje zabezpieczeń na CloudSimple?**

CloudSimple Chmura prywatna zapewnia następujące funkcje zabezpieczeń do zabezpieczania środowiska chmury prywatnej:

- **Dane szyfrowanie rest:** Umożliwia ona szyfrowanie danych magazynowanych, który znajduje się w sieci vSAN magazynu w chmurze prywatnej. sieć vsan, która obsługuje zewnętrzne klucza serwera zarządzania, który można wdrożyć w środowisku platformy Azure wirtualnych sieci lub w środowisku lokalnym. Aby uzyskać więcej informacji, zobacz [skonfigurować szyfrowanie sieci vSAN dla chmury prywatnej CloudSimple](https://docs.azure.cloudsimple.com/vsan-encryption).
- **Zabezpieczenia sieciowe:** Kontrola ruchu sieciowego z i do chmury prywatnej przez Internet, lokalnie i w ramach podsieci chmury prywatnej za pomocą reguł zapory.
- **Bezpieczne, prywatne połączenie:** Bezpieczne, prywatne połączenie między siecią lokalną i subskrypcji platformy Azure.

## <a name="compute"></a>Wystąpienia obliczeniowe

**Jakiego rodzaju hostów są dostępne?**

CloudSimple oferuje dwa typy hosta:

* **Węzeł CS28**: CPU:2 x 2,2 GHz, łączna liczba rdzeni 28, 48 zasobników tablicy skrótów. Pamięć RAM: 256 GB. Storage: Pamięci podręcznej NVMe 1600 GB, 5760 GB danych (samych pamięci Flash). Network: 2x25Gbe NIC.
* **Węzeł CS36**: Procesor CPU 2 x 2.3 GHz, łączna liczba rdzeni 36, 72 zasobników tablicy skrótów. Pamięć RAM: 512 GB. Storage: Dane 11,520 GB pamięci podręcznej NVMe 3200 GB (samych pamięci Flash). Network: 2x25Gbe NIC.

**Sposób obsługi awarii sprzętu**

Cała infrastruktura CloudSimple stale jest monitorowana przez platformę CloudSimple i jej zespołom usługi. W przypadku wykrycia awarii sprzętu, nowy węzeł zostanie dodany do chmury prywatnej. Aby zapewnić wysoką dostępność chmury prywatnej usunięciem węzła nie powiodło się.

## <a name="storage"></a>Magazyn

**Jakiego rodzaju magazynu jest obsługiwana w prywatnej chmurze?**

Oferuje CloudSimple **magazynu sieci vSAN firmy VMware z samych pamięci flash** z każdej chmury prywatnej. Każdy vSphere jest tworzony z własną sieć vSAN magazynu danych. Aby uzyskać więcej informacji, zobacz [składników VMware chmury prywatnej — magazynu sieci vSAN](https://docs.azure.cloudsimple.com/vmware-components/#vsan-storage).

**Czy szyfrowanie danych obsługiwane?**
Tak. Możesz skonfigurować Magazyn sieci vSAN w chmurze prywatnej można użyć serwera zarządzania kluczami (KMS), który jest wdrożony lokalnie lub na platformie Azure do szyfrowania danych przechowywanych w sieci vSAN.

**Sposób obsługi awarii dysków**

Monitorowanie CloudSimple stale monitoruje wszystkie składniki sprzętowe w chmurze prywatnej. Jeśli zostanie wykryta awaria dysku lub dysku jest identyfikowany jako przechodzenie w oparciu o Algorytm heurystyczny, nowy węzeł jest automatycznie dodawany do chmury prywatnej. Węzeł o dysku nie powiodło się lub awarie jest usuwany z chmury prywatnej.

## <a name="vmware"></a>VMware

**Jak wykonać przekazywanie dużej skali i migrację danych i aplikacji ze środowiska lokalnego?**

CloudSimple zapewnia natywne rozwiązanie vSphere firmy VMware. Każde narzędzie używane do migracji danych zbiorczego może służyć za pomocą CloudSimple chmury prywatnej. Niektóre dostępne opcje to:

- HCX VMware do migracji zbiorcze danych.
- Migrację zimnych danych przy użyciu narzędzia Storage vMotion ze środowiska lokalnego do CloudSimple.

**Czy można zainstalować narzędzi VMware?**

CloudSimple zapewnia natywne rozwiązanie vSphere firmy VMware. Dowolne narzędzie używane do zarządzania środowiskiem vSphere, mogą być używane w środowisku lokalnym na CloudSimple. Instalowanie narzędzi VMware CloudSimple obsługuje model bring-your-own-license (BYOL).

**Jak są aktualizacji i uaktualnień zarządzane?**

CloudSimple zarządza i aktualizuje wszystkie składniki infrastruktury chmury prywatnej w sposób bez bezproblemowe. Wszelkie poprawki aktualizacji lub zabezpieczeń wydane przez dostawców VMware lub infrastruktury zaplanowano do aktualizacji tak szybko, jak kwalifikuje się przez CloudSimple.

CloudSimple nie wykonuje uaktualnienia lub aktualizacje aplikacji zainstalowanych na chmurę prywatną.

## <a name="azure-integration"></a>Integracja z platformą Azure

**Jakie usługi platformy Azure są obsługiwane?**

CloudSimple zapewnia połączenie Azure ExpressRoute subskrypcji na platformie Azure. Wszystkie usługi, które są uruchamiane w ramach subskrypcji mają łączność sieciową do chmury prywatnej oraz może nawiązać połączenia z chmury prywatnej. Przykłady:

- **Azure Active Directory**: Usługa Azure Active Directory jako źródła tożsamości dla serwera vCenter CloudSimple.
- **Azure Storage**: Użyj magazynu do przechowywania kopii zapasowych, obrazów i innych danych z chmury prywatnej.
- **Aplikacje hybrydowe**: Można tworzyć architekturę aplikacji, która obejmuje chmur prywatnych i publicznych. Na przykład tworzenia serwerów sieci web na platformie Azure tego dostępu do aplikacji i serwery baz danych na chmurę prywatną CloudSimple.
- **Usługa Azure Monitor** i **usługi Azure Security Center**: Obciążenia, które jest uruchamiane w oprogramowaniu firmy VMware można użyć Centrum zabezpieczeń i monitorowania rejestrowania, metryki wydajności i zarządzanie zabezpieczeniami.

**Sposób mapowania moich dzierżaw VMware na platformę Azure?**

CloudSimple oferuje unikatową funkcję Zarządzanie maszynami wirtualnymi VMware w chmurze prywatnej w witrynie Azure portal. Puli zasobów programu vCenter jest skonfigurowany z ograniczeń zasobów, które mają być mapowane do Twojej subskrypcji przez administratora globalnego. 

**Jakie korzyści związanych z licencjonowaniem uzyskać za pomocą platformy Azure?**

CloudSimple możesz skorzystać z korzyści użycia hybrydowego platformy Azure i zaoszczędź nawet o 90 procent na licencje, aby zachować inwestycji w licencje firmy Microsoft i obniżyć całkowity koszt posiadania w porównaniu do innych chmur. Możesz również uzyskać rozszerzone aktualizacje zabezpieczeń systemu Windows Server 2008 i Microsoft SQL Server 2008. Niskich kosztów za pomocą BYOL do chmury na potrzeby typowych aplikacji, takich jak Veeam, Zerto i inne. 
