---
title: Wysyłanie zdarzeń za pomocą języka Go — usługa Azure Event Hubs | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera wskazówki dotyczące tworzenia aplikacji języka Go, która wysyła zdarzenia z usługi Azure Event Hubs.
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 44cbea8cc40f83ed5fdd2863da0cb0a01e8dc2b8
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53091064"
---
# <a name="send-events-to-event-hubs-using-go"></a>Wysyłanie zdarzeń do usługi Event Hubs za pomocą języka Go

Azure Event Hubs to platforma do pozyskiwania i strumieniowego przesyłania danych, która umożliwia odbieranie i przetwarzanie milionów zdarzeń na sekundę. Usługa Event Hubs pozwala przetwarzać i przechowywać zdarzenia, dane lub dane telemetryczne generowane przez rozproszone oprogramowanie i urządzenia. Dane wysłane do centrum zdarzeń mogą zostać przekształcone i zmagazynowane przy użyciu dowolnego dostawcy analityki czasu rzeczywistego lub adapterów przetwarzania wsadowego/magazynowania. Aby zapoznać się ze szczegółowym omówieniem usługi Event Hubs, zobacz [Omówienie usługi Event Hubs](event-hubs-about.md) i [Funkcje usługi Event Hubs](event-hubs-features.md).

W tym samouczku opisano sposób wysyłania zdarzeń do Centrum zdarzeń z aplikacji napisanej za pomocą języka Go. 

> [!NOTE]
> Ten przewodnik Szybki start możesz pobrać jako przykład z witryny [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs), zastąpić ciągi `EventHubConnectionString` i `EventHubName` wartościami swojego centrum zdarzeń, a następnie uruchomić go. Alternatywnie możesz utworzyć własne rozwiązanie, wykonując kroki opisane w tym samouczku.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne jest spełnienie następujących wymagań wstępnych:

* Przejdź zainstalowane lokalnie. Postępuj zgodnie z [w instrukcjach](https://golang.org/doc/install) w razie potrzeby.

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Tworzenie przestrzeni nazw usługi Event Hubs i centrum zdarzeń
Pierwszym krokiem jest skorzystanie z witryny [Azure Portal](https://portal.azure.com) w celu utworzenia przestrzeni nazw typu Event Hubs i uzyskania poświadczeń zarządzania wymaganych przez aplikację do komunikacji z centrum zdarzeń. Aby utworzyć obszar nazw i Centrum zdarzeń, wykonaj procedurę opisaną w [w tym artykule](event-hubs-create.md).

Teraz przejdź do następujących czynności w ramach tego samouczka.

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
