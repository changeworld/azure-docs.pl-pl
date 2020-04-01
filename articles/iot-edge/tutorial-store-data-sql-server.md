---
title: Samouczek — przechowywanie danych za pomocą modułu SQL przy użyciu usługi Azure IoT Edge
description: W tym samouczku pokazano, jak przechowywać dane lokalnie na urządzeniu usługi IoT Edge za pomocą modułu programu SQL Server
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 3d1b5ea9a9f78bc8a83159a34026d58d7a8cc89b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78944268"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>Samouczek: przechowywanie danych na brzegu sieci przy użyciu baz danych programu SQL Server

Wdrażanie modułu programu SQL Server w celu przechowywania danych na urządzeniu z systemem Linux z uruchomiona usługą Azure IoT Edge.

Usługi Azure IoT Edge i programu SQL Server można używać do przechowywania danych i wykonywania zapytań dotyczących danych na brzegu sieci. Usługa Azure IoT Edge ma podstawowe funkcje magazynu w celu buforowania komunikatów, jeśli urządzenie przejdzie w tryb offline, a następnie przekazania ich dalej po ponownym ustanowieniu połączenia. Możesz jednak potrzebować bardziej zaawansowanych możliwości magazynu, takich jak możliwość lokalnego wykonywania zapytań o dane. Urządzenia usługi IoT Edge mogą używać lokalnych baz danych do wykonywania bardziej złożonych obliczeń bez konieczności utrzymywania połączenia z centrum IoT Hub.

Ten artykuł zawiera instrukcje dotyczące wdrażania bazy danych programu SQL Server na urządzeniu usługi IoT Edge. Usługa Azure Functions, uruchomiona na urządzeniu usługi IoT Edge, strukturyzuje dane przychodzące, a następnie wysyła je do bazy danych. Kroki opisane w tym artykule można zastosować również do innych baz danych, które działają w kontenerach, na przykład MySQL lub PostgreSQL.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Używanie programu Visual Studio Code do tworzenia funkcji platformy Azure
> * Wdrażanie bazy danych SQL na urządzeniu usługi IoT Edge
> * Używanie programu Visual Studio Code do kompilowania modułów i wdrażania ich na urządzeniu usługi IoT Edge
> * Wyświetlanie wygenerowanych danych

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka, należy przejść przez poprzedni samouczek, aby skonfigurować środowisko programistyczne dla tworzenia kontenerów Systemu Linux: [Opracowanie modułów IoT Edge dla urządzeń z systemem Linux](tutorial-develop-for-linux.md). Wypełniając ten samouczek, należy mieć następujące wymagania wstępne w miejscu:

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Bezpłatna lub Standardowa na platformie Azure.
* Urządzenie AMD64 [Linux z systemem Azure IoT Edge](quickstart-linux.md).
  * Urządzenia ARM, takie jak Raspberry Pis, nie mogą uruchamiać programu SQL Server. Jeśli chcesz używać sql na urządzeniu ARM, możesz zarejestrować się, aby wypróbować [usługę Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/) w wersji zapoznawczej.
* Rejestr kontenerów, taki jak [usługa Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) skonfigurowany za pomocą [narzędzi Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) skonfigurowany do uruchamiania kontenerów systemu Linux.

W tym samouczku użyto modułu usługi Azure Functions do wysyłania danych do programu SQL Server. Aby opracować moduł usługi IoT Edge za pomocą usługi Azure Functions, zainstaluj następujące dodatkowe wymagania wstępne na komputerze deweloperskim:

