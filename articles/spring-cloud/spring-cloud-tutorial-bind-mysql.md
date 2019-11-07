---
title: Jak powiązać Azure Database for MySQL ze swoją aplikacją w chmurze Azure wiosną | Microsoft Docs
description: W tym artykule opisano sposób powiązania usługi Azure MySQL z Twoją aplikacją w chmurze Azure wiosennej
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: b6de5bb3b25c111d1b7775ea9570a4ae2cf45042
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607587"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-database-for-mysql"></a>Samouczek: powiązywanie usług platformy Azure z Twoją aplikacją w chmurze Azure wiosną: Azure Database for MySQL

Chmura sprężynowa platformy Azure umożliwia automatyczne powiązanie wybranych usług platformy Azure z aplikacjami zamiast ręcznego konfigurowania aplikacji do rozruchu ze sprężyną. W tym samouczku przedstawiono sposób powiązania aplikacji z usługą Azure MySQL.

## <a name="prerequisites"></a>Wymagania wstępne

* Wdrożone wystąpienie chmury Azure wiosennej
* Konto Azure Database for MySQL
* Interfejs wiersza polecenia platformy Azure

Jeśli nie masz wdrożonego wystąpienia chmury Azure wiosennej, wykonaj kroki opisane w tym [przewodniku szybki start](spring-cloud-quickstart-launch-app-portal.md) , aby wdrożyć swoją pierwszą aplikację w chmurze.

## <a name="bind-azure-database-for-mysql"></a>Azure Database for MySQL powiązania

1. Zanotuj nazwę użytkownika i hasło administratora konta usługi Azure MySQL. Nawiąż połączenie z serwerem i Utwórz bazę danych o nazwie `testdb` z klienta programu MySQL. Utwórz nowe konto inne niż administracyjne.

1. Dodaj następującą zależność do `pom.xml` projektu

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```
1. Usuń `spring.datasource.*` właściwości (jeśli istnieją) w pliku `application.properties`.

1. Zaktualizuj bieżące wdrożenie przy użyciu `az spring-cloud app update` lub Utwórz nowe wdrożenie dla tej zmiany przy użyciu `az spring-cloud app deployment create`.  Te polecenia spowodują aktualizację lub utworzenie aplikacji z nową zależnością.

1. Przejdź do strony usługi w chmurze ze sprężyną Azure w Azure Portal. Znajdź **pulpit nawigacyjny aplikacji** i wybierz aplikację, która ma zostać powiązana z usługą Azure MySQL.  Ta sama aplikacja została zaktualizowana lub wdrożona w poprzednim kroku. Następnie wybierz pozycję `Service binding` i wybierz przycisk `Create service binding`. Wypełnij formularz, upewnij się, że wybrano opcję **Typ powiązania** `Azure MySQL`, tę samą nazwę bazy danych, która była używana wcześniej, i tę samą nazwa użytkownika i hasło zanotowane w pierwszym kroku.

1. Uruchom ponownie aplikację, a to powiązanie powinno teraz funkcjonować.

1. Aby upewnić się, że powiązanie usługi jest prawidłowe, wybierz nazwę powiązania i sprawdź jej szczegóły. Pole `property` powinno wyglądać następująco:
    ```
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

## <a name="next-steps"></a>Następne kroki

W ramach tego samouczka nauczysz się powiązać aplikację w chmurze platformy Azure z bazą danych MySQL.  Aby dowiedzieć się więcej na temat zarządzania usługą Azure wiosennej w chmurze, Przeczytaj, aby dowiedzieć się więcej na temat odnajdywania i rejestracji usług.

> [!div class="nextstepaction"]
> [Dowiedz się, jak włączyć odnajdowanie usług i rejestracje przy użyciu rejestru wiosennej usługi w chmurze](spring-cloud-service-registration.md).

