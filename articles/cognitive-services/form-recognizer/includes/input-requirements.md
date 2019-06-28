---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 690219347782aad2140b0a1b0541590c5426c568
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67459728"
---
Aparat rozpoznawania formularza pracuje dokumentów wejściowych, które spełniają te wymagania:

* Musi być w formacie JPG, PNG lub PDF (tekst lub skanowane). Osadzony tekst plików PDF są najlepsze, ponieważ istnieje możliwość wystąpienia błędu w tej lokalizacji i wyodrębniania znaków.
* Rozmiar pliku musi być mniejszy niż 4 megabajty (MB).
* W przypadku obrazów wymiarów musi mieć od 50 x 50 pikseli i 4200 x 4200 pikseli.
* Jeśli zeskanowane dokumenty papieru, formularze powinna być wysokiej jakości skanowania.
* Tekst musi używać alfabetu łacińskiego (angielskie znaki).
* Dane muszą być drukowane (nie odręcznego).
* Dane mogą zawierać klucze i wartości.
* Klucze mogą być wyświetlane powyżej lub po lewej stronie wartości, ale nie niższej lub w prawo.

Aparat rozpoznawania formularza nie obsługuje obecnie następujące typy danych wejściowych:

* Złożone tabele (zagnieżdżonych tabel, scalone nagłówki lub komórek i tak dalej).
* Przycisk pola wyboru lub opcji.
* Dokumenty PDF jest dłuższa niż 50 stron.