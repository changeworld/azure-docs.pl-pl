---
title: 'Szybki start: Zmiana, uczenie aplikacji C# , Luis'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start języka C# dodasz przykładowe wypowiedzi do aplikacji Home Automation i przeprowadzisz uczenie aplikacji.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: 43bbcbcad8b4c03829ba702cecfff1e64b8e27b4
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70375561"
---
# <a name="quickstart-change-model-using-c"></a>Szybki start: zmiana modelu przy użyciu języka C#

[!INCLUDE [Quickstart introduction for change model](../../../includes/cognitive-services-luis-qs-change-model-intro-para.md)]

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* Najnowsza [**wersja programu Visual Studio Community**](https://www.visualstudio.com/downloads/).
* Zainstalowany język programowania C#.
* Pakiety NuGet [JsonFormatterPlus](https://www.nuget.org/packages/JsonFormatterPlus) i [CommandLine](https://www.nuget.org/packages/CommandLineParser/)

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>Plik JSON z przykładowymi wypowiedziami

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>Tworzenie kodu przewodnika Szybki start 

W programie Visual Studio Utwórz nową aplikację **klasycznej konsoli klasycznych systemu Windows** przy użyciu .NET Framework. Nadaj nazwę projektowi `ConsoleApp1`.

![Typ projektu programu Visual Studio](./media/luis-quickstart-cs-add-utterance/vs-project-type.png)

### <a name="add-the-systemweb-dependency"></a>Dodawanie zależności System.Web

W projekcie programu Visual Studio wymagana jest zależność **System.Web**. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy pozycję **odwołania** i wybierz polecenie **Dodaj odwołanie** z sekcji zestawy.

![Dodawanie odwołania System.Web](./media/luis-quickstart-cs-add-utterance/system.web.png)

### <a name="add-other-dependencies"></a>Dodawanie innych zależności

W projekcie programu Visual Studio wymagane są pakiety **JsonFormatterPlus** i **CommandLineParser**. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy pozycję **Odwołania**, a następnie wybierz polecenie **Zarządzaj pakietami NuGet...** . Wyszukaj i Dodaj wszystkie te dwa pakiety. 

![Dodawanie zależności innych firm](./media/luis-quickstart-cs-add-utterance/add-dependencies.png)


### <a name="write-the-c-code"></a>Tworzenie kodu w języku C#
Plik **Program.cs** powinien mieć następującą zawartość:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ConsoleApp1
{
    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Zaktualizuj zależności, aby:

   [!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=1-11 "Add the dependencies")]


Dodaj identyfikatory i ciągi usługi LUIS do klasy **Program**.

   [!code-csharp[Add the LUIS IDs and strings](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=19-30&dedent=8 "Add the LUIS IDs and strings")]

Dodaj klasę do zarządzania parametrami wiersza polecenia do klasy **Program**.

   [!code-csharp[Add class to manage command line parameters.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=32-46 "Add class to manage command-line parameters.")]

Dodaj metodę żądania GET do klasy **Program**.

   [!code-csharp[Add the GET request.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=49-59 "Add the GET request.")]


Dodaj metodę żądania POST do klasy **Program**. 

   [!code-csharp[Add the POST request.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=60-76 "Add the POST request.")]

Dodaj przykładowe wypowiedzi z metody pliku do klasy **Program**.

   [!code-csharp[Add example utterances from file.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=77-86 "Add example utterances from file.")]

Po zastosowaniu zmian w modelu przeprowadź uczenie modelu. Dodaj metodę do klasy **Program**.

   [!code-csharp[After the changes are applied to the model, train the model.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=87-96 "After the changes are applied to the model, train the model.")]

Uczenie może nie zakończyć się natychmiast. Sprawdź stan, aby upewnić się, że uczenie zostało zakończone. Dodaj metodę do klasy **Program**.

   [!code-csharp[Training may not complete immediately, check status to verify training is complete.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=97-103 "Training may not complete immediately, check status to verify training is complete.")]

Aby zarządzać argumentami wiersza polecenia, dodaj kod główny. Dodaj metodę do klasy **Program**.

   [!code-csharp[To manage command line arguments, add the main code.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=104-137 "To manage command-line arguments, add the main code.")]

### <a name="copy-utterancesjson-to-output-directory"></a>Kopiowanie pliku utterances.json do katalogu wyjściowego

W Eksplorator rozwiązań Dodaj `utterances.json` przez kliknięcie prawym przyciskiem myszy nazwę projektu Eksplorator rozwiązań, a następnie wybierz pozycję **Dodaj**, a następnie wybierz pozycję **istniejący element**. `utterances.json` Wybierz plik. Spowoduje to dodanie pliku do projektu. Następnie należy dodać go do katalogu wyjściowego. Kliknij prawym przyciskiem `utterances.json` myszy pozycję i wybierz pozycję **Właściwości**. W oknach właściwości dla pozycji **Akcja kompilacji** wybierz wartość `Content`, a dla pozycji **Kopiuj do katalogu wyjściowego** wartość `Copy Always`.  

![Oznaczanie pliku JSON jako zawartości](./media/luis-quickstart-cs-add-utterance/content-properties.png)

## <a name="build-code"></a>Kompilowanie kodu

Skompiluj kod w programie Visual Studio. 

## <a name="run-code"></a>Uruchamianie kodu

W katalogu /bin/Debug w projekcie uruchom aplikację z poziomu wiersza polecenia. 

```console
ConsoleApp1.exe --add utterances.json --train --status
```

W tym wierszu polecenia wyświetlane są wyniki wywołania interfejsu API dodawania wypowiedzi. 

[!INCLUDE [Quickstart response from API calls](../../../includes/cognitive-services-luis-qs-change-model-json-results.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Po ukończeniu przewodnika Szybki start usuń wszystkie pliki utworzone w tym przewodniku Szybki start. 

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"] 
> [Programowe tworzenie aplikacji LUIS](luis-tutorial-node-import-utterances-csv.md) 
