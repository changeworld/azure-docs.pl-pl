---
title: Samouczek — powiązanie pamięci podręcznej platformy Azure dla programu Redis z aplikacją usługi Azure Spring Cloud
description: W tym samouczku pokazano, jak powiązać pamięć podręczną azure dla programu Redis z aplikacją usługi Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: brendm
ms.openlocfilehash: 94f7b5a2363b7c53e0f70500e5a0a8cb6f64e611
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76277520"
---
# <a name="bind-azure-cache-for-redis-to-your-azure-spring-cloud-application"></a>Powiąż pamięć podręczną platformy Azure dla usługi Redis z aplikacją usługi Azure Spring Cloud 

Zamiast ręcznie konfigurować aplikacje spring boot, można automatycznie powiązać wybrane usługi platformy Azure z aplikacjami przy użyciu usługi Azure Spring Cloud. W tym artykule pokazano, jak powiązać aplikację z usługą Azure Cache for Redis.

## <a name="prerequisites"></a>Wymagania wstępne

* Wdrożone wystąpienie usługi Azure Spring Cloud
* Wystąpienie usługi Azure Cache for Redis
* Rozszerzenie usługi Azure Spring Cloud dla interfejsu wiersza polecenia platformy Azure

Jeśli nie masz wdrożonego wystąpienia usługi Azure Spring Cloud, wykonaj kroki opisane w [przewodniku Szybki start podczas wdrażania aplikacji Usługi Azure Spring Cloud.](spring-cloud-quickstart-launch-app-portal.md)

## <a name="bind-azure-cache-for-redis"></a>Powiąż pamięć podręczną platformy Azure dla redis

1. Dodaj następującą zależność do pliku pom.xml projektu:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. Usuwanie `spring.redis.*` wszystkich właściwości `application.properties` z pliku

1. Zaktualizuj `az spring-cloud app update` bieżące wdrożenie `az spring-cloud app deployment create`przy użyciu lub utwórz nowe wdrożenie przy użyciu programu .

1. Przejdź do strony usługi Azure Spring Cloud w witrynie Azure portal. Przejdź do **pulpitu nawigacyjnego aplikacji** i wybierz aplikację do powiązania z pamięcią podręczną Azure dla usługi Redis. Ta aplikacja jest taka sama, która została zaktualizowana lub wdrożona w poprzednim kroku.

1. Wybierz **pozycję Powiązanie usługi** i wybierz pozycję **Utwórz powiązanie usługi**. Wypełnij formularz, aby wybrać wartość **typu powiązania** Azure Cache **for Redis**, serwer usługi Azure Cache dla redis i opcję klucz **podstawowy.**

1. Uruchom ponownie aplikację. Powiązanie powinno teraz działać.

1. Aby upewnić się, że powiązanie usługi jest poprawne, wybierz nazwę powiązania i sprawdź jego szczegóły. Pole `property` powinno wyglądać następująco:
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak powiązać aplikację usługi Azure Spring Cloud z usługą Azure Cache for Redis. Aby dowiedzieć się więcej na temat powiązania usług z aplikacją, przejdź do samouczka na temat powiązania aplikacji z usługą Azure Database for MySQL wystąpienie.

> [!div class="nextstepaction"]
> [Dowiedz się, jak powiązać z usługą Azure Database dla wystąpienia MySQL](spring-cloud-tutorial-bind-mysql.md)
