---
title: Tworzenie klastrów usługi Azure Service Fabric w systemie Windows Server i Linux | Dokumentacja firmy Microsoft
description: Klastry usługi Service Fabric z systemem Windows Server i Linux, co oznacza, że będziesz mieć możliwość wdrażania i hostów dowolnym miejscu aplikacji usługi Service Fabric można uruchomić system Windows Server lub Linux.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/01/2019
ms.author: dekapur
ms.openlocfilehash: d1681aee9dc11f0dbd3133bced0b919a8c1623b8
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670476"
---
# <a name="overview-of-service-fabric-clusters-on-azure"></a>Omówienie usługi Service Fabric clusters na platformie Azure
Klaster usługi Service Fabric to zbiór połączonych z siecią maszyn wirtualnych lub fizycznych, w których mikrousługi są wdrażania i zarządzania nimi. Komputer lub maszynę Wirtualną, która jest częścią klastra, jest nazywana węzłem klastra. Klastry można skalować do tysięcy węzłów. Po dodaniu nowych węzłów do klastra usługi Service Fabric rebalances replik partycji usługi i wystąpień na większą liczbę węzłów. Ogólna zwiększa wydajność aplikacji i zmniejsza rywalizacji o dostęp do pamięci. Jeśli nie są wydajnie używane węzły w klastrze, możesz zmniejszyć liczbę węzłów w klastrze. Usługa Service Fabric ponownie rebalances replik partycji i wystąpień na obniżenie liczby węzłów, aby lepiej wykorzystać możliwości sprzętu na każdym węźle.

