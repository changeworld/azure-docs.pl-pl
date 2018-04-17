---
title: Moduł SQL krawędzi IoT Azure | Dokumentacja firmy Microsoft
description: Przechowywanie danych na krawędzi, z modułów programu Microsoft SQL, z usługi Azure Functions do formatowania danych.
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: ebertrams
ms.date: 02/21/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: d464bbfb9f38b184e47911a7224be8ec8679f0be
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="store-data-at-the-edge-with-sql-server-databases"></a>Przechowywanie danych na krawędzi, z baz danych programu SQL Server

Urządzenia brzegowe IoT Azure umożliwia przechowywania danych, który jest generowany na krawędzi. Urządzenia z sporadyczne połączenia z Internetem, można zachować własnych baz danych i raportów zmian z powrotem na chmurze tylko w przypadku połączenia. Urządzenia, które mają zaprogramowane wysyłania tylko najważniejszych danych w chmurze można zapisać pozostałe dane zbiorcze regularnych operacji przekazywania. Gdy w chmurze, dane strukturalne są udostępniane z innymi usługami Azure, na przykład do tworzenia modelu uczenia maszynowego. 

Ten artykuł zawiera instrukcje dotyczące wdrażania bazy danych programu SQL Server do urządzenia IoT. Środowisko Azure Functions, uruchomionej na urządzeniu IoT krawędzi struktury danych przychodzących, a następnie wysyła je do bazy danych. Kroki opisane w tym artykule dotyczą również innych baz danych, które działają w kontenerach, takich jak MySQL lub PostgreSQL. 

## <a name="prerequisites"></a>Wymagania wstępne 

Przed rozpoczęciem zgodnie z instrukcjami w tym artykule, należy wykonać następujące samouczki:
* Wdrożenie usługi Azure IoT krawędzi na symulowane urządzenie w [Windows](tutorial-simulate-device-windows.md) lub [systemu Linux](tutorial-simulate-device-linux.md)
* [Wdrażanie funkcji platformy Azure jako moduł krawędzi IoT](tutorial-deploy-function.md)

Następujące artykuły nie są wymagane do pomyślnego ukończenia tego samouczka, ale może zapewnić przydatne kontekstu:
* [Uruchom z rozwiązaniem Docker z obrazu kontenera 2017 serwera SQL](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker)
* [Użyj programu Visual Studio Code do opracowywania i wdrażania usługi Azure Functions do krawędzi IoT Azure](how-to-vscode-develop-azure-function.md)

