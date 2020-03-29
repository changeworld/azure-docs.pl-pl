---
title: Uruchamianie, zatrzymywania i usuwania aplikacji usługi Azure Spring Cloud | Dokumenty firmy Microsoft
description: Jak uruchomić, zatrzymać i usunąć aplikację usługi Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: brendm
ms.openlocfilehash: daa549e248668add54530e90174134c4e0059b3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276840"
---
# <a name="start-stop-and-delete-your-azure-spring-cloud-application"></a>Uruchamianie, zatrzymywania i usuwania aplikacji usługi Azure Spring Cloud

W tym przewodniku wyjaśniono, jak zmienić stan aplikacji w usłudze Azure Spring Cloud przy użyciu witryny Azure portal lub interfejsu wiersza polecenia platformy Azure.

## <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Po wdrożeniu aplikacji można ją uruchomić, zatrzymać i usunąć za pomocą witryny Azure portal.

1. Przejdź do wystąpienia usługi Azure Spring Cloud w witrynie Azure portal.
1. Wybierz kartę **Pulpit nawigacyjny aplikacji.**
1. Wybierz aplikację, którego stan chcesz zmienić.
1. Na stronie **Przegląd** dla tej aplikacji wybierz pozycję **Start/Stop**, **Uruchom ponownie**lub **Usuń**.

## <a name="using-the-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

> [!NOTE]
> Można użyć parametrów opcjonalnych i skonfigurować ustawienia domyślne za pomocą interfejsu wiersza polecenia platformy Azure. Dowiedz się więcej o platformie Azure CLI, czytając [naszą dokumentację referencyjną.](spring-cloud-cli-reference.md)  

Najpierw zainstaluj rozszerzenie usługi Azure Spring Cloud dla interfejsu wiersza polecenia platformy Azure w następujący sposób:

```azurecli
az extension add --name spring-cloud
```

Następnie wybierz dowolną z następujących operacji interfejsu wiersza polecenia platformy Azure:

* Aby uruchomić aplikację:

    ```azurecli
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Aby zatrzymać aplikację:

    ```azurecli
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Aby ponownie uruchomić aplikację:

    ```azurecli
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Aby usunąć aplikację:

    ```azurecli
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
