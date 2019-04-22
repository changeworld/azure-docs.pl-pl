---
title: Usługi Azure Service Bus przy użyciu platformy .NET i protokołu AMQP 1.0 | Dokumentacja firmy Microsoft
description: Za pomocą usługi Azure Service Bus z platformy .NET z obsługą protokołu AMQP
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
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 82301a17bb461b6d8733d5f046fe791ffbcf3ecb
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58885710"
---
# <a name="use-service-bus-from-net-with-amqp-10"></a>Usługa Service Bus z platformy .NET za pomocą protokołu AMQP 1.0

Obsługa protokołu AMQP 1.0 jest dostępna w wersji pakietów usługi Service Bus, 2.1 lub nowszej. Upewnij się masz najnowszą wersję, pobierając bitów usługi Service Bus z [NuGet][NuGet].

## <a name="configure-net-applications-to-use-amqp-10"></a>Konfigurowanie aplikacji platformy .NET do korzystania z protokołu AMQP 1.0

Domyślnie biblioteki klienckiej usługi Service Bus .NET komunikuje się z usługą Service Bus przy użyciu dedykowanego protokołu opartego na protokole SOAP. Użyj protokołu AMQP 1.0, zamiast domyślnego protokołu wymaga jawnej konfiguracji na parametry połączenia usługi Service Bus, zgodnie z opisem w następnej sekcji. Inne niż ta zmiana kodu aplikacji pozostaje niezmieniony, korzystając z protokołu AMQP 1.0.

