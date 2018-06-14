---
title: Omówienie przekaźnika usługi Azure Standard interfejsów API platformy .NET | Dokumentacja firmy Microsoft
description: Omówienie usługi Azure Standard interfejs API .NET przekazywania
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: b1da9ac1-811b-4df7-a22c-ccd013405c40
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: sethm
ms.openlocfilehash: aa1863a44f00ae17f63b02c7c247b2c9fd9925f6
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2018
ms.locfileid: "28016437"
---
# <a name="azure-relay-hybrid-connections-net-standard-api-overview"></a>Omówienie usługi Azure API standardowe .NET połączeń hybrydowych przekazywania

W tym artykule przedstawiono niektóre kluczowe Azure przekazywania hybrydowego połączenia .NET Standard [interfejsów API klienta](/dotnet/api/microsoft.azure.relay).
  
## <a name="relay-connection-string-builder-class"></a>Konstruktor ciągów połączenia przekaźnika — klasa

[RelayConnectionStringBuilder] [ RelayConnectionStringBuilder] klasy formatuje parametry połączenia, które są specyficzne dla połączeń hybrydowych przekazywania. Służy on do weryfikacji formatu ciągu połączenia lub utworzenia parametrów połączenia, od początku. Zobacz na przykład następujący kod:

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

Można również przekazać ciąg połączenia bezpośrednio do `RelayConnectionStringBuilder` metody. Ta operacja pozwala sprawdzić, czy ciąg połączenia jest w nieprawidłowym formacie. Jeśli wszystkie parametry są nieprawidłowe, generuje konstruktora `ArgumentException`.

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

[HybridConnectionStream] [ HCStream] klasy jest podstawowy obiekt używany do wysyłania i odbierania danych z punktu końcowego przekaźnika usługi Azure, czy korzystasz z [HybridConnectionClient][HCClient], lub [HybridConnectionListener][HCListener].

### <a name="getting-a-hybrid-connection-stream"></a>Pobieranie strumienia połączenia hybrydowego

#### <a name="listener"></a>Odbiornik

Przy użyciu [HybridConnectionListener] [ HCListener] obiektu, możesz uzyskać `HybridConnectionStream` obiektów w następujący sposób:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var listener = new HybridConnectionListener(csb.ToString());
// Open a connection to the Relay endpoint
await listener.OpenAsync();
// Get a `HybridConnectionStream`
var hybridConnectionStream = await listener.AcceptConnectionAsync();
```

#### <a name="client"></a>Klient

Przy użyciu [HybridConnectionClient] [ HCClient] obiektu, możesz uzyskać `HybridConnectionStream` obiektów w następujący sposób:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var client = new HybridConnectionClient(csb.ToString());
// Open a connection to the Relay endpoint and get a `HybridConnectionStream`
var hybridConnectionStream = await client.CreateConnectionAsync();
```

### <a name="receiving-data"></a>Odbieranie danych

[HybridConnectionStream] [ HCStream] klasa umożliwia komunikację dwukierunkową. W większości przypadków stale pojawi się ze strumienia. Podczas czytania tekst ze strumienia, można także używać [StreamReader](https://msdn.microsoft.com/library/system.io.streamreader(v=vs.110).aspx) obiektu, który umożliwia łatwiejsze analizy danych. Na przykład mogą odczytywać dane jako tekst, a nie jako `byte[]`.

Poniższy kod odczytuje poszczególnych wierszy tekstu ze strumienia, dopóki żądanie anulowania:

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

Po połączeniu, możesz wysłać komunikatu do punktu końcowego przekazywania. Ponieważ obiekt połączenia dziedziczy [strumienia](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx), wysyłania danych jako `byte[]`. Poniższy przykład pokazuje, jak to zrobić:

```csharp
var data = Encoding.UTF8.GetBytes("hello");
await clientConnection.WriteAsync(data, 0, data.Length);
```

Jednak jeśli chcesz wysłać tekst bezpośrednio, bez konieczności zakodować ciąg każdorazowo, może zawijać `hybridConnectionStream` obiekt z [StreamWriter](https://msdn.microsoft.com/library/system.io.streamwriter(v=vs.110).aspx) obiektu.

```csharp
// The StreamWriter object only needs to be created once
var textWriter = new StreamWriter(hybridConnectionStream);
await textWriter.WriteLineAsync("hello");
```

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat przekaźnika usługi Azure, odwiedź te linki:

* [Odwołanie Microsoft.Azure.Relay](/dotnet/api/microsoft.azure.relay)
* [Co to jest usługa Azure Relay?](relay-what-is-it.md)
* [Interfejsy API dostępne przekazywania](relay-api-overview.md)

[RelayConnectionStringBuilder]: /dotnet/api/microsoft.azure.relay.relayconnectionstringbuilder
[HCStream]: /dotnet/api/microsoft.azure.relay.hybridconnectionstream
[HCClient]: /dotnet/api/microsoft.azure.relay.hybridconnectionclient
[HCListener]: /dotnet/api/microsoft.azure.relay.hybridconnectionlistener