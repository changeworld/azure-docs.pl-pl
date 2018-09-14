---
title: Przechowywanie danych przy użyciu modułu SQL usługi Azure IoT Edge | Microsoft Docs
description: Dowiedz się, jak przechowywać dane lokalnie na urządzeniu usługi IoT Edge przy użyciu modułu programu SQL Server
services: iot-edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 08/30/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 2b393a5b60ba534fba8115ab3ef0f35a26ad3ed4
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2018
ms.locfileid: "43300357"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>Samouczek: przechowywanie danych na brzegu sieci przy użyciu baz danych programu SQL Server

Usługi Azure IoT Edge i programu SQL Server można używać do przechowywania danych i wykonywania zapytań dotyczących danych na brzegu sieci. Usługa Azure IoT Edge ma wbudowane podstawowe funkcje magazynu, które przechowują w pamięci podręcznej komunikaty, jeśli urządzenie przejdzie w tryb offline, a następnie przekazują je dalej po ponownym ustanowieniu połączenia. Możesz jednak potrzebować bardziej zaawansowanych możliwości magazynu, takich jak możliwość lokalnego wykonywania zapytań o dane. Dzięki włączeniu lokalnych baz danych urządzenia usługi IoT Edge mogą wykonywać bardziej złożone obliczenia bez konieczności utrzymywania połączenia z usługą IoT Hub. Na przykład serwisant może lokalnie na maszynie zwizualizować dane z czujników z ostatnich kilku dni, mimo że dane są przekazywane do chmury raz w miesiącu, aby pomóc ulepszyć model uczenia maszynowego.

Ten artykuł zawiera instrukcje dotyczące wdrażania bazy danych programu SQL Server na urządzeniu usługi IoT Edge. Usługa Azure Functions, uruchomiona na urządzeniu usługi IoT Edge, strukturyzuje dane przychodzące, a następnie wysyła je do bazy danych. Kroki opisane w tym artykule można zastosować również do innych baz danych, które działają w kontenerach, na przykład MySQL lub PostgreSQL.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności: 

> [!div class="checklist"]
> * Używanie programu Visual Studio Code do tworzenia funkcji platformy Azure
> * Wdrażanie bazy danych SQL na urządzeniu usługi IoT Edge
> * Używanie programu Visual Studio Code do kompilowania modułów i wdrażania ich na urządzeniu usługi IoT Edge
> * Wyświetlanie wygenerowanych danych

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Urządzenie usługi Azure IoT Edge:

* Jako urządzenia brzegowego możesz użyć maszyny deweloperskiej albo maszyny wirtualnej, postępując zgodnie z instrukcjami w przewodniku Szybki start dla urządzeń z systemem [Linux](quickstart-linux.md) lub [Windows](quickstart.md).

Zasoby w chmurze:

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Standardowa na platformie Azure. 

Zasoby do programowania:

* [Program Visual Studio Code](https://code.visualstudio.com/) 
* Rozszerzenie [C# for Visual Studio Code (obsługiwane przez technologię OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) dla programu Visual Studio Code. 
* Rozszerzenie usługi[Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) dla programu Visual Studio Code 
* [Zestaw .NET Core 2.1 SDK](https://www.microsoft.com/net/download). 
* [Program Docker CE](https://docs.docker.com/install/). 

## <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów
W tym samouczku rozszerzenie usługi Azure IoT Edge dla programu VS Code zostanie użyte do zbudowania modułu i utworzenia **obrazu kontenera** na podstawie plików. Następnie ten obraz zostanie wypchnięty do **rejestru**, w którym obrazy są przechowywane i zarządzane. Na koniec obraz zostanie wdrożony z rejestru w celu uruchomienia na urządzeniu usługi IoT Edge.  

Na potrzeby tego samouczka możesz użyć dowolnego rejestru zgodnego z platformą Docker. Dwie popularne usługi rejestru Docker dostępne w chmurze to [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) i [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). W tym samouczku używana jest usługa Azure Container Registry. 

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz kolejno pozycje **Utwórz zasób** > **Kontenery** > **Container Registry**.

    ![tworzenie rejestru kontenerów](./media/tutorial-deploy-function/create-container-registry.png)

2. Nadaj rejestrowi nazwę i wybierz subskrypcję.
3. Dla grupy zasobów zaleca się użycie nazwy tej samej grupy zasobów, która zawiera centrum IoT Hub. Dzięki umieszczeniu wszystkich zasobów w tej samej grupie można nimi zarządzać razem. Na przykład usunięcie grupy zasobów używanej do testowania spowoduje usunięcie wszystkich zasobów testowych zawartych w tej grupie. 
4. Ustaw jednostkę SKU na wartość **Podstawowa** i ustaw przełącznik **Administrator** na pozycję **Włącz**. 
5. Kliknij pozycję **Utwórz**.
6. Po utworzeniu kontenera rejestru przejdź do niego i wybierz pozycję **Klucze dostępu**. 
7. Skopiuj wartości w polach **Serwer logowania**, **Nazwa użytkownika** i **Hasło**. Te wartości zostaną wykorzystane później w samouczku. 

## <a name="create-a-function-project"></a>Tworzenie projektu funkcji

Aby wysyłać dane do bazy danych, potrzebujesz modułu, który może poprawnie tworzyć struktury danych, a następnie przechowywać je w tabeli. 

W następujących krokach przedstawiono sposób tworzenia funkcji usługi IoT Edge przy użyciu programu Visual Studio Code i rozszerzenia usługi Azure IoT Edge.

1. Otwórz program Visual Studio Code.
2. Otwórz zintegrowany terminal programu VS Code, wybierając pozycję **View (Widok)** > **Terminal**.
3. Otwórz paletę poleceń programu VS Code, wybierając pozycję **Widok** > **Paleta poleceń**.
4. W palecie poleceń wpisz i uruchom polecenie **Azure: zaloguj**, a następnie postępuj zgodnie z instrukcjami, aby zalogować się na koncie platformy Azure. Jeśli już się zalogowano, można pominąć ten krok.
3. W palecie poleceń wpisz i uruchom polecenie **Azure IoT Edge: nowe rozwiązanie usługi IoT Edge**. W palecie poleceń podaj następujące informacje, aby utworzyć rozwiązanie: 
   1. Wybierz folder, w którym chcesz utworzyć rozwiązanie. 
   2. Podaj nazwę rozwiązania lub zaakceptuj nazwę domyślną **EdgeSolution**.
   3. Wybierz szablon **Azure Functions — C#** jako szablon modułu. 
   4. Nadaj modułowi nazwę **sqlFunction**. 
   5. Określ usługę Azure Container Registry utworzoną w poprzedniej sekcji jako repozytorium obrazów dla pierwszego modułu. Zastąp ciąg **localhost:5000** skopiowaną wartością serwera logowania. Ostateczny ciąg będzie wyglądał następująco: **\<nazwa rejestru\>.azurecr.io/sqlFunction**.

4. W oknie programu VS Code zostanie załadowany obszar roboczy rozwiązania usługi IoT Edge. Zawiera on foldery **modules** i **.vscode** oraz plik szablonu manifestu wdrożenia. Otwórz plik **modules** > **sqlFunction** > **EdgeHubTrigger-Csharp** > **run.csx**.

5. Zastąp zawartość pliku następującym kodem:

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

6. W wierszu 24 zastąp ciąg **\<sql connection string\>** następującym ciągiem. Właściwość **Data Source** odwołuje się do nazwy kontenera programu SQL Server, który zostanie utworzony z nazwą **SQL** w następnej sekcji. 

   ```C#
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

7. Zapisz plik **run.csx**. 

## <a name="add-a-sql-server-container"></a>Dodawanie kontenera programu SQL Server

[Manifest wdrożenia](module-composition.md) deklaruje moduły, które środowisko uruchomieniowe usługi IoT Edge zainstaluje na urządzeniu usługi IoT Edge. Kod na potrzeby utworzenia dostosowanego modułu funkcji został dodany w poprzedniej sekcji, ale moduł programu SQL Server jest już skompilowany. Wystarczy poinstruować środowisko uruchomieniowe usługi IoT Edge, że ma dołączyć ten moduł, a następnie skonfigurować go na urządzeniu. 

1. W eksploratorze programu Visual Studio Code otwórz plik **deployment.template.json**. 
2. Znajdź sekcję **moduleContent.$edgeAgent.properties.desired.modules**. Powinny być w niej wyświetlane dwa moduły: moduł **tempSensor**, który generuje symulowane dane, i moduł **sqlFunction**.
3. Jeśli używasz kontenerów systemu Windows, zmodyfikuj sekcję **sqlFunction.settings.image**.
    ```json
    "image": "${MODULES.sqlFunction.windows-amd64}"
    ```
4. Dodaj następujący kod, aby zadeklarować trzeci moduł. Dodaj przecinek po sekcji sqlFunction i wstaw:

   ```json
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
   ```

   Oto przykład w przypadku jakichkolwiek problemów z dodawaniem elementu JSON. ![Dodawanie kontenera programu SQL Server](./media/tutorial-store-data-sql-server/view_json_sql.png)

5. W zależności od typu kontenerów platformy Docker na urządzeniu usługi IoT Edge zaktualizuj parametry **sql.settings** przy użyciu następującego kodu:

   * Kontenery systemu Windows:

      ```json
      "image": "microsoft/mssql-server-windows-developer",
      "createOptions": "{\"Env\": [\"ACCEPT_EULA=Y\",\"SA_PASSWORD=Strong!Passw0rd\"],\"HostConfig\": {\"Mounts\": [{\"Target\": \"C:\\\\mssql\",\"Source\": \"sqlVolume\",\"Type\": \"volume\"}],\"PortBindings\": {\"1433/tcp\": [{\"HostPort\": \"1401\"}]}}}"
      ```

   * Kontenery systemu Linux:

      ```json
      "image": "microsoft/mssql-server-linux:2017-latest",
      "createOptions": "{\"Env\": [\"ACCEPT_EULA=Y\",\"MSSQL_SA_PASSWORD=Strong!Passw0rd\"],\"HostConfig\": {\"Mounts\": [{\"Target\": \"/var/opt/mssql\",\"Source\": \"sqlVolume\",\"Type\": \"volume\"}],\"PortBindings\": {\"1433/tcp\": [{\"HostPort\": \"1401\"}]}}}"
      ```

   >[!Tip]
   >Za każdym razem, gdy tworzysz kontener programu SQL Server w środowisku produkcyjnym, zaleca się [zmienić domyślne hasło administratora systemu](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker#change-the-sa-password).

6. Zapisz plik **deployment.template.json**.

## <a name="build-your-iot-edge-solution"></a>Kompilowanie rozwiązania usługi IoT Edge

W poprzednich sekcjach utworzono rozwiązanie z jednym modułem, a następnie dodano drugi do szablonu manifestu wdrożenia. Teraz musisz skompilować rozwiązanie, utworzyć obrazy kontenerów dla modułów i wypchnąć obrazy do rejestru kontenerów. 

1. W pliku env podaj środowisku uruchomieniowemu usługi IoT Edge swoje poświadczenia rejestru, aby mogło uzyskać dostęp do obrazów modułów. Znajdź sekcje **CONTAINER_REGISTRY_USERNAME** oraz **CONTAINER_REGISTRY_PASSWORD** i wstaw swoje poświadczenia po znaku równości: 

   ```env
   CONTAINER_REGISTRY_USERNAME_yourContainerReg=<username>
   CONTAINER_REGISTRY_PASSWORD_yourContainerReg=<password>
   ```
2. Zapisz plik env.
3. Zaloguj się do rejestru kontenerów w programie Visual Studio Code, aby mógł on wypchnąć obrazy do rejestru. Użyj tych samych poświadczeń, które zostały dodane do pliku env. W zintegrowanym terminalu wprowadź następujące polecenie:

    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```
    Zostanie wyświetlony monit o podanie hasła. Wklej hasło w monicie i naciśnij klawisz **Enter**.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

4. W eksploratorze programu VS Code kliknij prawym przyciskiem myszy plik **deployment.template.json** i wybierz polecenie **Skompiluj i wypchnij rozwiązanie usługi IoT Edge**. 

## <a name="deploy-the-solution-to-a-device"></a>Wdrażanie rozwiązania na urządzeniu

Moduły można ustawić na urządzeniu za pomocą usługi IoT Hub, ale dostęp do usługi IoT Hub i urządzeń można również uzyskać za pomocą programu Visual Studio Code. W tej sekcji skonfigurujesz dostęp do usługi IoT Hub, a następnie użyjesz programu VS Code do wdrożenia rozwiązania na urządzeniu usługi IoT Edge. 

1. W palecie poleceń programu VS Code wybierz polecenie **Azure IoT Hub: Select IoT Hub (Azure IoT Hub: wybierz centrum IoT Hub)**.
2. Postępuj zgodnie z monitami, aby zalogować się na koncie platformy Azure. 
3. W palecie poleceń wybierz subskrypcję platformy Azure, a następnie wybierz centrum IoT Hub. 
4. W eksploratorze programu VS rozwiń sekcję **Urządzenia usługi Azure IoT Hub**. 
5. Kliknij prawym przyciskiem myszy urządzenie, które ma być urządzeniem docelowym wdrożenia, i wybierz polecenie **Create deployment for single device** (Utwórz wdrożenie dla pojedynczego urządzenia). 
6. W Eksploratorze plików przejdź do folderu **config** wewnątrz rozwiązania i wybierz plik **deployment.json**. Kliknij pozycję **Wybierz manifest wdrożenia usługi Edge**. 

Jeśli wdrożenie zakończy się pomyślnie, komunikat potwierdzenia zostanie wydrukowany w danych wyjściowych programu VS Code. Możesz również sprawdzić, czy wszystkie moduły są wdrożone i uruchomione na urządzeniu. 

Na urządzeniu usługi IoT Edge uruchom następujące polecenie, aby wyświetlić stan modułów. Może to potrwać kilka minut.

   ```PowerShell
   iotedge list
   ```

## <a name="create-the-sql-database"></a>Tworzenie bazy danych SQL

Po zastosowaniu manifestu wdrożenia do urządzenia uzyskujesz trzy uruchomione moduły. Moduł tempSensor generuje symulowane dane środowiska. Moduł sqlFunction pobiera dane i formatuje je na potrzeby bazy danych. 

Ta sekcja zawiera opis konfigurowania bazy danych SQL do przechowywania danych dotyczących temperatury. 

1. W narzędziu wiersza polecenia nawiąż połączenie z bazą danych. 
   * Kontener systemu Windows:
   
      ```cmd
      docker exec -it sql cmd
      ```
    
   * Kontener systemu Linux: 

      ```bash
      docker exec -it sql bash
      ```

2. Otwórz narzędzie polecenia SQL.
   * Kontener systemu Windows:

      ```cmd
      sqlcmd -S localhost -U SA -P "Strong!Passw0rd"
      ```

   * Kontener systemu Linux: 

      ```bash
      /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
      ```

3. Utwórz bazę danych: 

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

   ![Wyświetlanie danych lokalnych](./media/tutorial-store-data-sql-server/view-data.png)



## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie. Możesz także nadal używać tego samego urządzenia usługi IoT Edge jako urządzenia testowego. 

W przeciwnym razie możesz usunąć konfigurację lokalną i zasoby platformy Azure utworzone podczas pracy z tym artykułem, aby uniknąć naliczania opłat. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]



## <a name="next-steps"></a>Następne kroki

W tym samouczku został utworzony moduł usługi Azure Functions zawierający kod służący do filtrowania nieprzetworzonych danych wygenerowanych przez urządzenie usługi IoT Edge. Gdy wszystko będzie gotowe do tworzenia własnych modułów, możesz dowiedzieć się więcej na temat tego, jak [programować usługę Azure Functions za pomocą usługi Azure IoT Edge dla programu Visual Studio Code](how-to-develop-csharp-function.md). 

Kontynuuj pracę z kolejnymi samouczkami, aby dowiedzieć się o innych metodach, za pomocą których usługa Azure IoT Edge może ułatwiać przekształcanie danych w analizy biznesowe na brzegu sieci.

> [!div class="nextstepaction"]
> [Znajdowanie średnich przy użyciu okna ruchomego w usłudze Azure Stream Analytics](tutorial-deploy-stream-analytics.md)
