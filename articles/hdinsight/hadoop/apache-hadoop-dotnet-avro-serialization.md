---
title: Serializowanie danych na platformie Hadoop — Microsoft Avro Library – Azure
description: Dowiedz się, jak do serializacji i deserializacji danych na platformie Hadoop w HDInsight przy użyciu Microsoft Avro Library powinien je zachowywać pamięci, bazy danych lub pliku.
keywords: avro, hadoop avro
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.custom: hdiseo17may2017
ms.openlocfilehash: 5928c6490c9de6c48b75800158b8298007d7b8ed
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51246909"
---
# <a name="serialize-data-in-hadoop-with-the-microsoft-avro-library"></a>Serializowanie danych na platformie Hadoop przy użyciu Microsoft Avro Library

>[!NOTE]
>Zestaw SDK format Avro nie jest już obsługiwana przez firmę Microsoft. Biblioteka jest wspieranym przez społeczność "open source". Źródła dla biblioteki znajdują się w [Github](https://github.com/Azure/azure-sdk-for-net/tree/master/src/ServiceManagement/HDInsight/Microsoft.Hadoop.Avro).

W tym temacie pokazano, jak używać [Microsoft Avro Library](https://github.com/Azure/azure-sdk-for-net/tree/master/src/ServiceManagement/HDInsight/Microsoft.Hadoop.Avro) do wykonywania serializacji obiektów i innych struktur danych do strumieni w celu utrwalenia ich w pamięci, bazie danych lub pliku. Pokazano również, jak wykonać deserializacji je odzyskać oryginalne obiekty.

[!INCLUDE [windows-only](../../../includes/hdinsight-windows-only.md)]

## <a name="apache-avro"></a>Apache Avro
<a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Microsoft Avro Library</a> implementuje systemu Apache Avro danych serializacji dla środowiska Microsoft.NET. Apache Avro udostępnia format wymiany danych binarnych dla serializacji. Używa ona <a href="http://www.json.org" target="_blank">JSON</a> do definiowania niezależny od języka schematu z niesprecyzowanym współdziałanie języków. Dane serializowane w jednym języku mogą być odczytywane w innym. Obecnie są obsługiwane C, C++, C#, Java, PHP, Python i Ruby. Szczegółowe informacje dotyczące formatu można znaleźć w <a href="http://avro.apache.org/docs/current/spec.html" target="_blank">Apache Avro Specification</a>. 

>[!NOTE]
>Microsoft Avro Library nie obsługuje wywołania procedury zdalnej część tej specyfikacji.
>

Zserializowana reprezentacja obiektu w systemie Avro składa się z dwóch części: schemat i wartości rzeczywistej. W schemacie Avro w tym artykule opisano model danych niezależny od języka, serializacji danych przy użyciu formatu JSON. Są one przedstawiane równolegle binarną reprezentację danych. Mających niezależnie od reprezentacja binarna schemat zezwala na każdy obiekt ma zostać zapisany z nie kosztów ogólnych na wartość, co serializacji szybkie i reprezentacją małych.

## <a name="the-hadoop-scenario"></a>Scenariusz usługi Hadoop
Format serializacji Apache Avro jest powszechnie używana w usłudze Azure HDInsight i innych środowisk technologii Apache Hadoop. Avro zapewnia wygodny sposób złożonych danych reprezentują struktury w ramach zadania MapReduce usługi Hadoop. Format plików Avro (pliku kontenera obiektu Avro) został zaprojektowany do obsługi rozproszony model programowania MapReduce. Kluczową funkcją, która umożliwia dystrybucję to, że pliki są "podzielne", w tym sensie, że jeden można wyszukiwać dowolne miejsce w pliku i rozpocząć odczytywanie od określonego bloku.

## <a name="serialization-in-avro-library"></a>Serializacja w biblioteki Avro
Biblioteka .NET dla Avro obsługuje serializacji obiektów na dwa sposoby:

* **odbicie** — schemat JSON dla typów automatycznie składa się z danych atrybuty kontraktu typów .NET do zserializowania.
* **ogólny rekordu** -schematu JSON jest jawnie określona w rekord, reprezentowany przez [ **AvroRecord** ](https://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) klasy, gdy brak typów .NET do opisania schematu danych serializacji.

Jeśli schemat danych jest znany do zapisywania i odczytywania strumienia, dane mogą być wysyłane bez jego schematu. W przypadkach, gdy używany jest plik kontenera obiektu Avro, schematu są przechowywane w pliku. Można określić inne parametry, takie jak kodera-dekodera, używany do kompresji danych. Te scenariusze są opisane bardziej szczegółowo i przedstawiono w poniższych przykładach kodu:

## <a name="install-avro-library"></a>Zainstaluj biblioteki Avro
Wymagane jest spełnienie następujących przed zainstalowaniem biblioteki:

* <a href="https://www.microsoft.com/download/details.aspx?id=17851" target="_blank">Microsoft .NET Framework 4</a>
* <a href="http://james.newtonking.com/json" target="_blank">Newtonsoft Json.NET</a> (6.0.4 lub nowszy)

Należy pamiętać, że zależności plik jest pobierany automatycznie instalacji programu Microsoft Avro Library. Procedura znajduje się w następującej sekcji:

Microsoft Avro Library jest dystrybuowane jako pakiet NuGet, które mogą być instalowane z Visual Studio za pomocą poniższej procedury:

1. Wybierz **projektu** -> karta **Zarządzaj pakietami NuGet...**
2. Wyszukaj termin "Microsoft.Hadoop.Avro" w **Wyszukaj Online** pole.
3. Kliknij przycisk **zainstalować** znajdujący się obok **Biblioteka usługi Microsoft Azure HDInsight Avro**.

Należy pamiętać, że pliku Newtonsoft.Json.dll (> = 6.0.4) zależności także jest automatycznie pobierany z Microsoft Avro Library.

Kod źródłowy Microsoft Avro Library znajduje się w temacie [Github](https://github.com/Azure/azure-sdk-for-net/tree/master/src/ServiceManagement/HDInsight/Microsoft.Hadoop.Avro).

## <a name="compile-schemas-using-avro-library"></a>Skompilować schematy przy użyciu biblioteki Avro
Microsoft Avro Library zawiera narzędzie generowania kodu, które umożliwia tworzenie typów języka C# automatycznie w oparciu o wcześniej zdefiniowanego schematu JSON. Narzędzie generowania kodu nie będzie on rozpowszechniany binarny plik wykonywalny, ale można łatwo utworzyć za pomocą poniższej procedury:

1. Pobierz plik zip do najnowszej wersji zestawu SDK HDInsight kodu źródłowego z <a href="http://hadoopsdk.codeplex.com/SourceControl/latest#" target="_blank">Microsoft .NET zestaw SDK dla platformy Hadoop</a>. (Kliknij **Pobierz** ikony nie **pliki do pobrania** karty.)
2. Wyodrębnianie zestawu HDInsight SDK do katalogu na komputerze przy użyciu programu .NET Framework 4 jest zainstalowany i połączony z Internetem do pobrania niezbędnych zależności pakietów NuGet. Poniżej przyjęto założenie, że kod źródłowy został wyodrębniony do C:\SDK.
3. Przejdź do folderu C:\SDK\src\Microsoft.Hadoop.Avro.Tools i uruchom build.bat. (Plik wywołuje program MSBuild z dystrybucji 32-bitowego programu .NET Framework. Jeśli chcesz użyć wersji 64-bitowych, Edycja build.bat i obserwować komentarze wewnątrz pliku). Upewnij się, że kompilacja zakończyła się powodzeniem. (W niektórych systemach, program MSBuild może generować ostrzeżenia. Ostrzeżenia te nie wpływają na narzędziu tak długo, jak nie ma żadnych błędów kompilacji.)
4. Skompilowany narzędzie znajduje się w C:\SDK\Bin\Unsigned\Release\Microsoft.Hadoop.Avro.Tools.

Aby zapoznać się ze składnią wiersza polecenia, uruchom następujące polecenie z folderu, w którym znajduje się narzędzie generowania kodu: `Microsoft.Hadoop.Avro.Tools help /c:codegen`

Aby przetestować narzędzie, można wygenerować klas języka C# z przykładowego pliku schematu JSON, wyposażone w kodzie źródłowym. Wykonaj następujące polecenie:

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:

To powinien utworzyć dwa pliki C# w bieżącym katalogu: SensorData.cs i Location.cs.

Aby poznać logikę, która używa narzędzie generowania kodu podczas konwertowania schematu JSON na typy języka C#, zobacz plik, który GenerationVerification.feature znajduje się w C:\SDK\src\Microsoft.Hadoop.Avro.Tools\Doc.

Przestrzenie nazw są wyodrębniane z schematu JSON, przy użyciu logiki opisany w pliku, wymienione w poprzednim akapicie. Przestrzenie nazw wyodrębnione ze schematu pierwszeństwo niezależnie od rodzaju znajduje się za pomocą parametru /n w wierszu polecenia narzędzia. Jeśli chcesz przesłonić przestrzeni nazw zawartych w schemacie, użyj parametru /nf. Na przykład aby zmienić wszystkie przestrzenie nazw z SampleJSONSchema.avsc my.own.nspace, wykonaj następujące polecenie:

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:. /nf:my.own.nspace

## <a name="about-the-samples"></a>O przykłady
Sześć przykłady podane w tym temacie pokazują różne scenariusze obsługiwane przez Microsoft Avro Library. Microsoft Avro Library jest przeznaczona do pracy z dowolnym strumienia. W tych przykładach danych jest przetwarzany za pomocą strumieni pamięci, a nie strumieni plików lub bazy danych dla uproszczenia i zachowania spójności. Podejście przyjęte w środowisku produkcyjnym zależy od wymagań danego scenariusza, źródła danych i woluminów, ograniczeń związanych z wydajnością i innych czynników.

Pierwszych dwóch przykładach pokazano, jak do serializacji i deserializacji danych w pamięci, buforów strumieni przy użyciu odbicia i ogólny rekordów. Schemat w tych dwóch przypadkach przyjęto, że mogą być współużytkowane przez czytniki i moduły zapisujące poza pasmem.

Trzecia i czwarta przykłady pokazują, jak do serializacji i deserializacji danych przy użyciu plików systemu Avro obiektu kontenera. Gdy dane są przechowywane w pliku kontenera Avro, jego schematu jest zawsze przechowywany z nim, ponieważ schematu muszą zostać udostępnione do deserializacji.

Plik zawierający pierwsze cztery przykłady można pobrać z <a href="https://code.msdn.microsoft.com/Serialize-data-with-the-86055923" target="_blank">przykłady kodu platformy Azure</a> lokacji.

Piąty przykład pokazuje jak używać kodera-dekodera kompresji niestandardowych dla plików kontenera obiektu Avro. Przykład zawierającego kod, aby w tym przykładzie można pobrać z <a href="https://code.msdn.microsoft.com/Serialize-data-with-the-67159111" target="_blank">przykłady kodu platformy Azure</a> lokacji.

Szósty przykład pokazuje sposób użycia serializacji Avro do przekazywania danych do usługi Azure Blob storage, a następnie analizować za pomocą programu Hive przy użyciu klastra usługi HDInsight (Hadoop). Można go pobrać ze <a href="https://code.msdn.microsoft.com/Using-Avro-to-upload-data-ae81b1e3" target="_blank">przykłady kodu platformy Azure</a> lokacji.

Oto linki do przykładów z sześciu omówione w temacie:

* <a href="#Scenario1">**Serializacja za pomocą odbicia** </a> — schemat JSON dla typów można było serializować automatycznie składa się z danych atrybuty kontraktu.
* <a href="#Scenario2">**Serializacji z rekordem ogólny** </a> — w rekordzie schematu JSON jest jawnie określone, jeśli żaden typ .NET będzie dostępny odbicia.
* <a href="#Scenario3">**Serializacji przy użyciu plików kontenera obiektów za pomocą odbicia** </a> -schematu JSON jest automatycznie utworzone i udostępnione wraz z serializowane dane za pomocą pliku Avro obiektu kontenera.
* <a href="#Scenario4">**Serializacji przy użyciu plików kontenera obiektów z rekordem ogólny** </a> -schematu JSON jest jawnie określone przed serializacji i udostępnione wraz z danymi za pomocą pliku Avro obiektu kontenera.
* <a href="#Scenario5">**Przy użyciu plików kontenera obiektów przy użyciu kodera-dekodera kompresji niestandardowej serializacji** </a> — w przykładzie pokazano, jak utworzyć plik Avro obiektu kontenera za pomocą niestandardowych implementacji .NET kodera-dekodera kompresji danych Deflate.
* <a href="#Scenario6">**Przekazywanie danych dla usługi Microsoft Azure HDInsight przy użyciu systemu Avro** </a> — w przykładzie pokazano, jak serializacji Avro współdziała z usługą HDInsight. Aktywna subskrypcja platformy Azure i dostęp do klastra usługi Azure HDInsight są wymagane do uruchomienia tego przykładu.

## <a name="Scenario1"></a>Przykład 1: Serializacja za pomocą odbicia
Schemat JSON dla typów może być automatycznie tworzona przez Microsoft Avro Library za pośrednictwem odbicia z danych kontraktu atrybutów obiektów języka C# do zserializowania. Tworzy Microsoft Avro Library [ **IAvroSeralizer<T>**  ](https://msdn.microsoft.com/library/dn627341.aspx) do identyfikacji pola, które mają być serializowane.

W tym przykładzie obiekty ( **SensorData** klasy ze składową **lokalizacji** struktury) są serializowane w strumieniu pamięci i ten strumień jest z kolei deserializacji. Wynik jest następnie porównywany z pierwszego wystąpienia, aby potwierdzić, że **SensorData** obiektu odzyskać jest taka sama jak oryginalne.

Schemat w tym przykładzie przyjęto, że aby współdzielić je między czytników i składników zapisywania, dlatego format Avro obiektu kontenera nie jest wymagane. Na przykład jak do serializacji i deserializacji danych w pamięci, buforów przy użyciu odbicia z formatu kontenera obiektu, gdy schemat można udostępniać danych zobacz <a href="#Scenario3">serializacji przy użyciu plików kontenera obiektów za pomocą odbicia</a>.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serialize and deserialize sample data set represented as an object using reflection.
            //No explicit schema definition is required - schema of serialized objects is automatically built.
            public void SerializeDeserializeObjectUsingReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION\n");
                Console.WriteLine("Serializing Sample Data Set...");

                //Create a new AvroSerializer instance and specify a custom serialization strategy AvroDataContractResolver
                //for serializing only properties attributed with DataContract/DateMember
                var avroSerializer = AvroSerializer.Create<SensorData>();

                //Create a memory stream buffer
                using (var buffer = new MemoryStream())
                {
                    //Create a data set by using sample class and struct
                    var expected = new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } };

                    //Serialize the data to the specified stream
                    avroSerializer.Serialize(buffer, expected);


                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Deserialize data from the stream and cast it to the same type used for serialization
                    var actual = avroSerializer.Deserialize(buffer);

                    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                    //Finally, verify that deserialized data matches the original one
                    bool isEqual = this.Equal(expected, actual);

                    Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);

                }
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }



            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization to memory using reflection
                Sample.SerializeDeserializeObjectUsingReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION
    //
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


