---
title: Wdrażanie klastra kontenera platformy Docker — interfejs wiersza polecenia platformy Azure
description: Wdrażanie rozwiązania Kubernetes, DC/OS lub Docker Swarm w usłudze Azure Container Service przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 2.0
services: container-service
author: sauryadas
manager: jeconnoc
ms.service: container-service
ms.topic: quickstart
ms.date: 03/01/2017
ms.author: saudas
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: 037834fa94a4ef6ecf6ab87055ed1369a3c8f1fb
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="deploy-a-docker-container-hosting-solution-using-the-azure-cli-20"></a>Wdrażanie rozwiązania hostingu kontenera platformy Docker przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 2.0

Za pomocą poleceń `az acs` interfejsu wiersza polecenia platformy Azure w wersji 2.0 możesz tworzyć klastry w usłudze Azure Container Service i zarządzać nimi. Wdrożenie klastra usługi Azure Container Service jest również możliwe za pomocą witryny [Azure Portal](container-service-deployment.md) lub interfejsów API usługi Azure Container Service.

Aby uzyskać pomoc dotyczącą poleceń `az acs`, przekaż parametr `-h` do dowolnego polecenia. Na przykład: `az acs create -h`.



## <a name="prerequisites"></a>Wymagania wstępne
Aby utworzyć klaster usługi Azure Container Service przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 2.0, musisz mieć:
* Konto platformy Azure ([skorzystaj z bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/))
* Zainstalowany i skonfigurowany [interfejs wiersza polecenia platformy Azure w wersji 2.0](/cli/azure/install-az-cli2)

## <a name="get-started"></a>Rozpoczęcie pracy 
### <a name="log-in-to-your-account"></a>Logowanie się na swoim koncie
```azurecli
az login 
```

Postępuj zgodnie z monitami, aby zalogować się interaktywnie. Inne metody logowania omówiono w temacie [Get started with Azure CLI 2.0](/cli/azure/get-started-with-az-cli2) (Wprowadzenie do interfejsu wiersza polecenia platformy Azure w wersji 2.0).

### <a name="set-your-azure-subscription"></a>Ustawianie swojej subskrypcji platformy Azure

Jeśli masz więcej niż jedną subskrypcję platformy Azure, ustaw subskrypcję domyślną. Na przykład:

```
az account set --subscription "f66xxxxx-xxxx-xxxx-xxx-zgxxxx33cha5"
```


### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Zaleca się utworzenie grupy zasobów dla każdego klastra. Określ region świadczenia usługi Azure, w którym jest [dostępna](https://azure.microsoft.com/regions/services/) usługa Azure Container Service. Na przykład:

```azurecli
az group create -n acsrg1 -l "westus"
```
Dane wyjściowe będą podobne do następujących:

![Tworzenie grupy zasobów](./media/container-service-create-acs-cluster-cli/rg-create.png)


## <a name="create-an-azure-container-service-cluster"></a>Tworzenie klastra usługi Azure Container Service

Aby utworzyć klaster, użyj polecenia `az acs create`.
Nazwa klastra i nazwa grupy zasobów utworzonej w poprzednim kroku to obowiązkowe parametry. 

Dla pozostałych danych wejściowych są ustawiane wartości domyślne (zobacz poniższy ekran), o ile nie zostaną one zastąpione przy użyciu odpowiadających im przełączników. Na przykład koordynator jest domyślnie ustawiany na platformę DC/OS. Prefiks nazwy DNS jest tworzony na podstawie nazwy klastra, jeśli nie zostanie określony.

![Sposób użycia polecenia az acs create](./media/container-service-create-acs-cluster-cli/create-help.png)


