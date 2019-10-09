---
title: Jak powiązać Azure Database for MySQL ze swoją aplikacją w chmurze Azure wiosną | Microsoft Docs
description: W tym artykule opisano sposób powiązania usługi Azure MySQL z Twoją aplikacją w chmurze Azure wiosennej
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: e2add139f5cfd8299ec809793dd822b051d0f542
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72039021"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-database-for-mysql"></a>Samouczek: powiązywanie usług platformy Azure z Twoją aplikacją w chmurze Azure wiosną: Azure Database for MySQL

Chmura sprężynowa platformy Azure umożliwia automatyczne powiązanie wybranych usług platformy Azure z aplikacjami zamiast ręcznego konfigurowania aplikacji do rozruchu ze sprężyną. W tym samouczku przedstawiono sposób powiązania aplikacji z usługą Azure MySQL.

## <a name="prerequisites"></a>Wymagania wstępne

* Wdrożone wystąpienie chmury Azure wiosennej
* Konto Azure Database for MySQL
* Interfejs wiersza polecenia platformy Azure

W razie potrzeby zainstaluj rozszerzenie Cloud wiosny Azure dla interfejsu wiersza polecenia platformy Azure przy użyciu następującego polecenia:

```azurecli
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

>[!TIP]
> Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule.  Zawiera ona popularne narzędzia platformy Azure preinstalowane, w tym najnowsze wersje usług git, JDK, Maven i interfejsu wiersza polecenia platformy Azure. Jeśli logujesz się do subskrypcji platformy Azure, uruchom [Azure Cloud Shell](https://shell.azure.com) z Shell.Azure.com.  Więcej informacji na temat Azure Cloud Shell można znaleźć w [dokumentacji](../cloud-shell/overview.md) .

## <a name="bind-azure-database-for-mysql"></a>Azure Database for MySQL powiązania

1. Zanotuj nazwę użytkownika i hasło administratora konta usługi Azure MySQL. Nawiąż połączenie z serwerem i Utwórz bazę danych o nazwie `testdb` z klienta programu MySQL. Utwórz nowe konto inne niż administracyjne.

1. Dodaj następującą zależność w @no__t projektu — 0

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```
1. Usuń `spring.datasource.*` właściwości (jeśli istnieją) w pliku `application.properties`.

1. Zaktualizuj bieżące wdrożenie przy użyciu `az spring-cloud app update` lub Utwórz nowe wdrożenie dla tej zmiany przy użyciu `az spring-cloud app deployment create`.  Te polecenia spowodują aktualizację lub utworzenie aplikacji z nową zależnością.

1. Przejdź do strony usługi w chmurze ze sprężyną Azure w Azure Portal. Znajdź **pulpit nawigacyjny aplikacji** i wybierz aplikację, która ma zostać powiązana z usługą Azure MySQL.  Ta sama aplikacja została zaktualizowana lub wdrożona w poprzednim kroku. Następnie wybierz pozycję `Service binding` i wybierz przycisk `Create service binding`. Wypełnij formularz, pamiętając o wybraniu **typu powiązania** `Azure MySQL`, tej samej nazwie bazy danych, która była używana wcześniej, oraz tej samej nazwy użytkownika i hasła zanotowanym w pierwszym kroku.

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

