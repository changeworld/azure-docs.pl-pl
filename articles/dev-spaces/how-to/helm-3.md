---
title: Konfigurowanie klastra usługi Azure Dev Spaces do używania helm 3 (wersja zapoznawcza)
services: azure-dev-spaces
ms.date: 02/28/2020
ms.topic: conceptual
description: Dowiedz się, jak skonfigurować klaster przestrzeni deweloperskich do używania helma 3
keywords: Miejsca deweloperów platformy Azure, przestrzenie deweloperów, platforma do dokowania, sieci Kubernetes, platforma Azure, usługa AKS, usługa Azure Kubernetes, kontenery
ms.openlocfilehash: dbccb2618fd5a27805261d60e7891d920e0bc372
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454299"
---
# <a name="configure-your-azure-dev-spaces-cluster-to-use-helm-3-preview"></a>Konfigurowanie klastra usługi Azure Dev Spaces do używania helm 3 (wersja zapoznawcza)

Usługa Azure Dev Spaces domyślnie używa programu Helm 2 do instalowania usług użytkowników w przestrzeniach deweloperskich w klastrze usługi AKS. Można włączyć usługi Azure Dev Spaces do korzystania z Helm 3 zamiast Helm 2 instalowania usług użytkownika w przestrzeniach deweloperskich. Niezależnie od wersji usługi Helm Azure Dev Spaces używa do instalowania usług użytkownika, można nadal używać helm 2 lub 3 klienta do zarządzania własnymi wersjami w tym samym klastrze.

Po włączeniu Helm 3 usługa Azure Dev Spaces zachowuje się inaczej podczas instalowania usług użytkowników w przestrzeniach deweloperskich w następujący sposób:

* Program Tiller nie jest już wdrażany w klastrze w obszarze nazw *azds.*
* Helm przechowuje informacje o wersji w obszarze nazw, w którym usługa jest zainstalowana zamiast obszaru nazw *azds.*
* Informacje o wydaniu programu Helm 3 pozostają w obszarze nazw, w którym usługa jest instalowana po usunięciu kontrolera.
* Możesz bezpośrednio wchodzić w interakcje z dowolną wersją zarządzana przez usługi Azure Dev Spaces w klastrze przy użyciu klienta Helm 3.

W tym przewodniku dowiesz się, jak włączyć Helm 3 for Azure Dev Spaces, aby zainstalować usługi użytkownika w przestrzeniach deweloperskich.

> [!IMPORTANT]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="before-you-begin"></a>Przed rozpoczęciem

### <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free).
* [Zainstalowany interfejs wiersza polecenia platformy Azure][azure-cli].

### <a name="register-the-helm3preview-preview-feature"></a>Rejestrowanie funkcji podglądu Helm3Preview

Aby włączyć usługę Azure Dev Spaces do używania helm 3 do instalowania usług użytkowników w przestrzeniach deweloperskich, najpierw włącz flagę funkcji *Helm3Preview* w ramach subskrypcji za pomocą polecenia *az feature register:*

> [!WARNING]
> Każdy klaster usługi AKS, w który włączysz usługę Azure Dev Spaces za pomocą flagi funkcji *Helm3Preview,* użyje tego środowiska w wersji zapoznawczej. Aby nadal włączać w pełni obsługiwane usługi Azure Dev Spaces w klastrach AKS, nie należy włączać funkcji w wersji zapoznawczej w subskrypcjach produkcyjnych. Użyj oddzielnego testu lub dewelopera subskrypcji platformy Azure do testowania funkcji w wersji zapoznawczej.

```azure-cli
az feature register --namespace Microsoft.DevSpaces --name Helm3Preview
```

Rejestracja zajmuje kilka minut. Sprawdź stan rejestracji za pomocą polecenia *az feature show:*

```azure-cli
az feature show --namespace Microsoft.DevSpaces --name Helm3Preview
```

Gdy *stan* jest *zarejestrowany,* odśwież rejestrację *microsoft.DevSpaces* przy użyciu *rejestru dostawców az:*

```azure-cli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="limitations"></a>Ograniczenia

Następujące ograniczenia mają zastosowanie, gdy ta funkcja znajduje się w wersji zapoznawczej:

* Tej funkcji nie można używać w klastrach AKS z istniejącymi obciążeniami. Należy utworzyć nowy klaster AKS.

## <a name="create-your-cluster"></a>Tworzenie klastra

Utwórz nowy klaster AKS w regionie, który ma tę funkcję podglądu. Poniższe polecenia tworzą grupę zasobów o nazwie *MyResourceGroup* i nowy klaster AKS o nazwie *MyAKS:*

```azure-cli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces"></a>Włączanie pomieszczeń deweloperskich platformy Azure

Użyj polecenia *użyj miejsca-dev,* aby włączyć miejsca dewelopera w klastrze AKS i postępuj zgodnie z instrukcjami. Poniższe polecenie włącza miejsca dewelopera w klastrze *MyAKS* w grupie *MyResourceGroup* i tworzy domyślną przestrzeń dewelopera.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

## <a name="verify-dev-spaces-is-running-helm-3"></a>Sprawdź, czy miejsca deweloperów są uruchomione Helm 3

Sprawdź, czy kultywator nie jest uruchomiony, wymieniając wdrożenia w obszarze nazw *azds:*

```cmd
kubectl get deployment -n azds
```

Upewnij *się, że kultywator-deploy* nie jest uruchomiony w obszarze nazw azds. Przykład:

```console
$ kubectl get deployments -n azds
NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
azds-webhook-deployment   2/2     2            2           39m
traefik                   1/1     1            1           39m
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak usługa Azure Dev Spaces pomaga tworzyć bardziej złożone aplikacje w wielu kontenerach i jak można uprościć tworzenie współpracy, pracując z różnymi wersjami lub gałęziami kodu w różnych przestrzeniach.

> [!div class="nextstepaction"]
> [Tworzenie zespołów w usłudze Azure Dev Spaces][team-quickstart]


[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[team-quickstart]: ../quickstart-team-development.md