---
title: Zapytanie dotyczące danych Avro za pomocą usługi Azure Data Lake Analytics | Dokumentacja firmy Microsoft
description: Użyj właściwości treści wiadomości do kierowania telemetrii urządzenia do magazynu obiektów Blob i kwerend danych format Avro, które jest zapisywane do magazynu obiektów Blob.
services: iot-hub
documentationcenter: ''
author: ksaye
manager: obloch
ms.service: iot-hub
ms.topic: article
ms.date: 05/29/2018
ms.author: Kevin.Saye
ms.openlocfilehash: c56b567498047ee996018675134c252ec1de7e0c
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2018
ms.locfileid: "37081372"
---
# <a name="query-avro-data-by-using-azure-data-lake-analytics"></a>Dane Avro zapytania przy użyciu usługi Azure Data Lake Analytics

W tym artykule omówiono sposób zapytania na danych Avro aby skutecznie kierować wiadomości z Centrum IoT Azure do usług platformy Azure. Jak możemy ogłaszane w blogu [Centrum IoT Azure wiadomości routingu: teraz z routingiem na treść komunikatu], Centrum IoT obsługuje routing na właściwości lub treści wiadomości. Aby uzyskać więcej informacji, zobacz [routingu w treści wiadomości][Routing on message bodies]. 

Żądania zostało który, gdy Centrum IoT Azure kieruje komunikaty do magazynu obiektów Blob platformy Azure, Centrum IoT zapisuje zawartość w formacie Avro, który ma właściwość treści wiadomości oraz do komunikatu właściwość komunikatu. Centrum IoT obsługuje zapisywania danych do magazynu obiektów Blob tylko format Avro danych, a ten format nie jest używany dla innych punktów końcowych. Aby uzyskać więcej informacji, zobacz [podczas korzystania z usługi Azure Storage kontenery][When using Azure storage containers]. Avro format doskonale nadaje się do przechowywania danych i wiadomości, ale jest żądanie na potrzeby danych zapytania. Z kolei w formacie JSON lub CSV jest znacznie łatwiejsze do wykonywania zapytań danych.

Do potrzeb dużych danych nierelacyjnych i formaty adresów i rozwiązać ten problem, można użyć wielu wzorców danych big data Przekształcanie i skalowanie danych. Jeden wzorców "płać dla kwerendy," to Azure Data Lake Analytics, czyli fokus w tym artykule. Mimo że można łatwo wykonać zapytania w Hadoop ani innych rozwiązań, usługi Data Lake Analytics jest często lepiej nadaje się do tej metody "płać na zapytanie". 

Brak "ekstraktor" dla Avro w języku U-SQL. Aby uzyskać więcej informacji, zobacz [przykład Avro U-SQL].

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Zapytania i wyeksportuj dane Avro do pliku CSV
W tej sekcji zapytania na danych Avro i wyeksportować do pliku CSV w magazynie obiektów Blob platformy Azure, mimo że można łatwo umieścić dane w innych magazynach repozytoria lub danych.

1. Konfigurowanie Centrum IoT Azure na przesyłanie danych do punktu końcowego magazynu obiektów Blob platformy Azure przy użyciu właściwości w treści wiadomości, aby zaznaczyć wiadomości.

    ![W sekcji "Niestandardowe punkty końcowe."][img-query-avro-data-1a]

    ![Polecenie tras][img-query-avro-data-1b]

2. Sprawdź, czy urządzenie ma kodowania, typu zawartości i potrzebnych danych właściwości lub treść komunikatu zgodnie z informacjami zawartymi w dokumentacji produktu. Po wyświetleniu tych atrybutów w Eksploratorze urządzenia, jak pokazano poniżej, można sprawdzić, czy są prawidłowo ustawione.

    ![W okienku danych Centrum zdarzeń][img-query-avro-data-2]

