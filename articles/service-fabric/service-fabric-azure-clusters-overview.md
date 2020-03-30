---
title: Tworzenie klastrów w systemach Windows Server i Linux
description: Klastry sieci szkieletowej usług działają w systemach Windows Server i Linux, co oznacza, że będziesz mógł wdrażać i hostować aplikacje sieci szkieletowej usług w dowolnym miejscu, w którym można uruchomić system Windows Server lub Linux.
services: service-fabric
documentationcenter: .net
author: dkkapur
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: dekapur
ms.openlocfilehash: b6942c2a0647401df0d88b83e1b144ca3207a6db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614676"
---
# <a name="overview-of-service-fabric-clusters-on-azure"></a>Omówienie klastrów sieci szkieletowej usług na platformie Azure
Klaster sieci szkieletowej usług to połączony z siecią zestaw maszyn wirtualnych lub fizycznych, na których mikrousługi są wdrażane i zarządzane. Maszyna lub maszyna wirtualna, która jest częścią klastra jest nazywany węzłem klastra. Klastry można skalować do tysięcy węzłów. Jeśli dodasz nowe węzły do klastra, sieci szkieletowej usług równoważe repliki partycji usługi i wystąpienia w zwiększonej liczbie węzłów. Ogólna wydajność aplikacji poprawia i rywalizacji o dostęp do pamięci zmniejsza. Jeśli węzły w klastrze nie są używane efektywnie, można zmniejszyć liczbę węzłów w klastrze. Sieci szkieletowej usług ponownie równoważy repliki partycji i wystąpień w całej zmniejszonej liczby węzłów, aby lepiej wykorzystać sprzęt w każdym węźle.

