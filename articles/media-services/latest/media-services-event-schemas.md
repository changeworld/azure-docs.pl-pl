---
title: Usługa Azure schematów usługi Event Grid na potrzeby zdarzeń usługi Media Services
description: Opisuje właściwości, które są dostarczane dla zdarzeń usługi Media Services za pomocą usługi Azure Event Grid
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: reference
ms.date: 08/17/2018
ms.author: juliako
ms.openlocfilehash: a6a6c459e170627d26aa1445f4f4dd193965fe70
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/17/2018
ms.locfileid: "42054194"
---
# <a name="azure-event-grid-schemas-for-media-services-events"></a>Usługa Azure schematów usługi Event Grid na potrzeby zdarzeń usługi Media Services

Ten artykuł zawiera schematy i właściwości dla zdarzeń usługi Media Services.

Aby uzyskać listę przykładowych skryptów i samouczków, zobacz [źródła zdarzeń usługi Media Services](../../event-grid/event-sources.md#azure-subscriptions).

## <a name="available-event-types"></a>Zdarzenie dostępne typy

Media Services emitowały następujące typy zdarzeń:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| Stan zadania zmieni się. |
| Microsoft.Media.LiveEventConnectionRejected | Kodera połączenie jest odrzucane. |
| Microsoft.Media.LiveEventEncoderConnected | Koder ustanawia połączenie z wydarzenia na żywo. |
| Microsoft.Media.LiveEventEncoderDisconnected | Odłącza kodera. |
| Microsoft.Media.LiveEventIncomingDataChunkDropped | Serwer multimediów fragmentu danych spada, ponieważ jest za późno lub ma nakładające się sygnatura czasowa (sygnatura czasowa nowych fragmentów danych jest mniejsza niż czas zakończenia poprzedniego fragmentu danych). |
| Microsoft.Media.LiveEventIncomingStreamReceived | Media serwer odbiera pierwszy fragment danych dla każdej ścieżki, w usłudze stream ani połączenia. |
| Microsoft.Media.LiveEventIncomingStreamsOutOfSync | Serwer multimediów wykrywa audio i wideo strumieni nie są zsynchronizowane. Użyj jako ostrzeżenie, ponieważ środowisko użytkownika nie może mieć wpływ. |
| Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync | Serwer multimediów wykrywa, że jedną z dwóch strumieni wideo pochodzących z kodera nie są zsynchronizowane. Użyj jako ostrzeżenie, ponieważ środowisko użytkownika nie może mieć wpływ. |
| Microsoft.Media.LiveEventIngestHeartbeat | Publikowany co 20 sekund dla każdej ścieżki, w przypadku zdarzenia na żywo jest uruchomiona. Zapewnia obsługę podsumowanie kondycji. |
| Microsoft.Media.LiveEventTrackDiscontinuityDetected | Serwer multimediów wykrywa przerwa w ścieżce przychodzącej. |

Istnieją dwie kategorie dla **Live** zdarzeń: zdarzenia na poziomie usługi stream i poziom śledzenia zdarzeń. 

Stream poziom zdarzenia są wywoływane na połączenia lub strumienia. Każde zdarzenie ma `StreamId` parametr, który identyfikuje połączenia lub strumienia. Każdego strumienia lub połączenie ma jednej lub więcej ścieżek o różnych typach. Na przykład jedno połączenie z kodera może mieć cztery ścieżki wideo i jedną ścieżkę audio. Dostępne są następujące typy zdarzeń strumienia:

* LiveEventConnectionRejected
* LiveEventEncoderConnected
* LiveEventEncoderDisconnected

Poziom śledzenia zdarzenia są wywoływane na ścieżkę. Dostępne są następujące typy zdarzeń śledzenia:

* LiveEventIncomingDataChunkDropped
* LiveEventIncomingStreamReceived
* LiveEventIncomingStreamsOutOfSync
* LiveEventIncomingVideoStreamsOutOfSync
* LiveEventIngestHeartbeat
* LiveEventTrackDiscontinuityDetected

## <a name="jobstatechange"></a>JobStateChange

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
| previousState | ciąg | Stan zadania przed wystąpieniem zdarzenia. |
| state | ciąg | Nowy stan zadania zgłoszonego w tym zdarzeniu. Na przykład "kolejce: zadanie oczekuje na zasoby" lub "zaplanowane: zadanie jest gotowe do rozpoczęcia".|

Gdy stan zadania może być jedna z wartości: *kolejce*, *zaplanowane*, *przetwarzania*, *Zakończono*, *błąd*, *Anulowane*, *anulowanie*

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

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
      "StreamId":"Mystream1",
      "IngestUrl": "http://abc.ingest.isml",
      "EncoderIp": "118.238.251.xxx",
      "EncoderPort": 52859,
      "ResultCode": "MPE_INGEST_CODEC_NOT_SUPPORTED"
    },
    "dataVersion": "1.0"
  }
]
```

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| StreamId | ciąg | Identyfikator strumienia lub połączenia. Koder klienta odpowiada lub można dodać tego Identyfikatora w adresie URL pozyskiwania. |  
| IngestUrl | ciąg | Pozyskanie adresu URL podanego przez zdarzenie na żywo. |  
| EncoderIp | ciąg | Adres IP kodera. |
| EncoderPort | ciąg | Port kodera, z której pochodzi ten strumień. |
| ResultCode | ciąg | Przyczyna połączenie zostało odrzucone. Kody wyników są wymienione w poniższej tabeli. |

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

## <a name="liveeventencoderconnected"></a>LiveEventEncoderConnected

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
| StreamId | ciąg | Identyfikator strumienia lub połączenia. Koder lub klienta jest udostępnienie tego Identyfikatora w adresie URL pozyskiwania. |
| IngestUrl | ciąg | Pozyskanie adresu URL podanego przez zdarzenie na żywo. |
| EncoderIp | ciąg | Adres IP kodera. |
| EncoderPort | ciąg | Port kodera, z której pochodzi ten strumień. |

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

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
| StreamId | ciąg | Identyfikator strumienia lub połączenia. Koder klienta odpowiada lub można dodać tego Identyfikatora w adresie URL pozyskiwania. |  
| IngestUrl | ciąg | Pozyskanie adresu URL podanego przez zdarzenie na żywo. |  
| EncoderIp | ciąg | Adres IP kodera. |
| EncoderPort | ciąg | Port kodera, z której pochodzi ten strumień. |
| ResultCode | ciąg | Przyczyna kodera odłączanie. Może to być łagodne rozłączenia lub z błędem. Kody wyników są wymienione w poniższej tabeli. |

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

## <a name="liveeventincomingdatachunkdropped"></a>LiveEventIncomingDataChunkDropped

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
      "TrackType": "Video",
      "TrackName": "Video",
      "Bitrate": 300000,
      "Timestamp": 36656620000,
      "Timescale": 10000000,
      "ResultCode": "FragmentDrop_OverlapTimestamp"
    },
    "dataVersion": "1.0"
  }
]
```

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| TrackType | ciąg | Typ ścieżki (Audio / wideo). |
| TrackName | ciąg | Nazwa ścieżki. |
| Szybkość transmisji bitów | liczba całkowita | Szybkość transmisji bitów toru. |
| Znacznik czasu | ciąg | Sygnatura czasowa fragmentu danych porzucony. |
| Skala czasu | ciąg | Skala czasu znacznika czasu. |
| ResultCode | ciąg | Powód listy fragmentów danych. **FragmentDrop_OverlapTimestamp** lub **FragmentDrop_NonIncreasingTimestamp**. |

