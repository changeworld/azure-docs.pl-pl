---
title: Pojęcia — zabezpieczenia w usługach Kubernetes platformy Azure (AKS)
description: Dowiedz się więcej o zabezpieczeniach usługi Azure Kubernetes Service (AKS), w tym komunikacji głównej i węzła, zasad sieciowych i wpisów tajnych usługi Kubernetes.
services: container-service
ms.topic: conceptual
ms.date: 03/01/2019
ms.openlocfilehash: 7238e6cd7ab3625e2953a4408c82802d43372256
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595947"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Pojęcia dotyczące zabezpieczeń dla aplikacji i klastrów w usłudze Azure Kubernetes Service (AKS)

Aby chronić dane klientów podczas uruchamiania obciążeń aplikacji w usłudze Azure Kubernetes Service (AKS), bezpieczeństwo klastra jest kluczowym czynnikiem. Usługa Kubernetes zawiera składniki zabezpieczeń, takie jak *zasady sieciowe* i *wpisy tajne*. Platforma Azure następnie dodaje w składnikach, takich jak grupy zabezpieczeń sieci i zaaranżowane uaktualnienia klastra. Te składniki zabezpieczeń są łączone, aby utrzymać klaster AKS z dostępem do najnowszych aktualizacji zabezpieczeń systemu operacyjnego i wersji kubernetes oraz z bezpiecznym ruchem zasobników i dostępem do poufnych poświadczeń.

W tym artykule przedstawiono podstawowe pojęcia, które zabezpieczają aplikacje w uzywmiaj systemu AKS:

