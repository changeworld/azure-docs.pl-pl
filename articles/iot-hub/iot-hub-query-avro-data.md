---
title: Wykonywanie zapytań o dane Avro przy użyciu usługi Azure Data Lake Analytics | Dokumentacja firmy Microsoft
description: Użyj właściwości treści wiadomości do kierowania danych telemetrycznych z urządzenia do usługi Blob storage i wykonywanie zapytań o dane formatu Avro, które są zapisywane do magazynu obiektów Blob.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: asrastog
ms.openlocfilehash: a17df39c55b5c02c83e3f0b74a91d7109ddb4d3d
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2018
ms.locfileid: "43188948"
---
# <a name="query-avro-data-by-using-azure-data-lake-analytics"></a>Avro zapytania o dane za pomocą usługi Azure Data Lake Analytics

W tym artykule omówiono sposób wykonywania zapytań o dane Avro do efektywne kierowanie komunikatów z usługi Azure IoT Hub do usług platformy Azure. Jak firma Microsoft ogłosiła wpis w blogu [Usługa Azure routing komunikatów usługi IoT Hub: teraz przy użyciu routingu na treść komunikatu], usługi IoT Hub obsługuje routing na właściwości lub treści wiadomości. Aby uzyskać więcej informacji, zobacz [routingu w treści wiadomości][Routing on message bodies]. 

Żądania zostało, gdy usługa Azure IoT Hub kieruje komunikaty do usługi Azure Blob storage, usługi IoT Hub zapisuje zawartość w formacie Avro, która ma zarówno właściwość treści wiadomości, jak i właściwość wiadomości. Centrum IoT Hub obsługuje zapisywania danych do magazynu obiektów Blob tylko format Avro danych, a ten format nie jest używane dla innych punktów końcowych. Aby uzyskać więcej informacji, zobacz [podczas korzystania z kontenerów usługi Azure Storage][When using Azure storage containers]. Avro format to idealne narzędzie do przechowywania danych i komunikatów, może się jednak żądania na potrzeby zapytania o dane. W odróżnieniu od formacie JSON lub CSV jest znacznie łatwiejsze do wykonywania zapytań na danych.

Adres formatów i nierelacyjnych danych big data musi i stawić czoła temu wyzwaniu, można użyć wielu wzorców danych big data dla transformacji i skalowania danych. Jednym z wzorców, "płacić za zapytania," jest usługi Azure Data Lake Analytics, czyli fokus w tym artykule. Mimo że można łatwo wykonywać zapytania w usłudze Hadoop ani innych rozwiązań, Data Lake Analytics często lepiej jest odpowiedni dla tego podejścia "płacić za zapytania". 

Brak "ekstraktor" dla systemu Avro w języku U-SQL. Aby uzyskać więcej informacji, zobacz [przykład Avro U-SQL].

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Zapytania i wyeksportować dane Avro do pliku CSV
W tej sekcji, wykonywanie zapytań o dane Avro i wyeksportować je do pliku CSV w usłudze Azure Blob storage, mimo że można łatwo umieścić dane w innych magazynach danych lub repozytoriów.

1. Konfigurowanie usługi Azure IoT Hub do kierowania danych do punktu końcowego magazynu obiektów Blob platformy Azure przy użyciu właściwości w treści wiadomości, aby zaznaczyć wiadomości.

    ![W sekcji "Niestandardowe punkty końcowe."][img-query-avro-data-1a]

    ![Polecenie trasy][img-query-avro-data-1b]

2. Upewnij się, że urządzenie ma kodowania, typu zawartości i potrzebne dane w właściwości lub w treści wiadomości, zgodnie z odwołaniem w dokumentacji produktu. Podczas wyświetlania tych atrybutów w Device Explorer, jak pokazano poniżej, możesz sprawdzić, czy są one prawidłowo ustawione.

    ![W okienku dane do Centrum zdarzeń][img-query-avro-data-2]

