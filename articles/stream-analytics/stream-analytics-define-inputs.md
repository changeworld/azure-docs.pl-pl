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
ms.date: 04/19/2018
ms.openlocfilehash: 5ebf2d1025c8f9469a83a408cb79e3d944a601bc
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="stream-data-as-input-into-stream-analytics"></a>Strumień danych jako dane wejściowe do usługi analiza strumienia

Analiza strumienia akceptuje przychodzące dane z kilku rodzajów źródeł zdarzeń. Połączenie danych podana jako dane wejściowe do zadania usługi Stream Analytics jest określany jako zadanie *wejściowych*. 

Analiza strumienia ma najwyższej jakości integracji strumieni danych Azure jako dane wejściowe z trzy typy zasobów:
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) 

Te zasoby wejściowy może na żywo w tej samej subskrypcji platformy Azure jako zadania usługi analiza strumienia lub z innej subskrypcji.

## <a name="compare-stream-and-reference-inputs"></a>Porównanie odwołanie i strumienia danych wejściowych
Jak przekazania danych do źródła danych, ma używane przez zadanie usługi analiza strumienia i przetwarzane w czasie rzeczywistym. Dane wejściowe są podzielone na dwa typy: dane strumienia danych wejściowych i odwołują się dane wejściowe dane.

### <a name="data-stream-input"></a>Dane wejściowe strumienia danych
Strumień danych jest niepowiązany sekwencji zdarzeń w czasie. Zadania usługi analiza strumienia musi zawierać co najmniej jednego danych elementu wejściowego strumienia. Centra zdarzeń, Centrum IoT i magazynu obiektów Blob są obsługiwane jako źródeł dla wejścia strumienia danych. Centra zdarzeń są używane do zbierania strumieni zdarzeń z wielu urządzeń i usług. Strumienie te mogą obejmować źródła działań mediów społecznościowych, handlu standardowych informacji lub dane z czujników. Centra IoT są optymalizowane w celu zbierania danych z połączonych urządzeń w scenariuszach Internetu rzeczy (IoT).  Magazyn obiektów blob może służyć jako źródło danych wejściowych do wprowadzania danych zbiorczego jako strumienia, takich jak pliki dziennika.  

### <a name="reference-data-input"></a>Odwołanie do danych wejściowych
Analiza strumienia obsługuje również znane jako dane wejściowe *danych referencyjnych*. Jest to dane pomocnicze, który jest statycznych lub który zmienia się powoli. Dane referencyjne zwykle jest używana do wykonywania korelacji i wyszukiwania. Na przykład można sprzęgnąć danych w danych wejściowych strumienia z danymi w danych referencyjnych, tak jak będzie wykonywać sprzężenia SQL do odszukania wartości statyczne. Magazyn obiektów Blob Azure jest obecnie obsługiwane tylko źródła danych wejściowych danych referencyjnych. Odwołanie do źródła danych typu blob są ograniczone do 100 MB rozmiar.

Aby dowiedzieć się, jak utworzyć odwołanie do danych wejściowych danych, zobacz [danych referencyjnych użyj](stream-analytics-use-reference-data.md).  

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

Można również użyć [programu Azure PowerShell](https://docs.microsoft.com/en-us/powershell/module/azurerm.streamanalytics/New-AzureRmStreamAnalyticsInput), [.Net interfejsu API](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions), [interfejsu API REST](https://docs.microsoft.com/en-us/rest/api/streamanalytics/stream-analytics-input), i [programu Visual Studio](stream-analytics-tools-for-visual-studio.md) do tworzenia, edytowania i przetestować zadanie usługi analiza strumienia dane wejściowe.

## <a name="stream-data-from-event-hubs"></a>Strumień danych z usługi Event Hubs

Usługa Azure Event Hubs zapewnia wysoką skalowalnością ingestors zdarzeń publikowania / subskrypcji. Centrum zdarzeń można zbierać miliony zdarzeń na sekundę, dzięki czemu możliwe jest przetwarzanie i analizowanie olbrzymich ilości danych wytworzonych przez podłączone urządzenia i aplikacje. Centra zdarzeń i analiza strumienia razem umożliwiają end-to-end rozwiązania do analiz w czasie rzeczywistym. Centra zdarzeń umożliwiają źródła danych zdarzeń na platformie Azure w czasie rzeczywistym i zadania usługi analiza strumienia może przetwarzać tych zdarzeń w czasie rzeczywistym. Na przykład możesz wysłać kliknięć w sieci web, odczyty czujników lub online dziennika zdarzeń do usługi Event Hubs. Następnie można utworzyć zadania usługi analiza strumienia do użycia usługi Event Hubs jako strumieni danych wejściowych w czasie rzeczywistym filtrowanie, agregację i korelacji.

Sygnatura czasowa domyślne zdarzenia pochodzące z usługi Event Hubs w Stream Analytics jest sygnatury czasowej, które zdarzenia w przypadku odebrania koncentratora, który jest `EventEnqueuedUtcTime`. Do przetwarzania danych jako strumień przy użyciu sygnatury czasowej w przypadku ładunku, należy użyć [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) — słowo kluczowe.

### <a name="consumer-groups"></a>Grupy odbiorców
Należy skonfigurować każdego Centrum zdarzeń usługi analiza strumienia danych wejściowych ma własną grupę odbiorców. Jeśli zadanie zawiera samosprzężenie lub składa się z wielu danych wejściowych, niektóre dane wejściowe mogą odczytać przez więcej niż jeden czytnik poniżej. Taka sytuacja wpływa na liczbę czytników w grupie jednego konsumenta. Aby zapobiec przekroczeniu limitu centra zdarzeń w pięciu czytników dla każdej grupy odbiorców dla każdej partycji, jest najlepszym rozwiązaniem, aby wyznaczyć grupy odbiorców dla każdego zadania usługi analiza strumienia. Istnieje również limit 20 grup odbiorców, na Centrum zdarzeń. Aby uzyskać więcej informacji, zobacz [Event Hubs Programming Guide](../event-hubs/event-hubs-programming-guide.md).

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
| **Format serializacji zdarzeń** | Format serializacji (JSON, CSV lub Avro) przychodzącego strumienia danych. |
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
> Korzystając z Centrum zdarzeń jako punktu końcowego dla tras Centrum IoT, możesz uzyskać dostępu do przy użyciu medadata Centrum IoT [funkcja GetMetadataPropertyValue](https://msdn.microsoft.com/en-us/library/azure/mt793845.aspx).
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
| **Format serializacji zdarzeń** | Format serializacji (JSON, CSV lub Avro) przychodzącego strumienia danych. |
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
| **Format serializacji zdarzeń** | Format serializacji (JSON, CSV lub Avro) dla przychodzących strumieni danych. |
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
Znasz opcji połączenia danych na platformie Azure dla Twojego zadania usługi analiza strumienia. Aby dowiedzieć się więcej na temat usługi Stream Analytics, zobacz:

* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
