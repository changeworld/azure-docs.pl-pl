---
title: Środki zaradcze — narzędzie Microsoft Threat modelowania - Azure | Dokumentacja firmy Microsoft
description: Środki zaradcze strony Microsoft Threat modelowania narzędzia wyróżnianie możliwych rozwiązań najbardziej narażonych generowane zagrożeń.
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
ms.openlocfilehash: 07ef1fd3d81d795c9164741d22b5a689f86bd720
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23867983"
---
# <a name="microsoft-threat-modeling-tool-mitigations"></a>Środki zaradcze narzędzie modelowania zagrożeń firmy Microsoft

Narzędzie modelowania zagrożeń jest kluczowym elementem Microsoft Security Development Lifecycle (SDL). Umożliwia on architektom identyfikowanie i usuwanie potencjalnych problemów z zabezpieczeniami wcześnie, gdy są one względnie proste i ekonomiczne, aby rozwiązać oprogramowanie. W związku z tym znacznie zmniejsza całkowity koszt programowanie. Ponadto firma Microsoft zaprojektowane narzędzie z ekspertami z systemem innym niż zabezpieczeń pamiętać, ułatwiając modelowanie zagrożeń dla wszystkich deweloperów zapewniając wyraźnych wskazówek dotyczących tworzenia i analizowanie modeli zagrożeń.

Odwiedź stronę  **[narzędzie modelowania zagrożeń](./azure-security-threat-modeling-tool.md)**  aby zacząć już dzisiaj!

## <a name="mitigation-categories"></a>Środki zaradcze kategorii

Środki zaradcze narzędzie modelowania zagrożeń są podzielone według ramki zabezpieczeń aplikacji sieci Web, która składa się z następujących czynności:

| Kategoria | Opis |
| -------- | ----------- |
| **[Inspekcja i rejestrowanie](./azure-security-threat-modeling-tool-auditing-and-logging.md)** | Czy kto, co i kiedy? Inspekcja i rejestrowanie się jak aplikacja rekordy zdarzeń związanych z zabezpieczeniami |
| **[Uwierzytelnianie](./azure-security-threat-modeling-tool-authentication.md)** | Kim jesteś? Uwierzytelnianie jest procesem, w którym jednostki potwierdza tożsamość inną jednostkę, zwykle za pomocą poświadczeń, takie jak nazwa użytkownika i hasło |
| **[Autoryzacji](./azure-security-threat-modeling-tool-authorization.md)** | Co należy zrobić? Autoryzacja jest jak aplikacja udostępnia kontroli dostępu do zasobów i operacje |
| **[Zabezpieczenia komunikacji](./azure-security-threat-modeling-tool-communication-security.md)** | Kto należy mówimy do Zabezpieczenia komunikacji gwarantuje, że cała komunikacja wykonywane jest należycie zabezpieczone |
| **[Zarządzanie konfiguracją](./azure-security-threat-modeling-tool-configuration-management.md)** | Kto czy aplikacja działa jako? Które bazy danych łączy go z? Jak jest zarządzany aplikacji? Jak są zabezpieczone tych ustawień? Zarządzanie konfiguracją odwołuje się do jak aplikacja obsługuje te problemy z działaniem |
| **[Kryptografia](./azure-security-threat-modeling-tool-cryptography.md)** | Jak są przechowywanie kluczy tajnych (poufność)? Jak czy sprawdzające manipulacji danymi lub biblioteki (integralność)? Jak należy podaje ziarna dla losowych wartości, które muszą być silną kryptograficznie? Kryptografia odwołuje się do jak aplikacja wymusza poufność i integralność |
| **[Zarządzanie wyjątkami](./azure-security-threat-modeling-tool-exception-management.md)** | Po wywołaniu metody w aplikacji nie powiedzie się, czego aplikacji? Ile ujawnieniem? Czy można zwrócić informacje o błędzie przyjazną dla użytkowników końcowych Czy przekazać wyjątek cenne informacje do wywołującego? Aplikacja powiedzie bezpiecznie zamknąć? |
| **[Sprawdzania poprawności danych wejściowych](./azure-security-threat-modeling-tool-input-validation.md)** | Jak sprawdzić, czy dane wejściowe, którą otrzymuje aplikacji jest prawidłowy i bezpieczny? Sprawdzania poprawności danych wejściowych odnosi się do sposobu aplikacji filtry, scrubs lub odrzucenia danych wejściowych przed dodatkowego przetwarzania. Należy wziąć pod uwagę ograniczający dane wejściowe za pośrednictwem punktów wejścia i kodowanie danych wyjściowych przez punkty wyjścia. Czy można ufać danych ze źródeł, takich jak bazy danych i udziałów plików |
| **[Dane poufne](./azure-security-threat-modeling-tool-sensitive-data.md)** | Jak aplikacja obsługuje poufnych danych? Poufnych danych odwołuje się do jak aplikacja obsługuje dowolne dane, które muszą być chronione w pamięci, za pośrednictwem sieci, lub w magazynach trwałych |
| **[Zarządzanie sesjami](./azure-security-threat-modeling-tool-session-management.md)** | Jak aplikacji obsługi i chronić sesje użytkowników? Sesja odwołuje się do powiązanych interakcji między użytkownikiem i aplikacji sieci Web |

Dzięki temu można zidentyfikować:

* Gdzie składają się z najbardziej typowych błędów
* Gdzie znajdują się najbardziej przydatnych wyników ulepszenia

W związku z tym używać tych kategorii w celu fokus oraz ustalić ich priorytety służbowy zabezpieczeń, tak, że jeśli wiadomo, że występują najczęstszych problemów z zabezpieczeniami w wejściowych kategorii sprawdzania poprawności, uwierzytelniania i autoryzacji, możesz uruchomić istnieje. Aby uzyskać więcej informacji można znaleźć  **[to łącze patentowe](https://www.google.com/patents/US7818788)**

## <a name="next-steps"></a>Następne kroki

Odwiedź stronę  **[zagrożenia narzędzia do modelowania zagrożeń](./azure-security-threat-modeling-tool-threats.md)**  Aby dowiedzieć się więcej o kategoriach zagrożeń używa narzędzia do generowania projektowania możliwe zagrożenia.