---
title: Środki zaradcze — narzędzie do modelowania zagrożeń firmy Microsoft — Azure | Dokumentacja firmy Microsoft
description: Środki zaradcze stronę Microsoft Threat narzędzie do modelowania wyróżnianie możliwe rozwiązania najbardziej narażonych generowane zagrożenia.
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
ms.openlocfilehash: 24aa49fd4ccccda372d2632ef4aee22bd5cb2bf6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60611314"
---
# <a name="microsoft-threat-modeling-tool-mitigations"></a>Środki zaradcze narzędzie do modelowania zagrożeń firmy Microsoft

Narzędzie do modelowania zagrożeń jest kluczowym elementem z cykl projektowania zabezpieczeń (SDL) firmy Microsoft. Umożliwia ona architektów oprogramowania identyfikować i minimalizować potencjalne problemy wcześnie, gdy są one stosunkowo proste i ekonomiczne, aby rozwiązać. W rezultacie znacznie zmniejsza całkowity koszt rozwoju. Ponadto firma Microsoft narzędzie zaprojektowana pod kątem ekspertów niezwiązanych z zabezpieczeniami na uwadze, ułatwiając modelowanie zagrożeń dla wszystkich deweloperów, zapewniając przejrzyste wskazówki na temat tworzenia i analizowanie modele zagrożeń.

Odwiedź stronę **[narzędzie do modelowania zagrożeń](./azure-security-threat-modeling-tool.md)** aby zacząć już dziś!

## <a name="mitigation-categories"></a>Środki zaradcze kategorii

Środki zaradcze narzędzie do modelowania zagrożeń są podzielone według ramki zabezpieczeń aplikacji sieci Web, która składa się z następujących czynności:

| Category | Opis |
| -------- | ----------- |
| **[Inspekcja i rejestrowanie](./azure-security-threat-modeling-tool-auditing-and-logging.md)** | Kto zrobił, co i kiedy? Inspekcja i rejestrowanie się jak rekordy zdarzeń dotyczących zabezpieczeń w aplikacji |
| **[Uwierzytelnianie](./azure-security-threat-modeling-tool-authentication.md)** | Kim jesteś? Uwierzytelnianie jest procesem, w którym jednostki potwierdza tożsamość innej jednostki, zwykle za pomocą poświadczeń, takie jak nazwa użytkownika i hasło |
| **[Autoryzacja](./azure-security-threat-modeling-tool-authorization.md)** | Co należy zrobić? Autoryzacja to, jak aplikacja udostępnia mechanizmy kontroli dostępu do zasobów i operacji |
| **[Bezpieczeństwo komunikacji](./azure-security-threat-modeling-tool-communication-security.md)** | Kto należy się porozumiewają? Bezpieczeństwo komunikacji gwarantuje, że cała komunikacja, wykonywane są tak bezpieczne, jak to możliwe |
| **[Zarządzanie konfiguracją](./azure-security-threat-modeling-tool-configuration-management.md)** | Kto czy aplikacja działa jako? Które bazy danych ona nawiązać połączenie? Jak jest podawana aplikacji Jak są zabezpieczone te ustawienia? Zarządzanie konfiguracją odwołuje się do jak aplikacja obsługuje te problemy operacyjne |
| **[Kryptografia](./azure-security-threat-modeling-tool-cryptography.md)** | Jak są przechowywanie wpisów tajnych (poufność)? Jak się masz sprawdzające odporne Twoje dane lub bibliotek (integralność)? Jak można podajesz wartości początkowe wartości losowych, które muszą być silną kryptograficznie? Kryptografia odwołuje się do jak aplikacja wymusza poufności i integralności |
| **[Zarządzanie wyjątkami](./azure-security-threat-modeling-tool-exception-management.md)** | Podczas wywołania metody w aplikacji nie powiedzie się, czego aplikacji? Ile ujawnisz? Zwraca informacje o błędzie przyjazną dla użytkowników końcowych? Czy przekazać wyjątek cenne informacje obiektu wywołującego? Aplikacja elegancko nie powieść się? |
| **[Walidacja danych wejściowych](./azure-security-threat-modeling-tool-input-validation.md)** | Skąd wiadomo, że dane wejściowe, które odbiera aplikacja jest prawidłowy i bezpieczny? Walidacja danych wejściowych odnosi się do jak aplikacja filtry, scrubs lub odrzuci dane wejściowe przed dodatkowego przetwarzania. Należy wziąć pod uwagę, ograniczając danych wejściowych za pośrednictwem punktów wejścia i kodowanie danych wyjściowych za pomocą punktów zakończenia. Czy można ufać dane ze źródeł, takich jak bazy danych i udziałów plików? |
| **[Dane poufne](./azure-security-threat-modeling-tool-sensitive-data.md)** | Jak aplikacja obsługuje poufnych danych? Dotyczy jak aplikacja obsługuje dowolne dane, które muszą być chronione w pamięci, za pośrednictwem sieci, lub w trwałych magazynach danych poufnych |
| **[Zarządzanie sesjami](./azure-security-threat-modeling-tool-session-management.md)** | Jak czy aplikacji obsługi i chronić sesje użytkowników? Sesja odwołuje się do powiązanych interakcji między użytkownikiem a aplikacją sieci Web |

Pomaga to identyfikować:

* Gdzie są wykonywane najczęstsze pomyłki
* Gdzie są najbardziej wiarygodne ulepszenia

W rezultacie korzystanie z tych kategorii, aby skoncentrować się i priorytety pracy zabezpieczeń, tak, że jeśli wiesz, że najbardziej rozpowszechnionych problemy z zabezpieczeniami występują w danych wejściowych kategorie sprawdzania poprawności, uwierzytelniania i autoryzacji, możesz rozpocząć istnieje. Aby uzyskać więcej informacji, odwiedź stronę  **[ten link patentu](https://www.google.com/patents/US7818788)**

## <a name="next-steps"></a>Kolejne kroki

Odwiedź stronę **[zagrożenia narzędzie do modelowania zagrożeń](./azure-security-threat-modeling-tool-threats.md)** Aby dowiedzieć się więcej o kategoriach zagrożeń używa narzędzie do generowania projektu możliwe zagrożenia.