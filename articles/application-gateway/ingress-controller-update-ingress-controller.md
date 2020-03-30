---
title: Uaktualnianie kontrolera transferu danych przychodzących za pomocą helma
description: Ten artykuł zawiera informacje dotyczące sposobu uaktualniania transferu danych przychodzących bramy aplikacji przy użyciu helma.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 3903ccd1c15765d06cd1794a40567e2c70062538
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795905"
---
# <a name="how-to-upgrade-application-gateway-ingress-controller-using-helm"></a>Jak uaktualnić kontrolera ruchu przychodzącego usługi Application Gateway przy użyciu zestawu narzędzi Helm 

Kontroler transferu danych przychodzących bramy aplikacji platformy Azure dla usługi Kubernetes (AGIC) można uaktualnić przy użyciu repozytorium helm hostowanego w usłudze Azure Storage.

Przed rozpoczęciem procedury uaktualniania upewnij się, że dodano wymagane repozytorium:

- Wyświetl aktualnie dodane repozytoria Helm z:

    ```bash
    helm repo list
    ```

- Dodaj repozytorium AGIC z:

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

1. Wyświetl dostępne wersje `application-gateway-kubernetes-ingress` wykresu:

    ``` bash
    helm search -l application-gateway-kubernetes-ingress
    ```

    Przykładowa odpowiedź:

    ```bash
    NAME                                                    CHART VERSION   APP VERSION     DESCRIPTION
    application-gateway-kubernetes-ingress/ingress-azure    0.7.0-rc1       0.7.0-rc1       Use Azure Application Gateway as the ingress for an Azure...
    application-gateway-kubernetes-ingress/ingress-azure    0.6.0           0.6.0           Use Azure Application Gateway as the ingress for an Azure...
    ```

    Najnowsza dostępna wersja z powyższej listy to:`0.7.0-rc1`

1. Wyświetlanie aktualnie zainstalowanych wykresów Helm:

    ```bash
    helm list
    ```

    Przykładowa odpowiedź:

    ```bash
    NAME            REVISION        UPDATED                         STATUS  CHART                   APP VERSION     NAMESPACE
    odd-billygoat   22              Fri Jun 21 15:56:06 2019        FAILED  ingress-azure-0.7.0-rc1 0.7.0-rc1       default
    ```

    Nazwa ma miejsce w instalacji `odd-billygoat`wykresu Helm z powyższej odpowiedzi przykładowej . Użyjemy tej nazwy dla pozostałych poleceń. Rzeczywista nazwa wdrożenia najprawdopodobniej będzie się różnić.

1. Uaktualnij wdrożenie Helm do nowej wersji:

    ```bash
    helm upgrade \
        odd-billygoat \
        application-gateway-kubernetes-ingress/ingress-azure \
        --version 0.9.0-rc2
    ```

## <a name="rollback"></a>Wycofywania

Jeśli wdrożenie Helm zakończy się niepowodzeniem, można wycofać do poprzedniej wersji.

1. Pobierz ostatni znany zdrowy numer wydania:

    ```bash
    helm history odd-billygoat
    ```

    Przykładowe dane wyjściowe:

    ```bash
    REVISION        UPDATED                         STATUS          CHART                   DESCRIPTION
    1               Mon Jun 17 13:49:42 2019        DEPLOYED        ingress-azure-0.6.0     Install complete
    2               Fri Jun 21 15:56:06 2019        FAILED          ingress-azure-xx        xxxx
    ```

    Z przykładowego wyjścia `helm history` polecenia wygląda na to, `odd-billygoat` że ostatnie pomyślne wdrożenie naszego było`1`

1. Wycofywanie do ostatniej pomyślnej wersji:

    ```bash
    helm rollback odd-billygoat 1
    ```
