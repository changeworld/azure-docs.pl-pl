---
title: Co to jest rozpoznawanie formularzy?
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak analizować dane formularzy i tabel przy użyciu aparatu rozpoznawania formularzy.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 07/25/2019
ms.author: pafarley
ms.openlocfilehash: b07201556f08bde4ef8c7a7904c6619a126d7765
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68594546"
---
# <a name="what-is-form-recognizer"></a>Co to jest rozpoznawanie formularzy?

Aparat rozpoznawania formularzy platformy Azure to usługa poznawczej, która korzysta z technologii uczenia maszynowego do identyfikowania i wyodrębniania par klucz/wartość oraz danych tabeli z dokumentów formularzy. Następnie wyświetla ustrukturyzowane dane, które obejmują relacje w oryginalnym pliku. Możesz wywołać własny model aparatu rozpoznawania formularzy przy użyciu prostego interfejsu API REST, aby zmniejszyć złożoność i łatwo zintegrować go z przepływem pracy lub aplikacją. Aby rozpocząć, wystarczy pięć wypełnionych dokumentów formularzy lub dwóch wypełnionych formularzy i pusty formularz tego samego typu co materiał wejściowy. Szybko uzyskuj dokładne wyniki, które są dostosowane do konkretnej zawartości, bez znacznej interwencji ręcznej lub szczegółowej wiedzy o nauce danych.

## <a name="custom-models"></a>Modele niestandardowe

Niestandardowy model aparatu rozpoznawania formularzy pociąga za siebie własne dane i wystarczy pięć przykładowych formularzy wejściowych do uruchomienia. Gdy przesyłasz dane wejściowe, algorytm tworzy klastry według typu, odnajduje, jakie klucze i tabele są obecne i kojarzy wartości z kluczami i wpisami w tabelach. Następnie wyświetla ustrukturyzowane dane, które obejmują relacje w oryginalnym pliku. Po przeprowadzeniu szkolenia modelu można testować i przeszkolić go i ostatecznie użyć do niezawodnego wyodrębnienia danych z większej liczby formularzy zgodnie z potrzebami.

Uczenie nienadzorowane umożliwia modelowi poznać układ i relacje między polami oraz wpisy bez ręcznego dodawania etykiet danych lub intensywnego kodowania i konserwacji. Z kolei wstępnie przeszkolone modele uczenia maszynowego wymagają standardowych danych. Są one mniej dokładne z materiałami wejściowymi, które różnią się od tradycyjnych formatów, takich jak formularze specyficzne dla branż.

## <a name="prebuilt-receipt-model"></a>Wstępnie utworzony model przyjęcia

Aparat rozpoznawania formularzy zawiera również model do odczytywania przyjęć sprzedaży. Ten model wyodrębnia najważniejsze informacje, takie jak godzina i Data transakcji, informacje o sprzedawcy, kwoty podatków i sumy itd. Ponadto wstępnie utworzony model paragonów jest szkolony do rozpoznawania i zwracania całego tekstu w paragonie.

## <a name="what-it-includes"></a>Co zawiera

Aparat rozpoznawania formularzy jest dostępny jako interfejs API REST. Korzystając z tych interfejsów API, można utworzyć, wyszkolić i wyprowadzić niestandardowy model lub uzyskać dostęp do prebudowanego modelu. Jeśli chcesz, możesz nauczyć i uruchamiać modele niestandardowe w lokalnym kontenerze platformy Docker.

## <a name="input-requirements"></a>Wymagania wejściowe
### <a name="custom-model"></a>Model niestandardowy

[!INCLUDE [input requirements](./includes/input-requirements.md)]

### <a name="prebuilt-receipt-model"></a>Wstępnie utworzony model przyjęcia

Wymagania dotyczące danych wejściowych dla modelu paragonu są nieco inne.

