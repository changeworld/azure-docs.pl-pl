---
title: Jak zarządzać łączności i niezawodna obsługa komunikatów za pomocą zestawów SDK urządzeń Azure IoT Hub
description: Dowiedz się, jak poprawić łączności urządzeń i funkcji komunikatów przy użyciu zestawów SDK urządzeń Azure IoT Hub
services: iot-hub
author: yzhong94
ms.author: yizhon
ms.date: 07/07/2018
ms.topic: article
ms.service: iot-hub
ms.openlocfilehash: 9180c27e64f26c05e6e16007b74f9aa8a98bcfe5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61440305"
---
# <a name="manage-connectivity-and-reliable-messaging-by-using-azure-iot-hub-device-sdks"></a>Zarządzanie łącznością i niezawodna obsługa komunikatów za pomocą zestawów SDK urządzeń Azure IoT Hub

Ten artykuł zawiera ogólne wskazówki ułatwiające projektowanie aplikacji urządzenia, które są bardziej odporne na błędy. Prezentuje sposób korzystać z łączności i niezawodne funkcje obsługi komunikatów w zestawy SDK urządzeń Azure IoT. Celem tego przewodnika jest pomocne w zarządzaniu w następujących scenariuszach:

* Naprawianie połączenia sieciowego

* Przełączanie między różnych połączeń sieciowych

* Ponowne nawiązywanie połączenia z powodu błędów przejściowych połączenia usługi

Szczegóły implementacji, zależy od języka. Aby uzyskać więcej informacji zobacz dokumentację interfejsu API lub określonego zestawu SDK:

