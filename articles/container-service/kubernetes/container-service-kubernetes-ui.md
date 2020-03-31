---
title: (PRZESTARZAŁE) Zarządzanie klastrem platformy Azure Kubernetes za pomocą internetowego interfejsu użytkownika
description: Korzystanie z interfejsu użytkownika sieci Web usługi Kubernetes w usłudze kontenerów platformy Azure
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 02/21/2017
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 01abcc961d1c2ad9d3e2cf35f82e62929bc2fb89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371141"
---
# <a name="deprecated-using-the-kubernetes-web-ui-with-azure-container-service"></a>(PRZESTARZAŁE) Korzystanie z interfejsu użytkownika sieci Web usługi Kubernetes za pomocą usługi Azure Container Service

> [!TIP]
> Aby zapoznać się ze zaktualizowaną wersją tego artykułu, która korzysta z usługi Azure Kubernetes, zobacz [Dostęp do pulpitu nawigacyjnego platformy Kubernetes w usłudze Azure Kubernetes Service (AKS).](../../aks/kubernetes-dashboard.md)

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Wymagania wstępne
W tym przewodniku przyjęto założenie, że [utworzono klaster Kubernetes przy użyciu usługi Azure Container Service.](container-service-kubernetes-walkthrough.md)


Zakłada się również, że masz `kubectl` zainstalowaną platformę Azure CLI i narzędzia.

Można sprawdzić, czy `az` masz zainstalowane narzędzie, uruchamiając:

```azurecli
az --version
```

Jeśli nie masz zainstalowanego `az` narzędzia, instrukcje są [tutaj](https://github.com/azure/azure-cli#installation).

Można sprawdzić, czy `kubectl` masz zainstalowane narzędzie, uruchamiając:

```console
kubectl version
```

Jeśli nie masz `kubectl` zainstalowanego, możesz uruchomić:

```azurecli
az acs kubernetes install-cli
```

## <a name="overview"></a>Omówienie

### <a name="connect-to-the-web-ui"></a>Łączenie się z interfejsem użytkownika sieci Web
Interfejs użytkownika sieci Web kubernetes można uruchomić, uruchamiając:

```azurecli
az acs kubernetes browse -g [Resource Group] -n [Container service instance name]
```

Powinno to otworzyć przeglądarkę sieci Web skonfigurowaną do rozmowy z bezpiecznym serwerem proxy łączącym komputer lokalny z interfejsem użytkownika sieci Web usługi Kubernetes.

### <a name="create-and-expose-a-service"></a>Tworzenie i udostępnianie usługi
1. W interfejsie użytkownika sieci Web kubernetes kliknij przycisk **Utwórz** w prawym górnym oknie.

    ![Tworzenie interfejsu użytkownika kubernetes](./media/container-service-kubernetes-ui/create.png)

    Zostanie otwarte okno dialogowe, w którym można rozpocząć tworzenie aplikacji.

2. Nadaj `hello-nginx`mu nazwę . Użyj [ `nginx` kontenera z platformy Docker](https://hub.docker.com/_/nginx/) i wdrożyć trzy repliki tej usługi sieci web.

    ![Okno dialogowe Tworzenia zasobnika kubernetes](./media/container-service-kubernetes-ui/nginx.png)

3. W obszarze **Usługa**wybierz **pozycję Zewnętrzny** i wprowadź port 80.

    To ustawienie równoważe obciążenie ruchu do trzech replik.

    ![Okno dialogowe tworzenia usługi Kubernetes](./media/container-service-kubernetes-ui/service.png)

4. Kliknij **przycisk Wdrażanie,** aby wdrożyć te kontenery i usługi.

    ![Wdrożenie na platformie Kubernetes](./media/container-service-kubernetes-ui/deploy.png)

### <a name="view-your-containers"></a>Wyświetlanie kontenerów
Po **kliknięciu przycisku Deploy**interfejs użytkownika wyświetla widok usługi podczas wdrażania:

![Stan Kubernetes](./media/container-service-kubernetes-ui/status.png)

Stan każdego obiektu Kubernetes można zobaczyć w okręgu po lewej stronie interfejsu użytkownika, w obszarze **Zasobniki**. Jeśli jest to częściowo pełny okrąg, obiekt jest nadal wdrażany. Gdy obiekt jest w pełni wdrożony, wyświetla zielony znacznik wyboru:

![Wdrożone sieci Kubernetes](./media/container-service-kubernetes-ui/deployed.png)

Gdy wszystko jest uruchomione, kliknij jedną z zasobników, aby wyświetlić szczegółowe informacje o uruchomionej usłudze sieci web.

![Zasobniki Kubernetes](./media/container-service-kubernetes-ui/pods.png)

W widoku **Zasoby** można wyświetlić informacje o kontenerach w zasobniku, a także zasobów procesora CPU i pamięci używanych przez te kontenery:

![Zasoby Kubernetes](./media/container-service-kubernetes-ui/resources.png)

Jeśli nie widzisz zasobów, może być konieczne odczekanie kilku minut na propagowanie danych monitorowania.

Aby wyświetlić dzienniki kontenera, kliknij pozycję **Wyświetl dzienniki**.

![Dzienniki Kubernetes](./media/container-service-kubernetes-ui/logs.png)

### <a name="viewing-your-service"></a>Wyświetlanie usługi
Oprócz uruchamiania kontenerów interfejsu użytkownika kubernetes utworzył zewnętrznego, `Service` który apowiew moduł równoważenia obciążenia, aby przenieść ruch do kontenerów w klastrze.

W lewym okienku nawigacji kliknij pozycję **Usługi,** aby wyświetlić wszystkie usługi (powinna istnieć tylko jedna).

![Usługi Kubernetes](./media/container-service-kubernetes-ui/service-deployed.png)

W tym widoku powinien zostać wyświetlony zewnętrzny punkt końcowy (adres IP), który został przydzielony do usługi.
Po kliknięciu tego adresu IP, powinien zostać wyświetlony kontener Nginx uruchomiony za modułem równoważenia obciążenia.

![widok nginx](./media/container-service-kubernetes-ui/nginx-page.png)

### <a name="resizing-your-service"></a>Zmiana rozmiaru usługi
Oprócz wyświetlania obiektów w interfejsie użytkownika można edytować i aktualizować obiekty interfejsu API usługi Kubernetes.

Najpierw kliknij pozycję **Wdrożenia** w lewym okienku nawigacji, aby wyświetlić wdrożenie usługi.

Gdy już znajdziesz się w tym widoku, kliknij zestaw replik, a następnie kliknij pozycję **Edytuj** na górnym pasku nawigacyjnym:

![Edycja kubernetes](./media/container-service-kubernetes-ui/edit.png)

Edytuj `spec.replicas` pole, `2`które ma być , a następnie kliknij przycisk **Aktualizuj**.

Powoduje to, że liczba replik do spadku do dwóch przez usunięcie jednego z zasobników.

 

