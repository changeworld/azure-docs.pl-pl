---
title: Wdrażanie kontenerów z Helm w Azure Kubernetes
description: Wdrażanie kontenerów w klastrze Kubernetes usługi kontenera platformy Azure za pomocą narzędzia pakowania Helm
services: container-service
author: sauryadas
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/10/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 882e785968f94473e80c7a14e5a68498add37735
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="use-helm-to-deploy-containers-on-a-kubernetes-cluster"></a>Umożliwia wdrażanie kontenerów w klastrze Kubernetes Helm

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

[Helm](https://github.com/kubernetes/helm/) jest narzędziem open source tworzenia pakietów, które pomaga zainstalować i zarządzanie cyklem życia aplikacji Kubernetes. Podobnie jak menedżerów pakietu systemu Linux, takich jak Apt get i Yum, Helm służy do zarządzania Kubernetes wykresy, które są pakiety zasobów Kubernetes wstępnie skonfigurowane. W tym artykule przedstawiono sposób pracy z Helm w klastrze Kubernetes wdrożony w usłudze kontenera platformy Azure.

Helm ma dwa składniki: 
* **Helm CLI** jest klienta, który jest uruchamiany na komputerze lokalnie lub w chmurze  

* **Sterownicy** serwera, która działa w klastrze Kubernetes i umożliwia zarządzanie cyklem życia aplikacji Kubernetes 
 
## <a name="prerequisites"></a>Wymagania wstępne

* [Tworzenie klastra Kubernetes](container-service-kubernetes-walkthrough.md) usługi kontenera platformy Azure

* [Instalowanie i konfigurowanie `kubectl` ](../container-service-connect.md) na komputerze lokalnym

* [Zainstaluj Helm](https://github.com/kubernetes/helm/blob/master/docs/install.md) na komputerze lokalnym

## <a name="helm-basics"></a>Podstawy Helm 

Aby wyświetlić informacje o klastrze Kubernetes są sterownicy Instalowanie i wdrażanie aplikacji, wpisz następujące polecenie:

```bash
kubectl cluster-info 
```
![kubectl klastra — informacje](./media/container-service-kubernetes-helm/clusterinfo.png)
 
Po zainstalowaniu Helm zainstalować sterownicy w klastrze Kubernetes, wpisując następujące polecenie:

```bash
helm init --upgrade
```
Po pomyślnym ukończeniu, pojawić się dane wyjściowe podobne do następujących:

![Sterownicy instalacji](./media/container-service-kubernetes-helm/tiller-install.png)
 
 
 
 
Aby wyświetlić wszystkie dostępne wykresy Helm w repozytorium, wpisz następujące polecenie:

```bash 
helm search 
```

Zostaną wyświetlone dane wyjściowe podobne do następujących:

![Helm wyszukiwania](./media/container-service-kubernetes-helm/helm-search.png)
 
Aby zaktualizować wykresy, aby pobrać najnowsze wersje, wpisz:

```bash 
helm repo update 
```
## <a name="deploy-an-nginx-ingress-controller-chart"></a>Wdrażanie Nginx wejściowych kontrolera wykresu 
 
Aby wdrożyć wykresu kontrolera wejściowych Nginx, wpisz jedno polecenie:

```bash
helm install stable/nginx-ingress 
```
![Wdrażanie kontrolera wejściowych](./media/container-service-kubernetes-helm/nginx-ingress.png)

W przypadku wpisania `kubectl get svc` do wyświetlania wszystkich usług, które są uruchomione w klastrze, zobaczysz, że adres IP jest przypisane do kontrolera wejściowych. (Przypisania w trakcie, zobacz `<pending>`. Może potrwać kilka minut.) 

Po adres IP przypisany adres, przejdź do wartości jako zewnętrzny adres IP, aby wyświetlić Nginx wewnętrznej bazy danych, systemem. 
 
![Transfer danych przychodzących adresów IP](./media/container-service-kubernetes-helm/ingress-ip-address.png)


Aby wyświetlić listę wykresy zainstalowany w klastrze, wpisz:

```bash
helm list 
```

Polecenie można skrócić `helm ls`.
 
 
 
 
## <a name="deploy-a-mariadb-chart-and-client"></a>Wdrażanie wykresu MariaDB i klienta

Teraz można wdrożyć na wykresie MariaDB i MariaDB klienta do połączenia z bazą danych.

Aby wdrożyć wykresu MariaDB, wpisz następujące polecenie:

```bash
helm install --name v1 stable/mariadb
```

gdzie `--name` jest znacznika używany w wersjach.

> [!TIP]
> Jeśli wdrożenie nie powiedzie się, uruchom `helm repo update` i spróbuj ponownie.
>
 
 
Aby wyświetlić wszystkich schematów, które są wdrożone w klastrze, wpisz:

```bash 
helm list
```
 
Aby wyświetlić wszystkie wdrożenia uruchomiona w klastrze, wpisz:

```bash
kubectl get deployments 
``` 
 
 
Na koniec aby uruchomić pod dostęp klienta do, wpisz:

```bash
kubectl run v1-mariadb-client --rm --tty -i --image bitnami/mariadb --command -- bash  
``` 
 
 
Aby nawiązać połączenie klient, wpisz następujące polecenie, zastępując `v1-mariadb` o nazwie wdrożenia:

```bash
sudo mysql –h v1-mariadb
```
 
 
Można teraz używać standardowych poleceń SQL do tworzenia baz danych, tabelach itp. Na przykład `Create DATABASE testdb1;` tworzy pustą bazę danych. 
 
 
 
## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać więcej informacji o zarządzaniu Kubernetes wykresy, zobacz [Helm dokumentacji](https://github.com/kubernetes/helm/blob/master/docs/index.md). 

