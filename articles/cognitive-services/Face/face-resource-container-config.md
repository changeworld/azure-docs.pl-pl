---
title: Konfigurowanie kontenerów — ściana
titleSuffix: Azure Cognitive Services
description: Środowisko wykonawcze kontenera ściany jest `docker run` konfigurowane przy użyciu argumentów polecenia. Istnieją zarówno ustawienia wymagane, jak i opcjonalne.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 2f608843e27b79d02697df8e2a7f2aba6695e10a
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878429"
---
# <a name="configure-face-docker-containers"></a>Konfigurowanie kontenerów platformy Face Docker

Środowisko wykonawcze kontenera **ściany** jest `docker run` konfigurowane przy użyciu argumentów polecenia. Ten kontener ma kilka wymaganych ustawień, wraz z kilkoma ustawieniami opcjonalnymi. Dostępnych jest kilka [przykładów](#example-docker-run-commands) polecenia. Ustawienia specyficzne dla kontenera to ustawienia rozliczeń. 

## <a name="configuration-settings"></a>Ustawienia konfiguracji

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Ustawienia [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting)i [`Eula`](#eula-setting) ustawienia są używane razem i należy podać prawidłowe wartości dla wszystkich trzech z nich; w przeciwnym razie kontener nie zostanie uruchomiony. Aby uzyskać więcej informacji na temat używania tych ustawień konfiguracji do tworzenia wystąpienia kontenera, zobacz [Rozliczenia](face-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>Ustawienie konfiguracji apikey

Ustawienie `ApiKey` określa klucz zasobów platformy Azure używany do śledzenia informacji rozliczeniowych dla kontenera. Należy określić wartość dla apikey i wartość musi być prawidłowy klucz [`Billing`](#billing-configuration-setting) dla zasobu usług Cognitive _Services_ określonych dla ustawienia konfiguracji.

To ustawienie można znaleźć w następującym miejscu:

* Portal Azure: Zarządzanie zasobami **usług Cognitive Services** w obszarze **Klucze**

## <a name="applicationinsights-setting"></a>Ustawienie ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Ustawienie konfiguracji rozliczeń

Ustawienie `Billing` określa identyfikator URI punktu końcowego zasobu _usług Cognitive Services_ na platformie Azure używanego do pomiaru informacji rozliczeniowych dla kontenera. Należy określić wartość dla tego ustawienia konfiguracji, a wartość musi być prawidłowym identyfikatorem URI punktu końcowego dla zasobu _usług Cognitive Services_ na platformie Azure. Kontener raportuje użycie co 10 do 15 minut.

To ustawienie można znaleźć w następującym miejscu:

* Portal Azure: Omówienie **usług Cognitive Services,** oznaczone etykietą`Endpoint`

Pamiętaj, aby dodać _Routing twarzy_ do identyfikatora URI punktu końcowego, jak pokazano w przykładzie. 

|Wymagany| Nazwa | Typ danych | Opis |
|--|------|-----------|-------------|
|Tak| `Billing` | Ciąg | Identyfikator URI punktu końcowego rozliczeń. Aby uzyskać więcej informacji na temat uzyskiwania identyfikatora URI rozliczeń, zobacz [zbieranie wymaganych parametrów](face-how-to-install-containers.md#gathering-required-parameters). Aby uzyskać więcej informacji i pełną listę regionalnych punktów końcowych, zobacz [Niestandardowe nazwy poddomen dla usług Cognitive Services](../cognitive-services-custom-subdomains.md). |

<!-- specific to face only -->

## <a name="cloudai-configuration-settings"></a>Ustawienia konfiguracji CloudAI

Ustawienia konfiguracji w `CloudAI` sekcji zapewniają opcje specyficzne dla kontenera unikatowe dla kontenera. Następujące ustawienia i obiekty są obsługiwane dla `CloudAI` kontenera Ściana w sekcji

| Nazwa | Typ danych | Opis |
|------|-----------|-------------|
| `Storage` | Obiekt | Scenariusz magazynowania używany przez kontener Face. Aby uzyskać więcej informacji na temat scenariuszy magazynowania i skojarzonych ustawień `Storage` obiektu, zobacz Ustawienia [scenariusza magazynu](#storage-scenario-settings) |

### <a name="storage-scenario-settings"></a>Ustawienia scenariusza magazynu

Kontener Face przechowuje dane obiektów blob, cache, metadanych i kolejek, w zależności od tego, co jest przechowywane. Na przykład indeksy szkoleniowe i wyniki dla dużej grupy osób są przechowywane jako dane obiektów blob. Kontener Face udostępnia dwa różne scenariusze magazynu podczas interakcji z tymi typami danych i przechowywania ich:

* Memory (Pamięć)  
  Wszystkie cztery typy danych są przechowywane w pamięci. Nie są one rozpowszechniane, ani nie są trwałe. Jeśli kontener Face zostanie zatrzymany lub usunięty, wszystkie dane w magazynie dla tego kontenera zostaną zniszczone.  
  Jest to domyślny scenariusz magazynowania dla kontenera Face.
* Azure  
  Kontener face używa usługi Azure Storage i usługi Azure Cosmos DB do dystrybucji tych czterech typów danych w magazynie trwałym. Dane obiektów blob i kolejki są obsługiwane przez usługę Azure Storage. Metadane i dane pamięci podręcznej są obsługiwane przez usługę Azure Cosmos DB. Jeśli kontener face zostanie zatrzymany lub usunięty, wszystkie dane w magazynie dla tego kontenera pozostają przechowywane w usłudze Azure Storage i usłudze Azure Cosmos DB.  
  Zasoby używane w scenariuszu magazynu platformy Azure mają następujące dodatkowe wymagania
  * Zasób usługi Azure Storage musi używać rodzaju konta StorageV2
  * Zasób usługi Azure Cosmos DB musi używać interfejsu API usługi Azure Cosmos DB dla usługi MongoDB

Scenariusze magazynu i skojarzone ustawienia konfiguracji `Storage` są zarządzane `CloudAI` przez obiekt w sekcji konfiguracji. W `Storage` obiekcie dostępne są następujące ustawienia konfiguracji:

| Nazwa | Typ danych | Opis |
|------|-----------|-------------|
| `StorageScenario` | Ciąg | Scenariusz magazynu obsługiwane przez kontener. Dostępne są następujące wartości:<br/>`Memory`- Wartość domyślna. Kontener używa magazynu nietrwałego, nie rozproszonego i w pamięci, do użycia tymczasowego z jednym węzłem. Jeśli kontener zostanie zatrzymany lub usunięty, magazyn dla tego kontenera zostanie zniszczony.<br/>`Azure`- Kontener używa zasobów platformy Azure do magazynu. Jeśli kontener zostanie zatrzymany lub usunięty, magazyn dla tego kontenera jest utrwalony.|
| `ConnectionStringOfAzureStorage` | Ciąg | Parametry połączenia dla zasobu usługi Azure Storage używanego przez kontener.<br/>To ustawienie ma `Azure` zastosowanie tylko `StorageScenario` wtedy, gdy jest określone dla ustawienia konfiguracji. |
| `ConnectionStringOfCosmosMongo` | Ciąg | Parametry połączenia mongodb dla zasobu usługi Azure Cosmos DB używane przez kontener.<br/>To ustawienie ma `Azure` zastosowanie tylko `StorageScenario` wtedy, gdy jest określone dla ustawienia konfiguracji. |

Na przykład następujące polecenie określa scenariusz magazynu platformy Azure i udostępnia przykładowe parametry połączenia dla zasobów usługi Azure Storage i Usługi Cosmos DB używanych do przechowywania danych dla kontenera Face.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

Scenariusz magazynowania jest obsługiwany oddzielnie od instalacji wejściowych i mocowania wyjściowego. Można określić kombinację tych funkcji dla pojedynczego kontenera. Na przykład następujące polecenie definiuje instalację powiązania `D:\Output` platformy Docker do folderu na komputerze-hoście jako instalację wyjściową, a następnie wystąpienia kontenera z obrazu kontenera Face, zapisując pliki dziennika w formacie JSON do instalacji wyjściowej. Polecenie określa również scenariusz magazynu platformy Azure i udostępnia przykładowe parametry połączenia dla zasobów usługi Azure Storage i Usługi Cosmos DB używanych do przechowywania danych dla kontenera Face.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Disk:Format=json CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

## <a name="eula-setting"></a>Ustawienie Eula

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Płynne ustawienia

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Ustawienia poświadczeń serwera proxy http

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Ustawienia logowania
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Ustawienia instalacji

Użyj powiązania wierzchowce do odczytu i zapisu danych do i z kontenera. Można określić mocowanie wejściowe lub `--mount` mocowanie wyjściowe, określając opcję w poleceniu [uruchamiania platformy docker.](https://docs.docker.com/engine/reference/commandline/run/)

Kontenery face nie używają instalacji wejściowych lub wyjściowych do przechowywania danych szkoleniowych lub usługowych. 

Dokładna składnia lokalizacji instalacji hosta różni się w zależności od systemu operacyjnego hosta. Ponadto lokalizacja instalacji [komputera-hosta](face-how-to-install-containers.md#the-host-computer)może być niedostępna z powodu konfliktu między uprawnieniami używanymi przez konto usługi Platformy Docker a uprawnieniami lokalizacji instalacji hosta. 

|Optional (Opcjonalność)| Nazwa | Typ danych | Opis |
|-------|------|-----------|-------------|
|Niedozwolone| `Input` | Ciąg | Pojemniki na twarz nie używają tego.|
|Optional (Opcjonalność)| `Output` | Ciąg | Miejsce docelowe mocowania wyjściowego. Wartością domyślną jest `/output`. Jest to lokalizacja dzienników. Obejmuje to dzienniki kontenerów. <br><br>Przykład:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Przykładowe polecenia uruchamiania platformy dok. 

Poniższe przykłady używają ustawień konfiguracji, aby zilustrować sposób pisania i używania `docker run` poleceń.  Po uruchomieniu kontener będzie nadal działał, dopóki go nie [zatrzymasz.](face-how-to-install-containers.md#stop-the-container)

* **Znak kontynuacji wiersza:** Polecenia platformy Docker w poniższych `\`sekcjach używają ukośnika wstecznego, jako znaku kontynuacji wiersza. Zastąp lub usuń to na podstawie wymagań systemu operacyjnego hosta. 
* **Kolejność argumentów:** Nie należy zmieniać kolejności argumentów, chyba że są bardzo zaznajomieni z kontenerami platformy Docker.

Zamień {_argument_name_} własnymi wartościami:

| Symbol zastępczy | Wartość | Format lub przykład |
|-------------|-------|---|
| **{API_KEY}** | Klucz punktu końcowego `Face` zasobu `Face` na stronie Klucze platformy Azure. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Wartość punktu końcowego rozliczeń jest `Face` dostępna na stronie Przegląd platformy Azure.| Zobacz [zbieranie wymaganych parametrów](face-how-to-install-containers.md#gathering-required-parameters) dla jawnych przykładów. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Aby `Eula` `Billing`uruchomić `ApiKey` kontener, należy określić opcje i opcje; w przeciwnym razie kontener nie zostanie uruchomiony.  Aby uzyskać więcej informacji, zobacz [Rozliczenia](face-how-to-install-containers.md#billing).
> Wartość ApiKey jest **kluczem** `Cognitive Services` ze strony klucze zasobów platformy Azure. 

## <a name="face-container-docker-examples"></a>Przykłady platformy Docker kontenera ściany

Poniższe przykłady platformy Docker są dla kontenera twarzy. 

### <a name="basic-example"></a>Przykład podstawowy 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Przykład rejestrowania 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Następne kroki

* Sprawdź, [jak zainstalować i uruchomić kontenery](face-how-to-install-containers.md)
