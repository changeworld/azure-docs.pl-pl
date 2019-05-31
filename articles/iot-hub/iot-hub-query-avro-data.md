---
title: Wykonywanie zapytań o dane Avro przy użyciu usługi Azure Data Lake Analytics | Dokumentacja firmy Microsoft
description: Użyj właściwości treści wiadomości do kierowania danych telemetrycznych z urządzenia do usługi Blob storage i wykonywanie zapytań o dane formatu Avro, które są zapisywane do magazynu obiektów Blob.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: 84e1dd77c6e873dc2facb5126bbddf795192b60d
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257739"
---
# <a name="query-avro-data-by-using-azure-data-lake-analytics"></a>Avro zapytania o dane za pomocą usługi Azure Data Lake Analytics

W tym artykule omówiono sposób wykonywania zapytań o dane Avro do efektywne kierowanie komunikatów z usługi Azure IoT Hub do usług platformy Azure. [Routing komunikatów](iot-hub-devguide-messages-d2c.md) pozwala na filtrowanie danych za pomocą zaawansowanych zapytań na podstawie właściwości wiadomości, treści wiadomości, tagów bliźniaczych reprezentacji urządzeń i właściwości bliźniaczych reprezentacji urządzeń. Aby dowiedzieć się więcej na temat możliwości zapytań routingu wiadomości, zobacz artykuł na temat [komunikat o składni zapytań routingu](iot-hub-devguide-routing-query-syntax.md).

Żądania zostało, gdy usługi Azure IoT Hub kieruje komunikaty do usługi Azure Blob storage, domyślnie Centrum IoT Hub zapisuje zawartość w formacie Avro, która ma zarówno właściwość treści wiadomości, jak i właściwość wiadomości. Avro format nie jest używana dla innych punktów końcowych. Avro format to idealne narzędzie do przechowywania danych i komunikatów, może się jednak żądania na potrzeby zapytania o dane. W odróżnieniu od formacie JSON lub CSV jest znacznie łatwiejsze do wykonywania zapytań na danych. Usługa IoT Hub obsługuje teraz zapisywanie danych w magazynie obiektów Blob w formacie JSON, a także AVRO.