## <a name="sample-2-serialization-with-a-generic-record"></a>Przykład 2: Serializacji z rekordem ogólny
Schemat JSON można jawnie określone w rekordzie ogólnego, gdy nie można używać odbicia, ponieważ dane nie może być reprezentowany za pomocą klas platformy .NET za pomocą kontraktu danych. Ta metoda jest mniejsza niż przy użyciu odbicia. W takich przypadkach schemat danych może być również dynamiczne, oznacza to, że nie jest znany w czasie kompilacji. Dane reprezentowane w formacie wartości rozdzielanych przecinkami (CSV), którego schemat jest nieznany, dopóki nie jest przekształcane do formatu Avro w czasie wykonywania znajduje się przykład tego rodzaju scenariusza dynamicznej.

W tym przykładzie przedstawiono sposób tworzenia i używania [ **AvroRecord** ](https://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) jawnie określić schematu JSON, jak go wypełnić przy użyciu danych i jak do serializacji i deserializacji go. Wynik jest następnie porównywany z pierwszego wystąpienia, aby upewnić się, że rekord odzyskać jest taka sama jak oryginalne.

Schemat w tym przykładzie przyjęto, że aby współdzielić je między czytników i składników zapisywania, dlatego format Avro obiektu kontenera nie jest wymagane. Na przykład jak do serializacji i deserializacji danych w pamięci, buforów przy użyciu ogólnych rekordu z formatu kontenera obiektu, gdy schemat musi być uwzględniony w danych serializacji zobacz <a href="#Scenario4">serializacji przy użyciu obiektu kontenera plików za pomocą ogólny rekordu</a> przykład.

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;
    using Microsoft.Hadoop.Avro;

    //This class contains all methods demonstrating
    //the usage of Microsoft Avro Library
    public class AvroSample
    {

        //Serialize and deserialize sample data set by using a generic record.
        //A generic record is a special class with the schema explicitly defined in JSON.
        //All serialized data should be mapped to the fields of the generic record,
        //which in turn is then serialized.
        public void SerializeDeserializeObjectUsingGenericRecords()
        {
            Console.WriteLine("SERIALIZATION USING GENERIC RECORD\n");
            Console.WriteLine("Defining the Schema and creating Sample Data Set...");

            //Define the schema in JSON
            const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        {
                                            ""name"":""Location"",
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

            //Create a generic serializer based on the schema
            var serializer = AvroSerializer.CreateGeneric(Schema);
            var rootSchema = serializer.WriterSchema as RecordSchema;

            //Create a memory stream buffer
            using (var stream = new MemoryStream())
            {
                //Create a generic record to represent the data
                dynamic location = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location.Floor = 1;
                location.Room = 243;

                dynamic expected = new AvroRecord(serializer.WriterSchema);
                expected.Location = location;
                expected.Value = new byte[] { 1, 2, 3, 4, 5 };

                Console.WriteLine("Serializing Sample Data Set...");

                //Serialize the data
                serializer.Serialize(stream, expected);

                stream.Seek(0, SeekOrigin.Begin);

                Console.WriteLine("Deserializing Sample Data Set...");

                //Deserialize the data into a generic record
                dynamic actual = serializer.Deserialize(stream);

                Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                //Finally, verify the results
                bool isEqual = expected.Location.Floor.Equals(actual.Location.Floor);
                isEqual = isEqual && expected.Location.Room.Equals(actual.Location.Room);
                isEqual = isEqual && ((byte[])expected.Value).SequenceEqual((byte[])actual.Value);
                Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);
            }
        }

        static void Main()
        {

            string sectionDivider = "---------------------------------------- ";

            //Create an instance of AvroSample class and invoke methods
            //illustrating different serializing approaches
            AvroSample Sample = new AvroSample();

            //Serialization to memory using generic record
            Sample.SerializeDeserializeObjectUsingGenericRecords();

            Console.WriteLine(sectionDivider);
            Console.WriteLine("Press any key to exit.");
            Console.Read();
        }
    }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


## <a name="sample-3-serialization-using-object-container-files-and-serialization-with-reflection"></a>Przykład 3: Serializacji przy użyciu obiektu kontenera plików i serializacji, za pomocą odbicia
Ten przykład jest podobny do scenariusza w <a href="#Scenario1"> pierwszy przykład</a>, gdy schemat jest niejawnie określony za pomocą odbicia. Różnica polega na tym, w tym miejscu, schemat jest nie zakłada się, że określane z czytnika, z deserializuje go. **SensorData** obiektów serializacji i ich niejawnie określony schemat są przechowywane w pliku kontenera obiektu Avro, reprezentowane przez [ **AvroContainer** ](https://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx) klasy.

Dane jest serializowana, w tym przykładzie za pomocą [ **SequentialWriter<SensorData>**  ](https://msdn.microsoft.com/library/dn627340.aspx) i zdeserializowany z [ **SequentialReader<SensorData>**  ](https://msdn.microsoft.com/library/dn627340.aspx). Wynik następnie jest porównywany początkowej wystąpień, aby zapewnić tożsamości.

Dane w pliku obiektu kontenera jest skompresowany za pośrednictwem domyślnie [ **Deflate** ] [ deflate-100] kodera-dekodera kompresji z .NET Framework 4. Zobacz <a href="#Scenario5"> przykład piąty</a> w tym temacie, aby dowiedzieć się, jak użyć wersji nowszej i doskonałe [ **Deflate** ] [ deflate-110] kodera-dekodera kompresji dostępne w programie .NET Framework 4.5.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes the sample data set by using reflection and Avro object container files.
            //Serialized data is compressed with the Deflate codec.
            public void SerializeDeserializeUsingObjectContainersReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro object container file
                string path = "AvroSampleReflectionDeflate.avro";

                //Create a data set by using sample class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Data is compressed using the Deflate codec.
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream by using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader instance for type SensorData, which deserializes all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true)))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            bool isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual);
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using reflection to Avro object container file
                Sample.SerializeDeserializeUsingObjectContainersReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


