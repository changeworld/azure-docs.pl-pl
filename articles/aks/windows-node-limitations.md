---
title: Ograniczenia dotyczące pule węzłów systemu Windows Server w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się więcej o znanych ograniczeniach, po uruchomieniu pule węzłów systemu Windows Server i obciążeń aplikacji w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: tylermsft
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: twhitney
ms.openlocfilehash: 34ece6e49332f781f688a8741db3514faf8c9a25
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304393"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Bieżących ograniczeń dotyczących pule węzłów systemu Windows Server i obciążeń aplikacji w usłudze Azure Kubernetes Service (AKS)

W usłudze Azure Kubernetes Service (AKS), można utworzyć pulę węzłów z systemem Windows Server jako system operacyjny gościa na węzłach. Te węzły można uruchamiać natywne aplikacje kontenera Windows, takich jak oparty na .NET Framework. Jak istnieją istotne różnice w jak systemów Linux i system operacyjny Windows zapewnia obsługę kontenerów, niektóre typowe rozwiązania Kubernetes i funkcje związane z zasobników nie są obecnie dostępne dla Windows pule węzłów.

W tym artykule przedstawiono kilka ograniczeń i pojęcia systemu operacyjnego dla węzłów systemu Windows Server w usłudze AKS. Pule węzłów dla systemu Windows Server są obecnie dostępne w wersji zapoznawczej.

> [!IMPORTANT]
> Funkcje w wersji zapoznawczej usługi AKS są samoobsługi, uczestnictwo. Są one udostępniane do zbierania opinii i błędy z naszej społeczności. W wersji zapoznawczej te funkcje nie są przeznaczone do użytku produkcyjnego. Funkcje w wersji zapoznawczej objęte "starań" pomocy technicznej. Pomoc od zespołów pomocy technicznej usługi AKS jest dostępna w godzinach pracy i Pacyfik, część strefy czasowej (PST) tylko. Aby uzyskać więcej informacji zobacz następujące artykuły pomocy technicznej:
>
> * [Zasady pomocy technicznej usługi AKS][aks-support-policies]
> * [Pomoc techniczna platformy Azure — często zadawane pytania][aks-faq]

## <a name="limitations-for-windows-server-in-kubernetes"></a>Ograniczenia dotyczące systemu Windows Server w usłudze Kubernetes

Kontenery systemu Windows Server należy uruchomić na hoście kontenera z systemem Windows. Do uruchamiania kontenerów systemu Windows Server w usłudze AKS, możesz [utworzyć pulę węzłów z systemem Windows Server] [ windows-node-cli] jako systemu operacyjnego gościa. Obsługa puli węzeł serwera okna obejmuje pewne ograniczenia, które są częścią nadrzędnego serwera systemu Windows w projekcie platformy Kubernetes. Te ograniczenia nie są specyficzne dla usługi AKS. Aby uzyskać więcej informacji na temat tego nadrzędnego pomocy technicznej dla systemu Windows Server w usłudze Kubernetes, zobacz [kontenery systemu Windows Server w ograniczenia Kubernetes][upstream-limitations].

AKS dotyczą następujące ograniczenia nadrzędnego dla kontenerów systemu Windows Server w usłudze Kubernetes:

- Kontenery systemu Windows Server można używać tylko systemu Windows Server 2019, odpowiadającego podstawowego węzła systemu Windows Server, systemu operacyjnego.
    - Obrazy kontenera utworzone przy użyciu systemu Windows Server 2016, jako podstawowego systemu operacyjnego nie są obsługiwane.
- Nie można używać kontenerów uprzywilejowanych.
- Funkcje specyficzne dla systemu Linux, takie jak nazwa_użytkownika "," SELinux "," AppArmor "lub" POSIX funkcje nie są dostępne w kontenerach systemu Windows Server.
    - Ograniczenia systemu plików, które są specyficzne dla systemu Linux takie jak UUI/GUID na uprawnienia użytkownika również nie są dostępne w kontenerach systemu Windows Server.
- Usługa Azure dysków i plików platformy Azure są obsługiwane typy woluminów, dostępne jako woluminy systemu plików NTFS w kontenerze systemu Windows Server.
    - Magazyn oparty na systemu plików NFS / woluminy nie są obsługiwane.

## <a name="aks-limitations-for-windows-server-node-pools"></a>Ograniczenia AKS pule węzłów systemu Windows Server

Następujące dodatkowe ograniczenia do pomocy technicznej pulę węzłów systemu Windows Server w usłudze AKS:

- Klaster AKS zawsze zawiera pulę węzłów systemu Linux jako pierwszy pulę węzłów. Nie można usunąć tej pierwszej puli węzeł opartych na systemie Linux, chyba że sam klaster AKS zostanie usunięty.
- Klastry usługi AKS, należy użyć modelu wtyczki Azure CNI (zaawansowane) w sieci.
    - Sieć z wtyczki Kubenet (basic) nie jest obsługiwane. Nie można utworzyć klaster AKS, która korzysta z wtyczki kubenet. Aby uzyskać więcej informacji na temat różnic w modelach sieci, zobacz [sieci pojęcia związane z aplikacjami w usłudze AKS][azure-network-models].
    - Model sieciowych wtyczki Azure CNI wymaga dodatkowego planowania i zagadnień dotyczących zarządzania adresami IP. Aby uzyskać więcej informacji na temat sposobu planowania i implementacji wtyczki Azure CNI, zobacz [wtyczki Azure CNI konfigurowania sieci w usłudze AKS][configure-azure-cni].
- Węzły systemu Windows Server w usłudze AKS muszą być *uaktualnione* do najnowszej wersji systemu Windows Server 2019 do obsługi najnowszych poprawek, poprawek i aktualizacji. Aktualizacje Windows nie są włączone w obrazie węzeł podstawowy w usłudze AKS. Zgodnie z ustalonym harmonogramem w całym cyklu tworzenia wydań Windows Update i procesu sprawdzania poprawności należy wykonać uaktualnienie na pule węzłów systemu Windows Server, w klastrze AKS. Aby uzyskać więcej informacji na temat uaktualniania pulę węzłów systemu Windows Server, zobacz [uaktualnienia pulę węzłów w usłudze AKS][nodepool-upgrade].
    - Te uaktualnienia węzłów systemu Windows Server tymczasowo używać dodatkowych adresów IP w podsieci sieci wirtualnej, ponieważ nowy węzeł zostanie wdrożona, przed usunięciem węzła starego.
    - limity przydziału procesorów wirtualnych również tymczasowo są używane w ramach subskrypcji, ponieważ nowy węzeł zostanie wdrożona, a następnie usunąć węzła starego.
    - Nie można automatycznie aktualizować i zarządzać ponownego uruchomienia przy użyciu `kured` podobnie jak w przypadku węzłów systemu Linux w usłudze AKS.
- Klaster AKS może mieć maksymalnie osiem pule węzłów.
    - Może mieć maksymalnie 400 węzłów w tych pulach osiem węzłów.
- Nazwa puli węzeł systemu Windows Server z limitem 6 znaków.
- W wersji zapoznawczej funkcji w usłudze AKS, takie jak zasad sieciowych i skalowanie klastra nie są zalecane dla węzłów systemu Windows Server.
- Ruch przychodzący kontrolerów powinny zostać zaplanowane tylko w węzłach systemu Linux przy użyciu NodeSelector.
- Azure Dev do magazynowania jest obecnie dostępny tylko w przypadku pul węzłów opartych na systemie Linux.
- Grupy kont usług zarządzanych, czy pomoc techniczna (gMSA), gdy węzły systemu Windows Server nie są przyłączone do domeny usługi Active Directory nie jest obecnie dostępna w usłudze AKS.
    - Open source, nadrzędne [aparatu aks] [ aks-engine] projektu obecnie oferuje gMSA pomocy technicznej, jeśli chcesz korzystać z tej funkcji.

## <a name="os-concepts-that-are-different"></a>Pojęcia dotyczące systemu operacyjnego, które różnią się

Rozwiązanie Kubernetes jest w przeszłości skoncentrowane na systemie Linux. Wiele przykładów używanych w nadrzędnym [Kubernetes.io] [ kubernetes] witryny sieci Web są przeznaczone do użytku w węzłach systemu Linux. Po utworzeniu wdrożenia korzystające z kontenerów systemu Windows Server, następujące uwagi na poziomie systemu operacyjnego Zastosuj:

- **Tożsamość** -Linux używa identyfikatora użytkownika (UID) i identyfikator grupy (GID), reprezentowana jako typy liczb całkowitych. Nazwy użytkowników i grup nie są kanonicznej — są one po prostu aliasem w */etc/grup* lub */etc/haseł* do UID + GID.
    - Windows Server używa większych binarne identyfikatora zabezpieczeń (SID) przechowywanego w bazie danych Menedżera dostępu Windows zabezpieczeń (SAM). Ta baza danych nie jest udostępniony, między hostem i kontenery lub między kontenerów.
- **Uprawnienia do plików** -listy kontroli dostępu na podstawie identyfikatorów SID zamiast maski bitów, uprawnień i UID + GID korzysta z systemu Windows Server
- **Ścieżki plików** -Konwencji w systemie Windows Server jest użycie \ zamiast /.
    - W specyfikacji pod, które zainstalować woluminów Określ ścieżkę poprawnie na potrzeby kontenerów systemu Windows Server. Na przykład, zamiast instalacji punkt *wolumin/mnt/* w kontenerze systemu Linux Określ taką jak na literę dysku i lokalizację */K/woluminu* zainstalować jako *K:* dysku.

## <a name="next-steps"></a>Kolejne kroki

Aby rozpocząć pracę z kontenerami systemu Windows Server w usłudze AKS, [utworzyć pulę węzłów z systemem Windows Server w usłudze AKS][windows-node-cli].

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
