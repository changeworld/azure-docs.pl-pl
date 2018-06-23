---
title: Niestandardowe wymowy za pomocą niestandardowej usługi rozpoznawania mowy na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć model języka w usłudze mowy niestandardowe w usługach kognitywnych.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 11/23/2017
ms.author: panosper
ms.openlocfilehash: a74b69b84cc80809a25f18b580a18abb5721b8b1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347073"
---
# <a name="enable-custom-pronunciation"></a>Włącz wymowy niestandardowych
Niestandardowe wymowy umożliwia użytkownikom na definiowanie formularza fonetyczne i wyświetlenie termin lub słowo. Jest to przydatne w przypadku obsługi dostosowane warunków, takich jak nazwy produktu lub akronimów. Wymagany jest plikiem wymowy (plik txt prosty).

Oto jak to działa. W pliku pojedynczy txt można wprowadzić kilka wpisów niestandardowych wymowy. Struktura jest następujący:

```
Display form <Tab> Spoken form <Newline>
```

*Przykłady*:

| Wyświetl formularz | Mówionej formy |
|----------|-------|
| C3PO | Zobacz trzy pea o |
| L8R | późne są |
| CNTK | Zobacz n zepołowy k|

## <a name="requirements-for-the-spoken-form"></a>Wymagania dotyczące mówionej formy
Mówionej formy musi być pisane małymi literami, które można wymusić podczas importowania. Ponadto należy podać kontroli w odbierający dane. Nie karty w formularzu wyświetlania lub mówionej formy jest dozwolone. Może jednak, więcej zabronione znaki w postaci wyświetlanej (na przykład ~ i ^).

Każdy plik txt może mieć wiele wpisów. Na przykład zobacz poniższy zrzut ekranu:

![Zrzut ekranu Notatnik kilka wpisy dla wymowy akronim](../../../media/cognitive-services/custom-speech-service/custom-speech-pronunciation-file.png)

Mówionej formy jest fonetyczne sekwencji wyświetlania formularza. Składa się z liter, słowa lub sylab. Obecnie nie jest żadne dalsze wskazówki ani zbiór standardów, aby sformułować mówionej formy. 

## <a name="supported-pronunciation-characters"></a>Obsługiwane wymowy znaków
Niestandardowe wymowy jest obecnie obsługiwane w języku angielskim (en US) i niemiecki (de-de). W poniższej tabeli przedstawiono to zestaw znaków, który może służyć do wyrażenia mówionej formy termin (w pliku wymowy niestandardowych): 

| Język | Znaki |
|---------- |----------|
| Angielski (en US) | , b, c, d, e, f, g, h, i, j, k, l, e, p, q, r, s, t, u, v, w, x, y, z |
| Niemiecki (de-de) | ä strumień świetlny, znaki ü, ẞ, a, b, c, d, e, f, g, h, i, j, k, l, e, p, q, r, s, t, u, v, w, x, y, z |

>[UWAGA] Termin wyświetlania formularza (w pliku wymowy) powinna być zapisana w zestawie danych dostosowania języka taki sam sposób.

## <a name="requirements-for-the-display-form"></a>Wymagania dotyczące wyświetlania formularza
Wyświetl formularz można tylko niestandardowe programu word, termin, akronim lub wyrazy złożone łączące istniejące słowa. Można także wprowadzić alternatywne wymowy popularnych wyrazów. 

>[!NOTE]
Nie zaleca się używania tej funkcji, aby sformułować popularnych wyrazów lub zmodyfikować mówionej formy. Najlepiej dekoder, aby zobaczyć, jeśli niektórych nietypowe wyrazów (na przykład skróty wyrazy techniczne i słowa obce) nie są prawidłowo zdekodować. W takim przypadku należy dodać je do pliku niestandardowych wymowy. W modelu języka należy zawsze i tylko używać Wyświetl formularz wyrazu. 

## <a name="requirements-for-the-file-size"></a>Wymagania dotyczące rozmiar pliku
Rozmiar pliku txt, zawierający wpisy wymowy wynosi 1 MB. Zazwyczaj nie trzeba przekazać dużych ilości danych przy użyciu tego pliku. Większość plików wymowy niestandardowe mogą zostać kilka KB/s w rozmiarze. Kodowanie pliku txt dla wszystkich ustawień regionalnych powinna być BOM UTF-8. Angielską dopuszczalne jest również ANSI.

## <a name="next-steps"></a>Kolejne kroki
* Zwiększyć dokładność rozpoznawania przez utworzenie użytkownika [niestandardowych modelu akustycznego](cognitive-services-custom-speech-create-acoustic-model.md).
* [Tworzenie niestandardowych końcowego mowy na tekst](cognitive-services-custom-speech-create-endpoint.md), którego można użyć z aplikacji.