* Format musi być JPEG, PNG, BMP, PDF (tekst lub skanowany) lub TIFF.
* Rozmiar pliku musi być mniejszy niż 20 MB.
* Wymiary obrazu muszą zawierać się w przedziale od 50 x 50 pikseli i 10000 x 10000 pikseli. 
* Wymiary PDF muszą mieć co najwyżej 17 x 17 cali, odpowiadające rozmiarowi papieru legalnego lub A3 i mniejszemu.
* W przypadku plików PDF i TIFF tylko pierwsze 200 stron jest przetwarzanych (z subskrypcją warstwy Bezpłatna, są przetwarzane tylko dwie pierwsze strony).

## <a name="request-access"></a>Żądaj dostępu

Aparat rozpoznawania formularzy jest dostępny w wersji zapoznawczej o ograniczonym dostępie. Aby uzyskać dostęp do wersji zapoznawczej, Wypełnij i Prześlij formularz [żądania dostępu do aparatu rozpoznawania formularza](https://aka.ms/FormRecognizerRequestAccess) . Formularz żąda informacji o użytkowniku, firmie i scenariuszu użytkownika, dla którego będziesz używać aparatu rozpoznawania formularza. Jeśli żądanie zostało zatwierdzone przez zespół usługi Azure Cognitive Services, otrzymasz wiadomość e-mail z instrukcjami dotyczącymi uzyskiwania dostępu do usługi.

## <a name="where-do-i-start"></a>Od czego zacząć?

**Krok 1:** Utwórz zasób aparatu rozpoznawania formularzy w Azure Portal.

**Krok 2:** Postępuj zgodnie z przewodnikiem Szybki Start, aby użyć interfejsu API REST:
* [Szybki start: Uczenie modelu aparatu rozpoznawania formularzy i wyodrębnianie danych formularza przy użyciu interfejsu API REST z zwinięciem](quickstarts/curl-train-extract.md)
* [Szybki start: Uczenie modelu aparatu rozpoznawania formularzy i wyodrębnianie danych formularza przy użyciu interfejsu API REST w języku Python](quickstarts/python-train-extract.md)
* [Szybki start: Wyodrębnij dane przyjęcia przy użyciu zwinięcia](quickstarts/curl-receipts.md)
* [Szybki start: Wyodrębnij dane przyjęcia przy użyciu języka Python](quickstarts/python-receipts.md)

Zalecamy korzystanie z bezpłatnej usługi podczas nauki technologii. Należy pamiętać, że liczba bezpłatnych stron jest ograniczona do 500 miesięcznie.

**Krok 3.** Przeglądanie interfejsów API REST

Poniższe interfejsy API służą do uczenia i wyodrębnienia danych strukturalnych z formularzy.

|||
|---|---|
| Trenowanie modelu| Uczenie nowego modelu do analizowania formularzy przy użyciu pięciu postaci tego samego typu. Lub pouczenie się z pustym formularzem i dwoma wypełnionymi formularzami.  |
| Analiza formularza |Analizuj pojedynczy dokument przesłany jako strumień, aby wyodrębnić pary klucz/wartość i tabele z formularza z modelem niestandardowym.  |
| Analizuj potwierdzenie |Analizuj pojedynczy dokument paragonu w celu wyodrębnienia informacji o kluczu i innego tekstu paragonu.|

Zapoznaj się z [dokumentacją interfejsu API REST](https://aka.ms/form-recognizer/api) , aby dowiedzieć się więcej. 

## <a name="data-privacy-and-security"></a>Prywatność i zabezpieczenia danych

Ta usługa jest oferowana jako [wersja](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) zapoznawcza usługi platformy Azure w ramach [warunków świadczenia usługi online](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31). Podobnie jak w przypadku wszystkich usług poznawczych, deweloperzy korzystający z usługi rozpoznawania formularzy powinni mieć świadomość zasad firmy Microsoft dotyczących danych klientów. Zobacz [stronę usług Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) w Centrum zaufania firmy Microsoft, aby dowiedzieć się więcej.

## <a name="next-steps"></a>Następne kroki

Ukończ [Przewodnik Szybki Start](quickstarts/curl-train-extract.md) , aby rozpocząć pracę z [interfejsami API aparatu rozpoznawania formularzy](https://aka.ms/form-recognizer/api).