## <a name="sample-4-serialization-using-object-container-files-and-serialization-with-generic-record"></a>Przykład 4: Serializacji przy użyciu obiektu kontenera plików i serializacji z rekordem ogólny
Ten przykład jest podobny do scenariusza w <a href="#Scenario2"> drugi przykład</a>, gdzie schemat jest jawnie określony za pomocą formatu JSON. Różnica polega na tym, w tym miejscu, schemat jest nie zakłada się, że określane z czytnika, z deserializuje go.

Testowego zestawu danych są zbierane na listę wartości [ **AvroRecord** ](https://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) obiektów za pomocą jawnie zdefiniowanych schematu JSON, a następnie zapisywane w pliku kontenera obiektu reprezentowanego przez [  **AvroContainer** ](https://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx) klasy. Ten plik kontenera tworzy moduł zapisujący służący do serializowania danych bez kompresji, strumień pamięci, który jest następnie zapisywany do pliku. [ **Codec.Null** ](https://msdn.microsoft.com/library/microsoft.hadoop.avro.container.codec.null.aspx) parametr służący do tworzenia modułu odczytującego Określa, że te dane nie jest skompresowany.

Dane są następnie odczytu z pliku i przeprowadzona deserializacja kolekcji obiektów. Ta kolekcja jest porównywany z początkowej listę rekordów Avro, aby upewnić się, że są identyczne.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro.Schema;
        using Microsoft.Hadoop.Avro;

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes a sample data set by using a generic record and Avro object container files.
            //Serialized data is not compressed.
            public void SerializeDeserializeUsingObjectContainersGenericRecord()
            {
                Console.WriteLine("SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro object container file
                string path = "AvroSampleGenericRecordNullCodec.avro";

                Console.WriteLine("Defining the Schema and creating Sample Data Set...");

                //Define the schema in JSON
                const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        {
                                            ""name"":""Location"",
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

                //Create a generic serializer based on the schema
                var serializer = AvroSerializer.CreateGeneric(Schema);
                var rootSchema = serializer.WriterSchema as RecordSchema;

                //Create a generic record to represent the data
                var testData = new List<AvroRecord>();

                dynamic expected1 = new AvroRecord(rootSchema);
                dynamic location1 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location1.Floor = 1;
                location1.Room = 243;
                expected1.Location = location1;
                expected1.Value = new byte[] { 1, 2, 3, 4, 5 };
                testData.Add(expected1);

                dynamic expected2 = new AvroRecord(rootSchema);
                dynamic location2 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location2.Floor = 1;
                location2.Room = 244;
                expected2.Location = location2;
                expected2.Value = new byte[] { 6, 7, 8, 9 };
                testData.Add(expected2);

                //Serializing and saving data to file.
                //Create a MemoryStream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Data is not compressed (Null compression codec).
                    using (var writer = AvroContainer.CreateGenericWriter(Schema, buffer, Codec.Null))
                    {
                        using (var streamWriter = new SequentialWriter<object>(writer, 24))
                        {
                            // Serialize the data to stream by using the sequential writer
                            testData.ForEach(streamWriter.Write);
                        }
                    }

                    Console.WriteLine("Saving serialized data to file...");

                    //Save stream to file
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing the data.
                //Create a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader instance for type SensorData, which deserializes all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    using (var reader = AvroContainer.CreateGenericReader(buffer))
                    {
                        using (var streamReader = new SequentialReader<object>(reader))
                        {
                            var results = streamReader.Objects;

                            Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                            //Finally, verify the results
                            var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = (dynamic)serialized, actual = (dynamic)deserialized });
                            int count = 1;
                            foreach (var pair in pairs)
                            {
                                bool isEqual = pair.expected.Location.Floor.Equals(pair.actual.Location.Floor);
                                isEqual = isEqual && pair.expected.Location.Room.Equals(pair.actual.Location.Room);
                                isEqual = isEqual && ((byte[])pair.expected.Value).SequenceEqual((byte[])pair.actual.Value);
                                Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                                count++;
                            }
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using the given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of the AvroSample class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using generic record to Avro object container file
                Sample.SerializeDeserializeUsingObjectContainersGenericRecord();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.




## <a name="sample-5-serialization-using-object-container-files-with-a-custom-compression-codec"></a>Przykład 5: Serializacji przy użyciu plików kontenera obiektów przy użyciu kodera-dekodera kompresji niestandardowe
Piąty przykład pokazuje jak używać kodera-dekodera kompresji niestandardowych dla plików kontenera obiektu Avro. Przykład zawierającego kod, aby w tym przykładzie można pobrać z [przykłady kodu platformy Azure](https://code.msdn.microsoft.com/Serialize-data-with-the-67159111) lokacji.

[Avro Specification](http://avro.apache.org/docs/current/spec.html#Required+Codecs) umożliwia użycie kodera-dekodera kompresji opcjonalne (oprócz **Null** i **Deflate** ustawień domyślnych). W tym przykładzie nie implementuje nowe kodera-dekodera takich jak Snappy (wymienione jako obsługiwane opcjonalne kodera-dekodera w [Avro Specification](http://avro.apache.org/docs/current/spec.html#snappy)). Pokazuje, jak używać implementacji .NET Framework 4.5 [ **Deflate** ] [ deflate-110] koder-dekoder, który zapewnia lepszą algorytmu kompresji, na podstawie [zlib ](http://zlib.net/) blibliotera kompresji niż domyślna wersja .NET Framework 4.

    //
    // This code needs to be compiled with the parameter Target Framework set as ".NET Framework 4.5"
    // to ensure the desired implementation of the Deflate compression algorithm is used.
    // Ensure your C# project is set up accordingly.
    //

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.IO;
        using System.IO.Compression;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        #region Defining objects for serialization
        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }
        #endregion

        #region Defining custom codec based on .NET Framework V.4.5 Deflate
        //Avro.NET codec class contains two methods,
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed),
        //which are the key ones for data compression.
        //To enable a custom codec, one needs to implement these methods for the required codec.

        #region Defining Compression and Decompression Streams
        //DeflateStream (class from System.IO.Compression namespace that implements Deflate algorithm)
        //cannot be directly used for Avro because it does not support vital operations like Seek.
        //Thus one needs to implement two classes inherited from stream
        //(one for compressed and one for decompressed stream)
        //that use Deflate compression and implement all required features.
        internal sealed class CompressionStreamDeflate45 : Stream
        {
            private readonly Stream buffer;
            private DeflateStream compressionStream;

            public CompressionStreamDeflate45(Stream buffer)
            {
                Debug.Assert(buffer != null, "Buffer is not allowed to be null.");

                this.compressionStream = new DeflateStream(buffer, CompressionLevel.Fastest, true);
                this.buffer = buffer;
            }

            public override bool CanRead
            {
                get { return this.buffer.CanRead; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return this.buffer.CanWrite; }
            }

            public override void Flush()
            {
                this.compressionStream.Close();
            }

            public override long Length
            {
                get { return this.buffer.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.buffer.Position;
                }

                set
                {
                    this.buffer.Position = value;
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.buffer.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                return this.buffer.Seek(offset, origin);
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                this.compressionStream.Write(buffer, offset, count);
            }

            protected override void Dispose(bool disposed)
            {
                base.Dispose(disposed);

                if (disposed)
                {
                    this.compressionStream.Dispose();
                    this.compressionStream = null;
                }
            }
        }

        internal sealed class DecompressionStreamDeflate45 : Stream
        {
            private readonly DeflateStream decompressed;

            public DecompressionStreamDeflate45(Stream compressed)
            {
                this.decompressed = new DeflateStream(compressed, CompressionMode.Decompress, true);
            }

            public override bool CanRead
            {
                get { return true; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return false; }
            }

            public override void Flush()
            {
                this.decompressed.Close();
            }

            public override long Length
            {
                get { return this.decompressed.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.decompressed.Position;
                }

                set
                {
                    throw new NotSupportedException();
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.decompressed.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                throw new NotSupportedException();
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                throw new NotSupportedException();
            }

            protected override void Dispose(bool disposing)
            {
                base.Dispose(disposing);

                if (disposing)
                {
                    this.decompressed.Dispose();
                }
            }
        }
        #endregion

        #region Define Codec
        //Define the actual codec class containing the required methods for manipulating streams:
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed).
        //Codec class uses classes for compressed and decompressed streams defined above.
        internal sealed class DeflateCodec45 : Codec
        {

            //We merely use different IMPLEMENTATIONS of Deflate, so CodecName remains "deflate"
            public static readonly string CodecName = "deflate";

            public DeflateCodec45()
                : base(CodecName)
            {
            }

            public override Stream GetCompressedStreamOver(Stream decompressed)
            {
                if (decompressed == null)
                {
                    throw new ArgumentNullException("decompressed");
                }

                return new CompressionStreamDeflate45(decompressed);
            }

            public override Stream GetDecompressedStreamOver(Stream compressed)
            {
                if (compressed == null)
                {
                    throw new ArgumentNullException("compressed");
                }

                return new DecompressionStreamDeflate45(compressed);
            }
        }
        #endregion

        #region Define modified Codec Factory
        //Define modified codec factory to be used in the reader.
        //It catches the attempt to use "Deflate" and provide  a custom codec.
        //For all other cases, it relies on the base class (CodecFactory).
        internal sealed class CodecFactoryDeflate45 : CodecFactory
        {

            public override Codec Create(string codecName)
            {
                if (codecName == DeflateCodec45.CodecName)
                    return new DeflateCodec45();
                else
                    return base.Create(codecName);
            }
        }
        #endregion

        #endregion

        #region Sample Class with demonstration methods
        //This class contains methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes sample data set by using reflection and Avro object container files.
            //Serialized data is compressed with the custom compression codec (Deflate of .NET Framework 4.5).
            //
            //This sample uses memory stream for all operations related to serialization, deserialization and
            //object container manipulation, though file stream could be easily used.
            public void SerializeDeserializeUsingObjectContainersReflectionCustomCodec()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC\n");

                //Path for Avro object container file
                string path = "AvroSampleReflectionDeflate45.avro";

                //Create a data set by using sample class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Here the custom codec is introduced. For convenience, the next commented code line shows how to use built-in Deflate.
                    //Note that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations.
                    //using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, new DeflateCodec45()))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Because of SequentialReader<T> constructor signature, an AvroSerializerSettings instance is required
                    //when codec factory is explicitly specified.
                    //You may comment the line below if you want to use built-in Deflate (see next comment).
                    AvroSerializerSettings settings = new AvroSerializerSettings();

                    //Create a SequentialReader instance for type SensorData, which deserializes all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    //Here the custom codec factory is introduced.
                    //For convenience, the next commented code line shows how to use built-in Deflate
                    //(no explicit Codec Factory parameter is required in this case).
                    //Note that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations.
                    //using (var reader = new SequentialReader<SensorData>(AvroContainer.CreateReader<SensorData>(buffer, true)))
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true, settings, new CodecFactoryDeflate45())))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        bool isEqual;
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }
        #endregion

            #region Helper Methods

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }
            #endregion

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using reflection to Avro object container file using custom codec
                Sample.SerializeDeserializeUsingObjectContainersReflectionCustomCodec();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    //For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

