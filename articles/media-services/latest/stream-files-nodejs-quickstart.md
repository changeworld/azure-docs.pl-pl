---
title: Przesyłanie strumieniowe plików wideo za pomocą usługi Azure Media Services — node.js | Dokumenty firmy Microsoft
description: Wykonaj kroki tego samouczka, aby utworzyć nowe konto usługi Azure Media Services, zakodować plik i przesłać go strumieniowo do programu Azure Media Player.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
keywords: azure media services, stream
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: mvc
ms.date: 08/19/2019
ms.author: juliako
ms.openlocfilehash: fa9fbf3bac55ca0b26c3644b7f6818fa96088612
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "69639390"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---nodejs"></a>Samouczek: Kodowanie zdalnego pliku na podstawie adresu URL i przesyłanie strumieniowe wideo - Node.js

W tym samouczku pokazano, jak łatwo jest zakodować i rozpocząć przesyłanie strumieniowe wideo w wielu różnych przeglądarkach i urządzeniach korzystających z usługi Azure Media Services. Zawartość wejściową można określić przy użyciu adresów URL HTTP, adresów URL SAS lub ścieżek do plików znajdujących się w magazynie obiektów blob platformy Azure.

Przykład w tym artykule koduje zawartość, która jest dostępna za pośrednictwem adresu URL HTTPS. Pamiętaj, że obecnie usługa AMS w wersji 3 nie obsługuje fragmentarycznego kodowania transferu za pośrednictwem adresów URL HTTPS.

Pod koniec samouczka będzie można przesyłać strumieniowo wideo.  

![Odtwarzanie wideo](./media/stream-files-nodejs-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Instalowanie [pliku Node.js](https://nodejs.org/en/download/)
- [Utwórz konto usługi Media Services](create-account-cli-how-to.md).<br/>Koniecznie zapamiętaj wartości, które zostały użyte jako nazwa grupy zasobów i nazwa konta usługi Media Services.
- Postępuj zgodnie z instrukcjami zawartymi w temacie [Access Azure Media Services API with the Azure CLI](access-api-cli-how-to.md) (Uzyskiwanie dostępu do interfejsu API usług Azure Media Services za pomocą interfejsu wiersza polecenia platformy Azure) i zapisz poświadczenia. Będą one potrzebne w celu uzyskania dostępu do interfejsu API.

## <a name="download-and-configure-the-sample"></a>Pobieranie i konfigurowanie przykładu

Sklonuj repozytorium GitHub zawierające próbkę node.js przesyłania strumieniowego do komputera za pomocą następującego polecenia:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

Próbka znajduje się w folderze [StreamFilesSample.](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/master/AMSv3Samples/StreamFilesSample)

Otwórz [plik index.js](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L25) w pobranym projekcie. Zastąp `endpoint config` wartości poświadczeniami, które zostały dodane [z uzyskiwania dostępu do interfejsów API](access-api-cli-how-to.md).

W przykładzie są wykonywane następujące akcje:

1. Tworzenie **przekształcenia** (najpierw następuje sprawdzenie, czy podane przekształcenie istnieje). 
2. Tworzy **zasób** wyjściowy, który jest używany jako dane wyjściowe **zadania**kodowania.
3. Tworzenie danych wejściowych **zadania** opartych na adresie URL protokołu HTTPS.
4. Przesłanie **zadania** kodowania przy użyciu danych przychodzących i wychodzących, które zostały utworzone wcześniej.
5. Sprawdzanie stanu zadania.
6. Tworzy **lokalizator przesyłania strumieniowego**.
7. Utworzenie adresów URL przesyłania strumieniowego.

## <a name="run-the-sample-app"></a>Uruchamianie przykładowej aplikacji

1. Aplikacja pobiera zakodowane pliki. Utwórz folder, w którym chcesz, aby pliki wyjściowe zostały utworzone, i zaktualizować wartość zmiennej **outputFolder** w pliku [index.js.](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L39)
1. Otwórz **wiersz polecenia**, przejdź do katalogu próbki i wykonaj następujące polecenia.

    ```
    npm install 
    node index.js
    ```

Po zakończeniu pracy powinien zostać wyświetlony podobny wynik:

![Run](./media/stream-files-nodejs-quickstart/run.png)

## <a name="test-with-azure-media-player"></a>Testowanie przy użyciu usługi Azure Media Player

W tym artykule strumień jest testowany za pomocą odtwarzacza Azure Media Player. 

> [!NOTE]
> Jeśli odtwarzacz jest hostowany w witrynie korzystającej z protokołu HTTPS, zmień adres URL tak, aby zawierał ciąg „https”.

1. Otwórz przeglądarkę internetową [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/)i przejdź do pliku .
2. W polu **Adres URL** wklej jedną z wartości adresu URL przesyłania strumieniowego otrzymanych po uruchomieniu aplikacji. 
 
     Adres URL możesz wkleić w formacie HLS, Dash, lub Smooth, a usługa Azure Media Player przełączy się na odpowiedni protokół przesyłania strumieniowego w celu automatycznego odtworzenia na urządzeniu.
3. Naciśnij pozycję **Aktualizuj odtwarzacz**.

Usługi Azure Media Player można użyć do testowania, ale nie należy jej używać w środowisku produkcyjnym. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie potrzebujesz już żadnych zasobów w grupie zasobów, w tym kont usługi Media Services i magazynu utworzonych dla tego samouczka, usuń grupę zasobów.

Wykonaj następujące polecenie interfejsu wiersza polecenia:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="see-also"></a>Zobacz też

[Kody błędów pracy](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Pojęcia dotyczące usług multimedialnych](concepts-overview.md)