- [Zabezpieczenia składników głównych](#master-security)
- [Zabezpieczenia węzła](#node-security)
- [Uaktualnienia klastra](#cluster-upgrades)
- [Bezpieczeństwo sieci](#network-security)
- [Wpisy tajne usługi Kubernetes](#kubernetes-secrets)

## <a name="master-security"></a>Zabezpieczenia główne

W usłudze AKS składniki główne usługi Kubernetes są częścią usługi zarządzanej świadczonej przez firmę Microsoft. Każdy klaster AKS ma swój własny pojedynczy dzierżawca, dedykowany wzorzec Kubernetes, aby zapewnić serwer interfejsu API, harmonogram itp. Ten wzorzec jest zarządzany i utrzymywany przez firmę Microsoft.

Domyślnie serwer interfejsu API kubernetes używa publicznego adresu IP i w pełni kwalifikowanej nazwy domeny (FQDN). Dostęp do serwera interfejsu API można kontrolować za pomocą kontroli dostępu opartych na rolach usługi Kubernetes i usługi Azure Active Directory. Aby uzyskać więcej informacji, zobacz [Integracja usługi Azure AD z usługą AKS][aks-aad].

## <a name="node-security"></a>Zabezpieczenia węzła

Węzły usługi AKS to maszyny wirtualne platformy Azure, którymi zarządzasz i które możesz obsługiwać. Węzły Systemu Linux uruchamiają zoptymalizowaną dystrybucję Ubuntu przy użyciu środowiska wykonawczego kontenera Moby. Węzły systemu Windows Server (obecnie w wersji zapoznawczej w systemie AKS) uruchamiają zoptymalizowaną wersję systemu Windows Server 2019, a także używają środowiska wykonawczego kontenera Moby. Po utworzeniu lub skalowaniu klastra AKS węzły są automatycznie wdrażane z najnowszymi aktualizacjami i konfiguracjami zabezpieczeń systemu operacyjnego.

Platforma Azure automatycznie stosuje poprawki zabezpieczeń systemu operacyjnego do węzłów systemu Linux w trybie nocnym. Jeśli aktualizacja zabezpieczeń systemu operacyjnego Linux wymaga ponownego uruchomienia hosta, ponowne uruchomienie nie jest wykonywane automatycznie. Możesz ręcznie ponownie uruchomić węzły Systemu Linux lub typowym podejściem jest użycie [Kured][kured], demona restartu open source dla Kubernetes. Kured działa jako [DaemonSet][aks-daemonsets] i monitoruje każdy węzeł pod kątem obecności pliku wskazującego, że wymagany jest ponowny rozruch. Ponowne uruchomienie jest zarządzane w klastrze przy użyciu tego samego [procesu kordonu i drenażu](#cordon-and-drain) jako uaktualnienie klastra.

W przypadku węzłów systemu Windows Server (obecnie w wersji zapoznawczej w systemie AKS) usługa Windows Update nie uruchamia się automatycznie i nie stosuje najnowszych aktualizacji. Zgodnie z regularnym harmonogramem wokół cyklu wersji usługi Windows Update i własnego procesu sprawdzania poprawności należy przeprowadzić uaktualnienie w puli węzłów systemu Windows Server w klastrze AKS. Ten proces uaktualniania tworzy węzły, które uruchamiają najnowszy obraz i poprawki systemu Windows Server, a następnie usuwa starsze węzły. Aby uzyskać więcej informacji na temat tego procesu, zobacz [Uaktualnianie puli węzłów w programie AKS][nodepool-upgrade].

Węzły są wdrażane w prywatnej podsieci sieci wirtualnej bez przypisanych publicznych adresów IP. Do rozwiązywania problemów i zarządzania SSH jest domyślnie włączona. Ten dostęp SSH jest dostępny tylko przy użyciu wewnętrznego adresu IP.

Aby zapewnić magazyn, węzły używają dysków zarządzanych platformy Azure. W przypadku większości rozmiarów węzłów maszyny Wirtualnej są to dyski premium wspierane przez dyski SSD o wysokiej wydajności. Dane przechowywane na dyskach zarządzanych są automatycznie szyfrowane w spoczynku na platformie Azure. Aby zwiększyć nadmiarowość, te dyski są również bezpiecznie replikowane w centrum danych platformy Azure.

Środowiska Kubernetes, w UKS lub gdzie indziej, obecnie nie są całkowicie bezpieczne dla wrogiego użycia wielu dzierżawców. Dodatkowe funkcje zabezpieczeń, takie jak *Zasady zabezpieczeń zasobnika* lub bardziej szczegółowe kontroli dostępu oparte na rolach (RBAC) dla węzłów utrudnić exploity. Jednak dla prawdziwego zabezpieczenia podczas uruchamiania wrogich obciążeń wielodostępnych hypervisor jest jedynym poziomem zabezpieczeń, który należy zaufać. Domena zabezpieczeń dla usługi Kubernetes staje się całym klastrem, a nie pojedynczym węzłem. Dla tych typów wrogich obciążeń wielodostępnych należy użyć fizycznie izolowane klastry. Aby uzyskać więcej informacji na temat sposobów izolowania obciążeń, zobacz [Najważniejsze wskazówki dotyczące izolacji klastra w udziale AKS][cluster-isolation],

## <a name="cluster-upgrades"></a>Uaktualnienia klastra

W celu zapewnienia zabezpieczeń i zgodności lub korzystania z najnowszych funkcji platforma Azure udostępnia narzędzia do organizowania uaktualniania klastra i składników usługi AKS. Ta aranżacja uaktualnienia obejmuje zarówno wzorzec kubernetes i składniki agenta. Można wyświetlić [listę dostępnych wersji kubernetes](supported-kubernetes-versions.md) dla klastra AKS. Aby rozpocząć proces uaktualniania, należy określić jedną z tych dostępnych wersji. Platforma Azure następnie bezpiecznie kordony i opróżnia każdy węzeł usługi AKS i wykonuje uaktualnienie.

### <a name="cordon-and-drain"></a>Kordon i drenaż

Podczas procesu uaktualniania węzły usługi AKS są indywidualnie kordonowane z klastra, więc nowe zasobniki nie są zaplanowane na nich. Węzły są następnie opróżniane i uaktualniane w następujący sposób:

- Nowy węzeł jest wdrażany w puli węzłów. Ten węzeł uruchamia najnowszy obraz systemu operacyjnego i poprawki.
- Jeden z istniejących węzłów jest identyfikowany do uaktualnienia. Zasobniki w tym węźle są bezpiecznie zakończone i zaplanowane w innych węzłach w puli węzłów.
- Ten istniejący węzeł zostanie usunięty z klastra AKS.
- Następny węzeł w klastrze jest cordoned i opróżnione przy użyciu tego samego procesu, dopóki wszystkie węzły zostaną pomyślnie zastąpione w ramach procesu uaktualniania.

Aby uzyskać więcej informacji, zobacz [Uaktualnianie klastra AKS][aks-upgrade-cluster].

## <a name="network-security"></a>Bezpieczeństwo sieci

Aby uzyskać łączność i zabezpieczenia z sieciami lokalnymi, można wdrożyć klaster AKS w istniejących podsieciach sieci wirtualnej platformy Azure. Te sieci wirtualne mogą mieć połączenie sieci VPN lokacji do lokacji platformy Azure lub ekspresowe połączenie z siecią lokalną. Kontrolery transferu danych przychodzących Kubernetes można zdefiniować za pomocą prywatnych, wewnętrznych adresów IP, dzięki czemu usługi są dostępne tylko za pośrednictwem tego wewnętrznego połączenia sieciowego.

### <a name="azure-network-security-groups"></a>Sieciowe grupy zabezpieczeń platformy Azure

Aby filtrować przepływ ruchu w sieciach wirtualnych, platforma Azure używa reguł sieciowej grupy zabezpieczeń. Reguły te definiują źródłowe i docelowe zakresy adresów IP, porty i protokoły, które są dozwolone lub odmawiane dostępu do zasobów. Reguły domyślne są tworzone w celu umożliwienia ruchu TLS do serwera interfejsu API Kubernetes. Podczas tworzenia usług z modułami równoważenia obciążenia, mapowania portów lub tras transferu danych przychodzących usługa AKS automatycznie modyfikuje sieciową grupę zabezpieczeń, aby ruch był odpowiednio przepływowy.

## <a name="kubernetes-secrets"></a>Wpisy tajne usługi Kubernetes

Klucz *tajny* kubernetes służy do wstrzykiwania poufnych danych do zasobników, takich jak poświadczenia dostępu lub klucze. Najpierw utwórz klucz tajny przy użyciu interfejsu API kubernetes. Podczas definiowania zasobnika lub wdrożenia można zażądać określonego klucza tajnego. Wpisy tajne są dostarczane tylko do węzłów, które mają zaplanowane zasobnik, który tego wymaga, a klucz tajny jest przechowywany w *tmpfs*, nie jest zapisywany na dysku. Po usunięciu ostatniego zasobnika w węźle, który wymaga klucza tajnego, klucz tajny jest usuwany z tmpfs węzła. Wpisy tajne są przechowywane w danym obszarze nazw i są dostępne tylko przez zasobników w tym samym obszarze nazw.

Użycie wpisów tajnych zmniejsza poufne informacje, które są zdefiniowane w zasobniku lub usługi YAML manifestu. Zamiast tego należy zażądać klucza tajnego przechowywanego w usłudze Kubernetes API Server jako część manifestu YAML. Takie podejście zapewnia tylko dostęp do określonego zasobnika do klucza tajnego. Uwaga: surowe tajne pliki manifestów zawierają tajne dane w formacie base64 (więcej informacji można znaleźć w [oficjalnej dokumentacji).][secret-risks] W związku z tym ten plik powinien być traktowany jako poufne informacje i nigdy nie zobowiązał się do kontroli źródła.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z zabezpieczaniem klastrów AKS, zobacz [Uaktualnianie klastra AKS][aks-upgrade-cluster].

Aby zapoznać się z skojarzonymi najlepszymi rozwiązaniami, zobacz [Najważniejsze wskazówki dotyczące zabezpieczeń klastra i uaktualnień w uchronieniu AKS][operator-best-practices-cluster-security] i [Najważniejsze wskazówki dotyczące zabezpieczeń zasobników w ucho.][developer-best-practices-pod-security]

Aby uzyskać dodatkowe informacje na temat podstawowych pojęć kubernetów i usługi AKS, zobacz następujące artykuły:

- [Klastry i obciążenia Kubernetes / AKS][aks-concepts-clusters-workloads]
- [Kubernetes / AKS tożsamości][aks-concepts-identity]
- [Sieci wirtualne Kubernetes / AKS][aks-concepts-network]
- [Kubernetes / AKS pamięci masowej][aks-concepts-storage]
- [Skala Kubernetes / AKS][aks-concepts-scale]

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[secret-risks]: https://kubernetes.io/docs/concepts/configuration/secret/#risks

<!-- LINKS - Internal -->
[aks-daemonsets]: concepts-clusters-workloads.md#daemonsets
[aks-upgrade-cluster]: upgrade-cluster.md
[aks-aad]: azure-ad-integration.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[cluster-isolation]: operator-best-practices-cluster-isolation.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[developer-best-practices-pod-security]:developer-best-practices-pod-security.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
