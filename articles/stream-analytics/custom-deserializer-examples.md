---
title: Użyj deserializacji platformy .NET na potrzeby zadań Azure Stream Analytics
description: W tym artykule wyjaśniono format serializacji i interfejsy, które definiują niestandardowe deserializacji platformy .NET na potrzeby zadań w chmurze i krawędzi Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: f1452e56054948edffc6e9b3c98fa48d2589cb2a
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73024938"
---
# <a name="use-net-deserializers-for-azure-stream-analytics-jobs"></a>Użyj deserializacji platformy .NET na potrzeby zadań Azure Stream Analytics

Niestandardowe deserializacji platformy .NET umożliwiają zadanie Azure Stream Analytics odczytywanie danych z formatów poza trzema [wbudowanymi formatami danych](stream-analytics-parsing-json.md). W tym artykule wyjaśniono format serializacji i interfejsy, które definiują niestandardowe deserializacji platformy .NET na potrzeby zadań w chmurze i krawędzi Azure Stream Analytics. Istnieją również przykłady deserializacji dla buforu protokołu i formatu CSV.

## <a name="custom-net-deserializer"></a>Niestandardowa deserializacja platformy .NET

Poniższe przykłady kodu są interfejsami, które definiują niestandardową Deserializator i implementują `StreamDeserializer<T>`.

`UserDefinedOperator` jest klasą bazową dla wszystkich niestandardowych operatorów przesyłania strumieniowego. Inicjuje `StreamingContext`, która zawiera kontekst, który obejmuje mechanizm publikowania diagnostyki, dla którego konieczne będzie debugowanie wszelkich problemów z deserializatorem.

```csharp
    public abstract class UserDefinedOperator
    {
        public abstract void Initialize(StreamingContext streamingContext);
    }
```

Poniższy fragment kodu jest deserializacji dla danych przesyłanych strumieniowo. 

Błędy, które można pominąć, powinny być emitowane przy użyciu `IStreamingDiagnostics` przekazaną przez metodę Initialize `UserDefinedOperator`. Wszystkie wyjątki będą traktowane jako błędy i Deserializator zostanie utworzony ponownie. Po określonej liczbie błędów zadanie przejdzie w stan niepowodzenia.

`StreamDeserializer<T>` deserializacji strumienia do obiektu typu `T`. Muszą zostać spełnione następujące warunki:

1. T jest klasą lub strukturą.
1. Wszystkie pola publiczne w T są
    1. Jeden z [wartości parametrów, Byte, Short, UShort, int, uint, Long, DateTime, String, float, Double] lub ich odpowiedników dopuszczających wartość null.
    1. Inna struktura lub Klasa zgodnie z tymi samymi regułami.
    1. Tablica typu `T2` zgodna z tymi samymi regułami.
    1. IList`T2` gdzie T2 stosuje się do tych samych reguł.
    1. Nie ma żadnych typów cyklicznych.

Parametr `stream` jest strumieniem zawierającym serializowany obiekt. `Deserialize` zwraca kolekcję wystąpień `T`.

```csharp
    public abstract class StreamDeserializer<T> : UserDefinedOperator
    {
        public abstract IEnumerable<T> Deserialize(Stream stream);
    }
```

`StreamingContext` zawiera kontekst, który obejmuje mechanizm publikowania diagnostyki dla operatora użytkownika.

```csharp
    public abstract class StreamingContext
    {
        public abstract StreamingDiagnostics Diagnostics { get; }
    }
```

`StreamingDiagnostics` to Diagnostyka dla operatorów zdefiniowanych przez użytkownika, w tym Serializator, Deserializator i zdefiniowane przez użytkownika funkcje.

`WriteError` zapisuje komunikat o błędzie w dziennikach diagnostycznych i wysyła błąd do diagnostyki.

`briefMessage` jest krótkim komunikatem o błędzie. Ten komunikat jest wyświetlany w diagnostyce i jest używany przez zespół produktu do celów debugowania. Nie uwzględniaj poufnych informacji i Zachowaj komunikat krótszy niż 200 znaków

