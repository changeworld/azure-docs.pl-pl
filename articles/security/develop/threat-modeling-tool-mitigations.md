---
title: Środki zaradcze — narzędzie microsoft do modelowania zagrożeń — Azure | Dokumenty firmy Microsoft
description: Strona Łagodzenia dla narzędzia Microsoft Threat Modeling Tool podkreślając możliwe rozwiązania do najbardziej narażonych zagrożeń generowanych.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 748d10b994080b667885e5d0d5f4d688269e86ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68728042"
---
# <a name="microsoft-threat-modeling-tool-mitigations"></a>Narzędzia do modelowania zagrożeń firmy Microsoft – środki zaradcze

Narzędzie do modelowania zagrożeń jest podstawowym elementem cyklu życia programu Microsoft Security Development (SDL). Umożliwia architektom oprogramowania wczesne identyfikowanie i łagodzenie potencjalnych problemów z zabezpieczeniami, gdy są one stosunkowo łatwe i opłacalne do rozwiązania. W rezultacie znacznie zmniejsza całkowity koszt rozwoju. Ponadto zaprojektowaliśmy narzędzie z myślą o ekspertach niezwiązanych z bezpieczeństwem, ułatwiając modelowanie zagrożeń wszystkim deweloperom, zapewniając jasne wskazówki dotyczące tworzenia i analizowania modeli zagrożeń.

Odwiedź narzędzie do **[modelowania zagrożeń,](threat-modeling-tool.md)** aby rozpocząć już dziś!

## <a name="mitigation-categories"></a>Kategorie łagodzenia zmiany klimatu

Środki zaradcze narzędzia do modelowania zagrożeń są podzielone na kategorie zgodnie z ramką zabezpieczeń aplikacji sieci Web, która składa się z następujących elementów:

| Kategoria | Opis |
| -------- | ----------- |
| **[Inspekcja i rejestrowanie](threat-modeling-tool-auditing-and-logging.md)** | Kto co zrobił i kiedy? Inspekcja i rejestrowanie odnoszą się do sposobu, w jaki aplikacja rejestruje zdarzenia związane z zabezpieczeniami |
| **[Uwierzytelnianie](threat-modeling-tool-authentication.md)** | Kim jesteś? Uwierzytelnianie to proces, w którym jednostka potwierdza tożsamość innej jednostki, zazwyczaj za pomocą poświadczeń, takich jak nazwa użytkownika i hasło |
| **[Autoryzacji](threat-modeling-tool-authorization.md)** | Co możesz zrobić? Autoryzacja to sposób, w jaki aplikacja udostępnia formanty dostępu dla zasobów i operacji |
| **[Bezpieczeństwo komunikacji](threat-modeling-tool-communication-security.md)** | Z kim rozmawiasz? Bezpieczeństwo komunikacji zapewnia, że cała wykonywana komunikacja jest jak najbardziej bezpieczna |
| **[Zarządzanie konfiguracją](threat-modeling-tool-configuration-management.md)** | Kto działa jako aplikacja? Z jakimi bazami danych się łączy? W jaki sposób podawany jest wniosek? W jaki sposób te ustawienia są zabezpieczone? Zarządzanie konfiguracją odnosi się do sposobu, w jaki aplikacja radzi sobie z tymi problemami operacyjnymi |
| **[Kryptografia](threat-modeling-tool-cryptography.md)** | Jak zachowujesz tajemnice (poufność)? W jaki sposób chronisz swoje dane lub biblioteki (integralność)? Jak dostarczasz nasiona dla losowych wartości, które muszą być silnie kryptograficznie? Kryptografia odnosi się do tego, w jaki sposób aplikacja wymusza poufność i integralność |
| **[Zarządzanie wyjątkami](threat-modeling-tool-exception-management.md)** | Gdy wywołanie metody w aplikacji nie powiedzie się, co robi aplikacja? Ile ujawniasz? Czy zwracasz przyjazne informacje o błędach użytkownikom końcowym? Czy przekazujesz cenne informacje o wyjątkach z powrotem do osoby dzwoniącej? Czy aplikacja nie powiedzie się bezpiecznie? |
| **[Sprawdzanie poprawności danych wejściowych](threat-modeling-tool-input-validation.md)** | Skąd wiesz, że dane wejściowe, które otrzymuje aplikacja, są prawidłowe i bezpieczne? Sprawdzanie poprawności danych wejściowych odnosi się do sposobu filtrowania aplikacji, czyszczenia lub odrzuca dane wejściowe przed dodatkowym przetwarzaniem. Należy wziąć pod uwagę ograniczenie danych wejściowych za pośrednictwem punktów wejścia i kodowania danych wyjściowych za pośrednictwem punktów wyjścia. Czy ufasz danym ze źródeł, takich jak bazy danych i udziały plików? |
| **[Dane wrażliwe](threat-modeling-tool-sensitive-data.md)** | W jaki sposób aplikacja obsługuje poufne dane? Poufne dane odnoszą się do sposobu, w jaki aplikacja obsługuje wszelkie dane, które muszą być chronione w pamięci, w sieci lub w trwałych magazynach |
| **[Zarządzanie sesjami](threat-modeling-tool-session-management.md)** | W jaki sposób aplikacja obsługuje i chroni sesje użytkowników? Sesja odnosi się do serii powiązanych interakcji między użytkownikiem a aplikacją sieci Web |

Pomaga to zidentyfikować:

* Gdzie są najczęstsze błędy popełnione
* Gdzie są najbardziej użyteczne ulepszenia

W rezultacie te kategorie są używane do ustawiania ostrości i określania priorytetów pracy związanej z zabezpieczeniami, dzięki czemu jeśli wiesz, że występują najbardziej rozpowszechnione problemy z zabezpieczeniami w kategoriach sprawdzania poprawności danych wejściowych, uwierzytelniania i autoryzacji, możesz rozpocząć tam. Aby uzyskać więcej informacji, odwiedź ** [ten link patentowy](https://www.google.com/patents/US7818788)**

## <a name="next-steps"></a>Następne kroki

Odwiedź **[stronę Zagrożenia związane z modelingiem zagrożeń,](threat-modeling-tool-threats.md)** aby dowiedzieć się więcej o kategoriach zagrożeń używanych przez narzędzie do generowania możliwych zagrożeń projektowych.