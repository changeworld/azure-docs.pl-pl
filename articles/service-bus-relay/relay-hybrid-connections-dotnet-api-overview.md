---
title: Omówienie usługi Azure Relay standardowych interfejsów API platformy .NET | Dokumentacja firmy Microsoft
description: Omówienie usługi Azure Standard interfejsu API .NET usługi Relay
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
ms.openlocfilehash: 78ad3ab49db162af060b4273deea717cd3472668
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60749023"
---
# <a name="azure-relay-hybrid-connections-net-standard-api-overview"></a>Omówienie usługi Azure Relay .NET Standard interfejsowi API połączeń hybrydowych

Ten artykuł zawiera podsumowanie niektórych klucza usługi Azure Relay hybrydowego połączenia .NET Standard [interfejsów API klienta](/dotnet/api/microsoft.azure.relay).
  
## <a name="relay-connection-string-builder-class"></a>Klasa konstruktora parametrów połączenia usługi Relay

[RelayConnectionStringBuilder] [ RelayConnectionStringBuilder] klasy formaty ciągów połączeń, które są specyficzne dla połączeń hybrydowych usługi Relay. Służy do Sprawdź format parametrów połączenia lub Tworzenie parametrów połączenia od podstaw. Zobacz poniższy kod przykładowy:

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

Można również przekazać parametry połączenia bezpośrednio do `RelayConnectionStringBuilder` metody. Tej operacji można sprawdzić, czy ciąg połączenia jest w prawidłowym formacie. Jeśli dowolny z parametrów jest nieprawidłowy, generuje konstruktora `ArgumentException`.

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

## <a name="hybrid-connection-stream"></a>Strumień połączenia hybrydowego

[HybridConnectionStream] [ HCStream] klasa jest podstawowy obiekt używany do wysyłania i odbierania danych z punktu końcowego usługi Azure Relay, czy pracujesz z [HybridConnectionClient] [ HCClient], lub [HybridConnectionListener][HCListener].

### <a name="getting-a-hybrid-connection-stream"></a>Wprowadzenie strumienia połączenia hybrydowego

#### <a name="listener"></a>Odbiornik

Za pomocą [HybridConnectionListener] [ HCListener] obiektu, można uzyskać `HybridConnectionStream` obiektu w następujący sposób:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var listener = new HybridConnectionListener(csb.ToString());
// Open a connection to the Relay endpoint
await listener.OpenAsync();
// Get a `HybridConnectionStream`
var hybridConnectionStream = await listener.AcceptConnectionAsync();
```

#### <a name="client"></a>Klient

Za pomocą [HybridConnectionClient] [ HCClient] obiektu, można uzyskać `HybridConnectionStream` obiektu w następujący sposób:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var client = new HybridConnectionClient(csb.ToString());
// Open a connection to the Relay endpoint and get a `HybridConnectionStream`
var hybridConnectionStream = await client.CreateConnectionAsync();
```

### <a name="receiving-data"></a>Odbieranie danych

[HybridConnectionStream] [ HCStream] klasy umożliwia komunikację dwukierunkową. W większości przypadków ciągle otrzymujesz ze strumienia. Jeśli czytasz tekst ze strumienia, można także używać [StreamReader](https://msdn.microsoft.com/library/system.io.streamreader(v=vs.110).aspx) obiektu, który umożliwia łatwiejsze analizy danych. Na przykład może odczytywać dane jako tekst, a nie jako `byte[]`.

Poniższy kod odczytuje pojedyncze wiersze tekstu ze strumienia, dopóki nie zażądano anulowania:

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

Po połączeniu możesz wysłać komunikat do punktu końcowego usługi Relay. Ponieważ dziedziczy obiektu połączenia [Stream](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx), wysyłanie danych jako `byte[]`. Poniższy przykład pokazuje, jak to zrobić:

```csharp
var data = Encoding.UTF8.GetBytes("hello");
await clientConnection.WriteAsync(data, 0, data.Length);
```

Jednakże, jeśli chcesz wysłać tekst bezpośrednio, bez konieczności kodowania ciąg za każdym razem można opakować `hybridConnectionStream` obiekt z [StreamWriter —](https://msdn.microsoft.com/library/system.io.streamwriter(v=vs.110).aspx) obiektu.

```csharp
// The StreamWriter object only needs to be created once
var textWriter = new StreamWriter(hybridConnectionStream);
await textWriter.WriteLineAsync("hello");
```

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat usługi Azure Relay, skorzystaj z następujących linków:

* [Odwołanie Microsoft.Azure.Relay](/dotnet/api/microsoft.azure.relay)
* [Co to jest usługa Azure Relay?](relay-what-is-it.md)
* [Interfejsy API dostępne usługi Relay](relay-api-overview.md)

[RelayConnectionStringBuilder]: /dotnet/api/microsoft.azure.relay.relayconnectionstringbuilder
[HCStream]: /dotnet/api/microsoft.azure.relay.hybridconnectionstream
[HCClient]: /dotnet/api/microsoft.azure.relay.hybridconnectionclient
[HCListener]: /dotnet/api/microsoft.azure.relay.hybridconnectionlistener