* [C# dla programu Visual Studio Code (obsługiwane przez OmniSharp) rozszerzenie dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [Zestaw .NET Core 2.1 SDK](https://www.microsoft.com/net/download).

## <a name="create-a-function-project"></a>Tworzenie projektu funkcji

Aby wysyłać dane do bazy danych, potrzebujesz modułu, który może poprawnie tworzyć struktury danych, a następnie przechowywać je w tabeli.

### <a name="create-a-new-project"></a>Tworzenie nowego projektu

W następujących krokach przedstawiono sposób tworzenia funkcji usługi IoT Edge przy użyciu programu Visual Studio Code i narzędzi usługi Azure IoT.

1. Otwórz program Visual Studio Code.

2. Otwórz paletę poleceń VS Code, wybierając polecenie **Wyświetl** > **paletę poleceń**.

3. W palecie poleceń wpisz i uruchom polecenie **Azure IoT Edge: nowe rozwiązanie usługi IoT Edge**. W palecie poleceń podaj następujące informacje, aby utworzyć rozwiązanie:

   | Pole | Wartość |
   | ----- | ----- |
   | Wybierz folder | Wybierz lokalizację na maszynie deweloperskiej dla programu VS Code, aby utworzyć pliki rozwiązania. |
   | Podaj nazwę rozwiązania | Wprowadź opisową nazwę rozwiązania, na przykład **SqlSolution**, lub zaakceptuj nazwę domyślną. |
   | Wybierz szablon modułu | Wybierz pozycję **Azure Functions — C#**. |
   | Podaj nazwę modułu | Nadaj modułowi nazwę **sqlFunction**. |
   | Podaj repozytorium obrazów platformy Docker dla modułu | Repozytorium obrazów zawiera nazwę rejestru kontenerów oraz nazwę obrazu kontenera. Obraz kontenera jest wstępnie wypełniany w ostatnim kroku. Zastąp ciąg **localhost:5000** wartością serwera logowania z rejestru kontenerów platformy Azure. Serwer logowania możesz pobrać ze strony Przegląd rejestru kontenerów w witrynie Azure Portal. <br><br>Końcowy ciąg wygląda \<jak\>nazwa rejestru .azurecr.io/sqlfunction. |

   W oknie programu VS Code zostanie załadowany obszar roboczy rozwiązania usługi IoT Edge.

### <a name="add-your-registry-credentials"></a>Dodawanie poświadczeń rejestru

W pliku środowiska przechowywane są poświadczenia rejestru kontenerów udostępniane środowisku uruchomieniowemu usługi IoT Edge. Środowisko uruchomieniowe wymaga tych poświadczeń do ściągnięcia prywatnych obrazów na urządzenie usługi IoT Edge.

1. W eksploratorze programu VS Code otwórz plik env.
2. Zaktualizuj pola, używając **nazwy użytkownika** i **hasła**, które zostały skopiowane z usługi Azure Container Registry.
3. Zapisz ten plik.

### <a name="select-your-target-architecture"></a>Wybierz architekturę docelową

Obecnie Visual Studio Code może tworzyć moduły C dla urządzeń z systemem Linux AMD64 i Linux ARM32v7. Należy wybrać architekturę, która jest przeznaczona dla każdego rozwiązania, ponieważ kontener jest zbudowany i uruchamiany inaczej dla każdego typu architektury. Domyślnie jest Linux AMD64.

1. Otwórz paletę poleceń i wyszukaj **usługę Azure IoT Edge: Set Default Target Platform for Edge Solution**lub wybierz ikonę skrótu na pasku bocznym u dołu okna.

2. W palecie poleceń wybierz architekturę docelową z listy opcji. W tym samouczku używamy maszyny wirtualnej Ubuntu jako urządzenia IoT Edge, więc zachowamy domyślny **amd64**.

### <a name="update-the-module-with-custom-code"></a>Aktualizowanie modułu przy użyciu kodu niestandardowego

1. W eksploratorze kodu VS otwórz **moduły** > **sqlFunction** > **sqlFunction.cs**.

2. Zastąp całą zawartość pliku następującym kodem:

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.IO;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Client;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.EdgeHub;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;
   using Newtonsoft.Json;
   using Sql = System.Data.SqlClient;

   namespace Functions.Samples
   {
       public static class sqlFunction
       {
           [FunctionName("sqlFunction")]
           public static async Task FilterMessageAndSendMessage(
               [EdgeHubTrigger("input1")] Message messageReceived,
               [EdgeHub(OutputName = "output1")] IAsyncCollector<Message> output,
               ILogger logger)
           {
               const int temperatureThreshold = 20;
               byte[] messageBytes = messageReceived.GetBytes();
               var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

               if (!string.IsNullOrEmpty(messageString))
               {
                   logger.LogInformation("Info: Received one non-empty message");
                   // Get the body of the message and deserialize it.
                   var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                   //Store the data in SQL db
                   const string str = "<sql connection string>";
                   using (Sql.SqlConnection conn = new Sql.SqlConnection(str))
                   {
                       conn.Open();
                       var insertMachineTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'machine', " + messageBody.machine.temperature + ");";
                       var insertAmbientTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'ambient', " + messageBody.ambient.temperature + ");";
                       using (Sql.SqlCommand cmd = new Sql.SqlCommand(insertMachineTemperature + "\n" + insertAmbientTemperature, conn))
                       {
                           //Execute the command and log the # rows affected.
                           var rows = await cmd.ExecuteNonQueryAsync();
                           logger.LogInformation($"{rows} rows were updated");
                       }
                   }

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threashold.
                       using (var filteredMessage = new Message(messageBytes))
                       {
                            // Copy the properties of the original message into the new Message object.
                            foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                            {filteredMessage.Properties.Add(prop.Key, prop.Value);}
                            // Add a new property to the message to indicate it is an alert.
                            filteredMessage.Properties.Add("MessageType", "Alert");
                            // Send the message.
                            await output.AddAsync(filteredMessage);
                            logger.LogInformation("Info: Received and transferred a message with temperature above the threshold");
                       }
                   }
               }
           }
       }
       //Define the expected schema for the body of incoming messages.
       class MessageBody
       {
           public Machine machine {get; set;}
           public Ambient ambient {get; set;}
           public string timeCreated {get; set;}
       }
       class Machine
       {
           public double temperature {get; set;}
           public double pressure {get; set;}
       }
       class Ambient
       {
           public double temperature {get; set;}
           public int humidity {get; set;}
       }
   }
   ```

3. W wierszu 35 zastąp ** \<ciąg sql na\> ** następujący ciąg. Właściwość **Źródło danych** odwołuje się do kontenera programu SQL Server, który jeszcze nie istnieje. Utworzysz go o nazwie **SQL** w następnej sekcji.

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

4. Zapisz plik **sqlFunction.cs**.

5. Otwórz plik **sqlFunction.csproj**.

6. Znajdź grupę odwołań do pakietu i dodaj nową, aby uwzględnić SqlClient.

   ```csproj
   <PackageReference Include="System.Data.SqlClient" Version="4.5.1"/>
   ```

7. Zapisz plik **sqlFunction.csproj**.

## <a name="add-the-sql-server-container"></a>Dodawanie kontenera programu SQL Server

[Manifest wdrożenia](module-composition.md) deklaruje moduły, które środowisko uruchomieniowe usługi IoT Edge zainstaluje na urządzeniu usługi IoT Edge. Kod został podany w celu wykonania dostosowanego modułu funkcji w poprzedniej sekcji, ale moduł programu SQL Server jest już zbudowany i dostępny w portalu Azure Marketplace. Wystarczy poinstruować środowisko uruchomieniowe usługi IoT Edge, że ma dołączyć ten moduł, a następnie skonfigurować go na urządzeniu.

1. W programie Visual Studio Code otwórz paletę poleceń, wybierając pozycję **Wyświetl** > **paletę poleceń**.

2. W palecie poleceń wpisz i uruchom polecenie **Azure IoT Edge: Dodaj moduł IoT Edge**. W palecie poleceń podaj następujące informacje, aby dodać nowy moduł:

   | Pole | Wartość |
   | ----- | ----- |
   | Wybierz plik szablonu wdrożenia | Paleta poleceń wyróżnia plik deployment.template.json w bieżącym folderze rozwiązania. Wybierz ten plik.  |
   | Wybierz szablon modułu | Wybierz **moduł z portalu Azure Marketplace**. |

3. W portalu Azure IoT Edge module marketplace wyszukaj i wybierz **moduł PROGRAMU SQL Server**.

4. Zmień nazwę modułu na **sql**, wszystkie małe litery. Ta nazwa jest zgodna z nazwą kontenera zadeklarowanym w ciągu połączenia w pliku sqlFunction.cs.

5. Wybierz **opcję Importuj,** aby dodać moduł do rozwiązania.

6. W folderze rozwiązania otwórz plik **deployment.template.json.**

7. Znajdź sekcję **modules**. Powinny być widoczne trzy moduły. Moduł *SimulatedTemperatureSensor* jest domyślnie uwzględniony w nowych rozwiązaniach i zapewnia dane testowe do użycia z innymi modułami. Moduł *sqlFunction* jest modułem, który został pierwotnie utworzony i zaktualizowany o nowy kod. Na koniec moduł *sql* został zaimportowany z portalu Azure Marketplace.

   >[!Tip]
   >Moduł programu SQL Server jest dostarczany z domyślnym hasłem ustawionym w zmiennych środowiskowych manifestu wdrażania. Za każdym razem, gdy tworzysz kontener programu SQL Server w środowisku produkcyjnym, zaleca się [zmienić domyślne hasło administratora systemu](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker).

8. Zamknij plik **deployment.template.json.**

## <a name="build-your-iot-edge-solution"></a>Kompilowanie rozwiązania usługi IoT Edge

W poprzednich sekcjach utworzono rozwiązanie z jednym modułem, a następnie dodano drugi do szablonu manifestu wdrożenia. Moduł PROGRAMU SQL Server jest obsługiwany publicznie przez firmę Microsoft, ale należy konteneryzować kod w module Functions. W tej sekcji skompilować rozwiązanie, utworzyć obrazy kontenerów dla modułu sqlFunction i wypchnąć obraz do rejestru kontenerów.

1. W programie Visual Studio Code otwórz zintegrowany terminal, wybierając **pozycję Wyświetl** > **terminal**.  

1. Zaloguj się do rejestru kontenerów w programie Visual Studio Code, aby mógł on wypchnąć obrazy do rejestru. Użyj tych samych poświadczeń usługi Azure Container Registry (ACR), które zostały dodane do pliku env. W zintegrowanym terminalu wprowadź następujące polecenie:

    ```csh/sh
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

    Może zostać wyświetlone ostrzeżenie zabezpieczające zalecające użycie parametru --password-stdin. Użycie tego parametru wykracza poza zakres tego artykułu, jednak zalecamy zastosowanie tego najlepszego rozwiązania. Aby uzyskać więcej informacji, zobacz odwołanie do polecenia [logowania docker.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

1. W eksploratorze programu VS Code kliknij prawym przyciskiem myszy plik **deployment.template.json** i wybierz polecenie **Skompiluj i wypchnij rozwiązanie usługi IoT Edge**.

Po poinformowaniu programu Visual Studio Code o utworzeniu rozwiązania najpierw pobiera informacje z szablonu wdrożenia i generuje plik deployment.json w nowym folderze o nazwie **config**. Następnie uruchamia dwa polecenia w zintegrowanym `docker build` `docker push`terminalu: i . Polecenie kompilacji kodu i konteneryzuje moduł. Następnie polecenie wypychania wypycha kod do rejestru kontenerów, który został określony podczas inicjowania rozwiązania.

Można sprawdzić, czy moduł sqlFunction został pomyślnie wypchnięty do rejestru kontenerów. W witrynie Azure portal przejdź do rejestru kontenerów. Wybierz **repozytoria** i wyszukaj **sqlFunction**. Pozostałe dwa moduły, SimulatedTemperatureSensor i sql, nie zostaną wypchnięte do rejestru kontenerów, ponieważ ich repozytoria znajdują się już w rejestrach firmy Microsoft.

## <a name="deploy-the-solution-to-a-device"></a>Wdrażanie rozwiązania na urządzeniu

Moduły można ustawić na urządzeniu za pomocą usługi IoT Hub, ale dostęp do usługi IoT Hub i urządzeń można również uzyskać za pomocą programu Visual Studio Code. W tej sekcji skonfigurujesz dostęp do usługi IoT Hub, a następnie użyjesz programu VS Code do wdrożenia rozwiązania na urządzeniu usługi IoT Edge.

1. W eksploratorze programu VS rozwiń sekcję **Urządzenia usługi Azure IoT Hub**.

2. Kliknij prawym przyciskiem myszy urządzenie, które ma być urządzeniem docelowym wdrożenia, i wybierz polecenie **Create deployment for single device** (Utwórz wdrożenie dla pojedynczego urządzenia).

3. W Eksploratorze plików przejdź do folderu **config** wewnątrz rozwiązania i wybierz plik **deployment.amd64**. Kliknij pozycję **Wybierz manifest wdrożenia usługi Edge**.

   Nie należy używać pliku deployment.template.json jako manifestu wdrożenia.

Jeśli wdrożenie zakończy się pomyślnie, komunikat potwierdzenia zostanie wydrukowany w danych wyjściowych programu VS Code.

Odśwież stan urządzenia w sekcji Urządzenia usługi Azure IoT Hub w programie VS Code. Nowe moduły będą widoczne na liście i w ciągu kilku kolejnych minut zaczną być zgłaszane jako działające, w miarę instalowania i uruchamiania kontenerów. Możesz również sprawdzić, czy wszystkie moduły są wdrożone i uruchomione na urządzeniu. Na urządzeniu usługi IoT Edge uruchom następujące polecenie, aby wyświetlić stan modułów.

   ```cmd/sh
   iotedge list
   ```

## <a name="create-the-sql-database"></a>Tworzenie bazy danych SQL

Po zastosowaniu manifestu wdrożenia do urządzenia uzyskujesz trzy uruchomione moduły. Moduł SimulatedTemperatureSensor generuje symulowane dane środowiska. Moduł sqlFunction pobiera dane i formatuje je na potrzeby bazy danych. Ta sekcja zawiera opis konfigurowania bazy danych SQL do przechowywania danych dotyczących temperatury.

Uruchom następujące polecenia na urządzeniu usługi IoT Edge. Te polecenia łączą się z modułem **sql** działającym na urządzeniu oraz tworzą bazę danych i tabelę na potrzeby przechowywania przesyłanych do niego danych o temperaturze.

1. W narzędziu wiersza polecenia na urządzeniu usługi IoT Edge nawiąż połączenie z bazą danych.

      ```bash
      sudo docker exec -it sql bash
      ```

2. Otwórz narzędzie polecenia SQL.

      ```bash
      /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
      ```

3. Utwórz bazę danych:

      ```sql
      CREATE DATABASE MeasurementsDB
      ON
      (NAME = MeasurementsDB, FILENAME = '/var/opt/mssql/measurementsdb.mdf')
      GO
      ```

4. Zdefiniuj tabelę.

   ```sql
   CREATE TABLE MeasurementsDB.dbo.TemperatureMeasurements (measurementTime DATETIME2, location NVARCHAR(50), temperature FLOAT)
   GO
   ```

Plik docker programu SQL Server możesz dostosować, aby automatycznie skonfigurować program SQL Server do wdrożenia na wielu urządzeniach usługi IoT Edge. Aby uzyskać więcej informacji, zobacz [projekt demonstracyjny kontenera programu Microsoft SQL Server](https://github.com/twright-msft/mssql-node-docker-demo-app).

## <a name="view-the-local-data"></a>Wyświetlanie danych lokalnych

Po utworzeniu tabeli moduł sqlFunction uruchamia przechowywanie danych w lokalnej bazie danych programu SQL Server 2017 na urządzeniu usługi IoT Edge.

W narzędziu polecenia SQL uruchom następujące polecenie, aby wyświetlić sformatowane dane tabeli:

   ```sql
   SELECT * FROM MeasurementsDB.dbo.TemperatureMeasurements
   GO
   ```

   ![Wyświetlanie zawartości lokalnej bazy danych](./media/tutorial-store-data-sql-server/view-data.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie. Możesz także nadal używać tego samego urządzenia usługi IoT Edge jako urządzenia testowego.

W przeciwnym razie możesz usunąć konfigurację lokalną i zasoby platformy Azure utworzone podczas pracy z tym artykułem, aby uniknąć naliczania opłat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku został utworzony moduł usługi Azure Functions zawierający kod służący do filtrowania nieprzetworzonych danych wygenerowanych przez urządzenie usługi IoT Edge. Gdy wszystko będzie gotowe do tworzenia własnych modułów, możesz dowiedzieć się więcej na temat tego, jak [programować usługę Azure Functions za pomocą usługi Azure IoT Edge dla programu Visual Studio Code](how-to-develop-csharp-function.md).

Jeśli chcesz wypróbować inną metodę magazynu na urządzeniach brzegowych, przeczytaj o tym, jak używać usługi Azure Blob Storage w usłudze IoT Edge.

> [!div class="nextstepaction"]
> [Store data at the edge with Azure Blob Storage on IoT Edge (Przechowywanie danych na urządzeniu brzegowym dzięki usłudze Azure Blob Storage w usłudze IoT Edge)](how-to-store-data-blob.md)
