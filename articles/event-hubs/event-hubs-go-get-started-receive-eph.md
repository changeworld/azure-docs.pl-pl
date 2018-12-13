---
title: Odbieranie zdarzeń za pomocą języka Go — usługa Azure Event Hubs | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera wskazówki dotyczące tworzenia aplikacji języka Go, która odbiera zdarzenia z usługi Azure Event Hubs.
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: db952b82172928e42e951563d98bb32b275e8af7
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53084995"
---
# <a name="receive-events-from-event-hubs-using-go"></a>Odbieranie zdarzeń z usługi Event Hubs za pomocą języka Go

Azure Event Hubs to system zarządzania zdarzeń o wysokim stopniu skalowalności, który może obsługiwać miliony zdarzeń na sekundę, dzięki czemu aplikacje mogą przetwarzać oraz analizować duże ilości danych wytworzonych przez podłączone urządzenia i innymi systemami. Po zebraniu danych do Centrum zdarzeń, może odbierać i obsługa zdarzeń z użyciem w trakcie obsługi lub funkcji przekazywania z innymi systemami analizy.

Aby dowiedzieć się więcej na temat usługi Event Hubs, zobacz [Przegląd usługi Event Hubs][Event Hubs overview].

W tym samouczku opisano sposób odbieranie zdarzeń z Centrum zdarzeń w aplikacji z rzeczywistym użyciem. Aby dowiedzieć się, jak wysyłać zdarzenia zobacz [do odpowiedniego artykułu wysyłania](event-hubs-go-get-started-send.md).

Kod w tym samouczku pochodzi z [tych przykładów usługi GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs), który można sprawdzić, aby wyświetlić pełną pracy, w tym aplikacji zaimportuj instrukcje i deklaracje zmiennych.

Dostępne są inne przykłady [w zdarzeniu koncentratory pakietu repozytorium](https://github.com/Azure/azure-event-hubs-go/tree/master/_examples).

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka będą potrzebne następujące wymagania wstępne:

* Przejdź zainstalowane lokalnie. Postępuj zgodnie z [w instrukcjach](https://golang.org/doc/install) w razie potrzeby.
* Aktywne konto platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto][].
* Aby odbierać komunikaty, musi istnieć wiadomości w docelowym Centrum zdarzeń. Dowiedz się, jak wysyłać komunikaty [Wyślij samouczek](event-hubs-go-get-started-send.md).
* Istniejącym Centrum zdarzeń (zobacz następującą sekcję).
* Istniejące konto magazynu i kontener (patrz sekcja po następnej sekcji).

### <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

Ten samouczek rozpoczyna się od istniejącej usługi Event Hubs przestrzeni nazw i Centrum zdarzeń. Te jednostki można utworzyć, postępując zgodnie z instrukcjami wyświetlanymi w [w tym artykule](event-hubs-create.md).

### <a name="create-a-storage-account-and-container"></a>Utwórz konto magazynu i kontener

Stan, takich jak dzierżawy na partycje i punkty kontrolne w zdarzeniu stream są wspólne dla odbiorców za pomocą kontenera usługi Azure Storage. Możesz utworzyć konto magazynu i kontener za pomocą zestawu SDK Przejdź, jednak można również utworzyć jeden, postępując zgodnie z instrukcjami w [kontach magazynu Azure o](../storage/common/storage-create-storage-account.md).

Przykłady do tworzenia artefaktów magazynu za pomocą zestaw SDK języka Go są dostępne w [Go przykłady repozytorium](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage) i w próbce odpowiadający tego samouczka.

## <a name="receive-messages"></a>Odbieranie komunikatów

Aby odbierać komunikaty, Pobierz pakiety z rzeczywistym użyciem dla usługi Event Hubs za pomocą `go get` lub `dep`:

```bash
go get -u github.com/Azure/azure-event-hubs-go/...
go get -u github.com/Azure/azure-amqp-common-go/...
go get -u github.com/Azure/go-autorest/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
dep ensure -add github.com/Azure/go-autorest
```

## <a name="import-packages-in-your-code-file"></a>Zaimportuj pakiety w pliku kodu

Aby zaimportować pakiety z rzeczywistym użyciem, skorzystaj z następującego przykładu kodu:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
    eph "github.com/Azure/azure-event-hubs-go/eph"
    storageLeaser "github.com/Azure/azure-event-hubs-go/storage"
    azure "github.com/Azure/go-autorest/autorest/azure"
)
```

## <a name="create-service-principal"></a>Tworzenie jednostki usługi

Utwórz nową jednostkę usługi, postępując zgodnie z instrukcjami wyświetlanymi w [Tworzenie jednostki usługi platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 2.0](/cli/azure/create-an-azure-service-principal-azure-cli). Zapisz podanych poświadczeń w środowisku o następujących nazwach: zarówno zestaw Azure SDK dla języka Go i usługi Event Hubs pakietu są wstępnie skonfigurowane, aby wyszukać te nazwy zmiennych.

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Następnie Utwórz dostawcę autoryzacji klienta usługi Event Hubs, która używa tych poświadczeń:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

## <a name="get-metadata-struct"></a>Pobierz metadane struktury

Uzyskaj struktury z metadanymi o środowisku Azure za pomocą Przejdź zestawu SDK usługi Azure. Późniejszych operacji Użyj tej struktury, aby znaleźć poprawne punkty końcowe.

```go
azureEnv, err := azure.EnvironmentFromName("AzurePublicCloud")
if err != nil {
    log.Fatalf("could not get azure.Environment struct: %s\n", err)
}
```

## <a name="create-credential-helper"></a>Tworzenie poświadczeń pomocnika 

Utwórz pomocnika poświadczeń, który korzysta z poprzednich poświadczeń usługi Azure Active Directory (AAD) do tworzenia poświadczeń sygnatury dostępu współdzielonego (SAS) dla magazynu. Ostatni parametr poinformuje tego konstruktora, aby użyć tego samego zmiennych środowiskowych, jak używane wcześniej:

```go
cred, err := storageLeaser.NewAADSASCredential(
    subscriptionID,
    resourceGroupName,
    storageAccountName,
    storageContainerName,
    storageLeaser.AADSASCredentialWithEnvironmentVars())
