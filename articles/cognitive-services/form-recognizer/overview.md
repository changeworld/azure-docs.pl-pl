---
title: Co to jest rozpoznawanie formularzy?
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak użyć aparatu rozpoznawania formularza do analizowania danych formularza i tabeli.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 04/08/2019
ms.author: pafarley
ms.openlocfilehash: 82ee2aa5627ac5fa4584f5af6b6b80cc2813c667
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441836"
---
# <a name="what-is-form-recognizer"></a>Co to jest rozpoznawanie formularzy?

Usługa rozpoznawania formularzy na platformie Azure to usługa poznawcza używająca technologii uczenia maszynowego do identyfikowania i wyodrębniania par klucz-wartość i danych tabeli z dokumentów formularzy. Następnie wyświetla ustrukturyzowane dane, które obejmują relacje w oryginalnym pliku. Aby wywołać niestandardowy model rozpoznawania formularza, mniejsza złożoność i łatwo zintegrować go z przepływu pracy lub aplikacji przy użyciu prostego interfejsu API REST. Aby rozpocząć, wystarczy pięć dokumentów wypełniony formularz lub dwie formy wypełniane oraz pusty formularz z tego samego typu co Twoje materiały danych wejściowych. Możesz szybko uzyskać dokładne wyniki, które są dostosowane do określonej zawartości bez dużych ręcznej interwencji lub wiele różnych danych do analizy doświadczenia.

## <a name="request-access"></a>Żądaj dostępu
Aparat rozpoznawania formularza jest dostępna w wersji zapoznawczej dostęp ograniczony. Aby uzyskać dostęp do wersji zapoznawczej, wypełnij i Prześlij [żądanie dostępu do rozpoznawania formularza](https://aka.ms/FormRecognizerRequestAccess) formularza. Formularz żądania informacji o Tobie, firmy i Scenariusz użytkownika, dla której użyjesz rozpoznawania formularza. Jeśli Twoje żądanie zostanie zatwierdzone przez zespół usługi Azure Cognitive Services, otrzymasz wiadomość e-mail z instrukcjami dotyczącymi uzyskiwania dostępu do usługi.

## <a name="what-it-does"></a>Wyniki działania

Po przesłaniu danych wejściowych algorytm szkolenie modeli, klastry formularze według typów, odnajduje, jakie klucze i tabele są obecne i uczy się skojarzyć wartości, aby klucze i wpisy w tabelach. Uczenie nienadzorowane umożliwia modelowi poznać układ i relacje między polami oraz wpisy bez ręcznego dodawania etykiet danych lub intensywnego kodowania i konserwacji. Z drugiej strony modele uczenia maszynowego wstępnie przeszkolonych wymaganych danych standardowych i są mniej dokładny w przypadku korzystania z materiałów wsadowych, która odbiega od tradycyjnych formatów, takich jak formularze branżowych.

Po użytkownik nauczenia modelu, można przetestować i trenuj je ponownie, a ostatecznie umożliwia niezawodne wyodrębnianie danych z więcej formularzy zgodnie z potrzebami.

## <a name="what-it-includes"></a>Co zawiera

Aparat rozpoznawania formularza jest dostępna jako interfejs API REST. Tworzenie, szkolenie i ocenianie modelu za pomocą wywołania interfejsu API. Jeśli chcesz, możesz uruchomić model w lokalnym kontenerze Docker.

## <a name="input-requirements"></a>Wymagania dotyczące danych wejściowych

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="where-do-i-start"></a>Od czego zacząć?

**Krok 1:** Utwórz zasób rozpoznawania formularza w witrynie Azure portal.

**Krok 2:** Wypróbuj Szybki Start, aby uzyskać praktyczne doświadczenie w pracy:
* [Szybki start: Uczenie modelu rozpoznawania formularza i wyodrębnić dane formularza za pomocą interfejsu API REST za pomocą programu cURL](quickstarts/curl-train-extract.md)
* [Szybki start: Wytrenuj model rozpoznawania formularza i wyodrębnić dane formularza za pomocą interfejsu API REST przy użyciu języka Python](quickstarts/python-train-extract.md)

Firma Microsoft zaleca korzystanie z bezpłatnej usługi, gdy jest zapoznanie się z technologią, ale należy pamiętać, że liczbę stron bezpłatna jest ograniczona do 500 stron miesięcznie.

**Krok 3:** Przegląd interfejsów API REST

Nauczanie i wyodrębniania danych ze strukturą z formularzy przy użyciu następujących interfejsów API.

| Interfejs API REST | Opis |
|-----|-------------|
| Szkolenie | Szkolenie nowy model do analizowania formularzy za pomocą formularzy pięć tego samego typu. Lub jego trenowanie za pomocą pusty formularz i dwie formy wypełnione.  |
| Analiza  |Analizuj pojedynczy dokument przekazywany jako strumień do wyodrębnienia pary klucz wartość i tabele w formularzu za pomocą niestandardowego modelu.  |

Zapoznaj się z [dokument referencyjny dotyczący interfejsu API REST](https://aka.ms/form-recognizer/api). 

## <a name="data-privacy-and-security"></a>Prywatność i zabezpieczenia danych

Ta usługa jest oferowana jako [Podgląd](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) usługi platformy Azure w ramach [postanowieniami dotyczącymi świadczenia usług Online](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31). Podobnie jak w przypadku wszystkich usług cognitive services, deweloperzy korzystający z usługi rozpoznawania formularza powinien wiedzieć o zasady firmy Microsoft w sprawie danych klienta. Zobacz [stronę usług Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) w Centrum zaufania firmy Microsoft, aby dowiedzieć się więcej.

## <a name="next-steps"></a>Kolejne kroki

Wykonaj [Szybki Start](quickstarts/curl-train-extract.md) wprowadzenie [interfejsy API rozpoznawania fragmentów](https://aka.ms/form-recognizer/api).