## <a name="liveeventincomingstreamreceived"></a>LiveEventIncomingStreamReceived

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
| TrackType | ciąg | Typ ścieżki (Audio / wideo). |
| TrackName | ciąg | Nazwy ścieżki (albo, dostarczone przez koder lub, w przypadku protokołu RTMP, serwer generuje w *TrackType_Bitrate* formatu). |
| Szybkość transmisji bitów | liczba całkowita | Szybkość transmisji bitów toru. |
| IngestUrl | ciąg | Pozyskanie adresu URL podanego przez zdarzenie na żywo. |
| EncoderIp | ciąg  | Adres IP kodera. |
| EncoderPort | ciąg | Port kodera, z której pochodzi ten strumień. |
| Znacznik czasu | ciąg | Pierwszy znacznik czasu fragmentu danych odebranych. |
| Skala czasu | ciąg | Skala czasu, w którym jest reprezentowana sygnatury czasowej. |

## <a name="liveeventincomingstreamsoutofsync"></a>LiveEventIncomingStreamsOutOfSync

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
      "typeOfStreamWithMaxLastTimestamp": "Video"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| MinLastTimestamp | ciąg | Co najmniej ostatniego sygnatury czasowe między wszystkie ścieżki (audio lub wideo). |
| TypeOfTrackWithMinLastTimestamp | ciąg | Typ ścieżki (audio lub wideo) przy użyciu minimalnej sygnatury czasowej ostatniego. |
| MaxLastTimestamp | ciąg | Maksymalna liczba wszystkie sygnatury czasowe między wszystkie ścieżki (audio lub wideo). |
| TypeOfTrackWithMaxLastTimestamp | ciąg | Typ ścieżki (audio lub wideo) z maksymalną sygnatury czasowej ostatniego. |

