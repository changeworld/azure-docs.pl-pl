---
title: Uwierzytelnij się w klastrze Kubernetes
description: Dowiedz się, jak zapewnić klastrowi usługi Kubernetes dostęp do obrazów w rejestrze kontenerów platformy Azure, tworząc klucz tajny ściągania przy użyciu jednostki usługi
ms.topic: article
author: karolz-ms
ms.author: karolz
ms.reviewer: danlep
ms.date: 02/10/2020
ms.openlocfilehash: 0608ca0e0e53acf2f19910a7f1107dacf67d4e61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77154896"
---
# <a name="pull-images-from-an-azure-container-registry-to-a-kubernetes-cluster"></a>Ściąganie obrazów z rejestru kontenerów platformy Azure do klastra usługi Kubernetes

Rejestru kontenerów platformy Azure można używać jako źródła obrazów kontenerów z dowolnym klastrem Kubernetes, w tym "lokalnymi" klastrami Kubernetes, takimi jak [minikube](https://minikube.sigs.k8s.io/) i [rodzaj.](https://kind.sigs.k8s.io/) W tym artykule pokazano, jak utworzyć klucz tajny ściągania usługi Kubernetes na podstawie jednostki usługi Azure Active Directory. Następnie użyj klucza tajnego do ściągania obrazów z rejestru kontenerów platformy Azure we wdrożeniu usługi Kubernetes.

> [!TIP]
> Jeśli używasz zarządzanej [usługi Azure Kubernetes](../aks/intro-kubernetes.md), można również [zintegrować klaster](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json) z docelowym rejestrem kontenerów platformy Azure dla ściągania obrazów. 

W tym artykule przyjęto założenie, że utworzono już prywatny rejestr kontenerów platformy Azure. Należy również mieć klaster Kubernetes uruchomiony `kubectl` i dostępny za pośrednictwem narzędzia wiersza polecenia.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

Jeśli nie zapiszesz lub nie zapamiętasz hasła głównego usługi, możesz je zresetować za pomocą polecenia [resetowania poświadczeń az ad sp:][az-ad-sp-credential-reset]

```azurecli
az ad sp credential reset  --name http://<service-principal-name> --query password --output tsv
```

To polecenie zwraca nowe, prawidłowe hasło dla jednostki usługi.

## <a name="create-an-image-pull-secret"></a>Tworzenie klucza tajnego ściągania obrazu

Kubernetes używa *klucza tajnego ściągania obrazu* do przechowywania informacji potrzebnych do uwierzytelnienia w rejestrze. Aby utworzyć klucz tajny ściągania dla rejestru kontenerów platformy Azure, należy podać identyfikator jednostki usługi, hasło i adres URL rejestru. 

Utwórz klucz tajny ściągania obrazu za pomocą następującego `kubectl` polecenia:

```console
kubectl create secret docker-registry <secret-name> \
  --namespace <namespace> \
  --docker-server=https://<container-registry-name>.azurecr.io \
  --docker-username=<service-principal-ID> \
  --docker-password=<service-principal-password>
```
gdzie:

| Wartość | Opis |
| :--- | :--- |
| `secret-name` | Nazwa obrazu ciągnąć tajne, na *przykład, acr-secret* |
| `namespace` | Obszar nazw Kubernetes, aby umieścić klucz tajny w <br/> Jest potrzebny tylko wtedy, gdy klucz tajny ma być umieszczany w przestrzeni nazw innej niż domyślna przestrzeń nazw |
| `container-registry-name` | Nazwa rejestru kontenerów platformy Azure |
| `service-principal-ID` | Identyfikator jednostki usługi, który będzie używany przez firmę Kubernetes do uzyskiwania dostępu do rejestru |
| `service-principal-password` | Hasło jednostki usługi |

## <a name="use-the-image-pull-secret"></a>Użyj tajnego ściągania obrazu

Po utworzeniu klucza tajnego ściągania obrazu można go użyć do tworzenia zasobników i wdrożeń kubernetes. Podaj nazwę klucza tajnego `imagePullSecrets` w pliku wdrożenia. Przykład:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: your-awesome-app-pod
  namespace: awesomeapps
spec:
  containers:
    - name: main-app-container
      image: your-awesome-app:v1
      imagePullPolicy: IfNotPresent
  imagePullSecrets:
    - name: acr-secret
```

W poprzednim przykładzie `your-awesome-app:v1` jest nazwa obrazu do wyciągnięcia z rejestru `acr-secret` kontenerów platformy Azure i jest nazwą klucza ściągnięcia utworzonego w celu uzyskania dostępu do rejestru. Podczas wdrażania zasobnika, Kubernetes automatycznie pobiera obraz z rejestru, jeśli nie jest jeszcze obecny w klastrze.


## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat pracy z podmiotami usługi i rejestru kontenerów platformy Azure, zobacz [Uwierzytelnianie rejestru kontenerów platformy Azure z podmiotami usługi](container-registry-auth-service-principal.md)
* Dowiedz się więcej o wpisach tajnych ściągnięcia obrazów w [dokumentacji kubernetes](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod)


<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset