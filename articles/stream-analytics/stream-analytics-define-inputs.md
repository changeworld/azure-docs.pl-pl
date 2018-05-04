---
title: Strumień danych jako dane wejściowe do usługi Azure Stream Analytics
description: Więcej informacji na temat konfigurowania połączenia danych w usłudze Azure Stream Analytics. Dane wejściowe zawierają strumień danych zdarzeń i również danych referencyjnych.
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/27/2018
ms.openlocfilehash: 2b2ef68622f96d87a25d203d3d67aa0877397072
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2018
---
# <a name="stream-data-as-input-into-stream-analytics"></a>Strumień danych jako dane wejściowe do usługi analiza strumienia

Analiza strumienia ma najwyższej jakości integracji strumieni danych Azure jako dane wejściowe z trzy typy zasobów:
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) 

Te zasoby wejściowy może na żywo w tej samej subskrypcji platformy Azure jako zadania usługi analiza strumienia lub z innej subskrypcji.

### <a name="compression"></a>Kompresja
Analiza strumienia obsługuje kompresję we wszystkich źródeł wejściowego strumienia danych. Odwołanie do aktualnie obsługiwane typy to: None, GZip i kompresję Deflate. Obsługa kompresji nie jest dostępna dla danych referencyjnych. Jeśli format wejściowy jest dane Avro jest skompresowany, jest obsługiwane przezroczysty. Nie trzeba określać typ kompresji serializacji Avro. 

## <a name="create-or-edit-inputs"></a>Tworzenie i edytowanie danych wejściowych
Aby utworzyć nowe dane wejściowe i listy lub edytować istniejące dane wejściowe zadania przesyłania strumieniowego, można użyć portalu Azure:
1. Otwórz [portalu Azure](https://portal.azure.com) Aby znaleźć i wybrać zadanie usługi Stream Analytics.
2. Wybierz **dane wejściowe** opcję w obszarze **ustawienia** nagłówka. 
4. **Dane wejściowe** stronie są wyświetlane wszystkie istniejące dane wejściowe. 
5. Na **dane wejściowe** wybierz pozycję **dodać strumienia danych wejściowych** lub **Dodaj odwołanie do danych wejściowych** aby otworzyć stronę szczegółów.
6. Wybierz istniejące dane wejściowe do edytowania szczegółów i wybierz **zapisać** do edycji istniejących danych wejściowych.
7. Wybierz **testu** na stronie dane wejściowe, aby sprawdzić, czy opcje połączenia są prawidłowe i jest uruchomiony. 
8. Kliknij prawym przyciskiem myszy nazwę istniejące dane wejściowe, a następnie wybierz **przykładowe dane z danych wejściowych** zgodnie z potrzebami dla dalszego badania.


## <a name="stream-data-from-event-hubs"></a>Strumień danych z usługi Event Hubs

Usługa Azure Event Hubs zapewnia wysoką skalowalnością ingestors zdarzeń publikowania / subskrypcji. Centrum zdarzeń można zbierać miliony zdarzeń na sekundę, dzięki czemu możliwe jest przetwarzanie i analizowanie olbrzymich ilości danych wytworzonych przez podłączone urządzenia i aplikacje. Centra zdarzeń i analiza strumienia razem umożliwiają end-to-end rozwiązania do analiz w czasie rzeczywistym. Centra zdarzeń umożliwiają źródła danych zdarzeń na platformie Azure w czasie rzeczywistym i zadania usługi analiza strumienia może przetwarzać tych zdarzeń w czasie rzeczywistym. Na przykład możesz wysłać kliknięć w sieci web, odczyty czujników lub online dziennika zdarzeń do usługi Event Hubs. Następnie można utworzyć zadania usługi analiza strumienia do użycia usługi Event Hubs jako strumieni danych wejściowych w czasie rzeczywistym filtrowanie, agregację i korelacji.

Sygnatura czasowa domyślne zdarzenia pochodzące z usługi Event Hubs w Stream Analytics jest sygnatury czasowej, które zdarzenia w przypadku odebrania koncentratora, który jest `EventEnqueuedUtcTime`. Do przetwarzania danych jako strumień przy użyciu sygnatury czasowej w przypadku ładunku, należy użyć [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) — słowo kluczowe.

### <a name="consumer-groups"></a>Grupy odbiorców
Należy skonfigurować każdego Centrum zdarzeń usługi analiza strumienia danych wejściowych ma własną grupę odbiorców. Jeśli zadanie zawiera samosprzężenie lub składa się z wielu danych wejściowych, niektóre dane wejściowe mogą odczytać przez więcej niż jeden czytnik poniżej. Taka sytuacja wpływa na liczbę czytników w grupie jednego konsumenta. Aby zapobiec przekroczeniu limitu centra zdarzeń w pięciu czytników dla każdej grupy odbiorców dla każdej partycji, jest najlepszym rozwiązaniem, aby wyznaczyć grupy odbiorców dla każdego zadania usługi analiza strumienia. Istnieje również limit 20 grup odbiorców, na Centrum zdarzeń. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z usługi Azure Stream Analytics z odbiorników Centrum zdarzeń](stream-analytics-event-hub-consumer-groups.md).

