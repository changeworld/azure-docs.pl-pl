---
title: Przepisy kontenera narzędzia docker compose
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak wdrażanie wielu kontenerów usług Cognitive Services. Ta procedura pokazuje sposób organizowania wielu obrazów kontenerów platformy Docker przy użyciu narzędzia Docker Compose.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 8afb7e866bc2a5fefe28a71653c4a2a87fdc7a5b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445790"
---
# <a name="use-multiple-containers-in-a-private-network-with-docker-compose"></a>Korzystanie z wielu kontenerów w sieci prywatnej przy użyciu narzędzia Docker Compose

Dowiedz się, jak wdrażanie wielu kontenerów usług Cognitive Services. Ta procedura pokazuje sposób organizowania wielu obrazów kontenerów platformy Docker przy użyciu narzędzia Docker Compose.

> [Docker Compose](https://docs.docker.com/compose/) to narzędzie służące do definiowania i uruchamiania aplikacji platformy Docker z wieloma kontenerami. Za pomocą Compose umożliwia się plik yaml pozwalający Konfigurowanie usług aplikacji. Następnie za pomocą jednego polecenia, tworzenie i uruchamianie wszystkich usług z konfiguracji.

Gdy jest to konieczne, może być atrakcyjne organizowanie wiele obrazów kontenera na komputerze hosta. W tym artykule firma Microsoft będzie zebrane rozpoznawanie tekstu i usługi rozpoznawania formularza.

## <a name="prerequisites"></a>Wymagania wstępne

Ta procedura wymaga kilku narzędzi, które musi być zainstalowany i uruchamiane lokalnie.

* Korzystanie z subskrypcji platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* [Aparat platformy docker](https://www.docker.com/products/docker-engine) i Zweryfikuj, że interfejs wiersza polecenia platformy Docker działa w oknie konsoli.
* Zasób platformy Azure z warstwą cenową poprawne. Nie wszystkie warstwy cenowe pracować z tego kontenera:
  * **Przetwarzanie obrazów** zasobu o F0 lub standardowej ceny warstwy tylko.
  * **Formularz rozpoznawania** zasobu o F0 lub standardowej ceny warstwy tylko.
  * **Usługi cognitive Services** warstwa cenowa zasobu z S0.

## <a name="request-access-to-the-container-registry"></a>Żądanie dostępu do rejestru kontenerów

Wypełnij i Prześlij [formularz wniosku kontenery mowy w usłudze Cognitive Services](https://aka.ms/speechcontainerspreview/) Aby zażądać dostępu do kontenera. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="docker-compose-file"></a>Plik narzędzia docker compose

Plik YAML definiuje wszystkie usługi, które mają zostać wdrożone. Usługi te zależą od albo `DockerFile` lub istniejący obraz kontenera, w tym przypadku użyjemy dwa obrazy w wersji zapoznawczej. Skopiuj i wklej następujący plik YAML i zapisz go jako *docker compose.yaml*. Podaj odpowiednie _apikey_, _rozliczeń_, i _identyfikator URI punktu końcowego_ wartości w _docker-compose.yml_ pliku poniżej.

```yaml
version: '3.7'
services:
  forms:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    environment:
       eula: accept
       billing: # < Your form recognizer billing URL >
       apikey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionApiKey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionEndpointUri: # < Your form recognizer URI >
    volumes:
       - type: bind
         source: e:\publicpreview\output
         target: /output
       - type: bind
         source: e:\publicpreview\input
         target: /input
    ports:
      - "5010:5000"

  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    environment:
      eula: accept
      apikey: # < Your recognize text API key >
      billing: # < Your recognize text billing URL >
    ports:
      - "5021:5000"
```

> [!IMPORTANT]
> Tworzenie katalogów na komputerze hosta, które są określone w obszarze `volumes` węzła. Jest to wymagane, ponieważ katalogi muszą istnieć przed podjęciem próby zainstalowanie obrazu z powiązaniami woluminu.

## <a name="start-the-configured-docker-compose-services"></a>Start skonfigurowanego docker compose usług

Narzędzia docker compose plików umożliwia zarządzanie wszystkich zdefiniowanych usługi Cykl wsparcia; Uruchamianie/zatrzymywanie i ponowne tworzenie usług, wyświetlanie stanu usługi i przesyłanie strumieniowe dzienników. Otwórz interfejs wiersza polecenia z katalogu projektu (gdzie *docker compose.yaml* znajduje się plik).

> [!NOTE]
> Aby uniknąć błędów, upewnij się, komputer hosta poprawnie współużytkuje dysków twardych **aparat platformy Docker**. Na przykład jeśli *e:\publicpreview* jest używany jako katalog w *docker compose.yaml* udostępnianie *dysku E* za pomocą platformy docker.

Przy użyciu interfejsu wiersza polecenia, wykonaj następujące polecenie, aby uruchomić (lub ponownego uruchomienia) wszystkie usługi, które są zdefiniowane w *docker compose.yaml*:

```console
docker-compose up
```

Czas pierwszego wykonania `docker-compose up` polecenia w tej konfiguracji **Docker** każda będzie ściągać obrazy skonfigurowane w `services` węzła — pobieranie/instalowanie ich:

```console
Pulling forms (containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:)...
latest: Pulling from microsoft/cognitive-services-form-recognizer
743f2d6c1f65: Pull complete
72befba99561: Pull complete
2a40b9192d02: Pull complete
c7715c9d5c33: Pull complete
f0b33959f1c4: Pull complete
b8ab86c6ab26: Pull complete
41940c21ed3c: Pull complete
e3d37dd258d4: Pull complete
cdb5eb761109: Pull complete
fd93b5f95865: Pull complete
ef41dcbc5857: Pull complete
4d05c86a4178: Pull complete
34e811d37201: Pull complete
Pulling ocr (containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:)...
latest: Pulling from microsoft/cognitive-services-recognize-text
f476d66f5408: Already exists
8882c27f669e: Already exists
d9af21273955: Already exists
f5029279ec12: Already exists
1a578849dcd1: Pull complete
45064b1ab0bf: Download complete
4bb846705268: Downloading [=========================================>         ]  187.1MB/222.8MB
c56511552241: Waiting
e91d2aa0f1ad: Downloading [==============================================>    ]  162.2MB/176.1MB
```

Obrazy są pobierane, a następnie są uruchomione usługi obrazu.

```console
Starting docker_ocr_1   ... done
Starting docker_forms_1 ... doneAttaching to docker_ocr_1, docker_forms_1forms_1  | forms_1  | forms_1  | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
forms_1  | 
forms_1  | 
forms_1  | Using '/input' for reading models and other read-only data.
forms_1  | Using '/output/forms/812d811d1bcc' for writing logs and other output data.
forms_1  | Logging to console.
forms_1  | Submitting metering to 'https://westus2.api.cognitive.microsoft.com/'.
forms_1  | WARNING: No access control enabled!
forms_1  | warn: Microsoft.AspNetCore.Server.Kestrel[0]
forms_1  |       Overriding address(es) 'http://+:80'. Binding to endpoints defined in UseKestrel() instead.
forms_1  | Hosting environment: Production
forms_1  | Content root path: /app/forms
forms_1  | Now listening on: http://0.0.0.0:5000
forms_1  | Application started. Press Ctrl+C to shut down.
ocr_1    | 
ocr_1    | 
ocr_1    | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
ocr_1    |
ocr_1    | 
ocr_1    | Logging to console.
ocr_1    | Submitting metering to 'https://westcentralus.api.cognitive.microsoft.com/'.
ocr_1    | WARNING: No access control enabled!
ocr_1    | Hosting environment: Production
ocr_1    | Content root path: /
ocr_1    | Now listening on: http://0.0.0.0:5000
ocr_1    | Application started. Press Ctrl+C to shut down.
```

## <a name="verify-the-service-availability"></a>Sprawdź dostępność usługi

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

Poniżej przedstawiono przykładowe dane wyjściowe:

```
IMAGE ID            REPOSITORY                                                                 TAG
2ce533f88e80        containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer   latest
4be104c126c5        containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text    latest
```

### <a name="test-the-recognize-text-container"></a>Kontener tekstu rozpoznawaj testu

Otwórz przeglądarkę na maszynie hosta, a następnie przejdź do `localhost` z określonego portu z *docker compose.yaml*, na przykład `http://localhost:5021/swagger/index.html`. Spróbuj go funkcji można użyć interfejsu API do endpoint tekstu rozpoznawaj testowania.

![Rozpoznawanie tekstu struktury Swagger](media/recognize-text-swagger-page.png)

### <a name="test-the-form-recognizer-container"></a>Formularz kontener rozpoznawania testu

Otwórz przeglądarkę na maszynie hosta, a następnie przejdź do `localhost` z określonego portu z *docker compose.yaml*, na przykład `http://localhost:5010/swagger/index.html`. Spróbuj go funkcji można użyć interfejsu API, aby przetestować punkt końcowy rozpoznawania formularza.

![Formularz rozpoznawania Swagger](media/form-recognizer-swagger-page.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Kontenery usługi cognitive Services](../cognitive-services-container-support.md)