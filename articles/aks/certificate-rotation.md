---
title: Obracanie certyfikatów w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak obracać certyfikaty w klastrze usługi Azure Kubernetes (AKS).
services: container-service
author: zr-msft
ms.topic: article
ms.date: 11/15/2019
ms.author: zarhoads
ms.openlocfilehash: 00dcef4ae0f04fc7f550859238ae8c7e1ad19384
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549067"
---
# <a name="rotate-certificates-in-azure-kubernetes-service-aks"></a>Obracanie certyfikatów w usłudze Azure Kubernetes Service (AKS)

Usługa Azure Kubernetes Service (AKS) używa certyfikatów do uwierzytelniania z wieloma jej składnikami. Okresowo może być konieczne obracanie tych certyfikatów ze względów bezpieczeństwa lub zasad. Na przykład może być zasadą obracania wszystkich certyfikatów co 90 dni.

W tym artykule pokazano, jak obrócić certyfikaty w klastrze usługi AKS.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Ten artykuł wymaga, aby użytkownik był uruchomiony w wersji interfejsu wiersza polecenia platformy Azure w wersji 2.0.77 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="aks-certificates-certificate-authorities-and-service-accounts"></a>Certyfikaty AKS, urzędy certyfikacji i konta usług

Usługa AKS generuje i używa następujących certyfikatów, urzędów certyfikacji i kont usług:

* Serwer interfejsu API usługi AKS tworzy urząd certyfikacji o nazwie Urząd certyfikacji klastra.
* Serwer interfejsu API ma urząd certyfikacji klastra, który podpisuje certyfikaty dla komunikacji jednokierunkowej z serwera interfejsu API do kubeletów.
* Każdy kubelet tworzy również żądanie podpisywania certyfikatów (CSR), które jest podpisane przez urząd certyfikacji klastra, w celu komunikacji z kubelet do serwera interfejsu API.
* Magazyn wartości klucza etcd ma certyfikat podpisany przez urząd certyfikacji klastra do komunikacji z etcd do serwera interfejsu API.
* Magazyn wartości klucza etcd tworzy urząd certyfikacji, który podpisuje certyfikaty do uwierzytelniania i autoryzowania replikacji danych między replikami itp.
* Agregator interfejsu API używa urzędu certyfikacji klastra do wystawiania certyfikatów do komunikacji z innymi interfejsami API. Agregator interfejsu API może również mieć własny urząd certyfikacji do wystawiania tych certyfikatów, ale obecnie używa urzędu certyfikacji klastra.
* Każdy węzeł używa tokenu konta usługi (SA), który jest podpisany przez urząd certyfikacji klastra.
* Klient `kubectl` ma certyfikat do komunikowania się z klastrem AKS.

> [!NOTE]
> Klastry AKS utworzone przed marcem 2019 r. mają certyfikaty, które wygasają po dwóch latach. Każdy klaster utworzony po marcu 2019 r. lub dowolny klaster, który ma swoje certyfikaty, ma certyfikaty urzędu certyfikacji klastra, które wygasają po 30 latach. Wszystkie pozostałe certyfikaty wygasają po dwóch latach. Aby sprawdzić, kiedy klaster `kubectl get nodes` został utworzony, należy użyć, aby wyświetlić *wiek* pul węzłów.
> 
> Ponadto można sprawdzić datę wygaśnięcia certyfikatu klastra. Na przykład następujące polecenie wyświetla szczegóły certyfikatu dla klastra *myAKSCluster.*
> ```console
> kubectl config view --raw -o jsonpath="{.clusters[?(@.name == 'myAKSCluster')].cluster.certificate-authority-data}" | base64 -d > my-cert.crt
> openssl x509 -in my-cert.crt -text
> ```

## <a name="rotate-your-cluster-certificates"></a>Obracanie certyfikatów klastra

> [!WARNING]
> Obracanie certyfikatów `az aks rotate-certs` przy użyciu może spowodować do 30 minut przestoju dla klastra AKS.

Użyj [az aks get-credentials,][az-aks-get-credentials] aby zalogować się do klastra AKS. To polecenie pobiera i konfiguruje certyfikat `kubectl` klienta na komputerze lokalnym.

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

Służy `az aks rotate-certs` do obracania wszystkich certyfikatów, certyfikatów I IZ w klastrze.

```azurecli
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> Ukończenie może potrwać do `az aks rotate-certs` 30 minut. Jeśli polecenie nie powiedzie `az aks show` się przed zakończeniem, użyj, aby sprawdzić stan klastra jest *Certyfikat obracanie*. Jeśli klaster jest w stanie awarii, uruchom `az aks rotate-certs` ponownie, aby ponownie obrócić certyfikaty.

Sprawdź, czy stare certyfikaty nie `kubectl` są już prawidłowe, uruchamiając polecenie. Ponieważ certyfikaty używane przez `kubectl`program nie zostały zaktualizowane, zostanie wyświetlony błąd.  Przykład:

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

Zaktualizuj `kubectl` certyfikat `az aks get-credentials`używany przez uruchomienie pliku .

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

Sprawdź, czy certyfikaty zostały `kubectl` zaktualizowane przez uruchomienie polecenia, które zakończy się pomyślnie. Przykład:

```console
kubectl get no
```

> [!NOTE]
> Jeśli masz jakieś usługi, które są uruchamiane na szczycie usługi AKS, takie jak [Usługi Azure Dev Spaces,][dev-spaces]może być konieczne [zaktualizowanie certyfikatów związanych z tymi usługami.][dev-spaces-rotate]

## <a name="next-steps"></a>Następne kroki

W tym artykule pokazano, jak automatycznie obracać certyfikaty klastra, certyfikaty II i SA. Aby uzyskać więcej informacji na temat najlepszych rozwiązań dotyczących zabezpieczeń systemu AKS, można zapoznać się z najlepszymi rozwiązaniami dotyczącymi [zabezpieczeń i uaktualnień klastra w usłudze Azure Kubernetes Service (AKS).][aks-best-practices-security-upgrades]


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
[dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[dev-spaces-rotate]: ../dev-spaces/troubleshooting.md#error-using-dev-spaces-after-rotating-aks-certificates