if err != nil {
    log.Fatalf("could not prepare a storage credential: %s\n", err)
}
```

## <a name="create-checkpointer-and-leaser"></a>Tworzenie Checkpointer i Leaser 

Tworzenie **Leaser**, jest odpowiedzialny za dzierżawę partycji do określonego odbiorcy, a **Checkpointer**, odpowiedzialne za zapisywanie punktów kontrolnych dla strumienia komunikatów, tak aby mogli rozpocząć innym odbiornikom Odczytywanie z poprawne przesunięcie.

Obecnie pojedynczej **StorageLeaserCheckpointer** jest dostępna, używa tego samego kontenera magazynu do zarządzania zarówno dzierżawy, jak i punkty kontrolne. Oprócz nazwy konta i kontenera magazynu **StorageLeaserCheckpointer** wymaga poświadczeń, utworzony w poprzednim kroku, a struktura środowiska platformy Azure poprawnie dostępu do kontenera.

```go
leaserCheckpointer, err := storageLeaser.NewStorageLeaserCheckpointer(
    cred,
    storageAccountName,
    storageContainerName,
    azureEnv)
if err != nil {
    log.Fatalf("could not prepare a storage leaserCheckpointer: %s\n", err)
}
```

## <a name="construct-event-processor-host"></a>Konstruowania hosta procesora zdarzeń

Masz teraz elementy potrzebne do utworzenia EventProcessorHost w następujący sposób. Taki sam **StorageLeaserCheckpointer** służy jako Leaser i Checkpointer, zgodnie z opisem w poprzedniej sekcji:

```go
ctx := context.Background()
p, err := eph.New(
    ctx,
    nsName,
    hubName,
    tokenProvider,
    leaserCheckpointer,
    leaserCheckpointer)
if err != nil {
    log.Fatalf("failed to create EPH: %s\n", err)
}
defer p.Close(context.Background())
```

## <a name="create-handler"></a>Utwórz procedurę obsługi 

Teraz Utwórz procedurę obsługi i zarejestrowanie go za pomocą hosta procesora zdarzeń. Po uruchomieniu hosta, dotyczy to i inne programy na określonym obsługi komunikatów przychodzących:

```go
handler := func(ctx context.Context, event *eventhubs.Event) error {
    fmt.Printf("received: %s\n", string(event.Data))
    return nil
}

// register the handler with the EPH
_, err := p.RegisterHandler(ctx, handler)
if err != nil {
    log.Fatalf("failed to register handler: %s\n", err)
}
```

## <a name="receive-messages"></a>Odbieranie komunikatów

Wszystko, konfigurowania, można uruchomić hosta procesora zdarzeń za pomocą `Start(context)` do zapewnienia jego trwale pracę lub za pomocą `StartNonBlocking(context)` do uruchamiania tylko tak długo, jak wiadomości są dostępne.

Ten samouczek rozpoczyna się i działa w następujący sposób: zobacz przykładową GitHub, aby uzyskać przykłady dotyczące używania `StartNonBlocking`:

```go
ctx := context.Background()
err = p.Start()
if err != nil {
    log.Fatalf("failed to start EPH: %s\n", err)
}
```

## <a name="notes"></a>Uwagi

Instrukcje w tym samouczku obejmują użycie pojedynczego wystąpienia klasy **EventProcessorHost**. Aby zwiększyć przepustowość i niezawodność, należy uruchomić wiele wystąpień **EventProcessorHost** w różnych systemach. Leaser, które system zapewnia, że tylko jeden odbiornik jest skojarzony z i odbiera komunikaty z określonej partycji o określonej godzinie.

## <a name="next-steps"></a>Kolejne kroki
W tym przewodniku Szybki Start utworzono aplikację języka Go, która Odebrano komunikaty z Centrum zdarzeń. Aby dowiedzieć się więcej o sposobie wysyłania zdarzeń do Centrum zdarzeń za pomocą języka Go, zobacz [wysyłać zdarzenia z Centrum zdarzeń — z rzeczywistym użyciem](event-hubs-go-get-started-send.md).

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[bezpłatne konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
