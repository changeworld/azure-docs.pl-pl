---
title: 'Szybki start: Wykrywanie twarzy na obrazie przy użyciu zestawu .NET SDK interfejsu API rozpoznawania twarzy platformy Azure'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start użyjesz zestawu SDK interfejsu API rozpoznawania twarzy platformy Azure i języka C# do wykrywania twarzy na obrazie.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: bf4d11a18932d20e7dcc7580ebe5aa4e060c5a88
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606774"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-net-sdk"></a>Szybki start: Wykrywanie twarzy na obrazie przy użyciu zestawu .NET SDK rozpoznawania twarzy

W tym przewodniku Szybki start użyjesz zestawu SDK interfejsu API rozpoznawania twarzy i języka C# do wykrywania ludzkich twarzy na obrazie. Działający przykład kodu w tym przewodniku Szybki Start znajduje się w projekcie interfejsu API rozpoznawania twarzy w repozytorium [przewodników języka C# przetwarzania obrazów usług Cognitive Services](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/Face) w serwisie GitHub.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

## <a name="prerequisites"></a>Wymagania wstępne

- Klucz subskrypcji interfejsu API rozpoznawania twarzy. Klucz subskrypcji bezpłatnej wersji próbnej możesz uzyskać na stronie [Wypróbuj usługi Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Możesz też wykonać instrukcje z tematu [Create a Cognitive Services account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) (Tworzenie konta usług Cognitive Services), aby subskrybować usługę interfejsu API rozpoznawania twarzy i uzyskać klucz.
- Dowolna wersja programu [Visual Studio 2015 lub 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. W programie Visual Studio utwórz nowy projekt **Aplikacja konsoli (.NET Framework)** i nadaj jej nazwę **FaceDetection**. 
1. Jeśli w rozwiązaniu istnieją inne projekty, wybierz ten projekt jako pojedynczy projekt startowy.
1. Pobierz wymagane pakiety NuGet. Kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań, a następnie wybierz pozycję **Zarządzaj pakietami NuGet**. Kliknij kartę **Przeglądaj**, wybierz pozycję **Uwzględnij wersję wstępną**, a następnie znajdź i zainstaluj następujący pakiet:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)
1. Upewnij się, że zainstalowano najnowsze wersje wszystkich pakietów NuGet w projekcie. Kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań, a następnie wybierz pozycję **Zarządzaj pakietami NuGet**. Kliknij przycisk **aktualizacje** kartę i zainstalować najnowsze wersje wszelkie pakiety, które są wyświetlane.

## <a name="add-face-detection-code"></a>Dodawanie kodu służącego do wykrywania twarzy

Otwórz nowy plik *Program.cs* projektu. W tym pliku dodasz kod wymagany do ładowania obrazów i wykrywania twarzy.

### <a name="include-namespaces"></a>Uwzględnianie przestrzeni nazw

Dodaj następujące instrukcje `using` na początku pliku *Program.cs*.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=1-7)]

### <a name="add-essential-fields"></a>Dodawanie podstawowych pól

Dodaj **Program** klasy przy użyciu następujących pól. Te dane służą do określania sposobu nawiązywania połączenia z usługą rozpoznawania twarzy i lokalizacji, z której można pobrać dane wejściowe. Należy zaktualizować pole `subscriptionKey` wartością klucza subskrypcji. Konieczna może być również zmiana ciągu `faceEndpoint` w taki sposób, aby zawierał on poprawny identyfikator regionu. Należy także ustawić wartości `localImagePath` i/lub `remoteImageUrl` na ścieżki, które wskazują na rzeczywiste pliki obrazów.

Pole `faceAttributes` jest to po prostu tablica atrybutów określonych typów. Będzie ono określać informacje dotyczące wykrytych twarzy, które mają zostać pobrane.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=9-34)]

### <a name="create-and-use-the-face-client"></a>Tworzenie i używanie klienta interfejsu API rozpoznawania twarzy

Następnie dodaj **Main** metody **Program** klasy z następującym kodem. Służy on do konfigurowania klienta interfejsu API rozpoznawania twarzy.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=36-41)]

Również w **Main** metody, Dodaj następujący kod, korzystając z nowo utworzonego klienta rozpoznawania twarzy, wykrywanie twarzy na obrazie zdalnych i lokalnych. Następnie zostaną zdefiniowane metody wykrywania. 

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=43-50)]

### <a name="detect-faces"></a>Wykrywanie twarzy

Dodaj następującą metodę do klasy **Program**. Używa ona klienta usługi interfejsu API rozpoznawania twarzy do wykrywania twarzy na obrazie zdalnym, do którego odwołuje się adres URL. Używa ona `faceAttributes` pola&mdash; **DetectedFace** obiekty dodane do `faceList` będzie miał określone atrybuty (w tym przypadku, wiek i płeć).

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=52-74)]

W podobny sposób dodaj metodę **DetectLocalAsync**. Używa ona klienta usługi interfejsu API rozpoznawania twarzy do wykrywania twarzy na obrazie lokalnym, do którego odwołuje się ścieżka pliku.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=76-101)]

### <a name="retrieve-and-display-face-attributes"></a>Pobieranie i wyświetlanie atrybutów twarzy

Następnie zdefiniuj metodę **GetFaceAttributes**. Zwraca ona ciąg zawierający odpowiednie informacje o atrybutach.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=103-116)]

Na koniec zdefiniuj metodę **DisplayAttributes**, aby zapisać dane atrybutów twarzy jako dane wyjściowe konsoli. Następnie można zamknąć klas i przestrzeni nazw.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=118-125)]

## <a name="run-the-app"></a>Uruchamianie aplikacji

Pomyślna odpowiedź będzie zawierać płeć i wiek każdej twarzy na zdjęciu. Na przykład:

```
https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg
Male 37   Female 56
```

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start utworzono prostą aplikację konsolową .NET, która używa interfejsu API rozpoznawania twarzy do wykrywania twarzy na obrazach lokalnych i zdalnych. Następnie postępuj zgodnie z informacjami zawartymi w bardziej szczegółowym samouczku, aby dowiedzieć się, jak w intuicyjny sposób prezentować informacje o twarzy użytkownikom.

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie aplikacji WPF wykrywającej i analizującej twarze na zdjęciach](../Tutorials/FaceAPIinCSharpTutorial.md)
