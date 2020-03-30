---
title: Ograniczenia dotyczące pul węzłów systemu Windows Server w usłudze Azure Kubernetes (AKS)
description: Dowiedz się więcej o znanych ograniczeniach podczas uruchamiania pul węzłów systemu Windows Server i obciążeń aplikacji w usłudze Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 12/18/2019
ms.openlocfilehash: f4e9f63d0da1797b92c123034e6775f5b07bd4b3
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366409"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Bieżące ograniczenia dotyczące pul węzłów systemu Windows Server i obciążeń aplikacji w usłudze Azure Kubernetes (AKS)

W usłudze Azure Kubernetes Service (AKS) można utworzyć pulę węzłów, która uruchamia system Windows Server jako system operacyjny gościa w węzłach. Te węzły mogą uruchamiać natywne aplikacje kontenerów systemu Windows, takie jak te utworzone na platformie .NET Framework. Ponieważ istnieją poważne różnice w sposobie, w jaki system operacyjny Linux i Windows zapewnia obsługę kontenerów, niektóre typowe funkcje kubernetes i zasobników nie są obecnie dostępne dla pul węzłów systemu Windows.

W tym artykule opisano niektóre ograniczenia i pojęcia systemu operacyjnego dla węzłów systemu Windows Server w usłudze AKS. Pule węzłów dla systemu Windows Server są obecnie w wersji zapoznawczej.

> [!IMPORTANT]
> Funkcje podglądu usługi AKS są samoobsługowe. Wersje zapoznawcza są dostarczane w stanie "tak jak jest" i "w miarę dostępności" i są wyłączone z umów o gwarantowanym poziomie usług i ograniczonej gwarancji. Podglądy AKS są częściowo objęte obsługą klienta na podstawie najlepszych starań. W związku z tym te funkcje nie są przeznaczone do użytku produkcyjnego. Aby uzyskać dodatkowe informacje, zobacz następujące artykuły pomocy technicznej:
>
> * [Zasady wsparcia AKS][aks-support-policies]
> * [Często zadawane pytania dotyczące pomocy technicznej platformy Azure][aks-faq]

## <a name="which-windows-operating-systems-are-supported"></a>Które systemy operacyjne Windows są obsługiwane?

Usługa AKS używa systemu Windows Server 2019 jako wersji systemu operacyjnego hosta i obsługuje tylko izolację procesów. Obrazy kontenerów utworzone przy użyciu innych wersji systemu Windows Server nie są obsługiwane. [Zgodność z wersją kontenera systemu Windows][windows-container-compat]

## <a name="is-kubernetes-different-on-windows-and-linux"></a>Czy Kubernetes różni się w systemach Windows i Linux?

Obsługa puli węzłów serwera okien zawiera pewne ograniczenia, które są częścią nadrzędnego systemu Windows Server w projekcie Kubernetes. Ograniczenia te nie są specyficzne dla usługi AKS. Aby uzyskać więcej informacji na temat tej nadrzędnej obsługi systemu Windows Server w programie Kubernetes, zobacz [sekcję Obsługiwane funkcje i ograniczenia][upstream-limitations] pomocy technicznej programu Intro do systemu Windows w dokumencie [Kubernetes][intro-windows] z projektu Kubernetes.

Kubernetes jest historycznie koncentruje się na Linuksie. Wiele przykładów używanych w witrynie sieci Web [Kubernetes.io][kubernetes] nadrzędnej są przeznaczone do użycia w węzłach systemu Linux. Podczas tworzenia wdrożeń korzystających z kontenerów systemu Windows Server stosuje się następujące zagadnienia na poziomie systemu operacyjnego:

- **Tożsamość** — Linux identyfikuje użytkownika za pomocą identyfikatora użytkownika całkowitej (UID). Użytkownik ma również alfanumeryczną nazwę użytkownika do logowania, co Linux tłumaczy na identyfikator UID użytkownika. Podobnie Linux identyfikuje grupę użytkowników przez identyfikator grupy całkowitej (GID) i tłumaczy nazwę grupy na odpowiadającą jej GID.
    - System Windows Server używa większego binarnego identyfikatora zabezpieczeń (SID), który jest przechowywany w bazie danych Menedżera dostępu do zabezpieczeń systemu Windows (SAM). Ta baza danych nie jest współużytkowana między hostem i kontenerami lub między kontenerami.
