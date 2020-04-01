---
title: 'Samouczek: używanie funkcji Java w usłudze Azure Cosmos DB i centrach zdarzeń'
description: W tym samouczku pokazano, jak korzystać ze zdarzeń z centrum zdarzeń, aby aktualizacje w usłudze Azure Cosmos DB przy użyciu funkcji napisanej w języku Java.
author: KarlErickson
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: karler
ms.openlocfilehash: b6d7b2c60e777266b1cab578b8970c1fa1c6bc50
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77425327"
---
# <a name="tutorial-create-a-function-in-java-with-an-event-hub-trigger-and-an-azure-cosmos-db-output-binding"></a>Samouczek: Tworzenie funkcji w języku Java za pomocą wyzwalacza usługi Event Hub i powiązania danych wyjściowych usługi Azure Cosmos DB

W tym samouczku pokazano, jak używać usługi Azure Functions do tworzenia funkcji Java, która analizuje ciągły strumień danych temperatury i ciśnienia. Zdarzenia centrum zdarzeń, które reprezentują odczyty czujników wyzwalają funkcję. Funkcja przetwarza dane zdarzenia, a następnie dodaje wpisy stanu do usługi Azure Cosmos DB.

W tym samouczku:

> [!div class="checklist"]
> * Tworzenie i konfigurowanie zasobów platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.
> * Tworzenie i testowanie funkcji java, które współdziałają z tymi zasobami.
> * Wdrażaj swoje funkcje na platformie Azure i monitoruj je za pomocą usługi Application Insights.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, musisz mieć zainstalowany następujący problem:

* Zestaw [Java Developer Kit](https://aka.ms/azure-jdks), wersja 8
* Narzędzie [Apache Maven](https://maven.apache.org), wersja 3.0 lub nowsza
* [Narzędzie interfejsu wiersza polecenia platformy Azure,](/cli/azure/install-azure-cli) jeśli nie chcesz używać usługi Cloud Shell
* [Narzędzia podstawowe usług Azure Functions w](https://www.npmjs.com/package/azure-functions-core-tools) wersji 2.6.666 lub wyższej

> [!IMPORTANT]
> Aby `JAVA_HOME` ukończyć ten samouczek, należy ustawić zmienną środowiskową na lokalizację instalacji zestawu JDK.

Jeśli wolisz używać kodu dla tego samouczka bezpośrednio, zobacz [java-functions-eventhub-cosmosdb](https://github.com/Azure-Samples/java-functions-eventhub-cosmosdb) przykładowe repozytorium.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-azure-resources"></a>Tworzenie zasobów platformy Azure

W tym samouczku potrzebne są następujące zasoby:

* Grupa zasobów zawierająca inne zasoby
* Obszar nazw Centrów zdarzeń, Centrum zdarzeń i reguła autoryzacji
* Konto usługi Cosmos DB, baza danych i kolekcja
* Aplikacja funkcyjna i konto magazynu do hostowania

W poniższych sekcjach pokazano, jak utworzyć te zasoby przy użyciu interfejsu wiersza polecenia platformy Azure.

### <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Jeśli nie używasz usługi Cloud Shell, musisz użyć interfejsu wiersza polecenia platformy Azure lokalnie, aby uzyskać dostęp do konta. Użyj `az login` polecenia z monitu Bash, aby uruchomić środowisko logowania oparte na przeglądarce. Jeśli masz dostęp do więcej niż jednej subskrypcji `az account set --subscription` platformy Azure, ustaw wartość domyślną z identyfikatorem subskrypcji.

### <a name="set-environment-variables"></a>Ustawianie zmiennych środowiskowych

Następnie utwórz niektóre zmienne środowiskowe dla nazw i lokalizacji utworzonych zasobów. Użyj następujących poleceń, `<value>` zastępując symbole zastępcze wybranymi wartościami. Wartości powinny być zgodne z [regułami nazewnictwa i ograniczeniami dla zasobów platformy Azure](/azure/architecture/best-practices/resource-naming). Dla `LOCATION` zmiennej należy użyć jednej z `az functionapp list-consumption-locations` wartości wyprodukowanych przez polecenie.

```azurecli-interactive
RESOURCE_GROUP=<value>
EVENT_HUB_NAMESPACE=<value>
EVENT_HUB_NAME=<value>
EVENT_HUB_AUTHORIZATION_RULE=<value>
COSMOS_DB_ACCOUNT=<value>
STORAGE_ACCOUNT=<value>
FUNCTION_APP=<value>
LOCATION=<value>
```

Pozostała część tego samouczka używa tych zmiennych. Należy pamiętać, że te zmienne utrzymują się tylko przez czas trwania bieżącej sesji interfejsu wiersza polecenia platformy Azure lub usługi Cloud Shell. Należy ponownie uruchomić te polecenia, jeśli używasz innego lokalnego okna terminala lub limit czasu sesji usługi Cloud Shell.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Platforma Azure używa grup zasobów do zbierania wszystkich powiązanych zasobów na koncie. W ten sposób można wyświetlić je jako jednostkę i usunąć je za pomocą jednego polecenia, gdy skończysz z nimi.

Aby utworzyć grupę zasobów, użyj następującego polecenia:

```azurecli-interactive
az group create \
    --name $RESOURCE_GROUP \
    --location $LOCATION
```

### <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

Następnie utwórz obszar nazw usługi Azure Event Hubs, Centrum zdarzeń i regułę autoryzacji przy użyciu następujących poleceń:

```azurecli-interactive
az eventhubs namespace create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_NAMESPACE
az eventhubs eventhub create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_NAME \
    --namespace-name $EVENT_HUB_NAMESPACE \
    --message-retention 1
az eventhubs eventhub authorization-rule create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_AUTHORIZATION_RULE \
    --eventhub-name $EVENT_HUB_NAME \
    --namespace-name $EVENT_HUB_NAMESPACE \
    --rights Listen Send
```

Obszar nazw Centrum zdarzeń zawiera centrum zdarzeń rzeczywiste i jego regułę autoryzacji. Reguła autoryzacji umożliwia funkcjom wysyłanie wiadomości do centrum i nasłuchiwać odpowiednich zdarzeń. Jedna funkcja wysyła komunikaty reprezentujące dane telemetryczne. Inna funkcja nasłuchuje zdarzeń, analizuje dane zdarzeń i przechowuje wyniki w usłudze Azure Cosmos DB.

### <a name="create-an-azure-cosmos-db"></a>Tworzenie bazy danych Azure Cosmos DB

Następnie utwórz konto usługi Azure Cosmos DB, bazę danych i kolekcję przy użyciu następujących poleceń:

```azurecli-interactive
az cosmosdb create \
    --resource-group $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT
az cosmosdb database create \
    --resource-group-name $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT \
    --db-name TelemetryDb
az cosmosdb collection create \
    --resource-group-name $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT \
    --collection-name TelemetryInfo \
    --db-name TelemetryDb \
    --partition-key-path '/temperatureStatus'
```

Wartość `partition-key-path` partycje danych na `temperatureStatus` podstawie wartości każdego elementu. Klucz partycji umożliwia usługi Cosmos DB, aby zwiększyć wydajność, dzieląc dane na różne podzbiory, które mogą uzyskać dostęp niezależnie.

### <a name="create-a-storage-account-and-function-app"></a>Tworzenie konta magazynu i aplikacji funkcji

Następnie utwórz konto usługi Azure Storage, które jest wymagane przez usługę Azure Functions, a następnie utwórz aplikację funkcji. Użyj następujących poleceń:

```azurecli-interactive
az storage account create \
    --resource-group $RESOURCE_GROUP \
    --name $STORAGE_ACCOUNT \
    --sku Standard_LRS
az functionapp create \
    --resource-group $RESOURCE_GROUP \
    --name $FUNCTION_APP \
    --storage-account $STORAGE_ACCOUNT \
    --consumption-plan-location $LOCATION \
    --runtime java
```

Gdy `az functionapp create` polecenie tworzy aplikację funkcji, tworzy również zasób usługi Application Insights o tej samej nazwie. Aplikacja funkcji jest automatycznie konfigurowana `APPINSIGHTS_INSTRUMENTATIONKEY` z ustawieniem o nazwie, które łączy ją z usługą Application Insights. Po wdrożeniu funkcji na platformie Azure można wyświetlić dane telemetryczne aplikacji, zgodnie z opisem w dalszej części tego samouczka.

## <a name="configure-your-function-app"></a>Konfigurowanie aplikacji funkcji

Aplikacja funkcji będzie musiał uzyskać dostęp do innych zasobów, aby działać poprawnie. W poniższych sekcjach pokazano, jak skonfigurować aplikację funkcji, tak aby mogła działać na komputerze lokalnym.

### <a name="retrieve-resource-connection-strings"></a>Pobieranie ciągów połączeń zasobów

Użyj następujących poleceń, aby pobrać parametry połączeń magazynu, centrum zdarzeń i usługi Cosmos DB i zapisać je w zmiennych środowiskowych:

```azurecli-interactive
AZURE_WEB_JOBS_STORAGE=$( \
    az storage account show-connection-string \
        --name $STORAGE_ACCOUNT \
        --query connectionString \
        --output tsv)
echo $AZURE_WEB_JOBS_STORAGE
EVENT_HUB_CONNECTION_STRING=$( \
    az eventhubs eventhub authorization-rule keys list \
        --resource-group $RESOURCE_GROUP \
        --name $EVENT_HUB_AUTHORIZATION_RULE \
        --eventhub-name $EVENT_HUB_NAME \
        --namespace-name $EVENT_HUB_NAMESPACE \
        --query primaryConnectionString \
        --output tsv)
echo $EVENT_HUB_CONNECTION_STRING
COSMOS_DB_CONNECTION_STRING=$( \
    az cosmosdb keys list \
        --resource-group $RESOURCE_GROUP \
        --name $COSMOS_DB_ACCOUNT \
        --type connection-strings \
        --query connectionStrings[0].connectionString \
        --output tsv)
echo $COSMOS_DB_CONNECTION_STRING
```

Te zmienne są ustawione na wartości pobrane z poleceń interfejsu wiersza polecenia platformy Azure. Każde polecenie używa zapytania JMESPath do wyodrębnienia ciągu połączenia z zwracanego ładunku JSON. Parametry połączenia są również wyświetlane `echo` przy użyciu, dzięki czemu można potwierdzić, że zostały pomyślnie pobrane.

### <a name="update-your-function-app-settings"></a>Aktualizowanie ustawień aplikacji funkcji

Następnie użyj następującego polecenia, aby przenieść wartości ciągu połączenia do ustawień aplikacji na koncie usługi Azure Functions:

```azurecli-interactive
az functionapp config appsettings set \
    --resource-group $RESOURCE_GROUP \
    --name $FUNCTION_APP \
    --settings \
        AzureWebJobsStorage=$AZURE_WEB_JOBS_STORAGE \
        EventHubConnectionString=$EVENT_HUB_CONNECTION_STRING \
        CosmosDBConnectionString=$COSMOS_DB_CONNECTION_STRING
```

Zasoby platformy Azure zostały utworzone i skonfigurowane do poprawnego działania razem.

## <a name="create-and-test-your-functions"></a>Tworzenie i testowanie funkcji

Następnie utworzysz projekt na komputerze lokalnym, dodasz kod Java i przetestujesz go. Użyjesz poleceń, które współpracują z wtyczką usługi Azure Functions dla maven i podstawowymi narzędziami usługi Azure Functions. Funkcje będą uruchamiane lokalnie, ale będą korzystać z utworzonych zasobów w chmurze. Po przejściu funkcji pracy lokalnie, można użyć Maven wdrożyć je w chmurze i obserwować dane i analizy gromadzą.

Jeśli do utworzenia zasobów użyto usługi Cloud Shell, nie zostaniesz połączony z platformą Azure lokalnie. W takim przypadku `az login` użyj polecenia, aby uruchomić proces logowania oparty na przeglądarce. Następnie w razie potrzeby należy `az account set --subscription` ustawić domyślną subskrypcję z identyfikatorem subskrypcji. Na koniec uruchom następujące polecenia, aby odtworzyć niektóre zmienne środowiskowe na komputerze lokalnym. Zastąp `<value>` symbole zastępcze tymi samymi wartościami, które były wcześniej używane.

```bash
RESOURCE_GROUP=<value>
FUNCTION_APP=<value>
```

### <a name="create-a-local-functions-project"></a>Tworzenie projektu funkcji lokalnych

Użyj następującego polecenia Maven, aby utworzyć projekt funkcji i dodać wymagane zależności.

```bash
mvn archetype:generate --batch-mode \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype \
    -DappName=$FUNCTION_APP \
    -DresourceGroup=$RESOURCE_GROUP \
    -DgroupId=com.example \
    -DartifactId=telemetry-functions
```

To polecenie generuje kilka `telemetry-functions` plików wewnątrz folderu:

* Plik `pom.xml` do użytku z Maven
* `local.settings.json` Plik do przechowywania ustawień aplikacji do testowania lokalnego
* Plik, `host.json` który włącza pakiet rozszerzenia usług Azure Functions Extension Bundle, wymagany dla powiązania danych wyjściowych usługi Cosmos DB w funkcji analizy danych
* Plik `Function.java` zawierający domyślną implementację funkcji
* Kilka plików testowych, których ten samouczek nie potrzebuje

Aby uniknąć błędów kompilacji, należy usunąć pliki testowe. Uruchom następujące polecenia, aby przejść do nowego folderu projektu i usunąć folder testowy:

```bash
cd telemetry-functions
rm -r src/test
```

### <a name="retrieve-your-function-app-settings-for-local-use"></a>Pobieranie ustawień aplikacji funkcji do użytku lokalnego

Do testowania lokalnego projektu funkcji będzie potrzebować ciągów połączeń, które zostały dodane do aplikacji funkcji na platformie Azure wcześniej w tym samouczku. Użyj następującego polecenia Podstawowe narzędzia azure functions, które pobiera wszystkie ustawienia aplikacji `local.settings.json` funkcji przechowywane w chmurze i dodaje je do pliku:

```bash
func azure functionapp fetch-app-settings $FUNCTION_APP
```

### <a name="add-java-code"></a>Dodawanie kodu Java

Następnie otwórz `Function.java` plik i zastąp zawartość następującym kodem.

```java
package com.example;

import com.example.TelemetryItem.status;
import com.microsoft.azure.functions.annotation.Cardinality;
import com.microsoft.azure.functions.annotation.CosmosDBOutput;
import com.microsoft.azure.functions.annotation.EventHubOutput;
import com.microsoft.azure.functions.annotation.EventHubTrigger;
import com.microsoft.azure.functions.annotation.FunctionName;
import com.microsoft.azure.functions.annotation.TimerTrigger;
import com.microsoft.azure.functions.ExecutionContext;
import com.microsoft.azure.functions.OutputBinding;

public class Function {

    @FunctionName("generateSensorData")
    @EventHubOutput(
        name = "event",
        eventHubName = "", // blank because the value is included in the connection string
        connection = "EventHubConnectionString")
    public TelemetryItem generateSensorData(
        @TimerTrigger(
            name = "timerInfo",
            schedule = "*/10 * * * * *") // every 10 seconds
            String timerInfo,
        final ExecutionContext context) {

        context.getLogger().info("Java Timer trigger function executed at: "
            + java.time.LocalDateTime.now());
        double temperature = Math.random() * 100;
        double pressure = Math.random() * 50;
        return new TelemetryItem(temperature, pressure);
    }

    @FunctionName("processSensorData")
    public void processSensorData(
        @EventHubTrigger(
            name = "msg",
            eventHubName = "", // blank because the value is included in the connection string
            cardinality = Cardinality.ONE,
            connection = "EventHubConnectionString")
            TelemetryItem item,
        @CosmosDBOutput(
            name = "databaseOutput",
            databaseName = "TelemetryDb",
            collectionName = "TelemetryInfo",
            connectionStringSetting = "CosmosDBConnectionString")
            OutputBinding<TelemetryItem> document,
        final ExecutionContext context) {

        context.getLogger().info("Event hub message received: " + item.toString());

        if (item.getPressure() > 30) {
            item.setNormalPressure(false);
        } else {
            item.setNormalPressure(true);
        }

        if (item.getTemperature() < 40) {
            item.setTemperatureStatus(status.COOL);
        } else if (item.getTemperature() > 90) {
            item.setTemperatureStatus(status.HOT);
        } else {
            item.setTemperatureStatus(status.WARM);
        }

        document.setValue(item);
    }
}
```

Jak widać, ten plik zawiera `generateSensorData` dwie `processSensorData`funkcje i . Funkcja `generateSensorData` symuluje czujnik, który wysyła odczyty temperatury i ciśnienia do koncentratora zdarzeń. Wyzwalacz czasomierza uruchamia funkcję co 10 sekund, a powiązanie danych wyjściowych centrum zdarzeń wysyła wartość zwracaną do centrum zdarzeń.

Gdy centrum zdarzeń odbiera komunikat, generuje zdarzenie. Funkcja `processSensorData` jest uruchamiana po odebraniu zdarzenia. Następnie przetwarza dane zdarzenia i używa powiązania danych wyjściowych usługi Azure Cosmos DB do wysyłania wyników do usługi Azure Cosmos DB.

Dane używane przez te funkcje są `TelemetryItem`przechowywane przy użyciu klasy o nazwie , które należy zaimplementować. Utwórz nowy `TelemetryItem.java` plik wywoływany `Function.java` w tej samej lokalizacji i dodaj następujący kod:

```java
package com.example;

public class TelemetryItem {

    private String id;
    private double temperature;
    private double pressure;
    private boolean isNormalPressure;
    private status temperatureStatus;
    static enum status {
        COOL,
        WARM,
        HOT
    }

    public TelemetryItem(double temperature, double pressure) {
        this.temperature = temperature;
        this.pressure = pressure;
    }

    public String getId() {
        return id;
    }

    public double getTemperature() {
        return temperature;
    }

    public double getPressure() {
        return pressure;
    }

    @Override
    public String toString() {
        return "TelemetryItem={id=" + id + ",temperature="
            + temperature + ",pressure=" + pressure + "}";
    }

    public boolean isNormalPressure() {
        return isNormalPressure;
    }

    public void setNormalPressure(boolean isNormal) {
        this.isNormalPressure = isNormal;
    }

    public status getTemperatureStatus() {
        return temperatureStatus;
    }

    public void setTemperatureStatus(status temperatureStatus) {
        this.temperatureStatus = temperatureStatus;
    }
}
```

### <a name="run-locally"></a>Uruchamianie lokalnie

Teraz można tworzyć i uruchamiać funkcje lokalnie i zobaczyć dane są wyświetlane w usłudze Azure Cosmos DB.

Użyj następujących poleceń Maven, aby zbudować i uruchomić funkcje:

```bash
mvn clean package
mvn azure-functions:run
```

Po niektórych komunikatach kompilacji i uruchamiania zobaczysz dane wyjściowe podobne do następującego przykładu dla każdego uruchomienia funkcji:

```output
[10/22/19 4:01:30 AM] Executing 'Functions.generateSensorData' (Reason='Timer fired at 2019-10-21T21:01:30.0016769-07:00', Id=c1927c7f-4f70-4a78-83eb-bc077d838410)
[10/22/19 4:01:30 AM] Java Timer trigger function executed at: 2019-10-21T21:01:30.015
[10/22/19 4:01:30 AM] Function "generateSensorData" (Id: c1927c7f-4f70-4a78-83eb-bc077d838410) invoked by Java Worker
[10/22/19 4:01:30 AM] Executed 'Functions.generateSensorData' (Succeeded, Id=c1927c7f-4f70-4a78-83eb-bc077d838410)
[10/22/19 4:01:30 AM] Executing 'Functions.processSensorData' (Reason='', Id=f4c3b4d7-9576-45d0-9c6e-85646bb52122)
[10/22/19 4:01:30 AM] Event hub message received: TelemetryItem={id=null,temperature=32.728691307527015,pressure=10.122563042388165}
[10/22/19 4:01:30 AM] Function "processSensorData" (Id: f4c3b4d7-9576-45d0-9c6e-85646bb52122) invoked by Java Worker
[10/22/19 4:01:38 AM] Executed 'Functions.processSensorData' (Succeeded, Id=1cf0382b-0c98-4cc8-9240-ee2a2f71800d)
```

Następnie można przejść do [witryny Azure portal](https://portal.azure.com) i przejść do konta usługi Azure Cosmos DB. Wybierz **pozycję Eksplorator danych**, rozwiń pozycję **TelemetryInfo**, a następnie wybierz pozycję **Elementy,** aby wyświetlić dane po ich nadejściu.

![Eksplorator danych bazy danych usługi Cosmos](media/functions-event-hub-cosmos-db/data-explorer.png)

## <a name="deploy-to-azure-and-view-app-telemetry"></a>Wdrażanie na platformie Azure i wyświetlanie danych telemetrycznych aplikacji

Na koniec można wdrożyć aplikację na platformie Azure i sprawdzić, czy nadal działa tak samo, jak lokalnie.

Wdrażanie projektu na platformie Azure przy użyciu następującego polecenia:

```bash
mvn azure-functions:deploy
```

Twoje funkcje są teraz uruchamiane na platformie Azure i nadal gromadzą dane w usłudze Azure Cosmos DB. Wdrożoną aplikację funkcji można wyświetlić w portalu Azure i wyświetlić dane telemetryczne aplikacji za pośrednictwem połączonego zasobu usługi Application Insights, jak pokazano na poniższych zrzutach ekranu:

**Transmisja na żywo:**

![Strumień metryk na żywo usługi Application Insights na żywo](media/functions-event-hub-cosmos-db/application-insights-live-metrics-stream.png)

**Wydajności:**

![Blok Wydajności usługi Application Insights](media/functions-event-hub-cosmos-db/application-insights-performance.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu pracy z zasobami platformy Azure, które zostały utworzone w tym samouczku, można je usunąć za pomocą następującego polecenia:

```azurecli-interactive
az group delete --name $RESOURCE_GROUP
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak utworzyć funkcję platformy Azure, która obsługuje zdarzenia centrum zdarzeń i aktualizuje usługę Cosmos DB. Aby uzyskać więcej informacji, zobacz [przewodnik dla deweloperów języka Azure Functions Java](/azure/azure-functions/functions-reference-java). Aby uzyskać informacje na temat używanych adnotacji, zobacz odwołanie do [funkcji com.microsoft.azure.functions.annotation.](/java/api/com.microsoft.azure.functions.annotation)

W tym samouczku użyto zmiennych środowiskowych i ustawień aplikacji do przechowywania wpisów tajnych, takich jak parametry połączenia. Aby uzyskać informacje na temat przechowywania tych wpisów tajnych w usłudze Azure Key Vault, zobacz [Używanie odwołań do usługi App Service i usługi Azure Functions.](/azure/app-service/app-service-key-vault-references)

Następnie dowiedz się, jak używać usługi Azure Pipelines CI/CD do zautomatyzowanego wdrażania:

> [!div class="nextstepaction"]
> [Tworzenie i wdrażanie języka Java w usłudze Azure Functions](/azure/devops/pipelines/ecosystems/java-function)
