---
title: Samouczek — jak powiązać usługę Azure cache for Redis z Twoją aplikacją w chmurze platformy Azure
description: W tym samouczku pokazano, jak powiązać usługę Azure cache for Redis z aplikacją w chmurze platformy Azure
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: 1653db3619fd569238872ca1fcfd6d0c439e84c9
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74708779"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-cache-for-redis"></a>Samouczek: powiązywanie usług platformy Azure z Twoją aplikacją w chmurze Azure wiosną: Azure cache for Redis

Chmura sprężynowa platformy Azure umożliwia automatyczne powiązanie wybranych usług platformy Azure z aplikacjami zamiast ręcznego konfigurowania aplikacji do rozruchu ze sprężyną. W tym artykule pokazano, jak powiązać aplikację z usługą Azure cache for Redis.

## <a name="prerequisites"></a>Wymagania wstępne

* Wdrożone wystąpienie chmury Azure wiosennej
* Pamięć podręczna platformy Azure dla wystąpienia usługi Redis
* Rozszerzenie chmury Azure wiosny dla interfejsu wiersza polecenia platformy Azure

Jeśli nie masz wdrożonego wystąpienia chmury Azure wiosennej, wykonaj kroki opisane w tym [przewodniku szybki start](spring-cloud-quickstart-launch-app-portal.md) , aby wdrożyć swoją pierwszą aplikację w chmurze.

## <a name="bind-azure-cache-for-redis"></a>Powiązywanie pamięci podręcznej platformy Azure dla Redis

1. Dodaj następującą zależność do `pom.xml` projektu

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