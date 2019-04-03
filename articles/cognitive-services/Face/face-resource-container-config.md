---
title: Konfigurowanie kontenerów
titlesuffix: Face - Azure Cognitive Services
description: Ustawienia konfiguracji dla kontenerów.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 73fc17ae5c65cd1a6ce47a18cbe17e6c338b7aaf
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58882127"
---
# <a name="configure-face-docker-containers"></a>Konfigurowanie kontenerów platformy Docker twarzy

**Twarzy** środowisko uruchomieniowe kontenera jest konfigurowana przy użyciu `docker run` argumenty polecenia. Ten kontener ma wymagane ustawienia, wraz z kilku ustawień opcjonalnych. Kilka [przykłady](#example-docker-run-commands) polecenia są dostępne. Ustawienia specyficzne dla kontenera są ustawienia rozliczeń. 

## <a name="configuration-settings"></a>Ustawienia konfiguracji

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), I [ `Eula` ](#eula-setting) ustawienia są używane razem. Ponadto należy podać prawidłowe wartości dla wszystkich trzech ich; w przeciwnym razie Nie można uruchomić kontener. Aby uzyskać więcej informacji na temat tworzenia wystąpienia kontenera za pomocą tych ustawień konfiguracji, zobacz [rozliczeń](face-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>Ustawienie konfiguracji ApiKey

`ApiKey` Ustawienie umożliwia określenie klucza zasobów platformy Azure używane do śledzenia informacji rozliczeniowych dla kontenera. Należy określić wartość dla ApiKey i wartość musi być prawidłowy klucz dla _twarzy_ zasób określony dla [ `Billing` ](#billing-configuration-setting) ustawienia konfiguracji.

To ustawienie można znaleźć w tym miejscu następujące:

* Azure Portal: **Tarczy** zarządzanie zasobami w obszarze **kluczy**

## <a name="applicationinsights-setting"></a>Ustawienie dotycząca usługi Application Insights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Ustawienie konfiguracji rozliczeń

`Billing` Ustawienie określa identyfikator URI punktu końcowego z _twarzy_ zasobów na platformie Azure używane do pomiarów informacji rozliczeniowych dla kontenera. Należy określić wartość dla tego ustawienia konfiguracji, a wartość musi być prawidłowy identyfikator URI punktu końcowego dla _twarzy_ zasobów na platformie Azure. Kontener raportów użycia dotyczących co 10 do 15 minut.

To ustawienie można znaleźć w tym miejscu następujące:

* Azure Portal: **Tarczy** Przegląd, etykietą `Endpoint`

|Wymagane| Name (Nazwa) | Typ danych | Opis |
|--|------|-----------|-------------|
|Yes| `Billing` | Ciąg | Identyfikator URI punktu końcowego rozliczeń<br><br>Przykład:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0` |

<!-- specific to face only -->

## <a name="cloudai-configuration-settings"></a>Ustawienia konfiguracji CloudAI

Ustawienia konfiguracji w `CloudAI` sekcji Podaj opcji specyficznych dla kontenera unikatowe dla kontenera. Następujące ustawienia i obiekty, które są obsługiwane dla kontenera twarzy w `CloudAI` sekcji

| Name (Nazwa) | Typ danych | Opis |
|------|-----------|-------------|
| `Storage` | Obiekt | Scenariusz magazynowania używane przez kontener twarzy. Aby uzyskać więcej informacji na temat scenariuszy magazynowania i skojarzone ustawienia `Storage` obiektu, zobacz [scenariuszu miejsca do magazynowania](#storage-scenario-settings) |

### <a name="storage-scenario-settings"></a>Ustawienia scenariusza magazynu

Kontener twarzy przechowuje, obiektów blob, pamięci podręcznej, metadane i dane w kolejce, w zależności od tego, co jest magazynowana. Na przykład indeksy szkolenia i wyniki dla osób dużych grup są przechowywane jako dane obiektów blob. Kontener twarzy zawiera dwa scenariusze innego magazynu podczas interakcji z i przechowywania tych typów danych:

* Memory (Pamięć)  
  Wszystkie cztery typy danych są przechowywane w pamięci. Nie są rozpowszechniane, nie są one trwałych. Jeśli kontener twarzy jest zatrzymana lub usunięta, wszystkie dane w magazynie dla tego kontenera zostanie zniszczony.  
  Jest to scenariusz magazynowania domyślnego kontenera twarzy.
* Azure  
  Kontener twarzy korzysta z usługi Azure Storage i Azure Cosmos DB, aby rozdystrybuować te cztery rodzaje danych z magazynu trwałego. Dane obiektów blob i kolejek jest obsługiwane przez usługę Azure Storage. Metadane i pamięci podręcznej danych jest obsługiwane przez usługę Azure Cosmos DB. Jeśli kontener twarzy jest zatrzymana lub usunięta, wszystkie dane w magazynie dla tego kontenera pozostaje przechowywanych w usłudze Azure Storage i Azure Cosmos DB.  
  Zasoby używane przez scenariusza magazynu platformy Azure mają następujące wymagania dodatkowe
  * Zasób usługi Azure Storage, należy użyć StorageV2 rodzaj konta
  * Zasób usługi Azure Cosmos DB, należy użyć interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB

Scenariusze usługi storage i skojarzone ustawienia są zarządzane przez `Storage` obiekt w obszarze `CloudAI` sekcji konfiguracji. Następujące ustawienia konfiguracji są dostępne w `Storage` obiektu:

| Name (Nazwa) | Typ danych | Opis |
|------|-----------|-------------|
| `StorageScenario` | Ciąg | Scenariusz magazynowania obsługiwane przez kontener. Dostępne są następujące wartości<br/>`Memory` — Wartość domyślna. Kontener korzysta z magazynu nietrwałe, nierozpowszechniony i w pamięci, do użycia z jednym węzłem, tymczasowy. Jeśli kontener jest zatrzymana lub usunięta, jest niszczony magazynu dla tego kontenera.<br/>`Azure` -Container używa zasobów platformy Azure do przechowywania. Jeśli kontener jest zatrzymana lub usunięta, jest trwały magazyn dla tego kontenera.|
| `ConnectionStringOfAzureStorage` | Ciąg | Parametry połączenia dla zasobu usługi Azure Storage używany przez kontener.<br/>To ustawienie ma zastosowanie tylko wtedy, gdy `Azure` jest określona dla `StorageScenario` ustawienia konfiguracji. |
| `ConnectionStringOfCosmosMongo` | Ciąg | Parametry połączenia bazy danych MongoDB dla zasobu usługi Azure Cosmos DB używane przez kontener.<br/>To ustawienie ma zastosowanie tylko wtedy, gdy `Azure` jest określona dla `StorageScenario` ustawienia konfiguracji. |

Na przykład następujące polecenie Określa scenariusza magazynu platformy Azure i zawiera przykładowe parametry połączenia dla zasobów usługi Azure Storage i Cosmos DB używane do przechowywania danych dla kontenera twarzy.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

Scenariusz magazynowania są obsługiwane niezależnie od instalacji i wyjściowych instaluje. Można określić kombinacji tych funkcji do jednego kontenera. Na przykład następujące polecenie definiuje instalowania powiązania platformy Docker, aby `D:\Output` folderu na komputerze hosta jako dane wyjściowe instalacji, następnie tworzy wystąpienie kontenera z twarzy obraz kontenera, zapisywanie plików dziennika w formacie JSON w celu instalacji danych wyjściowych. Polecenie również określa scenariusza magazynu platformy Azure i zawiera przykładowe parametry połączenia dla zasobów usługi Azure Storage i Cosmos DB używane do przechowywania danych dla kontenera twarzy.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Disk:Format=json CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

## <a name="eula-setting"></a>Umowa licencyjna EULA ustawienie

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Ustawienia Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Ustawienia poświadczeń serwera proxy HTTP

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Ustawienia rejestrowania
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Ustawienia instalacji

Użyj powiązania instaluje do odczytu i zapisu danych do i z kontenera. Można określić instalacji danych wejściowych lub wyjściowych instalacji, określając `--mount` opcji [platformy docker, uruchom](https://docs.docker.com/engine/reference/commandline/run/) polecenia.

Kontenery twarzy nie używaj danych wejściowych lub danych wyjściowych instaluje do przechowywania szkolenie i usługi danych. 

Dokładna składnia lokalizację instalacji hosta różni się zależnie od systemu operacyjnego hosta. Ponadto [komputerze-hoście](face-how-to-install-containers.md#the-host-computer)w lokalizacji instalacji może być niedostępna z powodu konfliktu między uprawnienia użyte dla konta usługi Docker i hosta instalacji uprawnienia do lokalizacji. 

|Optional (Opcjonalność)| Name (Nazwa) | Typ danych | Opis |
|-------|------|-----------|-------------|
|Niedozwolone| `Input` | String | Kontenery twarzy nie należy używać tego.|
|Optional (Opcjonalność)| `Output` | String | Miejsce docelowe instalacji danych wyjściowych. Wartość domyślna to `/output`. Jest to Lokalizacja dzienników. W tym dzienniki kontenerów. <br><br>Przykład:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Przykład platformy docker, Uruchom polecenia 

W poniższych przykładach używane ustawienia konfiguracji, aby zilustrować, jak pisać i użyj `docker run` poleceń.  Po uruchomieniu kontenera będzie działać do momentu [zatrzymać](face-how-to-install-containers.md#stop-the-container) go.

* **Znak kontynuacji wiersza**: Platformy Docker w poniższych sekcjach użyto ukośnika, `\`, jako znak kontynuacji wiersza. Zamień lub Usuń ten na podstawie wymagań systemu operacyjnego hosta. 
* **Kolejność argumentów**: Nie należy zmieniać kolejność argumentów, jeśli nie znasz bardzo kontenerów platformy Docker.

Zastąp {_argument_name_} własnymi wartościami:

| Symbol zastępczy | Wartość | Format lub przykład |
|-------------|-------|---|
|{BILLING_KEY} | Klucz punktu końcowego zasobu twarzy. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT_URI} | Rozliczeń wartości punktu końcowego, w tym regionie.|`https://westcentralus.api.cognitive.microsoft.com/face/v1.0`|

> [!IMPORTANT]
> `Eula`, `Billing`, I `ApiKey` opcje muszą być określone w celu uruchomienia kontenera; w przeciwnym razie nie uruchamia się kontener.  Aby uzyskać więcej informacji, zobacz [rozliczeń](face-how-to-install-containers.md#billing).
> Wartość ApiKey **klucz** ze strony klucze w usłudze Azure Resource twarzy. 

## <a name="face-container-docker-examples"></a>Kontener platformy Docker przykłady twarzy

Są w poniższych przykładach platformy Docker dla kontenera twarzy. 

### <a name="basic-example"></a>Podstawowy przykład 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} \
  ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example"></a>Przykład rejestrowania 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Kolejne kroki

* Przegląd [sposobu instalowania i uruchamiania kontenerów](face-how-to-install-containers.md)