* [C/Python/iOS SDK](https://github.com/azure/azure-iot-sdk-c)

* [Zestaw SDK platformy .NET](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)

* [Zestaw SDK Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [Węzeł zestawu SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

## <a name="designing-for-resiliency"></a>Projektowanie pod kątem odporności

Urządzenia IoT często polegają na — ciągły lub niestabilny połączeń sieciowych (na przykład GSM lub satelity). Gdy urządzenia interakcji z usługami w chmurze z powodu sporadycznych usługi dostępności i poziom infrastrukturą lub przejściowe błędy mogą wystąpić błędy. Aplikację, która działa na urządzeniu musi zarządzać mechanizmy połączenia, ponowne połączenie i Logika ponawiania do wysyłania i odbierania komunikatów. Ponadto wymagania strategii ponawiania prób zależą od intensywnie scenariusz IoT urządzenia, kontekst, możliwości.

Zestawy SDK urządzeń Azure IoT Hub mają na celu uproszczenie łączenia i komunikacji z chmury do urządzenia i urządzenia do chmury. Te zestawy SDK zapewniają niezawodny sposób łączenia usługi Azure IoT Hub i kompleksowy zestaw opcji dla wysyłania i odbierania komunikatów. Deweloperzy można również zmodyfikować istniejącą implementacją dostosować lepszą strategię ponawiania dla danego scenariusza.

W poniższych sekcjach znajdują się odpowiednie funkcje zestawu SDK, obsługujące łączności i niezawodną obsługę komunikatów.

## <a name="connection-and-retry"></a>Połączenie i spróbuj ponownie

Ta sekcja zawiera omówienie dostępnych wzorców ponowne połączenie i spróbuj ponownie, związane z zarządzaniem połączeniami. Jego szczegóły implementacji wskazówki dotyczące korzystania z różnych zasad ponawiania w aplikacji urządzenia i wyświetla odpowiednie interfejsy API z zestawów SDK urządzeń.

### <a name="error-patterns"></a>Wzorców błędów

Błędy połączenia może się zdarzyć na wielu poziomach:

* Błędy sieciowe: Rozłączono błędów rozpoznania gniazda i nazwę

* Protokół błędy na poziomie transportu HTTP, AMQP i MQTT: odłączyć łącza lub wygasłych sesji

* Błędy na poziomie aplikacji, wynikających z dowolnym lokalnym pomyłek: nieprawidłowe poświadczenia lub zachowanie usługi, (na przykład, przekroczenie limitu przydziału lub ograniczanie przepustowości)

Zestawy SDK urządzeń wykrywania błędów na wszystkich trzech poziomach. Błędy związane z systemu operacyjnego i błędów sprzętu są wykrywane i nie obsługiwane przez zestawy SDK urządzeń. Projekt zestawu SDK będzie opierać się na [przejściowych błędów obsługi wskazówek dotyczących](/azure/architecture/best-practices/transient-faults#general-guidelines) z Centrum architektury platformy Azure.

### <a name="retry-patterns"></a>Ponów próbę wykonania wzorców

W poniższych krokach opisano proces ponawiania prób w przypadku wykrycia błędów połączenia:

1. Zestaw SDK wykrywa błąd i skojarzony błąd w sieci, protokół i aplikacji.

2. Zestaw SDK używa filtra błąd, aby określić typ błędu i zdecydować, jeśli jest potrzebna ponowna próba.

3. Jeśli zestaw SDK identyfikuje **nieodwracalny błąd**, operacje, takie jak połączenia, wysyłania i odbierania są zatrzymywane. Zestaw SDK powiadomi użytkownika. Przykładami nieodwracalne błędy błąd uwierzytelniania i błąd nieprawidłowy punkt końcowy.

4. Jeśli zestaw SDK identyfikuje **nieodwracalny błąd**, ponowną próbą zgodnie z zasadami ponawiania określony, dopóki nie upłynie zdefiniowanego limitu czasu.  Należy zauważyć, że zestaw SDK używa **Wartość wykładnicza wycofywania z zakłócenia** zasady ponawiania domyślnie.
5. Po wygaśnięciu limitu czasu zdefiniowanego zestawu SDK zatrzymuje próby połączenia lub wysyłania. Użytkownik otrzyma powiadomienie.

6. Zestaw SDK pozwala użytkownikom na dołączanie wywołanie zwrotne w celu odbierania zmiany stanu połączenia.

Zestawy SDK udostępniają trzy zasady ponawiania:

* **Wycofywanie Wykładnicze z zakłócenia**: Te domyślne zasady ponawiania są zwykle agresywne na początku i spowolnić wraz z upływem czasu, aż do napotkania Maksymalne opóźnienie. Projekt będzie opierać się na [wskazówki dotyczące z Centrum architektury platformy Azure ponawiania prób](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific). 

* **Niestandardowe ponawiania**: W przypadku niektórych języków zestawu SDK można zaprojektować niestandardowe zasady ponawiania są lepiej dostosowane do danego scenariusza i wstawić go do RetryPolicy. Niestandardowe ponawiania jest niedostępna na zestawu SDK języka C.

* **Bez ponawiania**: Można ustawić zasady ponawiania, aby "bez ponawiania," co powoduje wyłączenie Logika ponawiania. Zestaw SDK próbuje połączyć jeden raz i wysłać wiadomość raz, przy założeniu, że połączenie zostanie nawiązane. Zasada ta jest zazwyczaj używana w scenariuszach z przepustowości lub koszt dotyczy. Jeśli wybierzesz tę opcję, wiadomości, których nie można wysłać zostaną utracone i nie można go odzyskać.

### <a name="retry-policy-apis"></a>Interfejsy API zasady ponawiania

   | SDK | Metoda SetRetryPolicy | Implementacji zasad | Wytyczne dotyczące implementacji |
   |-----|----------------------|--|--|
   |  C/Python/iOS  | [IOTHUB_CLIENT_RESULT IoTHubClient_SetRetryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/2018-05-04/iothub_client/inc/iothub_client.h#L188)        | **Domyślne**: [IOTHUB_CLIENT_RETRY_EXPONENTIAL_BACKOFF](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Niestandardowy:** użycia dostępnych [retryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Bez ponawiania:** [IOTHUB_CLIENT_RETRY_NONE](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)  | [Implementacja języka C/Python/systemu iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#)  |
   | Java| [SetRetryPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.deviceclientconfig.setretrypolicy?view=azure-java-stable)        | **Domyślne**: [Klasa ExponentialBackoffWithJitter](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)<BR>**Niestandardowy:** zaimplementować [interfejsu RetryPolicy](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/RetryPolicy.java)<BR>**Bez ponawiania:** [NoRetry, klasa](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)  | [Implementacja języka Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md) |
   | .NET| [DeviceClient.SetRetryPolicy](/dotnet/api/microsoft.azure.devices.client.deviceclient.setretrypolicy?view=azure-dotnet) | **Domyślne**: [Klasa ExponentialBackoff](/dotnet/api/microsoft.azure.devices.client.exponentialbackoff?view=azure-dotnet)<BR>**Niestandardowy:** zaimplementować [IRetryPolicy interfejsu](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.iretrypolicy?view=azure-dotnet)<BR>**Bez ponawiania:** [NoRetry, klasa](/dotnet/api/microsoft.azure.devices.client.noretry?view=azure-dotnet) | [Implementacja języka C#](https://github.com/Azure/azure-iot-sdk-csharp) | |
   | Węzeł| [setRetryPolicy](/javascript/api/azure-iot-device/client?view=azure-iot-typescript-latest) | **Domyślne**: [Klasa ExponentialBackoffWithJitter](/javascript/api/azure-iot-common/exponentialbackoffwithjitter?view=azure-iot-typescript-latest)<BR>**Niestandardowy:** zaimplementować [interfejsu RetryPolicy](/javascript/api/azure-iot-common/retrypolicy?view=azure-iot-typescript-latest)<BR>**Bez ponawiania:** [NoRetry, klasa](/javascript/api/azure-iot-common/noretry?view=azure-iot-typescript-latest) | [Implementacja węzła](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them) |

Poniższe przykłady kodu przedstawiają ten przepływ:

#### <a name="net-implementation-guidance"></a>Wytyczne dotyczące implementacji platformy .NET

Poniższy przykład kodu pokazuje, jak zdefiniować i ustawić domyślną zasadę ponownych prób:

   ```csharp
   # define/set default retry policy
   RetryPolicy retryPolicy = new ExponentialBackoff(int.MaxValue, TimeSpan.FromMilliseconds(100), TimeSpan.FromSeconds(10), TimeSpan.FromMilliseconds(100));
   SetRetryPolicy(retryPolicy);
   ```

Aby uniknąć wysokie użycie procesora CPU, ponowne próby są ograniczone, jeśli kod natychmiast kończy się niepowodzeniem. Na przykład, kiedy nie ma sieci lub trasę do lokalizacji docelowej. Minimalny czas, aby wykonać następne ponowienie próby wynosi 1 s.

Jeśli usługa odpowiada za pomocą błędów ograniczania przepustowości, zasady ponawiania różni się i nie można zmienić za pośrednictwem publicznego interfejsu API:

   ```csharp
   # throttled retry policy
   RetryPolicy retryPolicy = new ExponentialBackoff(RetryCount, TimeSpan.FromSeconds(10), 
     TimeSpan.FromSeconds(60), TimeSpan.FromSeconds(5)); SetRetryPolicy(retryPolicy);
   ```

Mechanizm ponawiania prób zatrzyma się po `DefaultOperationTimeoutInMilliseconds`, który jest obecnie ustawiona w ciągu 4 minut.

#### <a name="other-languages-implementation-guidance"></a>Wytyczne dotyczące implementacji w innych językach

Aby uzyskać przykłady kodu w innych językach Przejrzyj następujące dokumenty implementacji. Repozytorium zawiera przykłady, które pokazują użycie zasady ponawiania interfejsów API.

* [C/Python/iOS SDK](https://github.com/azure/azure-iot-sdk-c)

* [Zestaw SDK platformy .NET](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)

* [Zestaw SDK Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [Węzeł zestawu SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

## <a name="next-steps"></a>Kolejne kroki

* [Korzystanie z zestawów SDK urządzenia i usługi](./iot-hub-devguide-sdks.md)

* [Korzystanie z zestawu SDK urządzenia IoT dla języka C](./iot-hub-device-sdk-c-intro.md)

* [Opracowywanie zawartości dla urządzeń z ograniczeniami](./iot-hub-devguide-develop-for-constrained-devices.md)

* [Opracowywanie zawartości dla urządzeń przenośnych](./iot-hub-how-to-develop-for-mobile-devices.md)

* [Rozwiązywanie problemów z odłącza urządzenia](iot-hub-troubleshoot-connectivity.md)