3. Konfigurowanie wystąpienia usługi Azure Data Lake Store i wystąpienia usługi Data Lake Analytics. Usługa Azure IoT Hub nie jest kierowany do wystąpienia programu Data Lake Store, ale wymaga wystąpienia usługi Data Lake Analytics.

    ![Data Lake Store i Data Lake Analytics][img-query-avro-data-3]

4. W usługi Data Lake Analytics należy skonfigurować usługi Azure Blob storage jako magazynu dodatkowego, usługi Azure IoT Hub kieruje dane do tego samego magazynu obiektów Blob.

    ![W okienku "Źródła danych"][img-query-avro-data-4]
 
5. Zgodnie z opisem w [przykład Avro U-SQL], potrzebne są cztery pliki DLL. Przekaż te pliki do lokalizacji w wystąpieniu usługi Data Lake Store.

    ![Cztery przekazywanych plików DLL][img-query-avro-data-5] 

6. W programie Visual Studio Utwórz projekt U-SQL.
 
    ![Utwórz projekt U-SQL][img-query-avro-data-6]

7. Wklej zawartość poniższego skryptu do nowo utworzony plik. Zmodyfikuj trzy wyróżnione sekcje: Twoje konto usługi Data Lake Analytics, skojarzone ścieżki plików DLL i poprawna ścieżka konta magazynu.
    
    ![Trzy sekcje do zmodyfikowania][img-query-avro-data-7a]

    Rzeczywiste skrypt U-SQL proste dane wyjściowe do pliku CSV:
    
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

    Zajęło to Data Lake Analytics Uruchom następujący skrypt, która została ograniczona do 10 jednostek analitycznych i przetworzyć pliki 177 pięć minut. Wynik jest wyświetlany w danych wyjściowych z pliku CSV, który jest wyświetlany na poniższej ilustracji:
    
    ![Wyniki dane wyjściowe do pliku CSV][img-query-avro-data-7b]

    ![Dane wyjściowe konwertowane do pliku CSV][img-query-avro-data-7c]

    Aby analizować dane JSON, przejdź do kroku 8.
    
8. Większość IoT komunikaty są w formacie pliku JSON. Dodając następujące wiersze, można przeanalizować wiadomość do pliku JSON, która umożliwia dodawanie klauzulach WHERE i tylko potrzebne dane wyjściowe.

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

    Dane wyjściowe Wyświetla kolumnę dla każdego elementu w `SELECT` polecenia. 
    
    ![Pokazywanie kolumny dla każdego elementu danych wyjściowych][img-query-avro-data-8]

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku przedstawiono sposób wykonywania zapytań o dane Avro do efektywne kierowanie komunikatów z usługi Azure IoT Hub do usług platformy Azure.

Aby uzyskać przykłady kompletne rozwiązania end-to-end, które używają usługi IoT Hub, zobacz [akcelerator rozwiązań Azure IoT zdalne monitorowanie][lnk-iot-sa-land].

Aby dowiedzieć się więcej na temat opracowywania rozwiązań usługi IoT Hub, zobacz [Przewodnik dla deweloperów usługi IoT Hub].

Aby dowiedzieć się więcej na temat routingu komunikatów w usłudze IoT Hub, zobacz [wysyłania i odbierania komunikatów za pomocą usługi IoT Hub][lnk-devguide-messaging].

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
[Usługa Azure routing komunikatów usługi IoT Hub: teraz przy użyciu routingu na treść komunikatu]: https://azure.microsoft.com/blog/iot-hub-message-routing-now-with-routing-on-message-body/

[Routing on message bodies]: iot-hub-devguide-query-language.md#routing-on-message-bodies
[When using Azure storage containers]:iot-hub-devguide-endpoints.md#when-using-azure-storage-containers

[Przykład Avro U-SQL]:https://github.com/Azure/usql/tree/master/Examples/AvroExamples

[lnk-iot-sa-land]: ../iot-accelerators/index.yml
[Przewodnik dla deweloperów usługi IoT Hub]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