Typ węzła definiuje rozmiar, liczbę i właściwości zestaw węzłów (maszyn wirtualnych) w klastrze. Następnie każdy typ węzła może być niezależnie skalowany w górę lub w dół oraz może mieć różne zestawy otwartych portów i różne metryki pojemności. Typy węzłów służą do definiowania ról zestawu węzłów klastra, takich jak „fronton” lub „zaplecze”. Klaster może mieć więcej niż jeden typ węzła, ale podstawowy typ węzła musi obejmować co najmniej pięć maszyn wirtualnych w przypadku klastrów produkcyjnych (lub co najmniej trzy maszyny wirtualne w przypadku klastrów testowych). [Usługi systemowe Service Fabric](service-fabric-technical-overview.md#system-services) są umieszczane w węzłach podstawowego typu. 

## <a name="cluster-components-and-resources"></a>Składniki klastra i zasoby
Klaster usługi Service Fabric na platformie Azure jest zasobem platformy Azure używa, która wchodzi w interakcję z innymi zasobami platformy Azure:
* Maszyny wirtualne i wirtualne karty sieciowe
* zestawy skalowania maszyn wirtualnych
* sieci wirtualnych
* moduły równoważenia obciążenia
* konta magazynu
* publiczne adresy IP

![Klaster usługi Service Fabric][Image]

### <a name="virtual-machine"></a>Maszyna wirtualna
A [maszyny wirtualnej](/azure/virtual-machines/) chociaż z technicznego punktu widzenia węzeł klastra jest proces środowiska uruchomieniowego usługi Service Fabric, który jest częścią klastra jest nazywana węzłem. Każdy węzeł ma przypisaną nazwę węzła (ciąg). Węzły mają właściwości, takie jak [właściwości umieszczania](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints). Każda maszyna lub maszyna wirtualna jest automatycznie uruchamiana usługa *FabricHost.exe*, który zacznie działać w czasie rozruchu, a następnie uruchamia dwa pliki wykonywalne, *Fabric.exe* i *FabricGateway.exe* , które składają się z węzłem. W przypadku wdrożenia produkcyjnego odpowiada jednemu węzłowi na maszynie fizycznej lub wirtualnej. W przypadku testowania scenariuszy może obsługiwać wiele węzłów na jednym komputerze lub maszynie Wirtualnej, uruchamiając wiele wystąpień *Fabric.exe* i *FabricGateway.exe*.

Każda maszyna wirtualna jest skojarzona z kartą interfejsu sieci wirtualnej (NIC) i każda karta sieciowa ma przypisany prywatny adres IP.  Maszyny Wirtualnej jest przypisane do sieci wirtualnej i lokalnym równoważenia za pośrednictwem karty sieciowej.

Wszystkie maszyny wirtualne w klastrze są umieszczane w sieci wirtualnej.  Wszystkie węzły w tym samym zestawie typu/skalowania węzłów są umieszczane w tej samej podsieci w sieci wirtualnej.  Te węzły tylko mają prywatnych adresów IP i nie mogą być adresowane bezpośrednio spoza sieci wirtualnej.  Klienci mieli dostęp do usług w węzłach, za pośrednictwem modułu równoważenia obciążenia platformy Azure.

### <a name="scale-setnode-type"></a>Typ zestawu/node skalowania
Podczas tworzenia klastra, należy zdefiniować co najmniej jeden typ węzła.  Węzły, lub maszyn wirtualnych w typie węzła mają tego samego rozmiaru i charakterystyk takich jak liczba procesorów CPU, pamięci, liczbę dysków i we/wy dysku.  Na przykład jeden typ węzła może być za mały frontonu maszyn wirtualnych z portami Otwórz w Internecie, inny typ węzła może być duże, zaplecza maszyn wirtualnych, które przetwarzają dane. W klastrach platformy Azure, każdy typ węzła jest mapowany na [zestawu skalowania maszyn wirtualnych](/azure/virtual-machine-scale-sets/).

Zestawy skalowania można użyć do wdrożenia i zarządzania kolekcją maszyn wirtualnych jako zestawu. Każdy typ węzła, który definiuje w klastrze usługi Azure Service Fabric konfiguruje zestaw skalowania oddzielne. Środowisko uruchomieniowe usługi Service Fabric jest załadować na każdej maszynie wirtualnej w zestawie skalowania, korzystając z rozszerzenia maszyny Wirtualnej platformy Azure. Można niezależnie skalować każdy typ węzła w górę lub w dół, zmiana jednostki SKU systemu operacyjnego, uruchomione w każdym węźle klastra, mają różne zestawy otwartych portów i użyj różne metryki pojemności. Zestaw skalowania ma pięć [uaktualnień](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains) do pięciu [domeny błędów](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains) i może mieć maksymalnie 100 maszyn wirtualnych.  Klastry z więcej niż 100 węzłów tworzy się przez utworzenie wielu typów zestawów/node skali.

> [!IMPORTANT]
> Wybór liczba typów węzłów klastra oraz właściwości poszczególnych typów węzłów (rozmiar podstawowej, połączone z Internetem, liczbę maszyn wirtualnych, itp.) jest ważnym zadaniem.  Aby uzyskać więcej informacji, przeczytaj [zagadnienia dotyczące planowania pojemności klastra](service-fabric-cluster-capacity.md).

Aby uzyskać więcej informacji, przeczytaj [zestawów skalowania maszyn wirtualnych i typy węzłów usługi Service Fabric](service-fabric-cluster-nodetypes.md).

### <a name="azure-load-balancer"></a>Azure Load Balancer
Wystąpienia maszyn wirtualnych są łączone za [równoważenia obciążenia platformy Azure](/azure/load-balancer/load-balancer-overview), który jest skojarzony z [publiczny adres IP](/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses) i etykiety DNS.  Podczas aprowizacji klastra z  *&lt;clustername&gt;*, nazwę DNS  *&lt;clustername&gt;.&lt; Lokalizacja&gt;. cloudapp.azure.com* jest etykieta DNS skojarzone z modułem równoważenia obciążenia przed zestawem skalowania.

Maszyny wirtualne w klastrze mają tylko [prywatnych adresów IP](/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses).  Ruch związany z zarządzaniem i ruchu w ramach usługi są przesyłane za pośrednictwem publiczny moduł równoważenia obciążenia.  Ruch sieciowy jest kierowany do tych maszyn przy użyciu reguły translatora adresów Sieciowych (klienci łączą się z określonych węzłów/wystąpień) lub reguły równoważenia obciążenia (ruch jest przesyłany do maszyn wirtualnych działanie okrężne).  Moduł równoważenia obciążenia ma skojarzonego publicznego adresu IP z nazwą DNS, w formacie:  *&lt;clustername&gt;.&lt; Lokalizacja&gt;. cloudapp.azure.com*.  Publiczny adres IP jest inny zasób platformy Azure w grupie zasobów.  Jeśli zdefiniujesz wiele typów węzłów w klastrze równoważenia obciążenia jest tworzony dla każdego zestawu typu/skalowania węzła. Alternatywnie możesz skonfigurować moduł równoważenia obciążenia dla wielu typów węzłów.  Podstawowy typ węzła jest etykieta DNS  *&lt;clustername&gt;.&lt; Lokalizacja&gt;. cloudapp.azure.com*, inne typy węzłów mieć etykietę DNS  *&lt;clustername&gt;-&lt;nodetype&gt;.&lt; Lokalizacja&gt;. cloudapp.azure.com*.

### <a name="storage-accounts"></a>Konta magazynu
Każdy typ węzła klastra jest obsługiwana przez [konta usługi Azure storage](/azure/storage/common/storage-introduction) i dyski zarządzane.

## <a name="cluster-security"></a>Zabezpieczenia klastra
Klaster usługi Service Fabric jest zasobem, którego jesteś właścicielem.  Jest odpowiedzialny za Zabezpieczanie klastrów zapobiega nieautoryzowanym użytkownikom nawiązywanie połączeń z nich. Zabezpieczonego klastra jest szczególnie ważne w przypadku obciążeń produkcyjnych są uruchomione w klastrze. 

### <a name="node-to-node-security"></a>Węzeł węzeł zabezpieczeń
Węzeł węzeł zabezpieczeń zabezpiecza komunikację między maszynami wirtualnymi lub komputerów w klastrze. W tym scenariuszu zabezpieczeń gwarantuje, że tylko te komputery, które są autoryzowane do przyłączenia się do klastra można uczestniczyć w hostingu aplikacji i usług w klastrze. Usługa Service Fabric używa certyfikatów X.509 do zabezpieczenia klastra i udostępnia funkcje zabezpieczeń aplikacji.  Certyfikat klastra jest wymagana do zabezpieczania ruchu klastra i klastra i serwera uwierzytelniania.  Podpisany — certyfikaty samodzielnie mogą być używane w przypadku klastrów testowych, ale certyfikatu z zaufanego urzędu certyfikacji powinien służyć do zabezpieczenia klastrów produkcyjnych.

Aby uzyskać więcej informacji, przeczytaj [zabezpieczeń między węzłami](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>Węzeł klienta zabezpieczeń
Węzeł klienta zabezpieczeń uwierzytelnia klientów i pomaga bezpiecznej komunikacji między klientem a poszczególnych węzłów w klastrze. Ten typ zabezpieczeń pomaga upewnić się, że tylko autoryzowani użytkownicy mogą dostęp do klastra i aplikacje, które są wdrażane w klastrze. Klienci są jednoznacznie identyfikowany przy użyciu jednej z ich poświadczeń zabezpieczeń certyfikatu X.509. Dowolną liczbę certyfikatów klienta opcjonalnie może służyć do uwierzytelniania klientów administratora lub użytkownika z klastrem.

Oprócz certyfikaty klienta można również skonfigurować do uwierzytelniania klientów z klastrem usługi Azure Active Directory.

Aby uzyskać więcej informacji, przeczytaj [węzeł klienta zabezpieczeń](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach
Kontrola dostępu oparta na rolach (RBAC) umożliwia przypisywanie kontroli dostępu w zakresie dla zasobów platformy Azure.  Reguły dostępu do różnych można przypisać do subskrypcji, grupy zasobów i zasobów.  Zasady RBAC są dziedziczone wzdłuż hierarchii zasobu, chyba że zastąpione na niższym poziomie.  Można przypisać dowolny użytkownik lub grup użytkowników na usługi AAD za pomocą reguł RBAC tak, aby wyznaczonym użytkownikom i grupy mogą modyfikować klastra.  Aby uzyskać więcej informacji, przeczytaj [RBAC platformy Azure — omówienie](/azure/role-based-access-control/overview).

Usługa Service Fabric obsługuje także kontroli dostępu, aby ograniczyć dostęp do pewnych operacji klastra dla różnych grup użytkowników. Dzięki temu zabezpieczeniu klastra. Dwa typy kontroli dostępu są obsługiwane w przypadku klientów nawiązujących połączenie z klastrem: Rola administratora i roli użytkownika.  

Aby uzyskać więcej informacji, przeczytaj [Service Fabric Role-Based kontroli dostępu (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac).

### <a name="network-security-groups"></a>Grupy zabezpieczeń sieci 
Sieciowe grupy zabezpieczeń (NSG) kontrolować ruch przychodzący i wychodzący w podsieci, maszyny Wirtualnej lub określonej karty sieciowej.  Domyślnie gdy wiele maszyn wirtualnych są umieszczone w tej samej sieci wirtualnej można komunikują się ze sobą za pośrednictwem dowolnego portu.  Jeśli chcesz ograniczyć komunikację między maszynami, można zdefiniować sieciowych grup zabezpieczeń do segmentu sieci lub izolowania maszyn wirtualnych od siebie nawzajem.  Jeśli masz wiele typów węzłów w klastrze, można zastosować sieciowe grupy zabezpieczeń do podsieci, aby zapobiec maszyn należących do różnych typów węzłów komunikowanie się ze sobą.  

Aby uzyskać więcej informacji, przeczytaj temat [grup zabezpieczeń](/azure/virtual-network/security-overview)

## <a name="scaling"></a>Skalowanie

Zmień zapotrzebowania aplikacji wraz z upływem czasu. Może być konieczne zwiększenie zasobów klastra aplikacji zwiększenia obciążenia lub sieci natężenia ruchu lub zmniejszanie zasobów klastra, gdy zapotrzebowanie maleje. Po utworzeniu klastra usługi Service Fabric można skalować klastra w poziomie (zmienić liczbę węzłów), czy w pionie (zmienić zasoby węzłów). Możesz skalować klastra w dowolnym momencie, nawet gdy działają obciążenia w klastrze. Jak jest skalowana w klastrze, aplikacje będą skalowane automatycznie również.

Aby uzyskać więcej informacji, przeczytaj [Azure skalowanie klastrów](service-fabric-cluster-scaling.md).

## <a name="upgrading"></a>Uaktualnianie
Klaster usługi Azure Service Fabric jest zasobem, który właścicielem, ale jest częściowo zarządzany przez firmę Microsoft. Firma Microsoft jest odpowiedzialna za poprawek podstawowego systemu operacyjnego i przeprowadzania uaktualnienia środowiska uruchomieniowego usługi Service Fabric w klastrze. Możesz ustawić do klastra, aby otrzymywać aktualizacje automatyczne środowiska uruchomieniowego, gdy firma Microsoft publikuje nową wersję lub wybrać wersję obsługiwane środowisko uruchomieniowe, która ma. Oprócz uaktualniania środowiska uruchomieniowego ale też aktualizować konfiguracji klastra, takie jak certyfikaty lub porty aplikacji.

Aby uzyskać więcej informacji, przeczytaj [uaktualniania klastrów](service-fabric-cluster-upgrade.md).

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne
Jesteś w stanie Tworzenie klastrów na maszynach wirtualnych z tymi systemami operacyjnymi:

* Windows Server 2012 R2
* Windows Server 2016 
* Windows Server 1709
* System Windows Server w wersji 1803
* Linux Ubuntu 16.04
* Red Hat Enterprise Linux 7.4 (Obsługa wersji zapoznawczej)

> [!NOTE]
> Jeśli zdecydujesz się wdrożyć usługi Service Fabric w systemie Windows Server w wersji 1709, należy pamiętać, że (1) nie jest długoterminowej, gałąź, obsługi, więc może trzeba będzie przenosić w przyszłości wersji i (2) Jeśli wdrażanie kontenerów, kontenery utworzone w systemie Windows Server 2016 nie działają w systemie Windows Server  1709 i na odwrót (trzeba będzie ponownie skompilować je wdrażać).
>


## <a name="next-steps"></a>Kolejne kroki
Przeczytaj więcej na temat [Zabezpieczanie](service-fabric-cluster-security.md), [skalowanie](service-fabric-cluster-scaling.md), i [uaktualnianie](service-fabric-cluster-upgrade.md) klastrów platformy Azure.

Dowiedz się więcej o [opcje pomocy technicznej usługi Service Fabric](service-fabric-support.md).

[Image]: media/service-fabric-azure-clusters-overview/Cluster.PNG