Aby uzyskać więcej informacji, zobacz [przy użyciu usługi Azure Blob Storage jako punktu końcowego routingu](iot-hub-devguide-messages-d2c.md#azure-blob-storage).

Adres formatów i nierelacyjnych danych big data musi i stawić czoła temu wyzwaniu, można użyć wielu wzorców danych big data dla transformacji i skalowania danych. Jednym z wzorców, "płacić za zapytania", Azure Data Lake Analytics, czyli fokus w tym artykule. Mimo że można łatwo wykonywać zapytania w usłudze Hadoop ani innych rozwiązań, Data Lake Analytics często lepiej jest odpowiedni dla tego podejścia "płacić za zapytania".

Brak "ekstraktor" dla systemu Avro w języku U-SQL. Aby uzyskać więcej informacji, zobacz [przykład Avro U-SQL](https://github.com/Azure/usql/tree/master/Examples/AvroExamples).

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Zapytania i wyeksportować dane Avro do pliku CSV

W tej sekcji, wykonywanie zapytań o dane Avro i wyeksportować je do pliku CSV w usłudze Azure Blob storage, mimo że można łatwo umieścić dane w innych magazynach danych lub repozytoriów.

1. Konfigurowanie usługi Azure IoT Hub do kierowania danych do punktu końcowego magazynu obiektów Blob platformy Azure przy użyciu właściwości w treści wiadomości, aby zaznaczyć wiadomości.

   ![W sekcji "Niestandardowe punkty końcowe."](./media/iot-hub-query-avro-data/query-avro-data-1a.png)

   ![Reguły routingu](./media/iot-hub-query-avro-data/query-avro-data-1b.png)

   Aby uzyskać więcej informacji na temat ustawień tras i niestandardowe punkty końcowe, zobacz [Routing komunikatów usługi IoT hub](iot-hub-create-through-portal.md#message-routing-for-an-iot-hub).

2. Upewnij się, że urządzenie ma kodowania, typu zawartości i potrzebne dane w właściwości lub w treści wiadomości, zgodnie z odwołaniem w dokumentacji produktu. Podczas wyświetlania tych atrybutów w Device Explorer, jak pokazano poniżej, możesz sprawdzić, czy są one prawidłowo ustawione.

   ![W okienku dane do Centrum zdarzeń](./media/iot-hub-query-avro-data/query-avro-data-2.png)

3. Konfigurowanie wystąpienia usługi Azure Data Lake Store i wystąpienia usługi Data Lake Analytics. Usługa Azure IoT Hub nie jest kierowany do wystąpienia programu Data Lake Store, ale wymaga wystąpienia usługi Data Lake Analytics.

   ![Data Lake Store i Data Lake Analytics](./media/iot-hub-query-avro-data/query-avro-data-3.png)

4. W usługi Data Lake Analytics należy skonfigurować usługi Azure Blob storage jako magazynu dodatkowego, usługi Azure IoT Hub kieruje dane do tego samego magazynu obiektów Blob.

   ![W okienku "Źródła danych"](./media/iot-hub-query-avro-data/query-avro-data-4.png)

5. Zgodnie z opisem w [przykład Avro U-SQL](https://github.com/Azure/usql/tree/master/Examples/AvroExamples), potrzebne są cztery pliki DLL. Przekaż te pliki do lokalizacji w wystąpieniu usługi Data Lake Store.

   ![Cztery przekazywanych plików DLL](./media/iot-hub-query-avro-data/query-avro-data-5.png)

6. W programie Visual Studio Utwórz projekt U-SQL.

   ! Utwórz project](./media/iot-hub-query-avro-data/query-avro-data-6.png) języka U-SQL

7. Wklej zawartość poniższego skryptu do nowo utworzony plik. Zmodyfikuj trzy wyróżnione sekcje: Twoje konto usługi Data Lake Analytics, skojarzone ścieżki plików DLL i poprawna ścieżka konta magazynu.

   ![Trzy sekcje do zmodyfikowania](./media/iot-hub-query-avro-data/query-avro-data-7a.png)

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
            ""fields"":
           [{
                ""name"":""EnqueuedTimeUtc"",
                ""type"":""string""
            },
            {
                ""name"":""Properties"",
                ""type"":
                {
                    ""type"":""map"",
                    ""values"":""string""
                }
            },
            {
                ""name"":""SystemProperties"",
                ""type"":
                {
                    ""type"":""map"",
                    ""values"":""string""
                }
            },
            {
                ""name"":""Body"",
                ""type"":[""null"",""bytes""]
            }]
        }"
        );

        @cnt =
        SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
        FROM @rs;

        OUTPUT @cnt TO @output_file USING Outputters.Text(); 
    ```

    Zajęło to Data Lake Analytics Uruchom następujący skrypt, która została ograniczona do 10 jednostek analitycznych i przetworzyć pliki 177 pięć minut. Wynik jest wyświetlany w danych wyjściowych z pliku CSV, który jest wyświetlany na poniższej ilustracji:

    ![Wyniki dane wyjściowe do pliku CSV](./media/iot-hub-query-avro-data/query-avro-data-7b.png)

    ![Dane wyjściowe konwertowane do pliku CSV](./media/iot-hub-query-avro-data/query-avro-data-7c.png)

    Aby analizować dane JSON, przejdź do kroku 8.

8. Większość IoT komunikaty są w formacie pliku JSON. Dodając następujące wiersze, można przeanalizować wiadomość do pliku JSON, która umożliwia dodawanie klauzulach WHERE i tylko potrzebne dane wyjściowe.

    ```sql
       @jsonify =
         SELECT Microsoft.Analytics.Samples.Formats.Json.JsonFunctions.JsonTuple(Encoding.UTF8.GetString(Body))
           AS message FROM @rs;
    
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

    ![Pokazywanie kolumny dla każdego elementu danych wyjściowych](./media/iot-hub-query-avro-data/query-avro-data-8.png)

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób wykonywania zapytań o dane Avro do efektywne kierowanie komunikatów z usługi Azure IoT Hub do usług platformy Azure.

Aby uzyskać przykłady kompletne rozwiązania end-to-end, które używają usługi IoT Hub, zobacz [dokumentacja akceleratorów rozwiązań IoT Azure](/azure/iot-accelerators).

Aby dowiedzieć się więcej na temat opracowywania rozwiązań usługi IoT Hub, zobacz [usługi IoT Hub — przewodnik dewelopera](iot-hub-devguide.md).

Aby dowiedzieć się więcej na temat routingu komunikatów w usłudze IoT Hub, zobacz [wysyłania i odbierania komunikatów za pomocą usługi IoT Hub](iot-hub-devguide-messaging.md).
