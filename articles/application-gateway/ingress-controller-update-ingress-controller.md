---
title: Uaktualnij kontroler transferu danych przychodzących za pomocą Helm
description: Ten artykuł zawiera informacje na temat uaktualniania Application Gateway transferu danych przychodzących przy użyciu Helm.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 10/23/2019
ms.author: caya
ms.openlocfilehash: 6ed73a2172e09e7255447b4467698670c95b63af
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513239"
---
# <a name="how-to-upgrade-application-gateway-ingress-controller-using-helm"></a>Jak uaktualnić Application Gateway transferu danych przychodzących za pomocą Helm 

Kontroler usługi Azure Application Gateway transfer danych przychodzących dla Kubernetes (AGIC) można uaktualnić przy użyciu repozytorium Helm hostowanego w usłudze Azure Storage.

Przed rozpoczęciem procedury uaktualniania upewnij się, że zostało dodane wymagane repozytorium:

- Wyświetl aktualnie dodane repozytoria Helm z:

    ```bash
    helm repo list
    ```

- Dodaj repozytorium AGIC przy użyciu:

    ```bash
    helm repo add \
        application-gateway-kubernetes-ingress \
        https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    ```

## <a name="upgrade"></a>Uaktualnienie

1. Odśwież repozytorium AGIC Helm, aby uzyskać najnowszą wersję:

    ```bash
    helm repo update
    ```

1. Wyświetl dostępne wersje wykresu `application-gateway-kubernetes-ingress`:

    ``` bash
    helm search -l application-gateway-kubernetes-ingress
    ```

    Przykładowa odpowiedź:

    ```bash
    NAME                                                    CHART VERSION   APP VERSION     DESCRIPTION
    application-gateway-kubernetes-ingress/ingress-azure    0.7.0-rc1       0.7.0-rc1       Use Azure Application Gateway as the ingress for an Azure...
    application-gateway-kubernetes-ingress/ingress-azure    0.6.0           0.6.0           Use Azure Application Gateway as the ingress for an Azure...
    ```

    Najnowsza dostępna wersja z powyższej listy: `0.7.0-rc1`

1. Wyświetl aktualnie zainstalowane wykresy Helm:

    ```bash
    helm list
    ```

    Przykładowa odpowiedź:

    ```bash
    NAME            REVISION        UPDATED                         STATUS  CHART                   APP VERSION     NAMESPACE
    odd-billygoat   22              Fri Jun 21 15:56:06 2019        FAILED  ingress-azure-0.7.0-rc1 0.7.0-rc1       default
    ```

    Instalacja wykresu Helm z przykładowej odpowiedzi powyżej ma nazwę `odd-billygoat`. Ta nazwa zostanie użyta w pozostałej części poleceń. Rzeczywista nazwa wdrożenia będzie prawdopodobnie różna.

1. Uaktualnij wdrożenie Helm do nowej wersji:

    ```bash
    helm upgrade \
        odd-billygoat \
        application-gateway-kubernetes-ingress/ingress-azure \
        --version 0.9.0-rc2
    ```

## <a name="rollback"></a>Wycofywania

Jeśli wdrożenie Helm nie powiedzie się, można wycofać do wcześniejszej wersji.

1. Pobierz ostatni znany numer wydania w dobrej kondycji:

    ```bash
    helm history odd-billygoat
    ```

    Przykładowe dane wyjściowe:

    ```bash
    REVISION        UPDATED                         STATUS          CHART                   DESCRIPTION
    1               Mon Jun 17 13:49:42 2019        DEPLOYED        ingress-azure-0.6.0     Install complete
    2               Fri Jun 21 15:56:06 2019        FAILED          ingress-azure-xx        xxxx
    ```

    Z przykładowych danych wyjściowych polecenia `helm history` wygląda jak ostatnie pomyślne wdrożenie naszych `odd-billygoat` była poprawka `1`

1. Wycofaj do ostatniej pomyślnej poprawki:

    ```bash
    helm rollback odd-billygoat 1
    ```
