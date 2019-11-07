---
title: Jak powiązać usługę Azure cache for Redis z aplikacją w chmurze z usługą Azure wiosną | Microsoft Docs
description: Dowiedz się, jak powiązać usługę Azure cache for Redis z aplikacją w chmurze platformy Azure
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: a901e4194909df85f53799d5937515e42ea87a69
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607570"
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