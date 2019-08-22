---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 4079b0cab0126a9750bbb91ca7046aa18dbe9ece
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657838"
---
Aparat rozpoznawania formularzy działa na dokumentach wejściowych, które spełniają następujące wymagania:

* Format musi być w formacie JPG, PNG lub PDF (tekst lub zeskanowany). Pliki PDF z tekstem osadzonym są najlepszym rozwiązaniem, ponieważ nie ma możliwości błędu podczas wyodrębniania znaków i lokalizacji.
* Jeśli pliki PDF są zablokowane hasłem, należy usunąć blokadę przed przesłaniem.
* Rozmiar pliku musi być mniejszy niż 4 MB.
* W przypadku obrazów wymiary muszą zawierać się w przedziale od 600 x 100 pikseli i 4200 x 4200 pikseli.
* W przypadku skanowania z dokumentów papierowych formularze powinny być skanów o wysokiej jakości.
* Tekst musi używać alfabetu łacińskiego (znaki angielskie).
* Dane muszą zawierać klucze i wartości.
* Klucze mogą pojawić się powyżej lub z lewej strony wartości, ale nie poniżej lub po prawej stronie.

Aparat rozpoznawania formularzy obecnie nie obsługuje następujących typów danych wejściowych:

* Tabele złożone (tabele zagnieżdżone, scalone nagłówki lub komórki itd.).
* Pola wyboru lub przyciski radiowe.
* Dokumenty PDF przekraczają 50 stron.
