---
title: Ograniczenia dotyczące pul węzłów systemu Windows Server w usłudze Azure Kubernetes Service (AKS)
description: Informacje o znanych ograniczeniach w przypadku uruchamiania pul węzłów systemu Windows Server i obciążeń aplikacji w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: c2c9e3d29ced5f75873656e253ecdbab5efe7df8
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114402"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Bieżące ograniczenia dotyczące pul węzłów systemu Windows Server i obciążeń aplikacji w usłudze Azure Kubernetes Service (AKS)

W usłudze Azure Kubernetes Service (AKS) można utworzyć pulę węzłów, w której działa system Windows Server jako system operacyjny gościa na węzłach. Te węzły mogą uruchamiać natywne aplikacje kontenera systemu Windows, takie jak te utworzone na .NET Framework. W związku z tym, że system operacyjny Linux i Windows zapewnia obsługę kontenera, niektóre typowe funkcje Kubernetes i powiązane z usługą nie są obecnie dostępne dla pul węzłów systemu Windows.

W tym artykule opisano niektóre ograniczenia i koncepcje systemu operacyjnego dla węzłów systemu Windows Server w AKS. Pule węzłów dla systemu Windows Server są obecnie w wersji zapoznawczej.

> [!IMPORTANT]
> Funkcja AKS w wersji zapoznawczej to samoobsługowe uczestnictwo. Wersje zapoznawcze są udostępniane w postaci "AS-IS" i "jako dostępne" i są wyłączone z umów dotyczących poziomu usług i ograniczonej rękojmi. Wersje zapoznawcze AKS są częściowo objęte obsługą klienta w oparciu o najlepszy nakład pracy. W związku z tym te funkcje nie są przeznaczone do użytku produkcyjnego. Aby dowiedzieć się więcej, zobacz następujące artykuły pomocy technicznej:
>
> * [Zasady pomocy technicznej AKS][aks-support-policies]
> * [Pomoc techniczna platformy Azure — często zadawane pytania][aks-faq]

## <a name="limitations-for-windows-server-in-kubernetes"></a>Ograniczenia dotyczące systemu Windows Server w Kubernetes

