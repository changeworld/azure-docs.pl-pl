---
title: Korzystanie z usługi Azure AD w usłudze Azure Kubernetes
description: Dowiedz się, jak korzystać z usługi Azure AD w usłudze Azure Kubernetes Service (AKS)
services: container-service
manager: gwallace
ms.topic: article
ms.date: 03/24/2020
ms.openlocfilehash: 430df504c677b005f5ff5e7fdd9346aed3e168af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294451"
---
# <a name="integrate-azure-ad-in-azure-kubernetes-service-preview"></a>Integracja usługi Azure AD w usłudze Azure Kubernetes (wersja zapoznawcza)

> [!Note]
> Nowe środowisko usługi AKS w wersji 2 nie ma wpływu na istniejące klastry usługi AKS w wersji 1 z integracją usługi AD.

Integracja usługi Azure AD z usługą AKS w wersji 2 ma na celu uproszczenie integracji usługi Azure AD z usługą AKS w wersji 1, w której użytkownicy byli zobowiązani do utworzenia aplikacji klienckiej, aplikacji serwera i wymagali dzierżawy usługi Azure AD, aby udzielić uprawnień odczytu katalogu. W nowej wersji dostawca zasobów AKS zarządza aplikacjami klienta i serwera za Ciebie.

## <a name="limitations"></a>Ograniczenia

* Obecnie nie można uaktualnić istniejącego klastra AKS w wersji 1 z włączoną usługą Azure AD do środowiska w wersji 2.

> [!IMPORTANT]
> Funkcje w wersji zapoznawczej usługi AKS są dostępne na zasadzie samoobsługowej, opt-in. Wersje zapoznawcza są dostarczane w stanie "tak jak jest" i "w miarę dostępności" i są wyłączone z umów o gwarantowanym poziomie usług i ograniczonej gwarancji. Podglądy AKS są częściowo objęte obsługą klienta na zasadzie najlepszego wysiłku. W związku z tym te funkcje nie są przeznaczone do użytku produkcyjnego. Aby uzyskać więcej informacji, zobacz następujące artykuły pomocy technicznej:
>
> - [Zasady wsparcia AKS](support-policies.md)
> - [Często zadawane pytania dotyczące pomocy technicznej platformy Azure](faq.md)

## <a name="before-you-begin"></a>Przed rozpoczęciem

Muszą być zainstalowane następujące zasoby:

- Interfejsu wiersza polecenia platformy Azure w wersji 2.2.0 lub nowszej
- Rozszerzenie aks-preview 0.4.38
- Kubectl z minimalną wersją [1.18 beta](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#client-binaries)

Aby zainstalować/zaktualizować rozszerzenie aks-preview lub nowsze, użyj następujących poleceń interfejsu wiersza polecenia platformy Azure:

```azurecli
az extension add --name aks-preview
az extension list
```

```azurecli
az extension update --name aks-preview
az extension list
```

Aby zainstalować kubectl, należy użyć następujących
```azurecli
curl -LO "https://storage.googleapis.com/kubernetes-release/release/v1.18.0-beta.2/bin/darwin/amd64/kubectl"
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
kubectl version --client
```

Skorzystaj z [tych instrukcji](https://kubernetes.io/docs/tasks/tools/install-kubectl/) dla innych systemów operacyjnych.

> [!CAUTION]
> Po zarejestrowaniu funkcji w subskrypcji nie można obecnie wyrejestrować tej funkcji. Po włączeniu niektórych funkcji w wersji zapoznawczej, domyślne mogą być używane dla wszystkich klastrów AKS utworzonych później w ramach subskrypcji. Nie włączaj funkcji w wersji zapoznawczej w subskrypcjach produkcyjnych. Zamiast tego należy użyć oddzielnej subskrypcji, aby przetestować funkcje w wersji zapoznawczej i zebrać opinie.

```azurecli-interactive
az feature register --name AAD-V2 --namespace Microsoft.ContainerService
```

Może upłynąć kilka minut, aby stan był pokazywał jako **zarejestrowany**. Stan rejestracji można sprawdzić za pomocą polecenia [az feature list:](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list)

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AAD-V2')].{Name:name,State:properties.state}"
```

Gdy stan jest wyświetlany jako zarejestrowany, `Microsoft.ContainerService` odśwież rejestrację dostawcy zasobów za pomocą polecenia [az provider register:](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register)

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Tworzenie klastra usługi AKS z włączoną usługą Azure AD

Teraz można utworzyć klaster AKS przy użyciu następujących poleceń interfejsu wiersza polecenia.

Najpierw utwórz grupę zasobów platformy Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Następnie utwórz klaster usługi AKS:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad
```
Powyższe polecenie tworzy klaster AKS z trzema węzłami, ale użytkownik, który utworzył klaster, domyślnie nie jest członkiem grupy, która ma dostęp do tego klastra. Ten użytkownik musi utworzyć grupę usługi Azure AD, dodać siebie jako członka grupy, a następnie zaktualizować klaster, jak pokazano poniżej. Postępuj zgodnie z instrukcjami [tutaj](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)

Po utworzeniu grupy i dodaniu siebie (i innych) jako członka, można zaktualizować klaster za pomocą grupy usługi Azure AD za pomocą następującego polecenia

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id1,id2>] [--aad-tenant-id <id>]
```
Alternatywnie, jeśli najpierw utworzysz grupę i dodasz członków, możesz włączyć grupę usługi Azure AD w czasie tworzenia przy użyciu następującego polecenia,

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id1,id2>] [--aad-tenant-id <id>]
```

Pomyślne utworzenie klastra usługi Azure AD w wersji 2 ma następującą sekcję w treści odpowiedzi
```
"Azure ADProfile": {
    "adminGroupObjectIds": null,
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Klaster jest tworzony w ciągu kilku minut.

## <a name="access-an-azure-ad-enabled-cluster"></a>Uzyskiwanie dostępu do klastra obsługującego usługę Azure AD
Aby uzyskać poświadczenia administratora, aby uzyskać dostęp do klastra:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster --admin
```
Teraz użyj polecenia kubectl get nodes, aby wyświetlić węzły w klastrze:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```

Aby uzyskać poświadczenia użytkownika, aby uzyskać dostęp do klastra:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
Postępuj zgodnie z instrukcjami, aby się zalogować.

Otrzymasz: **Musisz być zalogowany na serwerze (Nieautoryzowane)**

Użytkownik powyżej pobiera błąd, ponieważ użytkownik nie jest częścią grupy, która ma dostęp do klastra.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [kontroli dostępu opartej na rolach usługi Azure AD][azure-ad-rbac].

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md