---
title: Zagrożenia Azure - Microsoft Threat narzędzia modelowania — | Dokumentacja firmy Microsoft
description: Strona kategorii zagrożenie dla narzędzia modelowania zagrożeń firmy Microsoft, zawierający kategorie dla wszystkich narażonych generowane zagrożenia.
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 704f9995828866d4d2e4969e3aa922ed1e23c4ea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23867899"
---
# <a name="microsoft-threat-modeling-tool-threats"></a>Narzędzia do modelowania zagrożeń Microsoft zagrożeń

Narzędzie modelowania zagrożeń jest kluczowym elementem Microsoft Security Development Lifecycle (SDL). Umożliwia on architektom identyfikowanie i usuwanie potencjalnych problemów z zabezpieczeniami wcześnie, gdy są one względnie proste i ekonomiczne, aby rozwiązać oprogramowanie. W związku z tym znacznie zmniejsza całkowity koszt programowanie. Ponadto firma Microsoft zaprojektowane narzędzie z ekspertami z systemem innym niż zabezpieczeń pamiętać, ułatwiając modelowanie zagrożeń dla wszystkich deweloperów zapewniając wyraźnych wskazówek dotyczących tworzenia i analizowanie modeli zagrożeń.

> Odwiedź stronę  **[narzędzie modelowania zagrożeń](./azure-security-threat-modeling-tool.md)**  aby zacząć już dzisiaj!

Narzędzie modelowania zagrożeń pomaga w uzyskaniu odpowiedzi niektóre kwestie, takie jak te poniżej:

* Jak osoba atakująca może zmienić dane uwierzytelniania
* Jaki jest wpływ, jeśli osoba atakująca może odczytać dane profilu użytkownika?
* Co się stanie, jeśli odmówiono dostępu do bazy danych profilów użytkowników?

## <a name="stride-model"></a>STRIDE modelu

Lepsze pomocy sformułować tego rodzaju pytania wskazywany, firma Microsoft korzysta z modelu krok, który kategoryzuje różnego rodzaju zagrożenia i upraszcza ogólną konwersacje zabezpieczeń.

| Kategoria | Opis |
| -------- | ----------- |
| **Fałszowanie zawartości** | Obejmuje niedozwolony sposób uzyskiwania dostępu do, a następnie użyć informacji uwierzytelniania innego użytkownika, takie jak nazwa użytkownika i hasło |
| **Manipulowanie** | Obejmuje złośliwego modyfikacji danych. Nieautoryzowane zmiany wprowadzone w danych, takich jak które przechowywanych w bazie danych i zmiany danych przepływ między dwoma komputerami za pośrednictwem sieci otwarty, na przykład Internetem przykładów. |
| **Odrzucenie** | Skojarzone z użytkownikami, którzy Odmów wykonuje akcję bez osoby posiadające dowolny sposób, aby udowodnić, w przeciwnym razie — na przykład użytkownik wykona niedozwolonej operacji w systemie, która nie ma możliwość śledzenia zabronione operacji. Niemożność wyparcia się odwołuje się do możliwości systemu na zagrożenia odrzucenie licznika. Na przykład użytkownik, który dokonuje zakupu elementu może być konieczne podpisywania dla elementu po otrzymaniu. Dostawcę można używać jako dowód, że użytkownik otrzymały pakiet podpisem odbieranie |
| **Ujawnienie informacji** | Obejmuje ujawnienia informacji do osób, które nie mają mieć dostęp do niego — na przykład możliwość odczytać pliku, który nie przyznano im dostęp do użytkowników lub intruz możliwość odczytania danych przesyłanych między dwoma komputerami |
| **Odmowa usługi** | (DoS) ataki odmowy usługi dla prawidłowych użytkowników — na przykład, przez co serwer sieci Web, tymczasowo niedostępna lub niezdatna do użycia. Należy włączyć ochronę przed niektóre typy zagrożeń DoS po prostu w celu zwiększenia dostępności systemu i niezawodności |
| **Podniesienie uprawnień** | Nieuprawnionego użytkownika uzyskuje dostęp uprzywilejowany i tym samym ma wystarczające uprawnienia do naruszenia bezpieczeństwa lub zniszczenia całego systemu. Podniesienie uprawnień zagrożenia obejmują tych sytuacji, w których atakujący ma skutecznie przejścia wszystkie zabezpieczenia systemu i stać się częścią systemu zaufany, w rzeczywistości niebezpiecznych sytuacji |

## <a name="next-steps"></a>Następne kroki

Przejdź do  **[środki zaradcze narzędzia do modelowania zagrożeń](./azure-security-threat-modeling-tool-mitigations.md)**  Aby dowiedzieć się różne sposoby, można ograniczyć te zagrożenia z platformy Azure.