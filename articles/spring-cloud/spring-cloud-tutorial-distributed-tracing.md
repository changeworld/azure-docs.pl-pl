---
title: Samouczek — używanie śledzenia rozproszonego w chmurze Azure Spring Cloud
description: W tym samouczku pokazano, jak korzystać z funkcji śledzenia rozproszonego usługi Spring Cloud za pośrednictwem usługi Azure Application Insights
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: brendm
ms.openlocfilehash: 0815aa084462d1b829d64cd7c5d6fa7cebf534fc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78273209"
---
# <a name="use-distributed-tracing-with-azure-spring-cloud"></a>Korzystanie z śledzenia rozproszonego w usłudze Azure Spring Cloud

Dzięki rozproszonym narzędziom do śledzenia w usłudze Azure Spring Cloud można łatwo debugować i monitorować złożone problemy. Usługa Azure Spring Cloud integruje [spring cloud sleuth](https://spring.io/projects/spring-cloud-sleuth) z usługą Azure [Application Insights.](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) Ta integracja zapewnia zaawansowane możliwości śledzenia rozproszonego z witryny Azure portal.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Włącz śledzenie rozproszone w witrynie Azure portal.
> * Dodaj Spring Cloud Sleuth do swojej aplikacji.
> * Wyświetlanie map zależności dla aplikacji mikrousług.
> * Wyszukiwanie danych śledzenia za pomocą różnych filtrów.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, potrzebujesz usługi Azure Spring Cloud, która jest już aprowizowana i uruchomiona. Ukończ [przewodnik Szybki start podczas wdrażania aplikacji za pośrednictwem interfejsu wiersza polecenia platformy Azure,](spring-cloud-quickstart-launch-app-cli.md) aby aprowizować i uruchamiać usługę Azure Spring Cloud.
    
## <a name="add-dependencies"></a>Dodawanie zależności

1. Dodaj następujący wiersz do pliku application.properties:

   ```xml
   spring.zipkin.sender.type = web
   ```

   Po tej zmianie nadawca Zipkin może wysłać go do sieci.

1. Pomiń ten krok, jeśli postępowałeś zgodnie z naszym [przewodnikiem po przygotowaniu aplikacji usługi Azure Spring Cloud.](spring-cloud-tutorial-prepare-app-deployment.md) W przeciwnym razie przejdź do lokalnego środowiska programistycznego i edytuj plik pom.xml, aby uwzględnić następującą zależność Spring Cloud Sleuth:

    ```xml
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-sleuth</artifactId>
                <version>${spring-cloud-sleuth.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-sleuth</artifactId>
        </dependency>
    </dependencies>
    ```

1. Skompiluj i wdrażaj ponownie dla usługi Azure Spring Cloud, aby odzwierciedlić te zmiany.

## <a name="modify-the-sample-rate"></a>Modyfikowanie częstotliwości próbkowania

Można zmienić szybkość, z jaką dane telemetryczne są zbierane, modyfikując częstotliwość próbkowania. Na przykład, jeśli chcesz próbkować połowę tak często, otwórz plik application.properties i zmień następujący wiersz:

```xml
spring.sleuth.sampler.probability=0.5
```

Jeśli aplikacja została już skupilona i wdrożona, można zmodyfikować częstotliwość próbkowania. Zrób to, dodając poprzedni wiersz jako zmienną środowiskową w interfejsu wiersza polecenia platformy Azure lub witrynie Azure portal.

## <a name="enable-application-insights"></a>Włączanie usługi Application Insights

1. Przejdź do strony usługi Azure Spring Cloud w witrynie Azure portal.
1. Na stronie **Monitorowanie** wybierz pozycję **Śledzenie rozproszone**.
1. Wybierz **pozycję Edytuj ustawienie,** aby edytować lub dodać nowe ustawienie.
1. Utwórz nową kwerendę usługi Application Insights lub wybierz istniejącą kwerendę.
1. Wybierz kategorię rejestrowania, którą chcesz monitorować, i określ czas przechowywania w dniach.
1. Wybierz **zastosuj,** aby zastosować nowe śledzenie.

## <a name="view-the-application-map"></a>Wyświetlanie mapy aplikacji

Wróć do strony **Śledzenie rozproszone** i wybierz pozycję **Wyświetl mapę aplikacji**. Przejrzyj wizualną reprezentację aplikacji i ustawienia monitorowania. Aby dowiedzieć się, jak korzystać z mapy aplikacji, zobacz [Mapa aplikacji: Klasyfikowanie aplikacji rozproszonych](https://docs.microsoft.com/azure/azure-monitor/app/app-map).

## <a name="use-search"></a>Używanie wyszukiwania

Funkcja wyszukiwania służy do wykonywania zapytań dotyczących innych określonych elementów telemetrycznych. Na stronie **Śledzenie rozproszone** wybierz pozycję **Wyszukaj**. Aby uzyskać więcej informacji na temat korzystania z funkcji wyszukiwania, zobacz [Korzystanie z wyszukiwania w usłudze Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/diagnostic-search).

## <a name="use-application-insights"></a>Korzystanie z usługi Application Insights

Usługa Application Insights zapewnia możliwości monitorowania oprócz mapy aplikacji i funkcji wyszukiwania. Przeszukaj nazwę aplikacji w witrynie Azure Portal, a następnie otwórz stronę usługi Application Insights, aby znaleźć informacje o monitorowaniu. Aby uzyskać więcej wskazówek dotyczących korzystania z tych narzędzi, zapoznaj się z [zapytaniami dziennika usługi Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language)

## <a name="disable-application-insights"></a>Wyłączanie statystyk aplikacji

1. Przejdź do strony usługi Azure Spring Cloud w witrynie Azure portal.
1. W **obszarze Monitorowanie**wybierz pozycję Śledzenie **rozproszone**.
1. Wybierz **opcję Wyłącz,** aby wyłączyć wgląd w aplikacje.

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak włączyć i zrozumieć śledzenie rozproszone w usłudze Azure Spring Cloud. Aby dowiedzieć się, jak powiązać aplikację z bazą danych usługi Azure Cosmos DB, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Dowiedz się, jak powiązać z bazą danych usługi Azure Cosmos DB](spring-cloud-tutorial-bind-cosmos.md)
