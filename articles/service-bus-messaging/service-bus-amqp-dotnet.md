---
title: Usługa Azure Service Bus z usługami .NET i AMQP 1.0 | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób korzystania z usługi Azure Service Bus z aplikacji platformy .NET przy użyciu protokołu AMQP (Advanced Messaging Usłudze kolejkowania).
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 332bcb13-e287-4715-99ee-3d7d97396487
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 8157efac5ff1fc135659a84b4f4825ff36307480
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297668"
---
# <a name="use-service-bus-from-net-with-amqp-10"></a>Użyj usługi Service Bus z platformy .NET z komunikatem AMQP 1.0

Obsługa usługi AMQP 1.0 jest dostępna w pakiecie service bus w wersji 2.1 lub nowszej. Możesz mieć pewność, że masz najnowszą wersję, pobierając bity usługi Service Bus z [programu NuGet][NuGet].

## <a name="configure-net-applications-to-use-amqp-10"></a>Konfigurowanie aplikacji platformy .NET do używania usługi AMQP 1.0

Domyślnie biblioteka klienta usługi Service Bus .NET komunikuje się z usługą Service Bus przy użyciu dedykowanego protokołu opartego na posłudze SOAP. Aby użyć protokołu AMQP 1.0 zamiast protokołu domyślnego, wymagana jest jawna konfiguracja ciągu połączenia usługi Service Bus, zgodnie z opisem w następnej sekcji. Poza tą zmianą kod aplikacji pozostaje niezmieniony podczas korzystania z usługi AMQP 1.0.

