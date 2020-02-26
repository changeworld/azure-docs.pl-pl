---
title: Obróć certyfikaty w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak obrócić certyfikaty w klastrze usługi Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.topic: article
ms.date: 11/15/2019
ms.author: zarhoads
ms.openlocfilehash: 02bfdbc840065558003b249e1e3ea52f46ec64d6
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77596271"
---
# <a name="rotate-certificates-in-azure-kubernetes-service-aks"></a>Obróć certyfikaty w usłudze Azure Kubernetes Service (AKS)

Usługa Azure Kubernetes Service (AKS) używa certyfikatów do uwierzytelniania z wieloma składnikami. Okresowo może być konieczne obrócenie tych certyfikatów z przyczyn związanych z zabezpieczeniami lub zasadami. Na przykład może istnieć zasada, aby obrócić wszystkie certyfikaty co 90 dni.

W tym artykule pokazano, jak obrócić certyfikaty w klastrze AKS.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Ten artykuł wymaga uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2.0.77 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="aks-certificates-certificate-authorities-and-service-accounts"></a>AKS certyfikaty, urzędy certyfikacji i konta usług

AKS generuje i używa następujących certyfikatów, urzędów certyfikacji i kont usług:

* Serwer interfejsu API AKS tworzy urząd certyfikacji (CA) o nazwie Cluster-CA.
* Serwer interfejsu API ma urząd certyfikacji klastra, który podpisuje certyfikaty do jednokierunkowej komunikacji z serwera interfejsu API do kubelets.
* Każdy kubelet tworzy również żądanie podpisania certyfikatu (CSR) podpisane przez urząd certyfikacji klastra w celu komunikacji z kubelet z serwerem interfejsu API.
* Magazyn wartości klucza etcd ma certyfikat podpisany przez urząd certyfikacji klastra na potrzeby komunikacji z etcd z serwerem interfejsu API.
* Magazyn wartości klucza etcd tworzy urząd certyfikacji, który podpisuje certyfikaty w celu uwierzytelniania i autoryzacji replikacji danych między replikami etcd w klastrze AKS.
* Agregator interfejsów API używa urzędu certyfikacji klastra do wystawiania certyfikatów do komunikacji z innymi interfejsami API, takimi jak otwarte Service Broker dla platformy Azure. Agregator API może również mieć własny urząd certyfikacji do wystawiania tych certyfikatów, ale obecnie używa urzędu certyfikacji klastra.
* W każdym węźle jest używany token konta usługi (SA), który jest podpisany przez urząd certyfikacji klastra.
* Klient `kubectl` ma certyfikat do komunikacji z klastrem AKS.

> [!NOTE]
> W przypadku klastrów AKS utworzonych przed Marzec 2019 istnieją certyfikaty, które wygasną po upływie dwóch lat. Każdy klaster utworzony po marcu 2019 lub dowolny klaster, który ma swoje certyfikaty, został obrócony, ma certyfikaty, które wygasną po 30 latach. Aby sprawdzić, kiedy klaster został utworzony, użyj `kubectl get nodes`, aby zobaczyć *wiek* pul węzłów.
> 
> Ponadto możesz sprawdzić datę wygaśnięcia certyfikatu klastra. Na przykład następujące polecenie wyświetla szczegóły certyfikatu dla klastra *myAKSCluster* .
> ```console
> kubectl config view --raw -o jsonpath="{.clusters[?(@.name == 'myAKSCluster')].cluster.certificate-authority-data}" | base64 -d > my-cert.crt
> openssl x509 -in my-cert.crt -text
> ```

## <a name="rotate-your-cluster-certificates"></a>Obróć certyfikaty klastra

> [!WARNING]
> Obrócenie certyfikatów przy użyciu `az aks rotate-certs` może spowodować przestoje dla klastra AKS do 30 minut.

Użyj [AZ AKS Get-Credentials][az-aks-get-credentials] , aby zalogować się do klastra AKS. To polecenie pobiera również i konfiguruje `kubectl` certyfikat klienta na komputerze lokalnym.

```console
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

Użyj `az aks rotate-certs`, aby obrócić wszystkie certyfikaty, urzędy certyfikacji i sygnaturę dostępu współdzielonego w klastrze.

```console
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> Ukończenie `az aks rotate-certs` może potrwać do 30 minut. Jeśli polecenie zakończy się niepowodzeniem przed ukończeniem, użyj `az aks show`, aby sprawdzić, czy stan klastra to *rotacja certyfikatów*. Jeśli klaster jest w stanie niepowodzenia, uruchom ponownie `az aks rotate-certs`, aby ponownie obrócić certyfikaty.

Sprawdź, czy stare certyfikaty nie są już prawidłowe, uruchamiając polecenie `kubectl`. Ponieważ certyfikaty używane przez `kubectl`nie zostały zaktualizowane, zostanie wyświetlony komunikat o błędzie.  Na przykład:

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

Zaktualizuj certyfikat używany przez `kubectl`, uruchamiając `az aks get-credentials`.

```console
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

Sprawdź, czy certyfikaty zostały zaktualizowane, uruchamiając polecenie `kubectl`, które zakończy się pomyślnie. Na przykład:

```console
kubectl get no
```

## <a name="next-steps"></a>Następne kroki

W tym artykule pokazano, jak automatycznie obrócić certyfikaty klastra, urzędy certyfikacji i sygnaturę dostępu współdzielonego. Aby uzyskać więcej informacji na temat najlepszych rozwiązań dotyczących zabezpieczeń [, Zobacz najlepsze rozwiązania dotyczące zabezpieczeń klastrów i uaktualnień w usłudze Azure Kubernetes Service (AKS)][aks-best-practices-security-upgrades] .


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
