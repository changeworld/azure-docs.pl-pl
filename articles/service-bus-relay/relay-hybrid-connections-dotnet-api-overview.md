---
title: Omówienie standardowych interfejsów API usługi Azure Relay .NET | Dokumenty firmy Microsoft
description: W tym artykule podsumowano niektóre z kluczy omówienie usługi Azure Relay hybrydowy interfejs API .NET Standard.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: b1da9ac1-811b-4df7-a22c-ccd013405c40
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: spelluru
ms.openlocfilehash: 18eaf2d2daae817107be6cdb0da9359bb5f9b4e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514539"
---
# <a name="azure-relay-hybrid-connections-net-standard-api-overview"></a>Omówienie interfejsu API usługi Azure Relay hybrid connections .NET

W tym artykule podsumowano niektóre kluczowe interfejsy [API klienta usługi](/dotnet/api/microsoft.azure.relay)Azure Relay .NET Standard .
  
## <a name="relay-connection-string-builder-class"></a>Klasa konstruktora ciągów połączeń przekazywania

[Klasa RelayConnectionStringBuilder][RelayConnectionStringBuilder] formatuje parametry połączeń specyficzne dla połączeń hybrydowych przekazywania. Można go użyć do sprawdzenia formatu ciągu połączenia lub do utworzenia ciągu połączenia od podstaw. Zobacz następujący kod na przykład:

```csharp
var endpoint = "[Relay namespace]";
var entityPath = "[Name of the Hybrid Connection]";
var sharedAccessKeyName = "[SAS key name]";
var sharedAccessKey = "[SAS key value]";

var connectionStringBuilder = new RelayConnectionStringBuilder()
{
    Endpoint = endpoint,
    EntityPath = entityPath,
    SharedAccessKeyName = sasKeyName,
    SharedAccessKey = sasKeyValue
};
```

Można również przekazać parametry połączenia bezpośrednio `RelayConnectionStringBuilder` do metody. Ta operacja umożliwia sprawdzenie, czy parametry połączenia są w prawidłowym formacie. Jeśli którykolwiek z parametrów jest nieprawidłowy, konstruktor generuje plik `ArgumentException`.

```csharp
var myConnectionString = "[RelayConnectionString]";
// Declare the connectionStringBuilder so that it can be used outside of the loop if needed
RelayConnectionStringBuilder connectionStringBuilder;
try
{
    // Create the connectionStringBuilder using the supplied connection string
    connectionStringBuilder = new RelayConnectionStringBuilder(myConnectionString);
}
catch (ArgumentException ae)
{
    // Perform some error handling
}
```

## <a name="hybrid-connection-stream"></a>Strumień połączeń hybrydowych

[HybridConnectionStream][HCStream] Klasa jest podstawowym obiektem używanym do wysyłania i odbierania danych z punktu końcowego usługi Azure Relay, niezależnie od tego, czy pracujesz z [hybridconnectionClient,][HCClient]czy [HybridConnectionListener][HCListener].

### <a name="getting-a-hybrid-connection-stream"></a>Uzyskiwanie strumienia połączenia hybrydowego

#### <a name="listener"></a>Odbiornik

Za pomocą [obiektu HybridConnectionListener][HCListener] można `HybridConnectionStream` uzyskać obiekt w następujący sposób:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var listener = new HybridConnectionListener(csb.ToString());
// Open a connection to the Relay endpoint
await listener.OpenAsync();
// Get a `HybridConnectionStream`
var hybridConnectionStream = await listener.AcceptConnectionAsync();
```

#### <a name="client"></a>Klient

Za pomocą [obiektu HybridConnectionClient][HCClient] można `HybridConnectionStream` uzyskać obiekt w następujący sposób:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var client = new HybridConnectionClient(csb.ToString());
// Open a connection to the Relay endpoint and get a `HybridConnectionStream`
var hybridConnectionStream = await client.CreateConnectionAsync();
```

### <a name="receiving-data"></a>Odbieranie danych

[HybridConnectionStream][HCStream] Klasa umożliwia komunikację dwukierunkową. W większości przypadków stale odbierane ze strumienia. Jeśli czytasz tekst ze strumienia, można również użyć [StreamReader](https://msdn.microsoft.com/library/system.io.streamreader(v=vs.110).aspx) obiektu, który umożliwia łatwiejsze analizowanie danych. Na przykład można odczytywać dane jako `byte[]`tekst, a nie jako .

Poniższy kod odczytuje poszczególne wiersze tekstu ze strumienia, dopóki nie zażąda się anulowania:

```csharp
// Create a CancellationToken, so that we can cancel the while loop
var cancellationToken = new CancellationToken();
// Create a StreamReader from the 'hybridConnectionStream`
var streamReader = new StreamReader(hybridConnectionStream);

while (!cancellationToken.IsCancellationRequested)
{
    // Read a line of input until a newline is encountered
    var line = await streamReader.ReadLineAsync();
    if (string.IsNullOrEmpty(line))
    {
        // If there's no input data, we will signal that 
        // we will no longer send data on this connection
        // and then break out of the processing loop.
        await hybridConnectionStream.ShutdownAsync(cancellationToken);
        break;
    }
}
```

### <a name="sending-data"></a>Wysyłanie danych

Po nawiązaniu połączenia można wysłać wiadomość do punktu końcowego przekazywania. Ponieważ obiekt połączenia dziedziczy [Stream,](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx)wyślij `byte[]`dane jako plik . W poniższym przykładzie pokazano, jak to zrobić:

```csharp
var data = Encoding.UTF8.GetBytes("hello");
await clientConnection.WriteAsync(data, 0, data.Length);
```

Jednak jeśli chcesz wysłać tekst bezpośrednio, bez konieczności kodowania ciągu za każdym `hybridConnectionStream` razem, można zawinąć obiekt z [StreamWriter](https://msdn.microsoft.com/library/system.io.streamwriter(v=vs.110).aspx) obiektu.

```csharp
// The StreamWriter object only needs to be created once
var textWriter = new StreamWriter(hybridConnectionStream);
await textWriter.WriteLineAsync("hello");
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o usłudze Azure Relay, odwiedź następujące łącza:

* [Odwołanie microsoft.Azure.relay](/dotnet/api/microsoft.azure.relay)
* [Co to jest usługa Azure Relay?](relay-what-is-it.md)
* [Dostępne interfejsy API przekazywania](relay-api-overview.md)

[RelayConnectionStringBuilder]: /dotnet/api/microsoft.azure.relay.relayconnectionstringbuilder
[HCStream]: /dotnet/api/microsoft.azure.relay.hybridconnectionstream
[HCClient]: /dotnet/api/microsoft.azure.relay.hybridconnectionclient
[HCListener]: /dotnet/api/microsoft.azure.relay.hybridconnectionlistener