- **Uprawnienia do plików** — system Windows Server używa listy kontroli dostępu opartej na identyfikatorach SID, a nie maski bitowej uprawnień i UID+GID
- **Ścieżki plików** - konwencja w systemie Windows Server jest użycie \ zamiast /.
    - W specyfikacjach zasobników, które montują woluminy, określ poprawnie ścieżkę dla kontenerów systemu Windows Server. Na przykład zamiast punktu instalacji */mnt/volume* w kontenerze systemu Linux, należy określić literę dysku i lokalizację, taką jak */K/Volume* do zainstalowania jako dysk *K:.*

## <a name="what-kind-of-disks-are-supported-for-windows"></a>Jakiego rodzaju dyski są obsługiwane dla systemu Windows?

Dyski platformy Azure i pliki platformy Azure to obsługiwane typy woluminów, do których uzyskuje się dostęp jako woluminy NTFS w kontenerze systemu Windows Server.

## <a name="can-i-run-windows-only-clusters-in-aks"></a>Czy mogę uruchamiać tylko klastry systemu Windows w usłudze AKS?

Węzły główne (płaszczyzna sterowania) w klastrze AKS są obsługiwane przez usługę AKS, nie będą narażone na system operacyjny węzłów obsługujących składniki główne. Wszystkie klastry AKS są tworzone z domyślną pulę pierwszego węzła, który jest oparty na systemie Linux. Ta pula węzłów zawiera usługi systemowe, które są potrzebne do działania klastra. Zaleca się uruchomienie co najmniej dwóch węzłów w pierwszej puli węzłów, aby zapewnić niezawodność klastra i możliwość wykonywania operacji klastra. Nie można usunąć pierwszej puli węzłów opartych na systemie Linux, chyba że sam klaster AKS zostanie usunięty.

## <a name="what-network-plug-ins-are-supported"></a>Jakie wtyczki sieciowe są obsługiwane?

Klastry AKS z pulami węzłów systemu Windows muszą używać modelu sieci Azure CNI (zaawansowanego). Kubenet (podstawowe) sieci nie jest obsługiwany. Aby uzyskać więcej informacji na temat różnic w modelach sieci, zobacz [Pojęcia dotyczące sieci dla aplikacji w programie AKS][azure-network-models]. - Model sieci Azure CNI wymaga dodatkowego planowania i zagadnienia dotyczące zarządzania adresami IP. Aby uzyskać więcej informacji na temat planowania i implementowania usługi Azure CNI, zobacz [Konfigurowanie sieci CNI platformy Azure w programie AKS.][configure-azure-cni]

## <a name="can-i-change-the-max--of-pods-per-node"></a>Czy mogę zmienić maks. liczba zasobników na węzeł?

Tak. Aby uzyskać implikacje i opcje, które są dostępne, zobacz [Maksymalna liczba zasobników][maximum-number-of-pods].

## <a name="how-do-patch-my-windows-nodes"></a>Jak załatać moje węzły systemu Windows?

Węzły systemu Windows Server w systemie AKS muszą zostać *uaktualnione,* aby uzyskać najnowsze poprawki i aktualizacje. Aktualizacje systemu Windows nie są włączone w węzłach w usłudze AKS. AKS zwalnia nowe obrazy puli węzłów, jak tylko poprawki są dostępne, jest obowiązkiem klientów uaktualnienia pul węzłów, aby być na bieżąco na poprawki i poprawki. Dotyczy to również używanej wersji Kubernetes. Informacje o wersji programu AKS będą wskazywać, kiedy dostępne są nowe wersje. Aby uzyskać więcej informacji na temat uaktualniania puli węzłów systemu Windows Server, zobacz [Uaktualnianie puli węzłów w systemie AKS][nodepool-upgrade].

> [!NOTE]
> Zaktualizowany obraz systemu Windows Server będzie używany tylko wtedy, gdy uaktualnienie klastra (uaktualnienie płaszczyzny sterowania) zostało wykonane przed uaktualnieniem puli węzłów
>

