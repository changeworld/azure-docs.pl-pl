---
title: Wdróż samodzielną bramę Azure API Management w usłudze Kubernetes | Microsoft Docs
description: Dowiedz się, jak wdrożyć samodzielną bramę usługi Azure API Management w usłudze Kubernetes
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
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513837"
---
# <a name="deploy-a-self-hosted-azure-api-management-gateway-to-kubernetes"></a>Wdróż samodzielną bramę usługi Azure API Management w usłudze Kubernetes

W tym artykule przedstawiono procedurę wdrażania samodzielnej bramy usługi Azure API Management w klastrze Kubernetes.

> [!NOTE]
> Funkcja samoobsługowego bramy jest dostępna w wersji zapoznawczej. W wersji zapoznawczej Brama samoobsługowa jest dostępna tylko w warstwach deweloper i Premium bez dodatkowych opłat. Warstwa dewelopera jest ograniczona do jednego wdrożenia bramy samoobsługowego.


## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj procedury przedstawione w następującym przewodniku Szybki start: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md)
- Tworzenie klastra Kubernetes. [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/) jest dobrą opcją dla celów deweloperskich i testowych. W przypadku obciążeń produkcyjnych można użyć [usługi Azure Kubernetes](https://azure.microsoft.com/services/kubernetes-service/) lub klastra Kubernetes w chmurze obcej.
- [Zainicjuj obsługę administracyjną zasobu bramy w wystąpieniu API Management](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-the-gateway-to-kubernetes"></a>Wdróż bramę w usłudze Kubernetes

1. Wybierz pozycję **bramy** z sekcji **Ustawienia**.
2. Wybierz zasób bramy, który ma zostać wdrożony.
3. Wybierz pozycję **wdrożenie**.
4. Należy zauważyć, że nowy token w polu tekstowym **token** został wygenerowany automatycznie przy użyciu domyślnych wartości kluczy **wygaśnięcia** i **tajnych** . Dostosuj jedną lub obie w razie potrzeby, a następnie wybierz pozycję **Generuj** , aby utworzyć nowy token.
5. Upewnij się, że wybrano **Kubernetes** w obszarze **Skrypty wdrażania**.
6. Wybierz pozycję **< Gateway-name >. yml** **, aby pobrać** plik.
7. W razie konieczności dostosuj mapowania portów i nazwę kontenera w pliku YML.
8. Wybierz ikonę **kopiowania** znajdującą się po prawej stronie pola tekstowego **Wdróż** , aby zapisać polecenie `kubectl` w Schowku. 
9. Wklej polecenie do okna terminalu (lub polecenia). Należy pamiętać, że polecenie oczekuje, że pobrany plik środowiska będzie obecny w bieżącym katalogu.
```console
    kubectl apply -f <gateway-name>.yaml
```
10. Wykonaj polecenie. Polecenie instruuje klaster Kubernetes, aby uruchamiał kontener przy użyciu wstępnie hostowanego obrazu bramy pobranego z Container Registry firmy Microsoft i skonfigurować kontener do udostępniania portów HTTP (8080) i HTTPS (443).
11. Uruchom poniższe polecenie, aby sprawdzić, czy brama pod jest uruchomiona. Należy pamiętać, że nazwa użytkownika jest inna. 
```console
kubectl get pods
NAME                              READY   STATUS    RESTARTS   AGE
local-gateway-55f774f844-bv9wt    1/1     Running   0          1m
```
12. Uruchom poniższe polecenie, aby sprawdzić, czy Usługa bramy jest uruchomiona. Należy pamiętać, że nazwa usługi będzie się różnić. 
```console
kubectl get services
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
localgateway     NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
```
13. Wróć do Azure Portal i upewnij się, że wdrożony węzeł bramy jest w stanie dobra kondycja.

![Stan bramy](media/api-management-howto-deploy-self-hosted-gateway-to-k8s/status.png)

> [!TIP]
> Użyj <code>kubectl logs <gateway-pod-name></code> polecenia, aby wyświetlić migawkę dziennika bramy samohostowanej.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat bramy samoobsługowej, zobacz temat [usługa Azure API Management](self-hosted-gateway-overview.md) samodzielna Brama — Omówienie
* Dowiedz się więcej o [usłudze Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/intro-kubernetes)


