---
title: Uczenie modelu dla niestandardowego głosu — usługi mowy
titlesuffix: Azure Cognitive Services
description: Szkolenie mowy na tekst jest niezbędne w celu zwiększenia dokładności rozpoznawania modelu odniesienia firmy Microsoft lub modelu niestandardowego, który zamierzasz utworzyć. Model jest uczony przy użyciu transkrypcji oznaczone przez człowieka i dowolny tekst związany z. Te zestawy danych, wraz z wcześniej przekazane dane audio, są używane do analizy i uczenia modelu mowy na tekst do rozpoznawania słów, fraz, akronimów, nazwy i inne postanowienia specyficzne dla produktu.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 49195efa54c8e6eca9186a9e2fc33da84ff34413
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2019
ms.locfileid: "67625383"
---
# <a name="train-a-model-for-custom-speech"></a>Uczenie modelu niestandardowego rozpoznawania mowy

Szkolenie mowy na tekst jest niezbędne w celu zwiększenia dokładności rozpoznawania modelu odniesienia firmy Microsoft lub modelu niestandardowego, który zamierzasz utworzyć. Model jest uczony przy użyciu transkrypcji oznaczone przez człowieka i dowolny tekst związany z. Te zestawy danych, wraz z wcześniej przekazane dane audio, są używane do analizy i uczenia modelu mowy na tekst do rozpoznawania słów, fraz, akronimów, nazwy i inne postanowienia specyficzne dla produktu. Więcej w domeny zestawach danych, które zapewniają (dane dotyczące co użytkownicy mówią i spodziewasz się rozpoznawać), bardziej precyzyjne będzie modelu, co skutkuje rozpoznawanie. Należy pamiętać, że, wprowadzając niepowiązane dane do szkolenia, można zmniejszyć lub obniżyć dokładności modelu.

## <a name="use-training-to-resolve-accuracy-issues"></a>Użyj szkolenia, aby rozwiązać problemy dokładności

Jeśli występują problemy rozpoznawania zawierający model, za pomocą oznaczone przez człowieka zapisów i powiązanych danych, aby uzyskać dodatkowe szkolenie może pomóc zwiększyć dokładność. Umożliwia określenie, w którym zestawie danych do użycia, aby rozwiązać problemy z tej tabeli:

| Przypadek użycia | Typ danych |
|----------|-----------|
| Zwiększ dokładność rozpoznawania słownictwa specyficzne dla branży i gramatyki, takie jak terminologia medycznych lub żargon, IT | Dowolny tekst związany z (zdania/wypowiedzi) |
| Zdefiniuj fonetycznego i wyświetlane formularz programu word lub termin, który ma Wymowa niestandardowe, takie jak nazwy produktów lub akronimów. | Dowolny tekst związany z (Wymowa) |
| Zwiększ dokładność rozpoznawania na wypowiedzi style, akcentów lub określonych szum | Transkrypcje audio i oznaczone przez człowieka |
> [!IMPORTANT]
> Jeśli nie został jeszcze przekazany zestaw danych, zobacz [przygotowanie i przetestować danych](how-to-custom-speech-test-data.md). Ten dokument zawiera instrukcje dotyczące przekazywania danych i wytyczne dotyczące tworzenia wysokiej jakości zestawy danych.

## <a name="train-and-evaluate-a-model"></a>Nauczanie i ocena modelu

Pierwszym krokiem do nauczenia modelu jest Przekaż dane szkoleniowe. Użyj [przygotowanie i przetestować danych](how-to-custom-speech-test-data.md) instrukcje krok po kroku przygotować transkrypcje oznaczone przez człowieka i dowolny tekst związany z (wypowiedzi i wymowy). Po przekazaniu danych szkoleniowych, wykonaj te instrukcje, aby rozpocząć szkolenie modelu:

1. Przejdź do **mowy na tekst > Custom Speech > szkolenia**.
2. Kliknij przycisk **Train model**.
3. Następnie przyznać szkolenia **nazwa** i **opis**.
4. Z **scenariusza i plan bazowy model** menu rozwijanego wybierz scenariusz, która najlepiej pasuje do Twojej domeny. Jeśli masz pewności, których scenariusza, aby wybrać, wybierz opcję **ogólne**. Model odniesienia jest punktem wyjścia do trenowania. Jeśli nie masz preferencji, można użyć najnowszej.
5. Z **wybierz dane szkoleniowe** wybierz jednego lub wielu transkrypcji audio i oznaczone przez człowieka zestawów danych, które chcesz użyć do trenowania.
6. Po zakończeniu szkolenia można wykonywać dokładność testowanie na nowo trenowanego modelu. Ten krok jest opcjonalny.
7. Wybierz **Utwórz** do tworzenia modelu niestandardowego.

Tabela szkolenia wyświetla nowy wpis, który odpowiada na tę nowo utworzoną modelu. Tabela pokazuje również stanu:  Przetwarzania, zakończyło się powodzeniem, zakończone niepowodzeniem.

## <a name="evaluate-the-accuracy-of-a-trained-model"></a>Oceń dokładność uczonego modelu

Można sprawdzać dane i oceny dokładności modelu przy użyciu tych dokumentów:

* [Sprawdzanie danych](how-to-custom-speech-inspect-data.md)
* [Oceń swoje dane](how-to-custom-speech-evaluate-data.md)


Jeśli wybrano przetestować dokładność, koniecznie wybierz akustyczny zestawu danych, który różni się od tego, który jest używany przy użyciu modelu realistyczne wolumenu wydajności modelu.

## <a name="next-steps"></a>Kolejne kroki

* [Wdrażanie modelu](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Przygotuj i przetestować danych](how-to-custom-speech-test-data.md)
* [Sprawdzanie danych](how-to-custom-speech-inspect-data.md)
* [Oceń swoje dane](how-to-custom-speech-evaluate-data.md)
* [Uczenie modelu](how-to-custom-speech-train-model.md)
