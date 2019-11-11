---
title: Co to jest interfejs API czytnika immersyjny?
titleSuffix: Azure Cognitive Services
description: Interfejs API czytnika immersyjny to narzędzie, którego można użyć w celu podzielenia się z różnicami w nauce lub ułatwieniami nowych czytników i języków.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 3e6889cd5d26b88cedd6b110510cb89777252f54
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904360"
---
# <a name="what-is-immersive-reader"></a>Co to jest czytnik immersyjny?

[Czytnik immersyjny](https://www.onenote.com/learningtools) jest specjalnie zaprojektowanym narzędziem, które implementuje sprawdzone techniki w celu zwiększenia czytelności dla nowych czytelników, nauk językowych i osób, które mają różnice szkoleniowe, takie jak Dyslexia.

Czytnik immersyjny można zastosować w aplikacji internetowej, korzystając z zestawu SDK czytnika immersyjnego.

## <a name="what-does-immersive-reader-do"></a>Co robi czytnik immersyjny?

Czytnik immersyjny został zaprojektowany w celu ułatwienia dostępu do odczytu dla wszystkich użytkowników.

* Pokazuje zawartość w minimalnym widoku do czytania

  ![Czytnik immersyjny](./media/immersive-reader.png)

* Wyświetla obrazy często używanych słów

  ![Słownik obrazów](./media/picture-dictionary.png)

* Wyróżnia rzeczowniki, czasowniki, przymiotniki i parametrów

  ![Części mowy](./media/parts-of-speech.png)

* Odczytuje zawartość na głos

  ![Czytaj na głos](./media/read-aloud.png)

* Tłumaczy zawartość na inny język

  ![Tłumaczenie](./media/translation.png)

* Dzieli wyrazy na sylaby

  ![Syllabification](./media/syllabification.png)

## <a name="how-does-immersive-reader-work"></a>Jak działa czytnik immersyjny?

Czytnik immersyjny to autonomiczna aplikacja internetowa, która po wywołaniu przy użyciu zestawu SDK języka JavaScript czytnika immersyjny jest wyświetlana na podstawie istniejącej aplikacji sieci Web za pośrednictwem `iframe`. Po wywołaniu interfejsu API w celu uruchomienia czytnika immersyjny należy określić zawartość, która ma być wyświetlana w czytniku immersyjny. Nasz zestaw SDK obsługuje tworzenie i określanie stylów `iframe` i komunikacji z usługą wewnętrznej bazy danych czytnika immersyjny, która przetwarza zawartość dla części mowy, zamiany tekstu na mowę, tłumaczenie i tak dalej.

## <a name="next-steps"></a>Następne kroki

Rozpoczynanie pracy z czytnikiem immersyjnym:

* Przejdź do [przewodnika Szybki Start](./quickstart.md)
* Eksplorowanie [zestawu SDK czytnika immersyjny w witrynie GitHub](https://github.com/microsoft/immersive-reader-sdk)
* Przeczytaj [Kompendium zestawu SDK czytnika immersyjny](./reference.md)