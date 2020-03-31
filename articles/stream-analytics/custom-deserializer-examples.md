---
title: Odczytanie danych wejściowych w dowolnym formacie przy użyciu deserializatorów niestandardowych platformy .NET w usłudze Azure Stream Analytics
description: W tym artykule opisano format serializacji i interfejsy, które definiują niestandardowe deserializery platformy .NET dla zadań chmury i usługi Azure Stream Analytics w chmurze i na urządzeniach brzegowych.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 270e9a31c28e7209cfe43ea8307b928ed3257a35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845261"
---
# <a name="read-input-in-any-format-using-net-custom-deserializers"></a>Odczytanie danych wejściowych w dowolnym formacie przy użyciu deserializatorów niestandardowych platformy .NET

Deserializatory niestandardowe platformy .NET umożliwiają zadaniu usługi Azure Stream Analytics odczytywanie danych z formatów spoza trzech [wbudowanych formatów danych.](stream-analytics-parsing-json.md) W tym artykule opisano format serializacji i interfejsy definiujące niestandardowe deseryizery platformy .NET dla zadań chmury i usługi Azure Stream Analytics dla chmury i usługi Edge. Istnieją również deserializatory dla buforu protokołu i formatu CSV.

## <a name="net-custom-deserializer"></a>Deserializator niestandardowy .NET

Następujące przykłady kodu są interfejsy, które definiują niestandardowe deserializer i implementują `StreamDeserializer<T>`.

`UserDefinedOperator`jest klasą podstawową dla wszystkich niestandardowych operatorów przesyłania strumieniowego. Inicjuje `StreamingContext`, który zawiera kontekst, który zawiera mechanizm publikowania diagnostyki, dla których należy debugować wszelkie problemy z deserializatorem.

```csharp
    public abstract class UserDefinedOperator
    {
        public abstract void Initialize(StreamingContext streamingContext);
    }
```

Poniższy fragment kodu jest deserializacji dla danych przesyłania strumieniowego. 

Błędy możliwe do pominięcia powinny `IStreamingDiagnostics` być `UserDefinedOperator`emitowane przy użyciu przeszedł przez 's Initialize metody. Wszystkie wyjątki będą traktowane jako błędy i deserializer zostanie odtworzony. Po określonej liczbie błędów zadanie przejdzie do stanu awarii.

`StreamDeserializer<T>`deserializuje strumień do obiektu `T`typu . Muszą być spełnione następujące warunki:

1. T jest klasą lub strukturą.
1. Wszystkie pola publiczne w T są albo
    1. Jeden z [sbyte, bajt, krótki, ushort, int, uint, long, DateTime, string, float, double] lub ich nullable odpowiedniki.
    1. Inna struktura lub klasa po tych samych regułach.
    1. Tablica `T2` typu, która jest zgodna z tymi samymi regułami.
    1. IList,`T2` gdzie T2 jest zgodny z tymi samymi regułami.
    1. Nie ma żadnych typów cyklicznych.

Parametr `stream` jest strumieniem zawierającym obiekt szeregowy. `Deserialize`zwraca kolekcję `T` wystąpień.

```csharp
    public abstract class StreamDeserializer<T> : UserDefinedOperator
    {
        public abstract IEnumerable<T> Deserialize(Stream stream);
    }
```

`StreamingContext`zawiera kontekst, który obejmuje mechanizm publikowania diagnostyki dla operatora użytkownika.

```csharp
    public abstract class StreamingContext
    {
        public abstract StreamingDiagnostics Diagnostics { get; }
    }
```

`StreamingDiagnostics`to diagnostyka dla operatorów zdefiniowanych przez użytkownika, w tym serializatora, deserializatora i funkcji zdefiniowanych przez użytkownika.

`WriteError`zapisuje komunikat o błędzie do dzienników diagnostycznych i wysyła błąd do diagnostyki.

`briefMessage`to krótki komunikat o błędzie. Ten komunikat pojawia się w diagnostyce i jest używany przez zespół produktu do celów debugowania. Nie dołączaj poufnych informacji i zachowaj wiadomość mniejszą niż 200 znaków

`detailedMessage`to szczegółowy komunikat o błędzie, który jest dodawany tylko do dzienników diagnostycznych w magazynie. Ten komunikat powinien mieć mniej niż 2000 znaków.

```csharp
    public abstract class StreamingDiagnostics
    {
        public abstract void WriteError(string briefMessage, string detailedMessage);
    }
```

## <a name="deserializer-examples"></a>Przykłady deserializer

W tej sekcji pokazano, jak napisać niestandardowe deserializery dla Protobuf i CSV. Aby uzyskać dodatkowe przykłady, takie jak format AVRO dla przechwytywania centrum zdarzeń, odwiedź [usługę Azure Stream Analytics w usłudze GitHub.](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers)

### <a name="protocol-buffer-protobuf-format"></a>Format buforu protokołu (Protobuf)

Jest to przykład przy użyciu formatu buforu protokołu.

Załóżmy następującą definicję buforu protokołu.

```proto
syntax = "proto3";
// protoc.exe from nuget "Google.Protobuf.Tools" is used to generate .cs file from this schema definition.
// Run below command to generate the csharp class
// protoc.exe --csharp_out=. MessageBodyProto.proto

package SimulatedTemperatureSensor;
message MessageBodyProto {
    message Ambient {
      double temperature = 1;
      int64 humidity = 2;
    }

    message Machine {
      double temperature = 1;
      double pressure = 2;
    }

    Machine machine = 1;
    Ambient ambient = 2;
    string timeCreated = 3;
}
```

