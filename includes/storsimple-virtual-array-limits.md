---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 658fd9178495f14274c85eab2129c9dcd3be7693
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66143290"
---
| **Identyfikator limitu** | **Limit** | **Komentarze** |
| --- | --- | --- |
| Całkowita pojemność (w tym chmury) |Do 64 TB na urządzenie wirtualne |Możesz przełączać awaryjnie pełnego rozwiązania StorSimple Virtual Array do innej tablicy puste. Jeśli próbujesz przywrócić na tym samym urządzeniu, upewnij się, że masz wystarczająco dużo miejsca na urządzeniu, aby ukończyć tę operację. Po przekroczyła 32 TB, nie można przywrócić do tego samego urządzenia. |
| Maksymalna liczba poświadczeń konta magazynu na urządzenie |1 | |
| Maksymalna liczba woluminów/udziałów |16 | |
| Minimalny rozmiar udziału warstwowego |500 GB | |
| Minimalny rozmiar woluminu warstwowego |500 GB | |
| Maksymalny rozmiar udziału warstwowego |20 TB | |
| Maksymalny rozmiar woluminu warstwowego |5 TB | |
| Minimalny rozmiar udziału przypięty lokalnie |50 GB | |
| Minimalny rozmiar woluminu przypiętego lokalnie |50 GB | |
| Maksymalny rozmiar udziału przypięty lokalnie |2 TB | |
| Maksymalny rozmiar woluminu przypiętego lokalnie |200 GB | |
| Maksymalna liczba połączeń iSCSI z inicjatorów |512 | |
| Maksymalna liczba rekordów kontroli dostępu na urządzeniu |64 | |
| Maksymalna liczba kopii zapasowych zachowywanych przez urządzenie wirtualne na *.backups* folderu dla serwera plików |5 |Obejmuje to najnowsza zaplanowanych (generowany przez domyślne zasady kopii zapasowych), jak i ręcznego tworzenia kopii zapasowych. |
| Maksymalna liczba zaplanowanych kopii zapasowych przechowywane przez urządzenie |55 |30 codzienne wykonywanie kopii zapasowych<br>12 comiesięczne kopie zapasowe<br>13 corocznych kopii zapasowych |
| Maksymalna liczba ręcznego tworzenia kopii zapasowych, które są przechowywane przez urządzenie |45 | |
| Maksymalna liczba plików na udziale serwera plików |1 mln |Podczas przywracania urządzenia czasów przywracania są proporcjonalne do liczby plików we wszystkich akcji na urządzeniu. |
| Maksymalna liczba plików na wolumin na serwerze iSCSI |1 mln | |
| Maksymalna liczba plików na macierz wirtualną |4 mln | |
| Odzyskaj godzina przywracania |Szybkiego przywracania |Przywracanie opiera się na mapie cieplnej i zależy od rozmiaru woluminu.<br>Operacje tworzenia kopii zapasowej może wystąpić, gdy operacja przywracania jest w toku. |