## <a name="how-do-i-rotate-the-service-principal-for-my-windows-node-pool"></a>Jak obrócić jednostkę usługi dla mojej puli węzłów systemu Windows?

Podczas podglądu pule węzłów systemu Windows nie obsługują rotacji głównej usługi jako ograniczenia podglądu. Aby zaktualizować jednostkę usługi, należy utworzyć nową pulę węzłów systemu Windows i przeprowadzić migrację zasobników ze starszej puli do nowej. Po zakończeniu usuń starszą pulę węzłów.

## <a name="how-many-node-pools-can-i-create"></a>Ile pul węzłów można utworzyć?

Klaster AKS może mieć maksymalnie 10 pul węzłów. W tych pulach węzłów może być maksymalnie 1000 węzłów. [Ograniczenia puli węzłów][nodepool-limitations].

## <a name="what-can-i-name-my-windows-node-pools"></a>Co mogę nazwać pulami węzłów systemu Windows?

Musisz zachować nazwę maksymalnie 6 (sześć) znaków. Jest to aktualne ograniczenie AKS.

## <a name="are-all-features-supported-with-windows-nodes"></a>Czy wszystkie funkcje są obsługiwane przez węzły systemu Windows?

Zasady sieciowe i kubenet nie są obecnie obsługiwane w węzłach systemu Windows. 

## <a name="can-i-run-ingress-controllers-on-windows-nodes"></a>Czy mogę uruchamiać kontrolery transferu danych przychodzących w węzłach systemu Windows?

Tak, kontroler przychodzący obsługujący kontenery systemu Windows Server może działać w węzłach systemu Windows w usłudze AKS.

## <a name="can-i-use-azure-dev-spaces-with-windows-nodes"></a>Czy mogę używać usługi Azure Dev Spaces z węzłami systemu Windows?

Usługa Azure Dev Spaces jest obecnie dostępna tylko dla pul węzłów opartych na systemie Linux.

## <a name="can-my-windows-server-containers-use-gmsa"></a>Czy moje kontenery systemu Windows Server mogą używać gMSA?

Obsługa grupowych kont zarządzanych usług (gMSA) nie jest obecnie dostępna w usłudze AKS.

## <a name="can-i-use-azure-monitor-for-containers-with-windows-nodes-and-containers"></a>Czy mogę używać usługi Azure Monitor dla kontenerów z węzłami i kontenerami systemu Windows?

Tak, jednak usługa Azure Monitor nie gromadzi dzienników (stdout) z kontenerów systemu Windows. Nadal można dołączyć do strumienia na żywo dzienników stdout z kontenera systemu Windows.

## <a name="what-if-i-need-a-feature-which-is-not-supported"></a>Co zrobić, jeśli potrzebuję funkcji, która nie jest obsługiwana?

Ciężko pracujemy, aby wprowadzić wszystkie funkcje potrzebne do systemu Windows w systemie AKS, ale jeśli napotkasz luki, projekt open source, [upstream aks-engine][aks-engine] zapewnia łatwy i w pełni konfigurowalny sposób uruchamiania aplikacji Kubernetes na platformie Azure, w tym obsługę systemu Windows. Upewnij się, że zapoznaj się z naszym planem działania dotyczącym nadchodzących [map drogowych AKS.][aks-roadmap]

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z kontenerami systemu Windows Server w systemie AKS, [utwórz pulę węzłów z systemem Windows Server w systemie AKS][windows-node-cli].

<!-- LINKS - external -->
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine
[upstream-limitations]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/#supported-functionality-and-limitations
[intro-windows]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/
[aks-roadmap]: https://github.com/Azure/AKS/projects/1

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
[nodepool-limitations]: use-multiple-node-pools.md#limitations
[preview-support]: support-policies.md#preview-features-or-feature-flags
[windows-container-compat]: /virtualization/windowscontainers/deploy-containers/version-compatibility?tabs=windows-server-2019%2Cwindows-10-1909
[maximum-number-of-pods]: configure-azure-cni.md#maximum-pods-per-node