Uruchamianie `protoc.exe` z **Google.Protobuf.Tools** NuGet generuje plik .cs z definicją. Wygenerowany plik nie jest wyświetlany w tym miejscu.

Poniższy fragment kodu jest deserializer implementacji przy założeniu, że wygenerowany plik jest uwzględniony w projekcie. Ta implementacja jest tylko cienka otoka nad wygenerowanym plikiem.

```csharp
    public class MessageBodyDeserializer : StreamDeserializer<SimulatedTemperatureSensor.MessageBodyProto>
    {
        public override IEnumerable<SimulatedTemperatureSensor.MessageBodyProto> Deserialize(Stream stream)
        {
            while (stream.Position < stream.Length)
            {
                yield return SimulatedTemperatureSensor.MessageBodyProto.Parser.ParseDelimitedFrom(stream);
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
        }
    }
```

### <a name="csv"></a>CSV

Poniższy fragment kodu jest prosty deserializer CSV, który również pokazuje błędy propagowania.

```csharp
using System.Collections.Generic;
using System.IO;

using Microsoft.Azure.StreamAnalytics;
using Microsoft.Azure.StreamAnalytics.Serialization;

namespace ExampleCustomCode.Serialization
{
    public class CustomCsvDeserializer : StreamDeserializer<CustomEvent>
    {
        private StreamingDiagnostics streamingDiagnostics;

        public override IEnumerable<CustomEvent> Deserialize(Stream stream)
        {
            using (var sr = new StreamReader(stream))
            {
                string line = sr.ReadLine();
                while (line != null)
                {
                    if (line.Length > 0 && !string.IsNullOrWhiteSpace(line))
                    {
                        string[] parts = line.Split(',');
                        if (parts.Length != 3)
                        {
                            streamingDiagnostics.WriteError("Did not get expected number of columns", $"Invalid line: {line}");
                        }
                        else
                        {
                            yield return new CustomEvent()
                            {
                                Column1 = parts[0],
                                Column2 = parts[1],
                                Column3 = parts[2]
                            };
                        }
                    }

                    line = sr.ReadLine();
                }
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
            this.streamingDiagnostics = streamingContext.Diagnostics;
        }
    }

    public class CustomEvent
    {
        public string Column1 { get; set; }

        public string Column2 { get; set; }

        public string Column3 { get; set; }
    }
}

```

## <a name="serialization-format-for-rest-apis"></a>Format serializacji dla interfejsów API REST

Każde wejście usługi Stream Analytics ma **format serializacji.** Aby uzyskać więcej informacji na temat opcji wprowadzania, zobacz dokumentację [interfejsu API interfejsu REST wejścia.](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input)

Następujący kod Javascript jest przykładem formatu serializacji deserializer .NET podczas korzystania z interfejsu API REST:

```javascript
{    
   "properties":{    
      "type":"stream",  
      "serialization":{    
         "type":"CustomCLR",  
         "properties":{    
            "serializationDllPath":"<path to the dll inside UserCustomCode\CLR\ folder>", 
            "serializationClassName":"<Full name of the deserializer class name>" 
         }  
      }
   }  
}  
```

`serializationClassName`powinna być klasą, `StreamDeserializer<T>`która implementuje . Jest to opisane w poniższej sekcji.

## <a name="region-support"></a>Wsparcie regionu

Ta funkcja jest dostępna w następujących regionach:

* Zachodnio-środkowe stany USA
* Europa Północna
* Wschodnie stany USA
* Zachodnie stany USA
* Wschodnie stany USA 2
* Europa Zachodnia

Możesz [poprosić o pomoc techniczną](https://aka.ms/ccodereqregion) dla dodatkowych regionów.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="when-will-this-feature-be-available-in-all-azure-regions"></a>Kiedy ta funkcja będzie dostępna we wszystkich regionach platformy Azure?

Ta funkcja jest dostępna w [6 regionach.](https://docs.microsoft.com/azure/stream-analytics/custom-deserializer-examples#region-support) Jeśli chcesz korzystać z tej funkcji w innym regionie, możesz [przesłać żądanie](https://aka.ms/ccodereqregion). Obsługa wszystkich regionów platformy Azure znajduje się na planie działania.

### <a name="can-i-access-metadatapropertyvalue-from-my-inputs-similar-to-getmetadatapropertyvalue-function"></a>Czy mogę uzyskać dostęp do metadataPropertyValue z moich danych wejściowych podobnych do funkcji GetMetadataPropertyValue?

Ta funkcja nie jest obsługiwana. Jeśli potrzebujesz tej możliwości, możesz zagłosować na to żądanie na [UserVoice](https://feedback.azure.com/forums/270577-stream-analytics/suggestions/38779801-accessing-input-metadata-properties-in-custom-dese).

### <a name="can-i-share-my-deserializer-implementation-with-the-community-so-that-others-can-benefit"></a>Czy mogę udostępnić moje wdrożenie deserializer społeczności, aby inni mogli korzystać?

Po zaimplementowanie deserializatora możesz pomóc innym, udostępniając go społeczności. Prześlij swój kod do [repozytorium Usługi Azure Stream Analytics GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers).

## <a name="next-steps"></a>Następne kroki

* [Niestandardowe deseratory platformy .NET dla zadań w chmurze usługi Azure Stream Analytics](custom-deserializer.md)
