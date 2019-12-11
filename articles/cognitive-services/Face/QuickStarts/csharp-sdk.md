---
title: 'Przewodnik Szybki Start: Tworzenie biblioteki klienckiej dla platformy .NET | Microsoft Docs'
description: Rozpocznij pracę z biblioteką klienta programu Front dla platformy .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 5602e26e2e1b327d65d411f8e92f264f1bf9fb91
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977866"
---
# <a name="quickstart-face-client-library-for-net"></a>Przewodnik Szybki Start: Tworzenie biblioteki klienckiej dla platformy .NET

Rozpocznij pracę z biblioteką klienta programu Front dla platformy .NET. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Usługa interfejs API rozpoznawania twarzy zapewnia dostęp do zaawansowanych algorytmów służących do wykrywania i rozpoznawania ludzkich twarzy na obrazach.

Użyj biblioteki klienta programu Front for .NET, aby:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Wykrywanie twarzy na obrazie](#detect-faces-in-an-image)
* [Znajdź podobne twarze](#find-similar-faces)
* [Tworzenie i uczenie grupy osób](#create-and-train-a-person-group)
* [Identyfikowanie kroju](#identify-a-face)
* [Utwórz migawkę migracji danych](#take-a-snapshot-for-data-migration)

[Dokumentacja referencyjna](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet) | [kodu źródłowego biblioteki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.Face) | [pakietu (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.5.0-preview.1) | [próbka](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
* Bieżąca wersja [platformy .NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-face-azure-resource"></a>Tworzenie zasobu platformy Azure w postaci czołowej

Usługa Azure Cognitive Services jest reprezentowana przez zasoby platformy Azure, które subskrybujesz. Utwórz zasób dla konkretnej aplikacji przy użyciu [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na komputerze lokalnym. Możesz również wykonać następujące czynności:

* Uzyskaj [klucz wersji próbnej](https://azure.microsoft.com/try/cognitive-services/#decision) ważny przez siedem dni bezpłatnie. Po utworzeniu konta będzie ono dostępne w [witrynie sieci Web systemu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Wyświetl zasób na [Azure Portal](https://portal.azure.com/).

Po otrzymaniu klucza z subskrypcji próbnej lub zasobu [Utwórz zmienną środowiskową](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla adresu URL klucza i punktu końcowego o nazwie `FACE_SUBSCRIPTION_KEY` i `FACE_ENDPOINT`.

### <a name="create-a-new-c-application"></a>Utwórz nową C# aplikację

Utwórz nową aplikację platformy .NET Core w preferowanym edytorze lub środowisku IDE. 

W oknie konsoli (na przykład cmd, PowerShell lub bash) Użyj polecenia `dotnet new`, aby utworzyć nową aplikację konsolową o nazwie `face-quickstart`. To polecenie tworzy prosty projekt "Hello world" C# z pojedynczym plikiem źródłowym: *program.cs*. 

```dotnetcli
dotnet new console -n face-quickstart
```

Zmień katalog na nowo utworzony folder aplikacji. Aplikację można skompilować przy użyciu:

```dotnetcli
dotnet build
```

Dane wyjściowe kompilacji nie powinny zawierać ostrzeżeń ani błędów. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

W katalogu projektu Otwórz plik *program.cs* w preferowanym edytorze lub w środowisku IDE. Dodaj następujące dyrektywy `using`:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_using)]

W metodzie `Main` aplikacji Utwórz zmienne dla punktu końcowego i klucza usługi Azure Resource.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_mainvars)]

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

W katalogu aplikacji zainstaluj bibliotekę klienta programu Front for .NET przy użyciu następującego polecenia:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.5.0-preview.1
```

Jeśli używasz środowiska IDE programu Visual Studio, Biblioteka kliencka jest dostępna jako pakiet NuGet do pobrania.

## <a name="object-model"></a>Model obiektów

Poniższe klasy i interfejsy obsługują niektóre z najważniejszych funkcji zestawu .NET SDK:

|Nazwa|Opis|
|---|---|
|[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) | Ta klasa reprezentuje autoryzację do korzystania z usługi twarz i jest potrzebna dla wszystkich funkcji funkcjonalnych. Tworzysz wystąpienie z informacjami o subskrypcji i używasz ich do tworzenia wystąpień innych klas. |
|[FaceOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperations?view=azure-dotnet)|Ta klasa obsługuje podstawowe zadania wykrywania i rozpoznawania, które można wykonywać przy użyciu twarzy ludzkich. |
|[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)|Ta klasa reprezentuje wszystkie dane, które zostały wykryte z pojedynczej części obrazu. Można go użyć do pobierania szczegółowych informacji o kroju.|
|[FaceListOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet)|Ta klasa zarządza opartymi na chmurze konstrukcjami **FaceList** , w których jest przechowywany asortyment zestawu twarzy. |
|[PersonGroupPersonExtensions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongrouppersonextensions?view=azure-dotnet)| Ta klasa zarządza konstrukcjami **osób** , które są przechowywane w chmurze, które przechowują zestaw twarzy należących do jednej osoby.|
|[PersonGroupOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet)| Ta klasa **zarządza konstrukcjami** , które są przechowywane w chmurze, przechowujących zestaw obiektów **osób** . |
|[ShapshotOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)|Ta klasa zarządza funkcją migawek. Można jej użyć do tymczasowego zapisania wszystkich danych platformy opartej na chmurze i przeprowadzenia migracji tych danych do nowej subskrypcji platformy Azure. |

## <a name="code-examples"></a>Przykłady kodu

Poniższe fragmenty kodu przedstawiają sposób wykonywania następujących zadań przy użyciu biblioteki klienckiej programu Front for .NET:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Wykrywanie twarzy na obrazie](#detect-faces-in-an-image)
* [Znajdź podobne twarze](#find-similar-faces)
* [Tworzenie i uczenie grupy osób](#create-and-train-a-person-group)
* [Identyfikowanie kroju](#identify-a-face)
* [Utwórz migawkę migracji danych](#take-a-snapshot-for-data-migration)


## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

> [!NOTE]
> W tym przewodniku szybki start założono, że [utworzono zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla klucza i punktu końcowego, o nazwie `FACE_SUBSCRIPTION_KEY` i `FACE_ENDPOINT`.

W nowej metodzie Utwórz wystąpienie klienta z punktem końcowym i kluczem. Utwórz obiekt [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) z kluczem i użyj go w punkcie końcowym, aby utworzyć obiekt [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) .

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_auth)]

Prawdopodobnie chcesz wywołać tę metodę w metodzie `Main`.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_client)]

## <a name="detect-faces-in-an-image"></a>Wykrywanie twarzy na obrazie

W katalogu głównym klasy Zdefiniuj następujący ciąg adresu URL. Ten adres URL wskazuje zestaw przykładowych obrazów.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_image_url)]

Opcjonalnie możesz wybrać, który model AI ma być używany do wyodrębniania danych z wykrytych opcji. Aby uzyskać informacje na temat tych opcji, zobacz temat [Określanie modelu rozpoznawania](../Face-API-How-to-Topics/specify-recognition-model.md) .

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_models)]

Ostatnia Operacja wykrywania zajmie obiektem [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) , adresem URL obrazu i modelem rozpoznawania.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_call)]

### <a name="get-detected-face-objects"></a>Pobieranie wykrytych obiektów czołowych

W następnym bloku kodu Metoda `DetectFaceExtract` wykrywa twarze w trzech obrazach pod podanym adresem URL i tworzy listę obiektów [DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) w pamięci programu. Lista wartości [FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) określa funkcje do wyodrębnienia. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect)]

### <a name="display-detected-face-data"></a>Wyświetl wykryte dane dotyczące kroju

Pozostała część metody `DetectFaceExtract` analizuje i drukuje dane atrybutów dla każdej wykrytej klasy. Każdy atrybut musi być określony osobno w wywołaniu oryginalnego interfejsu API wykrywania kroju (na liście [FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) ). Poniższy kod przetwarza każdy atrybut, ale prawdopodobnie trzeba będzie tylko użyć jednego lub kilku.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_parse)]

## <a name="find-similar-faces"></a>Wyszukiwanie podobnych twarzy

Poniższy kod przyjmuje pojedyncze wykryte twarze (Źródło) i przeszukuje zestaw innych twarzy (element docelowy) w celu znalezienia dopasowań. Po znalezieniu dopasowania program drukuje identyfikator dopasowanej do konsoli.

### <a name="detect-faces-for-comparison"></a>Wykrywanie twarzy do porównania

Najpierw Zdefiniuj metodę wykrywania drugiej czołowej. Należy wykryć twarze w obrazach, zanim będzie można je porównać, a ta metoda wykrywania jest zoptymalizowana pod kątem operacji porównania. Nie wyodrębnimy szczegółowych atrybutów kroju, takich jak w powyższej sekcji, i używa innego modelu rozpoznawania.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_face_detect_recognize)]

### <a name="find-matches"></a>Znajdź dopasowania

Poniższa metoda wykrywa twarze w zestawie obrazów docelowych i w jednym obrazie źródłowym. Następnie porównuje je i odnajduje wszystkie obrazy docelowe podobne do obrazu źródłowego.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_find_similar)]

### <a name="print-matches"></a>Drukuj dopasowania

Poniższy kod drukuje szczegóły dopasowania do konsoli programu:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_find_similar_print)]

## <a name="create-and-train-a-person-group"></a>Tworzenie i uczenie grupy osób

Poniższy kod tworzy **odbiorcę** z sześciu różnych obiektów **osób** . Kojarzy każdej **osoby** z zestawem przykładowych obrazów, a następnie pociąga, aby rozpoznać każdą osobę według ich cech twarzy. Obiekty **osób** i **osób** są używane w operacjach sprawdzania, identyfikowania i grupowania.

Jeśli jeszcze tego nie zrobiono, zdefiniuj następujący ciąg adresu URL w katalogu głównym klasy. Wskazuje zestaw przykładowych obrazów.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_image_url)]

Kod w dalszej części tej sekcji określi model rozpoznawania służący do wyodrębniania danych z twarzy, a Poniższy fragment kodu tworzy odwołania do dostępnych modeli. Aby uzyskać informacje na temat modeli rozpoznawania, zobacz temat [Określanie modelu rozpoznawania](../Face-API-How-to-Topics/specify-recognition-model.md) .

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_models)]

### <a name="create-persongroup"></a>Utwórz osobę

Zadeklaruj zmienną ciągu w elemencie głównym klasy, aby reprezentować identyfikator grupy **osób** , którą utworzysz.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_declare)]

W nowej metodzie Dodaj następujący kod. Ten kod kojarzy nazwy osób z przykładowymi obrazami.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_files)]

Następnie Dodaj następujący kod, aby utworzyć obiekt **osoby** dla każdej osoby w słowniku i dodać dane dotyczące kroju z odpowiednich obrazów. Każdy obiekt **osoby** jest skojarzony z tą samą **osobą** przy użyciu unikatowego ciągu identyfikatora. Pamiętaj, aby przekazać zmienne `client`, `url`i `RECOGNITION_MODEL1` do tej metody.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_create)]

### <a name="train-persongroup"></a>Szkolenie zespołu

Po wyodrębnieniu danych kroju z obrazów i posortowaniu ich w różnych obiektach **osób** należy przeszkolić **osobę** , aby zidentyfikować funkcje wizualne skojarzone z poszczególnymi obiektami **osób** . Poniższy kod wywołuje metodę synchronicznego **pouczenia** i sonduje wyniki, drukując stan do konsoli.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_train)]

Ta grupa **osób** i skojarzone z nią obiekty **osób** są teraz gotowe do użycia w operacjach sprawdzania, identyfikowania lub grupowania.

## <a name="identify-a-face"></a>Identyfikowanie kroju

Operacja identyfikowania pobiera obraz osoby (lub wielu osób) i szuka tożsamości każdej z nich na obrazie. Porównuje każdą wykrytą twarzą z **osobą**, która jest bazą danych różnych obiektów **osób** , których funkcje twarzy są znane.

> [!IMPORTANT]
> Aby można było uruchomić ten przykład, należy najpierw uruchomić kod w temacie [Tworzenie i uczenie grupy osób](#create-and-train-a-person-group). Zmienne używane w tej sekcji&mdash;`client`, `url`i `RECOGNITION_MODEL1`&mdash;również muszą być dostępne w tym miejscu.

### <a name="get-a-test-image"></a>Pobierz obraz testowy

Zwróć uwagę, że kod służący do [tworzenia i uczenia grupy osób](#create-and-train-a-person-group) definiuje zmienną `sourceImageFileName`. Ta zmienna odnosi się do obrazu źródłowego&mdash;obraz zawierający osoby do zidentyfikowania.

### <a name="identify-faces"></a>Identyfikowanie twarzy

Poniższy kod pobiera obraz źródłowy i tworzy listę wszystkich wykrytych twarzy na obrazie. Są to twarze, które zostaną zidentyfikowane względem **osoby**.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_identify_sources)]

Następny fragment kodu wywołuje operację identyfikowania i drukuje wyniki w konsoli. W tym miejscu usługa próbuje dopasować wszystkie powierzchnie z obrazu źródłowego do **osoby** w danej **osobie**.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_identify)]

## <a name="take-a-snapshot-for-data-migration"></a>Utwórz migawkę migracji danych

Funkcja migawek pozwala przenosić zapisane dane, takie jak wyszkolone **osoby**, do innej subskrypcji platformy Azure Cognitive Services. Możesz chcieć użyć tej funkcji, jeśli na przykład utworzysz obiekt obiektu **osoby** przy użyciu subskrypcji bezpłatnej wersji próbnej i chcesz przeprowadzić migrację go do płatnej subskrypcji. Aby zapoznać się z omówieniem funkcji migawek, zobacz [Migrowanie danych](../Face-API-How-to-Topics/how-to-migrate-face-data.md) z obszaru.

W tym przykładzie zostanie przeprowadzona migracja grupy **osób** utworzonych w temacie [Tworzenie i uczenie osoby](#create-and-train-a-person-group). Możesz najpierw wykonać tę sekcję lub utworzyć własne konstrukcje danych (-y) do migracji.

### <a name="set-up-target-subscription"></a>Skonfiguruj subskrypcję docelową

Najpierw musisz mieć drugą subskrypcję platformy Azure z zasobem czołowym; można to zrobić, wykonując czynności opisane w sekcji [Konfigurowanie](#setting-up) . 

Następnie Zdefiniuj następujące zmienne w `Main` metodzie programu. Należy utworzyć nowe zmienne środowiskowe dla identyfikatora subskrypcji Twojego konta platformy Azure, a także klucz, punkt końcowy i Identyfikator subskrypcji nowego konta (docelowego). 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_vars)]

Na potrzeby tego przykładu należy zadeklarować zmienną dla identyfikatora docelowej **osoby**&mdash;obiektu, który należy do nowej subskrypcji, do której zostaną skopiowane dane.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_vars)]

### <a name="authenticate-target-client"></a>Uwierzytelnianie klienta docelowego

Następnie Dodaj kod w celu uwierzytelnienia pomocniczej subskrypcji programu Marketo.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_client)]

### <a name="use-a-snapshot"></a>Użyj migawki

Pozostałe operacje migawek muszą mieć miejsce w metodzie asynchronicznej. 

1. Pierwszym krokiem jest **wykonanie** migawki, która zapisuje dane pierwotnej subskrypcji w tymczasowej lokalizacji w chmurze. Ta metoda zwraca identyfikator używany do wykonywania zapytań dotyczących stanu operacji.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_take)]

1. Następnie wykonaj zapytanie o identyfikator do momentu zakończenia operacji.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_take_wait)]

1. Następnie użyj operacji **Zastosuj** , aby zapisać swoje dane z Twojej subskrypcji docelowej. Ta metoda zwraca również wartość identyfikatora.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_apply)]

1. Ponownie wykonaj zapytanie o nowy identyfikator do momentu zakończenia operacji.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_apply)]

1. Na koniec wykonaj blok try/catch i Zakończ metodę.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_trycatch)]

W tym momencie nowy obiekt obiektu **osoby** powinien mieć te same dane co oryginał i powinny być dostępne z nowej subskrypcji (docelowej) platformy Azure.

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację z katalogu aplikacji za pomocą polecenia `dotnet run`.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Jeśli utworzono **osobę** z tego przewodnika Szybki Start i chcesz ją usunąć, uruchom następujący kod w programie:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_delete)]

Zdefiniuj metodę usuwania przy użyciu następującego kodu:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_deletepersongroup)]

Ponadto w przypadku migrowania danych przy użyciu funkcji Snapshot w tym przewodniku Szybki Start należy również usunąć **osobę** , która została zapisana do subskrypcji docelowej.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_target_persongroup_delete)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start pokazano, jak używać biblioteki kroju dla programu .NET do wykonywania zadań bazowych. Następnie zapoznaj się z dokumentacją referencyjną, aby dowiedzieć się więcej o bibliotece.

> [!div class="nextstepaction"]
> [Odwołanie interfejs API rozpoznawania twarzy (.NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)

* [Co to jest interfejs API rozpoznawania twarzy?](../overview.md)
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/Face/Program.cs).
