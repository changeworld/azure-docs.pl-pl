---
title: Analizowanie wideo za pomocą usługi Azure Media Services | Microsoft Docs
description: Wykonaj kroki tego samouczka, aby przeanalizować wideo przy użyciu usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/09/2018
ms.author: juliako
ms.openlocfilehash: 54c49645722b6545d8ae872151b9b82674d44523
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="tutorial-analyze-videos-with-azure-media-services"></a>Samouczek: analizowanie wideo za pomocą usługi Azure Media Services 

W tym samouczku przedstawiono sposób analizowania wideo za pomocą usługi Azure Media Services. Istnieje wiele scenariuszy, w przypadku których głęboka analiza zarejestrowanego wideo lub dźwięku może być przydatna. Na przykład organizacje mogą zamieniać mowę na tekst w celu przekształcenia nagrań rozmów działu obsługi klienta w katalog z możliwością wyszukiwania, indeksami i pulpitami nawigacyjnymi — aby dzięki jego wykorzystaniu zwiększyć zadowolenie klientów. Następnie mogą analizować swoją działalność, uzyskując na przykład listę typowych skarg, przyczyn takich skarg itp.

Ten samouczek przedstawia sposób wykonania następujących czynności:    

> [!div class="checklist"]
> * Uruchamianie usługi Azure Cloud Shell
> * Tworzenie konta usługi Media Services
> * Uzyskiwanie dostępu do interfejsu API usługi Media Services
> * Konfigurowanie przykładowej aplikacji
> * Szczegółowe analizowanie przykładowego kodu
> * Uruchamianie aplikacji
> * Sprawdzanie danych wyjściowych
> * Oczyszczanie zasobów

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz zainstalowanego programu Visual Studio, możesz pobrać program [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).

## <a name="download-the-sample"></a>Pobierz przykład

Sklonuj repozytorium GitHub zawierające przykład dla platformy .NET na swoją maszynę za pomocą następującego polecenia:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-sample-code-in-detail"></a>Szczegółowe analizowanie przykładowego kodu

W tej sekcji są analizowane funkcje zdefiniowane w pliku [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/Program.cs) projektu *AnalyzeVideos*.

### <a name="start-using-media-services-apis-with-net-sdk"></a>Rozpoczynanie korzystania z interfejsów API usługi Media Services przy użyciu zestawu .NET SDK

Aby rozpocząć korzystanie z interfejsów API usługi Media Services na platformie .NET, należy utworzyć obiekt **AzureMediaServicesClient**. Aby utworzyć obiekt, należy podać poświadczenia wymagane do nawiązania połączenia z platformą Azure przez klienta przy użyciu usługi Azure AD. Najpierw należy uzyskać token, a następnie utworzyć obiekt **ClientCredential** na podstawie zwróconego tokenu. W kodzie sklonowanym na początku tego artykułu do uzyskania tokenu jest używany obiekt **ArmClientCredential**.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>Tworzenie zasobu wyjściowego na potrzeby przechowywania wyników zadania 

Zasób wyjściowy przechowuje wynik zadania. Projekt definiuje funkcję **DownloadResults**, która pobiera wyniki z zasobu wyjściowego do folderu wyjściowego, umożliwiając zapoznanie się z nimi.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-analyzes-videos"></a>Tworzenie przekształcenia i zadania analizującego wideo

W przypadku kodowania lub przetwarzania zawartości w usłudze Media Services typowym wzorcem postępowania jest skonfigurowanie ustawień kodowania jako przepisu. Następnie przesyła się **zadanie** w celu zastosowania tego przepisu do wideo. Przesłanie nowego zadania dla każdego nowego wideo powoduje zastosowanie przepisu do wszystkich wideo w bibliotece. Przepis w usłudze Media Services nazywa się **przekształceniem**. Aby uzyskać więcej informacji, zobacz [Przekształcenia i zadania](transform-concept.md). Przykład opisany w tym samouczku umożliwia zdefiniowanie przepisu, który analizuje określone wideo. 

#### <a name="transform"></a>Przekształcanie

Podczas tworzenia nowego wystąpienia obiektu **Transform** należy określić, jakie dane wyjściowe ma ono tworzyć. Wymagany parametr to obiekt **TransformOutput**, jak pokazano w powyższym kodzie. Każdy obiekt **TransformOutput** zawiera element **Preset**. Element **Preset** zawiera szczegółowe instrukcje operacji przetwarzania wideo i/lub dźwięku używanych do wygenerowania docelowego obiektu **TransformOutput**. W tym przykładzie jest używane ustawienie wstępne **VideoAnalyzerPreset**, a do jego konstruktora jest przekazywany język („en-US”). To ustawienie wstępne umożliwia przeprowadzenie wielu analiz dźwięku i wideo tworzących plik wideo. Istnieje możliwość użycia ustawienia wstępnego **AudioAnalyzerPreset**, jeśli jest potrzebne przeprowadzenie wielu analiz dźwięku w pliku wideo. 

