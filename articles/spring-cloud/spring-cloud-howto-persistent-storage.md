---
title: Jak korzystać z magazynu trwałego w usłudze Azure Spring Cloud | Dokumenty firmy Microsoft
description: Jak korzystać z magazynu trwałego w chmurze Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.openlocfilehash: 0e49d59386b19aa8da46b8c8e6acfe50e2124541
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278532"
---
# <a name="use-persistent-storage-in-azure-spring-cloud"></a>Używanie magazynu trwałego w usłudze Azure Spring Cloud

Usługa Azure Spring Cloud udostępnia dwa typy magazynu dla aplikacji: trwałe i tymczasowe.

Domyślnie usługa Azure Spring Cloud zapewnia magazyn tymczasowy dla każdego wystąpienia aplikacji. Magazyn tymczasowy jest ograniczony do 5 GB na wystąpienie z domyślną ścieżką instalacji /tmp.

> [!WARNING]
> Po ponownym uruchomieniu wystąpienia aplikacji skojarzony magazyn tymczasowy zostanie trwale usunięty.

Magazyn trwały to kontener udziału plików zarządzany przez platformę Azure i przydzielony dla aplikacji. Dane przechowywane w magazynie trwałym są współużytkowane przez wszystkie wystąpienia aplikacji. Wystąpienie usługi Azure Spring Cloud może mieć maksymalnie 10 aplikacji z włączoną funkcją magazynu trwałego. Każdej aplikacji przydziela się 50 GB trwałego miejsca. Domyślna ścieżka instalacji dla magazynu trwałego jest /persistent.

> [!WARNING]
> Jeśli wyłączysz magazyn trwały aplikacji, cały ten magazyn zostanie cofnięty, a wszystkie przechowywane dane zostanie utracone.

## <a name="use-the-azure-portal-to-enable-persistent-storage"></a>Włącz magazyn trwały za pomocą portalu Azure

1. Na stronie **głównej** witryny Azure portal wybierz pozycję **Wszystkie zasoby**.

    >![Znajdź ikonę Wszystkie zasoby](media/portal-all-resources.jpg)

1. Wybierz zasób usługi Azure Spring Cloud, który wymaga trwałego magazynu. W tym przykładzie wybrana aplikacja jest wywoływana **sprężyna**.

    > ![Wybierz aplikację](media/select-service.jpg)

1. W nagłówku **Ustawienia** wybierz pozycję **Aplikacje**.

1. Usługi usługi Azure Spring Cloud są wyświetlane w tabeli.  Wybierz usługę, do której chcesz dodać magazyn trwały. W tym przykładzie jest zaznaczona usługa **bramy.**

    > ![Wybór usługi](media/select-gateway.jpg)

1. Na stronie konfiguracji usługi wybierz **pozycję Konfiguracja**

1. Wybierz kartę **Magazyn trwały** i wybierz pozycję **Włącz**.

    > ![Włączanie magazynu trwałego](media/enable-persistent-storage.jpg)

Po włączeniu magazynu trwałego jego rozmiar i ścieżka są wyświetlane na stronie konfiguracji.

## <a name="use-the-azure-cli-to-modify-persistent-storage"></a>Modyfikowanie magazynu trwałego za pomocą interfejsu wiersza polecenia platformy Azure

W razie potrzeby zainstaluj rozszerzenie Spring Cloud dla interfejsu wiersza polecenia platformy Azure:

```azurecli
az extension add --name spring-cloud
```
Inne operacje:

* Aby utworzyć aplikację z włączoną funkcją magazynu trwałego:

    ```azurecli
    az spring-cloud app create -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* Aby włączyć magazyn trwały dla istniejącej aplikacji:

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* Aby wyłączyć magazyn trwały w istniejącej aplikacji:

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage false
    ```

    > [!WARNING]
    > Jeśli wyłączysz magazyn trwały aplikacji, cały ten magazyn zostanie cofnięty, a wszystkie przechowywane dane zostanie trwale utracone.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [przydziałach aplikacji i usług](spring-cloud-quotas.md).
* Dowiedz się, jak [ręcznie skalować aplikację](spring-cloud-tutorial-scale-manual.md).
