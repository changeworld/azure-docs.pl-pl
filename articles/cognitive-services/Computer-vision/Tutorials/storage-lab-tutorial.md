---
title: 'Samouczek: Generowanie metadanych obrazów platformy Azure'
titleSuffix: Azure Cognitive Services
description: W tym samouczku dowiesz się, jak zintegrować usługę Azure Computer Vision z aplikacją sieci web w celu wygenerowania metadanych obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 7c83350dbecaf20e9b35f159b2c01824777bc665
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973717"
---
# <a name="tutorial-use-computer-vision-to-generate-image-metadata-in-azure-storage"></a>Samouczek: Generowanie metadanych obrazu w usłudze Azure Storage za pomocą funkcji Przetwarzania obrazów

W tym samouczku dowiesz się, jak zintegrować usługę Azure Computer Vision z aplikacją sieci web w celu wygenerowania metadanych dla przekazanych obrazów. Jest to przydatne w przypadku scenariuszy [zarządzania zasobami cyfrowymi (DAM),](../Home.md#computer-vision-for-digital-asset-management) takich jak to, że firma chce szybko wygenerować opisowe napisy lub słowa kluczowe z wyszukujem dla wszystkich swoich obrazów.

Pełny przewodnik dotyczący aplikacji można znaleźć w [laboratorium usług Azure Storage i Cognitive Services](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md) na platformie GitHub — ten samouczek dotyczy ćwiczenia 5 z tego laboratorium. Możesz utworzyć pełną aplikację, wykonując każdy krok, ale jeśli chcesz tylko dowiedzieć się, jak zintegrować wizję komputera z istniejącą aplikacją internetową, przeczytaj tutaj.

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zasobu przetwarzania obrazów na platformie Azure
> * Wykonywanie analizy obrazu na obrazach usługi Azure Storage
> * Dołączanie metadanych do obrazów usługi Azure Storage
> * Sprawdzanie metadanych obrazów za pomocą Eksploratora usługi Azure Storage

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem. 

## <a name="prerequisites"></a>Wymagania wstępne

- Program [Visual Studio 2017 Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) lub nowszy z zainstalowanymi pakietami roboczymi „Tworzenie aplikacji na platformie ASP.NET i aplikacji internetowych” oraz „Tworzenie aplikacji na platformie Azure”.
- Konto usługi Azure Storage z kontenerem obiektów blob skonfigurowanym do przechowywania obrazów (wykonaj [ćwiczenia 1 laboratorium usługi Azure Storage,](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise1) jeśli potrzebujesz pomocy w tym kroku).
- Narzędzie Eksplorator usługi Azure Storage (wykonaj [ćwiczenie 2 z laboratorium usługi Azure Storage](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise2), jeśli potrzebujesz pomocy dotyczącej tego kroku).
- Aplikacja internetowa platformy ASP.NET z dostępem do usługi Azure Storage (wykonaj [ćwiczenie 3 z laboratorium usługi Azure Storage](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3), aby szybko utworzyć taką aplikację).

## <a name="create-a-computer-vision-resource"></a>Tworzenie zasobu przetwarzania obrazów

Musisz utworzyć zasób usługi Computer Vision dla swojego konta platformy Azure; ten zasób zarządza dostępem do usługi Przetwarzania Obrazów platformy Azure. 

1. Postępuj zgodnie z instrukcjami w [Tworzenie zasobu usług Azure Cognitive Services,](../../cognitive-services-apis-create-account.md) aby utworzyć zasób przetwarzania.

1. Następnie przejdź do menu grupy zasobów i kliknij subskrypcję interfejsu API usługi Computer Vision, która została właśnie utworzona. Skopiuj adres URL z obszaru **Punkt końcowy** do miejsca, z którego będzie go można za chwilę łatwo uzyskać. Następnie kliknij pozycję **Pokaż klucze dostępu**.

    ![Strona portalu Azure z opisanym łączem adresu URL punktu końcowego i kluczy dostępu](../Images/copy-vision-endpoint.png)
    
    [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]


1. W następnym oknie skopiuj wartość **KLUCZ 1** do schowka.

    ![Okno dialogowe Zarządzanie klawiszami z nakreślonym przyciskiem kopiowania](../Images/copy-vision-key.png)

## <a name="add-computer-vision-credentials"></a>Dodawanie poświadczeń przetwarzania obrazów

Następnie dodasz wymagane poświadczenia do aplikacji, aby mogła uzyskać dostęp do zasobów usługi Computer Vision

Otwórz aplikację internetową platformy ASP.NET w programie Visual Studio i przejdź do pliku **Web.config** w folderze głównym projektu. Dodaj następujące instrukcje `<appSettings>` do sekcji pliku, zastępując `VISION_KEY` kluczem skopiowanym w `VISION_ENDPOINT` poprzednim kroku i adresem URL zapisanym w poprzednim kroku.

```xml
<add key="SubscriptionKey" value="VISION_KEY" />
<add key="VisionEndpoint" value="VISION_ENDPOINT" />
```

Następnie w Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt i użyj polecenia **Zarządzaj pakietami NuGet**, aby zainstalować pakiet **Microsoft.Azure.CognitiveServices.Vision.ComputerVision**. Ten pakiet zawiera typy potrzebne do wywołania interfejsu API przetwarzania obrazów.

## <a name="add-metadata-generation-code"></a>Dodawanie kodu generowania metadanych

Następnie dodasz kod, który faktycznie wykorzystuje usługę Przetwarzania Obrazów do tworzenia metadanych obrazów. Te kroki dotyczą aplikacji platformy ASP.NET z laboratorium, ale możesz dostosować je do własnej aplikacji. W tym momencie ważne jest, że masz aplikację internetową platformy ASP.NET, która potrafi przekazywać obrazy do kontenera usługi Azure Storage, odczytywać obrazy z tego kontenera i wyświetlać je w widoku. Jeśli nie masz pewności co do tego kroku, najlepiej wykonać [ćwiczenie 3 laboratorium usługi Azure Storage Lab.](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3) 

1. Otwórz plik *HomeController.cs* w folderze **Kontrolery** projektu i dodaj następujące instrukcje `using` w górnej części pliku:

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
    ```

1. Następnie przejdź do metody **Upload**. Ta metoda konwertuje obrazy i przekazuje je do magazynu obiektów blob. Dodaj następujący kod bezpośrednio za blokiem, który rozpoczyna się od ciągu `// Generate a thumbnail` (lub na końcu procesu image-blob-creation). Ten kod pobiera obiekt blob zawierający obraz (`photo`) i używa usługi przetwarzania obrazów, aby wygenerować opis tego obrazu. Interfejs API przetwarzania obrazów generuje także listę słów kluczowych dla obrazu. Wygenerowany opis i słowa kluczowe są przechowywane w metadanych obiektu blob, aby można było je później pobrać.

    ```csharp
    // Submit the image to Azure's Computer Vision API
    ComputerVisionClient vision = new ComputerVisionClient(
        new ApiKeyServiceClientCredentials(ConfigurationManager.AppSettings["SubscriptionKey"]),
        new System.Net.Http.DelegatingHandler[] { });
    vision.Endpoint = ConfigurationManager.AppSettings["VisionEndpoint"];

    VisualFeatureTypes[] features = new VisualFeatureTypes[] { VisualFeatureTypes.Description };
    var result = await vision.AnalyzeImageAsync(photo.Uri.ToString(), features);

    // Record the image description and tags in blob metadata
    photo.Metadata.Add("Caption", result.Description.Captions[0].Text);

    for (int i = 0; i < result.Description.Tags.Count; i++)
    {
        string key = String.Format("Tag{0}", i);
        photo.Metadata.Add(key, result.Description.Tags[i]);
    }

    await photo.SetMetadataAsync();
    ```

1. Następnie przejdź do **Index** metody w tym samym pliku. Ta metoda wylicza przechowywane obiekty blob obrazu w kontenerze docelowego obiektu blob (jako wystąpienia **IListBlobItem)** i przekazuje je do widoku aplikacji. Zastąp blok `foreach` w tej metodzie następującym kodem. Ten kod wywołuje funkcję **CloudBlockBlob.FetchAttributes**, aby pobrać metadane dołączone do każdego obiektu blob. Wyodrębnia z metadanych opis wygenerowany komputerowo (`caption`) i dodaje go do obiektu **BlobInfo**, który jest przekazywany do widoku.
    
    ```csharp
    foreach (IListBlobItem item in container.ListBlobs())
    {
        var blob = item as CloudBlockBlob;
    
        if (blob != null)
        {
            blob.FetchAttributes(); // Get blob metadata
            var caption = blob.Metadata.ContainsKey("Caption") ? blob.Metadata["Caption"] : blob.Name;
    
            blobs.Add(new BlobInfo()
            {
                ImageUri = blob.Uri.ToString(),
                ThumbnailUri = blob.Uri.ToString().Replace("/photos/", "/thumbnails/"),
                Caption = caption
            });
        }
    }
    ```

## <a name="test-the-app"></a>Testowanie aplikacji

Zapisz wprowadzone zmiany w programie Visual Studio i naciśnij klawisze **Ctrl+F5**, aby uruchomić aplikację w przeglądarce. Użyj aplikacji, aby przekazać kilka obrazów z folderu „photos” w zasobach laboratorium lub z własnego folderu. Kiedy umieścisz kursor na jednym z obrazów w widoku, powinno zostać wyświetlone okno etykietki narzędzia z wygenerowanym komputerowo podpisem obrazu.

![Podpis wygenerowany przez komputer](../Images/thumbnail-with-tooltip.png)

Aby wyświetlić wszystkie dołączone metadane, użyj Eksploratora usługi Azure Storage w celu wyświetlenia kontenera magazynu, którego używasz na potrzeby obrazów. Kliknij prawym przyciskiem myszy dowolny obiekt blob w kontenerze i wybierz pozycję **Właściwości**. W oknie dialogowym zobaczysz listę par klucz-wartość. Wygenerowany komputerowo opis obrazu jest przechowywany w elemencie „Caption”, a słowa kluczowe wyszukiwania są przechowywane w elementach „Tag0”, „Tag1” i tak dalej. Gdy skończysz, kliknij przycisk **Anuluj**, aby zamknąć okno dialogowe.

![Okno dialogowe Właściwości obrazu z wyświetlonymi znacznikami metadanych](../Images/blob-metadata.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz kontynuować pracę nad swoją aplikacją internetową, zobacz sekcję [Następne kroki](#next-steps). Jeśli nie chcesz dalej używać tej aplikacji, usuń wszystkie zasoby specyficzne dla aplikacji. Aby usunąć zasoby, można usunąć grupę zasobów zawierającą subskrypcję usługi Azure Storage i zasób usługi Computer Vision. Spowoduje to usunięcie konta magazynu, przekazanych do niego obiektów blob i zasobu usługi App Service wymaganego do nawiązywania połączenia z aplikacją internetową platformy ASP.NET. 

Aby usunąć grupę zasobów, otwórz kartę **Grupy zasobów** w portalu, przejdź do grupy zasobów używanej dla tego projektu i kliknij pozycję **Usuń grupę zasobów** u góry widoku. Zostaniesz poproszony o wpisanie nazwy grupy zasobów, aby potwierdzić, że chcesz ją usunąć, ponieważ po usunięciu nie można odzyskać grupy zasobów.

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurowano usługę przetwarzania obrazów usługi Azure w istniejącej aplikacji sieci web, aby automatycznie generować podpisy i słowa kluczowe dla obrazów obiektów blob podczas ich przekazywania. Następnie zapoznaj się z ćwiczeniem 6 w laboratorium usługi Azure Storage, aby dowiedzieć się, jak dodać funkcję wyszukiwania do aplikacji internetowej. Ta funkcja wykorzystuje słowa kluczowe, które generuje usługa przetwarzania obrazów.

> [!div class="nextstepaction"]
> [Dodawanie funkcji wyszukiwania do aplikacji](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise6)
