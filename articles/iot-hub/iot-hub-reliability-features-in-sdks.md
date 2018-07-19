---
title: Jak zarządzać łączności i niezawodna obsługa komunikatów za pomocą zestawów SDK urządzeń Azure IoT Hub
description: Dowiedz się, jak poprawić łączności urządzeń i funkcji komunikatów przy użyciu zestawów SDK urządzeń Azure IoT Hub
services: iot-hub
keywords: ''
author: yzhong94
ms.author: yizhon
ms.date: 07/07/2018
ms.topic: article
ms.service: iot-hub
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: a318a1ef8b13b8fcb4f4401ac4d0e45037958d63
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2018
ms.locfileid: "39127595"
---
# <a name="how-to-manage-connectivity-and-reliable-messaging-using-azure-iot-hub-device-sdks"></a>Jak zarządzać łączności i niezawodna obsługa komunikatów za pomocą zestawów SDK urządzeń Azure IoT Hub

Ten przewodnik zawiera ogólne wskazówki dotyczące projektowania aplikacji odpornych na błędy urządzenia, wykorzystując łączności i niezawodne funkcje obsługi komunikatów w zestawy SDK urządzeń Azure IoT. Celem tego artykułu jest pomóc odpowiedzieć na pytania i obsługi tych scenariuszy:

- Zarządzanie połączenia sieciowego
- Zarządzanie przełączania się między różnych połączeń sieciowych
- ponowne nawiązanie połączenia z powodu błędów przejściowych połączenia usług zarządzania

Szczegóły implementacji może zależą od języka, zobacz dokumentację interfejsu API połączonego lub określonego zestawu SDK, aby uzyskać więcej informacji.

