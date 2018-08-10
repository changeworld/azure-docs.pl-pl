---
title: Wysyłanie zdarzeń do usługi Azure Event Hubs za pomocą języka Go | Dokumentacja firmy Microsoft
description: Rozpocznij wysyłanie zdarzeń do usługi Event Hubs za pomocą języka Go
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 07/23/2018
ms.author: shvija
ms.openlocfilehash: 40b3aa82c3e9e8ab9a30362c0a41998877655725
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005601"
---
# <a name="send-events-to-event-hubs-using-go"></a>Wysyłanie zdarzeń do usługi Event Hubs za pomocą języka Go

Azure Event Hubs to system zarządzania zdarzeń o wysokim stopniu skalowalności, który może obsługiwać miliony zdarzeń na sekundę, dzięki czemu aplikacje mogą przetwarzać oraz analizować duże ilości danych wytworzonych przez podłączone urządzenia i innymi systemami. Po zebraniu danych do Centrum zdarzeń, może odbierać i obsługa zdarzeń z użyciem w trakcie obsługi lub funkcji przekazywania z innymi systemami analizy.

Aby dowiedzieć się więcej na temat usługi Event Hubs, zobacz [Przegląd usługi Event Hubs][Event Hubs overview].

W tym samouczku opisano sposób wysyłania zdarzeń do Centrum zdarzeń z aplikacji napisanej za pomocą języka Go. Aby odbierać zdarzenia, należy użyć **Przejdź eph** pakietu (hosta procesora zdarzeń), zgodnie z opisem w [do odpowiedniego artykułu Receive](event-hubs-go-get-started-receive-eph.md).

Kod w tym samouczku pochodzi z [tych przykładów usługi GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs), który można sprawdzić, aby wyświetlić pełną działającą aplikację, w tym instrukcje importowania i deklaracje zmiennych.

Dostępne są inne przykłady [w zdarzeniu koncentratory pakietu repozytorium](https://github.com/Azure/azure-event-hubs-go/tree/master/_examples).

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne jest spełnienie następujących wymagań wstępnych:

* Przejdź zainstalowane lokalnie. Postępuj zgodnie z [w instrukcjach](https://golang.org/doc/install) w razie potrzeby.
* Aktywne konto platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto][].
* Istniejące usługi Event Hubs przestrzeni nazw i Centrum zdarzeń. Te jednostki można utworzyć, postępując zgodnie z instrukcjami wyświetlanymi w [w tym artykule](event-hubs-create.md).

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

## <a name="next-steps"></a>Kolejne kroki

Odwiedź następujące strony, aby dowiedzieć się więcej o usłudze Event Hubs:

* [Odbieranie zdarzeń za pomocą klasy EventProcessorHost](event-hubs-go-get-started-receive-eph.md)
* [Przegląd usługi Event Hubs][Event Hubs overview]
* [Tworzenie centrum zdarzeń](event-hubs-create.md)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[bezpłatne konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