### <a name="quick-acs-create-using-defaults"></a>Szybkie wykonanie polecenia `acs create` z użyciem ustawień domyślnych
Jeśli masz plik klucza publicznego SSH RSA `id_rsa.pub` w lokalizacji domyślnej (lub został on utworzony dla systemu [OS X i Linux](../../virtual-machines/linux/mac-create-ssh-keys.md) lub [Windows](../../virtual-machines/linux/ssh-from-windows.md)), użyj polecenia podobnego do poniższego:

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789
```
Jeśli nie masz klucza publicznego SSH, użyj drugiego polecenia. To polecenie z przełącznikiem `--generate-ssh-keys` utworzy go dla Ciebie.

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789 --generate-ssh-keys
```

Po wprowadzeniu polecenia poczekaj około 10 minut na utworzenie klastra. Dane wyjściowe polecenia obejmują w pełni kwalifikowane nazwy domen (FQDN, fully qualified domain name) węzłów głównych i węzłów agentów oraz polecenie SSH służące do nawiązywania połączeń z pierwszym węzłem głównym. Poniżej przedstawiono skrócone dane wyjściowe:

![Obraz przedstawiający polecenie acs create](./media/container-service-create-acs-cluster-cli/cluster-create.png)

> [!TIP]
> [Przewodnik po rozwiązaniu Kubernetes](../kubernetes/container-service-kubernetes-walkthrough.md) przedstawia sposób używania polecenia `az acs create` z wartościami domyślnymi w celu utworzenia klastra Kubernetes.
>

## <a name="manage-acs-clusters"></a>Zarządzanie klastrami usługi ACS

Do zarządzania klastrem służą dodatkowe polecenia `az acs`. Oto kilka przykładów.

### <a name="list-clusters-under-a-subscription"></a>Wyświetlanie listy klastrów w ramach subskrypcji

```azurecli
az acs list --output table
```

### <a name="list-clusters-in-a-resource-group"></a>Wyświetlanie listy klastrów w grupie zasobów

```azurecli
az acs list -g acsrg1 --output table
```

![Polecenie acs list](./media/container-service-create-acs-cluster-cli/acs-list.png)


### <a name="display-details-of-a-container-service-cluster"></a>Wyświetlanie szczegółów klastra usługi kontenera

```azurecli
az acs show -g acsrg1 -n acs-cluster --output list
```

![Polecenie acs show](./media/container-service-create-acs-cluster-cli/acs-show.png)


### <a name="scale-the-cluster"></a>Skalowanie klastra
Dozwolone jest skalowanie węzłów agentów zarówno na zewnątrz, jak i do wewnątrz. Parametr `new-agent-count` oznacza nową liczbę agentów w klastrze usługi ACS.

```azurecli
az acs scale -g acsrg1 -n acs-cluster --new-agent-count 4
```

![Polecenie acs scale](./media/container-service-create-acs-cluster-cli/acs-scale.png)

## <a name="delete-a-container-service-cluster"></a>Usuwanie klastra usługi kontenera
```azurecli
az acs delete -g acsrg1 -n acs-cluster 
```
To polecenie nie usuwa wszystkich zasobów (sieciowych i magazynowych) utworzonych podczas tworzenia usługi kontenera. Aby łatwo usunąć wszystkie zasoby, zaleca się wdrażanie każdego klastra w oddzielnej grupie zasobów. Następnie można usunąć grupę zasobów, gdy klaster nie jest już wymagany.

## <a name="next-steps"></a>Następne kroki
Teraz, gdy masz działający klaster, możesz zapoznać się z tymi dokumentami, aby uzyskać szczegółowe informacje na temat połączeń i zarządzania:

* [Łączenie z klastrem usługi Azure Container Service](../container-service-connect.md)
* [Współpraca z usługą Azure Container Service i rozwiązaniem DC/OS](container-service-mesos-marathon-rest.md)
* [Współpraca z usługą Azure Container Service i rozwiązaniem Docker Swarm](container-service-docker-swarm.md)
* [Współpraca z usługą Azure Container Service i rozwiązaniem Kubernetes](../kubernetes/container-service-kubernetes-walkthrough.md)