### <a name="stream-data-from-event-hubs"></a>Strumień danych z usługi Event Hubs
W poniższej tabeli opisano każdej właściwości w **wprowadzania nowych** strony w portalu Azure do strumienia danych wejściowych z Centrum zdarzeń:

| Właściwość | Opis |
| --- | --- |
| **Alias wejściowy** |Przyjazna nazwa umożliwia w zapytaniu zadanie odwoływać danych wejściowych. |
| **Subskrypcja** | Wybierz subskrypcję, w której istnieje zasób Centrum zdarzeń. | 
| **Przestrzeń nazw Centrum zdarzeń** | Przestrzeń nazw Centrum zdarzeń to kontener dla zestawu jednostek do obsługi komunikatów. Podczas tworzenia nowego Centrum zdarzeń można również utworzyć przestrzeń nazw. |
| **Nazwa Centrum zdarzeń** | Nazwa Centrum zdarzeń do użycia jako dane wejściowe. |
| **Nazwa zasad Centrum zdarzeń** | Zasady dostępu współdzielonego, który zapewnia dostęp do Centrum zdarzeń. Wszystkie zasady dostępu współdzielonego ma nazwę uprawnienia ustawić i klucze dostępu. Ta opcja jest automatycznie wypełniane w, chyba że zostanie wybrana opcja zapewnienie ustawień Centrum zdarzeń ręcznie.|
| **Grupy konsumentów Centrum zdarzeń** (zalecane) | Zdecydowanie zaleca się używania grupy odbiorców różne dla każdego zadania usługi analiza strumienia. Ten ciąg identyfikuje grupę odbiorców do użycia na potrzeby wysyłania danych z Centrum zdarzeń. Jeśli zostanie określona żadna grupa odbiorców, zadanie usługi Stream Analytics korzysta z $Default grupy odbiorców.  |
| **Format serializacji zdarzeń** | Format serializacji (JSON, CSV lub Avro) przychodzącego strumienia danych.  Upewnij się, była zgodna ze specyfikacją formatu JSON, a nie zawiera cyfry 0 dla liczb dziesiętnych. |
| **Kodowanie** | UTF-8 jest obecnie obsługiwany tylko format kodowania. |
| **Typ kompresji zdarzenia** | Typ kompresji używany do odczytu przychodzącego strumienia danych, takich jak brak (ustawienie domyślne), GZip i Deflate. |

Gdy dane pochodzą z elementu wejściowego strumienia Centrum zdarzeń, masz dostęp do następujących pól metadanych w kwerendzie Stream Analytics:

