---
title: Co to jest wszechstronne API czytnika?
titleSuffix: Azure Cognitive Services
description: Więcej informacji na temat Immersyjnych czytnika interfejsu API.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 4500b6213c549ab9977fe8f2d849ffa8089d04b9
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718433"
---
# <a name="what-is-immersive-reader"></a>Co to jest czytnik immersyjny?

[Immersyjnych czytnika](https://www.onenote.com/learningtools) to włącznie zaprojektowane narzędzie, które implementuje sprawdzone techniki w celu poprawy zrozumienia odczytu pojawiających się czytniki, language learners i osoby nauki różnic, takie jak dysleksja.

Czytnik immersyjny można zastosować w aplikacji internetowej, korzystając z zestawu SDK czytnika immersyjnego.

## <a name="what-does-immersive-reader-do"></a>Do czego służy Immersyjnych czytnika?

Wszechstronne czytnik jest przeznaczony do ułatwiają dostęp do odczytu dla wszystkich użytkowników.

* Przedstawia zawartość w widoku do czytania minimalny

  ![Czytnik immersyjny](./media/immersive-reader.png)

* Wyświetlanie zdjęć z powszechnie używanych wyrazów

  ![Słownik obrazu](./media/picture-dictionary.png)

* Najważniejsze funkcje rzeczowniki, zleceń, określeniem i parametrów

  ![Części mowy](./media/parts-of-speech.png)

* Odczytuje zawartość głos do Ciebie

  ![Odczytywany na głos](./media/read-aloud.png)

* Przekształca zawartość w innym języku

  ![{1&gt;Translacja&lt;1}](./media/translation.png)

* Podziały wyrazów w sylab w dół

  ![Syllabification](./media/syllabification.png)

## <a name="how-does-immersive-reader-work"></a>Jak działa Immersyjnych czytnika?

Wszechstronne czytnik jest autonomiczną aplikacją sieci web, gdy wywoływany przy użyciu zestawu SDK języka JavaScript usługi czytnika wszechstronne, jest wyświetlany u góry istniejącą aplikację sieci web za pośrednictwem `iframe`. Po wywołaniu interfejsu API można uruchomić Immersyjnych czytnika, należy określić zawartość, którą chcesz pokazać w czytniku Immersyjnych. Nasz zestaw SDK obsługuje tworzenie i stylu `iframe` i komunikacji z usługą zaplecza Immersyjnych czytnika, która przetwarza zawartość dla części mowy, zamiana tekstu na mowę, tłumaczenia i tak dalej.

## <a name="next-steps"></a>Następne kroki

Rozpoczynanie pracy z czytnikiem immersyjnym:

* Realizowanie [Szybki Start](./quickstart.md)
* Zapoznaj się z [Immersyjnych czytnika zestawu SDK w witrynie GitHub](https://github.com/Microsoft/immersive-reader-sdk)
* Odczyt [Immersyjnych czytnika odwołanie do zestawu SDK](./reference.md)