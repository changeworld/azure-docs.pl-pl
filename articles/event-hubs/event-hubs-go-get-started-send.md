---
title: Wysyłanie i odbieranie zdarzeń za pomocą języka Go — usługa Azure Event Hubs | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera wskazówki dotyczące tworzenia aplikacji języka Go, która wysyła zdarzenia z usługi Azure Event Hubs.
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: shvija
ms.openlocfilehash: 823ebc985c77785f8b48d12d5919dbbd1b2b1459
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821684"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-using-go"></a>Wysyłanie zdarzeń do lub odbieranie zdarzeń z usługi Event Hubs za pomocą języka Go
Azure Event Hubs to platforma do pozyskiwania i strumieniowego przesyłania danych, która umożliwia odbieranie i przetwarzanie milionów zdarzeń na sekundę. Usługa Event Hubs pozwala przetwarzać i przechowywać zdarzenia, dane lub dane telemetryczne generowane przez rozproszone oprogramowanie i urządzenia. Dane wysłane do centrum zdarzeń mogą zostać przekształcone i zmagazynowane przy użyciu dowolnego dostawcy analityki czasu rzeczywistego lub adapterów przetwarzania wsadowego/magazynowania. Aby zapoznać się ze szczegółowym omówieniem usługi Event Hubs, zobacz [Omówienie usługi Event Hubs](event-hubs-about.md) i [Funkcje usługi Event Hubs](event-hubs-features.md).

W tym samouczku opisano sposób pisania aplikacji przejdź do wysyłania zdarzeń do lub odbieranie zdarzeń z Centrum zdarzeń. 

> [!NOTE]
> Ten przewodnik Szybki start możesz pobrać jako przykład z witryny [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs), zastąpić ciągi `EventHubConnectionString` i `EventHubName` wartościami swojego centrum zdarzeń, a następnie uruchomić go. Alternatywnie możesz utworzyć własne rozwiązanie, wykonując kroki opisane w tym samouczku.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne jest spełnienie następujących wymagań wstępnych:

- Przejdź zainstalowane lokalnie. Postępuj zgodnie z [w instrukcjach](https://golang.org/doc/install) w razie potrzeby.
- Aktywne konto platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto][].
- **Tworzenie przestrzeni nazw usługi Event Hubs i Centrum zdarzeń**. Użyj [witryny Azure portal](https://portal.azure.com) do utworzenia przestrzeni nazw typu Event Hubs i uzyskania poświadczeń zarządzania wymaganych przez aplikację do komunikacji z Centrum zdarzeń. Aby utworzyć przestrzeń nazw i centrum zdarzeń, wykonaj procedurę opisaną w [tym artykule](event-hubs-create.md).

## <a name="send-events"></a>Wysyłanie zdarzeń
W tej sekcji pokazano, jak utworzyć aplikację języka Go do wysyłania zdarzeń do Centrum zdarzeń. 

### <a name="install-go-package"></a>Zainstaluj pakiet dla języka Go

Pobierz pakiet dla języka Go dla usługi Event Hubs za pomocą `go get` lub `dep`. Na przykład:

```bash
go get -u github.com/Azure/azure-event-hubs-go
go get -u github.com/Azure/azure-amqp-common-go/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
```

### <a name="import-packages-in-your-code-file"></a>Zaimportuj pakiety w pliku kodu

Aby zaimportować pakiety z rzeczywistym użyciem, skorzystaj z następującego przykładu kodu:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
)
```

### <a name="create-service-principal"></a>Tworzenie jednostki usługi

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

### <a name="create-event-hubs-client"></a>Tworzenie klienta usługi Event Hubs

Poniższy kod tworzy klienta usługi Event Hubs:

```go
hub, err := eventhubs.NewHub("namespaceName", "hubName", tokenProvider)
ctx := context.WithTimeout(context.Background(), 10 * time.Second)
defer hub.Close(ctx)
if err != nil {
    log.Fatalf("failed to get hub %s\n", err)
}
```

### <a name="write-code-to-send-messages"></a>Pisanie kodu w celu wysyłania komunikatów

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
hub.Send(ctx, eventhubs.NewEventFromString("hello Azure!"))
```

### <a name="extras"></a>Dodatki

Pobierz identyfikatory partycji w Centrum zdarzeń:

```go
info, err := hub.GetRuntimeInformation(ctx)
if err != nil {
    log.Fatalf("failed to get runtime info: %s\n", err)
}
log.Printf("got partition IDs: %s\n", info.PartitionIDs)
```

Uruchom aplikację do wysyłania zdarzeń do Centrum zdarzeń. 

Gratulacje! Wysłano komunikaty do centrum zdarzeń.

## <a name="receive-events"></a>Odbieranie zdarzeń

### <a name="create-a-storage-account-and-container"></a>Utwórz konto magazynu i kontener

Stan, takich jak dzierżawy na partycje i punkty kontrolne w zdarzeniu stream są wspólne dla odbiorców za pomocą kontenera usługi Azure Storage. Możesz utworzyć konto magazynu i kontener za pomocą zestawu SDK Przejdź, jednak można również utworzyć jeden, postępując zgodnie z instrukcjami w [kontach magazynu Azure o](../storage/common/storage-create-storage-account.md).

Przykłady do tworzenia artefaktów magazynu za pomocą zestaw SDK języka Go są dostępne w [Go przykłady repozytorium](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage) i w próbce odpowiadający tego samouczka.

### <a name="go-packages"></a>Przejdź pakietów

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

### <a name="import-packages-in-your-code-file"></a>Zaimportuj pakiety w pliku kodu

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

### <a name="create-service-principal"></a>Tworzenie jednostki usługi

Utwórz nową jednostkę usługi, postępując zgodnie z instrukcjami wyświetlanymi w [Tworzenie jednostki usługi platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 2.0](/cli/azure/create-an-azure-service-principal-azure-cli). Zapisz podanych poświadczeń w środowisku o następujących nazwach: Zarówno zestaw Azure SDK dla języka Go i usługi Event Hubs pakietu są wstępnie skonfigurowane, aby wyszukać te nazwy zmiennych.

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

### <a name="get-metadata-struct"></a>Pobierz metadane struktury

Uzyskaj struktury z metadanymi o środowisku Azure za pomocą Przejdź zestawu SDK usługi Azure. Późniejszych operacji Użyj tej struktury, aby znaleźć poprawne punkty końcowe.

```go
azureEnv, err := azure.EnvironmentFromName("AzurePublicCloud")
if err != nil {
    log.Fatalf("could not get azure.Environment struct: %s\n", err)
}
```

### <a name="create-credential-helper"></a>Tworzenie poświadczeń pomocnika 

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

### <a name="create-a-check-pointer-and-a-leaser"></a>Utwórz wskaźnik wyboru i leaser 

Tworzenie **leaser**, jest odpowiedzialny za dzierżawę partycji do określonego odbiorcy, a **Sprawdź wskaźnik**, odpowiedzialne za zapisywanie punktów kontrolnych dla strumienia komunikatów, tak aby mogli rozpocząć innym odbiornikom Odczytywanie z poprawne przesunięcie.

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

### <a name="construct-event-processor-host"></a>Konstruowania hosta procesora zdarzeń

Masz teraz elementy potrzebne do utworzenia EventProcessorHost w następujący sposób. Taki sam **StorageLeaserCheckpointer** służy jako leaser i wyboru wskaźnika, zgodnie z opisem w poprzedniej sekcji:

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

### <a name="create-handler"></a>Utwórz procedurę obsługi 

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

### <a name="write-code-to-receive-messages"></a>Pisanie kodu w celu odbierania komunikatów

Wszystko, konfigurowania, można uruchomić hosta procesora zdarzeń za pomocą `Start(context)` do zapewnienia jego trwale pracę lub za pomocą `StartNonBlocking(context)` do uruchamiania tylko tak długo, jak wiadomości są dostępne.

Ten samouczek rozpoczyna się i działa w następujący sposób: zobacz przykładową GitHub, aby uzyskać przykłady dotyczące używania `StartNonBlocking`:

```go
ctx := context.Background()
err = p.Start()
if err != nil {
    log.Fatalf("failed to start EPH: %s\n", err)
}
```

## <a name="next-steps"></a>Kolejne kroki
Przeczytaj następujące artykuły:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funkcje i terminologią dotyczącą usługi Azure Event Hubs](event-hubs-features.md)
- [Event Hubs — często zadawane pytania](event-hubs-faq.md)


<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[bezpłatne konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
