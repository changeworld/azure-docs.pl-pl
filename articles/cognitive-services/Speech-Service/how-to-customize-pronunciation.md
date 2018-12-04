---
title: Dostosowywanie Wymowa przy użyciu usługi mowy
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak dostosować Wymowa z usługa mowy.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 07/02/2018
ms.author: panosper
ms.openlocfilehash: c7f82e9fb286e53f9408e2134dbdc9c2b24ff267
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52837702"
---
# <a name="enable-custom-pronunciation"></a>Włącz Wymowa niestandardowe
Za pomocą niestandardowych wymowa, można zdefiniować fonetycznych formularza i wyświetlanie słowa lub terminy. Jest to przydatne do obsługi warunki niestandardowe, takie jak nazwy produktów lub akronimów. Wszystko co potrzebne jest plikiem Wymowa (pliku txt prosty).

Oto, jak to działa. W pliku txt pojedynczego można wprowadzić kilka niestandardowych Wymowa wpisów. Struktura jest następująca:

```
Display form <Tab> Spoken form <Newline>
```

W poniższej tabeli przedstawiono kilka przykładów:

| Formularz wyświetlania | Mówionej formy |
|----------|-------|
| C3PO | Zobacz trzy PLA o |
| L8R | Rozpoznanie późnego są |
| CNTK | Zobacz n herbaty k|

## <a name="requirements-for-the-spoken-form"></a>Wymagania dotyczące mówionej formy
Mówionej formy musi być pisana małymi literami i co można wymusić podczas importowania. Należy również podać zaewidencjonuje odbierający dane. Nie karty w mówionej formy lub formularz wyświetlania jest dozwolone. Jednak może być bardziej zabronione znaki w formularzu wyświetlania (na przykład ~ i ^).

Każdy plik txt może mieć wiele wpisów, jak pokazano na poniższej ilustracji:

![Przykłady Wymowa akronim](media/stt/custom-speech-pronunciation-file.png)

Mówionej formy jest ciągiem fonetycznych formularz wyświetlania. Składa się z liter, słowa lub sylab. Obecnie nie ma żadnych dalszych wskazówek lub zestaw standardów, aby pomóc w tworzeniu mówionej formy. 

## <a name="supported-pronunciation-characters"></a>Wymowa obsługiwane znaki
Wymowa niestandardowego jest obecnie obsługiwane dla języka angielskiego (en US) i niemiecki (de-de). Zestaw znaków, który służy do express mówionej formy termin (w pliku niestandardowego Wymowa) są wyświetlane w poniższej tabeli: 

| Język | Znaki |
|---------- |----------|
| Angielski (en US) | , b, c, d, e, f, g, h, i, "j", k, l, o, p, pytania i odpowiedzi, r, s, t, u, v, w, x, y, z |
| Niemiecki (de-de) | ä strumień świetlny, znaki ü,?, a, b, c, d, e, f, g, h, i, "j", k, l, o, p, pytania i odpowiedzi, r, s, t, u, v, w, x, y, z |

> [!NOTE]
> Formularz wyświetlania termin (w pliku Wymowa) zapisywane taki sam sposób w zestawie danych dostosowywania języka.

## <a name="requirements-for-the-display-form"></a>Wymagania dotyczące formularz wyświetlania
Formularz wyświetlania można tylko niestandardowe słowa, czasu trwania umowy, akronim lub wyrazy złożone łączące istniejące słowa. Można również wprowadzić alternatywne wymowy popularne wyrazy. 

>[!NOTE]
>Nie zaleca się używania tej funkcji, aby sformułować popularnych wyrazów lub zmodyfikować mówionej formy. Zaleca dekodera, aby zobaczyć, czy niektóre nietypowe wyrazów (np. skróty wyrazy techniczne i obce słowa) są niepoprawnie zdekodowane. W takim przypadku należy dodać je do pliku Wymowa niestandardowych. W model języka należy zawsze i tylko używać formularz wyświetlania wyrazu. 

## <a name="requirements-for-the-file-size"></a>Wymagania dotyczące rozmiaru pliku
Rozmiar pliku txt, który zawiera wpisy Wymowa jest ograniczona do 1 MB (1KB dla kluczy w warstwie bezpłatna). Zazwyczaj nie trzeba przekazywanie dużych ilości danych przy użyciu tego pliku. Większość plików niestandardowych Wymowa prawdopodobnie kilku kilobajtów (KB/s) w rozmiarze. Kodowanie pliku txt, dla wszystkich ustawień regionalnych powinna być BOM UTF-8. Angielskie ustawienia regionalne dopuszczalne jest również ANSI.

## <a name="next-steps"></a>Kolejne kroki
* Popraw dokładności rozpoznawania, tworząc [niestandardowy model akustyczny](how-to-customize-acoustic-models.md).
* Popraw dokładności rozpoznawania, tworząc [model języka niestandardowego](how-to-customize-language-model.md).
 