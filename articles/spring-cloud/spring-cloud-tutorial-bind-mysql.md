---
title: Samouczek — jak powiązać usługę Azure Database dla wystąpienia MySQL z aplikacją usługi Azure Spring Cloud
description: W tym samouczku pokazano, jak powiązać wystąpienie usługi Azure Database dla mysql z aplikacją usługi Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 657aa70d77fd1af9fd2121a3e98ea3aca7773642
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76277542"
---
# <a name="tutorial-bind-an-azure-database-for-mysql-instance-to-your-azure-spring-cloud-application"></a>Samouczek: Powiąż wystąpienie usługi Azure Database dla mysql z aplikacją usługi Azure Spring Cloud 

Za pomocą usługi Azure Spring Cloud można automatycznie powiązać wybrane usługi platformy Azure z aplikacjami, zamiast ręcznie konfigurować aplikację Spring Boot. W tym samouczku pokazano, jak powiązać aplikację z usługą Azure Database dla wystąpienia MySQL.

## <a name="prerequisites"></a>Wymagania wstępne

* Wdrożone wystąpienie usługi Azure Spring Cloud
* Konto usługi Azure Database for MySQL
* Interfejs wiersza polecenia platformy Azure

Jeśli nie masz wdrożonego wystąpienia usługi Azure Spring Cloud, postępuj zgodnie z instrukcjami w [przewodniku Szybki start: Uruchom aplikację usługi Azure Spring Cloud przy użyciu witryny Azure portal,](spring-cloud-quickstart-launch-app-portal.md) aby wdrożyć pierwszą aplikację Spring Cloud.

## <a name="bind-your-app-to-your-azure-database-for-mysql-instance"></a>Powiąż aplikację z usługą Azure Database dla wystąpienia MySQL

1. Zanotuj nazwę użytkownika administratora i hasło do konta MySQL usługi Azure Database. 

1. Połącz się z serwerem, utwórz bazę danych o nazwie **testdb** z klienta MySQL, a następnie utwórz nowe konto niebędące administratorem.

1. W pliku *pom.xml* projektu dodaj następującą zależność:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```
1. W pliku *application.properties* usuń `spring.datasource.*` wszystkie właściwości.

1. Zaktualizuj `az spring-cloud app update`bieżące wdrożenie, uruchamiając lub `az spring-cloud app deployment create`utwórz nowe wdrożenie dla tej zmiany, uruchamiając program .  Te polecenia zaktualizować lub utworzyć aplikację z nową zależnością.

1. W witrynie Azure portal na stronie usługi **Azure Spring Cloud** poszukaj **pulpitu nawigacyjnego aplikacji,** a następnie wybierz aplikację do powiązania z usługą Azure Database dla wystąpienia MySQL.  Jest to ta sama aplikacja, która została zaktualizowana lub wdrożona w poprzednim kroku. 

1. Wybierz polecenie **Powiązanie usługi**, a następnie wybierz przycisk **Utwórz powiązanie usługi.** 

1. Wypełnij formularz, wybierając **azure MySQL** jako **typ powiązania**, przy użyciu tej samej nazwy bazy danych, który był używany wcześniej i przy użyciu tej samej nazwy użytkownika i hasła, które zostały odnotowane w pierwszym kroku.

1. Uruchom ponownie aplikację, a to powiązanie powinno teraz działać.

1. Aby upewnić się, że powiązanie usługi jest poprawne, wybierz nazwę powiązania, a następnie sprawdź jego szczegóły. Pole `property` powinno wyglądać następująco:
    ```
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak powiązać aplikację usługi Azure Spring Cloud z usługą Azure Database for MySQL.  Aby dowiedzieć się więcej na temat zarządzania usługą Azure Spring Cloud, zobacz artykuł dotyczący odnajdowania i rejestracji usług.

> [!div class="nextstepaction"]
> [Włączanie odnajdowania i rejestracji usługi przy użyciu rejestru usługi Spring Cloud Service](spring-cloud-service-registration.md)
