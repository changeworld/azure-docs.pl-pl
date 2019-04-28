---
title: Azure Functions — omówienie | Microsoft Docs
description: Informacje na temat sposobu używania usługi Azure Functions do optymalizowania obciążeń asynchronicznych w ciągu minut.
services: functions
documentationcenter: na
author: mattchenderson
manager: jeconnoc
keywords: usługa Azure Functions, funkcje, przetwarzanie zdarzeń, elementy webhook, obliczanie dynamiczne, architektura bez serwera
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.service: azure-functions
ms.devlang: multiple
ms.topic: overview
ms.date: 10/03/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: b8d57a2bbaa53a0291dc9c05ab234c3238322a71
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61020285"
---
# <a name="an-introduction-to-azure-functions"></a>Wprowadzenie do usługi Azure Functions  
Azure Functions to rozwiązanie umożliwiające łatwe uruchamianie małych fragmentów kodu („funkcji”) w chmurze. Możesz napisać tylko kod rozwiązujący aktualny problem, nie martwiąc się o całą aplikację ani infrastrukturę do jej uruchomienia. Dzięki usłudze Functions programowanie może być jeszcze wydajniejsze i można korzystać z wybranego języka programowania, takiego jak C#, F#, Node.js, Java lub PHP. Płać tylko za czas działania kodu — platforma Azure jest skalowana zgodnie z potrzebami. Usługa Azure Functions pozwala tworzyć na platformie Microsoft Azure aplikacje [bezserwerowe](https://azure.microsoft.com/solutions/serverless/).

W tym temacie przedstawiono ogólne omówienie usługi Azure Functions. Jeśli chcesz od razu rozpocząć korzystanie z usługi Functions, skorzystaj z artykułu [Tworzenie pierwszej funkcji platformy Azure](functions-create-first-azure-function.md). Jeśli chcesz uzyskać informacje techniczne o usłudze Functions, zobacz [dokumentację dla deweloperów](functions-reference.md).

## <a name="features"></a>Funkcje
Oto główne funkcje usługi Functions:

* **Wybór języka** — pisz funkcje przy użyciu wybranego języka: C#, F # lub Javascript. Aby zapoznać się z innymi opcjami, zobacz [Obsługiwane języki](supported-languages.md).
* **Model cenowy płatności za użycie** — płać tylko za czas działania kodu. Zapoznaj się z opcjami planu hostingowego zużycia w [sekcji cennika](#pricing).  
* **Korzystaj z własnych zależności** — środowisko Functions obsługuje rozwiązania NuGet i NPM, dzięki czemu można używać ulubionych bibliotek.  
* **Zintegrowane zabezpieczenia** — ochrona funkcji wyzwalanych przez protokół HTTP za pośrednictwem dostawców uwierzytelniania OAuth, takich jak Azure Active Directory, Facebook, Google, Twitter i konto Microsoft.  
* **Uproszczona integracja** — łatwe korzystanie z usług platformy Azure i ofert oprogramowania jako usługi (SaaS). Kilka przykładów można znaleźć w [sekcji dotyczącej integracji](#integrations).  
* **Elastyczne programowanie** — kodowanie funkcji bezpośrednio w portalu lub konfigurowanie ciągłej integracji i wdrażanie kodu za pomocą usług [GitHub](../app-service/scripts/cli-continuous-deployment-github.md), [Azure DevOps Services](../app-service/scripts/cli-continuous-deployment-vsts.md) i innych [obsługiwanych narzędzi deweloperskich](../app-service/deploy-local-git.md).  
* **Open source** — środowisko uruchomieniowe Functions typu open source jest [dostępne w serwisie GitHub](https://github.com/azure/azure-webjobs-sdk-script).  

## <a name="what-can-i-do-with-functions"></a>Co można zrobić w środowisku Functions?
Środowisko Functions to doskonałe rozwiązanie do przetwarzania danych, integrowania systemów, pracy z Internetem rzeczy oraz tworzenia prostych interfejsów API i mikrousług. Usługa Functions może pomóc w wykonywaniu takich zadań, jak przetwarzanie zamówień lub obrazów, zarządzanie plikami lub wszelkie zadania, które mają być uruchamiane zgodnie z harmonogramem. 

Środowisko usługi Functions zawiera szablony ułatwiające rozpoczęcie pracy z kluczowymi scenariuszami, między innymi:

* **HTTPTrigger** — wyzwalanie wykonywania kodu za pomocą żądania HTTP. Aby zapoznać się z przykładem, zobacz [Tworzenie pierwszej funkcji](functions-create-first-azure-function.md).
* **TimerTrigger** — wykonywanie oczyszczania lub innych zadań wsadowych zgodnie ze wstępnie zdefiniowanym harmonogramem. Aby zapoznać się z przykładem, zobacz [Tworzenie funkcji wyzwalanej przez czasomierz](functions-create-scheduled-function.md).
* **CosmosDBTrigger** — przetwarzanie dokumentów usługi Azure Cosmos DB, gdy są one dodawane lub aktualizowane w kolekcjach w bazie danych NoSQL. Aby uzyskać więcej informacji, zobacz [Powiązania usługi Azure Cosmos DB](functions-bindings-cosmosdb-v2.md).
* **BlobTrigger** — przetwarzanie obiektów blob usługi Azure Storage dodawanych do kontenerów. Tej funkcji można użyć do zmiany rozmiaru obrazu. Więcej informacji zawiera artykuł [Powiązania magazynu Blob Storage](functions-bindings-storage-blob.md).
* **QueueTrigger** — odpowiadanie na komunikaty przychodzące w kolejce usługi Azure Storage. Aby uzyskać więcej informacji, zobacz [Powiązania usługi Azure Queue Storage](functions-bindings-storage-queue.md).
* **EventGridTrigger** — odpowiadanie na zdarzenia dostarczone do subskrypcji w usłudze Azure Event Grid. Obsługuje oparty na subskrypcji model odbierania zdarzeń, a w tym filtrowania. Jest dobrym rozwiązaniem w przypadku tworzenia architektur opartych na zdarzeniach. Aby zapoznać się z przykładem, zobacz [Automatyzowanie zmiany rozmiaru przekazanych obrazów za pomocą usługi Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md).
* **EventHubTrigger** — odpowiadanie na zdarzenia dostarczone do usługi Azure Event Hub. Opcja szczególnie przydatna podczas instrumentacji aplikacji, przetwarzania środowiska użytkownika lub przepływu pracy oraz pracy ze scenariuszami Internetu rzeczy (IoT). Aby uzyskać więcej informacji, zobacz [Powiązania usługi Event Hubs](functions-bindings-event-hubs.md).
* **ServiceBusQueueTrigger** — łączenie kodu z innymi usługami platformy Azure lub usługami lokalnymi przez nasłuchiwanie w kolejkach komunikatów. Aby uzyskać więcej informacji, zobacz [Powiązania usługi Service Bus](functions-bindings-service-bus.md).
* **ServiceBusTopicTrigger** — łączenie kodu z innymi usługami platformy Azure lub usługami lokalnymi przez subskrybowanie tematów. Aby uzyskać więcej informacji, zobacz [Powiązania usługi Service Bus](functions-bindings-service-bus.md).

Środowisko usługi Azure Functions obsługuje *wyzwalacze*, czyli metody uruchamiania wykonywania kodu, i *powiązania*, czyli metody upraszczania kodowania danych wejściowych i wyjściowych. Szczegółowy opis wyzwalaczy i powiązań oferowanych w środowisku Azure Functions można znaleźć w [odpowiedniej dokumentacji usługi Azure Functions dla deweloperów](functions-triggers-bindings.md).

## <a name="integrations"></a>Integracje
Usługę Azure Functions można integrować z różnymi usługami platformy Azure i firm zewnętrznych. Te usługi umożliwiają wyzwalanie funkcji i uruchamianie wykonywania. Mogą również służyć jako dane wejściowe i wyjściowe dla kodu. W środowisku usługi Azure Functions obsługiwane są poniższe integracje usług:

* Azure Cosmos DB
* Azure Event Hubs
* Azure Event Grid
* Azure Notification Hubs
* Azure Service Bus (kolejki i tematy)
* Azure Storage (obiekty blob, kolejki i tabele)
* Lokalne (za pomocą usługi Service Bus)
* Twilio (wiadomości SMS)

## <a name="pricing"></a>Ile kosztuje usługa Azure Functions?
Usługa Azure Functions oferuje dwa rodzaje planów cenowych. Wybierz ten, który najlepiej odpowiada Twoim potrzebom: 

* **Plan zużycia** — po uruchomieniu funkcji platforma Azure udostępnia wszystkie niezbędne zasoby obliczeniowe. Nie musisz martwić się o zarządzanie zasobami i płacisz tylko za czas działania kodu. 
* **Plan usługi App Service** — uruchamianie funkcji tak samo, jak aplikacji internetowych. Jeśli już używasz usługi App Service dla innych aplikacji, możesz uruchamiać swoje funkcje w ramach tego samego planu bez dodatkowych kosztów. 

Aby uzyskać więcej informacji o planach hostingu, zobacz [Porównanie planów hostingu usługi Azure Functions](functions-scale.md). Szczegółowe informacje są dostępne na [stronie cennika usługi Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="next-steps"></a>Następne kroki
* [Tworzenie pierwszej funkcji platformy Azure](functions-create-first-azure-function.md)  
  Od razu utwórz swoją pierwszą funkcję przy użyciu opcji szybkiego startu usługi Azure Functions. 
* [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)  
  Dalsze informacje techniczne na temat środowiska uruchomieniowego usługi Azure Functions, a także dokumentacja dotycząca kodowania funkcji oraz definiowania wyzwalaczy i powiązań.
* [Testowanie usługi Azure Functions](functions-test-a-function.md)  
  Opis różnych narzędzi i technik testowania funkcji.
* [Jak skalować usługę Azure Functions](functions-scale.md)  
  Omówienie planów usług dostępnych w środowisku Azure Functions, w tym planu hostingowego zużycia, oraz sposobu wybierania właściwego planu. 
* [Dowiedz się więcej o usłudze Azure App Service](../app-service/overview.md)  
  Środowisko Azure Functions korzysta z platformy Azure App Service na potrzeby funkcji podstawowych, takich jak wdrożenia, zmienne środowiskowe i diagnostyka. 