3. Konfigurowanie wystąpienia usługi Azure Data Lake Store i wystąpienia usługi Data Lake Analytics. Centrum IoT Azure nie może kierować do wystąpienia usługi Data Lake Store, ale wymaga wystąpienia usługi Data Lake Analytics.

    ![Wystąpienia usługi Data Lake Store i usługi Data Lake Analytics][img-query-avro-data-3]

4. Usługi Data Lake Analytics skonfigurować jako dodatkowego magazynu tego samego magazynu obiektów Blob, który Centrum IoT Azure przekierowuje dane do magazynu obiektów Blob platformy Azure.

    ![W okienku "Źródła danych"][img-query-avro-data-4]
 
5. Zgodnie z opisem w [przykład Avro U-SQL], potrzebne są cztery pliki DLL. Przekazywanie tych plików do lokalizacji w wystąpieniu usługi Data Lake Store.

    ![Cztery przekazanych plików DLL][img-query-avro-data-5] 

6. W programie Visual Studio Utwórz projekt U-SQL.
 
    ![Tworzenie projektu U-SQL][img-query-avro-data-6]

7. Wklej zawartość poniższego skryptu do nowo utworzonego pliku. Zmodyfikuj trzy sekcje wyróżnione: konto usługi Data Lake Analytics, skojarzone ścieżki plików DLL i poprawną ścieżkę dla konta magazynu.
    
    ![Trzy sekcje do zmodyfikowania][img-query-avro-data-7a]

    Rzeczywiste skryptu U-SQL proste dane wyjściowe do pliku CSV:
    
    ```sql
        DROP ASSEMBLY IF EXISTS [Avro];
        CREATE ASSEMBLY [Avro] FROM @"/Assemblies/Avro/Avro.dll";
        DROP ASSEMBLY IF EXISTS [Microsoft.Analytics.Samples.Formats];
        CREATE ASSEMBLY [Microsoft.Analytics.Samples.Formats] FROM @"/Assemblies/Avro/Microsoft.Analytics.Samples.Formats.dll";
        DROP ASSEMBLY IF EXISTS [Newtonsoft.Json];
        CREATE ASSEMBLY [Newtonsoft.Json] FROM @"/Assemblies/Avro/Newtonsoft.Json.dll";
        DROP ASSEMBLY IF EXISTS [log4net];
        CREATE ASSEMBLY [log4net] FROM @"/Assemblies/Avro/log4net.dll";

        REFERENCE ASSEMBLY [Newtonsoft.Json];
        REFERENCE ASSEMBLY [log4net];
        REFERENCE ASSEMBLY [Avro];
        REFERENCE ASSEMBLY [Microsoft.Analytics.Samples.Formats];

        // Blob container storage account filenames, with any path
        DECLARE @input_file string = @"wasb://hottubrawdata@kevinsayazstorage/kevinsayIoT/{*}/{*}/{*}/{*}/{*}/{*}";
        DECLARE @output_file string = @"/output/output.csv";

        @rs =
        EXTRACT
        EnqueuedTimeUtc string,
        Body byte[]
        FROM @input_file

        USING new Microsoft.Analytics.Samples.Formats.ApacheAvro.AvroExtractor(@"
        {
        ""type"":""record"",
        ""name"":""Message"",
        ""namespace"":""Microsoft.Azure.Devices"",
        ""fields"":[{
        ""name"":""EnqueuedTimeUtc"",
        ""type"":""string""
        },
        {
        ""name"":""Properties"",
        ""type"":{
        ""type"":""map"",
        ""values"":""string""
        }
        },
        {
        ""name"":""SystemProperties"",
        ""type"":{
        ""type"":""map"",
        ""values"":""string""
        }
        },
        {
        ""name"":""Body"",
        ""type"":[""null"",""bytes""]
        }
        ]
        }");

        @cnt =
        SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
        FROM @rs;

        OUTPUT @cnt TO @output_file USING Outputters.Text(); 
    ```    

    Zajęło usługi Data Lake Analytics Uruchom następujący skrypt, która została ograniczona do 10 jednostek analityczne i przetwarzane pliki 177 pięć minut. Wynik jest wyświetlany w danych wyjściowych pliku CSV, który jest wyświetlany na poniższej ilustracji:
    
    ![Wyniki dane wyjściowe do pliku CSV][img-query-avro-data-7b]

    ![Dane wyjściowe przekonwertowane do pliku CSV][img-query-avro-data-7c]

    Aby analizować dane JSON, przejdź do kroku 8.
    
