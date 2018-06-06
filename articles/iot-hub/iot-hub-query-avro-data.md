---
title: Zapytanie Avro danych przy użyciu usługi Azure Data Lake Analytics | Dokumentacja firmy Microsoft
description: Użyj właściwości treści wiadomości do telemetrii urządzenia trasy do magazynu obiektów blob i kwerend danych format Avro zapisywane do magazynu obiektów blob.
services: iot-hub
documentationcenter: ''
author: ksaye
manager: obloch
ms.service: iot-hub
ms.topic: article
ms.date: 05/29/2018
ms.author: Kevin.Saye
ms.openlocfilehash: 98a30155c73a937042b4bea6568543fb5152d748
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34727973"
---
# <a name="query-avro-data-using-azure-data-lake-analytics"></a>Dane Avro zapytania przy użyciu usługi Azure Data Lake Analytics

W tym artykule jest o tym, jak wykonać zapytania o dane Avro wydajnie routingu wiadomości z Centrum IoT Azure do usług platformy Azure. Następujące powiadomienia post blogu —[Centrum IoT Azure wiadomości routingu: teraz z routingiem na treść komunikatu], Centrum IoT obsługuje routing na właściwości lub treści wiadomości. Zobacz też [routingu w treści wiadomości][Routing on message bodies]. 

Żądania zostało który, gdy Centrum IoT Azure kieruje komunikaty do magazynu obiektów blob, Centrum IoT zapisuje zawartość w formacie Avro, który ma właściwości wiadomości i treści wiadomości. Należy pamiętać, że Centrum IoT obsługuje tylko zapisywania danych do magazynu w formacie danych Avro obiektów blob, a ten format nie jest używany dla innych punktów końcowych. Zobacz [podczas korzystania z usługi Azure Storage kontenery][When using Azure storage containers]. Avro format jest doskonały dla przechowywania danych i komunikat, jest trudne do wykonywania zapytań danych. Z kolei w formacie JSON lub CSV jest znacznie łatwiejsze do wykonywania zapytań danych.

Aby rozwiązać ten problem, można wielu wzorców danych big data Przekształcanie i skalowanie danych nierelacyjnych danych big data potrzeb i formaty adresów. Jednym z wzorców wzorzec "płać na zapytanie" jest usługi Azure Data Lake Analytics (ADLA). Jest fokus w tym artykule. Chociaż łatwo można wykonać zapytania w Hadoop ani innych rozwiązań, ADLA jest często nadaje lepiej takie podejście "płać na zapytanie". Brak "ekstraktor" dla Avro w języku U-SQL. Zobacz [przykład Avro U-SQL].

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Zapytania i wyeksportuj dane Avro do pliku CSV
Sekcji przedstawiono sposób zapytywanie o dane Avro i eksportowania ich do pliku CSV w magazynie obiektów Blob Azure, chociaż można łatwo umieścić dane w innych magazynach repozytoria lub danych.

1. Konfigurowanie Centrum IoT Azure na przesyłanie danych do punktu końcowego magazynu obiektów Blob Azure, aby zaznaczyć wiadomości przy użyciu właściwości w treści wiadomości.

    ![Przechwytywanie ekranu dla krok 1a][img-query-avro-data-1a]

    ![Przechwytywanie ekranu dla krok 1b][img-query-avro-data-1b]

2. Sprawdź, czy urządzenie ma kodowanie, typu zawartości i potrzebnych danych właściwości lub treść komunikatu zgodnie z informacjami zawartymi w dokumentacji produktu. Wyświetlony w Eksploratorze urządzenia (patrz poniżej), można sprawdzić, czy te atrybuty są poprawnie ustawione.

    ![Przechwytywanie ekranu do kroku 2][img-query-avro-data-2]

3. Konfigurowanie usługi Azure Data Lake magazyn (ADLS) i wystąpienie usługi Azure Data Lake Analytics. Gdy Centrum IoT Azure nie może kierować do usługi Azure Data Lake Store, ADLA wymaga jednego.

    ![Przechwytywanie ekranu do kroku 3][img-query-avro-data-3]

4. ADLA skonfigurować jako dodatkowego magazynu tego samego magazynu obiektów Blob, który Centrum IoT Azure przekierowuje dane do magazynu obiektów Blob Azure.

    ![Przechwytywanie ekranu do kroku 4][img-query-avro-data-4]
 
5. Zgodnie z opisem w [przykład Avro U-SQL], istnieją 4 bibliotek DLL, które są wymagane.  Przekazywanie tych plików do lokalizacji w sieci ADLS.

    ![Przechwytywanie ekranu do kroku 5][img-query-avro-data-5] 

6. W programie Visual Studio Utwórz projekt U-SQL
 
    ![Przechwytywanie ekranu do kroku 6][img-query-avro-data-6]

7. Skopiuj zawartość poniższego skryptu i wklej go do nowo utworzonego pliku. Modyfikowanie 3 sekcje wyróżnione: Twoje konto ADLA, ścieżki skojarzone biblioteki dll i poprawną ścieżkę dla konta magazynu.
    
    ![Przechwytywanie ekranu do kroku 7a][img-query-avro-data-7a]

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

    Uruchomienie skryptu, pokazano poniżej, ADLA trwało 5 minut po maksymalnie 10 jednostek analityczne i przetwarzane pliki 177, podsumowania dane wyjściowe do pliku CSV.
    
    ![Przechwytywanie ekranu do kroku 7b][img-query-avro-data-7b]

    Wyświetlanie danych wyjściowych, można zauważyć, że zawartość Avro został przekonwertowany na plik CSV. Jeśli chcesz przeanalizować składni JSON i przejdź do kroku 8.
    
    ![Przechwytywanie ekranu do kroku 7c][img-query-avro-data-7c]

    
8. Większość wiadomości IoT są w formacie JSON.  Dodawanie następujące wiersze, można przeanalizować komunikatu do formatu JSON, aby można było dodać klauzulach WHERE i tylko dane wyjściowe potrzebnych danych.

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

9. Wyświetlanie danych wyjściowych, pojawi się kolumny dla każdego elementu w poleceniu select. 
    
    ![Przechwytywanie ekranu do kroku 8][img-query-avro-data-8]

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku przedstawiono sposób zapytania na danych Avro wydajnie routingu wiadomości z Centrum IoT Azure do usług platformy Azure.

Aby zapoznać się przykładem kompletnych rozwiązań end-to-end korzystających z Centrum IoT, zobacz [zdalnego monitorowania Azure IoT akcelerator rozwiązań][lnk-iot-sa-land].

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

[przykład Avro U-SQL]:https://github.com/Azure/usql/tree/master/Examples/AvroExamples

[lnk-iot-sa-land]: ../iot-accelerators/index.md
[Przewodnik dewelopera Centrum IoT]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
