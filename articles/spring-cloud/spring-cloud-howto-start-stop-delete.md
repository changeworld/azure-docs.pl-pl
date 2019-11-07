---
title: Jak uruchamiać, zatrzymywać i usuwać aplikację w chmurze Azure wiosną | Microsoft Docs
description: Jak uruchamiać, zatrzymywać i usuwać aplikację w chmurze platformy Azure
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: 9f537ab425428728137e04713e434d8dc09e065a
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607720"
---
# <a name="how-to-start-stop-and-delete-your-azure-spring-cloud-application"></a>Jak uruchamiać, zatrzymywać i usuwać aplikację w chmurze platformy Azure

W tym przewodniku wyjaśniono, jak zmienić stan aplikacji w chmurze Azure sprężynowej przy użyciu Azure Portal lub interfejsu wiersza polecenia.

## <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Po wdrożeniu aplikacji można ją **uruchamiać**, **zatrzymywać**i **usuwać** przy użyciu Azure Portal.

1. Przejdź do wystąpienia usługi w chmurze ze sprężyną platformy Azure w Azure Portal.
1. Wybierz kartę **pulpit nawigacyjny aplikacji** .
1. Wybierz aplikację, której stan chcesz zmienić.
2. Na stronie **Przegląd** dla tej aplikacji Znajdź przyciski do **uruchamiania/zatrzymywania**, **ponownego uruchamiania**i **usuwania** aplikacji.

## <a name="using-the-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

> [!NOTE]
> Możesz użyć parametrów opcjonalnych i skonfigurować wartości domyślne przy użyciu interfejsu wiersza polecenia platformy Azure. Dowiedz się więcej na temat, odczytując [dokumentację referencyjną](spring-cloud-cli-reference.md).  

Zainstaluj rozszerzenie chmury wiosennej dla interfejsu wiersza polecenia platformy Azure:

```azurecli
az extension add --name spring-cloud
```

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
