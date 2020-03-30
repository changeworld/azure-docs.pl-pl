---
title: Azure Functions — omówienie
description: Informacje na temat sposobu używania usługi Azure Functions do optymalizowania obciążeń asynchronicznych w ciągu minut.
author: mattchenderson
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.topic: overview
ms.date: 01/16/2020
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: f0948cb58ba9403a34fbfd61ec43c29bed3440bc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77621002"
---
# <a name="an-introduction-to-azure-functions"></a>Wprowadzenie do usługi Azure Functions

Usługa Azure Functions umożliwia uruchamianie małych fragmentów kodu (nazywanych "funkcjami") bez martwienia się o infrastrukturę aplikacji. Dzięki usłudze Azure Functions infrastruktura chmury zapewnia wszystkie aktualne serwery potrzebne do utrzymania aplikacji na dużą skalę.

Funkcja jest "wyzwalana" przez określony typ zdarzenia. [Obsługiwane wyzwalacze](./functions-triggers-bindings.md) obejmują odpowiadanie na zmiany w danych, odpowiadanie na wiadomości, uruchamianie zgodnie z harmonogramem lub w wyniku żądania HTTP.

Chociaż zawsze można kod bezpośrednio względem mnóstwo usług, integracji z innymi usługami jest usprawniony przy użyciu powiązań. Powiązania zapewniają [deklaratywny dostęp do szerokiej gamy usług platformy Azure i innych firm.](./functions-triggers-bindings.md)

## <a name="features"></a>Funkcje

Niektóre kluczowe funkcje usługi Azure Functions obejmują:

- **Aplikacje bezserwerowe:** Funkcje umożliwiają tworzenie aplikacji [bezserwerowych](https://azure.microsoft.com/solutions/serverless/) na platformie Microsoft Azure.

- **Wybór języka:** Pisanie funkcji przy użyciu wyboru [C#, Java, JavaScript, Python i PowerShell](supported-languages.md).

- **Model cenowy płatności za użycie:** płać tylko za czas spędzony na uruchomieniu kodu. Zapoznaj się z opcjami planu hostingowego zużycia w [sekcji cennika](#pricing).  

- **Przynieś własne zależności:** Funkcje obsługuje NuGet i NPM, dając dostęp do ulubionych bibliotek.

- **Zintegrowane zabezpieczenia:** Chroń funkcje wyzwalane przez protokół HTTP za pomocą dostawców OAuth, takich jak Azure Active Directory, Facebook, Google, Twitter i Microsoft Account.

- **Uproszczona integracja:** Łatwo integruj się z usługami platformy Azure i ofertami oprogramowania jako usługi (SaaS).

- **Elastyczne opracowywanie:** Skonfiguruj ciągłą integrację i wdrażaj kod za pośrednictwem [usługi GitHub,](../app-service/scripts/cli-continuous-deployment-github.md) [usługi Azure DevOps](../app-service/scripts/cli-continuous-deployment-vsts.md)i innych [obsługiwanych narzędzi programistycznych.](../app-service/deploy-local-git.md)

- **Stateful serverless architecture**: Aranżuj aplikacje bezserwerowe z [funkcjami trwałymi](durable/durable-functions-overview.md).

- **Open-source**: Środowisko uruchomieniowe Funkcji jest open-source i [dostępne w usłudze GitHub](https://github.com/azure/azure-webjobs-sdk-script).

## <a name="what-can-i-do-with-functions"></a>Co można zrobić w środowisku Functions?

Funkcje to doskonałe rozwiązanie do przetwarzania danych zbiorczych, integrowania systemów, pracy z internetem rzeczy (IoT) oraz tworzenia prostych interfejsów API i mikro usług.

Seria szablonów jest dostępna na początek z kluczowymi scenariuszami, w tym:

- **HTTP**: Uruchom kod na podstawie [żądań HTTP](functions-create-first-azure-function.md)

- **Timer**: Planowanie [uruchamiania](./functions-create-scheduled-function.md) kodu w określonych godzinach

- **Usługa Azure Cosmos DB**: przetwarzanie [nowych i zmodyfikowanych dokumentów usługi Azure Cosmos DB](./functions-create-cosmos-db-triggered-function.md)

- **Magazyn obiektów Blob:** przetwarzanie [nowych i zmodyfikowanych obiektów blob usługi Azure Storage](./functions-create-storage-blob-triggered-function.md)

- **Magazyn kolejek:** odpowiadanie na [komunikaty kolejki usługi Azure Storage](./functions-create-storage-queue-triggered-function.md)

- **Siatka zdarzeń:** odpowiadaj na [zdarzenia usługi Azure Event Grid za pośrednictwem subskrypcji i filtrów](../event-grid/resize-images-on-storage-blob-upload-event.md)

- **Centrum zdarzeń:** odpowiadanie na [duże ilości zdarzeń usługi Azure Event Hub](./functions-bindings-event-hubs.md)

- **Kolejka usługi Service Bus:** łączenie się z innymi usługami platformy Azure lub usług lokalnych przez [odpowiadanie na komunikaty kolejki usługi Service Bus](./functions-bindings-service-bus.md)

- **Temat usługi Service Bus:** Łączenie innych usług platformy Azure lub usług lokalnych przez [odpowiadanie na komunikaty tematu usługi Service Bus](./functions-bindings-service-bus.md)

## <a name="how-much-does-functions-cost"></a><a name="pricing"></a>Ile kosztuje usługa Azure Functions?

Usługa Azure Functions ma trzy rodzaje planów cenowych. Wybierz ten, który najlepiej odpowiada Twoim potrzebom:

- **Plan zużycia:** Platforma Azure zapewnia wszystkie niezbędne zasoby obliczeniowe. Nie musisz się martwić o zarządzanie zasobami i płacić tylko za czas, przez który działa kod.

- **Plan premium:** Określ liczbę wstępnie rozgrzanych wystąpień, które są zawsze w trybie online i są gotowe do natychmiastowej odpowiedzi. Po uruchomieniu funkcji platforma Azure udostępnia dodatkowe zasoby obliczeniowe, które są potrzebne. Płacisz za wstępnie rozgrzane wystąpienia uruchomione w sposób ciągły i wszelkie dodatkowe wystąpienia używane jako platforma Azure skaluje aplikację i wyjmuje.

- **Plan usługi aplikacji:** uruchamiaj swoje funkcje, tak jak aplikacje internetowe. Jeśli używasz usługi App Service dla innych aplikacji, funkcje można uruchomić na tym samym planie bez dodatkowych kosztów.

Aby uzyskać więcej informacji o planach hostingu, zobacz [Porównanie planów hostingu usługi Azure Functions](functions-scale.md). Szczegółowe informacje są dostępne na [stronie cennika usługi Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="next-steps"></a>Następne kroki

- [Utwórz swoją pierwszą funkcję platformy Azure](functions-create-first-function-vs-code.md)  
  Wprowadzenie do [programu Visual Studio Code](functions-create-first-function-vs-code.md), [wiersza polecenia](functions-create-first-azure-function-azure-cli.md)lub użyj [witryny Azure portal](functions-create-first-azure-function.md).

- [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)  
  Dalsze informacje techniczne na temat środowiska uruchomieniowego usługi Azure Functions, a także dokumentacja dotycząca kodowania funkcji oraz definiowania wyzwalaczy i powiązań.

- [Jak skalować usługę Azure Functions](functions-scale.md)  
  Omówienie planów usług dostępnych w środowisku Azure Functions, w tym planu hostingowego zużycia, oraz sposobu wybierania właściwego planu.

- [Dowiedz się więcej o usłudze Azure App Service](../app-service/overview.md)  
  Środowisko Azure Functions korzysta z platformy Azure App Service na potrzeby funkcji podstawowych, takich jak wdrożenia, zmienne środowiskowe i diagnostyka.
