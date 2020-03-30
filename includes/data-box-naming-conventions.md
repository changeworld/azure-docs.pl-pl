---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: d965b89659a9e3dc01035221a729f094c336eb5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "66244613"
---
| Jednostka                                       | Konwencja   |
|----------------------------------------------|-----------------------------------------------------|
| Nazwy kontenerów dla bloków blob i stronicowych obiektów blob | Musi być prawidłowa nazwa DNS, która ma długości od 3 do 63 znaków. <br>  Musi zaczynać się literą lub cyfrą. <br> Może zawierać tylko małe litery, cyfry i łącznik (-). <br> Bezpośrednio przed łącznikiem (-) i bezpośrednio po nim musi znajdować się cyfra lub litera. <br> Kolejne łączniki nie są dozwolone w nazwach. |
| Udostępnianie nazw plików platformy Azure                  | Jak wyżej                                          |
| Nazwy katalogów i plików dla plików platformy Azure     |<li> Zachowanie wielkości liter, bez uwzględniania wielkości liter i nie może przekraczać 255 znaków długości. </li><li> Nie można zakończyć ukośnikiem do przodu (/). </li><li>Jeśli zostanie podana, zostanie ona automatycznie usunięta. </li><li> Następujące znaki nie są dozwolone:<code>" \\ / : \| < > * ?</code></li><li> Zastrzeżone znaki adresów URL muszą być poprzedzone odpowiednim znakiem ucieczki. </li><li> Nielegalne znaki ścieżki adresu URL nie są dozwolone. Punkty kodu, takie jak \\uE000, nie są prawidłowymi znakami Unicode. Niektóre znaki ASCII lub Unicode, takie jak znaki kontrolne (od \\0x00 do 0x1F, u0081 itp.), również nie są dozwolone. Reguły dotyczące ciągów Unicode w http/1.1 zobacz RFC 2616, Sekcja 2.2: Podstawowe reguły i RFC 3987. </li><li> Następujące nazwy plików nie są dozwolone: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK$, znak kropki (.) i dwa znaki kropki (..).</li>|
| Nazwy blokowych i stronicowych obiektów blob      | </li><li>W nazwach obiektów blob jest rozróżniana wielkość liter. Mogą zawierać dowolną kombinację znaków. </li><li>Nazwa obiektu blob musi zawierać od 1 do 1024 znaków. </li><li>Zastrzeżone znaki adresów URL muszą być poprzedzone odpowiednim znakiem ucieczki. </li><li>Liczba segmentów ścieżki w nazwie obiektu blob nie może przekraczać 254. Segment ścieżki to ciąg znajdujący się pomiędzy następującymi po sobie znakami ogranicznika (na przykład ukośnikami „/”), co odpowiada nazwie katalogu wirtualnego.</li> |
