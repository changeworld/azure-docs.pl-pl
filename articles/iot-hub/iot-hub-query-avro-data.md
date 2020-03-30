---
title: Zapytanie o dane Avro przy użyciu usługi Azure Data Lake Analytics | Dokumenty firmy Microsoft
description: Właściwości treści wiadomości służy do kierowania danych telemetrycznych urządzenia do magazynu obiektów Blob i kwerendy dane formatu Avro, który jest zapisywany w magazynie obiektów Blob.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: 92fc5bb88ff5efd8fe1a8cd61be833b3984b673a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73605614"
---
# <a name="query-avro-data-by-using-azure-data-lake-analytics"></a>Zapytanie o dane Avro przy użyciu usługi Azure Data Lake Analytics

W tym artykule omówiono sposób wykonywania zapytań dotyczących danych avro w celu efektywnego kierowania wiadomości z usługi Azure IoT Hub do usług platformy Azure. [Routing wiadomości](iot-hub-devguide-messages-d2c.md) umożliwia filtrowanie danych przy użyciu zapytań rozszerzonych na podstawie właściwości wiadomości, treści wiadomości, tagów bliźniaczej reprezentacji urządzenia i właściwości bliźniaczej reprezentacji urządzenia. Aby dowiedzieć się więcej o możliwościach wykonywania zapytań w obszarze Routing wiadomości, zobacz artykuł dotyczący [składni kwerendy routingu wiadomości](iot-hub-devguide-routing-query-syntax.md).

Wyzwaniem było to, że gdy usługa Azure IoT Hub kieruje wiadomości do magazynu obiektów Blob platformy Azure, domyślnie Usługa IoT Hub zapisuje zawartość w formacie Avro, która ma zarówno właściwość treści wiadomości, jak i właściwość wiadomości. Format Avro nie jest używany dla innych punktów końcowych. Chociaż format Avro doskonale nadaje się do przechowywania danych i wiadomości, jest to wyzwanie, aby użyć go do zapytań o dane. Dla porównania format JSON lub CSV jest znacznie łatwiejszy do wykonywania zapytań o dane. Usługa IoT Hub obsługuje teraz zapisywanie danych do magazynu obiektów Blob w json i AVRO.

