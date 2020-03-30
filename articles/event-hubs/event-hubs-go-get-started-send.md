---
title: 'Szybki start: wysyłanie i odbieranie zdarzeń przy użyciu usługi Go — Usługi Azure Event Hubs'
description: 'Szybki start: Ten artykuł zawiera przewodnik dotyczący tworzenia aplikacji Go, która wysyła zdarzenia z usługi Azure Event Hubs.'
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.author: shvija
ms.openlocfilehash: e5f52d0ddbf9a66d974732d6d98ca8a5b09cc2d0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "73720585"
---
# <a name="quickstart-send-events-to-or-receive-events-from-event-hubs-using-go"></a>Szybki start: wysyłanie zdarzeń do centrów zdarzeń lub odbieranie ich z nich za pomocą funkcji Go
Azure Event Hubs to platforma do pozyskiwania i strumieniowego przesyłania danych, która umożliwia odbieranie i przetwarzanie milionów zdarzeń na sekundę. Usługa Event Hubs pozwala przetwarzać i przechowywać zdarzenia, dane lub dane telemetryczne generowane przez rozproszone oprogramowanie i urządzenia. Dane wysłane do centrum zdarzeń mogą zostać przekształcone i zmagazynowane przy użyciu dowolnego dostawcy analityki czasu rzeczywistego lub adapterów przetwarzania wsadowego/magazynowania. Aby zapoznać się ze szczegółowym omówieniem usługi Event Hubs, zobacz [Omówienie usługi Event Hubs](event-hubs-about.md) i [Funkcje usługi Event Hubs](event-hubs-features.md).

W tym samouczku opisano sposób pisania aplikacji Go do wysyłania zdarzeń do lub odbierania zdarzeń z centrum zdarzeń. 

> [!NOTE]
> Ten przewodnik Szybki start możesz pobrać jako przykład z witryny [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs), zastąpić ciągi `EventHubConnectionString` i `EventHubName` wartościami swojego centrum zdarzeń, a następnie uruchomić go. Alternatywnie możesz utworzyć własne rozwiązanie, wykonując kroki opisane w tym samouczku.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne jest spełnienie następujących wymagań wstępnych:

- Przejdź zainstalowany lokalnie. W razie potrzeby należy postępować zgodnie [z tymi instrukcjami.](https://golang.org/doc/install)
- Aktywne konto platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto][] przed rozpoczęciem.
- **Utwórz obszar nazw Centrów zdarzeń i centrum zdarzeń**. Użyj [witryny Azure Portal,](https://portal.azure.com) aby utworzyć obszar nazw typu Usługi zdarzeń i uzyskać poświadczenia zarządzania, których aplikacja potrzebuje do komunikowania się z centrum zdarzeń. Aby utworzyć przestrzeń nazw i centrum zdarzeń, wykonaj procedurę opisaną w [tym artykule](event-hubs-create.md).

## <a name="send-events"></a>Wysyłanie zdarzeń
W tej sekcji pokazano, jak utworzyć aplikację Go, aby wysłać zdarzenia do centrum zdarzeń. 

### <a name="install-go-package"></a>Pakiet Zainstaluj go

Pobierz pakiet Go dla centrów `dep`zdarzeń z lub `go get` . Przykład:

```bash
go get -u github.com/Azure/azure-event-hubs-go
go get -u github.com/Azure/azure-amqp-common-go/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
```

### <a name="import-packages-in-your-code-file"></a>Importowanie pakietów w pliku kodu

Aby zaimportować pakiety Go, użyj następującego przykładu kodu:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
)
```

### <a name="create-service-principal"></a>Tworzenie jednostki usługi

Utwórz nowego podmiotu usługi, postępując zgodnie z instrukcjami w [obszarze Tworzenie jednostki usługi platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure 2.0.](/cli/azure/create-an-azure-service-principal-azure-cli) Zapisz podane poświadczenia w swoim środowisku z następującymi nazwami. Pakiet Azure SDK for Go i pakiety Usługi Event Hubs są wstępnie skonfigurowane w celu wyszukywały te nazwy zmiennych:

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Teraz utwórz dostawcę autoryzacji dla klienta usługi Event Hubs, który używa tych poświadczeń:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="create-event-hubs-client"></a>Tworzenie klienta centrów zdarzeń

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

We werwieniu kodu użyj (1) do interaktywnego wysyłania wiadomości z terminala lub (2) do wysyłania wiadomości w programie:

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

Pobierz identyfikatory partycji w centrum zdarzeń:

```go
info, err := hub.GetRuntimeInformation(ctx)
if err != nil {
    log.Fatalf("failed to get runtime info: %s\n", err)
}
log.Printf("got partition IDs: %s\n", info.PartitionIDs)
```

Uruchom aplikację, aby wysłać zdarzenia do centrum zdarzeń. 

Gratulacje! Wysłano komunikaty do centrum zdarzeń.

## <a name="receive-events"></a>Odbieranie zdarzeń

### <a name="create-a-storage-account-and-container"></a>Tworzenie konta i kontenera magazynu

Stan, takich jak dzierżawy partycji i punktów kontrolnych w strumieniu zdarzeń są współużytkowane przez odbiorców przy użyciu kontenera usługi Azure Storage. Można utworzyć konto magazynu i kontener za pomocą go SDK, ale można również utworzyć, postępując zgodnie z instrukcjami w [informacje o kontach usługi Azure storage](../storage/common/storage-create-storage-account.md).

Przykłady do tworzenia artefaktów magazynu za pomocą zestawu Go SDK są dostępne w [repozytorium próbek Go](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage) i w przykładzie odpowiadającym temu samouczkowi.

### <a name="go-packages"></a>Pakiety Go

Aby odbierać wiadomości, pobierz pakiety Go `go get` `dep`dla Centrów zdarzeń z lub:

```bash
go get -u github.com/Azure/azure-event-hubs-go/...
go get -u github.com/Azure/azure-amqp-common-go/...
go get -u github.com/Azure/go-autorest/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
dep ensure -add github.com/Azure/go-autorest
```

### <a name="import-packages-in-your-code-file"></a>Importowanie pakietów w pliku kodu

Aby zaimportować pakiety Go, użyj następującego przykładu kodu:

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

Utwórz nowego podmiotu usługi, postępując zgodnie z instrukcjami w [obszarze Tworzenie jednostki usługi platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure 2.0.](/cli/azure/create-an-azure-service-principal-azure-cli) Zapisz podane poświadczenia w swoim środowisku z następującymi nazwami: Pakiet Azure SDK for Go i pakiet Usługi Event Hubs są wstępnie skonfigurowane do wyszukiwania tych nazw zmiennych.

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Następnie utwórz dostawcę autoryzacji dla klienta usługi Event Hubs, który używa tych poświadczeń:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="get-metadata-struct"></a>Uzyskaj strukturę metadanych

Uzyskaj strukturę z metadanymi o środowisku platformy Azure przy użyciu narzędzia Azure Go SDK. Późniejsze operacje używają tej struktury, aby znaleźć poprawne punkty końcowe.

```go
azureEnv, err := azure.EnvironmentFromName("AzurePublicCloud")
if err != nil {
    log.Fatalf("could not get azure.Environment struct: %s\n", err)
}
```

### <a name="create-credential-helper"></a>Tworzenie pomocnika poświadczeń 

Utwórz pomocnika poświadczeń, który używa poprzednich poświadczeń usługi Azure Active Directory (AAD) do utworzenia poświadczenia sygnatury dostępu współdzielonego (SAS) dla magazynu. Ostatni parametr informuje ten konstruktora, aby używał tych samych zmiennych środowiskowych, które były używane wcześniej:

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

### <a name="create-a-check-pointer-and-a-leaser"></a>Tworzenie wskaźnika wyboru i dzierżawy 

Utwórz **dzierżawcę,** odpowiedzialnego za dzierżawę partycji do określonego odbiorcy i **wskaźnik wyboru**, odpowiedzialny za pisanie punktów kontrolnych dla strumienia wiadomości, tak aby inni odbiorcy mogli rozpocząć odczyt z właściwego przesunięcia.

Obecnie dostępny jest pojedynczy **punkt kontroli magazynu,** który używa tego samego kontenera magazynu do zarządzania dzierżawami i punktami kontrolnymi. Oprócz nazwy konta magazynu i kontenera **StorageLeaserCheckpointer** potrzebuje poświadczenia utworzonego w poprzednim kroku i struktury środowiska platformy Azure, aby poprawnie uzyskać dostęp do kontenera.

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

### <a name="construct-event-processor-host"></a>Konstruuj hosta procesora zdarzeń

Masz teraz elementy potrzebne do skonstruowania EventProcessorHost, w następujący sposób. Ten sam **StorageLeaserCheckpointer** jest używany zarówno jako leasingodziarz, jak i wskaźnik wyboru, jak opisano wcześniej:

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

### <a name="create-handler"></a>Tworzenie programu obsługi 

Teraz utwórz program obsługi i zarejestruj go za pomocą hosta procesora zdarzeń. Po uruchomieniu hosta stosuje to i wszystkie inne określone programy obsługi do wiadomości przychodzących:

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

Po skonfigurowaniu wszystkiego można uruchomić hosta `Start(context)` procesora zdarzeń, aby `StartNonBlocking(context)` utrzymać go na stałe, lub aby uruchomić tylko tak długo, jak długo są dostępne wiadomości.

Ten samouczek rozpoczyna się i działa w następujący sposób; zobacz przykład GitHub na `StartNonBlocking`przykład przy użyciu:

```go
ctx := context.Background()
err = p.Start()
if err != nil {
    log.Fatalf("failed to start EPH: %s\n", err)
}
```

## <a name="next-steps"></a>Następne kroki
Przeczytaj następujące artykuły:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funkcje i terminologia w usłudze Azure Event Hubs](event-hubs-features.md)
- [Event Hubs — często zadawane pytania](event-hubs-faq.md)


<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[darmowe konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
