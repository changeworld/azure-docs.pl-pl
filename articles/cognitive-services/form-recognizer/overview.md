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
ms.openlocfilehash: 2a120a59a58eb8d7a017cce0dd85c21038bdcf51
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65143220"
---
# <a name="what-is-form-recognizer"></a>Co to jest rozpoznawanie formularzy?

Usługa rozpoznawania formularzy na platformie Azure to usługa poznawcza używająca technologii uczenia maszynowego do identyfikowania i wyodrębniania par klucz-wartość i danych tabeli z dokumentów formularzy. Następnie wyświetla ustrukturyzowane dane, które obejmują relacje w oryginalnym pliku. Możesz wywołać niestandardowy model rozpoznawania formularza przy użyciu prostego interfejsu API REST w celu zredukowania złożoności i łatwo zintegrować go z przepływu pracy lub aplikacji. Wystarczy pięć dokumentów formularza lub pusty formularz tego samego typu co Twoje wejściowych materiału na rozpoczęcie pracy. Aby uzyskać wyniki szybko, dokładnie i dostosowane do określonych zawartości bez konieczności mocno ręcznej interwencji lub wiele różnych danych do analizy doświadczenia.

## <a name="request-access"></a>Żądanie dostępu
Aparat rozpoznawania formularza jest dostępne w wersji zapoznawczej dostęp ograniczony. Aby uzyskać dostęp do wersji zapoznawczej, wypełnij i Prześlij [żądanie dostępu do rozpoznawania formularz usługi Cognitive Services](https://aka.ms/FormRecognizerRequestAccess) formularza. Formularz żądania informacji o Tobie, firmy i Scenariusz użytkownika, dla której użyjesz rozpoznawania formularza. Jeśli Twoje żądanie zostanie zatwierdzone przez zespół usługi Azure Cognitive Services, otrzymasz wiadomość e-mail z instrukcjami dotyczącymi sposobu uzyskania dostępu do usługi.

## <a name="what-it-does"></a>Wyniki działania

Po przesłaniu danych wejściowych algorytm szkolenie modeli, klastry formularze według typów, odnajduje, jakie klucze i tabele są obecne i uczy się skojarzyć wartości, aby klucze i wpisy w tabelach. Uczenie nienadzorowane umożliwia modelowi poznać układ i relacje między polami oraz wpisy bez ręcznego dodawania etykiet danych lub intensywnego kodowania i konserwacji. Z drugiej strony uczenia maszynowego wstępnie szkolone modele wymaganych danych standardowych i są mniej dokładne z materiałem danych wejściowych, która odbiega od tradycyjnych formatów, takich jak formularze branżowych.

Gdy model jest uczony, można przetestować, ponowne szkolenie i ostatecznie umożliwia niezawodne wyodrębnianie danych z więcej formularzy zgodnie z potrzebami.

## <a name="what-it-includes"></a>Co zawiera

Aparat rozpoznawania formularza jest dostępna jako interfejs API REST. Możesz tworzyć, szkolenie i ocenianie modelu za pomocą wywołania interfejsu API i opcjonalnie można uruchomić model w lokalnym kontenerze Docker.

## <a name="input-requirements"></a>Wymagania dotyczące danych wejściowych

Aparat rozpoznawania formularz działa w wejściowych dokumentów, które spełniają następujące wymagania:

* Format JPG, PNG lub PDF (tekst lub skanowane). Tekst osadzone pliki PDF są preferowane, ponieważ istnieje możliwość wystąpienia błędu w tej lokalizacji i wyodrębniania znaków.
* Rozmiar pliku musi być mniejszy niż 4 megabajty (MB)
* Dla obrazów wymiary musi należeć do zakresu od 50 x 50 i 4200 x 4200 pikseli
* Jeśli zeskanowane dokumenty papieru, formularze powinna być wysokiej jakości skanowania
* Należy użyć alfabetu łacińskiego (angielskie znaki)
* Drukowanych danych (nie odręcznego)
* Musi zawierać klucze i wartości
* Klucze mogą być wyświetlane powyżej lub po lewej stronie wartości, ale nie niższej lub w prawo.

Ponadto jeszcze rozpoznawania formularza nie obsługuje następujących typów danych wejściowych:

* Złożone tabele (zagnieżdżonych tabel, scalone nagłówki lub komórki itd.) 
* Przycisk pola wyboru lub radiowych
* Dokumenty PDF jest dłuższa niż 50 stron

## <a name="where-do-i-start"></a>Od czego zacząć?

**Krok 1:** Utwórz zasób rozpoznawania formularza w witrynie Azure portal.

**Krok 2:** Wypróbuj Szybki Start, aby uzyskać praktyczne doświadczenie w pracy:
* [Szybki start: Wytrenuj model rozpoznawania formularza i wyodrębnianie danych formularza przy użyciu interfejsu API REST za pomocą programu cURL](quickstarts/curl-train-extract.md)
* [Szybki start: Wytrenuj model rozpoznawania formularza i wyodrębnianie danych formularza przy użyciu interfejsu API REST przy użyciu języka Python](quickstarts/python-train-extract.md)

Firma Microsoft zaleca bezpłatna usługa dla celów szkoleniowych, ale należy pamiętać, że liczbę stron bezpłatna jest ograniczona do 500 stron miesięcznie.

**Krok 3:** Przejrzyj użycie interfejsu API REST następujące interfejsy API w celu nauczenia i wyodrębniania danych ze strukturą z formularzy.

| Interfejs API REST | Opis |
|-----|-------------|
| Szkolenie | Szkolenie nowy model do analizowania formularzy przy użyciu formularzy 5 z tego samego typu lub pusty formularz.  |
| Analiza  |Analizuj pojedynczy dokument przekazywany jako strumień do wyodrębnienia pary klucz wartość i tabele w formularzu za pomocą niestandardowego modelu.  |

Zapoznaj się z [dokument referencyjny dotyczący interfejsu API REST](https://aka.ms/form-recognizer/api). 

## <a name="data-privacy-and-security"></a>Prywatność i zabezpieczenia danych

Usługa jest oferowana jako [Podgląd](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) usługi platformy Azure w ramach [postanowieniami dotyczącymi świadczenia usług Online](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31). Jak w przypadku wszystkich usług Cognitive deweloperzy korzystający z usługi rozpoznawania formularza powinien wiedzieć o zasady firmy Microsoft w sprawie danych klienta. Zobacz [stronę usług Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) w Centrum zaufania firmy Microsoft, aby dowiedzieć się więcej.

## <a name="next-steps"></a>Kolejne kroki

Postępuj zgodnie z [Szybki Start](quickstarts/curl-train-extract.md) rozpocząć pracę z usługą [interfejsy API rozpoznawania fragmentów](https://aka.ms/form-recognizer/api).
