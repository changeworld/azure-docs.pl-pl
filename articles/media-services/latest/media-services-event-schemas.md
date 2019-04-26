---
title: Usługa Azure schematów usługi Event Grid na potrzeby zdarzeń usługi Media Services
description: Opisuje właściwości, które są dostarczane dla zdarzeń usługi Media Services za pomocą usługi Azure Event Grid
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: reference
ms.date: 02/13/2019
ms.author: juliako
ms.openlocfilehash: f9fe689e6911c5e9497ee82132e8b70bd9aada7e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60322237"
---
# <a name="azure-event-grid-schemas-for-media-services-events"></a>Usługa Azure schematów usługi Event Grid na potrzeby zdarzeń usługi Media Services

Ten artykuł zawiera schematy i właściwości dla zdarzeń usługi Media Services.

Aby uzyskać listę przykładowych skryptów i samouczków, zobacz [źródła zdarzeń usługi Media Services](../../event-grid/event-sources.md#azure-subscriptions).

## <a name="job-related-event-types"></a>Zadania pokrewne typy zdarzeń

Media Services emitowały **zadania** powiązane typy zdarzeń, które opisano poniżej. Istnieją dwie kategorie dla **zadania** zdarzenia dotyczące: "Zmienia się stan zadania monitorowania" i "Monitorowania stan dane wyjściowe zadania zmieni się". 

Jeśli zasubskrybujesz zdarzenia JobStateChange można zarejestrować dla wszystkich zdarzeń. Lub może być subskrybowana tylko pod kątem określonych zdarzeń (na przykład końcowego stany JobErrored, JobFinished i JobCanceled). 

### <a name="monitoring-job-state-changes"></a>Monitorowanie zmian stanu zadania

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| Pobierz zdarzenie dla wszystkich zmian stanu zadania. |
| Microsoft.Media.JobScheduled| Pobierz zdarzenie, gdy zadanie przechodzi w stan Zaplanowane. |
| Microsoft.Media.JobProcessing| Pobierz zdarzenie, gdy zadanie przechodzi w stan przetwarzania. |
| Microsoft.Media.JobCanceling| Pobierz zdarzenie, gdy zadanie przechodzi w stan anulowania. |
| Microsoft.Media.JobFinished| Pobierz zdarzenie, gdy zadanie przechodzi do stanu Gotowe. Jest to stan końcowy, który zawiera dane wyjściowe zadania.|
| Microsoft.Media.JobCanceled| Pobierz zdarzenie, gdy zadanie przechodzi do stanu Anulowano. Jest to stan końcowy, który zawiera dane wyjściowe zadania.|
| Microsoft.Media.JobErrored| Pobierz zdarzenie, gdy zadanie przechodzi do stanu błędu. Jest to stan końcowy, który zawiera dane wyjściowe zadania.|

Zobacz [przykłady schematu](#event-schema-examples) poniżej.

### <a name="monitoring-job-output-state-changes"></a>Zmiany stanu generowania danych wyjściowych zadania monitorowania

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.Media.JobOutputStateChange| Pobierz zdarzenia dla wszystkich danych wyjściowych zadania, których stan zmieni się. |
| Microsoft.Media.JobOutputScheduled| Pobierz zdarzenie, kiedy przechodzi do stanu Zaplanowane generowania danych wyjściowych zadania. |
| Microsoft.Media.JobOutputProcessing| Pobierz zdarzenie w przypadku przejścia do stan przetwarzania generowania danych wyjściowych zadania. |
| Microsoft.Media.JobOutputCanceling| Pobierz zdarzenie, kiedy przechodzi do stanu anulowanie generowania danych wyjściowych zadania.|
| Microsoft.Media.JobOutputFinished| Pobierz zdarzenie po danych wyjściowych zadania, przechodzi do stanu.|
| Microsoft.Media.JobOutputCanceled| Pobierz zdarzenie, kiedy przejść dane wyjściowe zadania zostało anulowane stanu.|
| Microsoft.Media.JobOutputErrored| Pobierz zdarzenie, kiedy przechodzi do stanu błędu generowania danych wyjściowych zadania.|

Zobacz [przykłady schematu](#event-schema-examples) poniżej.

### <a name="monitoring-job-output-progress"></a>Postęp generowania danych wyjściowych zadania monitorowania

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.Media.JobOutputProgress| To zdarzenie odzwierciedla zadania przetwarzania postępu, od 0% do 100%. Usługa próbuje wysłać zdarzenie, jeśli istnieje została 5% lub wzrost większą wartość w toku lub jest on ma więcej niż 30 sekund od momentu ostatniego zdarzenia (pulsu). Wartość postępu nie jest gwarantowana rozpoczynają się od 0% lub 100% osiągnięcia, ani nie jest gwarantowane zwiększyć stałą prędkością, wraz z upływem czasu. To zdarzenie nie powinny służyć ustalenie, czy przetwarzanie zostało ukończone — zamiast tego należy użyć zdarzenia zmiany stanu.|

Zobacz [przykłady schematu](#event-schema-examples) poniżej.

## <a name="live-event-types"></a>Typy zdarzeń na żywo

Usługa Media Services również emituje **Live** typy zdarzeń, które opisano poniżej. Istnieją dwie kategorie dla **Live** zdarzeń: zdarzenia na poziomie usługi stream i poziom śledzenia zdarzeń. 

### <a name="stream-level-events"></a>Zdarzenia na poziomie Stream

Stream poziom zdarzenia są wywoływane na połączenia lub strumienia. Każde zdarzenie ma `StreamId` parametr, który identyfikuje połączenia lub strumienia. Każdego strumienia lub połączenie ma jednej lub więcej ścieżek o różnych typach. Na przykład jedno połączenie z kodera może mieć cztery ścieżki wideo i jedną ścieżkę audio. Dostępne są następujące typy zdarzeń strumienia:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.Media.LiveEventConnectionRejected | Kodera połączenie jest odrzucane. |
| Microsoft.Media.LiveEventEncoderConnected | Koder ustanawia połączenie z wydarzenia na żywo. |
| Microsoft.Media.LiveEventEncoderDisconnected | Odłącza kodera. |

Zobacz [przykłady schematu](#event-schema-examples) poniżej.

### <a name="track-level-events"></a>Poziom śledzenia zdarzeń

Poziom śledzenia zdarzenia są wywoływane na ścieżkę. 

> [!NOTE]
> Wszystkie zdarzenia śledzenia na poziomie są wywoływane po podłączeniu koder na żywo.

Dostępne są następujące typy zdarzeń śledzenia na poziomie:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.Media.LiveEventIncomingDataChunkDropped | Serwer multimediów fragmentu danych spada, ponieważ jest za późno lub ma nakładające się sygnatura czasowa (sygnatura czasowa nowych fragmentów danych jest mniejsza niż czas zakończenia poprzedniego fragmentu danych). |
| Microsoft.Media.LiveEventIncomingStreamReceived | Media serwer odbiera pierwszy fragment danych dla każdej ścieżki, w usłudze stream ani połączenia. |
| Microsoft.Media.LiveEventIncomingStreamsOutOfSync | Serwer multimediów wykrywa audio i wideo strumieni nie są zsynchronizowane. Użyj jako ostrzeżenie, ponieważ środowisko użytkownika nie może mieć wpływ. |
| Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync | Serwer multimediów wykrywa, że jedną z dwóch strumieni wideo pochodzących z kodera nie są zsynchronizowane. Użyj jako ostrzeżenie, ponieważ środowisko użytkownika nie może mieć wpływ. |
| Microsoft.Media.LiveEventIngestHeartbeat | Publikowany co 20 sekund dla każdej ścieżki, w przypadku zdarzenia na żywo jest uruchomiona. Zapewnia obsługę podsumowanie kondycji.<br/><br/>Po kodera początkowo był połączony, zdarzenia pulsu w dalszym ciągu emisji co 20 s, czy kodera jest nadal połączony, czy nie. |
| Microsoft.Media.LiveEventTrackDiscontinuityDetected | Serwer multimediów wykrywa przerwa w ścieżce przychodzącej. |

Zobacz [przykłady schematu](#event-schema-examples) poniżej.

## <a name="event-schema-examples"></a>Przykłady schematu zdarzeń

### <a name="jobstatechange"></a>JobStateChange

W poniższym przykładzie przedstawiono schematu **JobStateChange** zdarzeń: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
    "eventType": "Microsoft.Media.JobStateChange",
    "eventTime": "2018-04-20T21:26:13.8978772",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "previousState": "Processing",
      "state": "Finished"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| previousState | string | Stan zadania przed wystąpieniem zdarzenia. |
| state | string | Nowy stan zadania zgłoszonego w tym zdarzeniu. Na przykład "zaplanowane: Zadanie jest gotowe do rozpoczęcia"lub" Zakończono: Zadanie jest zakończone".|

Gdy stan zadania może być jedna z wartości: *W kolejce*, *zaplanowane*, *przetwarzania*, *Zakończono*, *błąd*, *anulowane*, *Anulowanie*

> [!NOTE]
> *W kolejce* ma znajdować się w **previousState** właściwości, ale nie **stanu** właściwości.

### <a name="jobscheduled-jobprocessing-jobcanceling"></a>JobCanceling JobScheduled, JobProcessing,

Dla każdego-final zadania zmiany stanu (np. JobCanceling JobScheduled, JobProcessing,) schematu przykład wygląda podobnie do następujących:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobProcessing",
  "eventTime": "2018-10-12T16:12:18.0839935",
  "id": "a0a6efc8-f647-4fc2-be73-861fa25ba2db",
  "data": {
    "previousState": "Scheduled",
    "state": "Processing",
    "correlationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="jobfinished-jobcanceled-joberrored"></a>JobErrored JobFinished, JobCanceled,

Dla każdego końcowego zadania zmiany stanu (np. JobErrored JobFinished, JobCanceled,) schematu przykład wygląda podobnie do następujących:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobFinished",
  "eventTime": "2018-10-12T16:25:56.4115495",
  "id": "9e07e83a-dd6e-466b-a62f-27521b216f2a",
  "data": {
    "outputs": [
      {
        "@odata.type": "#Microsoft.Media.JobOutputAsset",
        "assetName": "output-7640689F",
        "error": null,
        "label": "VideoAnalyzerPreset_0",
        "progress": 100,
        "state": "Finished"
      }
    ],
    "previousState": "Processing",
    "state": "Finished",
    "correlationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| Dane wyjściowe | Tablica | Pobiera zadanie danych wyjściowych.|

### <a name="joboutputstatechange"></a>JobOutputStateChange

W poniższym przykładzie przedstawiono schematu **JobOutputStateChange** zdarzeń:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobOutputStateChange",
  "eventTime": "2018-10-12T16:25:56.0242854",
  "id": "dde85f46-b459-4775-b5c7-befe8e32cf90",
  "data": {
    "previousState": "Processing",
    "output": {
      "@odata.type": "#Microsoft.Media.JobOutputAsset",
      "assetName": "output-7640689F",
      "error": null,
      "label": "VideoAnalyzerPreset_0",
      "progress": 100,
      "state": "Finished"
    },
    "jobCorrelationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="joboutputscheduled-joboutputprocessing-joboutputfinished-joboutputcanceling-joboutputcanceled-joboutputerrored"></a>JobOutputScheduled JobOutputProcessing, JobOutputFinished, JobOutputCanceling, JobOutputCanceled, JobOutputErrored

Dla każdej zmiany stanu JobOutput schematu przykład wygląda podobnie do następującego:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobOutputProcessing",
  "eventTime": "2018-10-12T16:12:18.0061141",
  "id": "f1fd5338-1b6c-4e31-83c9-cd7c88d2aedb",
  "data": {
    "previousState": "Scheduled",
    "output": {
      "@odata.type": "#Microsoft.Media.JobOutputAsset",
      "assetName": "output-7640689F",
      "error": null,
      "label": "VideoAnalyzerPreset_0",
      "progress": 0,
      "state": "Processing"
    },
    "jobCorrelationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```
### <a name="joboutputprogress"></a>JobOutputProgress

Schemat przykład wygląda podobnie do następującego:

 ```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/belohGroup/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/job-5AB6DE32",
  "eventType": "Microsoft.Media.JobOutputProgress",
  "eventTime": "2018-12-10T18:20:12.1514867",
  "id": "00000000-0000-0000-0000-000000000000",
  "data": {
    "jobCorrelationData": {
      "TestKey1": "TestValue1",
      "testKey2": "testValue2"
    },
    "label": "VideoAnalyzerPreset_0",
    "progress": 86
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

W poniższym przykładzie przedstawiono schematu **LiveEventConnectionRejected** zdarzeń: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventConnectionRejected",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "b303db59-d5c1-47eb-927a-3650875fded1",
    "data": { 
      "streamId":"Mystream1",
      "ingestUrl": "http://abc.ingest.isml",
      "encoderIp": "118.238.251.xxx",
      "encoderPort": 52859,
      "resultCode": "MPE_INGEST_CODEC_NOT_SUPPORTED"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| streamId | string | Identyfikator strumienia lub połączenia. Koder klienta odpowiada lub można dodać tego Identyfikatora w adresie URL pozyskiwania. |  
| ingestUrl | string | Pozyskanie adresu URL podanego przez zdarzenie na żywo. |  
| encoderIp | string | Adres IP kodera. |
| encoderPort | string | Port kodera, z której pochodzi ten strumień. |
| resultCode | string | Przyczyna połączenie zostało odrzucone. Kody wyników są wymienione w poniższej tabeli. |

Kody wyników są:

| Kod wyniku | Opis |
| ----------- | ----------- |
| MPE_RTMP_APPID_AUTH_FAILURE | Wprowadzony tekst jest niepoprawny adres URL pozyskiwania |
| MPE_INGEST_ENCODER_CONNECTION_DENIED | Koder IP nie jest obecny w IP skonfigurowana lista dozwolonych |
| MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED | Koder nie wysyłać metadane o strumień. |
| MPE_INGEST_CODEC_NOT_SUPPORTED | Określona kodera-dekodera nie jest obsługiwane. |
| MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED | Odebrano fragmentu przed odbieranie i nagłówek dla tego strumienia. |
| MPE_INGEST_MEDIA_QUALITIES_EXCEEDED | Jakość określony przekracza dozwolony limit maksymalny. |
| MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED | Zagregowane szybkości transmisji bitów przekracza maksymalny dozwolony limit. |
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | Znacznik czasu FLVTag wideo lub audio jest nieprawidłowy z kodera RTMP. |

### <a name="liveeventencoderconnected"></a>LiveEventEncoderConnected

W poniższym przykładzie przedstawiono schematu **LiveEventEncoderConnected** zdarzeń: 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderConnected",
    "eventTime": "2018-08-07T23:08:09.1710643",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| streamId | string | Identyfikator strumienia lub połączenia. Koder lub klienta jest udostępnienie tego Identyfikatora w adresie URL pozyskiwania. |
| ingestUrl | string | Pozyskanie adresu URL podanego przez zdarzenie na żywo. |
| encoderIp | string | Adres IP kodera. |
| encoderPort | string | Port kodera, z której pochodzi ten strumień. |

### <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

W poniższym przykładzie przedstawiono schematu **LiveEventEncoderDisconnected** zdarzeń: 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderDisconnected",
    "eventTime": "2018-08-07T23:08:09.1710872",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "resultCode": "S_OK"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| streamId | string | Identyfikator strumienia lub połączenia. Koder klienta odpowiada lub można dodać tego Identyfikatora w adresie URL pozyskiwania. |  
| ingestUrl | string | Pozyskanie adresu URL podanego przez zdarzenie na żywo. |  
| encoderIp | string | Adres IP kodera. |
| encoderPort | string | Port kodera, z której pochodzi ten strumień. |
| resultCode | string | Przyczyna kodera odłączanie. Może to być łagodne rozłączenia lub z błędem. Kody wyników są wymienione w poniższej tabeli. |

Kody wyników błąd to:

| Kod wyniku | Opis |
| ----------- | ----------- |
| MPE_RTMP_SESSION_IDLE_TIMEOUT | Upłynął limit czasu bezczynne przez dozwolony limit czasu sesji protokołu RTMP. |
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | Znacznik czasu FLVTag wideo lub audio jest nieprawidłowy z kodera RTMP. |
| MPE_CAPACITY_LIMIT_REACHED | Koder wysyłanie danych jest zbyt duża. |
| Nieznany błąd kodów | Te kody błędów może wynosić od błąd pamięci zduplikowane wpisy w mapie. |

Kody wyników łagodne rozłączenia są:

| Kod wyniku | Opis |
| ----------- | ----------- |
| S_OK | Koder pomyślnie odłączona. |
| MPE_CLIENT_TERMINATED_SESSION | Koder rozłączona (RTMP). |
| MPE_CLIENT_DISCONNECTED | Koder rozłączona (FMP4). |
| MPI_REST_API_CHANNEL_RESET | Odebrano polecenie Resetowanie kanału. |
| MPI_REST_API_CHANNEL_STOP | Odebrano polecenie zatrzymania kanału. |
| MPI_REST_API_CHANNEL_STOP | Kanał jest poddawana konserwacji. |
| MPI_STREAM_HIT_EOF | Strumień EOF są wysyłane przez koder. |

### <a name="liveeventincomingdatachunkdropped"></a>LiveEventIncomingDataChunkDropped

W poniższym przykładzie przedstawiono schematu **LiveEventIncomingDataChunkDropped** zdarzeń: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingDataChunkDropped",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "03da9c10-fde7-48e1-80d8-49936f2c3e7d",
    "data": { 
      "trackType": "Video",
      "trackName": "Video",
      "bitrate": 300000,
      "timestamp": 36656620000,
      "timescale": 10000000,
      "resultCode": "FragmentDrop_OverlapTimestamp"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| trackType | string | Typ ścieżki (Audio / wideo). |
| trackName | string | Nazwa ścieżki. |
| Szybkość transmisji bitów | liczba całkowita | Szybkość transmisji bitów toru. |
| timestamp | string | Sygnatura czasowa fragmentu danych porzucony. |
| Skala czasu | string | Skala czasu znacznika czasu. |
| resultCode | string | Powód listy fragmentów danych. **FragmentDrop_OverlapTimestamp** lub **FragmentDrop_NonIncreasingTimestamp**. |

### <a name="liveeventincomingstreamreceived"></a>LiveEventIncomingStreamReceived

W poniższym przykładzie przedstawiono schematu **LiveEventIncomingStreamReceived** zdarzeń: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamReceived",
    "eventTime": "2018-08-07T23:08:10.5069288Z",
    "id": "7f939a08-320c-47e7-8250-43dcfc04ab4d",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml/Streams(15864-stream0)15864-stream0",
      "trackType": "video",
      "trackName": "video",
      "bitrate": 2962000,
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "timestamp": "15336831655032322",
      "duration": "20000000",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| trackType | string | Typ ścieżki (Audio / wideo). |
| trackName | string | Nazwy ścieżki (albo, dostarczone przez koder lub, w przypadku protokołu RTMP, serwer generuje w *TrackType_Bitrate* formatu). |
| Szybkość transmisji bitów | liczba całkowita | Szybkość transmisji bitów toru. |
| ingestUrl | string | Pozyskanie adresu URL podanego przez zdarzenie na żywo. |
| encoderIp | string  | Adres IP kodera. |
| encoderPort | string | Port kodera, z której pochodzi ten strumień. |
| timestamp | string | Pierwszy znacznik czasu fragmentu danych odebranych. |
| Skala czasu | string | Skala czasu, w którym jest reprezentowana sygnatury czasowej. |

### <a name="liveeventincomingstreamsoutofsync"></a>LiveEventIncomingStreamsOutOfSync

W poniższym przykładzie przedstawiono schematu **LiveEventIncomingStreamsOutOfSync** zdarzeń: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamsOutOfSync",
    "eventTime": "2018-08-10T02:26:20.6269183Z",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "minLastTimestamp": "319996",
      "typeOfStreamWithMinLastTimestamp": "Audio",
      "maxLastTimestamp": "366000",
      "typeOfStreamWithMaxLastTimestamp": "Video",
      "timescaleOfMinLastTimestamp": "10000000", 
      "timescaleOfMaxLastTimestamp": "10000000"       
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| minLastTimestamp | string | Co najmniej ostatniego sygnatury czasowe między wszystkie ścieżki (audio lub wideo). |
| typeOfTrackWithMinLastTimestamp | string | Typ ścieżki (audio lub wideo) przy użyciu minimalnej sygnatury czasowej ostatniego. |
| maxLastTimestamp | string | Maksymalna liczba wszystkie sygnatury czasowe między wszystkie ścieżki (audio lub wideo). |
| typeOfTrackWithMaxLastTimestamp | string | Typ ścieżki (audio lub wideo) z maksymalną sygnatury czasowej ostatniego. |
| timescaleOfMinLastTimestamp| string | Pobiera skali czasu, w którym jest reprezentowana "MinLastTimestamp".|
| timescaleOfMaxLastTimestamp| string | Pobiera skali czasu, w którym jest reprezentowana "MaxLastTimestamp".|

### <a name="liveeventincomingvideostreamsoutofsync"></a>LiveEventIncomingVideoStreamsOutOfSync

W poniższym przykładzie przedstawiono schematu **LiveEventIncomingVideoStreamsOutOfSync** zdarzeń: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/LiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "6dd4d862-d442-40a0-b9f3-fc14bcf6d750",
    "data": {
      "firstTimestamp": "2162058216",
      "firstDuration": "2000",
      "secondTimestamp": "2162057216",
      "secondDuration": "2000",
      "timescale": "10000000"      
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| firstTimestamp | string | Odebrano jeden z poziomów śledzi/jakości wideo typu sygnatura czasowa. |
| FirstDuration | string | Czas trwania fragmentu danych za pomocą pierwszy znacznik czasu. |
| secondTimestamp | string  | Odebrano dla pewnego poziomu śledzenia/jakości wideo typu sygnatura czasowa. |
| SecondDuration | string | Czas trwania fragmentu danych z sygnaturą czasową drugie. |
| Skala czasu | string | Skala czasu sygnatury czasowe i czas trwania.|

### <a name="liveeventingestheartbeat"></a>LiveEventIngestHeartbeat

W poniższym przykładzie przedstawiono schematu **LiveEventIngestHeartbeat** zdarzeń: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIngestHeartbeat",
    "eventTime": "2018-08-07T23:17:57.4610506",
    "id": "7f450938-491f-41e1-b06f-c6cd3965d786",
    "data": {
      "trackType": "audio",
      "trackName": "audio",
      "bitrate": 160000,
      "incomingBitrate": 155903,
      "lastTimestamp": "15336837535253637",
      "timescale": "10000000",
      "overlapCount": 0,
      "discontinuityCount": 0,
      "nonincreasingCount": 0,
      "unexpectedBitrate": false,
      "state": "Running",
      "healthy": true
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| trackType | string | Typ ścieżki (Audio / wideo). |
| trackName | string | Nazwy ścieżki (albo, dostarczone przez koder lub, w przypadku protokołu RTMP, serwer generuje w *TrackType_Bitrate* formatu). |
| Szybkość transmisji bitów | liczba całkowita | Szybkość transmisji bitów toru. |
| IncomingBitrate | liczba całkowita | Obliczony transmisji bitów, oparte na fragmenty danych pochodzących z kodera. |
| lastTimestamp | string | Sygnatura czasowa najnowsze odebrane dla ścieżki w ciągu ostatnich 20 sekund. |
| Skala czasu | string | Skala czasu, w którym są wyrażane sygnatur czasowych. |
| overlapCount | liczba całkowita | Liczba fragmentów danych miały nakładających się sygnatur czasowych w ciągu ostatnich 20 sekund. |
| discontinuityCount | liczba całkowita | Liczba przerw zaobserwowane w ciągu ostatnich 20 sekund. |
| nonIncreasingCount | liczba całkowita | Liczba fragmentów danych z sygnaturami czasowymi w przeszłości zostały odebrane w ciągu ostatnich 20 sekund. |
| unexpectedBitrate | bool | Jeśli szybkości transmisji z oczekiwanymi i rzeczywistymi różnią się przez więcej niż dozwolony limit w ciągu ostatnich 20 sekund. To wartość true, wtedy i tylko wtedy, gdy, incomingBitrate > = 2 * szybkości transmisji bitów lub incomingBitrate < = szybkości transmisji bitów/2 lub IncomingBitrate = 0. |
| state | string | Stan zdarzenia na żywo. |
| w dobrej kondycji | bool | Wskazuje, czy pozyskiwania jest w dobrej kondycji na podstawie liczby i flagi. Dobra kondycja ma wartość true Jeśli overlapCount = 0 & & discontinuityCount = 0 & & nonIncreasingCount = 0 & & unexpectedBitrate = false. |

### <a name="liveeventtrackdiscontinuitydetected"></a>LiveEventTrackDiscontinuityDetected

W poniższym przykładzie przedstawiono schematu **LiveEventTrackDiscontinuityDetected** zdarzeń: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventTrackDiscontinuityDetected",
    "eventTime": "2018-08-07T23:18:06.1270405Z",
    "id": "5f4c510d-5be7-4bef-baf0-64b828be9c9b",
    "data": {
      "trackName": "video",
      "previousTimestamp": "15336837615032322",
      "trackType": "video",
      "bitrate": 2962000,
      "newTimestamp": "15336837619774273",
      "discontinuityGap": "575284",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| trackType | string | Typ ścieżki (Audio / wideo). |
| trackName | string | Nazwy ścieżki (albo, dostarczone przez koder lub, w przypadku protokołu RTMP, serwer generuje w *TrackType_Bitrate* formatu). |
| Szybkość transmisji bitów | liczba całkowita | Szybkość transmisji bitów toru. |
| PreviousTimestamp | string | Sygnatura czasowa poprzedniego fragmentu. |
| newTimestamp | string | Sygnatura czasowa bieżącego fragmentu. |
| discontinuityGap | string | Przerwa między powyżej dwóch sygnatur czasowych. |
| Skala czasu | string | Są reprezentowane w skali czasu, w której sygnatura czasowa i ciągłości przerwy. |

### <a name="common-event-properties"></a>Wspólne właściwości zdarzenia

Zdarzenie zawiera następujące dane najwyższego poziomu:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| temat | string | Temat EventGrid. Ta właściwość ma identyfikator zasobu dla konta usługi Media Services. |
| temat | string | Ścieżka zasobu dla kanału usługi Media Services w ramach konta usługi Media Services. Łączenie zapewniają tematu oraz temat, możesz zasobu, identyfikator zadania. |
| eventType | string | Jeden z typów zdarzeń zarejestrowane dla tego źródła zdarzeń. Na przykład "Microsoft.Media.JobStateChange". |
| eventTime | string | Czas, którego zdarzenie jest generowane na podstawie czasu UTC dostawcy. |
| id | string | Unikatowy identyfikator zdarzenia. |
| dane | obiekt | Dane zdarzenia usługi Media Services. |
| dataVersion | string | Wersja schematu dla obiektu danych. Wydawca Określa wersję schematu. |
| metadataVersion | string | Wersja schematu dla metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Usługa Event Grid udostępnia tę wartość. |

## <a name="next-steps"></a>Kolejne kroki

[Rejestrowanie zdarzeń zmiany stanu zadania](job-state-events-cli-how-to.md)

## <a name="see-also"></a>Zobacz także

- [Zestaw SDK platformy .NET EventGrid zawierający zdarzenia usługi multimediów](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definicje zdarzeń usługi Media Services](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)
