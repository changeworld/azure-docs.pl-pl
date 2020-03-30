---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 658fd9178495f14274c85eab2129c9dcd3be7693
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183262"
---
| **Identyfikator limitu** | **Limit** | **Komentarze** |
| --- | --- | --- |
| Całkowita pojemność (łącznie z chmurą) |Do 64 TB na urządzenie wirtualne |Można w trybie fail over pełne StorSimple Virtual Array do innej pustej tablicy. Jeśli spróbujesz przywrócić do tego samego urządzenia, upewnij się, że masz wystarczająco dużo miejsca na urządzeniu, aby zakończyć tę operację. Po przekroczeniu 32 TB nie można przywrócić do tego samego urządzenia. |
| Maksymalna liczba poświadczeń konta magazynu na urządzenie |1 | |
| Maksymalna liczba wolumenów/udziałów |16 | |
| Minimalny rozmiar udziału warstwowego |500 GB | |
| Minimalny rozmiar woluminu warstwowego |500 GB | |
| Maksymalny rozmiar udziału warstwowego |20 TB | |
| Maksymalny rozmiar woluminu warstwowego |5 TB | |
| Minimalny rozmiar lokalnie przypiętego udziału |50 GB | |
| Minimalny rozmiar woluminu przypiętego lokalnie |50 GB | |
| Maksymalny rozmiar lokalnie przypiętego udziału |2 TB | |
| Maksymalny rozmiar woluminu przypiętego lokalnie |200 GB | |
| Maksymalna liczba połączeń iSCSI od inicjatorów |512 | |
| Maksymalna liczba rekordów kontroli dostępu na urządzenie |64 | |
| Maksymalna liczba kopii zapasowych zachowanych przez urządzenie wirtualne w folderze *.backups* dla serwera plików |5 |Obejmuje to najnowsze zaplanowane (generowane przez domyślne zasady tworzenia kopii zapasowych) i ręczne tworzenie kopii zapasowych. |
| Maksymalna liczba zaplanowanych kopii zapasowych zachowanych przez urządzenie |55 |30 codziennych kopii zapasowych<br>12 miesięcznych kopii zapasowych<br>13 13 dziesięcioletnich kopii zapasowych |
| Maksymalna liczba ręcznych kopii zapasowych zatrzymywane przez urządzenie |45 | |
| Maksymalna liczba plików na jedną akcję dla serwera plików |1 mln |Podczas przywracania urządzenia czas przywracania jest proporcjonalny do liczby plików we wszystkich udziałach na urządzeniu. |
| Maksymalna liczba plików na wolumin dla serwera iSCSI |1 mln | |
| Maksymalna liczba plików na tablicę wirtualną |4 miliony | |
| Przywracanie czasu odzyskiwania |Szybkie przywracanie |Przywracanie jest oparte na mapie cieplnej i zależy od wielkości głośności.<br>Operacje tworzenia kopii zapasowej mogą wystąpić, gdy trwa operacja przywracania. |

