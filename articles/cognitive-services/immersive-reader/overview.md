---
title: Co to jest interfejs API czytnika immersive?
titleSuffix: Azure Cognitive Services
description: Interfejs API programu Immersive Reader to narzędzie, którego można używać do przyjmowania osób z różnicami w uczeniu się lub pomocy nowym czytelnikom i osobom uczącym się języka.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 01/4/2020
ms.author: metan
ms.openlocfilehash: 2eab7ab62f6a7a105dab33aa889af9f2be3a3fa3
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80052383"
---
# <a name="what-is-immersive-reader"></a>Co to jest czytnik immersyjny?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

[Immersive Reader](https://www.onenote.com/learningtools) to zaprojektowane z włącznie narzędzie, które wdraża sprawdzone techniki poprawy zrozumienia czytania dla nowych czytelników, osób uczących się języków i osób z różnicami w uczeniu się, takimi jak dysleksja.

Czytnik immersyjny można zastosować w aplikacji internetowej, korzystając z zestawu SDK czytnika immersyjnego.

## <a name="what-does-immersive-reader-do"></a>Do czego działa Immersive Reader?

Czytnik Immersive Reader został zaprojektowany tak, aby czytanie było bardziej dostępne dla wszystkich.

* Pokazuje zawartość w minimalnym widoku odczytu

  ![Czytnik immersyjny](./media/immersive-reader.png)

* Wyświetla obrazy często używanych słów

  ![Słownik obrazów](./media/picture-dictionary.png)

* Najważniejsze rzeczowniki, czasowniki, przymiotniki i przysłówki

  ![Części mowy](./media/parts-of-speech.png)

* Odczytuje twoje treści na głos

  ![Czytaj na głos](./media/read-aloud.png)

* Tłumaczy zawartość na inny język

  ![Tłumaczenie](./media/translation.png)

* Dzieli słowa na sylaby

  ![Syllabification](./media/syllabification.png)

## <a name="how-does-immersive-reader-work"></a>Jak działa immersive reader?

Immersive Reader to samodzielna aplikacja internetowa, która po wywołaniu przy użyciu modułu SDK Immersive Reader JavaScript jest wyświetlana na istniejącej aplikacji internetowej za pośrednictwem pliku `iframe`. Po wywołaniu interfejsu API, aby uruchomić immersive reader, należy określić zawartość, którą chcesz pokazać w czytniku Immersive. Nasz zestaw SDK obsługuje tworzenie i `iframe` stylizację i komunikację z usługą zaplecza Immersive Reader, która przetwarza zawartość dla części mowy, tekstu na mowę, tłumaczenia i tak dalej.

## <a name="next-steps"></a>Następne kroki

Rozpoczynanie pracy z czytnikiem immersyjnym:

* Wskakuj do [szybkiego startu](./quickstart.md)
* Poznaj [immersyjny sdk czytnika w usłudze GitHub](https://github.com/microsoft/immersive-reader-sdk)
* Przeczytaj dokumentację [SDK programu Immersive Reader](./reference.md)
