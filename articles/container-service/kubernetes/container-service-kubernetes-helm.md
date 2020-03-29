---
title: (PRZESTARZAŁE) Wdrażanie kontenerów z helmem w usłudze Azure Kubernetes
description: Narzędzie do pakowania Helm służy do wdrażania kontenerów w klastrze usługi Kubernetes w usłudze Azure Container Service
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 04/10/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: a32c9fab3877a693d2df26571b9fae4aa7b4380c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76271086"
---
# <a name="deprecated-use-helm-to-deploy-containers-on-a-kubernetes-cluster"></a>(PRZESTARZAŁE) Wdrażanie kontenerów w klastrze kubernetes za pomocą funkcji Helm

> [!TIP]
> Aby zapoznać się ze zaktualizowaną wersją tego artykułu, która korzysta z usługi Azure Kubernetes, zobacz [Instalowanie aplikacji z helmem w usłudze Azure Kubernetes Service (AKS)](../../aks/kubernetes-helm.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

[Helm](https://github.com/kubernetes/helm/) to narzędzie do pakowania typu open source, które pomaga zainstalować cykl życia aplikacji Kubernetes i zarządzać nimi. Podobnie jak menedżerów pakietów Linuksa, takich jak Apt-get i Yum, Helm służy do zarządzania wykresami Kubernetes, które są pakietami wstępnie skonfigurowanych zasobów Kubernetes. W tym artykule pokazano, jak pracować z helmem w klastrze Kubernetes wdrożonym w usłudze Azure Container Service.

Helm ma dwa składniki: 
* **Helm CLI** to klient, który działa na komputerze lokalnie lub w chmurze  

* **Tiller** to serwer, który działa w klastrze Kubernetes i zarządza cyklem życia aplikacji Kubernetes 
 
## <a name="prerequisites"></a>Wymagania wstępne

* [Tworzenie klastra usługi Kubernetes](container-service-kubernetes-walkthrough.md) w usłudze kontenerów platformy Azure

* [Instalowanie i `kubectl` konfigurowanie](../container-service-connect.md) na komputerze lokalnym

* [Instalowanie programu Helm](https://github.com/kubernetes/helm/blob/master/docs/install.md) na komputerze lokalnym

## <a name="helm-basics"></a>Podstawy steru 

Aby wyświetlić informacje o klastrze Kubernetes, w których instalujesz program Tiller i w których wdrażasz aplikacje, wpisz następujące polecenie:

```bash
kubectl cluster-info 
```
![kubectl cluster-info](./media/container-service-kubernetes-helm/clusterinfo.png)
 
Po zainstalowaniu programu Helm zainstaluj program Tiller w klastrze kubernetes, wpisując następujące polecenie:

```bash
helm init --upgrade
```
Po pomyślnym zakończeniu zobaczysz dane wyjściowe następujące:

![Instalacja kultywatera](./media/container-service-kubernetes-helm/tiller-install.png)
 
 
 
 
Aby wyświetlić wszystkie wykresy Helm dostępne w repozytorium, wpisz następujące polecenie:

```bash 
helm search 
```

Widzisz dane wyjściowe następujące:

![Wyszukiwanie sterów](./media/container-service-kubernetes-helm/helm-search.png)
 
Aby zaktualizować wykresy w celu uzyskania najnowszych wersji, wpisz:

```bash 
helm repo update 
```
## <a name="deploy-an-nginx-ingress-controller-chart"></a>Wdrażanie wykresu kontrolera transferu danych przychodzących Nginx 
 
Aby wdrożyć wykres kontrolera transferu danych przychodzących Nginx, wpisz pojedyncze polecenie:

```bash
helm install stable/nginx-ingress 
```
![Wdrażanie kontrolera transferu danych przychodzących](./media/container-service-kubernetes-helm/nginx-ingress.png)

Po wpisaniu, `kubectl get svc` aby wyświetlić wszystkie usługi, które są uruchomione w klastrze, widać, że adres IP jest przypisany do kontrolera transferu danych przychodzących. (Gdy przypisanie jest w `<pending>`toku, zobaczysz . Zajmuje to kilka minut). 

Po przypisaniu adresu IP przejdź do wartości zewnętrznego adresu IP, aby wyświetlić uruchomione zaplecze Nginx. 
 
![Adres IP usługi Ingress](./media/container-service-kubernetes-helm/ingress-ip-address.png)


Aby wyświetlić listę wykresów zainstalowanych w klastrze, wpisz:

```bash
helm list 
```

Polecenie można skrócić do `helm ls`.
 
 
 
 
## <a name="deploy-a-mariadb-chart-and-client"></a>Wdrażanie wykresu i klienta MariaDB

Teraz wdrożyć wykres MariaDB i klienta MariaDB, aby połączyć się z bazą danych.

Aby wdrożyć wykres MariaDB, wpisz następujące polecenie:

```bash
helm install --name v1 stable/mariadb
```

gdzie `--name` jest znacznik używany do wydań.

> [!TIP]
> Jeśli wdrożenie nie `helm repo update` powiedzie się, uruchom i spróbuj ponownie.
>
 
 
Aby wyświetlić wszystkie wykresy wdrożone w klastrze, wpisz:

```bash 
helm list
```
 
Aby wyświetlić wszystkie wdrożenia uruchomione w klastrze, wpisz:

```bash
kubectl get deployments 
``` 
 
 
Na koniec, aby uruchomić zasobnik, aby uzyskać dostęp do klienta, wpisz:

```bash
kubectl run v1-mariadb-client --rm --tty -i --image bitnami/mariadb --command -- bash  
``` 
 
 
Aby połączyć się z klientem, `v1-mariadb` wpisz następujące polecenie, zastępując nazwą wdrożenia:

```bash
sudo mysql –h v1-mariadb
```
 
 
Teraz można używać standardowych poleceń SQL do tworzenia baz danych, tabel itp. Na przykład `Create DATABASE testdb1;` tworzy pustą bazę danych. 
 
 
 
## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat zarządzania wykresami kubernetes, zobacz [dokumentację Helm](https://github.com/kubernetes/helm/blob/master/docs/index.md). 

