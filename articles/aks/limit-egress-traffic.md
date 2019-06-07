---
title: Ogranicz ruch wychodzący w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jakie porty i adresy są wymagane do kontroli ruchu wychodzącego ruchu w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 06/06/2019
ms.author: iainfou
ms.openlocfilehash: 43ba7593336372bbbd7a3a4bb9821665a42bbf29
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2019
ms.locfileid: "66752177"
---
# <a name="preview---limit-egress-traffic-for-cluster-nodes-and-control-access-to-required-ports-and-services-in-azure-kubernetes-service-aks"></a>(Wersja zapoznawcza) — ruch wychodzący Limit dla węzłów klastra i kontrola dostępu do wymaganych portów i usług w usłudze Azure Kubernetes Service (AKS)

Domyślnie klastry usługi AKS mają nieograniczony dostęp do Internetu wychodzących (dane wychodzące). Ten poziom dostępu do sieci umożliwia węzłów i usługi uruchomione na dostęp do zasobów zewnętrznych zgodnie z potrzebami. Jeśli chcesz ograniczyć ruch wychodzący ograniczoną liczbę portów i adresów muszą być dostępne do obsługi zadań konserwacji w dobrej kondycji klastra. Klaster jest następnie skonfigurowany do użycia tylko podstawowy system obrazy kontenera z rejestru kontenerów firmy Microsoft (MCR) lub Azure Container Registry (ACR), nie zewnętrzne z publicznych repozytoriów. Należy skonfigurować preferowanych reguły zapory i zabezpieczeń, aby zezwolić na te wymagane porty i adresy.

Ten artykuł szczegółowo opisuje jakie porty sieciowe i w pełni kwalifikowanych nazw domen (FQDN) są wymagane i opcjonalne, jeśli można ograniczyć ruch wychodzący w klastrze AKS.  Ta funkcja jest obecnie dostępna w wersji zapoznawczej.

> [!IMPORTANT]
> Funkcje w wersji zapoznawczej usługi AKS są samoobsługi, uczestnictwo. Są one udostępniane do zbierania opinii i błędy z naszej społeczności. W wersji zapoznawczej te funkcje nie są przeznaczone do użytku produkcyjnego. Funkcje w wersji zapoznawczej objęte "starań" pomocy technicznej. Pomoc od zespołów pomocy technicznej usługi AKS jest dostępna w godzinach pracy i Pacyfik, część strefy czasowej (PST) tylko. Aby uzyskać więcej informacji zobacz następujące artykuły pomocy technicznej:
>
> * [Zasady pomocy technicznej usługi AKS][aks-support-policies]
> * [Pomoc techniczna platformy Azure — często zadawane pytania][aks-faq]

## <a name="before-you-begin"></a>Przed rozpoczęciem

Potrzebujesz wiersza polecenia platformy Azure w wersji 2.0.66 lub później zainstalowane i skonfigurowane. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

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

Możesz użyć [zapory usługi Azure] [ azure-firewall] lub urządzenie zapory innych firm 3 do zabezpieczenia ruchu wychodzącego i zdefiniować te wymagane porty i adresy. AKS nie automatycznie tworzy te reguły. Następujące porty i adresy są dla odwołania i Utwórz odpowiednie zasady w zaporze sieciowej.

W usłudze AKS istnieją dwa rodzaje portów i adresów:

* [Wymagane porty i adres dla klastrów AKS](#required-ports-and-addresses-for-aks-clusters) szczegóły minimalnych wymagań na potrzeby ruch wychodzący autoryzowanych.
* [Opcjonalne zalecane adresy i porty w przypadku klastrów usługi AKS](#optional-recommended-addresses-and-ports-for-aks-clusters) nie są wymagane dla wszystkich scenariuszy, ale Integracja z innymi usługami, takie jak Azure Monitor nie będzie działać poprawnie. Przejrzyj listę portów opcjonalne i nazwy FQDN i autoryzowania usługi i składniki używane w klastrze AKS.

> [!NOTE]
> Ograniczanie ruchu wychodzącego działa tylko na nowych klastrów AKS utworzony po włączeniu rejestracji flagi funkcji. W przypadku istniejących klastrów [wykonaj operację uaktualniania klastra] [ aks-upgrade] przy użyciu `az aks upgrade` polecenia, aby ograniczyć ruch wychodzący.

## <a name="required-ports-and-addresses-for-aks-clusters"></a>Wymagane porty i adresy dla klastrów usługi AKS

Następujących portów wychodzących reguł sieci są wymagane dla klastra usługi AKS:

* TCP port *443*
* TCP port *9000* i TCP port *22* dla tunelu zasobnik frontonu do komunikowania się z zakończeniem tunelu na serwerze interfejsu API.
    * Aby uzyskać bardziej szczegółowe informacje, zobacz * *.hcp.\< Lokalizacja\>. azmk8s.io* i * *. tun.\< Lokalizacja\>. azmk8s.io* adresy w tabeli poniżej.

Następujące nazwy FQDN / wymaganych reguł aplikacji:

| NAZWA FQDN                       | Port      | Użycie      |
|----------------------------|-----------|----------|
| *.hcp. \<lokalizacji\>. azmk8s.io | HTTPS:443, TCP:22, TCP:9000 | Ten adres jest punkt końcowy serwera interfejsu API. Zastąp *\<lokalizacji\>* z regionem, w którym jest wdrażany klaster AKS. |
| *.tun.\<location\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000 | Ten adres jest punkt końcowy serwera interfejsu API. Zastąp *\<lokalizacji\>* z regionem, w którym jest wdrażany klaster AKS. |
| aksrepos.azurecr.io        | HTTPS:443 | Ten adres jest wymagane do dostępu do obrazów w usłudze Azure Container Registry (ACR). |
| *.blob.core.windows.net    | HTTPS:443 | Ten adres jest magazynu zaplecza dla obrazów przechowywanych w rejestru Azure container Registry. |
| mcr.microsoft.com          | HTTPS:443 | Ten adres jest wymagane do dostępu do obrazów w rejestrze kontenerów firmy Microsoft (MCR). |
| *.cdn.mscr.io              | HTTPS:443 | Ten adres jest wymagany dla MCR magazyn usługi Azure content delivery network (CDN). |
| management.azure.com       | HTTPS:443 | Ten adres jest wymagana do wykonania operacji Kubernetes GET/PUT. |
| login.microsoftonline.com  | HTTPS:443 | Ten adres jest wymagany do uwierzytelniania usługi Azure Active Directory. |
| api.snapcraft.io           | HTTPS:443, HTTP:80 | Ten adres jest wymagany, aby zainstalować pakiety przystawki w węzłach systemu Linux. |
| ntp.ubuntu.com             | UDP:123   | Ten adres jest wymagany do synchronizacji czasu NTP w węzłach systemu Linux. |
| *.docker.io                | HTTPS:443 | Ten adres jest wymagany do ściągania obrazów kontenera wymagane do przodu tunelu. |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>Opcjonalnie zalecane adresy i porty w przypadku klastrów usługi AKS

* UDP port *53* dla serwera DNS

Następujące nazwy FQDN / reguł aplikacji są zalecane w przypadku klastrów usługi AKS do poprawnego działania:

| NAZWA FQDN                                    | Port      | Użycie      |
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
