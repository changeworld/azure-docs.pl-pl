---
title: Jak powiązać usługę Azure cache for Redis z aplikacją w chmurze z usługą Azure wiosną | Microsoft Docs
description: Dowiedz się, jak powiązać usługę Azure cache for Redis z aplikacją w chmurze platformy Azure
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/06/2019
ms.author: v-vasuke
ms.openlocfilehash: d8fa31207baecc80674fb11b492927800676b8a2
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038944"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-cache-for-redis"></a>Samouczek: powiązywanie usług platformy Azure z Twoją aplikacją w chmurze Azure wiosną: Azure cache for Redis

Chmura sprężynowa platformy Azure umożliwia automatyczne powiązanie wybranych usług platformy Azure z aplikacjami zamiast ręcznego konfigurowania aplikacji do rozruchu ze sprężyną. W tym artykule pokazano, jak powiązać aplikację z usługą Azure cache for Redis.

## <a name="prerequisites"></a>Wymagania wstępne

* Wdrożone wystąpienie chmury Azure wiosennej
* Pamięć podręczna platformy Azure dla wystąpienia usługi Redis
* Rozszerzenie chmury Azure wiosny dla interfejsu wiersza polecenia platformy Azure

W razie potrzeby zainstaluj rozszerzenie Cloud wiosny Azure dla interfejsu wiersza polecenia platformy Azure przy użyciu następującego polecenia:

```azurecli
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

>[!TIP]
> Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule.  Zawiera ona popularne narzędzia platformy Azure preinstalowane, w tym najnowsze wersje usług git, JDK, Maven i interfejsu wiersza polecenia platformy Azure. Jeśli logujesz się do subskrypcji platformy Azure, uruchom [Azure Cloud Shell](https://shell.azure.com) z Shell.Azure.com.  Więcej informacji na temat Azure Cloud Shell można znaleźć w [dokumentacji](../cloud-shell/overview.md) .

## <a name="bind-azure-cache-for-redis"></a>Powiązywanie pamięci podręcznej platformy Azure dla Redis

1. Dodaj następującą zależność w @no__t projektu — 0

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. Usuń `spring.redis.*` właściwości (jeśli istnieją) w pliku `application.properties`

1. Zaktualizuj bieżące wdrożenie przy użyciu `az spring-cloud app update` lub Utwórz nowe wdrożenie przy użyciu `az spring-cloud app deployment create`.

1. Przejdź do strony usługi w chmurze ze sprężyną Azure w Azure Portal. Znajdź **pulpit nawigacyjny aplikacji** i wybierz aplikację, która ma zostać powiązana z pamięcią podręczną platformy Azure dla Redis.  Ta sama aplikacja została zaktualizowana lub wdrożona w poprzednim kroku. Następnie wybierz `Service binding` i wybierz przycisk `Create service binding`. Wypełnij formularz, wybierając pozycję **Typ powiązania** `Azure Cache for Redis`, serwer Redis i opcję klucz podstawowy. 

1. Uruchom ponownie aplikację, a to powiązanie powinno teraz współpracować.

1. Aby upewnić się, że powiązanie usługi jest prawidłowe, wybierz nazwę powiązania i sprawdź jej szczegóły. Pole `property` powinno wyglądać następująco:
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób powiązania aplikacji w chmurze platformy Azure z usługą Azure Redis.  Aby dowiedzieć się więcej na temat powiązań usług z aplikacją, przejdź do samouczka dotyczącego wiązania aplikacji z bazą danych MySQL.

> [!div class="nextstepaction"]
> [Dowiedz się, jak powiązać usługę Azure MySQL z usługą Azure wiosną w chmurze](spring-cloud-tutorial-bind-mysql.md).