## <a name="sample-6-using-avro-to-upload-data-for-the-microsoft-azure-hdinsight-service"></a>Przykład 6: Przy użyciu systemu Avro do przekazania danych dla usługi Microsoft Azure HDInsight
W szóstej przykładzie pokazano kilka technik programowania, związane z interakcji z usługą Azure HDInsight. Przykład zawierającego kod, aby w tym przykładzie można pobrać z [przykłady kodu platformy Azure](https://code.msdn.microsoft.com/Using-Avro-to-upload-data-ae81b1e3) lokacji.

Przykład wykonuje następujące zadania:

* Łączy do istniejącego klastra usługi HDInsight.
* Serializuje kilka plików CSV i przesyła wyniki do usługi Azure Blob storage. (Pliki CSV są dystrybuowane wraz z przykładu i reprezentują wyciąg z danych historycznych AMEX Stock dystrybuowane przez [Infochimps](http://www.infochimps.com/) okres 1970 roku 2010. Przykład odczytuje danych z plików CSV, konwertuje rekordy do wystąpień **Stock** klasy i szereguje je przy użyciu odbicia. Definicja typu podstawowego jest tworzony na podstawie schematu JSON, za pomocą narzędzia generowania kodu programu Microsoft Avro Library.
* Tworzy nową tabelę zewnętrzne o nazwie **zasobów** w gałęzi i łączy ją z danymi przekazany w poprzednim kroku.
* Wykonuje zapytanie przy użyciu programu Hive za pośrednictwem **zasobów** tabeli.

Ponadto próbki przeprowadza procedurę czyszczenia, przed i po wykonaniu operacji głównej. Podczas oczyszczania wszystkie powiązane dane obiektów Blob platformy Azure i foldery są usuwane, a tabela programu Hive jest usuwana. Można także wywoływać procedury oczyszczania z przykładowy wiersz polecenia.

Przykład obejmuje następujące wymagania wstępne:

* Aktywna subskrypcja Microsoft Azure i jego identyfikatora subskrypcji.
* Certyfikat zarządzania dla subskrypcji przy użyciu odpowiedniego klucza prywatnego. Certyfikat należy zainstalować w bieżącym magazynu prywatnego użytkownika na maszynie użytej do uruchomienia przykładu.
* Aktywnego klastra HDInsight.
* Konto usługi Azure Storage jest połączony z klastra HDInsight z poprzednich wymagań wstępnych oraz odpowiadający mu klucz podstawowy lub pomocniczy dostęp.

Wszystkie informacje z wymagań wstępnych powinny być wprowadzane do przykładowy plik konfiguracji, przed uruchomieniem przykładu. Istnieją dwa sposoby, aby to zrobić:

* Edytowanie pliku app.config w katalogu głównym próbki, a następnie skompiluj próbki
* Najpierw skompilować przykład, a następnie edytuj AvroHDISample.exe.config w katalogu kompilacji

W obu przypadkach należy wykonywać wszystkie zmiany w **<appSettings>** w sekcji Ustawienia. Postępuj zgodnie z komentarze w pliku.
Przykład jest uruchamiany z wiersza polecenia, wykonując następujące polecenie (gdzie plik zip z przykładem założono wyodrębnić C:\AvroHDISample; Jeśli w przeciwnym razie należy użyć ścieżki odpowiedniego pliku):

    AvroHDISample run C:\AvroHDISample\Data

Aby wyczyścić klastra, uruchom następujące polecenie:

    AvroHDISample clean

[deflate-100]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.100).aspx
[deflate-110]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.110).aspx
