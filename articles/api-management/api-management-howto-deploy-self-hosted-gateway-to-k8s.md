---
title: Wdrażanie samodzielnie hostowanych bram usługi Azure API Management w usłudze Kubernetes | Dokumenty firmy Microsoft
description: Dowiedz się, jak wdrożyć samodzielnie hostowane bramy usługi Azure API Management w usłudze Kubernetes
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 109316acb73d3c5f00186298c1f8840c516e5790
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73513837"
---
# <a name="deploy-a-self-hosted-azure-api-management-gateway-to-kubernetes"></a>Wdrażanie samodzielnie hostowanych bram usługi Azure API Management w usłudze Kubernetes

Ten artykuł zawiera kroki wdrażania samodzielnej bramy usługi Azure API Management w klastrze kubernetes.

> [!NOTE]
> Funkcja bramy hostowanego samodzielnie jest w wersji zapoznawczej. W wersji zapoznawczej brama hostowana samodzielnie jest dostępna tylko w warstwach Deweloper i Premium bez dodatkowych opłat. Warstwa deweloperów jest ograniczona do pojedynczego wdrożenia bramy hostowanej samodzielnie.


## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj procedury przedstawione w następującym przewodniku Szybki start: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md)
- Tworzenie klastra kubernetes. [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/) jest dobrym rozwiązaniem dla celów rozwoju i oceny. W przypadku obciążeń produkcyjnych można użyć [usługi Azure Kubernetes](https://azure.microsoft.com/services/kubernetes-service/) service lub klastra Kubernetes w chmurze obcej.
- [Aprowizuj zasób bramy w wystąpieniu usługi API Management](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-the-gateway-to-kubernetes"></a>Wdrażanie bramy w umięślice

1. Wybierz **bramy** z sekcji **Ustawienia**.
2. Wybierz zasób bramy, który zamierzasz wdrożyć.
3. Wybierz **opcję Wdrażanie**.
4. Należy zauważyć, że nowy token w polu tekstowym **token** został automatyczniegenerowany przy użyciu **domyślnych** wartości wygaśnięcia i **klucz tajny.** Dostosuj jedną lub obie w razie potrzeby i wybierz **pozycję Generuj,** aby utworzyć nowy token.
5. Upewnij się, że w obszarze **Skrypty wdrażania**wybrano opcję **Kubernetes** .
6. Wybierz **<łącze>.yml** nazwa bramy obok **pozycji Wdrożenie,** aby pobrać plik.
7. W razie potrzeby dostosuj mapowania portów i nazwę kontenera w pliku yml.
8. Wybierz ikonę **kopiowania** znajdującą się na prawym `kubectl` końcu pola tekstowego **Wdrażanie,** aby zapisać polecenie w schowku. 
9. Wklej polecenie do okna terminala (lub polecenia). Należy zauważyć, że polecenie oczekuje, że pobrany plik środowiska będzie obecny w bieżącym katalogu.
```console
    kubectl apply -f <gateway-name>.yaml
```
10. Wykonaj polecenie. Polecenie nakazuje klastrowi kubernetes uruchomienie kontenera przy użyciu obrazu bramy hostowanego samodzielnie pobranego z rejestru kontenerów firmy Microsoft oraz skonfigurowanie kontenera do udostępnienia portów HTTP (8080) i HTTPS (443).
11. Uruchom poniższe polecenie, aby sprawdzić, czy zasobnik bramy jest uruchomiony. Pamiętaj, że nazwa zasobnika będzie inna. 
```console
kubectl get pods
NAME                              READY   STATUS    RESTARTS   AGE
local-gateway-55f774f844-bv9wt    1/1     Running   0          1m
```
12. Uruchom poniższe polecenie, aby sprawdzić, czy usługa bramy jest uruchomiona. Należy pamiętać, że nazwa usługi będzie inna. 
```console
kubectl get services
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
localgateway     NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
```
13. Wróć do witryny Azure portal i upewnij się, że węzeł bramy, który właśnie wdrożony jest raportowanie stanu w dobrej kondycji.

![stan bramy](media/api-management-howto-deploy-self-hosted-gateway-to-k8s/status.png)

> [!TIP]
> Polecenie <code>kubectl logs <gateway-pod-name></code> służy do wyświetlania migawki dziennika bramy hostowanego samodzielnie.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o bramie hostowanej samodzielnie, zobacz [Omówienie bramy hostowanego przez](self-hosted-gateway-overview.md) usługę Azure API Management
* Dowiedz się więcej o [usłudze Azure Kubernetes](https://docs.microsoft.com/azure/aks/intro-kubernetes)


