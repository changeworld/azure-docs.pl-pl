---
title: Wdrażanie wielu kontenerów za pomocą narzędzia Docker Compose
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak wdrożyć wiele kontenerów usług Cognitive Services. W tym artykule pokazano, jak zorganizować wiele obrazów kontenerów platformy Docker przy użyciu docker compose.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 458cda927a6a123fcd9962efc6ab705e13f43286
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878786"
---
# <a name="use-docker-compose-to-deploy-multiple-containers"></a>Wdrażanie wielu kontenerów za pomocą narzędzia Docker Compose

W tym artykule pokazano, jak wdrożyć wiele kontenerów usług Azure Cognitive Services. W szczególności dowiesz się, jak używać docker Compose do organizowania wielu obrazów kontenerów platformy Docker.

> [Docker Compose](https://docs.docker.com/compose/) to narzędzie do definiowania i uruchamiania aplikacji platformy Docker z wieloma kontenerami. W aplikacji użyj pliku YAML, aby skonfigurować usługi aplikacji. Następnie należy utworzyć i uruchomić wszystkie usługi z konfiguracji, uruchamiając jedno polecenie.

Może być przydatne do organizowania wielu obrazów kontenerów na jednym komputerze-hoście. W tym artykule zbierzemy kontenery aparat rozpoznawania odczytu i formularzy.

## <a name="prerequisites"></a>Wymagania wstępne

Ta procedura wymaga kilku narzędzi, które muszą być zainstalowane i uruchamiane lokalnie:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* [Docker Engine](https://www.docker.com/products/docker-engine). Upewnij się, że wierszka polecenia platformy Docker działa w oknie konsoli.
* Zasób platformy Azure z poprawną warstwą cenową. Tylko następujące warstwy cenowe działają z tym kontenerem:
  * **Zasób przetwarzania usługi Computer Vision** tylko z warstwą cenową F0 lub Standard.
  * **Zasób rozpoznawania formularzy** tylko z warstwą cenową F0 lub Standard.
  * Zasób **usług Cognitive Services** z warstwą cenową S0.

## <a name="request-access-to-the-container-registry"></a>Żądanie dostępu do rejestru kontenerów

Wypełnij i prześlij [formularz żądania kontenerów mowy usług Cognitive Services](https://aka.ms/speechcontainerspreview/). 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="docker-compose-file"></a>Plik redagowania docker

Plik YAML definiuje wszystkie usługi, które mają zostać wdrożone. Te usługi opierają się `DockerFile` na istniejącym obrazie kontenera lub istniejącym. W takim przypadku użyjemy dwóch obrazów podglądu. Skopiuj i wklej następujący plik YAML i zapisz go jako *docker-compose.yaml*. Podaj odpowiednie wartości **apikey**, **billing**i **EndpointUri** w pliku.

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
         source: E:\publicpreview\output
         target: /output
       - type: bind
         source: E:\publicpreview\input
         target: /input
    ports:
      - "5010:5000"

  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-read"
    environment:
      eula: accept
      apikey: # < Your computer vision API key >
      billing: # < Your computer vision billing URL >
    ports:
      - "5021:5000"
```

> [!IMPORTANT]
> Utwórz katalogi na komputerze-hoście, które są określone w węźle **woluminów.** Takie podejście jest wymagane, ponieważ katalogi muszą istnieć przed próbą zainstalowania obrazu przy użyciu powiązań woluminu.

## <a name="start-the-configured-docker-compose-services"></a>Uruchamianie skonfigurowanych usług docker compose

Plik docker compose umożliwia zarządzanie wszystkimi etapami w cyklu życia zdefiniowanej usługi: uruchamianie, zatrzymywanie i odbudowywanie usług; wyświetlanie stanu usługi; i strumieniowania w dzienniku. Otwórz interfejs wiersza polecenia z katalogu projektu (w którym znajduje się plik docker-compose.yaml).

> [!NOTE]
> Aby uniknąć błędów, upewnij się, że komputer-host poprawnie współumie współumie współumieruje dyski z aparatem platformy Docker. Jeśli na przykład *E:\publicpreview* jest używany jako katalog w pliku *docker-compose.yaml,* udostępnij dysk **E** docker.

W interfejsie wiersza polecenia wykonaj następujące polecenie, aby uruchomić (lub ponownie uruchomić) wszystkie usługi zdefiniowane w pliku *docker-compose.yaml:*

```console
docker-compose up
```

Przy pierwszym uruchomieniu polecenia **docker-compose up** przy użyciu tej konfiguracji pobiera obrazy skonfigurowane w węźle **usług,** a następnie pobiera je i montuje:

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
Pulling ocr (containerpreview.azurecr.io/microsoft/cognitive-services-read:)...
latest: Pulling from microsoft/cognitive-services-read
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

Po pobraniu obrazów usługi obrazów są uruchamiane:

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

Oto kilka przykładowych danych wyjściowych:

```
IMAGE ID            REPOSITORY                                                                 TAG
2ce533f88e80        containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer   latest
4be104c126c5        containerpreview.azurecr.io/microsoft/cognitive-services-read              latest
```

### <a name="test-containers"></a>Pojemniki testowe

Otwórz przeglądarkę na komputerze-hoście i przejdź do **localhost** przy użyciu określonego portu z pliku http://localhost:5021/swagger/index.html *docker-compose.yaml,* takiego jak . Na przykład można użyć try **it** funkcji w interfejsie API, aby przetestować punkt końcowy aparatu rozpoznawania formularzy. Oba pojemniki swagger strony powinny być dostępne i sprawdzalne.

![Kontener aparatu rozpoznawania formularzy](media/form-recognizer-swagger-page.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Kontenery usług Cognitive Services](../cognitive-services-container-support.md)