8. Większość wiadomości IoT są w formacie JSON. Dodając następujące wiersze, można przeanalizować komunikatu do pliku JSON, który umożliwia dodawanie klauzulach WHERE i tylko potrzebne dane wyjściowe.

    ```sql
       @jsonify = SELECT Microsoft.Analytics.Samples.Formats.Json.JsonFunctions.JsonTuple(Encoding.UTF8.GetString(Body)) AS message FROM @rs;
    
        /*
        @cnt =
            SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
            FROM @rs;
        
        OUTPUT @cnt TO @output_file USING Outputters.Text();
        */
        
        @cnt =
            SELECT message["message"] AS iotmessage,
                   message["event"] AS msgevent,
                   message["object"] AS msgobject,
                   message["status"] AS msgstatus,
                   message["host"] AS msghost
            FROM @jsonify;
            
        OUTPUT @cnt TO @output_file USING Outputters.Text();
    ```

    Wyświetla dane wyjściowe kolumnę dla każdego elementu w `SELECT` polecenia. 
    
    ![Pokazywanie kolumny dla każdego elementu danych wyjściowych][img-query-avro-data-8]

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku przedstawiono sposób zapytania na danych Avro aby skutecznie kierować wiadomości z Centrum IoT Azure do usług platformy Azure.

Przykłady kompletnych rozwiązań end-to-end korzystających z Centrum IoT można znaleźć [zdalnego monitorowania Azure IoT akcelerator rozwiązań][lnk-iot-sa-land].

Aby dowiedzieć się więcej na temat tworzenia rozwiązań z Centrum IoT, zobacz [Przewodnik dewelopera Centrum IoT].

Aby dowiedzieć się więcej na temat w Centrum IoT rozsyłania wiadomości, zobacz [wysyłania i odbierania wiadomości z Centrum IoT][lnk-devguide-messaging].

<!-- Images -->
[img-query-avro-data-1a]: ./media/iot-hub-query-avro-data/query-avro-data-1a.png
[img-query-avro-data-1b]: ./media/iot-hub-query-avro-data/query-avro-data-1b.png
[img-query-avro-data-2]: ./media/iot-hub-query-avro-data/query-avro-data-2.png
[img-query-avro-data-3]: ./media/iot-hub-query-avro-data/query-avro-data-3.png
[img-query-avro-data-4]: ./media/iot-hub-query-avro-data/query-avro-data-4.png
[img-query-avro-data-5]: ./media/iot-hub-query-avro-data/query-avro-data-5.png
[img-query-avro-data-6]: ./media/iot-hub-query-avro-data/query-avro-data-6.png
[img-query-avro-data-7a]: ./media/iot-hub-query-avro-data/query-avro-data-7a.png
[img-query-avro-data-7b]: ./media/iot-hub-query-avro-data/query-avro-data-7b.png
[img-query-avro-data-7c]: ./media/iot-hub-query-avro-data/query-avro-data-7c.png
[img-query-avro-data-8]: ./media/iot-hub-query-avro-data/query-avro-data-8.png

<!-- Links -->
[Centrum IoT Azure wiadomości routingu: teraz z routingiem na treść komunikatu]: https://azure.microsoft.com/blog/iot-hub-message-routing-now-with-routing-on-message-body/

[Routing on message bodies]: iot-hub-devguide-query-language.md#routing-on-message-bodies
[When using Azure storage containers]:iot-hub-devguide-endpoints.md#when-using-azure-storage-containers

[Przykład Avro U-SQL]:https://github.com/Azure/usql/tree/master/Examples/AvroExamples

[lnk-iot-sa-land]: ../iot-accelerators/index.yml
[Przewodnik dewelopera Centrum IoT]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
