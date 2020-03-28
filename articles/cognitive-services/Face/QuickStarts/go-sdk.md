---
title: 'Szybki start: Biblioteka klienta twarzy dla Go | Dokumenty firmy Microsoft'
description: Wprowadzenie do biblioteki klienta Face dla go.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 2db40150167a8f16242b2feb15b77820fa1970a9
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76941430"
---
# <a name="quickstart-face-client-library-for-go"></a>Szybki start: biblioteka klienta twarzy dla Go

Wprowadzenie do biblioteki klienta Face dla go. Wykonaj następujące kroki, aby zainstalować bibliotekę i wypróbować nasze przykłady podstawowych zadań. Usługa Face zapewnia dostęp do zaawansowanych algorytmów wykrywania i rozpoznawania ludzkich twarzy na obrazach.

Użyj biblioteki klienta usługi Face, aby przejść do:

* [Wykrywanie twarzy na obrazie](#detect-faces-in-an-image)
* [Wyszukiwanie podobnych twarzy](#find-similar-faces)
* [Tworzenie i szkolenie grupy osób](#create-and-train-a-person-group)
* [Identyfikowanie twarzy](#identify-a-face)
* [Tworzenie migawki do migracji danych](#take-a-snapshot-for-data-migration)

[Dokumentacja](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face) | [referencyjna Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face) | [SDK do pobrania](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję](https://azure.microsoft.com/free/)
* Najnowsza wersja [programu Go](https://golang.org/dl/)

## <a name="set-up"></a>Konfiguruj

### <a name="create-a-face-azure-resource"></a>Tworzenie zasobu platformy Face Azure 

Rozpocznij korzystanie z usługi Face, tworząc zasób platformy Azure. Wybierz odpowiedni dla Ciebie typ zasobu:

* [Zasób próbny](https://azure.microsoft.com/try/cognitive-services/#decision) (nie jest wymagana subskrypcja platformy Azure): 
    * Ważne przez siedem dni, za darmo. Po zarejestrowaniu się klucz wersji próbnej i punkt końcowy będą dostępne w [witrynie sieci Web platformy Azure.](https://azure.microsoft.com/try/cognitive-services/my-apis/) 
    * Jest to świetna opcja, jeśli chcesz wypróbować usługę Face, ale nie masz subskrypcji platformy Azure.
* [Zasób usługi Face:](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)
    * Dostępne za pośrednictwem witryny Azure portal, dopóki nie usuniesz zasobu.
    * Użyj bezpłatnej warstwy cenowej, aby wypróbować usługę, a później uaktualnić do warstwy płatnej dla produkcji.
* [Zasób wielousługowy:](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne)
    * Dostępne za pośrednictwem witryny Azure portal, dopóki nie usuniesz zasobu.  
    * Użyj tego samego klucza i punktu końcowego dla aplikacji w wielu usługach Cognitive Services.

### <a name="create-an-environment-variable"></a>Tworzenie zmiennej środowiskowej

>[!NOTE]
> Punkty końcowe dla zasobów niepodstawowych utworzonych po 1 lipca 2019 r. używają niestandardowego formatu poddomeny przedstawionego poniżej. Aby uzyskać więcej informacji i pełną listę regionalnych punktów końcowych, zobacz [Niestandardowe nazwy poddomen dla usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Korzystając z klucza i punktu końcowego z utworzonego zasobu, utwórz dwie zmienne środowiskowe do uwierzytelniania:
* `FACE_SUBSCRIPTION_KEY`- Klucz zasobu do uwierzytelniania żądań.
* `FACE_ENDPOINT`- Punkt końcowy zasobu do wysyłania żądań interfejsu API. Będzie to wyglądać tak: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Użyj instrukcji dla systemu operacyjnego.
<!-- replace the below endpoint and key examples -->
#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx FACE_SUBSCRIPTION_KEY <replace-with-your-product-name-key>
setx FACE_ENDPOINT <replace-with-your-product-name-endpoint>
```

Po dodaniu zmiennej środowiskowej uruchom ponownie okno konsoli.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export FACE_SUBSCRIPTION_KEY=<replace-with-your-product-name-key>
export FACE_ENDPOINT=<replace-with-your-product-name-endpoint>
```

Po dodaniu zmiennej środowiskowej uruchom polecenie `source ~/.bashrc` z okna konsoli, aby zmiany zostały uwzględnione.

#### <a name="macos"></a>[Macos](#tab/unix)

Edytuj `.bash_profile`swój program i dodaj zmienną środowiskową:

```bash
export FACE_SUBSCRIPTION_KEY=<replace-with-your-product-name-key>
export FACE_ENDPOINT=<replace-with-your-product-name-endpoint>
```

Po dodaniu zmiennej środowiskowej uruchom polecenie `source .bash_profile` z okna konsoli, aby zmiany zostały uwzględnione.
***

### <a name="create-a-go-project-directory"></a>Tworzenie katalogu projektu Go

W oknie konsoli (cmd, PowerShell, Terminal, Bash) utwórz nowy `my-app`obszar roboczy dla projektu Go o nazwie i przejdź do niego.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

Obszar roboczy będzie zawierał trzy foldery:

* **src** - Ten katalog będzie zawierał kod źródłowy i pakiety. Wszystkie pakiety `go get` zainstalowane za pomocą polecenia będą w tym folderze.
* **pkg** — ten katalog będzie zawierał skompilowane obiekty pakietu Go. Wszystkie te pliki `.a` mają rozszerzenie.
* **bin** — ten katalog będzie zawierał binarne pliki `go install`wykonywalne, które są tworzone po uruchomieniu .

> [!TIP]
> Aby dowiedzieć się więcej o strukturze obszaru roboczego Go, zobacz [dokumentację języka Go](https://golang.org/doc/code.html#Workspaces). Ten przewodnik zawiera `$GOPATH` informacje `$GOROOT`dotyczące ustawień i .

### <a name="install-the-client-library-for-go"></a>Instalowanie biblioteki klienta dla go

Następnie zainstaluj bibliotekę klienta dla Go:

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

a jeśli używasz programu dep, w ramach repozytorium uruchom:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

### <a name="create-a-go-application"></a>Tworzenie aplikacji Go

Następnie utwórz plik w katalogu **src** o nazwie `sample-app.go`:

```bash
cd src
touch sample-app.go
```

Otwórz `sample-app.go` w preferowanym edytorze IDE lub tekstu. Następnie dodaj nazwę pakietu i zaimportuj następujące biblioteki:

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_imports)]

Następnie rozpoczniesz dodawanie kodu do wykonywania różnych operacji usługi Face.

## <a name="object-model"></a>Model obiektu

Następujące klasy i interfejsy obsługują niektóre z głównych funkcji usługi Face Go SDK.

|Nazwa|Opis|
|---|---|
|[PodstawaClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#BaseClient) | Ta klasa reprezentuje autoryzację do korzystania z usługi Face i jest potrzebna dla wszystkich funkcji face. Tworzenie wystąpienia z informacjami o subskrypcji i używać go do tworzenia wystąpień innych klas. |
|[Klienta](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)|Ta klasa obsługuje podstawowe zadania wykrywania i rozpoznawania, które można wykonać z ludzkimi twarzami. |
|[Wykrytaface](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)|Ta klasa reprezentuje wszystkie dane, które zostały wykryte z pojedynczej twarzy na obrazie. Można go użyć do pobrania szczegółowych informacji o twarzy.|
|[ListaClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#ListClient)|Ta klasa zarządza konstrukcjami **FaceList** przechowywanymi w chmurze, które przechowują różne zestaw twarzy. |
|[OsobaOsobowaKlient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)| Ta klasa zarządza konstrukcjami **person** przechowywanych w chmurze, które przechowują zestaw ścian należących do jednej osoby.|
|[PersonGroupClient (Osoba)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)| Ta klasa zarządza konstrukcjami **persongroup** przechowywanych w chmurze, które przechowują zestaw różnych **person** obiektów. |
|[MigawkaKlient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient)|Ta klasa zarządza funkcje migawka. Można go użyć do tymczasowego zapisania wszystkich danych twarzy w chmurze i migracji tych danych do nowej subskrypcji platformy Azure. |

## <a name="code-examples"></a>Przykłady kodu

Te przykłady kodu pokazują, jak wykonać podstawowe zadania przy użyciu biblioteki klienta usługi Face dla Go:

* [Uwierzytelnij klienta](#authenticate-the-client)
* [Wykrywanie twarzy na obrazie](#detect-faces-in-an-image)
* [Wyszukiwanie podobnych twarzy](#find-similar-faces)
* [Tworzenie i szkolenie grupy osób](#create-and-train-a-person-group)
* [Identyfikowanie twarzy](#identify-a-face)
* [Tworzenie migawki do migracji danych](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>Uwierzytelnij klienta

> [!NOTE] 
> Ten przewodnik Szybki start zakłada, że [utworzono zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla klucza twarzy i punktu końcowego, nazwany `FACE_SUBSCRIPTION_KEY` i `FACE_ENDPOINT` odpowiednio.

Utwórz funkcję **główną** i dodaj do niej następujący kod, aby utworzyć wystąpienia klienta z punktem końcowym i kluczem. Tworzenie **[CognitiveServicesAuthorizer](https://godoc.org/github.com/Azure/go-autorest/autorest#CognitiveServicesAuthorizer)** obiektu z kluczem i używać go z punktem końcowym do utworzenia **[Client](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)** obiektu. Ten kod również wystąpienia obiektu kontekstu, który jest potrzebny do tworzenia obiektów klienta. Definiuje również zdalną lokalizację, w której znajdują się niektóre przykładowe obrazy w tym przewodniku Szybki start.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_main_client)]


## <a name="detect-faces-in-an-image"></a>Wykrywanie twarzy na obrazie

Dodaj następujący kod w metodzie **głównej.** Ten kod definiuje zdalny obraz próbki i określa, które operacje twarzy, które mają zostać wyodrębnione z obrazu. Określa również, który model AI ma być używany do wyodrębniania danych z wykrytych twarzy. Zobacz [Określanie modelu rozpoznawania,](../Face-API-How-to-Topics/specify-recognition-model.md) aby uzyskać informacje na temat tych opcji. Na koniec **[DetectWithURL](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.DetectWithURL)** metoda wykonuje operację wykrywania twarzy na obrazie i zapisuje wyniki w pamięci programu.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect)]

### <a name="display-detected-face-data"></a>Wyświetlanie danych wykrytej twarzy

Następny blok kodu przyjmuje pierwszy element w tablicy **[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)** obiektów i drukuje jego atrybuty do konsoli. Jeśli użyto obrazu z wieloma ścianami, należy iterować za pośrednictwem tablicy zamiast.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect_display)]

## <a name="find-similar-faces"></a>Wyszukiwanie podobnych twarzy

Poniższy kod przyjmuje pojedynczą wykrytą twarz (źródło) i przeszukuje zestaw innych twarzy (docelowych), aby znaleźć dopasowania. Po znalezieniu dopasowania, drukuje identyfikator dopasowanej ściany do konsoli.

### <a name="detect-faces-for-comparison"></a>Wykrywanie twarzy do porównania

Najpierw zapisz odwołanie do ściany wykrytej w sekcji [Wykrywanie twarzy w](#detect-faces-in-an-image) sekcji obrazu. Ta twarz będzie źródłem.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_single_ref)]

Następnie wprowadź następujący kod, aby wykryć zestaw twarzy w innym obrazie. Te twarze będą celem.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_multiple_ref)]

### <a name="find-matches"></a>Znajdź dopasowania

Poniższy kod używa **[FindSimilar](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.FindSimilar)** metody, aby znaleźć wszystkie ściany docelowe, które pasują do ściany źródłowej.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar)]

### <a name="print-matches"></a>Drukuj dopasowania

Poniższy kod drukuje szczegóły dopasowania do konsoli.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_print)]


## <a name="create-and-train-a-person-group"></a>Tworzenie i szkolenie grupy osób

Aby przejść przez ten scenariusz, należy zapisać następujące obrazy w katalogu https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/imagesgłównym projektu: .

Ta grupa obrazów zawiera trzy zestawy obrazów jednotwych, które odpowiadają trzem różnym osobom. Kod zdefiniuje trzy obiekty **osoby PersonGroup Person** `woman`Person `man`i `child`skojarzy je z plikami obrazów rozpoczynającymi się od , i .

### <a name="create-persongroup"></a>Tworzenie grupy osób

Po pobraniu obrazów dodaj następujący kod na dole **metody głównej.** Ten kod uwierzytelnia obiekt **[PersonGroupClient,](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)** a następnie używa go do zdefiniowania nowej **grupy persongroup**.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_setup)]

### <a name="create-persongroup-persons"></a>Tworzenie osób z grupy osób

Następny blok kodu uwierzytelnia **[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)** i używa go do zdefiniowania trzech nowych **persongroup person** obiektów. Obiekty te reprezentują jedną osobę w zestawie obrazów.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_setup)]

### <a name="assign-faces-to-persons"></a>Przypisywanie ścian do osób

Poniższy kod sortuje obrazy według ich prefiksu, wykrywa twarze i przypisuje ściany do każdego odpowiedniego obiektu **PersonGroup Person Person,** na podstawie nazwy pliku obrazu.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_assign)]

### <a name="train-persongroup"></a>Grupa osób pociągu

Po przypisaniu ścian należy szkolić **grupę osób,** aby mogła identyfikować funkcje wizualne skojarzone z każdym z jej obiektów **Person.** Poniższy kod wywołuje metodę **pociągu** asynchronicznej i sonduje wynik, drukując stan do konsoli.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_train)]

## <a name="identify-a-face"></a>Identyfikowanie twarzy

Poniższy kod wykonuje obraz z wieloma twarzami i wygląda, aby znaleźć tożsamość każdej osoby na obrazie. Porównuje każdą wykrytą twarz z **grupą osób**, bazą danych różnych obiektów **osoby,** których rysy twarzy są znane.

> [!IMPORTANT]
> Aby uruchomić ten przykład, należy najpierw uruchomić kod w [create i wyszkolić grupę osób](#create-and-train-a-person-group).

### <a name="get-a-test-image"></a>Pobierz obraz testowy

Poniższy kod wygląda w katalogu głównym projektu dla obrazu _test-image-person-group.jpg_ i ładuje go do pamięci programu. Ten obraz można znaleźć w tym samym repozytorium, co https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/imagesobrazy używane w [twórz i trenuj grupę osób:](#create-and-train-a-person-group).

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_get)]

### <a name="detect-source-faces-in-test-image"></a>Wykrywanie powierzchni źródłowych na obrazie testowym

Następny blok kodu wykonuje zwykłe wykrywanie twarzy na obrazie testowym, aby pobrać wszystkie twarze i zapisać je w tablicy.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_detect)]