Podczas tworzenia obiektu **Transform** należy najpierw sprawdzić, czy taki obiekt już istnieje, używając metody **Get**, tak jak przedstawiono w poniższym kodzie.  W przypadku usługi Media Services 3 metody **Get** wywołane dla obiektów zwracają **wartość null**, jeśli obiekt nie istnieje (sprawdzana jest nazwa bez uwzględniania wielkości liter).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Zadanie

Jak wspomniano powyżej, obiekt **Transform** jest przepisem, a obiekt **Job** to rzeczywiste żądanie skierowane do usługi Media Services i mające na celu zastosowanie obiektu **Transform** do określonej wejściowej zawartości wideo lub dźwiękowej. Obiekt **Job** określa informacje takie jak lokalizacja wejściowego pliku wideo oraz danych wyjściowych. Istnieje możliwość określenia lokalizacji pliku wideo za pomocą adresów URL protokołu HTTPS, adresów URL sygnatury dostępu współdzielonego lub zasobów znajdujących się na koncie usługi Media Service. 

W tym przykładzie dane wejściowe zadania to lokalny film wideo.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Oczekiwanie na zakończenie zadania

Ukończenie zadania zajmuje trochę czasu, a Ty chcesz otrzymać powiadomienie o tym fakcie. Są dostępne różne opcje powiadamiania o ukończeniu zadania. Najprostsza opcja (przedstawiona tutaj) to użycie sondowania. 

Sondowanie nie jest najlepszym rozwiązaniem w przypadku zastosowań produkcyjnych ze względu na możliwe opóźnienia. Jeśli sondowanie będzie nadużywane w ramach konta, może zostać ograniczone. Deweloperzy zamiast niego powinni używać usługi Event Grid.

Usługę Event Grid zaprojektowano pod kątem wysokiej dostępności, stałego poziomu wydajności i dynamicznej skalowalności. Dzięki usłudze Event Grid Twoje aplikacje mogą nasłuchiwać zdarzeń pochodzących z praktycznie wszystkich usług platformy Azure i ze źródeł niestandardowych oraz reagować na nie. Prosta, reaktywna obsługa zdarzeń oparta na protokole HTTP pomaga w tworzeniu wydajnych rozwiązań za pośrednictwem inteligentnego filtrowania i routingu zdarzeń. Zobacz [Kierowanie zdarzeń do niestandardowego internetowego punktu końcowego](job-state-events-cli-how-to.md).

**Zadanie** zwykle przechodzi przez następujące stany: **Scheduled (Zaplanowane)**, **Queued (W kolejce)**, **Processing (Przetwarzane)**, **Finished (Zakończone)** (stan końcowy). Jeśli zadanie napotka błąd, może być w stanie **Error (Błąd)**. Jeśli zadanie jest w trakcie anulowania, może być w stanie **Canceling (Anulowanie)**, a po zakończeniu tej operacji w stanie **Canceled (Anulowane)**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#WaitForJobToFinish)]

### <a name="download-the-result-of-the-job"></a>Pobieranie wyniku zadania

Następująca funkcja umożliwia pobranie wyników z zasobu wyjściowego do folderu „wyjściowego”, co umożliwia zapoznanie się z wynikami zadania. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#DownloadResults)]

### <a name="clean-up-resource-in-your-media-services-account"></a>Czyszczenie zasobów na koncie usługi Media Services

Zazwyczaj należy wyczyścić wszystko z wyjątkiem obiektów, których zamierzasz użyć ponownie (zazwyczaj są to obiekty Transform, obiekty StreamingLocator są utrwalane itd.). Jeśli Twoje konto ma być czyste po przeprowadzeniu eksperymentów, należy usunąć zasoby, których ponowne użycie nie jest planowane. Na przykład następujący kod usuwa zadania.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>Uruchamianie przykładowej aplikacji

Naciśnij klawisze Ctrl+F5, aby uruchomić aplikację *AnalyzeVideos*.

Po uruchomieniu programu zadanie tworzy miniatury dla każdej twarzy wykrytej w wideo. Tworzy także plik insights.json.

## <a name="examine-the-output"></a>Sprawdzanie danych wyjściowych

Plik wyjściowy analizy wideo ma nazwę insights.json. Ten plik zawiera wyniki analiz wideo. Opis elementów znajdujących się w pliku json zawiera artykuł [Inteligentna analiza multimediów](intelligence-concept.md).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie są już potrzebne żadne zasoby w grupie zasobów, w tym konto usługi Media Services i konta magazynu utworzone w ramach tego samouczka, usuń grupę zasobów utworzoną wcześniej. Do tego celu możesz użyć narzędzia **CloudShell**.

W usłudze **CloudShell** uruchom następujące polecenie:

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Wielowątkowość

Zestawy SDK usługi Azure Media Services 3 nie są bezpieczne wątkowo. Podczas pracy z aplikacją wielowątkową należy wygenerować nowy obiekt AzureMediaServicesClient dla każdego wątku.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: przekazywanie, kodowanie i przesyłanie strumieniowe plików](stream-files-tutorial-with-api.md)
