---
title: 'Samouczek: używanie rozproszonego śledzenia z chmurą Azure wiosennej | Microsoft Docs'
description: Dowiedz się, jak używać śledzenia rozproszonego chmury wiosennej za pomocą usługi Azure Application Insights
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/06/2019
ms.author: v-vasuke
ms.openlocfilehash: 95aae47bddffd102c5d6d6dac67f22e6777cc3e7
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038937"
---
# <a name="tutorial-using-distributed-tracing-with-azure-spring-cloud"></a>Samouczek: używanie rozproszonego śledzenia w chmurze Azure wiosennej

Rozproszone narzędzia do śledzenia w chmurze sprężynowe umożliwiają łatwe debugowanie i monitorowanie złożonych problemów. Chmura sprężynowa platformy Azure integruje [chmurę Sleuth](https://spring.io/projects/spring-cloud-sleuth) z usługą Azure [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) , aby zapewnić zaawansowane możliwości śledzenia rozproszonego z Azure Portal.

W tym artykule dowiesz się, jak:

> [!div class="checklist"]
> * Włącz śledzenie rozproszone w Azure Portal
> * Dodaj chmurę Sleuth do aplikacji
> * Wyświetlanie map zależności dla aplikacji mikrousług
> * Wyszukaj dane śledzenia przy użyciu różnych filtrów

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

* Została już zainicjowana i uruchomiona usługa w chmurze Azure wiosną.  Wykonaj [ten przewodnik Szybki Start](spring-cloud-quickstart-launch-app-cli.md) , aby zainicjować obsługę administracyjną usługi w chmurze Azure wiosną i uruchomić ją.
    
## <a name="add-dependencies"></a>Dodaj zależności

Zezwól nadawcy zipkin na wysyłanie do sieci Web, dodając następujący wiersz do pliku Application. Properties:

```xml
spring.zipkin.sender.type = web
```

Możesz pominąć następny krok, jeśli korzystasz z naszego [przewodnika po przygotowywanie aplikacji w chmurze platformy Azure](spring-cloud-tutorial-prepare-app-deployment.md). W przeciwnym razie przejdź do lokalnego środowiska deweloperskiego i edytuj plik `pom.xml`, aby uwzględnić zależność Sleuth chmury Wiosnowej:

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

* Skompiluj i Wdróż ponownie dla usługi w chmurze Azure wiosny, aby odzwierciedlić te zmiany. 

## <a name="modify-the-sample-rate"></a>Modyfikuj częstotliwość próbkowania
Możesz zmienić częstotliwość zbierania danych telemetrycznych, modyfikując częstotliwość próbkowania. Na przykład, jeśli chcesz próbkować połowę często, przejdź do pliku `application.properties` i zmień następujący wiersz:

```xml
spring.sleuth.sampler.probability=0.5
```

Jeśli masz już skompilowaną i wdrożoną aplikację, możesz zmodyfikować częstotliwość próbkowania, dodając powyższy wiersz jako zmienną środowiskową w interfejsie wiersza polecenia platformy Azure lub w portalu. 

## <a name="enable-application-insights"></a>Włączanie usługi Application Insights

1. Przejdź do strony usługi w chmurze ze sprężyną Azure w Azure Portal.
1. W sekcji monitorowanie wybierz opcję **śledzenie rozproszone**.
1. Wybierz pozycję **Edytuj ustawienie** , aby edytować lub dodać nowe ustawienie.
1. Utwórz nowe zapytanie usługi Application Insights lub Wybierz istniejące.
1. Wybierz kategorię rejestrowania, którą chcesz monitorować, i określ czas przechowywania (w dniach).
1. Wybierz pozycję **Zastosuj** , aby zastosować nowe śledzenie.

## <a name="view-application-map"></a>Wyświetl mapę aplikacji

Wróć do strony śledzenie rozproszone i wybierz pozycję **Wyświetl mapę aplikacji**. Przejrzyj wizualną reprezentację ustawień aplikacji i monitorowania. Aby dowiedzieć się, jak używać mapy aplikacji, zobacz [ten artykuł](https://docs.microsoft.com/azure/azure-monitor/app/app-map).

## <a name="search"></a>Search

Użyj funkcji Search, aby wykonać zapytanie dotyczące innych określonych elementów telemetrii. Na stronie **śledzenie rozproszone** wybierz pozycję **Wyszukaj**. Aby uzyskać więcej informacji na temat korzystania z funkcji wyszukiwania, zobacz [ten artykuł](https://docs.microsoft.com/azure/azure-monitor/app/diagnostic-search).

## <a name="application-insights-page"></a>Strona Application Insights

Application Insights udostępnia możliwości monitorowania oprócz mapy aplikacji i wyszukiwania. Wyszukaj w Azure Portal nazwę aplikacji, a następnie Uruchom stronę Application Insights, aby uzyskać więcej informacji. Aby uzyskać więcej wskazówek na temat korzystania z tych narzędzi, [zapoznaj się z dokumentacją](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).


## <a name="disable-application-insights"></a>Wyłącz Application Insights

1. Przejdź do strony usługi w chmurze ze sprężyną Azure w Azure Portal.
1. W sekcji monitorowanie kliknij pozycję **śledzenie rozproszone**.
1. Kliknij przycisk **Wyłącz** , aby wyłączyć Application Insights

## <a name="next-steps"></a>Następne kroki

W ramach tego samouczka nauczysz się, jak włączyć i zrozumieć rozproszone śledzenie w chmurze Azure wiosennej. Aby dowiedzieć się, jak powiązać aplikację z usługą Azure CosmosDB, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Dowiedz się, jak powiązać aplikację z usługą Azure CosmosDB](spring-cloud-tutorial-bind-cosmos.md).