## <a name="liveeventincomingvideostreamsoutofsync"></a>LiveEventIncomingVideoStreamsOutOfSync

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
      "FirstTimestamp": "2162058216",
      "FirstDuration": "2000",
      "SecondTimestamp": "2162057216",
      "SecondDuration": "2000"
    },
    "dataVersion": "1.0"
  }
]
```

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| FirstTimestamp | ciąg | Odebrano jeden z poziomów śledzi/jakości wideo typu sygnatura czasowa. |
| FirstDuration | ciąg | Czas trwania fragmentu danych za pomocą pierwszy znacznik czasu. |
| SecondTimestamp | ciąg  | Odebrano dla pewnego poziomu śledzenia/jakości wideo typu sygnatura czasowa. |
| SecondDuration | ciąg | Czas trwania fragmentu danych z sygnaturą czasową drugie. |

## <a name="liveeventingestheartbeat"></a>LiveEventIngestHeartbeat

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
| TrackType | ciąg | Typ ścieżki (Audio / wideo). |
| TrackName | ciąg | Nazwy ścieżki (albo, dostarczone przez koder lub, w przypadku protokołu RTMP, serwer generuje w *TrackType_Bitrate* formatu). |
| Szybkość transmisji bitów | liczba całkowita | Szybkość transmisji bitów toru. |
| IncomingBitrate | liczba całkowita | Obliczony transmisji bitów, oparte na fragmenty danych pochodzących z kodera. |
| LastTimestamp | ciąg | Sygnatura czasowa najnowsze odebrane dla ścieżki w ciągu ostatnich 20 sekund. |
| Skala czasu | ciąg | Skala czasu, w którym są wyrażane sygnatur czasowych. |
| OverlapCount | liczba całkowita | Liczba fragmentów danych miały nakładających się sygnatur czasowych w ciągu ostatnich 20 sekund. |
| DiscontinuityCount | liczba całkowita | Liczba przerw zaobserwowane w ciągu ostatnich 20 sekund. |
| NonIncreasingCount | liczba całkowita | Liczba fragmentów danych z sygnaturami czasowymi w przeszłości zostały odebrane w ciągu ostatnich 20 sekund. |
| UnexpectedBitrate | wartość logiczna | Jeśli szybkości transmisji z oczekiwanymi i rzeczywistymi różnią się przez więcej niż dozwolony limit w ciągu ostatnich 20 sekund. Wartość true, jeśli i tylko wtedy, gdy, IncomingBitrate jest > = 2 * szybkości transmisji bitów lub IncomingBitrate < = szybkości transmisji bitów/2 lub IncomingBitrate = 0. |
| Stan | ciąg | Stan zdarzenia na żywo. |
| W dobrej kondycji | wartość logiczna | Wskazuje, czy pozyskiwania jest w dobrej kondycji na podstawie liczby i flagi. Dobra kondycja ma wartość true Jeśli OverlapCount = 0 & & DiscontinuityCount = 0 & & NonIncreasingCount = 0 & & UnexpectedBitrate = false. |

## <a name="liveeventtrackdiscontinuitydetected"></a>LiveEventTrackDiscontinuityDetected

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
| TrackType | ciąg | Typ ścieżki (Audio / wideo). |
| TrackName | ciąg | Nazwy ścieżki (albo, dostarczone przez koder lub, w przypadku protokołu RTMP, serwer generuje w *TrackType_Bitrate* formatu). |
| Szybkość transmisji bitów | liczba całkowita | Szybkość transmisji bitów toru. |
| PreviousTimestamp | ciąg | Sygnatura czasowa poprzedniego fragmentu. |
| NewTimestamp | ciąg | Sygnatura czasowa bieżącego fragmentu. |
| DiscontinuityGap | ciąg | Przerwa między powyżej dwóch sygnatur czasowych. |
| Skala czasu | ciąg | Są reprezentowane w skali czasu, w której sygnatura czasowa i ciągłości przerwy. |

## <a name="common-event-properties"></a>Wspólne właściwości zdarzenia

Zdarzenie zawiera następujące dane najwyższego poziomu:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| temat | ciąg | Temat EventGrid. Ta właściwość ma identyfikator zasobu dla konta usługi Media Services. |
| Temat | ciąg | Ścieżka zasobu dla kanału usługi Media Services w ramach konta usługi Media Services. Łączenie zapewniają tematu oraz temat, możesz zasobu, identyfikator zadania. |
| Typ zdarzenia | ciąg | Jeden z typów zdarzeń zarejestrowane dla tego źródła zdarzeń. Na przykład "Microsoft.Media.JobStateChange". |
| eventTime | ciąg | Czas, którego zdarzenie jest generowane na podstawie czasu UTC dostawcy. |
| id | ciąg | Unikatowy identyfikator zdarzenia. |
| dane | obiekt | Dane zdarzenia usługi Media Services. |
| dataVersion | ciąg | Wersja schematu dla obiektu danych. Wydawca Określa wersję schematu. |
| metadataVersion | ciąg | Wersja schematu dla metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Usługa Event Grid udostępnia tę wartość. |

## <a name="next-steps"></a>Kolejne kroki

[Rejestrowanie zdarzeń zmiany stanu zadania](job-state-events-cli-how-to.md)