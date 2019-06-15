---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: d965b89659a9e3dc01035221a729f094c336eb5b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66244613"
---
| Jednostka                                       | Konwencja   |
|----------------------------------------------|-----------------------------------------------------|
| Nazwy kontenerów dla blokowych obiektów blob i stronicowych obiektów blob | Musi być prawidłową nazwą DNS, który składa się z 3 do 63 znaków. <br>  Musi zaczynać się literą lub cyfrą. <br> Może zawierać tylko małe litery, cyfry i znaki łącznika (-). <br> Bezpośrednio przed łącznikiem (-) i bezpośrednio po nim musi znajdować się cyfra lub litera. <br> Następujące po sobie łączniki nie są dozwolone w nazwach. |
| Nazwy udziałów plików platformy Azure                  | Jak wyżej                                          |
| Nazwy katalogów i plików dla usługi Azure files     |<li> Zachowywanie, bez uwzględniania wielkości liter i nie może przekraczać 255 znaków długości. </li><li> Nie może kończyć się ukośnikiem (/). </li><li>Jeśli nie dostarczono, zostaną automatycznie usunięte. </li><li> Następujące znaki nie są dozwolone: <code>" \\ / : \| < > * ?</code></li><li> Zastrzeżone znaki adresów URL muszą być poprzedzone odpowiednim znakiem ucieczki. </li><li> Niedozwolone znaki ścieżki adresu URL nie jest dozwolone. Kod punkty, takich jak \\uE000 nie są prawidłowymi znakami Unicode. Niektóre znaki ASCII lub Unicode, takie jak znaki kontrolne (0x00 do 0x1F \\u0081, itp.), również nie są dozwolone. Zasady dotyczące Unicode ciągów w HTTP/1.1 znajduje się dokumencie RFC 2616 2.2 sekcji: Podstawowe zasady i RFC 3987. </li><li> Następujące nazwy plików nie są dozwolone: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK$, znak kropki (.) i kropka dwa znaki (.).</li>|
| Nazwy blokowych i stronicowych obiektów blob      | </li><li>W nazwach obiektów blob jest rozróżniana wielkość liter. Mogą zawierać dowolną kombinację znaków. </li><li>Nazwa obiektu blob musi zawierać od 1 do 1024 znaków. </li><li>Zastrzeżone znaki adresów URL muszą być poprzedzone odpowiednim znakiem ucieczki. </li><li>Liczba segmentów ścieżki w nazwie obiektu blob nie może przekraczać 254. Segment ścieżki to ciąg znajdujący się pomiędzy następującymi po sobie znakami ogranicznika (na przykład ukośnikami „/”), co odpowiada nazwie katalogu wirtualnego.</li> |
