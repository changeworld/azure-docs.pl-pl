---
title: Emocji interfejsu API języka C# samouczek | Dokumentacja firmy Microsoft
description: Poznaj podstawowe aplikacji Windows, który używa kognitywnych API rozpoznawania emocji — warstwa usług do rozpoznawania emocji wyrażona kroje obrazu.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 01/23/2017
ms.author: anroth
ms.openlocfilehash: f015e5720f65d0951a77de76ce8882a6dcdc1c3b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347592"
---
# <a name="emotion-api-in-c35-tutorial"></a>Emocji interfejsu API w języku C&#35; samouczka

> [!IMPORTANT]
> Interfejs API podglądu kończy się 30 października 2017 r. Testowanie nowego [wideo indeksatora interfejsu API w wersji zapoznawczej](https://azure.microsoft.com/services/cognitive-services/video-indexer/) można łatwo wyodrębnić szczegółowych informacji z wideo i celu ułatwienia pracy funkcję odnajdowania zawartości, takich jak wyniki wyszukiwania został określony poprzez wykrycie słowa rozmowy, kroje znaków i emocji. [Dowiedz się więcej](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

Poznaj podstawowe aplikacji systemu Windows, która używa interfejsu API rozpoznawania emocji — warstwa do rozpoznawania emocji wyrażona kroje obrazu. Poniżej umożliwia na przykład Prześlij adres URL obrazu lub lokalnie przechowywanych plików. W tym przykładzie typu open source jako szablon służy do tworzenia własnych aplikacji dla systemu Windows przy użyciu interfejsu API rozpoznawania emocji — warstwa i WPF (Windows Presentation Foundation), część .NET Framework.

## <a name="Prerequisites">Wymagania wstępne</a>
#### <a name="platform-requirements"></a>Wymagania dotyczące platformy  
Poniżej przykład został opracowany dla programu .NET Framework za pomocą [programu Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).  

#### <a name="subscribe-to-emotion-api-and-get-a-subscription-key"></a>Subskrybowanie API rozpoznawania emocji — warstwa i uzyskiwanie klucza subskrypcji  
Przed utworzeniem w przykładzie, należy subskrybować emocji interfejsu API, który jest częścią usługi kognitywnych firmy Microsoft. Zobacz [subskrypcje](https://azure.microsoft.com/try/cognitive-services/). W tym samouczku można zarówno klucza podstawowego i pomocniczego. Upewnij się, że należy stosować najlepsze rozwiązania dla zachowania klucz tajny klucza interfejsu API i zabezpieczenia.  

#### <a name="get-the-client-library-and-example"></a>Pobierz klienta biblioteki i przykładowe  
Możesz ją pobrać za pomocą biblioteki klienta interfejsu API rozpoznawania emocji — warstwa [SDK](https://www.github.com/microsoft/cognitive-emotion-windows). Plik zip pobranego ma zostać wyodrębniony do wybranego folderu, w przypadku wielu użytkowników wybierz folder, w programie Visual Studio 2015.
## <a name="Step1">Krok 1: Otwórz przykładu</a>
1.  Uruchom program Microsoft Visual Studio 2015 i kliknij przycisk **pliku**, wybierz pozycję **Otwórz**, następnie **projektu/rozwiązania**.
2.  Przejdź do folderu, w którym zapisano pobranych plików API rozpoznawania emocji — warstwa. Kliknij **emocji**, następnie **Windows**, a następnie **próbki WPF** folderu.
3.  Kliknij dwukrotnie, aby otworzyć plik programu Visual Studio 2015 Solution (.sln) o nazwie **EmotionAPI-WPF-Samples.sln**. Spowoduje to otwarcie rozwiązania w programie Visual Studio.

## <a name="Step2">Krok 2: Tworzenie przykładu</a>
1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy **odwołania** i wybierz **Zarządzaj pakietami NuGet**.

  ![Menedżer pakietów Nuget Otwórz](../Images/EmotionNuget.png)

2.  **Menedżera pakietów NuGet** zostanie otwarte okno. Najpierw wybierz **Przeglądaj** w lewym górnym rogu, a następnie w polu wyszukiwania wpisz "Newtonsoft.Json", wybierz **Newtonsoft.Json** pakiet, a następnie kliknij przycisk **zainstalować**.  

  ![Przejdź do pakietu NuGet](../Images/EmotionNugetBrowse.png)  

3.  Naciśnij klawisze Ctrl + Shift + B, lub kliknij przycisk **kompilacji** menu wstążki, następnie wybierz **Kompiluj rozwiązanie**.

## <a name="Step3">Krok 3: Uruchamianie przykładu</a>
1.  Po zakończeniu kompilacji, naciśnij klawisz **F5** lub kliknij przycisk **Start** menu wstążki do uruchamiania w przykładzie.
2.  Znajdź okno API rozpoznawania emocji — warstwa z **pole tekstowe** odczytywania "**Wklej swój klucz subskrypcji, aby uruchomić**". Wklej swój klucz subskrypcji w polu tekstowym, zgodnie z poniższym zrzucie ekranu. Możesz zachować klucz subskrypcji na komputerze lub laptop przez kliknięcie przycisku "Zapisz klucz". Jeśli chcesz usunąć klucz subskrypcji z systemu, kliknij przycisk "Usuń klucz", aby usunąć go z komputera lub laptop.
  
  ![Funkcje emocji — interfejs](../Images/EmotionKey.png)

3.  W obszarze "**wybierz scenariusz**"Kliknij, aby użyć jednego z dwóch scenariuszy"**wykryć emocji przy użyciu strumienia**"lub"**wykryć emocji przy użyciu adresu URL**", następnie postępuj zgodnie z instrukcjami ekran. Microsoft odbiera obrazów, przekazywanie i może używać ich do poprawy API rozpoznawania emocji — warstwa i powiązane usługi. Poprzez przesłanie obrazu, należy potwierdzić, że zostały wykonane naszych [Developer Kodeks postępowania](https://azure.microsoft.com/support/legal/developer-code-of-conduct/).
4.  Brak przykład obrazów do użycia w tej przykładowej aplikacji. Obrazy te można znaleźć na [repozytorium Github interfejsu API krój](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) w obszarze **danych** folderu. Należy pamiętać, że korzystanie z tych obrazów jest licencjonowane zgodnie z umową użyć odpowiedniego, co oznacza, że są one można używać do testowania w tym przykładzie, ale nie do ponownego publikowania.

## <a name="Review">Przejrzyj i Dowiedz się więcej</a>
Teraz, gdy masz uruchomioną aplikację Daj nam Przejrzyj jak ten przykład aplikacji integruje się z kognitywnych usług firmy Microsoft. Ułatwi o kontynuowanie budynku na tej aplikacji lub utworzyć własną aplikację przy użyciu interfejsu API rozpoznawania emocji — warstwa firmy Microsoft. 

Ta aplikacja przykład korzysta z biblioteki klienta interfejsu API rozpoznawania emocji — warstwa, cienkie C# klienta otokę dla interfejsu API rozpoznawania emocji — warstwa firmy Microsoft. Podczas tworzenia aplikacji przykład, jak opisano powyżej, masz biblioteki klienta od pakietu NuGet. Możesz przejrzeć kod źródłowy biblioteki klienta w folderze zatytułowany "[biblioteki klienta](https://github.com/Microsoft/Cognitive-Emotion-Windows/tree/master/ClientLibrary)" w obszarze **emocji**, **Windows**, **biblioteki klienta** , która jest częścią repozytorium pobrany plik wspomnianego powyżej [wymagania wstępne](#Prerequisites).
 
Możesz także znaleźć informacje o tym, jak użycie kodu biblioteki klienta w **Eksploratora rozwiązań**: w obszarze **EmotionAPI WPF_Samples**, rozwiń węzeł **DetectEmotionUsingStreamPage.xaml** do Zlokalizuj **DetectEmotionUsingStreamPage.xaml.cs**, która jest używana do przeglądania lokalnie przechowywanych plików lub rozwiń **DetectEmotionUsingURLPage.xaml** można znaleźć  **DetectEmotionUsingURLPage.xaml.cs**, który jest używany podczas przekazywania adres URL obrazu. Kliknij dwukrotnie. xaml.cs pliki, aby je otworzyć nowego systemu Windows w programie Visual Studio. 

Przegląd biblioteki klienckiej emocji pobiera użycia w naszym przykładzie aplikacji, Przyjrzyjmy się dwie wstawki kodu z **DetectEmotionUsingStreamPage.xaml.cs** i **DetectEmotionUsingURLPage.xaml.cs**. Każdy plik zawiera komentarze w kodzie wskazujący "Klucz przykładowy kod URUCHAMIA tutaj" i "Klucz przykładowy kod kończy się tutaj" Aby zlokalizować kod, który wstawki przedstawionym poniżej.

Interfejs API rozpoznawania emocji — warstwa jest możliwość pracy z adres URL obrazu lub danych binarnych obrazu (w formie strumień oktetu) jako danych wejściowych. Istnieją dwie opcje są podane poniżej. W obu przypadkach należy najpierw odnaleźć using dyrektywa, która umożliwia korzystanie z biblioteki klienta emocji. 
```csharp

            // ----------------------------------------------------------------------- 
            // KEY SAMPLE CODE STARTS HERE 
            // Use the following namespace for EmotionServiceClient 
            // ----------------------------------------------------------------------- 
            using Microsoft.ProjectOxford.Emotion; 
            using Microsoft.ProjectOxford.Emotion.Contract; 
            // ----------------------------------------------------------------------- 
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------- 
```
#### <a name="detectemotionusingurlpagexamlcs"></a>DetectEmotionUsingURLPage.xaml.cs 

Następujący fragment kodu przedstawia sposób korzystania z biblioteki klienta można przesłać klucz subskrypcji i adres URL zdjęć do usługi interfejsu API rozpoznawania emocji — warstwa. 

```csharp

            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // -----------------------------------------------------------------------

            window.Log("EmotionServiceClient is created");

            //
            // Create Project Oxford Emotion API Service client
            //
            EmotionServiceClient emotionServiceClient = new EmotionServiceClient(subscriptionKey);

            window.Log("Calling EmotionServiceClient.RecognizeAsync()...");
            try
            {
                //
                // Detect the emotions in the URL
                //
                Emotion[] emotionResult = await emotionServiceClient.RecognizeAsync(url);
                return emotionResult;
            }
            catch (Exception exception)
            {
                window.Log("Detection failed. Please make sure that you have the right subscription key and proper URL to detect.");
                window.Log(exception.ToString());
                return null;
            }
            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE
            // -----------------------------------------------------------------------
```
#### <a name="detectemotionusingstreampagexamlcs"></a>DetectEmotionUsingStreamPage.xaml.cs 

Poniżej przedstawiono sposób przesłać klucz subskrypcji i przechowywane lokalnie obrazu do interfejsu API rozpoznawania emocji — warstwa. 


```csharp


            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // -----------------------------------------------------------------------

            //
            // Create Project Oxford Emotion API Service client
            //
            EmotionServiceClient emotionServiceClient = new EmotionServiceClient(subscriptionKey);

            window.Log("Calling EmotionServiceClient.RecognizeAsync()...");
            try
            {
                Emotion[] emotionResult;
                using (Stream imageFileStream = File.OpenRead(imageFilePath))
                {
                    //
                    // Detect the emotions in the URL
                    //
                    emotionResult = await emotionServiceClient.RecognizeAsync(imageFileStream);
                    return emotionResult;
                }
            }
            catch (Exception exception)
            {
                window.Log(exception.ToString());
                return null;
            }
            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE
            // -----------------------------------------------------------------------
```
<!--
## <a name="Related">Related Topics</a>
[Emotion API Overview](.)
-->
