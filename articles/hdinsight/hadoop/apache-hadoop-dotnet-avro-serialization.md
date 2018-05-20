---
title: Serializować danych na platformie Azure Hadoop - Microsoft Avro Library – | Dokumentacja firmy Microsoft
description: Dowiedz się, jak serializowania i deserializowania danych w Hadoop w usłudze HDInsight przy użyciu Microsoft Avro Library do utrwalenia pamięci, bazą danych lub pliku.
keywords: avro, hadoop avro
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: c78dc20d-5d8d-4366-94ac-abbe89aaac58
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jgao
ms.custom: hdiseo17may2017
ms.openlocfilehash: 0d195ab3b84a522eae4010f3b08a829f7056a35f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="serialize-data-in-hadoop-with-the-microsoft-avro-library"></a>Serializować danych platformy Hadoop za pomocą programu Microsoft Avro Library

>[!NOTE]
>Zestaw SDK Avro nie jest już obsługiwana przez firmę Microsoft. Biblioteka jest obsługiwane społeczności typu open source. Źródeł dla biblioteki znajdują się w [Github](https://github.com/Azure/azure-sdk-for-net/tree/master/src/ServiceManagement/HDInsight/Microsoft.Hadoop.Avro).

W tym temacie przedstawiono sposób użycia [Microsoft Avro Library](https://github.com/Azure/azure-sdk-for-net/tree/master/src/ServiceManagement/HDInsight/Microsoft.Hadoop.Avro) do serializacji obiektów i inne struktury danych do strumieni zachowywał je w pamięci, bazą danych lub plik. Widoczny jest również sposób deserializacji je odzyskać oryginalnych obiektów.

[!INCLUDE [windows-only](../../../includes/hdinsight-windows-only.md)]

## <a name="apache-avro"></a>Apache Avro
<a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Microsoft Avro Library</a> implementuje systemu Apache Avro danych serializacji dla środowiska Microsoft.NET. Format wymiany danych binarnych Apache Avro zapewnia serializacji. Używa <a href="http://www.json.org" target="_blank">JSON</a> do definiowania języka-schematu z niesprecyzowanym współdziałanie języków. Dane serializowane w jednym języku mogą być odczytywane w innym. C, C++, C#, Java, PHP, Python i Ruby są obecnie obsługiwane. Szczegółowe informacje na temat formatu można znaleźć w <a href="http://avro.apache.org/docs/current/spec.html" target="_blank">Apache Avro Specification</a>. 

>[!NOTE]
>Microsoft Avro Library nie obsługuje część wywołania procedury zdalnej tej specyfikacji.
>

Zserializowana reprezentacja obiektu w systemie Avro składa się z dwóch części: schemat i wartością rzeczywistą. W schemacie Avro opisuje modelu danych niezależny od języka serializowanych danych przy użyciu formatu JSON. Są one przedstawiane równolegle to binarna reprezentacja danych. Posiadanie schematów oddzielona od reprezentacja binarna zezwala na każdy obiekt ma zostać zapisany z nie kosztów ogólnych na wartość, co serializacji szybkiego i reprezentacja mała.

## <a name="the-hadoop-scenario"></a>Scenariusz Hadoop
Format serializacji Apache Avro jest powszechnie używany w innych środowiskach Apache Hadoop i usłudze Azure HDInsight. Avro oferują wygodny sposób do reprezentowania złożone struktury danych w ramach zadania MapReduce z Hadoop. Format plików Avro (pliku kontenera obiektu Avro) został zaprojektowany do obsługi rozproszony model programowania MapReduce. Funkcja klucza, który umożliwia dystrybucję jest, że pliki są "podzielne", w tym sensie, że jeden wyszukiwać dowolne miejsce w pliku i rozpocząć odczytywanie od określonego bloku.

## <a name="serialization-in-avro-library"></a>Serializacja w bibliotece Avro
Biblioteka .NET dla Avro obsługuje serializowania obiektów na dwa sposoby:

* **odbicie** -schematu JSON dla typów automatycznie składa się z danych kontraktu atrybuty typów .NET do serializacji.
* **ogólny rekordu** -schematu A JSON jawnie określonej w rekordzie reprezentowany przez [ **AvroRecord** ](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) klasy, gdy istnieją żadnych typów .NET, do schematu dla danych można było serializować opisania.

Jeśli schemat danych jest znane do zapisywania i odczytywania strumienia, dane mogą być wysyłane bez jego schematu. W przypadku stosowania pliku kontenera obiektu Avro schematu są przechowywane w pliku. Można określić innych parametrów, takich jak koder-dekoder, używany w przypadku kompresji danych. Te scenariusze są opisane bardziej szczegółowo i przedstawiono w poniższych przykładach kodu:

## <a name="install-avro-library"></a>Zainstaluj biblioteki Avro
Poniżej przedstawiono wymagane przed instalacją biblioteki:

* <a href="http://www.microsoft.com/download/details.aspx?id=17851" target="_blank">Microsoft .NET Framework 4</a>
* <a href="http://james.newtonking.com/json" target="_blank">Newtonsoft Json.NET</a> (6.0.4 lub nowszy)

Należy pamiętać, że zależności Newtonsoft.Json.dll jest automatycznie pobierane z instalacji programu Microsoft Avro Library. Procedura znajduje się w następującej sekcji:

Microsoft Avro Library jest rozpowszechniany jako pakietu NuGet, który może zostać zainstalowany z programu Visual Studio za pomocą następującej procedury:

1. Wybierz **projektu** -> kartę **Zarządzaj pakietami NuGet...**
2. Wyszukaj "Microsoft.Hadoop.Avro" w **wyszukiwania Online** pole.
3. Kliknij przycisk **zainstalować** znajdujący się obok **Biblioteka programu Microsoft Azure HDInsight Avro**.

Należy pamiętać, że Newtonsoft.Json.dll (> = 6.0.4) zależności również jest automatycznie pobierana z Microsoft Avro Library.

Microsoft Avro Library kod źródłowy jest dostępny na [Github](https://github.com/Azure/azure-sdk-for-net/tree/master/src/ServiceManagement/HDInsight/Microsoft.Hadoop.Avro).

## <a name="compile-schemas-using-avro-library"></a>Kompilacja za pomocą biblioteki Avro schematów
Microsoft Avro Library zawiera narzędzie generowania kodu, które umożliwia tworzenie typów C# automatycznie w oparciu o wcześniej zdefiniowanego schematu JSON. Narzędzie generowania kodu nie jest rozpowszechniany jako binarny plik wykonywalny, ale mogą być łatwo tworzone za pomocą następującej procedury:

1. Pobierz plik zip do najnowszej wersji zestawu SDK HDInsight kodu źródłowego z <a href="http://hadoopsdk.codeplex.com/SourceControl/latest#" target="_blank">Microsoft .NET SDK dla platformy Hadoop</a>. (Kliknij **Pobierz** ikony, nie **pobiera** kartę.)
2. Wyodrębnij do katalogu na komputerze z programu .NET Framework 4 zainstalowane i połączone z Internetem pobierania pakietów NuGet niezbędnych zależności zestawu SDK HDInsight. Poniżej przyjęto założenie, że kod źródłowy został wyodrębniony do C:\SDK.
3. Przejdź do folderu C:\SDK\src\Microsoft.Hadoop.Avro.Tools i uruchom build.bat. (Plik wywołuje program MSBuild z dystrybucji 32-bitowej platformy .NET. Jeśli chcesz używać wersji 64-bitowych, Edycja build.bat po komentarze w pliku). Upewnij się, że kompilacja zakończy się pomyślnie. (W niektórych systemach MSBuild może powodować ostrzeżenia. Ostrzeżenia te nie wpływają na narzędzie tak długo, jak nie ma żadnych błędów kompilacji.)
4. Skompilowany narzędzie znajduje się w C:\SDK\Bin\Unsigned\Release\Microsoft.Hadoop.Avro.Tools.

Aby zapoznać się ze składnią wiersza polecenia, uruchom następujące polecenie z folderu, w którym znajduje się narzędzie generowania kodu: `Microsoft.Hadoop.Avro.Tools help /c:codegen`

Aby przetestować narzędzie, można wygenerować klas C# z przykładowy plik schematu JSON podane z kodem źródłowym. Uruchom następujące polecenie:

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:

To powinien utworzyć pliki dwóch C# w bieżącym katalogu: SensorData.cs i Location.cs.

Aby poznać logiki, która używa narzędzie generowania kodu podczas konwertowania schematu JSON dla typów C#, zobacz plik, który GenerationVerification.feature znajduje się w C:\SDK\src\Microsoft.Hadoop.Avro.Tools\Doc.

Przestrzenie nazw są wyodrębniane ze schematu JSON przy użyciu logiki opisany w pliku wspomnianego powyżej. Przestrzenie nazw wyodrębnione ze schematu pierwszeństwo niezależnie od jest dostarczana z parametru /n w wierszu polecenia narzędzia. Jeśli chcesz przesłonić przestrzenie nazw zawartych w schemacie, użyj parametru /nf. Na przykład aby zmienić wszystkie przestrzenie nazw z SampleJSONSchema.avsc my.own.nspace, uruchom następujące polecenie:

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:. /nf:my.own.nspace

## <a name="about-the-samples"></a>Przykłady — informacje
Sześć przykłady podane w tym temacie przedstawiono różne scenariusze obsługiwane przez Microsoft Avro Library. Microsoft Avro Library jest przeznaczona do pracy z jakimkolwiek strumieniu. W tych przykładach danych steruje się za pośrednictwem strumieni pamięci zamiast strumieni plików lub baz danych, aby był prosty i spójności. Podejście przyjęte w środowisku produkcyjnym zależy od wymagań scenariusza dokładne, źródła danych i wolumin ograniczeń wydajności i innych czynników.

Pierwsze dwa przykłady przedstawiają sposób serializacji i deserializacji danych do buforów strumienia pamięci przy użyciu odbicia i rodzajowy rekordów. Schemat w tych dwóch przypadkach przyjęto, że mogą być współużytkowane przez czytelników i autorzy poza pasmem.

Trzeci i czwarty przykłady pokazują, jak do serializowania i deserializowania danych przy użyciu plików Avro obiektu kontenera. Gdy dane są przechowywane w pliku kontenera Avro, schematem jest zawsze przechowywane z nim ponieważ schematu musi być udostępniony do deserializacji.

Przykład zawierający pierwsze cztery przykłady można pobrać z <a href="http://code.msdn.microsoft.com/Serialize-data-with-the-86055923" target="_blank">przykłady kodu Azure</a> lokacji.

Piąty przykładzie pokazano sposób użycia koder-dekoder kompresji niestandardowych dla plików kontenera obiektów Avro. Przykładowe zawierającego kod, w tym przykładzie mogą być pobierane ze <a href="http://code.msdn.microsoft.com/Serialize-data-with-the-67159111" target="_blank">przykłady kodu Azure</a> lokacji.

Szósty pokazano, jak użyć serializacji Avro do przekazywania danych do magazynu obiektów Blob platformy Azure, a następnie analizować przy użyciu usługi Hive z klastrem usługi HDInsight (Hadoop). Można go pobrać z <a href="https://code.msdn.microsoft.com/Using-Avro-to-upload-data-ae81b1e3" target="_blank">przykłady kodu Azure</a> lokacji.

Oto łącza do sześć próbek omówione w tym temacie:

* <a href="#Scenario1">**Serializacja za pomocą odbicia** </a> -schematu JSON dla typów można było serializować automatycznie składa się z danych atrybuty kontraktu.
* <a href="#Scenario2">**Serializacja z rekordem ogólnego** </a> — w rekordzie jawnie określono schematu JSON, gdy typ architektury .NET, nie jest dostępna w celu odbicia.
* <a href="#Scenario3">**Serializacji przy użyciu plików kontenera obiektów za pomocą odbicia** </a> -schematu JSON jest automatycznie utworzony i udostępnione wraz z serializowanych danych za pomocą pliku kontenera obiektu Avro.
* <a href="#Scenario4">**Serializacji przy użyciu plików kontenera obiektów z rekordem ogólnego** </a> -jawnie określone przed serializacji i udostępnione wraz z danymi za pomocą pliku kontenera obiektu Avro schematu JSON.
* <a href="#Scenario5">**Serializacji przy użyciu plików kontenera obiektów z koder-dekoder kompresji niestandardowych** </a> -przykładzie przedstawiono sposób tworzenia pliku kontenera obiektu Avro dostosowane implementacji .NET koder-dekoder kompresji danych Deflate.
* <a href="#Scenario6">**Przekazywanie danych do usługi Microsoft Azure HDInsight przy użyciu Avro** </a> -serializacji Avro współdziałania z usługą HDInsight pokazano w przykładzie. Aktywną subskrypcją platformy Azure i dostęp do klastra usługi Azure HDInsight są wymagane do uruchomienia tego przykładu.

## <a name="Scenario1"></a>Przykład 1: Serializacja za pomocą odbicia
Schematu JSON dla typów można kontraktu atrybutów obiektów C#, aby można było serializować automatycznie utworzony przez Microsoft Library Avro za pośrednictwem odbicia z danych. Tworzy Microsoft Avro Library [ **IAvroSeralizer<T>**  ](http://msdn.microsoft.com/library/dn627341.aspx) do identyfikowania pola, które mają być serializowane.

W tym przykładzie obiekty ( **SensorData** klasy z elementem członkowskim **lokalizacji** struktury) są serializowane do strumienia pamięci, i z kolei jest deserializacji tego strumienia. Wynik jest następnie porównywany początkowej wystąpienia upewnij się, że **SensorData** obiektu odzyskane jest takie same jak oryginalne.

Schemat w tym przykładzie przyjęto, że mogą być współużytkowane przez czytelników i zapisywania, dlatego format kontenera obiektu Avro nie jest wymagane. Na przykład sposobu serializowania i deserializowania danych do buforów pamięci przy użyciu odbicia w formacie kontenera obiektu, gdy schemat można udostępniać dane zobacz <a href="#Scenario3">serializacji przy użyciu plików kontenera obiektów za pomocą odbicia</a>.

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
Schematu JSON można jawnie określić w rekordzie ogólnym po odbicia nie można użyć, ponieważ dane nie mogą być reprezentowane przez klasy .NET z kontraktem danych. Ta metoda działa wolniej niż przy użyciu odbicia. W takich przypadkach schematu dla danych mogą być również dynamiczny, oznacza to, że nie jest znany w czasie kompilacji. Dane reprezentowane jako pliki wartości rozdzielanych przecinkami (CSV), którego schemat jest nieznany, dopóki nie jest przekształcana na format Avro w czasie wykonywania jest to przykład tego rodzaju scenariusza dynamicznej.

W tym przykładzie przedstawiono sposób tworzenia i używania [ **AvroRecord** ](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) Aby jawnie określić schematu JSON, jak i wypełnić je danymi i jak serializacji i deserializacji. Wynik jest następnie porównywany do początkowej wystąpienia, aby potwierdzić, że rekord odzyskane jest taki sam jak oryginalny.

Schemat w tym przykładzie przyjęto, że mogą być współużytkowane przez czytelników i zapisywania, dlatego format kontenera obiektu Avro nie jest wymagane. Na przykład sposobu serializowania i deserializowania danych do buforów pamięci przy użyciu rekordu ogólny format obiektu kontenera podczas schematu musi być uwzględniona w danych serializacji. zobacz <a href="#Scenario4">serializacji przy użyciu plików kontenera obiektów z rekordem ogólnego</a> przykład.

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


## <a name="sample-3-serialization-using-object-container-files-and-serialization-with-reflection"></a>Przykład 3: Serializacji przy użyciu plików kontenera obiektów i serializacji za pomocą odbicia
W tym przykładzie jest podobny do scenariusza w <a href="#Scenario1"> pierwszym przykładzie</a>, gdzie schemat jest niejawnie określony za pomocą odbicia. Różnica jest w tym miejscu, schemat nie przyjęto, że powinni znać odczytującego deserializuje go. **SensorData** obiektów, aby można było serializować i ich niejawnie określony schemat są przechowywane w pliku Avro kontenera obiektu reprezentowanego przez [ **AvroContainer** ](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx) klasy.

Dane jest serializowany w tym przykładzie z [ **SequentialWriter<SensorData>**  ](http://msdn.microsoft.com/library/dn627340.aspx) i zdeserializowany z [ **SequentialReader<SensorData>**](http://msdn.microsoft.com/library/dn627340.aspx). Wynik jest porównany początkowej wystąpienia, aby zapewnić tożsamości.

Dane w pliku kontenera obiektu są kompresowane przez domyślny [ **Deflate** ] [ deflate-100] koder-dekoder kompresji z programu .NET Framework 4. Zobacz <a href="#Scenario5"> przykład piąty</a> w tym temacie, aby dowiedzieć się, jak użyć wersji nowszej i wyższego poziomu [ **Deflate** ] [ deflate-110] koder-dekoder kompresji jest dostępne w programie .NET Framework 4.5.

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


## <a name="sample-4-serialization-using-object-container-files-and-serialization-with-generic-record"></a>Przykład 4: Serializacji przy użyciu plików kontenera obiektów i serializacji z rekordem ogólny
W tym przykładzie jest podobny do scenariusza w <a href="#Scenario2"> drugi przykład</a>, gdzie schemat jest jawnie określony z formatu JSON. Różnica jest w tym miejscu, schemat nie przyjęto, że powinni znać odczytującego deserializuje go.

Testowego zestawu danych zbieranych na listę wartości [ **AvroRecord** ](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) obiektów za pomocą jawnie zdefiniowanych schematu JSON, a następnie zapisywana w pliku kontenera obiektu reprezentowanego przez [ **AvroContainer** ](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx) klasy. Ten plik kontenera tworzy moduł zapisujący służący do serializowania danych jako nieskompresowane strumień pamięci, który następnie jest zapisywana w pliku. [ **Codec.Null** ](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.codec.null.aspx) parametru użytego do utworzenia czytnika danych określa, że te dane nie jest skompresowany.

Dane są następnie odczytu z pliku i deserializacji do kolekcji obiektów. Ta kolekcja jest porównywany z początkowej listy rekordów Avro, aby upewnić się, że są one identyczne.

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




## <a name="sample-5-serialization-using-object-container-files-with-a-custom-compression-codec"></a>Przykład 5: Serializacji przy użyciu plików kontenera obiektów z koder-dekoder kompresji niestandardowych
Piąty przykładzie pokazano sposób użycia koder-dekoder kompresji niestandardowych dla plików kontenera obiektów Avro. Przykładowe zawierającego kod, w tym przykładzie mogą być pobierane ze [przykłady kodu Azure](http://code.msdn.microsoft.com/Serialize-data-with-the-67159111) lokacji.

[Avro Specification](http://avro.apache.org/docs/current/spec.html#Required+Codecs) umożliwia użycie koder-dekoder kompresji opcjonalne (oprócz **Null** i **Deflate** ustawień domyślnych). W tym przykładzie nie implementuje nowy koder-dekoder takich jak Snappy (wymieniony jako obsługiwany opcjonalne kodera-dekodera w [Avro Specification](http://avro.apache.org/docs/current/spec.html#snappy)). Widoczny jest sposób korzysta z wdrożenia programu .NET Framework 4.5 [ **Deflate** ] [ deflate-110] koder-dekoder, który zapewnia lepszą algorytmu kompresji na podstawie [zlib](http://zlib.net/) biblioteki kompresji niż domyślna wersja .NET Framework 4.

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

## <a name="sample-6-using-avro-to-upload-data-for-the-microsoft-azure-hdinsight-service"></a>Przykład 6: Przy użyciu Avro przekazywać dane do usługi Microsoft Azure HDInsight
Niektóre technik programowania, związanych z interakcji z usługą Azure HDInsight pokazano w przykładzie szóstego. Przykładowe zawierającego kod, w tym przykładzie mogą być pobierane ze [przykłady kodu Azure](https://code.msdn.microsoft.com/Using-Avro-to-upload-data-ae81b1e3) lokacji.

Przykład wykonuje następujące zadania:

* Nawiązuje połączenie z istniejącym klastrem usługi HDInsight.
* Serializuje kilka plików CSV i przekazuje wynik do magazynu obiektów Blob platformy Azure. (Pliki CSV są dystrybuowane wraz z próbki i reprezentują wyodrębniania z załączniku historyczne dane dystrybuowana [Infochimps](http://www.infochimps.com/) okres 1970 2010. Przykład odczytuje dane z pliku CSV, konwertuje rekordy do wystąpień **giełdowych** klasy, a następnie serializuje je przy użyciu odbicia. Definicja typu podstawowego jest tworzona na podstawie schematu JSON za pomocą narzędzia generowania kodu programu Microsoft Avro Library.
* Tworzy nową tabelę zewnętrznych o nazwie **zasobów** w gałęzi i przekazać go do danych w poprzednim kroku łącza.
* Wykonuje zapytanie przy użyciu Hive za pośrednictwem **zasobów** tabeli.

Ponadto próbki przeprowadza procedurę czyszczenia przed i po wykonaniu operacji głównych. Podczas oczyszczania zostaną usunięte wszystkie dane obiektów Blob platformy Azure i foldery, a tabela Hive jest usuwana. Można także wywoływać procedury czyszczenia z przykładowy wiersz polecenia.

Próbka ma następujące wymagania wstępne:

* Aktywną subskrypcją Microsoft Azure i jego identyfikatora subskrypcji.
* Certyfikat zarządzania do subskrypcji przy użyciu odpowiedniego klucza prywatnego. Należy zainstalować certyfikat w magazynie bieżącego użytkownika prywatnych na komputerze użyta do uruchomienia przykładu.
* Aktywnego klastra usługi HDInsight.
* Konta usługi Azure Storage jest połączony z klastrem usługi HDInsight z poprzednich wymagań wstępnych, oraz odpowiedni klucz podstawowy lub pomocniczy dostęp.

Wszystkie informacje z wymagań wstępnych powinny być wprowadzane do przykładowy plik konfiguracji, przed uruchomieniem próbki. Istnieją dwa sposoby można to zrobić:

* Edytowanie pliku app.config w katalogu głównym próbki i późniejszego kompilowania próbki
* Tworzyć przykładowy kod najpierw, a następnie edytuj AvroHDISample.exe.config w katalog kompilacji

W obu przypadkach należy wykonywać wszystkie zmiany w **<appSettings>** w sekcji Ustawienia. Postępuj zgodnie z komentarzami w pliku.
Próbki jest uruchamiany z poziomu wiersza polecenia, wykonując następujące polecenie (gdzie próbką plik zip został zakłada się, że wyodrębnione do C:\AvroHDISample; Jeśli w przeciwnym razie wartość, użyj ścieżki odpowiedniego pliku):

    AvroHDISample run C:\AvroHDISample\Data

Aby wyczyścić klastra, uruchom następujące polecenie:

    AvroHDISample clean

[deflate-100]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.100).aspx
[deflate-110]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.110).aspx
