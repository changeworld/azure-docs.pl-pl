---
title: Pojęcia — zabezpieczeń w usługach Azure Kubernetes (AKS)
description: Dowiedz się więcej na temat zabezpieczeń w usłudze Azure Kubernetes Service (AKS), tym komunikacji głównego i języka node, zasad sieciowych i wpisy tajne usługi Kubernetes.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: mlearned
ms.openlocfilehash: 1d100f17130594ace6169f5840915c88435cb9a8
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67615770"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Pojęcia dotyczące zabezpieczeń dla aplikacji i klastrów w usłudze Azure Kubernetes Service (AKS)

Aby chronić Twoje dane klienta, jak uruchamiać obciążenia aplikacji w usłudze Azure Kubernetes Service (AKS), zabezpieczenie klastra jest klucza. Kubernetes obejmuje składniki zabezpieczeń, takie jak *zasad sieciowych* i *wpisów tajnych*. Platforma Azure następnie dodaje składniki, takie jak sieciowe grupy zabezpieczeń i zorganizowanych uaktualnienia klastra. Te składniki zabezpieczeń są łączone w celu zachowania klaster AKS najnowsze aktualizacje zabezpieczeń systemu operacyjnego i wersji platformy Kubernetes i za pomocą zabezpieczonej zasobnika ruchu i dostęp do poufnych poświadczeń.

W tym artykule przedstawiono podstawowe pojęcia, z których zabezpieczania aplikacji w usłudze AKS:

- [Podstawowe składniki zabezpieczeń](#master-security)
- [Węzeł zabezpieczeń](#node-security)
- [Uaktualnianie klastra](#cluster-upgrades)
- [Bezpieczeństwo sieci](#network-security)
- [Wpisy tajne usługi Kubernetes](#kubernetes-secrets)

## <a name="master-security"></a>Zabezpieczenia głównego

W usłudze AKS składniki rozwiązania Kubernetes są częścią usług zarządzanych przez firmę Microsoft. Każdy klaster AKS jest własne wzorzec Kubernetes gośćmi jednego, przeznaczonego do zapewnienia serwera interfejsu API, harmonogram itp. Ten wzorzec jest zarządzana i obsługiwana przez firmę Microsoft.

Domyślnie serwer interfejsu API rozwiązania Kubernetes używa publicznego adresu IP i za pomocą w pełni kwalifikowaną nazwę (FQDN). Można kontrolować dostęp do serwera interfejsu API przy użyciu kontroli dostępu opartej na rolach platformy Kubernetes i usługi Azure Active Directory. Aby uzyskać więcej informacji, zobacz [integracji z usługą Azure AD za pomocą usługi AKS][aks-aad].

## <a name="node-security"></a>Węzeł zabezpieczeń

Węzłów AKS są maszyn wirtualnych platformy Azure, które umożliwia zarządzanie i obsługa. Uruchom zoptymalizowane dystrybucji Ubuntu przy użyciu kontener środowiska uruchomieniowego Moby węzłów systemu Linux. Węzłów systemu Windows Server (obecnie dostępna w wersji zapoznawczej w usłudze AKS) uruchamiany zoptymalizowanej 2019 r Server systemu Windows Zwolnij, a także korzystania ze środowiska uruchomieniowego Moby kontenera. Gdy klaster AKS zostanie utworzony lub skalowany w górę, węzły zostają automatycznie wdrożone najnowsze aktualizacje zabezpieczeń systemu operacyjnego i konfiguracjach.

Platforma Azure automatycznie stosuje poprawki zabezpieczeń systemu operacyjnego do węzłów systemu Linux w nocy. Jeśli aktualizacja zabezpieczeń systemu operacyjnego Linux wymaga ponownego uruchomienia komputera hosta, że ponowne uruchomienie komputera nie jest wykonywana automatycznie. Użytkownik może ręcznie uruchomić węzłów systemu Linux lub typowym podejściem jest użycie [Kured][kured] , an open-source reboot daemon for Kubernetes. Kured runs as a [DaemonSet][aks-daemonsets] i monitoruje każdy węzeł na obecność pliku wskazujący, że wymagane jest ponowne uruchomienie komputera. Ponowne uruchamianie odbywa się w klastrze, korzystając z tych samych [odizolowywanie i opróżnianie procesu](#cordon-and-drain) jako uaktualniania klastra.

W przypadku węzłów systemu Windows Server (obecnie dostępna w wersji zapoznawczej w usłudze AKS) aktualizacja Windows automatycznie uruchamiania i Zastosuj najnowsze aktualizacje. Zgodnie z ustalonym harmonogramem w całym cyklu tworzenia wydań Windows Update i procesu sprawdzania poprawności należy wykonać uaktualnienie na pule węzłów systemu Windows Server, w klastrze AKS. Ten proces uaktualnienia tworzy węzły, które Uruchom najnowszego obrazu systemu Windows Server i poprawkami, a następnie usuwa starszą węzły. Aby uzyskać więcej informacji na temat tego procesu, zobacz [uaktualnienia pulę węzłów w usłudze AKS][nodepool-upgrade].

Węzły są wdrażane w podsieci prywatnej sieci wirtualnej przy użyciu nie publiczne adresy IP, które są przypisane. Do celów zarządzania i rozwiązywania problemów protokół SSH jest domyślnie włączona. Ten dostęp SSH jest dostępna tylko przy użyciu wewnętrznego adresu IP.

Aby udostępnić magazyn, węzły używają usługi Azure Managed Disks. Dla większości rozmiarów maszyn wirtualnych węzła są obsługiwane przez dyski SSD o wysokiej wydajności dysków w warstwie Premium. Dane przechowywane na dyskach zarządzanych są automatycznie szyfrowane w stanie spoczynku na platformie Azure. W celu zapewnienia nadmiarowości te dyski jednocześnie możliwość bezpiecznego są replikowane w obrębie centrum danych platformy Azure.

Środowisk Kubernetes w usłudze AKS, lub w innych miejscach, obecnie nie są całkowicie bezpieczne dla szkodliwy użycie wielu obcych dzierżaw. Funkcje dodatkowe zabezpieczenia, takie jak *zasad zabezpieczeń zasobnika* lub więcej kontroli dostępu w zakresie opartej na rolach (RBAC) dla węzłów utrudniają luki w zabezpieczeniach. Wartość true, zabezpieczeń przy uruchamianiu obciążeń liczonych w szkodliwy wielodostępne, funkcja hypervisor to tylko poziom zabezpieczeń, które należy ufać. Domeny zabezpieczeń dla rozwiązania Kubernetes staje się całego klastra, a nie oddzielnego węzła. Dla tych typów szkodliwy obciążenia z wieloma dzierżawami należy użyć fizycznie izolowane klastrów. Aby uzyskać więcej informacji na temat sposobów izolowania obciążeń, zobacz [najlepsze rozwiązania dotyczące izolacji klastra w usłudze AKS][cluster-isolation],

## <a name="cluster-upgrades"></a>Uaktualnianie klastra

Zabezpieczenia i zgodność z przepisami lub korzystać z najnowszych funkcji platforma Azure udostępnia narzędzia do organizowania Uaktualnianie klastra usługi AKS i składników. Takie ograniczenia uaktualniania zawiera zarówno Kubernetes głównego i agenta składniki. Możesz wyświetlić [listę dostępnych wersji rozwiązania Kubernetes](supported-kubernetes-versions.md) dla klastra usługi AKS. Aby uruchomić proces uaktualniania, należy określić jedną z tych dostępnych wersji. Platforma Azure, a następnie bezpiecznie cordons i opróżnia każdy węzeł usługi AKS i przeprowadza uaktualnienie.

### <a name="cordon-and-drain"></a>Cordon i opróżniania

Podczas procesu uaktualniania węzłów AKS są indywidualnie odizolowywane z klastra, dzięki czemu nowych zasobników nie są zaplanowane na nich. Węzły są następnie opróżniane i uaktualnione w następujący sposób:

- Nowy węzeł jest wdrażana w puli węzłów. Ten węzeł jest uruchamiany najnowszego obrazu systemu operacyjnego i poprawki.
- Jeden z istniejących węzłów, który jest identyfikowany do uaktualnienia. Zasobników, w tym węźle są bezpiecznie zakończone i zaplanowane na innych węzłach w puli węzłów.
- Ten istniejący węzeł jest usuwany z klastra AKS.
- Kolejnego węzła w klastrze jest odizolowywane i opróżniane przy użyciu tego samego procesu, aż wszystkie węzły są pomyślnie zastąpił jako część procesu uaktualniania.

Aby uzyskać więcej informacji, zobacz [Uaktualnianie klastra usługi AKS][aks-upgrade-cluster].

## <a name="network-security"></a>Bezpieczeństwo sieci

Dla łączności i zabezpieczeń w sieciach lokalnych można wdrożyć klaster AKS do istniejących podsieci sieci wirtualnej platformy Azure. Te sieci wirtualne mogą mieć Azure połączenia sieci VPN typu lokacja-lokacja lub usługi Express Route do sieci lokalnej. Kontrolery transferu danych przychodzących rozwiązania Kubernetes można zdefiniować przy użyciu prywatnych, wewnętrznych adresów IP tak usługi są dostępne tylko za pośrednictwem tego połączenia sieci wewnętrznej.

### <a name="azure-network-security-groups"></a>Grupy zabezpieczeń sieci platformy Azure

Aby filtrować przepływu ruchu w sieciach wirtualnych, platforma Azure stosuje reguły sieciowej grupy zabezpieczeń. Te reguły określają źródło i docelowe zakresy adresów IP, portów i protokołów, które są dozwolone lub odmowa dostępu do zasobów. Aby zezwolić na ruch protokołu TLS do serwera interfejsu API rozwiązania Kubernetes tworzone są reguły domyślne. Podczas tworzenia usługi przy użyciu usługi równoważenia obciążenia, mapowania portów lub ruch przychodzący trasy, AKS automatycznie modyfikuje sieciowej grupy zabezpieczeń dla ruchu odpowiedni przepływ.

## <a name="kubernetes-secrets"></a>Wpisy tajne usługi Kubernetes

Kubernetes *klucz tajny* służy do wstrzyknąć poufne dane do zasobników, takich jak dostęp do poświadczeń ani kluczy. Należy najpierw utworzyć wpis tajny przy użyciu interfejsu API rozwiązania Kubernetes. Podczas definiowania wdrożenia lub zasobników, można żądać określonego klucza tajnego. Klucze tajne są udostępniane tylko węzły, które mają zaplanowane zasobnik, który go wymaga i klucz tajny jest przechowywany w *tmpfs*robaków napisanych nie na dysku. Po usunięciu ostatniego zasobnik w węźle, który wymaga klucza tajnego klucza tajnego został usunięty z tmpfs węzła. Klucze tajne są przechowywane w ramach danego obszaru nazw i może zostać oceniony jedynie przez zasobników w tej samej przestrzeni nazw.

Korzystanie z kluczy tajnych zmniejsza poufne informacje, które jest zdefiniowana w zasobniku lub YAML manifestu usługi. Zamiast tego możesz poprosić hasła przechowywane w serwera interfejsu API rozwiązania Kubernetes jako część manifeście YAML. To podejście zapewnia tylko dostęp pod określonym do klucza tajnego. Uwaga: nieprzetworzone pliki manifestu tajny zawiera poufne dane w formacie base64 (zobacz [oficjalnej dokumentacji][secret-risks] Aby uzyskać więcej informacji). W związku z tym ten plik powinien być traktowane jako poufne informacje i nigdy nie zostały zatwierdzone do kontroli źródła.

## <a name="next-steps"></a>Kolejne kroki

Aby rozpocząć pracę z zabezpieczaniem klastry usługi AKS, zobacz [Uaktualnianie klastra usługi AKS][aks-upgrade-cluster].

Najlepsze rozwiązania dotyczące skojarzone, zobacz [najlepsze rozwiązania dotyczące zabezpieczeń klastra i uaktualnień w usłudze AKS][operator-best-practices-cluster-security].

Dodatkowe informacje na temat podstawowej platformy Kubernetes oraz pojęcia zostały przedstawione z usługi AKS zobacz następujące artykuły:

- [Kubernetes / klastrów usługi AKS i obciążeń][aks-concepts-clusters-workloads]
- [Kubernetes / tożsamości AKS][aks-concepts-identity]
- [Kubernetes / sieci wirtualne w usłudze AKS][aks-concepts-network]
- [Kubernetes / magazynu usługi AKS][aks-concepts-storage]
- [Kubernetes / skalowanie usługi AKS][aks-concepts-scale]

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
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
