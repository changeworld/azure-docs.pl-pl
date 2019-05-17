---
title: Ogranicz ruch wychodzący w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jakie porty i adresy są wymagane do kontroli ruchu wychodzącego ruchu w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/14/2019
ms.author: iainfou
ms.openlocfilehash: de0ba13a527569e446a44c275b7323d4487f53b6
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65780296"
---
# <a name="preview---limit-egress-traffic-for-cluster-nodes-and-control-access-to-required-ports-and-services-in-azure-kubernetes-service-aks"></a>(Wersja zapoznawcza) — ruch wychodzący Limit dla węzłów klastra i kontrola dostępu do wymaganych portów i usług w usłudze Azure Kubernetes Service (AKS)

Domyślnie klastry usługi AKS mają nieograniczony dostęp do Internetu wychodzących (dane wychodzące). Ten poziom dostępu do sieci umożliwia węzłów i usługi uruchomione na dostęp do zasobów zewnętrznych zgodnie z potrzebami. Jeśli chcesz ograniczyć ruch wychodzący ograniczoną liczbę portów i adresów muszą być dostępne do obsługi zadań konserwacji w dobrej kondycji klastra. Klaster jest następnie skonfigurowany do użycia tylko podstawowy system obrazy kontenera z rejestru kontenerów firmy Microsoft (MCR) lub Azure Container Registry (ACR), nie zewnętrzne z publicznych repozytoriów.

Ten artykuł szczegółowo opisuje jakie porty sieciowe i w pełni kwalifikowanych nazw domen (FQDN) są wymagane i opcjonalne, jeśli można ograniczyć ruch wychodzący w klastrze AKS.  Ta funkcja jest obecnie dostępna w wersji zapoznawczej.

> [!IMPORTANT]
> Funkcje w wersji zapoznawczej usługi AKS są samoobsługi i opcjonalnych. Wersje zapoznawcze są udostępniane do zbierania opinii i błędy z naszej społeczności. Nie są one jednak obsługiwane przez pomoc techniczną systemu Azure. Jeśli tworzenie klastra lub Dodaj następujące funkcje do istniejących klastrów tego klastra jest obsługiwany, dopóki ta funkcja nie jest już dostępna w wersji zapoznawczej i absolwentów, które są ogólnie dostępne (GA).
>
> Jeśli wystąpią problemy związane z wersji zapoznawczej, [Otwórz problem w repozytorium GitHub usługi AKS] [ aks-github] o nazwie funkcja w wersji zapoznawczej w tytuł usterki.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Potrzebujesz wiersza polecenia platformy Azure w wersji 2.0.61 lub później zainstalowane i skonfigurowane. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

Aby utworzyć klaster usługi AKS, które ogranicza ruch wychodzący, należy najpierw włączyć flagi funkcji w ramach Twojej subskrypcji. Rejestracja funkcja umożliwia skonfigurowanie każdego klastra AKS, można utworzyć na podstawowy system obrazy kontenerów z MCR lub ACR. Aby zarejestrować *AKSLockingDownEgressPreview* flagę funkcji, należy użyć [az feature register] [ az-feature-register] polecenia, jak pokazano w poniższym przykładzie:

```azurecli-interactive
az feature register --name AKSLockingDownEgressPreview --namespace Microsoft.ContainerService
```

