---
title: Jak uruchamiać, zatrzymywać i usuwać aplikację w chmurze Azure wiosną | Microsoft Docs
description: Jak uruchamiać, zatrzymywać i usuwać aplikację w chmurze platformy Azure
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: f7f76644d13c20704d2c3bd908176ac452df2a20
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72039084"
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

* Aby uruchomić aplikację:
    ```Azure CLI
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Aby zatrzymać aplikację:
    ```Azure CLI
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Aby ponownie uruchomić aplikację:
    ```Azure CLI
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Aby usunąć aplikację:
    ```Azure CLI
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
