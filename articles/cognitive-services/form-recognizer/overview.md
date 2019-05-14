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
ms.openlocfilehash: 8fb382227c71fce7ebe062057adf5edfb90a1a92
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2019
ms.locfileid: "65601629"
---
# <a name="what-is-form-recognizer"></a>Co to jest rozpoznawanie formularzy?

Usługa rozpoznawania formularzy na platformie Azure to usługa poznawcza używająca technologii uczenia maszynowego do identyfikowania i wyodrębniania par klucz-wartość i danych tabeli z dokumentów formularzy. Następnie wyświetla ustrukturyzowane dane, które obejmują relacje w oryginalnym pliku. Aby wywołać niestandardowy model rozpoznawania formularza, mniejsza złożoność i łatwo zintegrować go z przepływu pracy lub aplikacji przy użyciu prostego interfejsu API REST. Aby rozpocząć, wystarczy pięć dokumentów formularza lub pusty formularz z tego samego typu co Twoje materiały danych wejściowych. Możesz szybko uzyskać dokładne wyniki, które są dostosowane do określonej zawartości bez dużych ręcznej interwencji lub wiele różnych danych do analizy doświadczenia.

## <a name="request-access"></a>Żądaj dostępu
Aparat rozpoznawania formularza jest dostępna w wersji zapoznawczej dostęp ograniczony. Aby uzyskać dostęp do wersji zapoznawczej, wypełnij i Prześlij [żądanie dostępu do rozpoznawania formularza](https://aka.ms/FormRecognizerRequestAccess) formularza. Formularz żądania informacji o Tobie, firmy i Scenariusz użytkownika, dla której użyjesz rozpoznawania formularza. Jeśli Twoje żądanie zostanie zatwierdzone przez zespół usługi Azure Cognitive Services, otrzymasz wiadomość e-mail z instrukcjami dotyczącymi uzyskiwania dostępu do usługi.

## <a name="what-it-does"></a>Wyniki działania

Po przesłaniu danych wejściowych algorytm szkolenie modeli, klastry formularze według typów, odnajduje, jakie klucze i tabele są obecne i uczy się skojarzyć wartości, aby klucze i wpisy w tabelach. Uczenie nienadzorowane umożliwia modelowi poznać układ i relacje między polami oraz wpisy bez ręcznego dodawania etykiet danych lub intensywnego kodowania i konserwacji. Z drugiej strony modele uczenia maszynowego wstępnie przeszkolonych wymaganych danych standardowych i są mniej dokładny w przypadku korzystania z materiałów wsadowych, która odbiega od tradycyjnych formatów, takich jak formularze branżowych.

Po użytkownik nauczenia modelu, można przetestować i trenuj je ponownie, a ostatecznie umożliwia niezawodne wyodrębnianie danych z więcej formularzy zgodnie z potrzebami.

## <a name="what-it-includes"></a>Co zawiera

Aparat rozpoznawania formularza jest dostępna jako interfejs API REST. Tworzenie, szkolenie i ocenianie modelu za pomocą wywołania interfejsu API. Jeśli chcesz, możesz uruchomić model w lokalnym kontenerze Docker.

## <a name="input-requirements"></a>Wymagania dotyczące danych wejściowych

Aparat rozpoznawania formularza pracuje dokumentów wejściowych, które spełniają te wymagania:

* Musi być w formacie JPG, PNG lub PDF (tekst lub skanowane). Osadzony tekst plików PDF są najlepsze, ponieważ istnieje możliwość wystąpienia błędu w tej lokalizacji i wyodrębniania znaków.
* Rozmiar pliku musi być mniejszy niż 4 megabajty (MB).
* W przypadku obrazów wymiarów musi mieć od 50 x 50 pikseli i 4200 x 4200 pikseli.
* Jeśli zeskanowane dokumenty papieru, formularze powinna być wysokiej jakości skanowania.
* Tekst musi używać alfabetu łacińskiego (angielskie znaki).
* Dane muszą być drukowane (nie odręcznego).
* Dane mogą zawierać klucze i wartości.
* Klucze mogą być wyświetlane powyżej lub po lewej stronie wartości, ale nie niższej lub w prawo.

Aparat rozpoznawania formularza nie obsługuje obecnie następujące typy danych wejściowych:

* Złożone tabele (zagnieżdżonych tabel, scalone nagłówki lub komórek i tak dalej).
* Przycisk pola wyboru lub opcji.
* Dokumenty PDF jest dłuższa niż 50 stron.

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
| Szkol | Szkolenie nowy model do analizowania formularzy przy użyciu pięć formularze z tego samego typu lub pusty formularz.  |
| Analyze  |Analizuj pojedynczy dokument przekazywany jako strumień do wyodrębnienia pary klucz wartość i tabele w formularzu za pomocą niestandardowego modelu.  |

Zapoznaj się z [dokument referencyjny dotyczący interfejsu API REST](https://aka.ms/form-recognizer/api). 

## <a name="data-privacy-and-security"></a>Prywatność i zabezpieczenia danych

Ta usługa jest oferowana jako [Podgląd](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) usługi platformy Azure w ramach [postanowieniami dotyczącymi świadczenia usług Online](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31). Podobnie jak w przypadku wszystkich usług cognitive services, deweloperzy korzystający z usługi rozpoznawania formularza powinien wiedzieć o zasady firmy Microsoft w sprawie danych klienta. Zobacz [stronę usług Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) w Centrum zaufania firmy Microsoft, aby dowiedzieć się więcej.

## <a name="next-steps"></a>Kolejne kroki

Wykonaj [Szybki Start](quickstarts/curl-train-extract.md) wprowadzenie [interfejsy API rozpoznawania fragmentów](https://aka.ms/form-recognizer/api).
