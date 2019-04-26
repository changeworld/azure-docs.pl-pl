---
title: (PRZESTARZAŁE) Zarządzanie klastrem Kubernetes na platformie Azure przy użyciu interfejsu użytkownika sieci web
description: Przy użyciu interfejsu użytkownika sieci web rozwiązania Kubernetes w usłudze Azure Container Service
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 02/21/2017
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: c3a79b2e4fab807613a54d2792f5f5b97570293b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60309770"
---
# <a name="deprecated-using-the-kubernetes-web-ui-with-azure-container-service"></a>(PRZESTARZAŁE) Usługa Azure Container Service przy użyciu interfejsu użytkownika sieci web rozwiązania Kubernetes

> [!TIP]
> Aby uzyskać zaktualizowaną wersję tego artykułu, który korzysta z usługi Azure Kubernetes Service, zobacz [dostęp do pulpitu nawigacyjnego sieci web rozwiązania Kubernetes w usłudze Azure Kubernetes Service (AKS)](../../aks/kubernetes-dashboard.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Wymagania wstępne
W tym przewodniku przyjęto założenie, iż [utworzone za pomocą usługi Azure Container Service klastra Kubernetes](container-service-kubernetes-walkthrough.md).


Zakłada również, że masz wiersza polecenia platformy Azure i `kubectl` narzędzia są zainstalowane.

Można sprawdzić, czy `az` zainstalowane, uruchamiając narzędzie:

```console
$ az --version
```

Jeśli nie masz `az` narzędzie jest zainstalowane, istnieją instrukcje [tutaj](https://github.com/azure/azure-cli#installation).

Można sprawdzić, czy `kubectl` zainstalowane, uruchamiając narzędzie:

```console
$ kubectl version
```

Jeśli nie masz `kubectl` zainstalowany, możesz uruchomić:

```console
$ az acs kubernetes install-cli
```

## <a name="overview"></a>Omówienie

### <a name="connect-to-the-web-ui"></a>Nawiązać połączenie z interfejsu użytkownika sieci web
Możesz uruchomić interfejs użytkownika sieci web rozwiązania Kubernetes, uruchamiając:

```console
$ az acs kubernetes browse -g [Resource Group] -n [Container service instance name]
```

Powinny to otwarcie przeglądarki sieci web skonfigurowany tak, aby komunikować się z serwera proxy bezpieczne połączenie komputera lokalnego do interfejsu użytkownika sieci web rozwiązania Kubernetes.

### <a name="create-and-expose-a-service"></a>Utworzyć i uwidocznić usługę
1. W sieci web rozwiązania Kubernetes interfejsu użytkownika, kliknij przycisk **Utwórz** przycisk w prawym górnym oknie po prawej stronie.

    ![Kubernetes Create UI](./media/container-service-kubernetes-ui/create.png)

    Otwarcie okna dialogowego, gdzie możesz rozpocząć tworzenie aplikacji.

2. Nadaj jej nazwę `hello-nginx`. Użyj [ `nginx` kontenera docker,](https://hub.docker.com/_/nginx/) i wdrażanie trzech replik tej usługi sieci web.

    ![Okno dialogowe Tworzenie zasobnika Kubernetes](./media/container-service-kubernetes-ui/nginx.png)

3. W obszarze **usługi**, wybierz opcję **zewnętrznych** i wprowadź numer portu 80.

    To ustawienie równoważy obciążenie ruchu do trzech replik.

    ![Okno dialogowe Tworzenie usługi Kubernetes](./media/container-service-kubernetes-ui/service.png)

4. Kliknij przycisk **Wdróż** na wdrażanie tych kontenerów i usług.

    ![Kubernetes Deploy](./media/container-service-kubernetes-ui/deploy.png)

### <a name="view-your-containers"></a>Wyświetlanie Twoich kontenerów
Po kliknięciu **Wdróż**, interfejs użytkownika przedstawiono usługi wdrażania:

![Stan usługi Kubernetes](./media/container-service-kubernetes-ui/status.png)

Możesz wyświetlić stan każdego obiektu Kubernetes w okręgu w obszarze po lewej stronie interfejsu użytkownika, **zasobników**. Jeśli jest częściowo pełny okrąg, a następnie obiekt jego wdrażanie nadal trwa. Gdy obiekt jest w pełni wdrożony, Wyświetla zielony znacznik wyboru:

![Kubernetes Deployed](./media/container-service-kubernetes-ui/deployed.png)

Gdy wszystko jest uruchomiona, kliknij jeden z zasobników w taki sposób, aby wyświetlić szczegóły dotyczące uruchomionej usługi sieci web.

![Zasobników Kubernetes](./media/container-service-kubernetes-ui/pods.png)

W **zasobników** widoku znajdują się informacje o kontenerach w zasobnik, a także zasoby Procesora i pamięci używane przez tych kontenerów:

![Zasoby platformy Kubernetes](./media/container-service-kubernetes-ui/resources.png)

Jeśli nie widzisz zasobów może być konieczne Poczekaj kilka minut, zanim dane monitorowania do propagowania.

Aby wyświetlić dzienniki dla kontenera usługi, kliknij przycisk **wyświetlanie dzienników**.

![Dzienniki platformy Kubernetes](./media/container-service-kubernetes-ui/logs.png)

### <a name="viewing-your-service"></a>Wyświetlanie usługi
Oprócz uruchamiania kontenerów, interfejs użytkownika rozwiązania Kubernetes utworzył zewnętrznego `Service` która aprowizuje modułu równoważenia obciążenia, aby przenieść ruch do kontenerów w klastrze.

W okienku nawigacji po lewej stronie kliknij **usług** do wyświetlenia wszystkich usług (powinien istnieć tylko jeden).

![Kubernetes Services](./media/container-service-kubernetes-ui/service-deployed.png)

W tym widoku powinny być widoczne zewnętrznego punktu końcowego (adresu IP), która została przydzielona do usługi.
Kliknięcie tego adresu IP powinien zostać wyświetlony kontener Nginx uruchomiony za modułem równoważenia obciążenia.

![Widok serwera nginx](./media/container-service-kubernetes-ui/nginx-page.png)

### <a name="resizing-your-service"></a>Zmiana rozmiaru usługi
Oprócz wyświetlania obiektów w interfejsie użytkownika, można edytować i aktualizowanie obiektów interfejsu API rozwiązania Kubernetes.

Najpierw kliknij pozycję **wdrożeń** w okienku nawigacji po lewej stronie, aby zobaczyć wdrożenia usługi.

Po otwarciu tego widoku, kliknij pozycję zestawu replik, a następnie kliknij **Edytuj** na pasku nawigacyjnym górny:

![Kubernetes Edit](./media/container-service-kubernetes-ui/edit.png)

Edytuj `spec.replicas` pole było `2`i kliknij przycisk **aktualizacji**.

To powoduje, że liczba replik, można usunąć z dwoma przez usunięcie jednego z zasobników.

 

