---
title: 'Szybki start: biblioteka klienta przetwarzania obrazów w urozumienie komputera dla go'
titleSuffix: Azure Cognitive Services
description: Wprowadzenie do biblioteki klienta usługi Computer Vision dla go z tym przewodnikiem Szybki start.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: d8f40ab57ee2569b2cb5bf62f391919476b8ab17
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80136014"
---
<a name="HOLTop"></a>

[Dokumentacja](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision) | [referencyjna Pakiet źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision) | [Package](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję platformy Azure](https://azure.microsoft.com/free/)
* Najnowsza wersja [programu Go](https://golang.org/dl/)

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-computer-vision-azure-resource"></a>Tworzenie zasobu platformy Computer Vision Azure

Usługi Azure Cognitive Services są reprezentowane przez zasoby platformy Azure, które subskrybujesz. Utwórz zasób dla przetwarzania obrazów komputerowych przy użyciu [witryny Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na komputerze lokalnym. Możesz również wykonać następujące czynności:

* Pobierz [klucz wersji próbnej](https://azure.microsoft.com/try/cognitive-services/#decision) ważny przez siedem dni za darmo. Po zarejestrowaniu się będzie on dostępny w [witrynie sieci Web platformy Azure.](https://azure.microsoft.com/try/cognitive-services/my-apis/)  
* Wyświetl swój zasób w [witrynie Azure portal](https://portal.azure.com/).

Po otrzymasz klucz z subskrypcji próbnej lub zasobu, [utwórz zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla klucza i adresu URL punktu końcowego, odpowiednio `COMPUTER_VISION_SUBSCRIPTION_KEY` i `COMPUTER_VISION_ENDPOINT`, odpowiednio.

### <a name="create-a-go-project-directory"></a>Tworzenie katalogu projektu Go

W oknie konsoli (cmd, PowerShell, Terminal, Bash) utwórz nowy `my-app`obszar roboczy dla projektu Go o nazwie i przejdź do niego.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

Obszar roboczy będzie zawierał trzy foldery:

* **src** - Ten katalog będzie zawierał kod źródłowy i pakiety. Wszystkie pakiety `go get` zainstalowane za pomocą polecenia zostaną w tym katalogu.
* **pkg** — ten katalog będzie zawierał skompilowane obiekty pakietu Go. Wszystkie te pliki `.a` mają rozszerzenie.
* **bin** — ten katalog będzie zawierał binarne pliki `go install`wykonywalne, które są tworzone po uruchomieniu .

> [!TIP]
> Aby dowiedzieć się więcej o strukturze obszaru roboczego Go, zobacz [dokumentację języka Go](https://golang.org/doc/code.html#Workspaces). Ten przewodnik zawiera `$GOPATH` informacje `$GOROOT`dotyczące ustawień i .

### <a name="install-the-client-library-for-go"></a>Instalowanie biblioteki klienta dla go

Następnie zainstaluj bibliotekę klienta dla Go:

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

a jeśli używasz programu dep, w ramach repozytorium uruchom:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

### <a name="create-a-go-application"></a>Tworzenie aplikacji Go

Następnie utwórz plik w katalogu **src** o nazwie `sample-app.go`:

```bash
cd src
touch sample-app.go
```

Otwórz `sample-app.go` w preferowanym edytorze IDE lub tekstu. Następnie dodaj nazwę pakietu i zaimportuj następujące biblioteki:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_imports)]

Ponadto zadeklarować kontekst w katalogu głównym skryptu. Ten obiekt będzie potrzebny do wykonywania większości wywołań funkcji przetwarzania obrazów komputerowych:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_context)]

Następnie rozpoczniesz dodawanie kodu do wykonywania różnych operacji przetwarzania.

## <a name="object-model"></a>Model obiektu

Następujące klasy i interfejsy obsługują niektóre z głównych funkcji SDK Computer Vision Go.

|Nazwa|Opis|
|---|---|
| [PodstawaClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | Ta klasa jest potrzebna dla wszystkich funkcji przetwarzania obrazu, takich jak analiza obrazu i czytanie tekstu. Tworzenie wystąpienia z informacjami o subskrypcji i używać go do wykonywania większości operacji obrazu.|
|[ObrazAnaliza](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ImageAnalysis)| Ten typ zawiera wyniki **analyzeimage** wywołania funkcji. Istnieją podobne typy dla każdej z funkcji specyficznych dla kategorii.|
|[ReadOperationResult (Wynik operacji)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ReadOperationResult)| Ten typ zawiera wyniki operacji odczytu wsadowego. |
|[VisualFeatureTytyty](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#VisualFeatureTypes)| Ten typ definiuje różne rodzaje analizy obrazu, które można wykonać w standardowej operacji analizy. Należy określić zestaw visualfeatureTypes wartości w zależności od potrzeb. |

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące zadania za pomocą biblioteki klienta usługi Computer Vision dla go:

* [Uwierzytelnij klienta](#authenticate-the-client)
* [Analizowanie obrazu](#analyze-an-image)
* [Czytanie drukowanego i odręcznego tekstu](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Uwierzytelnij klienta

> [!NOTE]
> W tym kroku założono, że [utworzono zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla klucza i punktu końcowego przetwarzania usługi Przetwarzania, nazwanych `COMPUTER_VISION_SUBSCRIPTION_KEY` i `COMPUTER_VISION_ENDPOINT` odpowiednio.

Utwórz `main` funkcję i dodaj do niej następujący kod, aby utworzyć wystąpienia klienta z punktem końcowym i kluczem.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_client)]

## <a name="analyze-an-image"></a>Analizowanie obrazu

Poniższy kod używa obiektu klienta do analizowania obrazu zdalnego i drukowania wyników na konsoli. Możesz uzyskać opis tekstowy, kategoryzację, listę znaczników, wykryte obiekty, wykryte marki, wykryte twarze, flagi zawartości dla dorosłych, główne kolory i typ obrazu.

### <a name="set-up-test-image"></a>Konfigurowanie obrazu testowego

Najpierw zapisz odwołanie do adresu URL obrazu, który chcesz przeanalizować. Umieść to `main` wewnątrz swojej funkcji.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_url)]

> [!NOTE]
> Można również analizować obraz lokalny. Zobacz przykładowy kod w [usłudze GitHub, aby](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) uzyskać scenariusze dotyczące obrazów lokalnych.

### <a name="specify-visual-features"></a>Określanie obiektów wizualnych

Następujące wywołania funkcji wyodrębnić różne funkcje wizualne z przykładowego obrazu. Te funkcje zostaną zdefiniowane w poniższych sekcjach.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze)]

### <a name="get-image-description"></a>Pobierz opis obrazu

Następująca funkcja pobiera listę wygenerowanych podpisów dla obrazu. Aby uzyskać więcej informacji na temat opisu obrazu, zobacz [Opis obrazów](../../concept-describing-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_describe)]

### <a name="get-image-category"></a>Pobierz kategorię obrazu

Następująca funkcja pobiera wykrytą kategorię obrazu. Aby uzyskać więcej informacji, zobacz [Kategoryzowanie obrazów](../../concept-categorizing-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_categorize)]

### <a name="get-image-tags"></a>Pobierz znaczniki obrazów

Następująca funkcja pobiera zestaw wykrytych znaczników na obrazie. Aby uzyskać więcej informacji, zobacz [Tagi zawartości](../../concept-tagging-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_tags)]