Kontenery systemu Windows Server muszą działać na hoście kontenera z systemem Windows. Aby uruchomić kontenery systemu Windows Server w AKS, można [utworzyć pulę węzłów z systemem Windows Server][windows-node-cli] jako system operacyjny gościa. Obsługa puli węzłów serwera okna zawiera pewne ograniczenia, które są częścią nadrzędnego serwera systemu Windows w projekcie Kubernetes. Te ograniczenia nie są specyficzne dla AKS. Aby uzyskać więcej informacji o tej obsłudze nadrzędnej dla systemu Windows Server w Kubernetes, zobacz [kontenery systemu Windows Server w ograniczeniach Kubernetes](https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/#supported-functionality-and-limitations).

Następujące ograniczenia nadrzędne dla kontenerów systemu Windows Server w programie Kubernetes są istotne dla AKS:

- Kontenery systemu Windows Server mogą korzystać tylko z systemu Windows Server 2019, który odpowiada podstawowemu systemowi OPERACYJNEmu węzła systemu Windows Server.
    - Obrazy kontenerów utworzone przy użyciu systemu Windows Server 2016 jako podstawowy system operacyjny nie są obsługiwane.
- Nie można używać kontenerów uprzywilejowanych.
- Funkcje specyficzne dla systemu Linux, takie jak RunAsUser, SELinux, AppArmor lub POSIX, nie są dostępne w kontenerach Windows Server.
    - Ograniczenia w systemie plików, które są specyficzne dla systemu Linux, takie jak UUI/GUID, na uprawnienia użytkownika również nie są dostępne w kontenerach systemu Windows Server.
- Dyski platformy Azure i Azure Files są obsługiwanymi typami woluminów dostępnymi jako woluminy NTFS w kontenerze systemu Windows Server.
    - Magazyny i woluminy NFS nie są obsługiwane.

## <a name="aks-limitations-for-windows-server-node-pools"></a>Ograniczenia AKS dla pul węzłów systemu Windows Server

Następujące dodatkowe ograniczenia dotyczą obsługi puli węzłów systemu Windows Server w programie AKS:

- Klaster AKS zawsze zawiera pulę węzłów systemu Linux jako pierwszą pulę węzłów. Nie można usunąć tej pierwszej puli węzłów opartej na systemie Linux, chyba że zostanie usunięty sam klaster AKS.
- Klastry AKS muszą używać modelu sieci usługi Azure CNI (Advanced).
    - Sieć korzystającą wtyczki kubenet (podstawowa) nie jest obsługiwana. Nie można utworzyć klastra AKS, który korzysta z korzystającą wtyczki kubenet. Aby uzyskać więcej informacji na temat różnic między modelami sieci, zobacz [pojęcia dotyczące sieci dla aplikacji w AKS][azure-network-models].
    - Model sieci usługi Azure CNI wymaga dodatkowego planowania i zagadnień związanych z zarządzaniem adresami IP. Aby uzyskać więcej informacji na temat planowania i implementowania usługi Azure CNI, zobacz [Konfigurowanie sieci Azure CNI w programie AKS][configure-azure-cni].
- Węzły systemu Windows Server w AKS muszą zostać uaktualnione do najnowszej wersji systemu windows Server 2019, aby zachować najnowsze poprawki poprawek i aktualizacji. Aktualizacje systemu Windows nie są włączone w podstawowym obrazie węzła w AKS. Zgodnie z regularnym harmonogramem Windows Update cyklu wydania i procesu weryfikacji należy przeprowadzić uaktualnienie w puli węzłów systemu Windows Server w klastrze AKS. Aby uzyskać więcej informacji na temat uaktualniania puli węzłów systemu Windows Server, zobacz [uaktualnianie puli węzłów w AKS][nodepool-upgrade].
    - Podczas uaktualniania węzła systemu Windows Server tymczasowo zużywane są dodatkowe adresy IP w podsieci sieci wirtualnej w ramach wdrożenia nowego węzła przed usunięciem starego węzła.
    - przydziały vCPU są również tymczasowo zużywane w ramach subskrypcji jako nowy węzeł, a następnie został usunięty stary węzeł.
    - Nie można automatycznie aktualizować i zarządzać ponownymi uruchomieniami `kured` przy użyciu węzłów systemu Linux w AKS.
- Klaster AKS może mieć maksymalnie osiem pul węzłów.
    - Dla tych ośmiu pul węzłów można mieć maksymalnie 400 węzłów.
- Nazwa puli węzłów systemu Windows Server ma limit 6 znaków.
- Funkcje w wersji zapoznawczej w programie AKS, takie jak zasady sieciowe i automatyczne skalowanie klastra, nie są zatwierdzone dla węzłów systemu Windows Server.
- Kontrolery transferu danych przychodzących należy planować tylko w węzłach systemu Linux przy użyciu NodeSelector.
- Azure Dev Spaces jest obecnie dostępna tylko dla pul węzłów opartych na systemie Linux.
- Obsługa kont usług zarządzanych przez grupę (gMSA), gdy węzły systemu Windows Server nie są przyłączone do domeny Active Directory nie są obecnie dostępne w AKS.
    - Projekt "open source" w ramach strumienia [AKS-Engine][aks-engine] zapewnia obecnie obsługę gMSA, jeśli trzeba będzie używać tej funkcji.

## <a name="os-concepts-that-are-different"></a>Różne koncepcje systemu operacyjnego

Kubernetes jest historycznym systemem Linux. Wiele przykładów używanych w nadrzędnej witrynie sieci Web [Kubernetes.IO][kubernetes] jest przeznaczonych do użycia w węzłach systemu Linux. Podczas tworzenia wdrożeń korzystających z kontenerów systemu Windows Server następujące zagadnienia są stosowane na poziomie systemu operacyjnego:

- **Tożsamość** — Linux używa identyfikatora użytkownika (UID) i identyfikatora GROUPID (GID) reprezentowanego jako typy całkowite. Nazwy użytkowników i grup nie są kanoniczne — to tylko alias w */etc/Groups* lub */etc/passwd* z powrotem do UID + GID.
    - System Windows Server używa większego identyfikatora zabezpieczeń (SID), który jest przechowywany w bazie danych Menedżera dostępu zabezpieczeń (SAM) systemu Windows. Ta baza danych nie jest udostępniana między hostem i kontenerami ani między kontenerami.
- **Uprawnienia do plików** — system Windows Server używa listy kontroli dostępu opartej na identyfikatorach SID, a nie maski bitów uprawnień i UID + GID
- **Ścieżki plików** — Konwencja w systemie Windows Server jest używana \ zamiast/.
    - W obszarze specyfikacje instalujące woluminy określ ścieżkę dla kontenerów systemu Windows Server. Na przykład zamiast punktu instalacji */mnt/Volume* w kontenerze systemu Linux należy określić literę dysku i lokalizację, taką jak */K/Volume* , do zainstalowania jako dysk *K:* .

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z kontenerami systemu Windows Server w programie AKS, należy [utworzyć pulę węzłów, w której działa system Windows Server w AKS][windows-node-cli].

<!-- LINKS - external -->
[upstream-limitations]: https://kubernetes.io/docs/setup/windows/#limitations
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
