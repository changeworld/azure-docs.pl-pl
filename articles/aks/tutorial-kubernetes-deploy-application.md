---
title: Samouczek Kubernetes na platformie Azure — wdrażanie aplikacji
description: W tym samouczku usługi Azure Kubernetes Service (AKS) można wdrożyć aplikację z wielokontenerami w klastrze przy użyciu obrazu niestandardowego przechowywanego w Azure Container Registry.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: cc01b12e493f3e0d3cd63786c27819d4704f97f4
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263879"
---
# <a name="tutorial-run-applications-in-azure-kubernetes-service-aks"></a>Samouczek: uruchamianie aplikacji w usłudze Azure Kubernetes Service (AKS)

Usługa Kubernetes udostępnia rozproszoną platformę dla aplikacji kontenerowych. Tworzenie i wdrażanie własnych aplikacji i usług w klastrze Kubernetes oraz umożliwienie klastrowi zarządzania dostępnością i łącznością. W tym samouczku część czwarta z siedmiu, przykładowa aplikacja jest wdrażana w klastrze Kubernetes. Dowiesz się, jak:

> [!div class="checklist"]
> * Aktualizowanie pliku manifestu Kubernetes
> * Uruchamianie aplikacji w Kubernetes
> * Testowanie aplikacji

W dodatkowych samouczkach ta aplikacja jest skalowana i aktualizowana.

W tym przewodniku szybki start założono podstawową wiedzę na temat koncepcji Kubernetes. Aby uzyskać więcej informacji, zobacz [Kubernetes podstawowe pojęcia dotyczące usługi Azure Kubernetes Service (AKS)][kubernetes-concepts].

## <a name="before-you-begin"></a>Przed rozpoczęciem

W poprzednich samouczkach aplikacja została spakowana do obrazu kontenera, ten obraz został przekazany do Azure Container Registry i został utworzony klaster Kubernetes.

Do ukończenia tego samouczka jest potrzebny wstępnie @no__t utworzony plik manifestu Kubernetes-0. Ten plik został pobrany z kodem źródłowym aplikacji w poprzednim samouczku. Sprawdź, czy repozytorium zostało sklonowane i czy zostały zmienione katalogi w sklonowanym repozytorium. Jeśli nie wykonano tych kroków, a chcesz skorzystać z [samouczka 1 — Tworzenie obrazów kontenerów][aks-tutorial-prepare-app].

Ten samouczek wymaga uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2.0.53 lub nowszej. Uruchom `az --version`, aby znaleźć wersję. Jeśli konieczne jest zainstalowanie lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="update-the-manifest-file"></a>Aktualizowanie pliku manifestu

W tych samouczkach wystąpienie Azure Container Registry (ACR) przechowuje obraz kontenera dla przykładowej aplikacji. Aby wdrożyć aplikację, należy zaktualizować nazwę obrazu w pliku manifestu Kubernetes, aby uwzględnić nazwę serwera logowania ACR.

Pobierz nazwę serwera logowania usługi ACR przy użyciu polecenia [AZ ACR list][az-acr-list] w następujący sposób:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Przykładowy plik manifestu z repozytorium git sklonowany w pierwszym samouczku używa nazwy serwera logowania *firmy Microsoft*. Upewnij się, że jesteś w sklonowanym katalogu *Azure-głosowa-App-Redis* , a następnie otwórz plik manifestu za pomocą edytora tekstów, takiego jak `vi`:

```console
vi azure-vote-all-in-one-redis.yaml
```

Zamień *firmę Microsoft* na nazwę serwera logowania ACR. Nazwa obrazu znajduje się w wierszu 51 pliku manifestu. Poniższy przykład pokazuje domyślną nazwę obrazu:

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Podaj własną nazwę serwera logowania ACR, aby plik manifestu wyglądał jak w poniższym przykładzie:

```yaml
containers:
- name: azure-vote-front
  image: <acrName>.azurecr.io/azure-vote-front:v1
```

Zapisz i zamknij plik. W `vi` użyj `:wq`.

## <a name="deploy-the-application"></a>Wdrażanie aplikacji

Aby wdrożyć aplikację, użyj polecenia [polecenia kubectl Apply][kubectl-apply] . To polecenie analizuje plik manifestu i tworzy zdefiniowane obiekty Kubernetes. Określ przykładowy plik manifestu, jak pokazano w następującym przykładzie:

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

Następujące przykładowe dane wyjściowe pokazują zasoby pomyślnie utworzone w klastrze AKS:

```
$ kubectl apply -f azure-vote-all-in-one-redis.yaml

deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Testowanie aplikacji

Po uruchomieniu aplikacji usługa Kubernetes udostępnia fronton aplikacji w Internecie. Ten proces może potrwać kilka minut.

Aby monitorować postęp, użyj polecenia [polecenia kubectl Get Service][kubectl-get] z argumentem `--watch`.

```console
kubectl get service azure-vote-front --watch
```

Początkowo *zewnętrzny adres IP* dla usługi *Azure-poświęconej głosowaniu* jest pokazywany jako *oczekujący*:

```
azure-vote-front   LoadBalancer   10.0.34.242   <pending>     80:30676/TCP   5s
```

Gdy *zewnętrzny adres IP* zmienia się z *oczekujące* na rzeczywisty publiczny adres IP, użyj `CTRL-C`, aby zatrzymać proces Obserwuj `kubectl`. Następujące przykładowe dane wyjściowe pokazują prawidłowy publiczny adres IP przypisany do usługi:

```
azure-vote-front   LoadBalancer   10.0.34.242   52.179.23.131   80:30676/TCP   67s
```

Aby wyświetlić aplikację w działaniu, Otwórz przeglądarkę internetową na zewnętrzny adres IP usługi:

![Obraz przedstawiający klaster Kubernetes na platformie Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

Jeśli aplikacja nie została załadowana, może to być spowodowane problemem z autoryzacją w rejestrze obrazu. Aby wyświetlić stan kontenerów, użyj polecenia `kubectl get pods`. Jeśli nie można ściągnąć obrazów kontenerów, zobacz [uwierzytelnianie za pomocą Azure Container Registry z usługi Azure Kubernetes](cluster-container-registry-integration.md).

## <a name="next-steps"></a>Następne kroki

W tym samouczku Przykładowa aplikacja do głosowania platformy Azure została wdrożona w klastrze Kubernetes w AKS. Wiesz już, jak:

> [!div class="checklist"]
> * Aktualizowanie plików manifestu Kubernetes
> * Uruchamianie aplikacji w Kubernetes
> * Testowanie aplikacji

Przejdź do następnego samouczka, aby dowiedzieć się, jak skalować aplikację Kubernetes i podstawową infrastrukturę Kubernetes.

> [!div class="nextstepaction"]
> [Skalowanie aplikacji i infrastruktury Kubernetes][aks-tutorial-scale]

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-scale]: ./tutorial-kubernetes-scale.md
[az-acr-list]: /cli/azure/acr
[azure-cli-install]: /cli/azure/install-azure-cli
[kubernetes-concepts]: concepts-clusters-workloads.md
[kubernetes-service]: concepts-network.md#services
