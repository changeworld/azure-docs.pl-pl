---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 11783f8fac147e6fb2cf371ee1a588cf318a5e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75379444"
---
Aparat rozpoznawania formularzy działa na dokumentach wejściowych, które spełniają następujące wymagania:

* Format musi być JPG, PNG, PDF (tekst lub skanowane) lub TIFF. Pliki PDF osadzone w tekście są najlepsze, ponieważ nie ma możliwości wystąpienia błędu w wyodrębnianiu znaków i lokalizacji.
* Jeśli pliki PDF są zablokowane hasłem, należy usunąć blokadę przed ich przesłaniem.
* Dokumenty PDF i TIFF muszą mieć 200 stron lub mniej, a całkowity rozmiar zestawu danych szkoleniowych musi wynosić 500 stron lub mniej.
* W przypadku obrazów wymiary muszą wynosić od 600 x 100 pikseli do 4200 x 4200 pikseli.
* W przypadku skanowania z dokumentów papierowych formularze powinny być skanami wysokiej jakości.
* Tekst musi używać alfabetu łacińskiego (znaki angielskie).
* W przypadku uczenia się bez nadzoru (bez oznaczonych danych) dane muszą zawierać klucze i wartości.
* W przypadku uczenia się bez nadzoru (bez oznaczonych danych) klucze muszą znajdować się powyżej lub po lewej stronie wartości; nie mogą pojawić się poniżej lub po prawej stronie.

Aparat rozpoznawania formularzy nie obsługuje obecnie tych typów danych wejściowych:

* Złożone tabele (tabele zagnieżdżone, scalone nagłówki lub komórki itd.).
* Pola wyboru lub przyciski radiowe.
