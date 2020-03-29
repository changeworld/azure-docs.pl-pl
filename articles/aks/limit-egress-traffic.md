---
title: Ograniczanie ruchu wychodzącego w usłudze Azure Kubernetes (AKS)
description: Dowiedz się, jakie porty i adresy są wymagane do kontrolowania ruchu wychodzącego w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: 2cd7aeea272d22615d3ba3d3db6acc2c84d22cca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080187"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>Sterowanie ruchem wychodzącym dla węzłów klastra w usłudze Azure Kubernetes Service (AKS)

Domyślnie klastry AKS mają nieograniczony wychodzący (wychodzący) dostęp do Internetu. Ten poziom dostępu do sieci umożliwia uruchamianie węzłów i usług w celu uzyskania dostępu do zasobów zewnętrznych zgodnie z potrzebami. Jeśli chcesz ograniczyć ruch wychodzący, ograniczona liczba portów i adresów musi być dostępna do obsługi zadań konserwacji klastra w dobrej kondycji. Klaster jest domyślnie skonfigurowany do używania tylko podstawowych obrazów kontenerów systemu z rejestru kontenerów firmy Microsoft (MCR) lub rejestru kontenerów platformy Azure (ACR). Skonfiguruj preferowaną zaporę i reguły zabezpieczeń, aby zezwolić na te wymagane porty i adresy.

W tym artykule opisano, jakie porty sieciowe i w pełni kwalifikowane nazwy domen (FQDN) są wymagane i opcjonalne, jeśli ograniczysz ruch wychodzący w klastrze AKS.

> [!IMPORTANT]
> Ten dokument obejmuje tylko sposób blokowania ruchu opuszczającego podsieć usługi AKS. AKS nie ma wymagań przychodzących.  Blokowanie ruchu wewnętrznego podsieci przy użyciu sieciowych grup zabezpieczeń (NSG) i zapór nie jest obsługiwane. Aby kontrolować i blokować ruch w klastrze, użyj [funkcji Zasady sieciowe][network-policy].

## <a name="before-you-begin"></a>Przed rozpoczęciem

Potrzebujesz zainstalowanego i skonfigurowany interfejsu wiersza polecenia platformy Azure w wersji 2.0.66 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="egress-traffic-overview"></a>Przegląd ruchu wychodzącego

Do celów zarządzania i operacyjnych węzły w klastrze AKS muszą uzyskiwać dostęp do niektórych portów i w pełni kwalifikowanych nazw domen (FQDN). Te akcje mogą być do komunikowania się z serwerem interfejsu API lub do pobrania, a następnie zainstalować składniki klastra core Kubernetes i aktualizacje zabezpieczeń węzła. Domyślnie ruch internetowy ruchu wychodzącego (wychodzącego) nie jest ograniczony dla węzłów w klastrze AKS. Klaster może pobierać obrazy kontenerów systemu podstawowego z zewnętrznych repozytoriów.

Aby zwiększyć bezpieczeństwo klastra usługi AKS, można ograniczyć ruch wychodzący. Klaster jest skonfigurowany do ściągania obrazów kontenerów systemu podstawowego z mcr lub acr. Jeśli ruch wychodzący zostanie zablokowany w ten sposób, zdefiniuj określone porty i nazwy FQDN, aby umożliwić węzłom AKS prawidłową komunikację z wymaganymi usługami zewnętrznymi. Bez tych autoryzowanych portów i sieci FQDN węzły usługi AKS nie mogą komunikować się z serwerem interfejsu API ani instalować podstawowych składników.

[Zapora azure][azure-firewall] lub urządzenie zapory innej firmy można użyć do zabezpieczenia ruchu wychodzącego i zdefiniowania tych wymaganych portów i adresów. AKS nie tworzy automatycznie tych reguł dla Ciebie. Następujące porty i adresy są do odwołania podczas tworzenia odpowiednich reguł w zaporze sieciowej.