### <a name="identify-faces"></a>Identyfikowanie twarzy

Identify **[Identify](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.Identify)** Metoda przyjmuje tablicę wykrytych twarzy i porównuje je z **daną grupą persongroup** (zdefiniowaną i przeszkoloną we wcześniejszej sekcji). Jeśli można dopasować wykrytą twarz do **osoby** w grupie, zapisuje wynik.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id)]

Ten kod następnie drukuje szczegółowe wyniki dopasowania do konsoli.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_print)]


## <a name="verify-faces"></a>Weryfikowanie ścian

Operacja Verify przyjmuje identyfikator twarzy i inny identyfikator twarzy lub obiekt **Person** i określa, czy należą one do tej samej osoby.

Poniższy kod wykrywa twarze w dwóch obrazach źródłowych, a następnie weryfikuje każdy z nich względem twarzy wykrytej na obrazie docelowym.

### <a name="get-test-images"></a>Pobierz obrazy testowe

Następujące bloki kodu deklarują zmienne, które będą wskazywać obrazy docelowe i źródłowe dla operacji weryfikacji.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_images)]

### <a name="detect-faces-for-verification"></a>Wykrywanie twarzy w celu weryfikacji

Poniższy kod wykrywa twarze w obrazach źródłowych i docelowych i zapisuje je w zmiennych.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_source)]

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_target)]

