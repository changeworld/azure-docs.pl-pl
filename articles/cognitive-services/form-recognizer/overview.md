---
title: Co to jest rozpoznawanie formularzy?
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak użyć aparatu rozpoznawania formularza do analizowania danych formularza i tabeli.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 07/01/2019
ms.author: pafarley
ms.openlocfilehash: a17e47fb059c23ab2e6eb69f3cfe6f2f8d0e51b2
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67502894"
---
# <a name="what-is-form-recognizer"></a>Co to jest rozpoznawanie formularzy?

Azure rozpoznawania formularza jest usługi cognitive service używa technologii uczenia maszynowego do identyfikacji i prowadzenie dokumentów w postaci pary klucz/wartość i dane w tabeli. Następnie wyświetla ustrukturyzowane dane, które obejmują relacje w oryginalnym pliku. Aby wywołać niestandardowy model rozpoznawania formularza, mniejsza złożoność i łatwo zintegrować go z przepływu pracy lub aplikacji przy użyciu prostego interfejsu API REST. Aby rozpocząć, wystarczy pięć dokumentów wypełniony formularz lub dwie formy wypełniane oraz pusty formularz z tego samego typu co Twoje materiały danych wejściowych. Możesz szybko uzyskać dokładne wyniki, które są dostosowane do określonej zawartości bez dużych ręcznej interwencji lub wiele różnych danych do analizy doświadczenia.

## <a name="custom-models"></a>Modele niestandardowe

Niestandardowy model rozpoznawania formularza szkolenie modeli do swoich danych, a dzięki temu wystarczy pięć przykładowych danych wejściowych formularzy można uruchomić. Po przesłaniu danych wejściowych algorytm klastrów formularze według typu, odnajduje, jakie klucze i tabele są obecne i kojarzy wartości, aby klucze i wpisy w tabelach. Następnie wyświetla ustrukturyzowane dane, które obejmują relacje w oryginalnym pliku. Po użytkownik nauczenia modelu, można przetestować i trenuj je ponownie, a ostatecznie umożliwia niezawodne wyodrębnianie danych z więcej formularzy zgodnie z potrzebami.

Uczenie nienadzorowane umożliwia modelowi poznać układ i relacje między polami oraz wpisy bez ręcznego dodawania etykiet danych lub intensywnego kodowania i konserwacji. Z drugiej strony modele uczenia maszynowego wstępnie przeszkolonych wymagają standardowych danych. Są one mniej dokładne z materiałem danych wejściowych, która odbiega od tradycyjnych formatów, takich jak formularze branżowych.

## <a name="pre-built-receipt-model"></a>Potwierdzenie wstępnie utworzonych modeli

Aparat rozpoznawania formularz zawiera także modelu na potrzeby odczytywania paragonów. Ten model wyodrębnia informacje o kluczu, takie jak Data i godzina transakcji, handlowca informacji, ilości podatki i sum i innych. Ponadto model wstępnie skompilowanych potwierdzenia jest uczony do rozpoznawania i zwraca cały tekst na potwierdzenie.

## <a name="what-it-includes"></a>Co zawiera

Aparat rozpoznawania formularza jest dostępna jako interfejs API REST. Można utworzyć, szkolenie i oceniania modelu niestandardowego lub dostęp do modelu wstępnie utworzonych za pomocą tych interfejsów API. Jeśli chcesz, możesz uczyć i uruchamianie niestandardowych modeli w lokalnym kontenerze Docker.

## <a name="input-requirements-custom-model"></a>Wymagania wprowadzania (niestandardowy model)

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="request-access"></a>Żądaj dostępu

Aparat rozpoznawania formularza jest dostępna w wersji zapoznawczej dostęp ograniczony. Aby uzyskać dostęp do wersji zapoznawczej, wypełnij i Prześlij [żądanie dostępu do rozpoznawania formularza](https://aka.ms/FormRecognizerRequestAccess) formularza. Formularz żądania informacji o Tobie, firmy i Scenariusz użytkownika, dla której użyjesz rozpoznawania formularza. Jeśli Twoje żądanie zostanie zatwierdzone przez zespół usługi Azure Cognitive Services, otrzymasz wiadomość e-mail z instrukcjami dotyczącymi uzyskiwania dostępu do usługi.

## <a name="where-do-i-start"></a>Od czego zacząć?

**Krok 1:** Utwórz zasób rozpoznawania formularza w witrynie Azure portal.

**Krok 2:** Wykonaj Przewodnik Szybki Start za pomocą interfejsu API REST:
* [Szybki start: Uczenie modelu rozpoznawania formularza i wyodrębnić dane formularza za pomocą interfejsu API REST za pomocą programu cURL](quickstarts/curl-train-extract.md)
* [Szybki start: Wytrenuj model rozpoznawania formularza i wyodrębnić dane formularza za pomocą interfejsu API REST przy użyciu języka Python](quickstarts/python-train-extract.md)
* [Szybki start: Wyodrębnianie danych otrzymania przy użyciu programu cURL](quickstarts/curl-receipts.md)
* [Szybki start: Wyodrębnianie danych otrzymania przy użyciu języka Python](quickstarts/python-receipts.md)

Zalecamy użycie bezpłatnej usługi, gdy jest zapoznanie się z technologią. Należy pamiętać, że liczba stron bezpłatna jest ograniczona do 500 miesięcznie.

**Krok 3:** Przegląd interfejsów API REST

Nauczanie i wyodrębniania danych ze strukturą z formularzy przy użyciu następujących interfejsów API.

|||
|---|---|
| Trenowanie modelu| Szkolenie nowy model do analizowania formularzy za pomocą formularzy pięć tego samego typu. Lub jego trenowanie za pomocą pusty formularz i dwie formy wypełnione.  |
| Analizowanie formularza |Analizuj pojedynczy dokument przekazywany jako strumień do wyodrębnienia pary klucz/wartość i tabele w formularzu za pomocą niestandardowego modelu.  |
| Analizowanie potwierdzenia |Analizowanie dokumentu jednego potwierdzenia, wyodrębnić kluczowe informacje i inne teksty potwierdzenia.|

Zapoznaj się z [dokumentacji interfejsu API REST](https://aka.ms/form-recognizer/api) Aby dowiedzieć się więcej. 

## <a name="data-privacy-and-security"></a>Prywatność i zabezpieczenia danych

Ta usługa jest oferowana jako [Podgląd](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) usługi platformy Azure w ramach [postanowieniami dotyczącymi świadczenia usług Online](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31). Podobnie jak w przypadku wszystkich usług cognitive services, deweloperzy korzystający z usługi rozpoznawania formularza powinien wiedzieć o zasady firmy Microsoft w sprawie danych klienta. Zobacz [stronę usług Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) w Centrum zaufania firmy Microsoft, aby dowiedzieć się więcej.

## <a name="next-steps"></a>Kolejne kroki

Wykonaj [Szybki Start](quickstarts/curl-train-extract.md) wprowadzenie [interfejsy API rozpoznawania fragmentów](https://aka.ms/form-recognizer/api).
