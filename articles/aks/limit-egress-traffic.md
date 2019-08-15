---
title: Ogranicz ruch wychodzący w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jakie porty i adresy są wymagane do sterowania ruchem wychodzącym w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/06/2019
ms.author: mlearned
ms.openlocfilehash: cf9dc304efea8874d16953f74bf88a4317760819
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69031839"
---
# <a name="preview---limit-egress-traffic-for-cluster-nodes-and-control-access-to-required-ports-and-services-in-azure-kubernetes-service-aks"></a>Wersja zapoznawcza — Ogranicz ruch wychodzący dla węzłów klastra i Kontroluj dostęp do wymaganych portów i usług w usłudze Azure Kubernetes Service (AKS)

Domyślnie klastry AKS mają nieograniczony dostęp do Internetu (ruch wychodzący). Ten poziom dostępu do sieci umożliwia uruchamianie węzłów i usług w celu uzyskania dostępu do zasobów zewnętrznych w razie potrzeby. Aby ograniczyć ruch wychodzący, należy uzyskać dostęp do nieograniczonej liczby portów i adresów, aby zachować prawidłowe zadania konserwacji klastra. Klaster jest następnie skonfigurowany tak, aby używał wyłącznie obrazów kontenera systemu podstawowego z programu Microsoft Container Registry (MCR) lub Azure Container Registry (ACR), a nie zewnętrznych repozytoriów publicznych. Musisz skonfigurować preferowane reguły zapory i zabezpieczeń, aby zezwolić na te wymagane porty i adresy.

W tym artykule opisano, które porty sieciowe i w pełni kwalifikowane nazwy domen (FQDN) są wymagane i opcjonalne w przypadku ograniczenia ruchu wychodzącego w klastrze AKS.  Ta funkcja jest obecnie dostępna w wersji zapoznawczej.

> [!IMPORTANT]
> Funkcja AKS w wersji zapoznawczej to samoobsługowe uczestnictwo. Wersje zapoznawcze są udostępniane w postaci "AS-IS" i "jako dostępne" i są wyłączone z umów dotyczących poziomu usług i ograniczonej rękojmi. Wersje zapoznawcze AKS są częściowo objęte obsługą klienta w oparciu o najlepszy nakład pracy. W związku z tym te funkcje nie są przeznaczone do użytku produkcyjnego. Aby dowiedzieć się więcej, zobacz następujące artykuły pomocy technicznej:
>
> * [Zasady pomocy technicznej AKS][aks-support-policies]
> * [Pomoc techniczna platformy Azure — często zadawane pytania][aks-faq]

## <a name="before-you-begin"></a>Przed rozpoczęciem

Wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.66 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

Aby utworzyć klaster AKS, który może ograniczać ruch wychodzący, należy najpierw włączyć flagę funkcji w ramach subskrypcji. Ta rejestracja funkcji konfiguruje wszystkie klastry AKS utworzone w celu używania obrazów kontenera systemu podstawowego z MCR lub ACR. Aby zarejestrować flagę funkcji *AKSLockingDownEgressPreview* , użyj polecenia [AZ Feature Register][az-feature-register] , jak pokazano w następującym przykładzie:

> [!CAUTION]
> Po zarejestrowaniu funkcji w ramach subskrypcji nie można obecnie wyrejestrować tej funkcji. Po włączeniu niektórych funkcji w wersji zapoznawczej można użyć wartości domyślnych dla wszystkich klastrów AKS utworzonych w ramach subskrypcji. Nie włączaj funkcji w wersji zapoznawczej w ramach subskrypcji produkcyjnych. Korzystaj z oddzielnej subskrypcji, aby testować funkcje w wersji zapoznawczej i zbierać opinie.

```azurecli-interactive
az feature register --name AKSLockingDownEgressPreview --namespace Microsoft.ContainerService
```

