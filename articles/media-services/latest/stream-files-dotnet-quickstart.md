---
title: Przesyłanie strumieniowe plików wideo za pomocą usługi Azure Media Services — .NET | Microsoft Docs
description: Wykonaj kroki tego przewodnika Szybki start, aby utworzyć nowe konto usługi Azure Media Services, zakodować plik i przesłać go strumieniowo do usługi Azure Media Player.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
keywords: azure media services, stream
ms.service: media-services
ms.workload: media
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/08/2018
ms.author: juliako
ms.openlocfilehash: 40759fc65caa181651de68756f4374f879fd9c9c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="quickstart-stream-video-files---net"></a>Szybki start: przesyłanie strumieniowe plików wideo — .NET

> [!NOTE]
> Najnowsza wersja usługi Azure Media Services jest w wersji zapoznawczej i może być nazywana wersją 3. Aby rozpocząć korzystanie z interfejsów API w wersji 3, należy utworzyć nowe konto usługi Media Services zgodnie z opisem w tym przewodniku Szybki start. 

Ten przewodnik Szybki start pokazuje, jak łatwo jest rozpocząć strumieniowe przesyłanie filmów wideo do różnych przeglądarek i urządzeń za pomocą usługi Azure Media Services. 

Na końcu tego przewodnika Szybki start będziesz umieć przesyłać strumieniowo filmy wideo.  

![Odtwarzanie wideo](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz zainstalowanego programu Visual Studio, możesz pobrać program [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).

## <a name="download-the-sample"></a>Pobierz przykład

Sklonuj repozytorium GitHub zawierające przykład przesyłania strumieniowego platformy .NET na swoją maszynę za pomocą następującego polecenia:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts.git
 ```

Objaśnienia działania poszczególnych funkcji w przykładzie znajdziesz po sprawdzeniu kodu i przyjrzeniu się komentarzom w [tym pliku źródłowym](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do witryny [Azure Portal](http://portal.azure.com).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="run-the-sample-app"></a>Uruchamianie przykładowej aplikacji

Po uruchomieniu aplikacji zostaną wyświetlone adresy URL, których można użyć do odtwarzania wideo za pomocą różnych protokołów. 

1. Naciśnij klawisze Ctrl+F5, aby uruchomić aplikację *EncodeAndStreamFiles*.
2. Wybierz protokół **HLS** firmy Apple — kończy się ciągiem *manifest(format=m3u8-aapl)*— i skopiuj adres URL przesyłania strumieniowego z konsoli.

![Dane wyjściowe](./media/stream-files-tutorial-with-api/output.png)

W przykładowym [kodzie źródłowym](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) możesz zobaczyć, jak jest zbudowany adres URL. Aby go utworzyć, musisz połączyć nazwę hosta punktu końcowego przesyłania strumieniowego i ścieżkę lokalizatora przesyłania strumieniowego.  

## <a name="test-with-azure-media-player"></a>Testowanie przy użyciu usługi Azure Media Player

W tym artykule strumień jest testowany za pomocą odtwarzacza Azure Media Player. 

> [!NOTE]
> Jeśli odtwarzacz jest hostowany w witrynie korzystającej z protokołu HTTPS, zmień adres URL tak, aby zawierał ciąg „https”.

1. Otwórz przeglądarkę i przejdź pod adres [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. W polu **Adres URL** wklej jedną z wartości adresu URL przesyłania strumieniowego otrzymanych po uruchomieniu aplikacji. 
3. Naciśnij pozycję **Aktualizuj odtwarzacz**.

Usługi Azure Media Player można użyć do testowania, ale nie należy jej używać w środowisku produkcyjnym. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie są już potrzebne żadne zasoby z grupy zasobów, w tym konto usługi Media Services i magazynu utworzone w ramach tego przewodnika Szybki start, usuń grupę zasobów. Do tego celu możesz użyć narzędzia **CloudShell**.

W usłudze **CloudShell** uruchom następujące polecenie:

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="examine-the-code"></a>Analizowanie kodu

Objaśnienia działania poszczególnych funkcji w przykładzie znajdziesz po sprawdzeniu kodu i przyjrzeniu się komentarzom w [tym pliku źródłowym](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

Samouczek [przekazywania, kodowania i strumieniowego przesyłania plików](stream-files-tutorial-with-api.md) zawiera bardziej zaawansowany przykład przesyłania strumieniowego wraz ze szczegółowymi objaśnieniami. 

## <a name="multithreading"></a>Wielowątkowość

Zestawy SDK usługi Azure Media Services 3 nie są bezpieczne wątkowo. Podczas pracy z aplikacją wielowątkową należy wygenerować nowy obiekt AzureMediaServicesClient dla każdego wątku.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: przekazywanie, kodowanie i przesyłanie strumieniowe plików](stream-files-tutorial-with-api.md)