W bieżącej wersji istnieje kilka funkcji interfejsu API, które nie są obsługiwane podczas korzystania z usługi AMQP. Te nieobsługiwały funkcje są wymienione w sekcji [Różnice behawioralne](#behavioral-differences). Niektóre zaawansowane ustawienia konfiguracji mają również inne znaczenie podczas korzystania z protokołu AMQP.

### <a name="configuration-using-appconfig"></a>Konfiguracja przy użyciu app.config

Dobrą praktyką dla aplikacji jest używanie pliku konfiguracyjnego App.config do przechowywania ustawień. W przypadku aplikacji usługi Service Bus można użyć pliku App.config do przechowywania ciągu połączenia usługi Service Bus. Przykładowy plik App.config jest następujący:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
    </appSettings>
</configuration>
```

Wartość tego `Microsoft.ServiceBus.ConnectionString` ustawienia to parametry połączenia usługi Service Bus używane do konfigurowania połączenia z usługą Service Bus. Format jest następujący:

`Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp`

Gdzie `namespace` `SAS key` i są uzyskiwane z [witryny Azure portal][Azure portal] podczas tworzenia obszaru nazw usługi Service Bus. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru nazw usługi Service Bus przy użyciu portalu Azure][Create a Service Bus namespace using the Azure portal].

W przypadku korzystania z usługi AMQP dołącz ciąg połączenia za pomocą pliku `;TransportType=Amqp`. Ta notacja nakazuje bibliotece klienta nawiązanie połączenia z usługą Service Bus przy użyciu usługi AMQP 1.0.

## <a name="message-serialization"></a>Serializacja wiadomości

W przypadku korzystania z protokołu domyślnego domyślnym zachowaniem serializacji biblioteki klienta .NET jest użycie typu [DataContractSerializer][DataContractSerializer] do serializacji wystąpienia [BrokeredMessage][BrokeredMessage] do transportu między biblioteką klienta a usługą Service Bus. Podczas korzystania z trybu transportu AMQP biblioteka klienta używa systemu typu AMQP do serializacji [wiadomości z brokerem][BrokeredMessage] do komunikatu AMQP. Ta serializacja umożliwia odebranie i zinterpretowanie wiadomości przez aplikację odbierającą, która potencjalnie działa na innej platformie, na przykład aplikacji Java, która używa interfejsu API JMS do uzyskiwania dostępu do usługi Service Bus.

Podczas konstruowania [Wystąpienia BrokeredMessage,][BrokeredMessage] można podać .NET obiektu jako parametr do konstruktora służyć jako treść wiadomości. Dla obiektów, które mogą być mapowane do typów pierwotnych AMQP, treść jest serializowany do typów danych AMQP. Jeśli obiekt nie może być bezpośrednio mapowane do typu pierwotnego AMQP; oznacza to, że typ niestandardowy zdefiniowany przez aplikację, a następnie obiekt jest serializowany przy użyciu [DataContractSerializer][DataContractSerializer], a bajty serializowane są wysyłane w komunikacie danych AMQP.

Aby ułatwić współdziałanie z non-.NET klientami, należy używać tylko typów .NET, które mogą być serializowane bezpośrednio do typów AMQP dla treści wiadomości. W poniższej tabeli opisano te typy i odpowiednie mapowanie do systemu typu AMQP.

| Typ obiektu obiektu .NET | Zamapowany typ amqp | Typ sekcji treści AMQP |
| --- | --- | --- |
| bool |wartość logiczna |Wartość AMQP |
| byte |ubyt |Wartość AMQP |
| ushort |ushort |Wartość AMQP |
| uint |uint |Wartość AMQP |
| ulong |ulong |Wartość AMQP |
| sbyte |byte |Wartość AMQP |
| short |short |Wartość AMQP |
| int |int |Wartość AMQP |
| long |long |Wartość AMQP |
| float |float |Wartość AMQP |
| double |double |Wartość AMQP |
| decimal |dziesiętne128 |Wartość AMQP |
| char |char |Wartość AMQP |
| DateTime |sygnatura czasowa |Wartość AMQP |
| Guid (identyfikator GUID) |uuid |Wartość AMQP |
| bajt[] |binarny |Wartość AMQP |
| ciąg |ciąg |Wartość AMQP |
| System.collections.ilist |list |Wartość AMQP: elementy zawarte w kolekcji mogą być tylko te, które są zdefiniowane w tej tabeli. |
| System.array |tablica |Wartość AMQP: elementy zawarte w kolekcji mogą być tylko te, które są zdefiniowane w tej tabeli. |
| System.collections.idictionary |map |Wartość AMQP: elementy zawarte w kolekcji mogą być tylko te, które są zdefiniowane w tej tabeli. Uwaga: obsługiwane są tylko klucze ciągu. |
| Identyfikator uri |Opisany ciąg(patrz poniższa tabela) |Wartość AMQP |
| Datetimeoffset |Opisane long(patrz poniższa tabela) |Wartość AMQP |
| przedział_czasu |Opisane long(patrz poniżej) |Wartość AMQP |
| Strumień |binarny |Dane AMQP (mogą być wielokrotne). Sekcje Dane zawierają nieprzetworzone bajty odczytywane z obiektu Stream. |
| Inny obiekt |binarny |Dane AMQP (mogą być wielokrotne). Zawiera serializowany plik binarny obiektu, który używa DataContractSerializer lub serializatora dostarczone przez aplikację. |

| Typ .NET | Zamapowany typ opisany przez amqp | Uwagi |
| --- | --- | --- |
| Identyfikator uri |`<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>` |Uri.AbsoluteUri |
| Datetimeoffset |`<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` |DateTimeOffset.UtcTicks |
| przedział_czasu |`<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type>` |TimeSpan.Kleszcze |

## <a name="behavioral-differences"></a>Różnice w zachowaniu

Istnieją pewne niewielkie różnice w zachowaniu interfejsu API usługi Service Bus .NET podczas korzystania z protokołu AMQP w porównaniu z protokołem domyślnym:

* [Właściwość OperationTimeout][OperationTimeout] jest ignorowana.
* `MessageReceiver.Receive(TimeSpan.Zero)`jest realizowany `MessageReceiver.Receive(TimeSpan.FromSeconds(10))`jako .
* Wypełnianie wiadomości za pomocą tokenów blokady może odbywać się tylko przez odbiorniki wiadomości, które początkowo odebrały wiadomości.

## <a name="control-amqp-protocol-settings"></a>Sterowanie ustawieniami protokołu AMQP

Interfejsy [API platformy .NET](/dotnet/api/) udostępniają kilka ustawień w celu kontrolowania zachowania protokołu AMQP:

* **[MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver.prefetchcount?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessageReceiver_PrefetchCount)**: Steruje początkowym kredytem zastosowanym do łącza. Wartość domyślna to 0.
* **[MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.maxframesize?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_MaxFrameSize)**: Steruje maksymalnym rozmiarem ramki AMQP oferowanym podczas negocjacji w czasie otwarcia połączenia. Wartość domyślna to 65 536 bajtów.
* **[MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.batchflushinterval?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_BatchFlushInterval)**: Jeśli transfery są wsadowe, ta wartość określa maksymalne opóźnienie wysyłania dyspozycji. Domyślnie dziedziczone przez nadawców/odbiorców. Pojedynczy nadawca/odbiorca może zastąpić wartość domyślną, która wynosi 20 milisekund.
* **[MessagingFactorySettings.AmqpTransportSettings.UseSslStreamSecurity](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.usesslstreamsecurity?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_UseSslStreamSecurity)**: Określa, czy połączenia PROTOKOŁU AMQP są ustanawiane za pośrednictwem połączenia TLS. Wartość domyślna to **prawda**.

## <a name="next-steps"></a>Następne kroki

Chcesz dowiedzieć się więcej? Odwiedź następujące linki:

* [Omówienie usługi AMQP usługi Service Bus]
* [Przewodnik dotyczący protokołu AMQP 1.0]

[Create a Service Bus namespace using the Azure portal]: service-bus-create-namespace-portal.md
[DataContractSerializer]: https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer.aspx
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azureservicebus-4.0.0
[Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory.acceptmessagesession?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessagingFactory_AcceptMessageSession
[OperationTimeout]: /dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout
[NuGet]: https://nuget.org/packages/WindowsAzure.ServiceBus/
[Azure portal]: https://portal.azure.com
[Omówienie usługi AMQP usługi Service Bus]: service-bus-amqp-overview.md
[Przewodnik dotyczący protokołu AMQP 1.0]: service-bus-amqp-protocol-guide.md