Wyświetlenie stanu *rejestracji*może potrwać kilka minut. Stan rejestracji można sprawdzić za pomocą polecenia [AZ Feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSLockingDownEgressPreview')].{Name:name,State:properties.state}"
```

Gdy wszystko będzie gotowe, Odśwież rejestrację dostawcy zasobów *Microsoft. ContainerService* za pomocą polecenia [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="egress-traffic-overview"></a>Ruch wychodzący — Omówienie

W celach zarządzania i operacyjnych węzły w klastrze AKS muszą uzyskiwać dostęp do określonych portów i w pełni kwalifikowanych nazw domen (FQDN). Te akcje mogą być komunikowane się z serwerem interfejsu API lub do pobrania, a następnie zainstalowania podstawowych składników klastra Kubernetes i aktualizacji zabezpieczeń węzła. Domyślnie ruch internetowy (wychodzący) nie jest ograniczony dla węzłów w klastrze AKS. Klaster może pobierać podstawowe obrazy kontenerów systemu z zewnętrznych repozytoriów.

Aby zwiększyć bezpieczeństwo klastra AKS, możesz ograniczyć ruch wychodzący. Klaster jest skonfigurowany do ściągania obrazów kontenera systemu podstawowego z MCR lub ACR. W przypadku zablokowania ruchu wychodzącego w ten sposób należy zdefiniować określone porty i nazwy FQDN, aby umożliwić węzłom AKS poprawne komunikowanie się z wymaganymi usługami zewnętrznymi. Bez tych autoryzowanych portów i nazw FQDN węzły AKS nie mogą komunikować się z serwerem interfejsu API ani instalować składników podstawowych.

Do zabezpieczenia ruchu wychodzącego i definiowania wymaganych portów i adresów można użyć [zapory platformy Azure][azure-firewall] lub urządzenia zapory innej firmy. Usługa AKS nie tworzy automatycznie tych reguł. Następujące porty i adresy są przeznaczone do celów referencyjnych podczas tworzenia odpowiednich reguł w zaporze sieciowej.

W programie AKS istnieją dwa zestawy portów i adresów:

* [Wymagane porty i adresy dla klastrów AKS](#required-ports-and-addresses-for-aks-clusters) zawierają szczegóły dotyczące minimalnych wymagań dla autoryzowanego ruchu wychodzącego.
* [Opcjonalne zalecane adresy i porty klastrów AKS](#optional-recommended-addresses-and-ports-for-aks-clusters) nie są wymagane dla wszystkich scenariuszy, ale integracja z innymi usługami, takimi jak Azure monitor nie działa prawidłowo. Przejrzyj tę listę opcjonalnych portów i nazw FQDN oraz Autoryzuj wszystkie usługi i składniki używane w klastrze AKS.

> [!NOTE]
> Ograniczanie ruchu wychodzącego działa tylko w przypadku nowych klastrów AKS utworzonych po włączeniu rejestracji flag funkcji. W przypadku istniejących klastrów należy [wykonać operację uaktualniania klastra][aks-upgrade] przy użyciu `az aks upgrade` polecenia przed ograniczeniem ruchu wychodzącego.

## <a name="required-ports-and-addresses-for-aks-clusters"></a>Wymagane porty i adresy dla klastrów AKS

Następujące porty wychodzące/reguły sieciowe są wymagane dla klastra AKS:

* Port TCP *443*
* Port TCP *9000* i port TCP *22* dla frontonu tunelu do komunikacji z końcem tunelu na serwerze interfejsu API.
    * Aby uzyskać bardziej szczegółowe informacje, zobacz * *. HCP.\< Location\>. azmk8s.IO* i * *. TUN.\< adresy\>. azmk8s.IO* w poniższej tabeli.

Wymagane są następujące reguły dotyczące nazwy FQDN/aplikacji:

| NAZWA FQDN                       | Port      | Zastosowanie      |
|----------------------------|-----------|----------|
| *.hcp. \<Location\>. azmk8s.IO | HTTPS:443, TCP:22, TCP:9000 | Ten adres jest punktem końcowym serwera interfejsu API. *Zamień\<lokalizację\>* na region, w którym wdrożono klaster AKS. |
| *.tun. \<Location\>. azmk8s.IO | HTTPS:443, TCP:22, TCP:9000 | Ten adres jest punktem końcowym serwera interfejsu API. *Zamień\<lokalizację\>* na region, w którym wdrożono klaster AKS. |
| aksrepos.azurecr.io        | HTTPS:443 | Ten adres jest wymagany w celu uzyskania dostępu do obrazów w Azure Container Registry (ACR). |
| *.blob.core.windows.net    | HTTPS:443 | Ten adres jest magazynem zaplecza dla obrazów przechowywanych w ACR. |
| mcr.microsoft.com          | HTTPS:443 | Ten adres jest wymagany w celu uzyskania dostępu do obrazów w programie Microsoft Container Registry (MCR). |
| *.cdn.mscr.io              | HTTPS:443 | Ten adres jest wymagany dla magazynu MCR obsługiwanego przez usługę Azure Content Delivery Network (CDN). |
| management.azure.com       | HTTPS:443 | Ten adres jest wymagany do Kubernetes operacji pobierania/UMIESZCZAnia. |
| login.microsoftonline.com  | HTTPS:443 | Ten adres jest wymagany na potrzeby uwierzytelniania Azure Active Directory. |
| api.snapcraft.io           | HTTPS:443, HTTP:80 | Ten adres jest wymagany do zainstalowania pakietów Snap w węzłach systemu Linux. |
| ntp.ubuntu.com             | UDP:123   | Ten adres jest wymagany w przypadku synchronizacji czasu NTP w węzłach systemu Linux. |
| *. docker.io                | HTTPS:443 | Ten adres jest wymagany do ściągnięcia wymaganych obrazów kontenerów dla frontonu tunelu. |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>Opcjonalne zalecane adresy i porty dla klastrów AKS

* Port UDP *53* dla systemu DNS

Następujące reguły dotyczące nazwy FQDN/aplikacji są zalecane, aby klastry AKS działały prawidłowo:

| NAZWA FQDN                                    | Port      | Zastosowanie      |
|-----------------------------------------|-----------|----------|
| *.ubuntu.com                            | HTTP:80   | Ten adres umożliwia pobranie przez węzły klastra systemu Linux wymaganych poprawek i aktualizacji zabezpieczeń. |
| packages.microsoft.com                  | HTTPS:443 | Ten adres jest repozytorium pakietów firmy Microsoft używanym do buforowanych operacji *apt-get* . |
| dc.services.visualstudio.com            | HTTPS:443 | Zalecane do poprawnego metryk i monitorowania przy użyciu Azure Monitor. |
| *.opinsights.azure.com                  | HTTPS:443 | Zalecane do poprawnego metryk i monitorowania przy użyciu Azure Monitor. |
| *. monitoring.azure.com                  | HTTPS:443 | Zalecane do poprawnego metryk i monitorowania przy użyciu Azure Monitor. |
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | Ten adres jest używany do poprawnego działania Azure Policy (obecnie w wersji zapoznawczej w AKS). |
| apt.dockerproject.org                   | HTTPS:443 | Ten adres jest używany do poprawnego instalowania i działania sterowników w węzłach opartych na procesorze GPU. |
| nvidia.github.io                        | HTTPS:443 | Ten adres jest używany do poprawnego instalowania i działania sterowników w węzłach opartych na procesorze GPU. |

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera informacje o portach i adresach, które mają być dozwolone w przypadku ograniczenia ruchu wychodzącego dla klastra. Można również określić, jak same z nich mogą komunikować się i jakie ograniczenia mają w klastrze. Aby uzyskać więcej informacji, zobacz [bezpieczny ruch między różnymi elementami sieciowymi przy użyciu zasad sieciowych w AKS][network-policy].

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