### <a name="get-verification-results"></a>Uzyskaj wyniki weryfikacji

Poniższy kod porównuje każdy z obrazów źródłowych do obrazu docelowego i drukuje komunikat wskazujący, czy należą one do tej samej osoby.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver)]


## <a name="take-a-snapshot-for-data-migration"></a>Tworzenie migawki do migracji danych

Funkcja Migawki umożliwia przenoszenie zapisanych danych twarzy, takich jak przeszkolony **persongroup,** do innej subskrypcji usługi Azure Cognitive Services Face. Możesz użyć tej funkcji, jeśli na przykład utworzono obiekt **PersonGroup** przy użyciu bezpłatnej subskrypcji próbnej, a teraz chcesz przeprowadzić migrację do płatnej subskrypcji. Zobacz [Migracja danych twarzy,](../Face-API-How-to-Topics/how-to-migrate-face-data.md) aby uzyskać ogólny przegląd funkcji Migawki.

W tym przykładzie zostanie migrowanie **grupy osób utworzonej** w [umień i szkolenie grupy osób](#create-and-train-a-person-group). Można najpierw ukończyć tę sekcję lub użyć własnej konstrukcji danych twarzy.

### <a name="set-up-target-subscription"></a>Konfigurowanie subskrypcji docelowej

Po pierwsze musisz mieć drugą subskrypcję platformy Azure z zasobem Face; Można to zrobić, powtarzając kroki w sekcji [Konfigurowanie.](#set-up) 

Następnie należy utworzyć następujące zmienne w górnej części **metody głównej.** Należy również utworzyć nowe zmienne środowiskowe dla identyfikatora subskrypcji konta platformy Azure, a także klucz, punkt końcowy i identyfikator subskrypcji nowego konta (docelowego).

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_target_client)]