W bieżącej wersji istnieje kilka funkcji interfejsu API, które nie są obsługiwane w przypadku korzystania z protokołu AMQP. Nieobsługiwanych funkcji są wymienione w sekcji [różnice w zachowaniu](#behavioral-differences). Niektóre ustawienia konfiguracji zaawansowanej również mają inne znaczenie, gdy za pomocą protokołu AMQP.

### <a name="configuration-using-appconfig"></a>Konfiguracja przy użyciu pliku App.config

Jest dobrym rozwiązaniem dla aplikacji, aby użyć pliku konfiguracji App.config do przechowywania ustawień. Dla aplikacji usługi Service Bus można użyć pliku App.config do przechowywania parametrów połączenia usługi Service Bus. Przykładowy plik App.config jest następująca:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
    </appSettings>
</configuration>
```

Wartość `Microsoft.ServiceBus.ConnectionString` ma parametry połączenia usługi Service Bus, która umożliwia skonfigurowanie połączenia usługi Service Bus. Format jest następujący:

`Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp`

Gdzie `namespace` i `SAS key` są uzyskiwane z [witryny Azure portal] [ Azure portal] podczas tworzenia przestrzeni nazw usługi Service Bus. Aby uzyskać więcej informacji, zobacz [tworzenie przestrzeni nazw usługi Service Bus przy użyciu witryny Azure portal][Create a Service Bus namespace using the Azure portal].

Korzystając z protokołu AMQP, Dołącz ciąg połączenia za pomocą `;TransportType=Amqp`. Tej notacji powoduje, że biblioteki klienta, aby wprowadzić swoje połączenie usługi Service Bus przy użyciu protokołu AMQP 1.0.

## <a name="message-serialization"></a>Serializacja wiadomości

Korzystając z domyślnym protokołem, domyślne zachowanie serializacji biblioteki klienckiej platformy .NET jest użycie [DataContractSerializer] [ DataContractSerializer] typ do zserializowania [BrokeredMessage] [ BrokeredMessage] wystąpienia dla transportu między biblioteki klienckiej i usługi Service Bus. Podczas korzystania z trybu transportu AMQP Biblioteka klienta używa system typów protokołu AMQP do serializacji [komunikatu obsługiwanego przez brokera] [ BrokeredMessage] do wiadomości protokołu AMQP. Tej serializacji umożliwia komunikat jest odbierany i interpretowane przez aplikację odbierającą, potencjalnie jest uruchomiona na różne platformy, na przykład aplikacji Java, która używa interfejsu API JMS do dostępu do usługi Service Bus.

Podczas konstruowania [BrokeredMessage] [ BrokeredMessage] wystąpienia obiektu .NET można podać jako parametr do konstruktora, która będzie służyć jako treść komunikatu. Dla obiektów, które mogą być mapowane na typy pierwotne protokołu AMQP treść jest serializowana z typami danych protokołu AMQP. Jeśli obiekt nie może bezpośrednio zamapowany na typ pierwotny protokół AMQP; oznacza to, niestandardowy typ zdefiniowany przez aplikację, a następnie serializowany jest obiekt, za pomocą [DataContractSerializer][DataContractSerializer], i serializacji bajty są wysyłane w wiadomościach danych protokołu AMQP.

Aby ułatwić współdziałania z klientami — .NET, należy użyć tylko te typy .NET, które można serializować bezpośrednio do typów połączeń AMQP dla treści wiadomości. W poniższej tabeli przedstawiono te typy i odpowiadające im mapowanie do systemu typów protokołu AMQP.

| Typ obiektu na treść platformy .NET | Typ zamapowanego protokołu AMQP | Typ części treści protokołu AMQP |
| --- | --- | --- |
| bool |wartość logiczna |Wartość protokołu AMQP |
| bajt |ubyte |Wartość protokołu AMQP |
| ushort |ushort |Wartość protokołu AMQP |
| uint |uint |Wartość protokołu AMQP |
| ulong |ulong |Wartość protokołu AMQP |
| sbyte — |bajt |Wartość protokołu AMQP |
| Krótka |Krótka |Wartość protokołu AMQP |
| int |int |Wartość protokołu AMQP |
| długi |długi |Wartość protokołu AMQP |
| float |float |Wartość protokołu AMQP |
| double |double |Wartość protokołu AMQP |
| decimal |decimal128 |Wartość protokołu AMQP |
| Char |Char |Wartość protokołu AMQP |
| DateTime |sygnatura czasowa |Wartość protokołu AMQP |
| Guid |uuid |Wartość protokołu AMQP |
| byte[] |dane binarne |Wartość protokołu AMQP |
| string |string |Wartość protokołu AMQP |
| System.Collections.IList |list |Wartość AMQP: elementy zawarte w kolekcji można tylko te, które są zdefiniowane w tej tabeli. |
| System.Array |tablica |Wartość AMQP: elementy zawarte w kolekcji można tylko te, które są zdefiniowane w tej tabeli. |
| System.Collections.IDictionary |map |Wartość AMQP: elementy zawarte w kolekcji można tylko te, które są zdefiniowane w tej tabeli. Uwaga: obsługiwane są tylko kluczy ciągu. |
| Identyfikator URI |Opisano parametry (patrz poniższa tabela) |Wartość protokołu AMQP |
| DateTimeOffset |Opisano długo (patrz poniższa tabela) |Wartość protokołu AMQP |
| TimeSpan |Opisano długo (patrz poniżej) |Wartość protokołu AMQP |
| Strumień |dane binarne |Dane AMQP (może być wiele). Sekcje danych zawierają odczytanych z obiektu Stream bajtów raw. |
| Inny obiekt |dane binarne |Dane AMQP (może być wiele). Zawiera wartość binarną Zserializowany obiekt, który korzysta z elementu DataContractSerializer, lub element serializujący dostarczoną przez aplikację. |

| Typ architektury .NET | Zamapowane AMQP opisem typu | Uwagi |
| --- | --- | --- |
| Identyfikator URI |`<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>` |Uri.AbsoluteUri |
| DateTimeOffset |`<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` |DateTimeOffset.UtcTicks |
| TimeSpan |`<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type>` |TimeSpan.Ticks |

## <a name="behavioral-differences"></a>Różnice w zachowaniu

Istnieją pewne niewielkie różnice w zachowaniu API .NET usługi Service Bus, korzystając z protokołu AMQP, w porównaniu z domyślnym protokołem:

* [OperationTimeout] [ OperationTimeout] właściwość jest ignorowana.
* `MessageReceiver.Receive(TimeSpan.Zero)` jest implementowany jako `MessageReceiver.Receive(TimeSpan.FromSeconds(10))`.
* Kończenie wiadomości przez tokeny blokady jest możliwe tylko przez odbiorców wiadomości, które początkowo Odebrane komunikaty.

## <a name="control-amqp-protocol-settings"></a>Ustawienia protokołu AMQP kontroli

[Interfejsów API platformy .NET](/dotnet/api/) ujawnić kilka ustawień w celu sterowania zachowaniem protokołu AMQP:

* **[MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver.prefetchcount?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessageReceiver_PrefetchCount)**: Określa początkowe środki zastosowane do łącza. Wartość domyślna to 0.
* **[MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.maxframesize?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_MaxFrameSize)**: Formanty maksymalny rozmiar ramki protokołu AMQP oferowane w trakcie negocjowania połączenia Otwórz czasu. Wartość domyślna to 65 536 bajtów.
* **[MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.batchflushinterval?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_BatchFlushInterval)**: Jeśli transfery batchable, ta wartość określa maksymalne opóźnienie wysyłania przepisy. Dziedziczone przez nadawcy/odbiorcy, domyślnie. Poszczególne nadawcy/odbiorcy mogą zastąpić domyślne, czyli 20 MS.
* **[MessagingFactorySettings.AmqpTransportSettings.UseSslStreamSecurity](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.usesslstreamsecurity?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_UseSslStreamSecurity)**: Określa, czy nawiązywane są połączenia AMQP, przez połączenie SSL. Wartość domyślna to **true**.

## <a name="next-steps"></a>Kolejne kroki

Czy chcesz dowiedzieć się więcej? Skorzystaj z następujących linków:

* [Omówienie AMQP usługi Service Bus]
* [Przewodnik dotyczący protokołu AMQP 1.0]

[Create a Service Bus namespace using the Azure portal]: service-bus-create-namespace-portal.md
[DataContractSerializer]: https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer.aspx
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azureservicebus-4.0.0
[Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory.acceptmessagesession?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessagingFactory_AcceptMessageSession
[OperationTimeout]: /dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout
[NuGet]: https://nuget.org/packages/WindowsAzure.ServiceBus/
[Azure portal]: https://portal.azure.com
[Omówienie AMQP usługi Service Bus]: service-bus-amqp-overview.md
[Przewodnik dotyczący protokołu AMQP 1.0]: service-bus-amqp-protocol-guide.md