| Właściwość | Opis |
| --- | --- |
| **EventProcessedUtcTime** |Data i czas przetwarzania zdarzenia przez usługę Stream Analytics. |
| **EventEnqueuedUtcTime** |Data i godzina zdarzenia otrzymała centrów zdarzeń. |
| **partitionId** |Identyfikator liczony od zera partycję adapter wejścia. |

Na przykład przy użyciu tych pól, można napisać zapytanie, jak w następującym przykładzie:

```sql
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
```

> [!NOTE]
> Korzystając z Centrum zdarzeń jako punktu końcowego dla tras Centrum IoT, możesz uzyskać dostępu do przy użyciu medadata Centrum IoT [funkcja GetMetadataPropertyValue](https://msdn.microsoft.com/library/azure/mt793845.aspx).
> 

## <a name="stream-data-from-iot-hub"></a>Strumień danych z Centrum IoT
Centrum Iot Azure to wysoce skalowalna publikowania / subskrypcji zoptymalizowane pod kątem scenariuszach IoT systemem zbierania zdarzeń.

Sygnatura czasowa domyślne zdarzenia pochodzące z Centrum IoT w Stream Analytics jest sygnatury czasowej, które dotarły zdarzenia w Centrum IoT, który jest `EventEnqueuedUtcTime`. Do przetwarzania danych jako strumień przy użyciu sygnatury czasowej w przypadku ładunku, należy użyć [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) — słowo kluczowe.

> [!NOTE]
> Tylko wiadomości wysyłane z `DeviceClient` właściwości mogą być przetwarzane.
> 

### <a name="consumer-groups"></a>Grupy odbiorców
Należy skonfigurować każdy Centrum IoT Analytics strumień wejściowy ma własną grupę odbiorców. Jeśli zadanie zawiera samosprzężenie lub składa się z wielu danych wejściowych, niektóre dane wejściowe mogą odczytać przez więcej niż jeden czytnik poniżej. Taka sytuacja wpływa na liczbę czytników w grupie jednego konsumenta. Aby zapobiec przekroczeniu limitu Centrum IoT Azure pięć czytników dla każdej grupy odbiorców dla każdej partycji, jest najlepszym rozwiązaniem, aby wyznaczyć grupy odbiorców dla każdego zadania usługi analiza strumienia.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>Konfigurowanie Centrum IoT jako strumienia danych wejściowych
W poniższej tabeli opisano każdej właściwości w **wprowadzania nowych** strony w portalu Azure podczas konfigurowania Centrum IoT jako strumienia danych wejściowych.

| Właściwość | Opis |
| --- | --- |
| **Alias wejściowy** | Przyjazna nazwa umożliwia w zapytaniu zadanie odwoływać danych wejściowych.|
| **Subskrypcja** | Wybierz subskrypcję, w której istnieje zasób Centrum IoT. | 
| **IoT Hub** | Nazwa centrum IoT można użyć jako danych wejściowych. |
| **punkt końcowy** | Punktu końcowego Centrum IoT.|
| **Nazwa zasady dostępu współdzielonego** | Zasady dostępu współdzielonego, który zapewnia dostęp do Centrum IoT. Wszystkie zasady dostępu współdzielonego ma nazwę uprawnienia ustawić i klucze dostępu. |
| **Klucz zasady dostępu współdzielonego** | Klucz dostępu współdzielonego, używany do autoryzacji dostępu do Centrum IoT.  Ta opcja jest automatycznie wypełniane w, chyba że zostanie wybrana opcja zapewnienie Centrum Iot ustawień ręcznie. |
| **Grupy odbiorców** | Zdecydowanie zaleca się użycie grupy konsumentów różnych dla każdego zadania usługi analiza strumienia. Grupy odbiorców służy do pozyskiwania danych z Centrum IoT. Analiza strumienia używa grupa użytkowników $Default, chyba że określono inaczej.  |
| **Format serializacji zdarzeń** | Format serializacji (JSON, CSV lub Avro) przychodzącego strumienia danych.  Upewnij się, była zgodna ze specyfikacją formatu JSON, a nie zawiera cyfry 0 dla liczb dziesiętnych. |
| **Kodowanie** | UTF-8 jest obecnie obsługiwany tylko format kodowania. |
| **Typ kompresji zdarzenia** | Typ kompresji używany do odczytu przychodzącego strumienia danych, takich jak brak (ustawienie domyślne), GZip i Deflate. |


Użycie danych strumienia z Centrum IoT, masz dostęp do następujących pól metadanych w kwerendzie Stream Analytics:

| Właściwość | Opis |
| --- | --- |
| **EventProcessedUtcTime** | Data i godzina, który był przetwarzany zdarzenia. |
| **EventEnqueuedUtcTime** | Data i godzina, które zdarzenie zostało odebrane przez Centrum IoT. |
| **partitionId** | Identyfikator liczony od zera partycję adapter wejścia. |
| **IoTHub.MessageId** | Identyfikator, który służy do skorelowania dwukierunkowej komunikacji w Centrum IoT. |
| **IoTHub.CorrelationId** | Identyfikator, który jest używany w odpowiedzi komunikat i opinie w Centrum IoT. |
| **IoTHub.ConnectionDeviceId** | Identyfikator uwierzytelniania używany do wysłania tej wiadomości. Ta wartość jest dołączana do komunikatów servicebound przez Centrum IoT. |
| **IoTHub.ConnectionDeviceGenerationId** | Identyfikator generacji uwierzytelnionego urządzenia, którego użyto do wysłania tej wiadomości. Ta wartość jest dołączana do komunikatów servicebound przez Centrum IoT. |
| **IoTHub.EnqueuedTime** | Czas, kiedy wiadomość dotarła Centrum IoT. |
| **IoTHub.StreamId** | Właściwość zdarzenie niestandardowe dodane przez urządzenie nadawcy. |


## <a name="stream-data-from-blob-storage"></a>Strumień danych z magazynu obiektów Blob
W przypadku scenariuszy z dużych ilości danych bez struktury mają być przechowywane w chmurze Azure Blob storage oferuje ekonomiczne i skalowalne rozwiązanie. Dane w magazynie obiektów Blob jest zazwyczaj uważana za przechowywanych danych. Jednak obiektu blob danych mogą być przetwarzane jako strumień danych Stream Analytics. 

Przetwarzanie dziennika jest często używane scenariusz korzystania z usługi Stream Analytics przy użyciu magazynu obiektów Blob w danych wejściowych. W tym scenariuszu pliki danych telemetrycznych została przechwycona z systemu i musi zostać przeanalizowany i przetwarzane w celu wyodrębnienia istotnych danych.

Sygnatura czasowa domyślne zdarzenia magazynu obiektów Blob w Stream Analytics jest sygnatura czasowa czy ostatniej modyfikacji obiektu blob, który jest `BlobLastModifiedUtcTime`. Do przetwarzania danych jako strumień przy użyciu sygnatury czasowej w przypadku ładunku, należy użyć [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) — słowo kluczowe.

Wejść w formacie CSV *wymagają* wiersz nagłówka do definiowania pól dla zestawu danych i wszystkie pola wiersz nagłówka musi być unikatowa.

Analiza strumienia nie obsługuje obecnie deserializowania wiadomości AVRO generowane przez Centrum zdarzeń przechwytywania lub kontenera magazynu Azure Centrum IoT niestandardowe punktu końcowego.

> [!NOTE]
> Analiza strumienia obsługuje dodawanie zawartości do istniejącego pliku obiektu blob. Analiza strumienia zostaną wyświetlone tylko raz każdego pliku, a wszelkie zmiany wprowadzone w pliku, gdy zadanie może odczytywać dane nie są przetwarzane. Najlepszym rozwiązaniem jest jednocześnie Przekaż wszystkie dane dla pliku blob, a następnie dodaj dodatkowe nowszych zdarzeń do pliku innego, nowych obiektów blob.
> 

### <a name="configure-blob-storage-as-a-stream-input"></a>Konfigurowanie magazynu obiektów Blob jako strumień wejściowy 

W poniższej tabeli opisano każdej właściwości w **wprowadzania nowych** strony w portalu Azure podczas konfigurowania magazynu obiektów Blob jako strumienia danych wejściowych.

| Właściwość | Opis |
| --- | --- |
| **Alias wejściowy** | Przyjazna nazwa umożliwia w zapytaniu zadanie odwoływać danych wejściowych. |
| **Subskrypcja** | Wybierz subskrypcję, w której istnieje zasób Centrum IoT. | 
| **Konto magazynu** | Nazwa konta magazynu, w którym znajdują się pliki obiektu blob. |
| **Klucz konta magazynu** | Klucz tajny, skojarzone z kontem magazynu. Ta opcja jest automatycznie wypełniane w, chyba że zaznaczono opcję, aby zapewnić ustawienia magazynu obiektów Blob ręcznie. |
| **Kontener** | Kontener obiektu blob danych wejściowych. Kontenery umożliwiają logiczne grupowanie dla obiektów blob przechowywanych w usłudze Microsoft Azure Blob. Przekazywanie obiektu blob do usługi magazynu obiektów Blob platformy Azure, należy określić kontener dla tego obiektu blob. Można wybrać **Użyj istniejącego** kontenera lub **Utwórz nowy** Aby utworzyć nowy kontener.|
| **Wzorzec ścieżki** (opcjonalnie) | Ścieżka plików używana do lokalizowania obiektów blob w określonym kontenerze. W ścieżce można określić co najmniej jedno wystąpienie następujących trzech zmiennych: `{date}`, `{time}`, lub `{partition}`<br/><br/>Przykład 1: `cluster1/logs/{date}/{time}/{partition}`<br/><br/>Przykład 2: `cluster1/logs/{date}`<br/><br/>`*` Znak nie jest dozwolona wartość prefiksu ścieżki. Jedyne prawidłowe <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">znaków obiektów blob platformy Azure</a> są dozwolone. |
| **Format daty** (opcjonalnie) | Jeśli użyjesz zmiennej daty w ścieżce, format daty, w którym pliki są organizowane. Przykład: `YYYY/MM/DD` |
| **Format czasu** (opcjonalnie) |  Jeśli użyjesz zmiennej czasu w ścieżce, format czasu, w którym pliki są organizowane. Obecnie jest to jedyna obsługiwana wartość `HH` godzin. |
| **Format serializacji zdarzeń** | Format serializacji (JSON, CSV lub Avro) przychodzącego strumienia danych.  Upewnij się, była zgodna ze specyfikacją formatu JSON, a nie zawiera cyfry 0 dla liczb dziesiętnych. |
| **Kodowanie** | Dla woluminu CSV i JSON UTF-8 jest obecnie obsługiwany tylko format kodowania. |
| **Kompresja** | Typ kompresji używany do odczytu przychodzącego strumienia danych, takich jak brak (ustawienie domyślne), GZip i Deflate. |

Gdy dane pochodzą ze źródła magazynu obiektów Blob, masz dostęp do następujących pól metadanych w kwerendzie Stream Analytics:

| Właściwość | Opis |
| --- | --- |
| **BlobName** |Nazwa zdarzenia dostarczone z blob danych wejściowych. |
| **EventProcessedUtcTime** |Data i czas przetwarzania zdarzenia przez usługę Stream Analytics. |
| **BlobLastModifiedUtcTime** |Data i godzina ostatniej modyfikacji obiektu blob. |
| **partitionId** |Identyfikator liczony od zera partycję adapter wejścia. |

Na przykład przy użyciu tych pól, można napisać zapytanie, jak w następującym przykładzie:

```sql
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
```

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Szybki Start: Tworzenie zadania usługi analiza strumienia za pomocą portalu Azure](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