Następnie umieść wartość identyfikatora subskrypcji w tablicy dla następnych kroków.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_target_id)]

### <a name="authenticate-target-client"></a>Uwierzytelnij klienta docelowego

W dalszej części skryptu zapisz oryginalny obiekt klienta jako klienta źródłowego, a następnie uwierzytelnij nowy obiekt klienta dla subskrypcji docelowej. 

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_target_auth)]

### <a name="take-a-snapshot"></a>Tworzenie migawki

Następnym krokiem jest, aby zrobić migawkę z **[Take](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient.Take)**, który zapisuje dane twarzy oryginalnej subskrypcji do tymczasowej lokalizacji w chmurze. Ta metoda zwraca identyfikator używany do wykonywania zapytań o stan operacji.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_take)]

Następnie kwerendy identyfikator, dopóki operacja została zakończona.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_query)]

### <a name="apply-the-snapshot"></a>Stosowanie migawki

Użyj **[zastosuj](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient.Apply)** operacji, aby zapisać nowo przekazane dane twarzy do subskrypcji docelowej. Ta metoda zwraca również identyfikator.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_apply)]

Ponownie kwerendy identyfikator, dopóki operacja została zakończona.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_apply_query)]

Po wykonaniu tych kroków można uzyskać dostęp do konstrukcji danych twarzy z nowej subskrypcji (docelowej).

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację Go `go run [arguments]` za pomocą polecenia z katalogu aplikacji.

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie innych skojarzonych z nią zasobów.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Jeśli w tym przewodniku Szybki start utworzono **grupę osób** i chcesz ją usunąć, należy wywołać metodę **[Usuń.](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient.Delete)** Jeśli dane zostały zmigrowane przy użyciu funkcji Migawka w tym przewodniku Szybki start, należy również usunąć **grupę persongroup** zapisaną w subskrypcji docelowej.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak korzystać z biblioteki Twarz dla programu Go do wykonywania zadań bazowych. Następnie zapoznaj się z dokumentacją referencyjną, aby dowiedzieć się więcej o bibliotece.

> [!div class="nextstepaction"]
> [Odwołanie do interfejsu API twarzy (Go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face)

* [Co to jest usługa rozpoznawania twarzy?](../overview.md)
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/Face/FaceQuickstart.go).
