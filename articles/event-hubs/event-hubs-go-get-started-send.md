---
title: Wysyłanie zdarzeń do usługi Azure Event Hubs za pomocą języka Go | Dokumentacja firmy Microsoft
description: Rozpocznij wysyłanie zdarzeń do usługi Event Hubs za pomocą języka Go
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 10/18/2018
ms.author: shvija
ms.openlocfilehash: f5e30a103b09613caee8e9912a89a5bc2d390f65
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2018
ms.locfileid: "49458092"
---
# <a name="send-events-to-event-hubs-using-go"></a>Wysyłanie zdarzeń do usługi Event Hubs za pomocą języka Go

Azure Event Hubs to platforma do pozyskiwania i strumieniowego przesyłania danych, która umożliwia odbieranie i przetwarzanie milionów zdarzeń na sekundę. Usługa Event Hubs pozwala przetwarzać i przechowywać zdarzenia, dane lub dane telemetryczne generowane przez rozproszone oprogramowanie i urządzenia. Dane wysłane do centrum zdarzeń mogą zostać przekształcone i zmagazynowane przy użyciu dowolnego dostawcy analityki czasu rzeczywistego lub adapterów przetwarzania wsadowego/magazynowania. Aby uzyskać szczegółowe omówienie usługi Event hubs, zobacz [Przegląd usługi Event Hubs](event-hubs-about.md) i [funkcji usługi Event Hubs](event-hubs-features.md).

W tym samouczku opisano sposób wysyłania zdarzeń do Centrum zdarzeń z aplikacji napisanej za pomocą języka Go. 

> [!NOTE]
> Ten przewodnik Szybki Start można pobrać jako próbki z [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs), Zastąp `EventHubConnectionString` i `EventHubName` ciągi z własnymi wartościami Centrum zdarzeń i uruchomimy ją. Alternatywnie możesz wykonać kroki w tym samouczku, aby utworzyć własny.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne jest spełnienie następujących wymagań wstępnych:

* Przejdź zainstalowane lokalnie. Postępuj zgodnie z [w instrukcjach](https://golang.org/doc/install) w razie potrzeby.
* Istniejące usługi Event Hubs przestrzeni nazw i Centrum zdarzeń. Te jednostki można utworzyć, postępując zgodnie z instrukcjami wyświetlanymi w [w tym artykule](event-hubs-create.md).

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Tworzenie przestrzeni nazw usługi Event Hubs i centrum zdarzeń
Pierwszym krokiem jest skorzystanie z witryny [Azure Portal](https://portal.azure.com) w celu utworzenia przestrzeni nazw typu Event Hubs i uzyskania poświadczeń zarządzania wymaganych przez aplikację do komunikacji z centrum zdarzeń. Aby utworzyć obszar nazw i Centrum zdarzeń, wykonaj procedurę opisaną w [w tym artykule](event-hubs-create.md), a następnie wykonaj następujące czynności w ramach tego samouczka.

## <a name="install-go-package"></a>Zainstaluj pakiet dla języka Go

Pobierz pakiet dla języka Go dla usługi Event Hubs za pomocą `go get` lub `dep`. Na przykład:

```bash
go get -u github.com/Azure/azure-event-hubs-go
go get -u github.com/Azure/azure-amqp-common-go/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
```

## <a name="import-packages-in-your-code-file"></a>Zaimportuj pakiety w pliku kodu

Aby zaimportować pakiety z rzeczywistym użyciem, skorzystaj z następującego przykładu kodu:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
)
```

## <a name="create-service-principal"></a>Tworzenie jednostki usługi

Utwórz nową jednostkę usługi, postępując zgodnie z instrukcjami wyświetlanymi w [Tworzenie jednostki usługi platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 2.0](/cli/azure/create-an-azure-service-principal-azure-cli). Zapisz podanych poświadczeń w środowisku z następującymi nazwami. Zestaw Azure SDK dla języka Go i pakietów usługi Event Hubs są wstępnie skonfigurowane do wyszukania tych nazwy zmiennych:

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Teraz Utwórz dostawcę autoryzacji klienta usługi Event Hubs, która używa tych poświadczeń:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

## <a name="create-event-hubs-client"></a>Tworzenie klienta usługi Event Hubs

Poniższy kod tworzy klienta usługi Event Hubs:

```go
hub, err := eventhubs.NewHub("namespaceName", "hubName", tokenProvider)
ctx := context.WithTimeout(context.Background(), 10 * time.Second)
defer hub.Close(ctx)
if err != nil {
    log.Fatalf("failed to get hub %s\n", err)
}
```

## <a name="send-messages"></a>Wysyłanie komunikatów

W poniższym fragmencie kodu przy użyciu (1) do wysyłania wiadomości interaktywnie z poziomu terminalu lub (2) w celu wysyłania komunikatów w ramach programu:

```go
// 1. send messages at the terminal
ctx = context.Background()
reader := bufio.NewReader(os.Stdin)
for {
    fmt.Printf("Input a message to send: ")
    text, _ := reader.ReadString('\n')
    hub.Send(ctx, eventhubs.NewEventFromString(text))
}

// 2. send messages within program
ctx = context.Background()
hub.Send(ctx, eventhubs.NewEventFromString("hello Azure!")
```

## <a name="extras"></a>Dodatki

Pobierz identyfikatory partycji w Centrum zdarzeń:

```go
info, err := hub.GetRuntimeInformation(ctx)
if err != nil {
    log.Fatalf("failed to get runtime info: %s\n", err)
}
log.Printf("got partition IDs: %s\n, info.PartitionIDs)
```

Uruchom aplikację do wysyłania zdarzeń do Centrum zdarzeń. 

Gratulacje! Wysłano komunikaty do centrum zdarzeń.

## <a name="next-steps"></a>Kolejne kroki
W tym przewodniku Szybki Start zostały wysłane wiadomości do Centrum zdarzeń za pomocą języka Go. Aby dowiedzieć się, jak odbierać zdarzenia z Centrum zdarzeń za pomocą języka Go, zobacz [odbieranie zdarzeń z Centrum zdarzeń — z rzeczywistym użyciem](event-hubs-go-get-started-receive-eph.md).

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