Zajmuje kilka minut, zanim stan wyświetlany *zarejestrowanej*. Można sprawdzić stanu rejestracji za pomocą [lista funkcji az] [ az-feature-list] polecenia:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSLockingDownEgressPreview')].{Name:name,State:properties.state}"
```

Gdy wszystko będzie gotowe, Odśwież rejestracji *Microsoft.ContainerService* dostawcy zasobów za pomocą [az provider register] [ az-provider-register] polecenia:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="egress-traffic-overview"></a>Omówienie ruchu wychodzącego

Zarządzanie i cele operacyjne węzłów w klastrze AKS na potrzeby dostępu do określonych portów i w pełni kwalifikowanych nazw domen (FQDN). Te działania można aby komunikować się z serwera interfejsu API lub Pobierz i zainstaluj podstawowe składniki klastra Kubernetes i aktualizacji zabezpieczeń węzła. Domyślnie (ruch wychodzący) ruch internetowy ruch wychodzący nie jest ograniczona do węzłów w klastrze AKS. Klaster może Wypychaj podstawowego systemu obrazy kontenerów z zewnętrznych repozytoriów.

Aby zwiększyć bezpieczeństwo klastra usługi AKS, możesz ograniczyć ruch wychodzący. Klaster jest skonfigurowany do ściągania obrazów kontenera z MCR lub ACR podstawowego systemu. Blokować ruch wychodzący w ten sposób należy zdefiniować określone porty i nazw FQDN, aby umożliwić węzłów AKS do poprawnie komunikowania się z usługami zewnętrznymi wymagane. Bez tych autoryzowanych portów i FQDN węzłów AKS nie może komunikować się z serwerem interfejsu API lub zainstalować podstawowe składniki.

Możesz użyć [zapory usługi Azure] [ azure-firewall] lub urządzenie zapory innych firm 3 do zabezpieczenia ruchu wychodzącego i zdefiniować te wymagane porty i adresy.

W usłudze AKS istnieją dwa rodzaje portów i adresów:

* [Wymagane porty i adres dla klastrów AKS](#required-ports-and-addresses-for-aks-clusters) szczegóły minimalnych wymagań na potrzeby ruch wychodzący autoryzowanych.
* [Opcjonalne zalecane adresy i porty w przypadku klastrów usługi AKS](#optional-recommended-addresses-and-ports-for-aks-clusters) nie są wymagane dla wszystkich scenariuszy, ale Integracja z innymi usługami, takie jak Azure Monitor nie będzie działać poprawnie. Przejrzyj listę portów opcjonalne i nazwy FQDN i autoryzowania usługi i składniki używane w klastrze AKS.

> [!NOTE]
> Ograniczanie ruchu wychodzącego działa tylko na nowych klastrów AKS utworzony po włączeniu rejestracji flagi funkcji. W przypadku istniejących klastrów [wykonaj operację uaktualniania klastra] [ aks-upgrade] przy użyciu `az aks upgrade` polecenia, aby ograniczyć ruch wychodzący.

## <a name="required-ports-and-addresses-for-aks-clusters"></a>Wymagane porty i adresy dla klastrów usługi AKS

Następujących portów wychodzących reguł sieci są wymagane dla klastra usługi AKS:

* TCP port *443*
* TCP port *9000*

Następujące nazwy FQDN / wymaganych reguł aplikacji:

| Nazwa FQDN                      | Port      | Użycie      |
|---------------------------|-----------|----------|
| *.azmk8s.io               | HTTPS:443 | Ten adres jest punkt końcowy serwera interfejsu API. |
| aksrepos.azurecr.io       | HTTPS:443 | Ten adres jest wymagane do dostępu do obrazów w usłudze Azure Container Registry (ACR). |
| *.blob.core.windows.net   | HTTPS:443 | Ten adres jest magazynu zaplecza dla obrazów przechowywanych w rejestru Azure container Registry. |
| mcr.microsoft.com         | HTTPS:443 | Ten adres jest wymagane do dostępu do obrazów w rejestrze kontenerów firmy Microsoft (MCR). |
| management.azure.com      | HTTPS:443 | Ten adres jest wymagana do wykonania operacji Kubernetes GET/PUT. |
| login.microsoftonline.com | HTTPS:443 | Ten adres jest wymagany do uwierzytelniania usługi Azure Active Directory. |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>Opcjonalnie zalecane adresy i porty w przypadku klastrów usługi AKS

Następujących portów wychodzących / reguł sieci nie są wymagane w przypadku klastrów usługi AKS działał poprawnie, ale zalecane:

* UDP port *123* do celów synchronizacji czasu NTP
* UDP port *53* dla serwera DNS

Następujące nazwy FQDN / reguł aplikacji są zalecane w przypadku klastrów usługi AKS do poprawnego działania:

| Nazwa FQDN                                    | Port      | Użycie      |
|-----------------------------------------|-----------|----------|
| *.ubuntu.com                            | HTTP:80   | Ten adres służy do pobierania zabezpieczeń wymagane poprawki i aktualizacje węzłów klastra systemu Linux. |
| packages.microsoft.com                  | HTTPS:443 | Ten adres jest repozytorium pakietów firmy Microsoft, które są używane dla pamięci podręcznej *polecenia apt-get* operacji. |
| dc.services.visualstudio.com            | HTTPS:443 | Zalecane w przypadku metryk poprawne i monitorowanie za pomocą usługi Azure Monitor. |
| *.opinsights.azure.com                  | HTTPS:443 | Zalecane w przypadku metryk poprawne i monitorowanie za pomocą usługi Azure Monitor. |
| *.monitoring.azure.com                  | HTTPS:443 | Zalecane w przypadku metryk poprawne i monitorowanie za pomocą usługi Azure Monitor. |
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | Ten adres jest używany do poprawnego działania usługi Azure Policy (obecnie dostępna w wersji zapoznawczej w usłudze AKS). |
| apt.dockerproject.org                   | HTTPS:443 | Ten adres jest używany do instalacji odpowiedni sterownik i działania na węzłach opartych na procesorze GPU. |
| nvidia.github.io                        | HTTPS:443 | Ten adres jest używany do instalacji odpowiedni sterownik i działania na węzłach opartych na procesorze GPU. |

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono jakie porty i adresy umożliwia, jeśli ograniczenia ruchu wychodzącego dla klastra. Można również zdefiniować jak zasobników, samodzielnie może komunikować się i jakie ograniczenia ma w klastrze. Aby uzyskać więcej informacji, zobacz [bezpieczny ruch między zasobników w usłudze AKS za pomocą zasad sieciowych][network-policy].

<!-- LINKS - external -->
[aks-github]: https://github.com/azure/aks/issues]

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