Aby uzyskać więcej informacji, zobacz [Korzystanie z usługi Azure Storage jako punktu końcowego routingu](iot-hub-devguide-messages-d2c.md#azure-storage).

Aby sprostać nierelacyjne potrzeby i formaty dużych zbiorów danych i przezwyciężyć to wyzwanie, można użyć wielu wzorców dużych zbiorów danych do przekształcania i skalowania danych. Jednym z wzorców "pay per query" jest usługa Azure Data Lake Analytics, która jest głównym tematem tego artykułu. Chociaż można łatwo wykonać kwerendę w Hadoop lub innych rozwiązań, Data Lake Analytics często lepiej nadaje się do tego podejścia "pay per query".

Istnieje "ekstraktor" dla Avro w U-SQL. Aby uzyskać więcej informacji, zobacz [przykład U-SQL Avro](https://github.com/Azure/usql/tree/master/Examples/AvroExamples).

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Zapytanie i eksportowanie danych Avro do pliku CSV

W tej sekcji kwerendy avro danych i wyeksportować go do pliku CSV w magazynie obiektów Blob platformy Azure, chociaż można łatwo umieścić dane w innych repozytoriach lub magazynów danych.

1. Skonfiguruj usługę Azure IoT Hub, aby rozsyłać dane do punktu końcowego magazynu obiektów Blob platformy Azure przy użyciu właściwości w treści wiadomości, aby wybrać wiadomości.

   ![Sekcja "Niestandardowe punkty końcowe"](./media/iot-hub-query-avro-data/query-avro-data-1a.png)

   ![Reguły routingu](./media/iot-hub-query-avro-data/query-avro-data-1b.png)

   Aby uzyskać więcej informacji na temat ustawień tras i niestandardowych punktów końcowych, zobacz [Routing wiadomości dla centrum IoT](iot-hub-create-through-portal.md#message-routing-for-an-iot-hub)hub .

2. Upewnij się, że urządzenie ma kodowanie, typ zawartości i potrzebne dane we właściwościach lub treści wiadomości, jak odwołuje się w dokumentacji produktu. Podczas wyświetlania tych atrybutów w Eksploratorze urządzeń, jak pokazano w tym miejscu, można sprawdzić, czy są one ustawione poprawnie.

   ![Okienko Dane Centrum zdarzeń](./media/iot-hub-query-avro-data/query-avro-data-2.png)

3. Konfigurowanie wystąpienia usługi Azure Data Lake Store i wystąpienia usługi Data Lake Analytics. Usługa Azure IoT Hub nie jest kierowana do wystąpienia magazynu usługi Data Lake Store, ale wymaga wystąpienia usługi Data Lake Analytics.

   ![Wystąpienia usługi Data Lake Store i Data Lake Analytics](./media/iot-hub-query-avro-data/query-avro-data-3.png)

4. W usłudze Data Lake Analytics skonfiguruj magazyn obiektów blob platformy Azure jako dodatkowy magazyn, ten sam magazyn obiektów Blob, do który usługa Azure IoT Hub kieruje dane.

   ![Okienko "Źródła danych"](./media/iot-hub-query-avro-data/query-avro-data-4.png)

5. Jak wspomniano w [przykładzie U-SQL Avro](https://github.com/Azure/usql/tree/master/Examples/AvroExamples), potrzebne są cztery pliki DLL. Przekaż te pliki do lokalizacji w wystąpieniu usługi Data Lake Store.

   ![Cztery przesłane pliki DLL](./media/iot-hub-query-avro-data/query-avro-data-5.png)

6. W programie Visual Studio utwórz projekt U-SQL.

   ! Tworzenie projektu U-SQL](./media/iot-hub-query-avro-data/query-avro-data-6.png)

7. Wklej zawartość następującego skryptu do nowo utworzonego pliku. Zmodyfikuj trzy wyróżnione sekcje: konto usługi Data Lake Analytics, skojarzone ścieżki plików biblioteki DLL i właściwą ścieżkę dla konta magazynu.

   ![Trzy sekcje, które mają zostać zmienione](./media/iot-hub-query-avro-data/query-avro-data-7a.png)

   Rzeczywisty skrypt U-SQL dla prostego wyjścia do pliku CSV:

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

    Uruchomienie następującego skryptu zajęło programowi Data Lake Analytics pięć minut, który został ograniczony do 10 jednostek analitycznych i przetworzył 177 plików. Wynik jest wyświetlany na wyjściu pliku CSV, który jest wyświetlany na poniższej ilustracji:

    ![Wyniki wyjścia do pliku CSV](./media/iot-hub-query-avro-data/query-avro-data-7b.png)

    ![Dane wyjściowe przekonwertowane na plik CSV](./media/iot-hub-query-avro-data/query-avro-data-7c.png)

    Aby przeanalizować JSON, przejdź do kroku 8.

8. Większość komunikatów IoT jest w formacie JSON. Dodając następujące wiersze, można przeanalizować komunikat do pliku JSON, który pozwala dodać klauzule WHERE i dane wyjściowe tylko potrzebne dane.

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

    Dane wyjściowe wyświetla kolumnę `SELECT` dla każdego elementu w poleceniu.

    ![Dane wyjściowe przedstawiające kolumnę dla każdego elementu](./media/iot-hub-query-avro-data/query-avro-data-8.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak zbadać dane avro, aby skutecznie kierować wiadomości z usługi Azure IoT Hub do usług platformy Azure.

Aby zapoznać się z przykładami kompletnych kompleksowych rozwiązań korzystających z usługi IoT Hub, zobacz [dokumentację akceleratorów rozwiązań Azure IoT.](/azure/iot-accelerators)

Aby dowiedzieć się więcej na temat opracowywania rozwiązań za pomocą usługi IoT Hub, zobacz [przewodnik dla deweloperów usługi IoT Hub.](iot-hub-devguide.md)

Aby dowiedzieć się więcej o routingu wiadomości w Centrum IoT, zobacz [Wysyłanie i odbieranie wiadomości za pomocą usługi IoT Hub](iot-hub-devguide-messaging.md).