- [Zestaw SDK języka C/Python/systemu iOS](https://github.com/azure/azure-iot-sdk-c)
- [Zestaw SDK platformy .NET](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
- [Zestaw SDK Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)
- [Węzeł zestawu SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)


## <a name="designing-for-resiliency"></a>Projektowanie pod kątem odporności

Urządzenia IoT często zależy od połączenia sieciowe — ciągły i/lub niestabilny, takich jak GSM lub satelitarnej. Ponadto podczas interakcji z usługami w chmurze, ze względu na tymczasowe warunki, takie jak i dostępności usługi sporadyczne błędy poziom infrastrukturą (powszechnie znane jako błędy przejściowe) mogą wystąpić błędy. Aplikacji uruchomionej na urządzeniu muszą zarządzać połączenia i mechanizmy ponowne nawiązanie połączenia, a także Logika ponawiania, wysyłanie i odbieranie komunikatów. Ponadto wymagania strategii ponawiania prób zależą od intensywnie scenariusz IoT, które poddawana urządzenia i kontekst urządzenia i możliwości.

Aby uprościć łączenie i komunikacji z chmury do urządzenia i urządzenia do chmury, zapewniając niezawodne i kompleksowy sposób łączenia i wysyłanie i odbieranie komunikatów z usługi Azure IoT Hub i staraj się zestawów SDK urządzeń Azure IoT Hub. Deweloperzy można także modyfikować istniejące wdrożenia na opracowanie strategii ponawiania odpowiednie dla danego scenariusza.

W poniższych sekcjach znajdują się odpowiednie funkcje zestawu SDK, obsługujące łączności i niezawodną obsługę komunikatów.

## <a name="connection-and-retry"></a>Połączenie i spróbuj ponownie

Ta sekcja zawiera omówienie dostępnych wzorców ponowne nawiązanie połączenia i spróbuj ponownie, związane z zarządzaniem połączeniami, wytyczne dotyczące implementacji dla przy użyciu różnych zasad ponawiania w aplikacji urządzenia, a odpowiednie interfejsy API dla zestawów SDK urządzeń.

### <a name="error-patterns"></a>Wzorców błędów
Błędy połączenia może się zdarzyć na wielu poziomach:

-  Błędy sieci, takich jak gniazda rozłączonych i błędów rozpoznawania nazw
- Błędy na poziomie protokołu HTTP, AMQP i MQTT transportu, takich jak odłączyć łącza lub sesji wygasła
- Błędy na poziomie aplikacji, które wynikają z dowolnego lokalnego błędów, takich jak nieprawidłowe poświadczenia lub zachowanie, takich jak przekroczenia limitu przydziału lub ograniczenia przepustowości usługi

Zestawy SDK urządzeń wykrywać błędy wszystkich trzech poziomów.  Błędy związane z systemu operacyjnego i błędów sprzętu są wykrywane i nie obsługiwane przez zestawy SDK urządzeń.  Projekt będzie opierać się na [przejściowych błędów obsługi wskazówek dotyczących](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults#general-guidelines) z Centrum architektury platformy Azure.

### <a name="retry-patterns"></a>Ponów próbę wykonania wzorców

Ogólny proces ponawiania prób w przypadku wykrycia błędów połączeń są: 
1. Zestaw SDK wykrywa błąd i skojarzony błąd w sieci, protokół i aplikacji.
2. Używa zestawu SDK, błąd filtr, aby zdecydować, jeśli zależy od typu błędu, spróbuj ponownie należy przeprowadzić.  Jeśli **nieodwracalny błąd** jest identyfikowany przez zestaw SDK, zostaną zatrzymane operacji (połączeń i wysyłania/odbierania) i zestaw SDK powiadomi użytkownika. Wystąpił nieodwracalny błąd jest błąd, który zestaw SDK można zidentyfikować i określić, że nie można odzyskać, na przykład, uwierzytelniania lub błąd nieprawidłowy punkt końcowy.
3. Jeśli **nieodwracalny błąd** jest identyfikowane, zestaw SDK rozpoczyna się ponowić próbę przy użyciu zasady ponawiania określone do momentu wygaśnięcia ważności zdefiniowanego limitu czasu.
4. Po upływie zdefiniowanego limitu czasu, zestaw SDK przestanie próby połączenia lub wysłać i powiadamia użytkownika.
5.  Zestaw SDK pozwala użytkownikom na dołączanie wywołanie zwrotne w celu odbierania zmiany stanu połączenia. 

Zasady ponawiania trzy są dostępne:
- **Wycofywanie Wykładnicze z zakłócenia**: jest to domyślne zasady ponawiania, zastosowane.  Go jest zwykle agresywne na początku, spowalnia, a następnie trafienia Maksymalne opóźnienie, który nie został przekroczony.  Projekt będzie opierać się na [wskazówki dotyczące z Centrum architektury platformy Azure ponawiania prób](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific).
- **Niestandardowe ponawiania**: można implementować niestandardowe zasady ponawiania i wstrzyknięcia go w RetryPolicy w zależności od języka, możesz wybrać. Można zaprojektować zasady ponawiania, które jest odpowiednie dla danego scenariusza.  To nie jest dostępne w zestawie SDK C.
- **Bez ponawiania**: istnieje możliwość ustawienia zasad ponawiania "bez ponawiania," co powoduje wyłączenie Logika ponawiania.  Zestaw SDK próbuje połączyć jeden raz i wysłać wiadomość raz, przy założeniu, że połączenie zostanie nawiązane. Ta zasada wykorzystania w przypadkach, w przypadku, gdy istnieją wątpliwości przepustowości lub kosztów.   Jeśli ta opcja jest wybrana, wiadomości, których nie można wysłać zostaną utracone i nie można go odzyskać. 

### <a name="retry-policy-apis"></a>Interfejsy API zasady ponawiania

   | SDK | Metoda SetRetryPolicy | Implementacji zasad | Wytyczne dotyczące implementacji |
   |-----|----------------------|--|--|
   |  IOS-C/Python  | [IOTHUB_CLIENT_RESULT IoTHubClient_SetRetryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/2018-05-04/iothub_client/inc/iothub_client.h#L188)        | **Domyślne**: [IOTHUB_CLIENT_RETRY_EXPONENTIAL_BACKOFF](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Niestandardowy:** użycia dostępnych [retryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Bez ponawiania:** [IOTHUB_CLIENT_RETRY_NONE](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)  | [Implementacja języka C/Python/systemu iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#)  |
   | Java| [SetRetryPolicy](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.sdk.iot.device._device_client_config.setretrypolicy?view=azure-java-stable)        | **Domyślne**: [ExponentialBackoffWithJitter, klasa](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)<BR>**Niestandardowy:** zaimplementować [interfejsu RetryPolicy](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/RetryPolicy.java)<BR>**Bez ponawiania:** [NoRetry, klasa](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)  | [Implementacja języka Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md) |[Zestaw SDK platformy .NET](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
   | .NET| [DeviceClient.SetRetryPolicy](/dotnet/api/microsoft.azure.devices.client.deviceclient.setretrypolicy?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_SetRetryPolicy_Microsoft_Azure_Devices_Client_IRetryPolicy) | **Domyślne**: [ExponentialBackoff, klasa](/dotnet/api/microsoft.azure.devices.client.exponentialbackoff?view=azure-dotnet)<BR>**Niestandardowy:** zaimplementować [IRetryPolicy interfejsu](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.iretrypolicy?view=azure-dotnet)<BR>**Bez ponawiania:** [NoRetry, klasa](/dotnet/api/microsoft.azure.devices.client.noretry?view=azure-dotnet) | [Implementacja języka C#]() |
   | Węzeł| [setRetryPolicy](/javascript/api/azure-iot-device/client?view=azure-iot-typescript-latest#azure_iot_device_Client_setRetryPolicy) | **Domyślne**: [ExponentialBackoffWithJitter, klasa](/javascript/api/azure-iot-common/exponentialbackoffwithjitter?view=azure-iot-typescript-latest)<BR>**Niestandardowy:** zaimplementować [interfejsu RetryPolicy](/javascript/api/azure-iot-common/retrypolicy?view=azure-iot-typescript-latest)<BR>**Bez ponawiania:** [NoRetry, klasa](/javascript/api/azure-iot-common/noretry?view=azure-iot-typescript-latest) | [Implementacja węzła](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them) |
   

Poniżej przedstawiono przykłady kodu ilustrujące ten przepływ. 

#### <a name="net-implementation-guidance"></a>Wytyczne dotyczące implementacji platformy .NET

Poniższy przykład kodu pokazuje jak zdefiniować i ustawić domyślną zasadę ponownych prób:

   ```csharp
   # define/set default retry policy
   RetryPolicy retryPolicy = new ExponentialBackoff(int.MaxValue, TimeSpan.FromMilliseconds(100), TimeSpan.FromSeconds(10), TimeSpan.FromMilliseconds(100));
   SetRetryPolicy(retryPolicy);
   ```

Aby uniknąć wysokie użycie procesora CPU, ponowne próby są ograniczane, jeśli kod nie powiedzie się natychmiast (na przykład, gdy nie ma żadnych sieci ani trasy do miejsca docelowego), aby minimalny czas, aby wykonać następne ponowienie próby wynosi 1 s. 

Jeśli usługa odpowiada błędem ograniczania przepustowości, zasady ponawiania różni się i nie można zmienić za pośrednictwem publicznego interfejsu API:

   ```csharp
   # throttled retry policy
   RetryPolicy retryPolicy = new ExponentialBackoff(RetryCount, TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(60), TimeSpan.FromSeconds(5));
   SetRetryPolicy(retryPolicy);
   ```

Mechanizm ponawiania prób zatrzyma się po `DefaultOperationTimeoutInMilliseconds`, który jest obecnie ustawiona w ciągu 4 minut.

#### <a name="other-languages-implementation-guidance"></a>Wytyczne dotyczące implementacji w innych językach
Inne języki na ten temat można znaleźć w dokumentacji wdrożenia poniżej.  Przykłady ilustrujące użycie zasady ponawiania, interfejsy API są udostępniane w repozytorium.
- [Zestaw SDK języka C/Python/systemu iOS](https://github.com/azure/azure-iot-sdk-c)
- [Zestaw SDK platformy .NET](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
- [Zestaw SDK Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)
- [Węzeł zestawu SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

