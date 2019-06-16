---
title: (PRZESTARZAŁE) Wdrażanie kontenerów przy użyciu narzędzia Helm w usłudze Azure Kubernetes
description: Wdrażanie kontenerów w klastrze Kubernetes w usłudze Azure Container Service za pomocą narzędzia pakietu Narzędzia Helm
services: container-service
author: sauryadas
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/10/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 05edbf40e8cd5f8edbdc8b74b540962b1a25c8de
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60712314"
---
# <a name="deprecated-use-helm-to-deploy-containers-on-a-kubernetes-cluster"></a>(PRZESTARZAŁE) Użyj narzędzia Helm do wdrażania kontenerów w klastrze Kubernetes

> [!TIP]
> Aby uzyskać zaktualizowaną wersję tego artykułu, który korzysta z usługi Azure Kubernetes Service, zobacz [instalowanie aplikacji za pomocą narzędzia Helm w usłudze Azure Kubernetes Service (AKS)](../../aks/kubernetes-helm.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

[Polecenie Helm](https://github.com/kubernetes/helm/) to narzędzie open source pakietu, które pomaga zainstalować i zarządzanie cyklem życia aplikacji platformy Kubernetes. Podobnie jak menedżerów pakietów systemu Linux, takie jak Apt-get i Yum, Helm służy do zarządzania wykresów Kubernetes, które są pakietami wstępnie skonfigurowane zasoby platformy Kubernetes. W tym artykule pokazano, jak pracować z narzędzia Helm w klastrze Kubernetes wdrożonych w usłudze Azure Container Service.

Polecenie Helm ma dwa składniki: 
* **Interfejsu wiersza polecenia narzędzia Helm** jest klient, który jest uruchamiany na komputerze, lokalnie lub w chmurze  

* **Tiller** to serwer, który działa w klastrze Kubernetes i zarządzanie cyklem życia aplikacji w usłudze Kubernetes 
 
## <a name="prerequisites"></a>Wymagania wstępne

* [Tworzenie klastra Kubernetes](container-service-kubernetes-walkthrough.md) w usłudze Azure Container Service

* [Instalowanie i konfigurowanie `kubectl` ](../container-service-connect.md) na komputerze lokalnym

* [Zainstaluj narzędzie Helm](https://github.com/kubernetes/helm/blob/master/docs/install.md) na komputerze lokalnym

## <a name="helm-basics"></a>Podstawy narzędzia Helm 

Aby wyświetlić informacje o klastrze Kubernetes są Tiller instalowania i wdrażania aplikacji, wpisz następujące polecenie:

```bash
kubectl cluster-info 
```
![Narzędzie kubectl w klastrze — informacje](./media/container-service-kubernetes-helm/clusterinfo.png)
 
Po zainstalowaniu narzędzia Helm, należy zainstalować Tiller w klastrze usługi Kubernetes, wpisując następujące polecenie:

```bash
helm init --upgrade
```
Po pomyślnym zakończeniu, zostaną wyświetlone dane wyjściowe podobne do następujących:

![Instalacja tiller](./media/container-service-kubernetes-helm/tiller-install.png)
 
 
 
 
Aby wyświetlić wszystkie dostępne wykresów rozwiązania Helm w repozytorium, wpisz następujące polecenie:

```bash 
helm search 
```

Zostaną wyświetlone dane wyjściowe podobne do następujących:

![Polecenie Helm search](./media/container-service-kubernetes-helm/helm-search.png)
 
Aby zaktualizować wykresy, aby uzyskać najnowsze wersje, wpisz:

```bash 
helm repo update 
```
## <a name="deploy-an-nginx-ingress-controller-chart"></a>Wdrażanie organizacyjnego kontrolera danych przychodzących serwera Nginx 
 
Aby wdrożyć organizacyjnego kontrolera danych przychodzących Nginx, wpisz jedno polecenie:

```bash
helm install stable/nginx-ingress 
```
![Wdrażanie kontrolera danych przychodzących](./media/container-service-kubernetes-helm/nginx-ingress.png)

Jeśli wpiszesz `kubectl get svc` Aby wyświetlić wszystkie usługi, które są uruchomione w klastrze, zobaczysz, że adres IP jest przypisywany do kontrolera danych przychodzących. (Przydziału w trakcie, zostanie wyświetlony `<pending>`. Może potrwać kilka minut.) 

Po adres IP przypisany adres, przejdź do wartość zewnętrznego adresu IP, aby wyświetlić wewnętrznej bazy danych serwera Nginx uruchomiony. 
 
![Adres IP ruchu przychodzącego](./media/container-service-kubernetes-helm/ingress-ip-address.png)


Aby wyświetlić listę wykresy zainstalowany w klastrze, wpisz:

```bash
helm list 
```

Można skrócić polecenie, aby `helm ls`.
 
 
 
 
## <a name="deploy-a-mariadb-chart-and-client"></a>Wdrażanie wykresów MariaDB i klienta

Teraz można wdrożyć, wykres MariaDB i MariaDB klienta do łączenia z bazą danych.

Aby wdrożyć wykresu MariaDB, wpisz następujące polecenie:

```bash
helm install --name v1 stable/mariadb
```

gdzie `--name` to znacznik używane dla wersji.

> [!TIP]
> Jeśli wdrożenie nie powiedzie się, uruchom `helm repo update` , a następnie spróbuj ponownie.
>
 
 
Aby wyświetlić wszystkie wykresy, wdrożonych w klastrze, wpisz:

```bash 
helm list
```
 
Aby wyświetlić wszystkie wdrożenia, uruchomiony w klastrze, wpisz:

```bash
kubectl get deployments 
``` 
 
 
Na koniec aby uruchomić zasobnik dostęp klienta do, wpisz:

```bash
kubectl run v1-mariadb-client --rm --tty -i --image bitnami/mariadb --command -- bash  
``` 
 
 
Aby nawiązać połączenie klienta, wpisz następujące polecenie, zastępując `v1-mariadb` o nazwie wdrożenia:

```bash
sudo mysql –h v1-mariadb
```
 
 
Można teraz używać standardowych poleceń SQL do tworzenia baz danych, tabelach itp. Na przykład `Create DATABASE testdb1;` tworzy pustą bazę danych. 
 
 
 
## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać więcej informacji o zarządzaniu wykresów Kubernetes, zobacz [Helm dokumentacji](https://github.com/kubernetes/helm/blob/master/docs/index.md). 