`detailedMessage` jest szczegółowym komunikatem o błędzie, który jest dodawany tylko do dzienników diagnostycznych w magazynie. Ta wiadomość powinna być krótsza niż 2000 znaków.

```csharp
    public abstract class StreamingDiagnostics
    {
        public abstract void WriteError(string briefMessage, string detailedMessage);
    }
```

## <a name="deserializer-examples"></a>Przykłady deserializacji

W tej sekcji pokazano, jak napisać niestandardowe deserializatory dla protobuf i CSV. Aby uzyskać dodatkowe przykłady, odwiedź stronę [Azure Stream Analytics w witrynie GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers).

### <a name="protocol-buffer-protobuf-format"></a>Format buforu protokołu (protobuf)

Jest to przykład przy użyciu formatu buforu protokołu.

Przyjmij następującą definicję buforu protokołu.

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

Uruchamianie `protoc.exe` z narzędzia NuGet usługi **Google. protobuf. Tools** generuje plik. cs z definicją. Wygenerowany plik nie jest tutaj wyświetlany.

Poniższy fragment kodu jest implementacją deserializacji, przy założeniu, że wygenerowany plik zostanie uwzględniony w projekcie. Ta implementacja jest tylko elastyczną otoką w wygenerowanym pliku.

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

Poniższy fragment kodu jest prostym deserializatorem CSV, który również ilustruje propagowanie błędów.

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

## <a name="serialization-format-for-rest-apis"></a>Format serializacji interfejsów API REST

Każde dane wejściowe Stream Analytics ma **format serializacji**. Aby uzyskać więcej informacji na temat opcji wprowadzania, zobacz dokumentację [interfejsu API REST](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input) .

Poniższy kod JavaScript jest przykładem formatu serializacji deserializacji .NET podczas korzystania z interfejsu API REST:

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

`serializationClassName` powinna być klasą implementującą `StreamDeserializer<T>`. Opisano to w poniższej sekcji.

## <a name="region-support"></a>Obsługa regionów

Ta funkcja jest dostępna w następujących regionach:

* Zachodnio-środkowe stany USA (dostępne)
* Europa Północna (dostępna)
* Wschodnie stany USA (dostępne)
* Zachodnie stany USA (od razu — trwa wycofywanie)
* Wschodnie stany USA 2 (od razu w trakcie wdrażania)
* Europa Zachodnia (wkrótce)

Możesz [zażądać obsługi](https://aka.ms/ccodereqregion) dodatkowych regionów.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="when-will-this-feature-be-available-in-all-azure-regions"></a>Kiedy ta funkcja będzie dostępna we wszystkich regionach świadczenia usługi Azure?

Ta funkcja jest dostępna w 6 regionach (#region-Support). Jeśli interesuje Cię korzystanie z tej funkcji w innym regionie, możesz [przesłać żądanie](https://aka.ms/ccodereqregion). Obsługa wszystkich regionów świadczenia usługi Azure jest zaplanowania.

### <a name="can-i-access-metadatapropertyvalue-from-my-inputs-similar-to-getmetadatapropertyvalue-function"></a>Czy mogę uzyskać dostęp do MetadataPropertyValue z danych wejściowych, podobnie jak funkcja GetMetadataPropertyValue?

Ta funkcja nie jest obsługiwana. Jeśli potrzebujesz tej możliwości, możesz głosować na to żądanie w usłudze [UserVoice](https://feedback.azure.com/forums/270577-stream-analytics/suggestions/38779801-accessing-input-metadata-properties-in-custom-dese).

### <a name="can-i-share-my-deserializer-implementation-with-the-community-so-that-others-can-benefit"></a>Czy mogę udostępnić moją implementację deserializacji ze społecznością, tak aby inni mogli skorzystać?

Po zaimplementowaniu deserializacji można ułatwić innym użytkownikom udostępnianie go społeczności. Prześlij swój kod do [Azure Stream Analytics repozytorium GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers).

## <a name="next-steps"></a>Następne kroki

* [Niestandardowe deserializacji platformy .NET dla Azure Stream Analytics zadań w chmurze](custom-deserializer.md)
