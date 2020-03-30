---
title: Animowane wykrywanie postaci za pomocą indeksatora wideo
titleSuffix: Azure Media Services
description: W tym temacie pokazano, jak używać animowanego wykrywania znaków za pomocą indeksatora wideo.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: af608dcfbb5d98cf3116de4e14dc12bf6facb97b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989913"
---
# <a name="animated-character-detection-preview"></a>Animowane wykrywanie znaków (podgląd)

Indeksator wideo usługi Azure Media Services obsługuje wykrywanie, grupowanie i rozpoznawanie znaków w animowanej zawartości poprzez integrację z [niestandardową wizją usług Cognitive Services.](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) Ta funkcja jest dostępna zarówno za pośrednictwem portalu, jak i za pośrednictwem interfejsu API.

Po przesłaniu animowanego filmu z określonym modelem animacji indeksator wideo wyodrębnia klatki kluczowe, wykrywa animowane postacie w tych klatkach, grupuje podobny znak i wybiera najlepszą próbkę. Następnie wysyła zgrupowane znaki do niestandardowej wizji, która identyfikuje znaki na podstawie modeli, na których został przeszkolony. 

Przed rozpoczęciem treningu modelu znaki są wykrywane bezimiennie. Podczas dodawania nazw i trenowania modelu indeksator wideo rozpozna znaki i odpowiednio je nadadzą.

## <a name="flow-diagram"></a>Diagram przepływu

Na poniższym diagramie przedstawiono przepływ animowanego procesu wykrywania znaków.

