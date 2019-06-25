---
title: Czytnik Immersyjnych — informacje
titlesuffix: Azure Cognitive Services
description: Dowiedz się więcej o Immersyjnych czytnika
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 38056f701b76f3fc2cf1f617a6977321445f78ff
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296806"
---
# <a name="what-is-immersive-reader"></a>Co to jest wszechstronne czytnika?

[Immersyjnych czytnika](https://www.onenote.com/learningtools) to włącznie zaprojektowane narzędzie, które implementuje sprawdzone techniki w celu poprawy zrozumienia odczytu pojawiających się czytniki, language learners i osoby nauki różnic, takie jak dysleksja.

Umożliwia wszechstronne czytnika w aplikacji sieci web przy użyciu Immersyjnych zestawu SDK czytnika.

## <a name="what-does-immersive-reader-do"></a>Do czego służy Immersyjnych czytnika?

Wszechstronne czytnik jest przeznaczony do ułatwiają dostęp do odczytu dla wszystkich użytkowników.

* Przedstawia zawartość w widoku do czytania minimalny

  ![Wszechstronne czytnika](./media/immersive-reader.png)

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

## <a name="next-steps"></a>Kolejne kroki

Rozpoczynanie pracy z czytnika Immersyjnych:

* Realizowanie [Szybki Start](./quickstart.md)
* Zapoznaj się z [Immersyjnych czytnika zestawu SDK w witrynie GitHub](https://github.com/Microsoft/immersive-reader-sdk)
* Odczyt [Immersyjnych czytnika odwołanie do zestawu SDK](./reference.md)