Typ węzła definiuje rozmiar, liczbę i właściwości zestawu węzłów (maszyn wirtualnych) w klastrze. Następnie każdy typ węzła może być niezależnie skalowany w górę lub w dół oraz może mieć różne zestawy otwartych portów i różne metryki pojemności. Typy węzłów służą do definiowania ról zestawu węzłów klastra, takich jak „fronton” lub „zaplecze”. Klaster może mieć więcej niż jeden typ węzła, ale podstawowy typ węzła musi obejmować co najmniej pięć maszyn wirtualnych w przypadku klastrów produkcyjnych (lub co najmniej trzy maszyny wirtualne w przypadku klastrów testowych). [Usługi systemowe Service Fabric](service-fabric-technical-overview.md#system-services) są umieszczane w węzłach podstawowego typu. 

## <a name="cluster-components-and-resources"></a>Składniki i zasoby klastra
Klaster sieci szkieletowej usług na platformie Azure to zasób platformy Azure, który używa innych zasobów platformy Azure i współdziała z nimi:
* Maszyny wirtualne i wirtualne karty sieciowe
* zestawy skalowania maszyn wirtualnych
* sieci wirtualnych
* moduły równoważenia obciążenia
* konta magazynowania
* publiczne adresy IP

![Klaster sieci szkieletowej usług][Image]

### <a name="virtual-machine"></a>Maszyna wirtualna
[Maszyna wirtualna,](/azure/virtual-machines/) która jest częścią klastra jest nazywany węzłem, choć technicznie węzeł klastra jest procesem wykonywania sieci szkieletowej usług. Każdy węzeł ma przypisaną nazwę węzła (ciąg). Węzły mają cechy, takie jak [właściwości umieszczania](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints). Każdy komputer lub maszyna wirtualna ma usługę automatycznego *uruchamiania, FabricHost.exe*, która rozpoczyna pracę w czasie rozruchu, a następnie uruchamia dwa pliki wykonywalne, *Fabric.exe* i *FabricGateway.exe*, które tworzą węzeł. Wdrożenie produkcyjne to jeden węzeł na maszynę fizyczną lub wirtualną. W przypadku scenariuszy testowych można obsługiwać wiele węzłów na jednym komputerze lub maszynie wirtualnej, uruchamiając wiele wystąpień *fabric.exe* i *FabricGateway.exe*.

Każda maszyna wirtualna jest skojarzona z kartą wirtualnego interfejsu sieciowego (NIC), a każdej karcie sieciowej jest przypisywany prywatny adres IP.  Maszyna wirtualna jest przypisana do sieci wirtualnej i saldara lokalnego za pośrednictwem karty sieciowej.

Wszystkie maszyny wirtualne w klastrze są umieszczane w sieci wirtualnej.  Wszystkie węzły w tym samym typie/skalowaniu węzła są umieszczane w tej samej podsieci w sieci wirtualnej.  Te węzły mają tylko prywatne adresy IP i nie są bezpośrednio adresowalne poza siecią wirtualną.  Klienci mogą uzyskiwać dostęp do usług w węzłach za pośrednictwem modułu równoważenia obciążenia platformy Azure.

### <a name="scale-setnode-type"></a>Zestaw skalowania/typ węzła
Podczas tworzenia klastra można zdefiniować jeden lub więcej typów węzłów.  Węzły lub maszyny wirtualne w typie węzła mają taki sam rozmiar i właściwości, takie jak liczba procesorów, pamięć, liczba dysków i we/wy dysku.  Na przykład jeden typ węzła może być dla małych, front-end maszyn wirtualnych z portami otwartymi do Internetu, podczas gdy inny typ węzła może być dla dużych, zaplecza maszyn wirtualnych, które przetwarzają dane. W klastrach platformy Azure każdy typ węzła jest mapowany na [zestaw skalowania maszyny wirtualnej.](/azure/virtual-machine-scale-sets/)

Zestawy skalowania można używać do wdrażania i zarządzania kolekcją maszyn wirtualnych jako zestawu. Każdy typ węzła zdefiniowany w klastrze sieci szkieletowej usług Azure konfiguruje oddzielny zestaw skalowania. Środowisko uruchomieniowe sieci szkieletowej usług jest uruchamiane na każdej maszynie wirtualnej w zestawie skalowania przy użyciu rozszerzeń maszyn wirtualnych platformy Azure. Można niezależnie skalować każdy typ węzła w górę lub w dół, zmienić jednostkę SKU systemu operacyjnego działającą w każdym węźle klastra, mieć otwarte różne zestawy portów i używać różnych metryk pojemności. Zestaw skalowania ma pięć [domen uaktualnienia](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains) i pięć [domen błędów](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains) i może mieć maksymalnie 100 maszyn wirtualnych.  Klastry zawierające więcej niż 100 węzłów można utworzyć, tworząc wiele zestawów skalowania/typów węzłów.

> [!IMPORTANT]
> Ważnym zadaniem jest wybranie liczby typów węzłów dla klastra i właściwości każdego typu węzła (rozmiar, podstawowy, internetowy, liczba maszyn wirtualnych itp.).  Aby uzyskać więcej informacji, przeczytaj [zagadnienia dotyczące planowania pojemności klastra](service-fabric-cluster-capacity.md).

Aby uzyskać więcej informacji, przeczytaj [artykuł Typy węzłów sieci szkieletowej usług i zestawy skalowania maszyny wirtualnej](service-fabric-cluster-nodetypes.md).

### <a name="azure-load-balancer"></a>Azure Load Balancer
Wystąpienia maszyn wirtualnych są dołączane za [modułem równoważenia obciążenia platformy Azure,](/azure/load-balancer/load-balancer-overview)który jest skojarzony z [publicznym adresem IP](/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses) i etykietą DNS.  Podczas aprowizowania klastra * &lt;&gt;* z nazwą klastra , nazwą DNS, * &lt;nazwą klastra&gt;.&lt; lokalizacja&gt;.cloudapp.azure.com* jest etykietą DNS skojarzoną z modułem równoważenia obciążenia przed zestawem skalowania.

Maszyny wirtualne w klastrze mają tylko [prywatne adresy IP](/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses).  Ruch zarządzania i ruchu usługowego są kierowane przez publiczny moduł równoważenia obciążenia.  Ruch sieciowy jest kierowany do tych komputerów za pośrednictwem reguł NAT (klienci łączą się z określonymi węzłami/wystąpieniami) lub regułami równoważenia obciążenia (ruch przechodzi do działaniem okrężnym maszyn wirtualnych).  Moduł równoważenia obciążenia ma skojarzony publiczny adres IP o nazwie DNS w formacie: * &lt;nazwa klastra&gt;.&lt; lokalizacji&gt;.cloudapp.azure.com*.  Publiczny adres IP to kolejny zasób platformy Azure w grupie zasobów.  Jeśli zdefiniowano wiele typów węzłów w klastrze, dla każdego typu/skalowania węzła tworzony jest moduł równoważenia obciążenia. Można też skonfigurować pojedynczy moduł równoważenia obciążenia dla wielu typów węzłów.  Typ węzła podstawowego ma * &lt;nazwa&gt;&lt; klastra etykiet DNS . lokalizacja&gt;.cloudapp.azure.com,* inne typy węzłów mają * &lt;typ&gt;-&lt;&gt;węzła&lt; clustername etykiety DNS . lokalizacji&gt;.cloudapp.azure.com*.

### <a name="storage-accounts"></a>Konta magazynu
Każdy typ węzła klastra jest obsługiwany przez [konto magazynu platformy Azure](/azure/storage/common/storage-introduction) i dyski zarządzane.

## <a name="cluster-security"></a>Zabezpieczenia klastra
Klaster sieci szkieletowej usług jest zasobem, który jesteś właścicielem.  Użytkownik jest odpowiedzialny za zabezpieczenie klastrów, aby zapobiec łączeniu się z nimi nieautoryzowanym użytkownikom. Bezpieczny klaster jest szczególnie ważne podczas uruchamiania obciążeń produkcyjnych w klastrze. 

### <a name="node-to-node-security"></a>Zabezpieczenia między węzłami
Zabezpieczenia między węzłami zabezpieczają komunikację między maszynami wirtualnymi lub komputerami w klastrze. Ten scenariusz zabezpieczeń gwarantuje, że tylko komputery, które są upoważnione do przyłączenia się do klastra mogą uczestniczyć w hostowania aplikacji i usług w klastrze. Usługa Service Fabric używa certyfikatów X.509 do zabezpieczenia klastra i zapewnienia funkcji zabezpieczeń aplikacji.  Certyfikat klastra jest wymagany do zabezpieczenia ruchu klastra i zapewnienia uwierzytelniania klastra i serwera.  Certyfikaty z podpisem własnym mogą być używane dla klastrów testowych, ale certyfikat z zaufanego urzędu certyfikacji powinien być używany do zabezpieczania klastrów produkcyjnych.

Aby uzyskać więcej informacji, przeczytaj informacje [o zabezpieczeniach między węzłami](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>Zabezpieczenia między klientami
Zabezpieczenia klient-węzeł uwierzytelniają klientów i pomagają w zabezpieczeniu komunikacji między klientem a poszczególnymi węzłami w klastrze. Ten typ zabezpieczeń pomaga zapewnić, że tylko autoryzowani użytkownicy mogą uzyskiwać dostęp do klastra i aplikacji wdrożonych w klastrze. Klienci są jednoznacznie identyfikowani za pomocą poświadczeń zabezpieczeń certyfikatu X.509. Dowolnej liczby opcjonalnych certyfikatów klienta można używać do uwierzytelniania klientów administratora lub użytkowników za pomocą klastra.

Oprócz certyfikatów klientów usługę Azure Active Directory można również skonfigurować do uwierzytelniania klientów za pomocą klastra.

Aby uzyskać więcej informacji, przeczytaj informacje [o zabezpieczeniach między klientami](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach
Kontrola dostępu oparta na rolach (RBAC) umożliwia przypisywanie precyzyjnych kontroli dostępu do zasobów platformy Azure.  Do subskrypcji, grup zasobów i zasobów można przypisać różne reguły dostępu.  Reguły RBAC są dziedziczone wzdłuż hierarchii zasobów, chyba że zostaną zastąpione na niższym poziomie.  Można przypisać dowolne grupy użytkowników lub użytkowników w ujmowanie za pomocą reguł RBAC, aby wyznaczeni użytkownicy i grupy mogli modyfikować klaster.  Aby uzyskać więcej informacji, przeczytaj [omówienie usługi Azure RBAC](/azure/role-based-access-control/overview).

Usługa Sieci szkieletowej obsługuje również kontrolę dostępu, aby ograniczyć dostęp do niektórych operacji klastra dla różnych grup użytkowników. Pomaga to uczynić klaster bardziej bezpieczne. Dwa typy kontroli dostępu są obsługiwane dla klientów, którzy łączą się z klastrem: rola administratora i rola użytkownika.  

Aby uzyskać więcej informacji, zobacz [Kontrola dostępu oparta na rolach sieci szkieletowej usług (RBAC).](service-fabric-cluster-security.md#role-based-access-control-rbac)

### <a name="network-security-groups"></a>Grupy zabezpieczeń sieci 
Sieciowe grupy zabezpieczeń kontrolują ruch przychodzący i wychodzący podsieci, maszyny wirtualnej lub określonej karty sieciowej.  Domyślnie, gdy wiele maszyn wirtualnych są umieszczane w tej samej sieci wirtualnej mogą komunikować się ze sobą za pośrednictwem dowolnego portu.  Jeśli chcesz ograniczyć komunikację między maszynami, można zdefiniować sieciowe sieciowe sieciowe, aby segmentować sieć lub odizolować maszyny wirtualne od siebie nawzajem.  Jeśli masz wiele typów węzłów w klastrze, można zastosować sieciowe sieciowe do podsieci, aby zapobiec komunikowaniu się ze sobą maszyn należących do różnych typów węzłów.  

Aby uzyskać więcej informacji, przeczytaj artykuł o [grupach zabezpieczeń](/azure/virtual-network/security-overview)

## <a name="scaling"></a>Skalowanie

Wymagania aplikacji zmieniają się wraz z czasem. Może być konieczne zwiększenie zasobów klastra w celu zaspokojenia zwiększonego obciążenia aplikacji lub ruchu sieciowego lub zmniejszenia zasobów klastra w przypadku spadku popytu. Po utworzeniu klastra sieci szkieletowej usług można skalować klaster w poziomie (zmieniać liczbę węzłów) lub w pionie (zmienić zasoby węzłów). Klaster można skalować w dowolnym momencie, nawet gdy obciążenia są uruchomione w klastrze. W miarę skalowania klastra aplikacje są również skalowane automatycznie.

Aby uzyskać więcej informacji, przeczytaj artykuł [Skalowanie klastrów platformy Azure](service-fabric-cluster-scaling.md).

## <a name="upgrading"></a>Uaktualnianie
Klaster sieci szkieletowej usług Azure jest zasobem, który jest własnością, ale jest częściowo zarządzany przez firmę Microsoft. Firma Microsoft jest odpowiedzialna za instalowanie poprawek bazowego systemu operacyjnego i wykonywanie uaktualnień środowiska uruchomieniowego sieci szkieletowej usług w klastrze. Klaster można ustawić tak, aby odbierał automatyczne uaktualnienia środowiska uruchomieniowego, gdy firma Microsoft wyda nową wersję, lub wybrać obsługiwaną wersję środowiska uruchomieniowego. Oprócz uaktualnień środowiska uruchomieniowego można również zaktualizować konfigurację klastra, taką jak certyfikaty lub porty aplikacji.

Aby uzyskać więcej informacji, zobacz [Uaktualnianie klastrów](service-fabric-cluster-upgrade.md).

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne
Można tworzyć klastry na maszynach wirtualnych z uruchomionymi tymi systemami operacyjnymi:

| System operacyjny | Najwcześniejsza obsługiwana wersja sieci szkieletowej usług |
| --- | --- |
| Windows Server 2012 R2 | Wszystkie wersje |
| Windows Server 2016 | Wszystkie wersje |
| System Windows Server 1709 | 6.0 |
| System Windows Server 1803 | 6.4 |
| System Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6.0 |

Aby uzyskać dodatkowe informacje, zobacz [Obsługiwane wersje klastra na platformie Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-versions#supported-operating-systems)

> [!NOTE]
> Jeśli zdecydujesz się wdrożyć sieć szkieletową usług w systemie Windows Server 1709, należy pamiętać, że (1) nie jest to długoterminowa gałąź obsługi, więc może być konieczne przeniesienie wersji w przyszłości i (2) w przypadku wdrażania kontenerów kontenery zbudowane w systemie Windows Server 2016 nie działają w systemie Windows Server 1709 i odwrotnie (trzeba będzie je odbudować, aby je wdrożyć).
>


## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [zabezpieczaniu,](service-fabric-cluster-security.md) [skalowaniu](service-fabric-cluster-scaling.md)i [uaktualnianiu](service-fabric-cluster-upgrade.md) klastrów platformy Azure.

Dowiedz się więcej o [opcjach pomocy technicznej sieci szkieletowej](service-fabric-support.md)usług .

[Image]: media/service-fabric-azure-clusters-overview/Cluster.PNG
