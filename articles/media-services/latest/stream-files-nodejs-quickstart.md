---
title: Stream plików wideo za pomocą usługi Azure Media Services — Node.js | Dokumentacja firmy Microsoft
description: Wykonaj kroki tego przewodnika Szybki start, aby utworzyć nowe konto usługi Azure Media Services, zakodować plik i przesłać go strumieniowo do usługi Azure Media Player.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
keywords: azure media services, stream
ms.service: media-services
ms.workload: media
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/26/2019
ms.author: juliako
ms.openlocfilehash: 22b7f2380b509daa4cb9931d6fc57c1297628e3d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61233180"
---
# <a name="quickstart-stream-video-files---nodejs"></a>Szybki start: Przesyłanie strumieniowe wideo — Node.js

Ten przewodnik Szybki start pokazuje, jak łatwo jest zakodować i rozpocząć strumieniowe przesyłanie wideo do różnych przeglądarek i urządzeń za pomocą usługi Azure Media Services. Zawartość wejściową można określić przy użyciu adresów URL HTTP, adresów URL SAS lub ścieżek do plików znajdujących się w magazynie obiektów blob platformy Azure.
W przykładzie w tym temacie kodowana jest zawartość, która jest udostępniana za pośrednictwem adresu URL protokołu HTTPS. Pamiętaj, że obecnie usługa AMS w wersji 3 nie obsługuje fragmentarycznego kodowania transferu za pośrednictwem adresów URL HTTPS.

Na końcu tego przewodnika Szybki start będziesz umieć przesyłać strumieniowo filmy wideo.  

![Odtwarzanie wideo](./media/stream-files-nodejs-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Zainstalować środowisko [Node.js](https://nodejs.org/en/download/).
- [Utwórz konto usługi Media Services](create-account-cli-how-to.md).<br/>Koniecznie zapamiętaj wartości, które zostały użyte jako nazwa grupy zasobów i nazwa konta usługi Media Services.
- Postępuj zgodnie z instrukcjami zawartymi w temacie [Access Azure Media Services API with the Azure CLI](access-api-cli-how-to.md) (Uzyskiwanie dostępu do interfejsu API usług Azure Media Services za pomocą interfejsu wiersza polecenia platformy Azure) i zapisz poświadczenia. Będą one potrzebne w celu uzyskania dostępu do interfejsu API.

## <a name="download-and-configure-the-sample"></a>Pobieranie i konfigurowanie przykładu

Sklonuj repozytorium GitHub, zawierający przesyłania strumieniowego przykładu środowiska Node.js na komputerze, korzystając z następującego polecenia:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

Przykład znajduje się w [StreamFilesSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/master/AMSv3Samples/StreamFilesSample) folderu.

Otwórz [index.js](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L25) w pobranego projektu. Zastąp `endpoint config` wartości przy użyciu poświadczeń otrzymanych [dostęp do interfejsów API](access-api-cli-how-to.md).

W przykładzie są wykonywane następujące akcje:

1. Tworzenie **przekształcenia** (najpierw następuje sprawdzenie, czy podane przekształcenie istnieje). 
2. Tworzenie **zasobu** danych wyjściowych, który zostanie użyty jako dane wyjściowe **zadania** kodowania.
3. Tworzenie danych wejściowych **zadania** opartych na adresie URL protokołu HTTPS.
4. Przesłanie **zadania** kodowania przy użyciu danych przychodzących i wychodzących, które zostały utworzone wcześniej.
5. Sprawdzenie stanu zadania.
6. Tworzy **lokalizator przesyłania strumieniowego**.
7. Utworzenie adresów URL przesyłania strumieniowego.

## <a name="run-the-sample-app"></a>Uruchamianie przykładowej aplikacji

1. Aplikacja pobiera pliki zakodowane. Utwórz folder miejsce na pliki wyjściowe przejść i zaktualizuj wartość **outputFolder** zmienną [index.js](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L39) pliku.
1. Otwórz **polecenia**, przejdź do katalogu próbki i wykonaj następujące polecenia.

    ```
    npm install 
    node index.js
    ```

Po zakończeniu uruchamiania, podobne dane wyjściowe powinny być:

![Uruchom polecenie](./media/stream-files-nodejs-quickstart/run.png)

## <a name="test-with-azure-media-player"></a>Testowanie przy użyciu usługi Azure Media Player

W tym artykule strumień jest testowany za pomocą odtwarzacza Azure Media Player. 

> [!NOTE]
> Jeśli odtwarzacz jest hostowany w witrynie korzystającej z protokołu HTTPS, zmień adres URL tak, aby zawierał ciąg „https”.

1. Otwórz przeglądarkę i przejdź pod adres [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. W polu **Adres URL** wklej jedną z wartości adresu URL przesyłania strumieniowego otrzymanych po uruchomieniu aplikacji. 
 
     Adres URL możesz wkleić w formacie HLS, Dash, lub Smooth, a usługa Azure Media Player przełączy się na odpowiedni protokół przesyłania strumieniowego w celu automatycznego odtworzenia na urządzeniu.
3. Naciśnij pozycję **Aktualizuj odtwarzacz**.

Usługi Azure Media Player można użyć do testowania, ale nie należy jej używać w środowisku produkcyjnym. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie są już potrzebne żadne zasoby z grupy zasobów, w tym konto usługi Media Services i magazynu utworzone w ramach tego przewodnika Szybki start, usuń grupę zasobów.

Wykonaj następujące polecenie interfejsu wiersza polecenia:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="see-also"></a>Zobacz także

[Kody błędów zadania](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Pojęcia dotyczące usługi Media Services](concepts-overview.md)