### <a name="detect-objects"></a>Wykrywanie obiektów

Poniższa funkcja wykrywa typowe obiekty na obrazie i drukuje je na konsoli. Aby uzyskać więcej informacji, zobacz [Wykrywanie obiektów](../../concept-object-detection.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_objects)]

### <a name="detect-brands"></a>Wykrywanie marek

Poniższy kod wykrywa marki firmowe i logo na obrazie i drukuje je na konsoli. Aby uzyskać więcej informacji, [wykrywanie marki](../../concept-brand-detection.md).

Najpierw zadeklarować odwołanie do nowego `main` obrazu w ramach funkcji.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brand_url)]

Poniższy kod definiuje funkcję wykrywania marki.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brands)]

### <a name="detect-faces"></a>Wykrywanie twarzy

Następująca funkcja zwraca wykryte twarze na obrazie z ich współrzędnymi prostokąta i określonymi atrybutami twarzy. Aby uzyskać więcej informacji, zobacz [Wykrywanie twarzy](../../concept-detecting-faces.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Wykrywanie treści dla dorosłych, rasistowskich lub gory

Następująca funkcja drukuje wykrytą obecność zawartości dla dorosłych na obrazie. Aby uzyskać więcej informacji, zobacz [Adult, racy, gory treści](../../concept-detecting-adult-content.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Pobierz schemat kolorów obrazu

Następująca funkcja drukuje wykryte atrybuty kolorów obrazu, takie jak kolory dominujące i kolor akcentu. Aby uzyskać więcej informacji, zobacz [Schematy kolorów](../../concept-detecting-color-schemes.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Pobierz zawartość specyficzną dla domeny

Usługa Computer Vision może używać specjalistycznych modeli do dalszej analizy obrazów. Aby uzyskać więcej informacji, zobacz [Zawartość specyficzna dla domeny](../../concept-detecting-domain-content.md). 

Poniższy kod analizuje dane dotyczące wykrytych gwiazd na obrazie.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_celebs)]

Poniższy kod analizuje dane dotyczące wykrytych punktów orientacyjnych na obrazie.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Pobierz typ obrazu

Następująca funkcja drukuje informacje o&mdash;typie obrazu, niezależnie od tego, czy jest to obiekt clipart, czy rysunek liniowy.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>Czytanie drukowanego i odręcznego tekstu

Funkcja Wizja komputerowa może odczytywać widoczny tekst na obrazie i konwertować go na strumień znaków. Kod w tej sekcji definiuje `RecognizeTextReadAPIRemoteImage`funkcję, która używa obiektu klienta do wykrywania i wyodrębniania drukowanego lub odręcznego tekstu na obrazie.

Dodaj przykładowe odwołanie do obrazu `main` i wywołanie funkcji w funkcji.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_readinmain)]

