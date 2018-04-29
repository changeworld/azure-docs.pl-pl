---
title: Zarządzanie klastrem Azure Kubernetes z interfejsu użytkownika sieci web
description: Przy użyciu interfejsu użytkownika sieci web Kubernetes usługi kontenera platformy Azure
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 02/21/2017
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 0680551d3a87c942574a4eac70fa380cc1e9b5d9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="using-the-kubernetes-web-ui-with-azure-container-service"></a>Przy użyciu interfejsu użytkownika sieci web Kubernetes z usługi kontenera platformy Azure

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

## <a name="prerequisites"></a>Wymagania wstępne
W tym przewodniku założono, że [utworzony klaster Kubernetes za pomocą usługi kontenera platformy Azure](container-service-kubernetes-walkthrough.md).


Założono również, że masz 2.0 interfejsu wiersza polecenia platformy Azure i `kubectl` narzędzia są zainstalowane.

Możesz przetestować, jeśli masz `az` zainstalowany, uruchamiając narzędzie:

```console
$ az --version
```

Jeśli nie masz `az` narzędzie zainstalowane, nie ma instrukcji [tutaj](https://github.com/azure/azure-cli#installation).

Możesz przetestować, jeśli masz `kubectl` zainstalowany, uruchamiając narzędzie:

```console
$ kubectl version
```

Jeśli nie masz `kubectl` zainstalowany, możesz uruchomić:

```console
$ az acs kubernetes install-cli
```

## <a name="overview"></a>Przegląd

### <a name="connect-to-the-web-ui"></a>Nawiązania połączenia interfejsu użytkownika sieci web
Interfejs użytkownika sieci web Kubernetes można uruchomić, uruchamiając:

```console
$ az acs kubernetes browse -g [Resource Group] -n [Container service instance name]
```

Należy to otwarcie przeglądarki sieci web skonfigurowana do komunikowania się bezpieczne połączenie komputera lokalnego do interfejsu użytkownika sieci web Kubernetes serwera proxy.

### <a name="create-and-expose-a-service"></a>Utworzyć i uwidocznić usługę
1. W Kubernetes interfejsu użytkownika sieci web, kliknij przycisk **Utwórz** przycisk w górnym prawym okienku.

    ![Kubernetes Tworzenie interfejsu użytkownika](./media/container-service-kubernetes-ui/create.png)

    Zostanie otwarte okno dialogowe której będzie można rozpocząć tworzenie aplikacji.

2. Nadaj nazwę `hello-nginx`. Użyj [ `nginx` kontenera z Docker](https://hub.docker.com/_/nginx/) i wdrażanie trzech replik tej usługi sieci web.

    ![Okno dialogowe Tworzenie Kubernetes Pod](./media/container-service-kubernetes-ui/nginx.png)

3. W obszarze **usługi**, wybierz pozycję **zewnętrznych** i wprowadź port 80.

    To ustawienie zrównoważenie obciążenia ruchu do trzech replik.

    ![Okno dialogowe Tworzenie usługi Kubernetes](./media/container-service-kubernetes-ui/service.png)

4. Kliknij przycisk **Wdróż** do wdrażania tych kontenerów i usług.

    ![Wdrażanie Kubernetes](./media/container-service-kubernetes-ui/deploy.png)

### <a name="view-your-containers"></a>Wyświetl kontenerów
Po kliknięciu **Wdróż**, interfejs użytkownika przedstawiono usługi wdrażania:

![Stan Kubernetes](./media/container-service-kubernetes-ui/status.png)

Można wyświetlić stan każdego obiektu Kubernetes okręgu po lewej stronie interfejsu użytkownika, w obszarze **stanowiskami**. Jeśli jest częściowo koło obiektu nadal jest wdrażana. Gdy obiekt pełni zostanie wdrożona, Wyświetla zielony znacznik wyboru:

![Kubernetes wdrożony](./media/container-service-kubernetes-ui/deployed.png)

Gdy wszystko jest uruchomiona, kliknij jeden z Twojej stanowiskami, aby zobaczyć szczegóły dotyczące uruchomionej usługi sieci web.

![Stanowiskami Kubernetes](./media/container-service-kubernetes-ui/pods.png)

W **stanowiskami** widoku wyświetlane są informacje o kontenery w pod, jak również zasoby Procesora i pamięci używane przez te kontenerów:

![Kubernetes zasobów](./media/container-service-kubernetes-ui/resources.png)

Jeśli nie widzisz zasoby, może być konieczne Poczekaj kilka minut dla danych monitorowania propagacji.

Aby wyświetlić dzienniki dla kontenera sieci, kliknij przycisk **Sprawdź dzienniki**.

![Dzienniki Kubernetes](./media/container-service-kubernetes-ui/logs.png)

### <a name="viewing-your-service"></a>Wyświetlanie usługi
Poza uruchamianiem kontenerów, interfejsu użytkownika Kubernetes utworzył zewnętrzne `Service` który udostępnia usługi równoważenia obciążenia, aby przenieść ruchu kontenery w klastrze.

W okienku nawigacji po lewej stronie kliknij **usług** Aby wyświetlić wszystkie usługi (powinien istnieć tylko jeden).

![Kubernetes usług](./media/container-service-kubernetes-ui/service-deployed.png)

W tym widoku powinny być widoczne punkt końcowy zewnętrzne (adres IP), która została przydzielona do usługi.
Kliknięcie tego adresu IP, powinna zostać wyświetlona z kontener Nginx uruchomiony za usługą równoważenia obciążenia.

![Widok nginx](./media/container-service-kubernetes-ui/nginx-page.png)

### <a name="resizing-your-service"></a>Zmiana rozmiaru usługi
Oprócz wyświetlania obiektów w interfejsie użytkownika, możesz edytować i aktualizowanie obiektów interfejsu API Kubernetes.

Najpierw kliknij **wdrożeń** w lewym okienku nawigacji do wdrożenia usługi.

Po przejściu do tego widoku, kliknij zestawu replik, a następnie kliknij przycisk **Edytuj** na pasku nawigacyjnym górny:

![Edytuj Kubernetes](./media/container-service-kubernetes-ui/edit.png)

Edytuj `spec.replicas` ono być `2`i kliknij przycisk **aktualizacji**.

Powoduje to, że liczba replik można usunąć z dwoma usuwając jeden z stanowiskami.

 