> [!IMPORTANT]
> Korzystając z usługi Azure Firewall, aby ograniczyć ruch wychodzący i utworzyć trasę zdefiniowaną przez użytkownika (UDR), aby wymusić cały ruch wychodzący, upewnij się, że utworzysz odpowiednią regułę DNAT w zaporze, aby poprawnie zezwolić na ruch przychodzący. Korzystanie z zapory azure z UDR przerywa konfigurację transferu danych przychodzących z powodu routingu asymetrycznego. (Problem występuje, jeśli podsieć AKS ma domyślną trasę, która przechodzi do prywatnego adresu IP zapory, ale używasz publicznego modułu równoważenia obciążenia — przychodzących lub usługi Kubernetes typu: LoadBalancer). W takim przypadku ruch przychodzącego modułu równoważenia obciążenia jest odbierany za pośrednictwem publicznego adresu IP, ale ścieżka powrotu przechodzi przez prywatny adres IP zapory. Ponieważ zapora jest stanowa, porzuca powracający pakiet, ponieważ zapora nie jest świadoma ustalonej sesji. Aby dowiedzieć się, jak zintegrować Zaporę platformy Azure z modułem transferuwładnika transferu ruchu przychodzącego lub modułu równoważenia obciążenia usługi, zobacz [Integrowanie zapory azure z modułem równoważenia obciążenia standardowego platformy Azure](https://docs.microsoft.com/azure/firewall/integrate-lb).
> Ruch dla portu TCP 9000, portu TCP 22 i portu UDP 1194 można zablokować, korzystając z reguły sieciowej między adresami IP węzłów procesu dostępu wychodzącego a adresem IP serwera INTERFEJSU API.

W uzywniu AKS istnieją dwa zestawy portów i adresów:

* [Wymagane porty i adres dla klastrów usługi AKS](#required-ports-and-addresses-for-aks-clusters) szczegółowe minimalne wymagania dotyczące autoryzowanego ruchu wychodzącego.
* [Opcjonalne zalecane adresy i porty dla klastrów usługi AKS](#optional-recommended-addresses-and-ports-for-aks-clusters) nie są wymagane dla wszystkich scenariuszy, ale integracja z innymi usługami, takimi jak Usługa Azure Monitor, nie będzie działać poprawnie. Przejrzyj tę listę opcjonalnych portów i sieci FQDN i autoryzuj dowolne usługi i składniki używane w klastrze AKS.

> [!NOTE]
> Ograniczenie ruchu wychodzącego działa tylko w nowych klastrach AKS. W przypadku istniejących klastrów wykonaj operację `az aks upgrade` [uaktualniania klastra][aks-upgrade] za pomocą polecenia przed ograniczeniem ruchu wychodzącego.

## <a name="required-ports-and-addresses-for-aks-clusters"></a>Wymagane porty i adresy dla klastrów usługi AKS

Dla klastra AKS wymagane są następujące porty wychodzące/ reguły sieciowe:

* Port TCP *443*
* Protokół TCP [IPAddrOfYourAPIServer]:443 jest wymagany, jeśli masz aplikację, która musi rozmawiać z serwerem interfejsu API.  Tę zmianę można ustawić po utworzeniu klastra.
* Port TCP *9000*, port TCP *22* i port UDP *1194* dla zasobnika frontowego tunelu do komunikowania się z końcem tunelu na serwerze INTERFEJSU API.
    * Aby uzyskać bardziej szczegółowe, zobacz **.hcp.\< lokalizacji\>.azmk8s.io* i **.tun.\< lokalizacji\>.azmk8s.io* adresów w poniższej tabeli.
* Port UDP *123* do synchronizacji czasu (NTP) (węzły Systemu Linux).
* Port UDP *53* dla SYSTEMU DNS jest również wymagany, jeśli masz zasobników bezpośrednio uzyskujących dostęp do serwera INTERFEJSU API.

Wymagane są następujące reguły FQDN / application:

> [!IMPORTANT]
> ***Blob.core.windows.net i aksrepos.azurecr.io** nie są już wymagane reguły FQDN dla blokady ruchu wychodzącego.  W przypadku istniejących klastrów wykonaj operację `az aks upgrade` [uaktualniania klastra][aks-upgrade] za pomocą polecenia, aby usunąć te reguły.

> [!IMPORTANT]
> *.cdn.mscr.io została zastąpiona przez *.data.mcr.microsoft.com dla regionów chmury publicznej platformy Azure. Uaktualnij istniejące reguły zapory, aby zmiany zostały wprowadzone.

- Azure Global

| Nazwa FQDN                       | Port      | Użycie      |
|----------------------------|-----------|----------|
| *.hcp. \<lokalizacja\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Ten adres jest wymagany do komunikacji z serwerem < > interfejsu API. Zastąp * \<lokalizację\> * regionem, w którym jest wdrażany klaster AKS. |
| *.tun. \<lokalizacja\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Ten adres jest wymagany do komunikacji z serwerem < > interfejsu API. Zastąp * \<lokalizację\> * regionem, w którym jest wdrażany klaster AKS. |
| *.cdn.mscr.io       | Protokół HTTPS:443 | Ten adres jest wymagany dla magazynu MCR wspieranego przez sieć dostarczania zawartości platformy Azure (CDN). |
| mcr.microsoft.com          | Protokół HTTPS:443 | Ten adres jest wymagany do uzyskania dostępu do obrazów w rejestrze kontenerów firmy Microsoft (MCR). Rejestr ten zawiera obrazy/wykresy (na przykład moby itp.) wymagane do działania klastra podczas uaktualniania i skalowania klastra |
| *.data.mcr.microsoft.com             | Protokół HTTPS:443 | Ten adres jest wymagany dla magazynu MCR wspieranego przez sieć dostarczania zawartości platformy Azure (CDN). |
| management.azure.com       | Protokół HTTPS:443 | Ten adres jest wymagany dla operacji GET/PUT firmy Kubernetes. |
| login.microsoftonline.com  | Protokół HTTPS:443 | Ten adres jest wymagany do uwierzytelniania usługi Azure Active Directory. |
| ntp.ubuntu.com             | UDP:123   | Ten adres jest wymagany do synchronizacji czasu NTP w węzłach systemu Linux. |
| packages.microsoft.com     | Protokół HTTPS:443 | Ten adres jest repozytorium pakietów firmy Microsoft używanym do buforowanych operacji *apt-get.*  Przykładowe pakiety obejmują Moby, PowerShell i Azure CLI. |
| acs-mirror.azureedge.net      | Protokół HTTPS:443 | Ten adres jest dla repozytorium wymagane do zainstalowania wymaganych plików binarnych, takich jak kubenet i Azure CNI. |

- Azure w Chinach — 21Vianet

| Nazwa FQDN                       | Port      | Użycie      |
|----------------------------|-----------|----------|
| *.hcp. \<lokalizacja\>.cx.prod.service.azk8s.cn | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Ten adres jest wymagany do komunikacji z serwerem < > interfejsu API. Zastąp * \<lokalizację\> * regionem, w którym jest wdrażany klaster AKS. |
| *.tun. \<lokalizacja\>.cx.prod.service.azk8s.cn | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Ten adres jest wymagany do komunikacji z serwerem < > interfejsu API. Zastąp * \<lokalizację\> * regionem, w którym jest wdrażany klaster AKS. |
| *.azk8s.cn        | Protokół HTTPS:443 | Ten adres jest wymagany do pobrania wymaganych plików binarnych i obrazów|
| mcr.microsoft.com          | Protokół HTTPS:443 | Ten adres jest wymagany do uzyskania dostępu do obrazów w rejestrze kontenerów firmy Microsoft (MCR). Rejestr ten zawiera obrazy/wykresy (na przykład moby itp.) wymagane do działania klastra podczas uaktualniania i skalowania klastra |
| *.cdn.mscr.io       | Protokół HTTPS:443 | Ten adres jest wymagany dla magazynu MCR wspieranego przez sieć dostarczania zawartości platformy Azure (CDN). |
| *.data.mcr.microsoft.com             | Protokół HTTPS:443 | Ten adres jest wymagany dla magazynu MCR wspieranego przez sieć dostarczania zawartości platformy Azure (CDN). |
| management.chinacloudapi.cn       | Protokół HTTPS:443 | Ten adres jest wymagany dla operacji GET/PUT firmy Kubernetes. |
| login.chinacloudapi.cn  | Protokół HTTPS:443 | Ten adres jest wymagany do uwierzytelniania usługi Azure Active Directory. |
| ntp.ubuntu.com             | UDP:123   | Ten adres jest wymagany do synchronizacji czasu NTP w węzłach systemu Linux. |
| packages.microsoft.com     | Protokół HTTPS:443 | Ten adres jest repozytorium pakietów firmy Microsoft używanym do buforowanych operacji *apt-get.*  Przykładowe pakiety obejmują Moby, PowerShell i Azure CLI. |

- Azure Government

| Nazwa FQDN                       | Port      | Użycie      |
|----------------------------|-----------|----------|
| *.hcp. \<lokalizacja\>.cx.aks.containerservice.azure.us | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Ten adres jest wymagany do komunikacji z serwerem < > interfejsu API. Zastąp * \<lokalizację\> * regionem, w którym jest wdrażany klaster AKS. |
| *.tun. \<lokalizacja\>.cx.aks.containerservice.azure.us | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Ten adres jest wymagany do komunikacji z serwerem < > interfejsu API. Zastąp * \<lokalizację\> * regionem, w którym jest wdrażany klaster AKS. |
| mcr.microsoft.com          | Protokół HTTPS:443 | Ten adres jest wymagany do uzyskania dostępu do obrazów w rejestrze kontenerów firmy Microsoft (MCR). Rejestr ten zawiera obrazy/wykresy (na przykład moby itp.) wymagane do działania klastra podczas uaktualniania i skalowania klastra |
|*.cdn.mscr.io              | Protokół HTTPS:443 | Ten adres jest wymagany dla magazynu MCR wspieranego przez sieć dostarczania zawartości platformy Azure (CDN). |
| *.data.mcr.microsoft.com             | Protokół HTTPS:443 | Ten adres jest wymagany dla magazynu MCR wspieranego przez sieć dostarczania zawartości platformy Azure (CDN). |
| management.usgovcloudapi.net       | Protokół HTTPS:443 | Ten adres jest wymagany dla operacji GET/PUT firmy Kubernetes. |
| login.microsoftonline.us  | Protokół HTTPS:443 | Ten adres jest wymagany do uwierzytelniania usługi Azure Active Directory. |
| ntp.ubuntu.com             | UDP:123   | Ten adres jest wymagany do synchronizacji czasu NTP w węzłach systemu Linux. |
| packages.microsoft.com     | Protokół HTTPS:443 | Ten adres jest repozytorium pakietów firmy Microsoft używanym do buforowanych operacji *apt-get.*  Przykładowe pakiety obejmują Moby, PowerShell i Azure CLI. |
| acs-mirror.azureedge.net      | Protokół HTTPS:443 | Ten adres jest dla repozytorium wymagane do zainstalowania wymaganych plików binarnych, takich jak kubenet i Azure CNI. |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>Opcjonalne zalecane adresy i porty dla klastrów AKS

Następujące porty wychodzące / reguły sieciowe są opcjonalne dla klastra AKS:

Następujące reguły FQDN / application są zalecane dla klastrów AKS do poprawnego działania:

| Nazwa FQDN                                    | Port      | Użycie      |
|-----------------------------------------|-----------|----------|
| security.ubuntu.com, azure.archive.ubuntu.com, changelogs.ubuntu.com | HTTP:80   | Ten adres umożliwia węzłom klastra systemu Linux pobieranie wymaganych poprawek i aktualizacji zabezpieczeń. |

## <a name="required-addresses-and-ports-for-gpu-enabled-aks-clusters"></a>Wymagane adresy i porty dla klastrów AKS z obsługą gpu

Następujące reguły FQDN / application są wymagane dla klastrów AKS z włączonym procesorem GPU:

| Nazwa FQDN                                    | Port      | Użycie      |
|-----------------------------------------|-----------|----------|
| nvidia.github.io | Protokół HTTPS:443 | Ten adres jest używany do poprawnej instalacji i działania sterownika w węzłach opartych na procesorze GPU. |
| us.download.nvidia.com | Protokół HTTPS:443 | Ten adres jest używany do poprawnej instalacji i działania sterownika w węzłach opartych na procesorze GPU. |
| apt.dockerproject.org | Protokół HTTPS:443 | Ten adres jest używany do poprawnej instalacji i działania sterownika w węzłach opartych na procesorze GPU. |

## <a name="required-addresses-and-ports-with-azure-monitor-for-containers-enabled"></a>Wymagane adresy i porty z włączoną usługą Azure Monitor dla kontenerów

Następujące reguły FQDN / application są wymagane dla klastrów AKS, które mają włączoną usługę Azure Monitor dla kontenerów:

| Nazwa FQDN                                    | Port      | Użycie      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | Protokół HTTPS:443    | Jest to dla poprawnych metryk i monitorowania danych telemetrycznych przy użyciu usługi Azure Monitor. |
| *.ods.opinsights.azure.com    | Protokół HTTPS:443    | Jest to używane przez usługę Azure Monitor do pozyskiwania danych analizy dziennika. |
| *.oms.opinsights.azure.com | Protokół HTTPS:443 | Ten adres jest używany przez program omsagent, który służy do uwierzytelniania usługi analizy dzienników. |
|*.microsoftonline.com | Protokół HTTPS:443 | Służy do uwierzytelniania i wysyłania metryk do usługi Azure Monitor. |
|*.monitoring.azure.com | Protokół HTTPS:443 | Służy do wysyłania danych metryk do usługi Azure Monitor. |

## <a name="required-addresses-and-ports-with-azure-dev-spaces-enabled"></a>Wymagane adresy i porty z włączoną usługą Azure Dev Spaces

Następujące reguły FQDN / application są wymagane dla klastrów AKS, które mają włączone usługi Azure Dev Spaces włączone:

| Nazwa FQDN                                    | Port      | Użycie      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | Protokół HTTPS:443 | Ten adres jest używany do ściągania linux alpine i innych obrazów Azure Dev Spaces |
| gcr.io | HTTP:443 | Ten adres służy do ciągnięcia obrazów helm/tiller |
| storage.googleapis.com | HTTP:443 | Ten adres służy do ciągnięcia obrazów helm/tiller |
| azds-\<guid\>. \<lokalizacja\>.azds.io | Protokół HTTPS:443 | Aby komunikować się z usługami zaplecza usługi Azure Dev Spaces dla kontrolera. Dokładną nazwy FQDN można znaleźć w "dataplaneFqdn"\.w %USERPROFILE% azds\settings.json |

## <a name="required-addresses-and-ports-for-aks-clusters-with-azure-policy-in-public-preview-enabled"></a>Wymagane adresy i porty klastrów AKS z włączoną usługą Azure Policy (w publicznej wersji zapoznawczej)

> [!CAUTION]
> Niektóre z poniższych funkcji są w wersji zapoznawczej.  Sugestie w tym artykule mogą ulec zmianie w miarę przenoszenia funkcji do publicznej wersji zapoznawczej i przyszłych etapów wydania.

Następujące reguły FQDN / application są wymagane dla klastrów AKS, które mają włączoną zasadę azure.

| Nazwa FQDN                                    | Port      | Użycie      |
|-----------------------------------------|-----------|----------|
| gov-prod-policy-data.trafficmanager.net | Protokół HTTPS:443 | Ten adres jest używany do poprawnego działania zasad platformy Azure. (obecnie w wersji zapoznawczej w AKS) |
| raw.githubusercontent.com | Protokół HTTPS:443 | Ten adres jest używany do ściągania wbudowanych zasad z usługi GitHub w celu zapewnienia prawidłowego działania zasad platformy Azure. (obecnie w wersji zapoznawczej w AKS) |
| *.gk. \<lokalizacja\>.azmk8s.io | Protokół HTTPS:443    | Dodatek zasad platformy Azure, który prowadzi rozmowy z punktem końcowym inspekcji gatekeeper uruchomionego na serwerze głównym w celu uzyskania wyników inspekcji. |
| dc.services.visualstudio.com | Protokół HTTPS:443 | Dodatek zasad platformy Azure, który wysyła dane telemetryczne do punktu końcowego aplikacji insights. |

## <a name="required-by-windows-server-based-nodes-in-public-preview-enabled"></a>Wymagane przez węzły oparte na systemie Windows Server (w publicznej wersji zapoznawczej) włączone

> [!CAUTION]
> Niektóre z poniższych funkcji są w wersji zapoznawczej.  Sugestie w tym artykule mogą ulec zmianie w miarę przenoszenia funkcji do publicznej wersji zapoznawczej i przyszłych etapów wydania.

Dla klastrów AKS opartych na systemie Windows Server wymagane są następujące reguły FQDN/ application:

| Nazwa FQDN                                    | Port      | Użycie      |
|-----------------------------------------|-----------|----------|
| onegetcdn.azureedge.net, winlayers.blob.core.windows.net, winlayers.cdn.mscr.io, go.microsoft.com | Protokół HTTPS:443 | Aby zainstalować pliki binarne związane z systemem Windows |
| mp.microsoft.com, www<span></span>.msftconnecttest.com, ctldl.windowsupdate.com | HTTP:80 | Aby zainstalować pliki binarne związane z systemem Windows |
| kms.core.windows.net | TCP:1688 | Aby zainstalować pliki binarne związane z systemem Windows |


## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jakie porty i adresy, aby umożliwić, jeśli ograniczysz ruch wychodzący dla klastra. Można również zdefiniować, w jaki sposób zasobników mogą komunikować się i jakie ograniczenia mają w klastrze. Aby uzyskać więcej informacji, zobacz [Zabezpieczanie ruchu między zasobnikami przy użyciu zasad sieciowych w uzywmiacza][network-policy].

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policy]: use-network-policies.md
[azure-firewall]: ../firewall/overview.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-upgrade]: upgrade-cluster.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