> [!NOTE]
> Można również wyodrębnić tekst z obrazu lokalnego. Zobacz przykładowy kod w [usłudze GitHub, aby](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) uzyskać scenariusze dotyczące obrazów lokalnych.

### <a name="call-the-read-api"></a>Wywołanie interfejsu API odczytu

Zdefiniuj nową `RecognizeTextReadAPIRemoteImage`funkcję do czytania tekstu, . Dodaj poniższy kod, który wywołuje **BatchReadFile** metody dla danego obrazu. Ta metoda zwraca identyfikator operacji i uruchamia proces asynchroniczne, aby odczytać zawartość obrazu.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_call)]

### <a name="get-read-results"></a>Uzyskaj wyniki odczytu

Następnie pobierz identyfikator operacji zwrócony z **wywołania BatchReadFile** i użyj go z **GetReadOperationResult** metody kwerendy usługi dla wyników operacji. Poniższy kod sprawdza operację w odstępach jednosekundowych, dopóki wyniki nie zostaną zwrócone. Następnie drukuje wyodrębnione dane tekstowe na konsoli.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_response)]

### <a name="display-read-results"></a>Wyświetlanie wyników odczytu

Dodaj następujący kod, aby przeanalizować i wyświetlić pobrane dane tekstowe i zakończyć definicję funkcji.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_display)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację z katalogu aplikacji `go run` za pomocą polecenia.

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie innych skojarzonych z nią zasobów.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Odwołanie do interfejsu API przetwarzania obrazów komputerowych (Go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision)

* [Czym jest przetwarzanie obrazów?](../../Home.md)
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go).