![Diagram przepływu](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>Konta

W zależności od typu konta indeksatora wideo dostępne są różne zestawy funkcji. Aby uzyskać informacje na temat łączenia konta z platformą Azure, zobacz [Tworzenie konta indeksatora wideo połączonego z platformą Azure](connect-to-azure.md).

* Konto próbne: Indeksator wideo używa wewnętrznego konta Usługi Custom Vision do utworzenia modelu i połączenia go z kontem indeksatora wideo. 
* Konto płatne: łączysz konto Custom Vision z kontem Video Indexer (jeśli jeszcze go nie masz, musisz najpierw utworzyć konto).

### <a name="trial-vs-paid"></a>Wersja próbna a płatna

|Funkcjonalność|Wersja próbna|Wypłacane|
|---|---|---|
|Konto Usługi Custom Vision|Zarządzane za kulisami przez Video Indexer. |Twoje konto Custom Vision jest połączone z indeksatorem wideo.|
|Liczba modeli animacji|Jeden|Do 100 modeli na konto (ograniczenie Custom Vision).|
|Trenowanie modelu|Indeksator wideo trenuje model dla nowych znaków dodatkowe przykłady istniejących znaków.|Właściciel konta trenuje model, gdy są one gotowe do wprowadzania zmian.|
|Zaawansowane opcje w uzywce niestandardowej|Brak dostępu do portalu Custom Vision.|Modele można dostosować samodzielnie w portalu Niestandardowej wizji.|

## <a name="use-the-animated-character-detection-with-portal"></a>Korzystanie z animowanego wykrywania znaków w portalu 

W tej sekcji opisano kroki, które należy wykonać, aby rozpocząć korzystanie z animowanego modelu wykrywania znaków. 

Ponieważ na kontach próbnych integracja usługi Custom Vision jest zarządzana przez indeksator wideo, można rozpocząć tworzenie i używanie animowanego modelu znaków i pominąć następującą sekcję ("Połącz konto Usługi Custom Vision").

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>Połącz swoje konto Custom Vision (tylko konta płatne)

Jeśli posiadasz płatne konto indeksatora wideo, musisz najpierw połączyć konto Usługi Custom Vision. Jeśli nie masz jeszcze konta Custom Vision, utwórz je. Aby uzyskać więcej informacji, zobacz [Niestandardowa wizja](../../cognitive-services/custom-vision-service/home.md).

> [!NOTE]
> Oba konta muszą znajdować się w tym samym regionie. Integracja usługi Custom Vision nie jest obecnie obsługiwana w regionie Japonii.

#### <a name="connect-a-custom-vision-account-with-api"></a>Łączenie konta usługi Custom Vision za pomocą interfejsu API 

Wykonaj następujące kroki, aby połączyć konto Usługi Custom Vision z indeksatorem wideo lub zmienić konto Usługi Custom Vision, które jest obecnie połączone z indeksatorem wideo:

1. Przejdź do [www.customvision.ai](https://www.customvision.ai) i zaloguj się.
1. Skopiuj następujące klawisze: 

    * Klucz szkolenia (dla zasobu szkoleniowego)
    * Klucz przewidywania (dla zasobu prognozowania)
    * Endpoint 
    * Identyfikator zasobu prognozowania
    
    > [!NOTE]
    > Aby zapewnić wszystkie klucze, musisz mieć dwa oddzielne zasoby w udziale Niestandardowej wizji, jeden do szkolenia i jeden do przewidywania.
1. Przeglądaj i loguj się do [indeksatora wideo](https://vi.microsoft.com/).
1. Kliknij znak zapytania w prawym górnym rogu strony i wybierz pozycję **Odwołanie do interfejsu API**.
1. Upewnij się, że subskrybujesz usługę API Management, klikając kartę **Produkty.** Jeśli masz podłączony interfejs API, możesz przejść do następnego kroku, w przeciwnym razie subskrybuj. 
1. W portalu dla deweloperów kliknij **pozycję Pełne odwołanie do interfejsu API** i przejdź do strony **Operacje**.  
1. Wybierz **pozycję Połącz konto niestandardowe vision (PREVIEW)** i kliknij pozycję **Wypróbuj**.
1. Wypełnij wymagane pola, a także token dostępu i kliknij przycisk **Wyślij**. 

    Aby uzyskać więcej informacji na temat uzyskiwania tokenu dostępu indeksatora wideo, przejdź do [portalu dla deweloperów](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Account-Access-Token?)i zobacz [odpowiednią dokumentację](video-indexer-use-apis.md#obtain-access-token-using-the-authorization-api).  
1. Gdy połączenie zwróci odpowiedź 200 OK, Twoje konto zostanie połączone.
1. Aby zweryfikować połączenie, przejdź do portalu [Video Indexer):](https://vi.microsoft.com/)
1. Kliknij przycisk **Dostosowywania modelu zawartości** w prawym górnym rogu.
1. Przejdź do karty **Animowane postacie.**
1. Po kliknięciu na Zarządzaj modelami w ujamie"**, zostaniesz przeniesiony na konto Custom Vision, które właśnie połączyłeś.

> [!NOTE]
> Obecnie obsługiwane są tylko modele, które zostały utworzone za pośrednictwem indeksatora wideo. Modele utworzone za pośrednictwem usługi Custom Vision nie będą dostępne. Ponadto najlepszym rozwiązaniem jest edytowanie modeli, które zostały utworzone za pośrednictwem indeksatora wideo tylko za pośrednictwem platformy indeksatora wideo, ponieważ zmiany wprowadzone za pośrednictwem usługi Custom Vision mogą powodować niezamierzone wyniki.

### <a name="create-an-animated-characters-model"></a>Tworzenie animowanego modelu postaci

1. Przejdź do witryny internetowej [Video Indexer](https://vi.microsoft.com/) i zaloguj się.
1. Kliknij przycisk dostosowywania modelu zawartości w prawym górnym rogu strony.

    ![Dostosowywanie modelu zawartości](./media/animated-characters-recognition/content-model-customization.png)
1. Przejdź do karty **Animowane znaki** w sekcji dostosowywania modelu.
1. Kliknij **dodaj model**.
1. Nazwij model i kliknij enter, aby zapisać nazwę.

> [!NOTE]
> Najlepszym rozwiązaniem jest, aby mieć jeden niestandardowy model wizji dla każdej serii animowanej. 

### <a name="index-a-video-with-an-animated-model"></a>Indeksowanie wideo za pomocą animowanego modelu

1. Kliknij przycisk **Prześlij** z górnego menu.
1. Wybierz film do przesłania (z pliku lub adresu URL).
1. Kliknij **opcje zaawansowane**.
1. W **obszarze Osoby / Animowane postacie** wybierz pozycję Modele **animacji**.
1. Jeśli masz jeden model zostanie wybrany automatycznie, a jeśli masz wiele modeli, możesz wybrać odpowiedni z menu rozwijanego.
1. Kliknij na upload.
1. Po zindeksja wideo w okienku **Statystyka** zostaną wyświetlone wykryte znaki w sekcji **Animowane postacie.**

> [!NOTE] 
> Przed tagowaniem i szkoleniem modelu wszystkie animowane postacie zostaną nazwane "Nieznane #X". Po przeszkoleniu modelu zostaną one również rozpoznane.

### <a name="customize-the-animated-characters-models"></a>Dostosowywanie modeli animowanych postaci

1. Oznacz i trenuj model.

    1. Oznacz wykryty znak, edytując jego nazwę. Gdy znak jest przeszkolony w modelu, zostanie rozpoznany jako następny film zindeksowany z tym modelem. 
    1. Aby oznaczyć animowaną postać w filmie, przejdź do karty **Statystyki** i kliknij przycisk **Edytuj** w prawym górnym rogu okna.
    1. W okienku **Statystyka** kliknij dowolną z wykrytych animowanych postaci i zmień ich nazwy z "Nieznane #X" (lub nazwę, która została wcześniej przypisana do znaku).
    1. Po wpisaniu nowej nazwy kliknij ikonę wyboru obok nowej nazwy. Spowoduje to zapisanie nowej nazwy w modelu w indeksatorze wideo.
    1. Po zakończeniu edycji wszystkich nazw, które chcesz, należy trenować modelu.

        Otwórz stronę dostosowywania i kliknij kartę **Animowane postacie,** a następnie kliknij przycisk **Trenuj,** aby trenować model.
         
        Jeśli masz płatne konto, możesz kliknąć link **Zarządzaj modelami w wizji klienta** (jak pokazano poniżej). Następnie zostaniesz przekierowany na stronę modelu w **uzywce Niestandardowej wizji**.
 
        ![Dostosowywanie modelu zawartości](./media/animated-characters-recognition/content-model-customization-tab.png)

     1. Po przeszkoleniu każdy film wideo, który zostanie zindeksowany lub ponownie zindeksowany z tym modelem, rozpozna wyszkolone znaki. 
    Płatne konta, które mają dostęp do swojego konta Custom Vision, mogą tam wyświetlać modele i oznaczone obrazy. Dowiedz się więcej o [ulepszaniu klasyfikatora w aplikacji Custom Vision](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-improving-your-classifier).

1. Usuń animowaną postać.

    1. Aby usunąć animowaną postać ze statystyk filmu, przejdź na kartę **Statystyka** i kliknij przycisk **Edytuj** w prawym górnym rogu okna.
    1. Wybierz animowaną postać, a następnie kliknij przycisk **Usuń** pod ich nazwą.

    > [!NOTE]
    > Spowoduje to usunięcie wglądu z tego filmu, ale nie wpłynie na model.

1. Usuwanie modelu.

    1. Kliknij przycisk **Dostosowywania modelu zawartości** w górnym menu i przejdź do karty **Animowane znaki.**
    1. Kliknij ikonę wielokropek po prawej stronie modelu, który chcesz usunąć, a następnie przycisk usuń.
    
    * Płatne konto: model zostanie odłączony od indeksatora wideo i nie będzie można go ponownie połączyć.
    * Konto próbne: model zostanie również usunięty z wizji celnej. 
    
        > [!NOTE]
        > Na koncie próbnym masz tylko jeden model, którego możesz użyć. Po usunięciu nie można trenować innych modeli.

## <a name="use-the-animated-character-detection-with-api"></a>Korzystanie z animowanego wykrywania znaków za pomocą interfejsu API 

1. Połącz konto usługi Custom Vision.

    Jeśli posiadasz płatne konto indeksatora wideo, musisz najpierw połączyć konto Usługi Custom Vision. <br/>
    Jeśli nie masz jeszcze konta Custom Vision, utwórz je. Aby uzyskać więcej informacji, zobacz [Niestandardowa wizja](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/home).

    [Połącz swoje konto Custom Vision za pomocą interfejsu API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Connect-Custom-Vision-Account?tags=&pattern=&groupBy=tag).
1. Utwórz animowany model postaci.

    Użyj interfejsu API [modelu animacji tworzenia.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Animation-Model?&groupBy=tag)
1. Indeksuj lub ponownie indeksuj film.

    Użyj [ponownego indeksowania](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) interfejsu API. 
1. Dostosuj animowane modele postaci.

    Użyj interfejsu API [modelu animacji pociągu.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Train-Animation-Model?&groupBy=tag)

### <a name="view-the-output"></a>Wyświetlanie danych wyjściowych

Zobacz animowane postacie w wygenerowanym pliku JSON.

```json
"animatedCharacters": [
    {
    "videoId": "e867214582",
    "confidence": 0,
    "thumbnailId": "00000000-0000-0000-0000-000000000000",
    "seenDuration": 201.5,
    "seenDurationRatio": 0.3175,
    "isKnownCharacter": true,
    "id": 4,
    "name": "Bunny",
    "appearances": [
        {
            "startTime": "0:00:52.333",
            "endTime": "0:02:02.6",
            "startSeconds": 52.3,
            "endSeconds": 122.6
        },
        {
            "startTime": "0:02:40.633",
            "endTime": "0:03:16.6",
            "startSeconds": 160.6,
            "endSeconds": 196.6
        },
    ]
    },
]
```

## <a name="limitations"></a>Ograniczenia

* Obecnie funkcja "identyfikacji animacji" nie jest obsługiwana w regionie Azji Wschodniej.
* Znaki, które wydają się być małe lub daleko w filmie, mogą nie być prawidłowo zidentyfikowane, jeśli jakość filmu jest niska.
* Zaleca się użycie modelu na zestaw animowanych postaci (na przykład w serialu animowanym).

## <a name="next-steps"></a>Następne kroki

[Omówienie usługi Video Indexer](video-indexer-overview.md)