Po ukończeniu wymagane samouczki powinny mieć wymagania wstępne gotowy na komputerze: 
* Aktywnym Centrum Azure IoT.
* Urządzenie brzegowe IoT z co najmniej 2 GB pamięci RAM i dysku 2 GB.
* [Program Visual Studio Code](https://code.visualstudio.com/) 
* [Rozszerzenie usługi Azure IoT Edge dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Rozszerzenie C# for Visual Studio Code (obsługiwane przez technologię OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Docker](https://docs.docker.com/engine/installation/)
* [Zestaw .NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd) 
* [Python 2.7](https://www.python.org/downloads/)
* [IoT krawędzią formantu skryptu](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
* Szablon AzureIoTEdgeFunction (`dotnet new -i Microsoft.Azure.IoT.Edge.Function`)
* Aktywnym Centrum IoT z co najmniej IoT urządzenia.

Zarówno systemu Windows i Linux kontenery x64 architektury procesora działa w tym samouczku. SQL Server nie obsługuje procesorów ARM.

## <a name="deploy-a-sql-server-container"></a>Wdrażanie kontenera programu SQL Server

W tej sekcji możesz dodać bazy danych MS SQL symulowane urządzenie brzegowe IoT. Użyj programu SQL Server 2017 docker kontener obrazu, dostępne jako [Windows](https://hub.docker.com/r/microsoft/mssql-server-windows-developer/) kontenera i jako [Linux](https://hub.docker.com/r/microsoft/mssql-server-linux/) kontenera. 

### <a name="deploy-sql-server-2017"></a>Wdrażanie programu SQL Server 2017

Domyślnie kodu w tej sekcji tworzy kontener z bezpłatna wersja Developer programu SQL Server w 2017 r. Jeśli chcesz zamiast tego uruchomić wersji produkcyjnej, zobacz [Uruchom produkcji kontener obrazów](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#production) Aby uzyskać szczegółowe informacje. 

W kroku 3, możesz dodać utworzyć opcje do kontenera programu SQL Server, które są ważne w przypadku nawiązywania zmienne środowiskowe i trwałych magazynu. Skonfigurowanego [zmiennych środowiskowych](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-environment-variables) zaakceptować umowę licencyjną użytkownika oprogramowania i określ hasło. [Magazynu trwałych](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#persist) jest konfigurowana przy użyciu [instaluje](https://docs.docker.com/storage/volumes/). Instalacji utworzyć kontener 2017 serwera SQL z *sqlvolume* kontenera woluminów dołączony tak, aby dane będzie się powtarzać, nawet po usunięciu kontenera. 

1. Otwórz `deployment.json` pliku w Visual Studio Code. 
2. Zastąp **modułów** sekcji pliku następującym kodem: 

   ```json
   "modules": {
          "filterFunction": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "<docker registry address>/filterfunction:latest",
              "createOptions": "{}"
            }
          },
          "tempSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
              "createOptions": "{}"
            }
          },
          "sql": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "",
              "createOptions": ""
             }
          }
        }
   ```

3. Zastąp `<docker registry address>` z adresem w samouczku ukończone [wdrażanie funkcji platformy Azure jako moduł krawędzi IoT - preview](https://docs.microsoft.com/en-us/azure/iot-edge/tutorial-deploy-function)

   >[!NOTE]
   >Adres rejestru kontenera jest taki sam jak adres serwera logowania skopiowany z rejestru. Powinna być w formie `<your container registry name>.azurecr.io`

4. W zależności od systemu operacyjnego, której używasz należy zaktualizować ustawienia modułu SQL z następującym kodem: 

   * W systemie Windows:

      ```json
      "image": "microsoft/mssql-server-windows-developer",
      "createOptions": "{\"Env\": [\"ACCEPT_EULA=Y\",\"MSSQL_SA_PASSWORD=Strong!Passw0rd\"],\"HostConfig\": {\"Mounts\": [{\"Target\": \"C:\\\\mssql\",\"Source\": \"sqlVolume\",\"Type\": \"volume\"}],\"PortBindings\": {\"1433/tcp\": [{\"HostPort\": \"1401\"}]}}}"
      ```

   * Linux:

      ```json
      "image": "microsoft/mssql-server-linux:2017-latest",
      "createOptions": "{\"Env\": [\"ACCEPT_EULA=Y\",\"MSSQL_SA_PASSWORD=Strong!Passw0rd\"],\"HostConfig\": {\"Mounts\": [{\"Target\": \"/var/opt/mssql\",\"Source\": \"sqlVolume\",\"Type\": \"volume\"}],\"PortBindings\": {\"1433/tcp\": [{\"HostPort\": \"1401\"}]}}}"
      ```

5. Zapisz plik. 
6. W palecie VS kod polecenia Wybierz **krawędzi: tworzenie wdrożenia dla urządzenie brzegowe**. 
7. Wybierz identyfikatora urządzenia IoT krawędzi
8. Wybierz `deployment.json` plików, które zostało zaktualizowane. W oknie danych wyjściowych widać odpowiednie dane wyjściowe dla danego wdrożenia. 
9. Aby uruchomić środowisko uruchomieniowe programu Edge, zaznacz **krawędzi: Start krawędzi** w palecie polecenia.

>[!TIP]
>Należy utworzyć kontener programu SQL Server w środowisku produkcyjnym za każdym razem [zmienić domyślne hasło administratora systemu](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker#change-the-sa-password).

## <a name="create-the-sql-database"></a>Tworzenie bazy danych SQL

Ta sekcja przeprowadzi Cię przez ustawienie bazy danych SQL do przechowywania danych temperatury odebranych z czujników podłączone do urządzenia IoT krawędzi. Jeśli używasz symulowane urządzenie tym dane pochodzą z *tempSensor* kontenera. 

W narzędziu wiersza polecenia połączenia z bazą danych: 

* Kontener systemu Windows
   ```cmd
   docker exec -it sql cmd
   ```

* Kontener systemu Linux
   ```bash
   docker exec -it sql bash
   ```

Otwórz narzędzie polecenia SQL: 

* Kontener systemu Windows
   ```cmd
   sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

* Kontener systemu Linux
   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

Tworzenie bazy danych: 

* Kontener systemu Windows
   ```sql
   CREATE DATABASE MeasurementsDB
   ON
   (NAME = MeasurementsDB, FILENAME = 'C:\mssql\measurementsdb.mdf')
   GO
   ```

* Kontener systemu Linux
   ```sql
   CREATE DATABASE MeasurementsDB
   ON
   (NAME = MeasurementsDB, FILENAME = '/var/opt/mssql/measurementsdb.mdf')
   GO
   ```

Zdefiniuj tabelę: 

   ```sql
   CREATE TABLE MeasurementsDB.dbo.TemperatureMeasurements (measurementTime DATETIME2, location NVARCHAR(50), temperature FLOAT)
   GO
   ```

Można dostosować w pliku docker programu SQL Server, aby automatycznie skonfigurować program SQL Server do wdrożenia na wielu urządzeniach IoT krawędzi. Aby uzyskać więcej informacji, zobacz [projektu pokaz kontenera programu Microsoft SQL Server](https://github.com/twright-msft/mssql-node-docker-demo-app).

## <a name="understand-the-sql-connection"></a>Zrozumienie połączenia SQL

W innych samouczków używamy trasy umożliwiające kontenerów do komunikacji, zachowując od siebie odizolowane. Podczas pracy z bazy danych programu SQL Server, jednak bliżej relacji jest konieczne. 

Krawędź IoT automatycznie tworzy mostka (Linux) lub sieci NAT (z systemem Windows), podczas uruchamiania. Sieć jest nazywany **azure iot krawędzią**. Jeśli trzeba do debugowania tego połączenia, można wyszukiwać jego właściwości w wierszu polecenia:

* Windows

   ```cmd
   docker network inspect azure-iot-edge
   ```

* Linux

   ```bash
   sudo docker network inspect azure-iot-edge
   ```

Krawędź IoT można również rozwiązać DNS nazwa kontenera za pomocą docker, dzięki czemu nie trzeba odwoływać się do bazy danych SQL Server przy użyciu adresu IP. 

Na przykład poniżej przedstawiono ciągu połączenia używanego w następnej sekcji: 

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

Widać, że parametry połączenia odwołuje się do kontenera przy użyciu nazwy **sql**. Zmiana nazwy modułu do innego elementu można zaktualizować te parametry połączenia. W przeciwnym razie przejdź do następnej sekcji. 

## <a name="update-your-azure-function"></a>Aktualizacja funkcji platformy Azure
Aby wysłać dane do bazy danych, zaktualizuj funkcji platformy Azure funkcji FilterFunction utworzonego w poprzednim samouczka. Tak, aby struktury danych odebranych przez użytkownika czujników, następnie przechowuje je w tabeli SQL, należy zmienić ten plik. 

1. W programie Visual Studio Code Otwórz folder funkcji FilterFunction. 
2. Zastąp plik run.csx następujący kod, który zawiera parametry połączenia SQL z poprzedniej sekcji: 

   ```csharp
   #r "Microsoft.Azure.Devices.Client"
   #r "Newtonsoft.Json"
   #r "System.Data.SqlClient"

   using System.IO;
   using Microsoft.Azure.Devices.Client;
   using Newtonsoft.Json;
   using Sql = System.Data.SqlClient;
   using System.Threading.Tasks;

   // Filter messages based on the temperature value in the body of the message and the temperature threshold value.
   public static async Task Run(Message messageReceived, IAsyncCollector<Message> output, TraceWriter log)
   {
       const int temperatureThreshold = 25;
       byte[] messageBytes = messageReceived.GetBytes();
       var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

       if (!string.IsNullOrEmpty(messageString))
       {
           // Get the body of the message and deserialize it
           var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

           //Store the data in SQL db
           const string str = "Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;";
           using (Sql.SqlConnection conn = new Sql.SqlConnection(str))
           {
           conn.Open();
           var insertMachineTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'machine', " + messageBody.machine.temperature + ");";
           var insertAmbientTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'ambient', " + messageBody.ambient.temperature + ");"; 
               using (Sql.SqlCommand cmd = new Sql.SqlCommand(insertMachineTemperature + "\n" + insertAmbientTemperature, conn))
               {
               //Execute the command and log the # rows affected.
               var rows = await cmd.ExecuteNonQueryAsync();
               log.Info($"{rows} rows were updated");
               }
           }

           if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
           {
               // Send the message to the output as the temperature value is greater than the threshold
               var filteredMessage = new Message(messageBytes);
               // Copy the properties of the original message into the new Message object
               foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
               {
                   filteredMessage.Properties.Add(prop.Key, prop.Value);
               }
               // Add a new property to the message to indicate it is an alert
               filteredMessage.Properties.Add("MessageType", "Alert");
               // Send the message        
               await output.AddAsync(filteredMessage);
               log.Info("Received and transferred a message with temperature above the threshold");
           }
       }
   }

   //Define the expected schema for the body of incoming messages
   class MessageBody
   {
       public Machine machine {get;set;}
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
   ```

## <a name="update-your-container-image"></a>Aktualizacja obrazu kontenera

Aby zastosować zmiany, które zostały wprowadzone, aktualizacja kontenera obrazu, przed opublikowaniem i uruchomienie IoT krawędzi.

1. W programie Visual Studio Code, rozwiń węzeł **Docker** folderu. 
2. Oparte na platformie, rozwiń pozycję **windows nano** lub **linux x64** folderu. 
3. Kliknij prawym przyciskiem myszy **plik Dockerfile** plik i wybierz **krawędzi IoT Tworzenie modułu Docker obrazu**.
4. Przejdź do **funkcji FilterFunction** folderu projektu i kliknij przycisk **wybierz folder jako EXE_DIR**.
5. W wyskakującym polu tekstowym w górnej części okna kodu programu VS Code wprowadź nazwę obrazu. Na przykład `<your container registry address>/filterfunction:latest`. Jeśli są wdrażane w lokalnym rejestrze, nazwa powinna być `<localhost:5000/filterfunction:latest>`.
6. W kodzie VS palety polecenia, wybierz **krawędzi: Push krawędzi IoT modułu Docker obrazu**. 
7. W polu tekstowym wyskakujących wprowadzić taką samą nazwę obrazu. 
8. W kodzie VS palety polecenia, wybierz **krawędzi: Uruchom ponownie krawędzi**.

## <a name="view-the-local-data"></a>Wyświetl dane lokalne

Po ponownie uruchomić kontenerów, danych otrzymywanych z czujnikami temperatury jest przechowywana w lokalnej bazie danych programu SQL Server 2017 na urządzeniu IoT krawędzi. 

W narzędziu wiersza polecenia połączenia z bazą danych: 

* Kontener systemu Windows
   ```cmd
   docker exec -it sql cmd
   ```

* Kontener systemu Linux
   ```bash
   docker exec -it sql bash
   ```

Otwórz narzędzie polecenia SQL: 

* Kontener systemu Windows
   ```cmd
   sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

* Kontener systemu Linux
   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

Zostaną wyświetlone dane: 

   ```sql
   SELECT * FROM MeasurementsDB.dbo.TemperatureMeasurements
   GO
   ```

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [konfigurowania programu SQL Server 2017 kontener obrazów na Docker](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker).

* Odwiedź stronę [repozytorium GitHub mssql docker](https://github.com/Microsoft/mssql-docker) zasobów, opinie i znane problemy.
