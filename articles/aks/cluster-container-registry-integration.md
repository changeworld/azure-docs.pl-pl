---
title: Integracja Azure Container Registry z usługą Azure Kubernetes Service
description: Dowiedz się, jak zintegrować usługę Azure Kubernetes Service (AKS) z usługą Azure Container Registry (ACR)
services: container-service
author: mlearned
manager: gwallace
ms.service: container-service
ms.topic: article
ms.date: 08/15/2018
ms.author: mlearned
ms.openlocfilehash: 6c06453d479ae55ceb1c05a7ee8a29ce19a7a13b
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034971"
---
# <a name="preview---authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Wersja zapoznawcza — uwierzytelnianie za pomocą Azure Container Registry z usługi Azure Kubernetes

W przypadku korzystania z Azure Container Registry (ACR) z usługą Azure Kubernetes Service (AKS) należy nawiązać mechanizm uwierzytelniania. W tym artykule opisano zalecane konfiguracje uwierzytelniania między tymi dwiema usługami platformy Azure.

Za pomocą interfejsu wiersza polecenia platformy Azure można skonfigurować AKS do integracji z ACR w kilku prostych poleceniach.

> [!IMPORTANT]
> Funkcja AKS w wersji zapoznawczej to samoobsługowe uczestnictwo. Wersje zapoznawcze są udostępniane w postaci "AS-IS" i "jako dostępne" i są wyłączone z umów dotyczących poziomu usług i ograniczonej rękojmi. Wersje zapoznawcze AKS są częściowo objęte obsługą klienta w oparciu o najlepszy nakład pracy. W związku z tym te funkcje nie są przeznaczone do użytku produkcyjnego. Aby dowiedzieć się więcej, zobacz następujące artykuły pomocy technicznej:
>
> * [Zasady pomocy technicznej AKS](support-policies.md)
> * [Pomoc techniczna platformy Azure — często zadawane pytania](faq.md)

## <a name="before-you-begin"></a>Przed rozpoczęciem

Wymagane są następujące elementy:

* Rola **właściciela** lub **administratora konta platformy Azure** w **subskrypcji platformy Azure**
* Potrzebujesz także interfejsu wiersza polecenia platformy Azure w wersji 2.0.70 lub nowszej oraz rozszerzenia AKS-Preview 0.4.8
* Na kliencie musi być [zainstalowany](https://docs.docker.com/install/) program Docker i potrzebny jest dostęp do [centrum platformy Docker](https://hub.docker.com/)

## <a name="install-latest-aks-cli-preview-extension"></a>Zainstaluj rozszerzenie najnowszej wersji zapoznawczej interfejsu wiersza polecenia AKS

Potrzebujesz rozszerzenia **AKS-Preview 0.4.8** lub nowszego.

```azurecli
az extension remove --name aks-preview 
az extension add -y --name aks-preview
```

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>Utwórz nowy klaster AKS z integracją ACR

Integrację AKS i ACR można skonfigurować podczas początkowego tworzenia klastra AKS.  Aby umożliwić klastrowi AKS współdziałanie z ACR, używana jest jednostka **usługi** Azure Active Directory. Następujące polecenie interfejsu wiersza polecenia tworzy ACR w określonej grupie zasobów i konfiguruje odpowiednią rolę **ACRPull** dla jednostki usługi. Jeśli *ACR-Name* nie istnieje w określonej grupie zasobów, zostanie automatycznie utworzona domyślna nazwa `aks<resource-group>acr` ACR.  Podaj prawidłowe wartości parametrów poniżej.  Parametry w nawiasach są opcjonalne.
```azurecli
az login
az aks create -n myAKSCluster -g myResourceGroup --enable-acr [--acr <acr-name-or-resource-id>]
```
\* * Identyfikator zasobu ACR ma następujący format: 

/subscriptions/< Subscription-d >/resourceGroups/< Resource-Group-Name ><name> 
  
Wykonanie tego kroku może potrwać kilka minut.

## <a name="create-acr-integration-for-existing-aks-clusters"></a>Tworzenie integracji ACR dla istniejących klastrów AKS

Zintegruj ACR z istniejącymi klastrami ACR, dostarczając prawidłowe wartości dla **ACR-Name** i **ACR-Resource-ID** poniżej.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --enable-acr --acr <acrName>
az aks update -n myAKSCluster -g myResourceGroup --enable-acr --acr <acr-resource-id>
```

## <a name="log-in-to-your-acr"></a>Logowanie do ACR

Użyj poniższego polecenia, aby zalogować się do ACR.  Zastąp <acrname> parametr nazwą ACR.  Na przykład wartość domyślna to **aks < grupy zasobów > ACR**.

```azurecli
az acr login -n <acrName>
```

## <a name="pull-an-image-from-docker-hub-and-push-to-your-acr"></a>Ściąganie obrazu z usługi Docker Hub i wypychanie do ACR

Pobierz obraz z usługi Docker Hub, Oznacz obraz i wypchnij do ACR.

```console
acrloginservername=$(az acr show -n <acrname> -g <myResourceGroup> --query loginServer -o tsv)
docker pull nginx
```

```
$ docker tag nginx $acrloginservername/nginx:v1
$ docker push $acrloginservername/nginx:v1

The push refers to repository [someacr1.azurecr.io/nginx]
fe6a7a3b3f27: Pushed
d0673244f7d4: Pushed
d8a33133e477: Pushed
v1: digest: sha256:dc85890ba9763fe38b178b337d4ccc802874afe3c02e6c98c304f65b08af958f size: 948
```

## <a name="update-the-state-and-verify-pods"></a>Zaktualizuj stan i sprawdź, czy są to zasobniki

Wykonaj następujące kroki, aby zweryfikować wdrożenie.

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

Wyświetl plik YAML i edytuj Właściwość Image, zastępując wartość nazwą serwera logowania ACR, obrazem i tagiem.

```
$ cat acr-nginx.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx0-deployment
  labels:
    app: nginx0-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx0
  template:
    metadata:
      labels:
        app: nginx0
    spec:
      containers:
      - name: nginx
        image: <replace this image property with you acr login server, image and tag>
        ports:
        - containerPort: 80

$ kubectl apply -f acr-nginx.yaml
$ kubectl get pods

You should have two running pods.

NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

<!-- LINKS - external -->
[AKS AKS CLI]:  https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create
