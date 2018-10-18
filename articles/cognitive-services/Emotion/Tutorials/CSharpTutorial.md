---
title: 'Samouczek: rozpoznawanie emocji na twarzy na obrazie — interfejs API rozpoznawania emocji, C#'
titlesuffix: Azure Cognitive Services
description: Zapoznaj się z podstawową aplikacją systemu Windows, która umożliwia rozpoznawanie emocji wyrażonych na twarzach na obrazie.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: tutorial
ms.date: 01/23/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: f3a84a68718fba29e2a4b2fae057e68976119c95
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237028"
---
# <a name="tutorial-recognize-emotions-on-a-face-in-an-image"></a>Samouczek: rozpoznawanie emocji na twarzy na obrazie.

> [!IMPORTANT]
> Interfejs API rozpoznawania emocji zostanie wycofany w dniu 15 lutego 2019 r. Rozpoznawanie emocji jest teraz ogólnie dostępne jako część [interfejsu API rozpoznawania twarzy](https://docs.microsoft.com/azure/cognitive-services/face/). 

Zapoznaj się z podstawową aplikacją systemu Windows, która korzysta z interfejsu API rozpoznawania emocji w celu rozpoznawania emocji wyrażonych na twarzach na obrazie. Poniższy przykład umożliwia przesłanie adresu URL obrazu lub lokalnego pliku. Ten przykład typu „open source” może służyć jako szablon do tworzenia własnej aplikacji systemu Windows korzystającej z interfejsu API rozpoznawania emocji i platformy WPF (Windows Presentation Foundation), która jest częścią programu .NET Framework.

## <a name="Prerequisites">Wymagania wstępne</a>
#### <a name="platform-requirements"></a>Wymagania dotyczące platformy  
Poniższy przykład jest przeznaczony dla programu .NET Framework i został opracowany za pomocą [programu Visual Studio 2015 w wersji Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).  

#### <a name="subscribe-to-emotion-api-and-get-a-subscription-key"></a>Subskrybowanie interfejsu API rozpoznawania emocji i uzyskiwanie klucza subskrypcji  
Przed utworzeniem przykładu musisz zasubskrybować interfejs API rozpoznawania emocji, który jest częścią usług Microsoft Cognitive Services. Zobacz [Subskrypcje](https://azure.microsoft.com/try/cognitive-services/). W tym samouczku można używać zarówno klucza podstawowego, jak i pomocniczego. Pamiętaj, aby wdrożyć najlepsze rozwiązania umożliwiające bezpieczne przechowywanie klucza interfejsu API.  

#### <a name="get-the-client-library-and-example"></a>Pobieranie przykładu i biblioteki klienta  
Bibliotekę klienta interfejsu API rozpoznawania emocji można pobrać za pośrednictwem [zestawu SDK](https://www.github.com/microsoft/cognitive-emotion-windows). Pobrany plik zip trzeba rozpakować do dowolnego folderu. Wielu użytkowników wybiera folder Visual Studio 2015.
## <a name="Step1">Krok 1. Otwieranie przykładu</a>
1.  Uruchom program Microsoft Visual Studio 2015, a następnie kliknij pozycje **Plik**, **Otwórz** i **Projekt/rozwiązanie**.
2.  Przejdź do folderu, w którym zapisano pobrane pliki przykładu Emotion API. Kliknij pozycje **Emotion** i **Windows**, a następnie kliknij folder **Sample-WPF**.
3.  Kliknij dwukrotnie, aby otworzyć plik rozwiązania programu Visual Studio 2015 (.sln) o nazwie **EmotionAPI-WPF-Samples.sln**. Spowoduje to otwarcie rozwiązania w programie Visual Studio.

## <a name="Step2">Krok 2. Kompilowanie przykładu</a>
1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy pozycję **Odwołania**, a następnie wybierz polecenie **Zarządzaj pakietami NuGet**.

  ![Otwieranie menedżera pakietów NuGet](../Images/EmotionNuget.png)

2.  Pojawi się okno **Menedżer pakietów NuGet**. Najpierw wybierz pozycję **Przeglądaj** w lewym górnym rogu, a następnie w polu wyszukiwania wpisz tekst „Newtonsoft.Json”, wybierz pakiet **Newtonsoft.Json** i kliknij pozycję **Zainstaluj**.  

  ![Przechodzenie do pakietu NuGet](../Images/EmotionNugetBrowse.png)  

3.  Naciśnij klawisze Ctrl+Shift+B lub kliknij polecenie **Kompiluj** w menu wstążki, a następnie wybierz pozycję **Kompiluj rozwiązanie**.

## <a name="Step3">Krok 3. Uruchamianie przykładu</a>
1.  Po ukończeniu kompilacji naciśnij klawisz **F5** lub kliknij przycisk **Start** w menu wstążki, aby uruchomić przykład.
2.  Znajdź okno Emotion API z **polem tekstowym** zawierającym ciąg „**Paste your subscription key here to start**” (Aby rozpocząć, wklej tutaj swój klucz subskrypcji). Wklej klucz subskrypcji w polu tekstowym, jak pokazano na poniższym zrzucie ekranu. Możesz zachować klucz subskrypcji na komputerze lub laptopie, klikając przycisk „Save Key” (Zapisz klucz). Jeśli chcesz usunąć klucz subskrypcji z systemu, kliknij przycisk „Delete Key” (Usuń klucz). Klucz zostanie usunięty z komputera lub laptopa.

  ![Interfejs funkcji rozpoznawania emocji](../Images/EmotionKey.png)

3.  W obszarze „**Select Scenario**” (Wybierz scenariusz) wybierz scenariusz „**Detect emotion using a stream**” (Wykryj emocje przy użyciu strumienia) lub „**Detect emotion using a URL**” (Wykryj emocje przy użyciu adresu URL), następnie postępuj zgodnie z instrukcjami na ekranie. Przekazywane obrazy są wysyłane do firmy Microsoft. Mogą one zostać użyte w celu udoskonalenia interfejsu API rozpoznawania emocji i powiązanych usług. Przesłanie obrazu jest równoznaczne z potwierdzeniem, że stosowano się do zasad naszego [Kodeksu postępowania dewelopera](https://azure.microsoft.com/support/legal/developer-code-of-conduct/).
4.  Dla tej aplikacji są dostępne przykładowe obrazy. Można je znaleźć w [repozytorium Github interfejsu API rozpoznawania twarzy](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) w folderze **Data**. Należy pamiętać, że używanie tych obrazów podlega umowie dozwolonego użytkowania. Oznacza to, że można ich używać do testowania przykładu, ale nie można ich publikować.

## <a name="Review">Przeglądanie i dodatkowe informacje</a>
Gdy już mamy uruchomioną aplikację, sprawdźmy, jak można ją zintegrować z usługami Microsoft Cognitive Services. Ułatwi to zarówno rozbudowę tej aplikacji, jak i opracowywanie własnej aplikacji korzystającej z interfejsu API rozpoznawania emocji firmy Microsoft.

Ta przykładowa aplikacja korzysta z biblioteki klienta interfejsu API rozpoznawania emocji — uproszczonej otoki klienta w języku C# przeznaczonej dla interfejsu API rozpoznawania emocji firmy Microsoft. Wykonanie kompilacji przykładowej aplikacji w sposób opisany powyżej pozwala uzyskać bibliotekę klienta z pakietu NuGet. Kod źródłowy biblioteki klienta można przejrzeć w folderze o nazwie „[Client Library](https://github.com/Microsoft/Cognitive-Emotion-Windows/tree/master/ClientLibrary)” w obszarze **Emotion**, **Windows**, **Client Library**, który jest częścią pobranego repozytorium plików wskazanego w powyższej sekcji [Wymagania wstępne](#Prerequisites).

Informacje o tym, jak używać kodu biblioteki klienta można również znaleźć w **Eksploratorze rozwiązań**. W obszarze **EmotionAPI-WPF_Samples** rozwiń węzeł **DetectEmotionUsingStreamPage.xaml**, aby uzyskać dostęp do pliku **DetectEmotionUsingStreamPage.xaml.cs**, który służy do znajdowania lokalnego pliku, lub węzeł **DetectEmotionUsingURLPage.xaml**, aby uzyskać dostęp do pliku **DetectEmotionUsingURLPage.xaml.cs**, który służy do przekazywania adresu URL obrazu. Dwukrotne kliknięcie plików .xaml.cs pozwala je otworzyć w nowych oknach w programie Visual Studio.

W ramach zapoznawania się ze sposobem używania biblioteki klienta Emotion w przykładowej aplikacji przyjrzyjmy się dwóm fragmentom kodu z plików **DetectEmotionUsingStreamPage.xaml.cs** i **DetectEmotionUsingURLPage.xaml.cs**. Każdy plik zawiera komentarze o treści „KEY SAMPLE CODE STARTS HERE” (Przykładowy kod klucza zaczyna się tutaj) i „KEY SAMPLE CODE ENDS HERE” (Przykładowy kod klucza kończy się tutaj), które ułatwiają znalezienie fragmentów przedstawionych poniżej.

Interfejs API rozpoznawania emocji współdziała z danymi wejściowymi w postaci adresu URL obrazu lub binarnymi danymi obrazu (w formie strumienia oktetowego). Przegląd tych dwóch opcji można znaleźć poniżej. W obu przypadkach najpierw należy znaleźć dyrektywę using, która pozwala używać biblioteki klienta Emotion.
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

Ten fragment kodu przedstawia użycie biblioteki klienta w celu przesłania klucza subskrypcji i adresu URL zdjęcia do usługi interfejsu API rozpoznawania emocji.

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

Poniżej pokazano sposób przesyłania klucza subskrypcji i lokalnego obrazu do interfejsu API rozpoznawania emocji.


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
