---
title: Zagrożeń — narzędzie do modelowania zagrożeń firmy Microsoft — Azure | Dokumentacja firmy Microsoft
description: Strona kategorii przed zagrożeniami dla narzędzia do modelowania zagrożeń firmy Microsoft, zawierający wszystkie dostępne kategorie generowane zagrożenia.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: fd7c5fd929163dc7fcd22fbb045dee0fe3070394
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60611558"
---
# <a name="microsoft-threat-modeling-tool-threats"></a>Narzędzie do modelowania zagrożeń firmy Microsoft zagrożenia

Narzędzie do modelowania zagrożeń jest kluczowym elementem z cykl projektowania zabezpieczeń (SDL) firmy Microsoft. Umożliwia ona architektów oprogramowania identyfikować i minimalizować potencjalne problemy wcześnie, gdy są one stosunkowo proste i ekonomiczne, aby rozwiązać. W rezultacie znacznie zmniejsza całkowity koszt rozwoju. Ponadto firma Microsoft narzędzie zaprojektowana pod kątem ekspertów niezwiązanych z zabezpieczeniami na uwadze, ułatwiając modelowanie zagrożeń dla wszystkich deweloperów, zapewniając przejrzyste wskazówki na temat tworzenia i analizowanie modele zagrożeń.

> Odwiedź stronę **[narzędzie do modelowania zagrożeń](./azure-security-threat-modeling-tool.md)** aby zacząć już dziś!

Narzędzie do modelowania zagrożeń pomaga w uzyskaniu odpowiedzi na niektóre pytania, takie jak podane poniżej:

* Jak osoba atakująca zmieniają dane uwierzytelniania
* Jaki jest wpływ, jeśli osoba atakująca może odczytać danych profilu użytkownika?
* Co się stanie w przypadku odmowy dostępu do bazy danych profilów użytkowników?

## <a name="stride-model"></a>STRIDE modelu

Aby lepszą pomoc sformułować tego rodzaju pytania wskazywany, firma Microsoft korzysta z modelu krok, który kategoryzuje różnego rodzaju zagrożenia i upraszcza ogólną konwersacje zabezpieczeń.

| Category | Opis |
| -------- | ----------- |
| **Fałszowanie zawartości** | Polega na nielegalny dostęp, a następnie używając informacje o uwierzytelnianiu przez innego użytkownika, takie jak nazwa użytkownika i hasło |
| **Manipulowanie** | Obejmuje złośliwego modyfikowania danych. Przykłady obejmują nieautoryzowane zmiany trwałych danych, takich jak, które przechowywane w bazie danych i zmiany danych podczas ich przepływu pomiędzy dwoma komputerami za pośrednictwem sieci open, takich jak Internet |
| **Repudiation** | Skojarzone z użytkownikami, którzy Odmów, wykonując akcję bez innych podmiotów, posiadające jakikolwiek sposób, aby potwierdzić, w przeciwnym razie — na przykład użytkownik wykona niedozwolonej operacji w systemie, który nie umożliwia śledzenie operacji zabronione. Niemożność wyparcia się odnosi się do to zdolność systemu zagrożenia, możliwość wyparcia licznika. Na przykład użytkownik, który nabywa element może być się dla elementu po otrzymaniu. Dostawcy służy następnie podpisem otrzymania jako dowód, że użytkownik odebrał pakiet |
| **Ujawnienie informacji** | Obejmuje ujawnienia informacji do osób, którzy nie powinni mieć do nich dostępu — na przykład możliwość odczytu pliku, który nie przyznano im dostęp do użytkowników lub intruza możliwość odczytywania danych przesyłanych między dwoma komputerami |
| **Odmowa usługi** | Atakom typu odmowa usługi (DoS) odmowa usługi dla prawidłowych użytkowników — na przykład, dokonując serwera sieci Web tymczasowo niedostępna lub uniemożliwiającym jego używanie. Należy włączyć ochronę przed określonymi typami zagrożeń DoS, po prostu w celu zwiększenia dostępności systemu i niezawodności |
| **Podniesienie uprawnień** | Nieuprawnionego użytkownika uzyskuje dostęp uprzywilejowany i tym samym ma wystarczające uprawnienia dostępu do naruszenia bezpieczeństwa lub zniszczenia całego systemu. Podniesienie uprawnień zagrożenia obejmują tych sytuacji, w których osoba atakująca ma się skutecznie przejścia wszystkie zabezpieczenia systemu i stają się częścią systemu zaufany, w rzeczywistości niebezpiecznych sytuacji |

## <a name="next-steps"></a>Kolejne kroki

Przejdź do **[środki zaradcze narzędzie do modelowania zagrożeń](./azure-security-threat-modeling-tool-mitigations.md)** się różne sposoby, pozwala ograniczyć te zagrożenia za pomocą platformy Azure.