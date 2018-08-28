---
title: Samouczek dotyczący usługi Kubernetes na platformie Azure — wdrażanie aplikacji
description: W tym samouczku dotyczącym usługi Azure Kubernetes Service (AKS) wdrożysz aplikację z wieloma kontenerami w klastrze przy użyciu obrazu niestandardowego przechowywanego w usłudze Azure Container Registry.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 08/14/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: bf817f553250ead449ec0d5db3d33acc2eff23f3
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2018
ms.locfileid: "41919439"
---
# <a name="tutorial-run-applications-in-azure-kubernetes-service-aks"></a>Samouczek: uruchamianie aplikacji w usłudze Azure Kubernetes Service (AKS)

Usługa Kubernetes zapewnia rozproszoną platformę dla konteneryzowanych aplikacji. Możesz kompilować i wdrażać własne aplikacje oraz usługi w klastrze Kubernetes, który zarządza dostępnością i łącznością. W tym samouczku (część czwarta z siedmiu) przykładowa aplikacja jest wdrażana w klastrze Kubernetes. Omawiane kwestie:

> [!div class="checklist"]
> * Aktualizowanie plików manifestu usługi Kubernetes
> * Uruchamianie aplikacji w usłudze Kubernetes
> * Testowanie aplikacji

W kolejnych samouczkach ta aplikacja jest skalowana w poziomie i aktualizowana.

Na potrzeby tego samouczka założono, że masz podstawową wiedzę na temat pojęć związanych z rozwiązaniem Kubernetes. Aby uzyskać szczegółowe informacje na jego temat, zapoznaj się z [dokumentacją rozwiązania Kubernetes][kubernetes-documentation].

## <a name="before-you-begin"></a>Przed rozpoczęciem

W poprzednich samouczkach aplikacja została spakowana w postaci obrazu kontenera, obraz został przekazany do usługi Azure Container Registry i utworzono klaster usługi Kubernetes.

Do ukończenia tego samouczka potrzebujesz wstępnie utworzonego pliku manifestu usługi Kubernetes `azure-vote-all-in-one-redis.yaml`. Ten plik został pobrany z kodu źródłowego aplikacji w poprzednim samouczku. Sprawdź, czy został utworzony klon repozytorium oraz czy katalogi zostały zmienione na sklonowane repozytorium. Jeśli nie wykonano tych kroków, a chcesz kontynuować pracę, wróć do części [Samouczek 1 — tworzenie obrazów kontenera][aks-tutorial-prepare-app].

Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.44 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="update-the-manifest-file"></a>Aktualizowanie pliku manifestu

W tych samouczkach w wystąpieniu usługi Azure Container Registry (ACR) przechowywany jest obraz kontenera na potrzeby przykładowej aplikacji. W celu wdrożenia aplikacji należy zaktualizować nazwę obrazu w pliku manifestu usługi Kubernetes w taki sposób, aby uwzględniała nazwę serwera logowania usługi ACR.

Pobierz nazwę serwera logowania usługi ACR przy użyciu polecenia [az acr list][az-acr-list] w następujący sposób:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Przykładowy plik manifestu z repozytorium git sklonowany w pierwszym samouczku korzysta z nazwy serwera logowania *microsoft*. Otwórz ten plik manifestu w edytorze tekstu, takim jak `vi`:

```console
vi azure-vote-all-in-one-redis.yaml
```

Zastąp wartość *microsoft* nazwą serwera logowania usługi ACR. Nazwę obrazu można znaleźć w wierszu 47 pliku manifestu. W poniższym przykładzie przedstawiono domyślną nazwę obrazu:

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Podaj własną nazwę serwera logowania usługi ACR, aby plik manifestu wyglądał podobnie jak w poniższym przykładzie:

```yaml
containers:
- name: azure-vote-front
  image: <acrName>.azurecr.io/azure-vote-front:v1
```

Zapisz i zamknij plik.

## <a name="deploy-the-application"></a>Wdrażanie aplikacji

Aby wdrożyć aplikację, użyj polecenia [kubectl apply][kubectl-apply]. To polecenie analizuje plik manifestu i tworzy zdefiniowane obiekty usługi Kubernetes. Określ przykładowy plik manifestu, jak pokazano w poniższym przykładzie:

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

Obiekty usługi Kubernetes są tworzone w ramach klastra, jak pokazano w poniższym przykładzie:

```
$ kubectl apply -f azure-vote-all-in-one-redis.yaml

deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Testowanie aplikacji

Utworzenie [usługi Kubernetes][kubernetes-service] powoduje uwidocznienie aplikacji w Internecie. Ten proces może potrwać kilka minut. Aby monitorować postęp, użyj polecenia [kubectl get-service][kubectl-get] z argumentem `--watch`:

```console
kubectl get service azure-vote-front --watch
```

Początkowo adres *EXTERNAL-IP* dla usługi *azure-vote-front* jest wyświetlany jako *oczekujący*, jak pokazano w poniższym przykładzie:

```
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
```

Gdy dla adresu *EXTERNAL-IP* wartość *oczekujący* zmieni się na rzeczywisty publiczny adres IP, naciśnij klawisze `CTRL-C`, aby zatrzymać proces śledzenia narzędzia kubectl. W poniższym przykładzie pokazano, że publiczny adres IP jest teraz przypisany:

```
azure-vote-front   10.0.34.242   52.179.23.131   80:30676/TCP   2m
```

Aby wyświetlić działającą aplikację, otwórz zewnętrzny adres IP w przeglądarce internetowej.

![Obraz przedstawiający klaster Kubernetes na platformie Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

Jeśli aplikacja się nie załadowała, może być to spowodowane problemem z autoryzacją rejestru obrazów. Aby wyświetlić stan kontenerów, użyj polecenia `kubectl get pods`. Jeśli nie można ściągnąć obrazów kontenerów, zapoznaj się z sekcją dotyczącą [zezwalania na dostęp do rejestru kontenerów za pomocą wpisu tajnego usługi Kubernetes](https://docs.microsoft.com/azure/container-registry/container-registry-auth-aks#access-with-kubernetes-secret).

## <a name="next-steps"></a>Następne kroki

W tym samouczku aplikacja do głosowania na platformie Azure została wdrożona w klastrze Kubernetes w usłudze AKS. W tym samouczku omówiono:

> [!div class="checklist"]
> * Aktualizowanie plików manifestu usługi Kubernetes
> * Uruchamianie aplikacji w usłudze Kubernetes
> * Testowanie aplikacji

Przejdź do następnego samouczka, aby uzyskać informacje na temat sposobu skalowania aplikacji Kubernetes i bazowej infrastruktury usługi Kubernetes.

> [!div class="nextstepaction"]
> [Skalowanie aplikacji i infrastruktury rozwiązania Kubernetes][aks-tutorial-scale]

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-documentation]: https://kubernetes.io/docs/home/
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-scale]: ./tutorial-kubernetes-scale.md
[az-acr-list]: /cli/azure/acr#list
[azure-cli-install]: /cli/azure/install-azure-cli
