---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 17dc32f8948387b90229d3c4c07102cff98e3018
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562674"
---
Aparat rozpoznawania formularzy działa na dokumentach wejściowych, które spełniają następujące wymagania:

* Format musi być w formacie JPG, PNG lub PDF (tekst lub zeskanowany). Pliki PDF z tekstem osadzonym są najlepszym rozwiązaniem, ponieważ nie ma możliwości błędu podczas wyodrębniania znaków i lokalizacji.
* Jeśli pliki PDF są zablokowane hasłem, należy usunąć blokadę przed przesłaniem.
* Rozmiar pliku musi być mniejszy niż 4 MB.
* W przypadku obrazów wymiary muszą zawierać się w przedziale od 50 x 50 pikseli i 4200 x 4200 pikseli.
* W przypadku skanowania z dokumentów papierowych formularze powinny być skanów o wysokiej jakości.
* Tekst musi używać alfabetu łacińskiego (znaki angielskie).
* Dane muszą zawierać klucze i wartości.
* Klucze mogą pojawić się powyżej lub z lewej strony wartości, ale nie poniżej lub po prawej stronie.

Aparat rozpoznawania formularzy obecnie nie obsługuje następujących typów danych wejściowych:

* Tabele złożone (tabele zagnieżdżone, scalone nagłówki lub komórki itd.).
* Pola wyboru lub przyciski radiowe.
* Dokumenty PDF przekraczają 50 stron.