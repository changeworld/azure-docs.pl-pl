---
title: Przenoszenie aplikacji mobilnych wywoływanie interfejsów API sieci Web do produkcji — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak utworzyć aplikację mobilną wywołującą interfejsy API sieci Web (Przenieś do środowiska produkcyjnego)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 1a82fc7dc1b18fa21657170af29f7de7e84d7c1f
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702032"
---
# <a name="mobile-app-that-calls-web-apis---move-to-production"></a>Aplikacja mobilna, która wywołuje interfejsy API sieci Web — Przenieś do środowiska produkcyjnego

Ten artykuł zawiera szczegółowe informacje na temat ulepszania jakości i niezawodności aplikacji przed przeniesieniem ich do środowiska produkcyjnego.

## <a name="handling-errors-in-mobile-applications"></a>Obsługa błędów w aplikacjach mobilnych

W tym momencie może wystąpić wiele warunków błędu. Główne scenariusze obsługi są cichymi awariami i powrotu do interakcji. Inne warunki, które należy wziąć pod uwagę w przypadku produkcji, obejmują między innymi sytuacje braku sieci, przestoje usług, wymagania dotyczące zgody administratora oraz inne przypadki specyficzne dla scenariusza.

Każda biblioteka MSAL ma przykładowy kod i zawartość typu wiki opisującą sposób obsługi tych warunków:

- [MSAL wiki systemu Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL wiki systemu iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigating-and-investigating-issues"></a>Eliminowanie i badanie problemów

Aby zdiagnozować problemy w aplikacji, można zbierać dane. Aby uzyskać informacje na temat rodzajów danych, które można zbierać, zobacz wiki platformy MSAL.

- Użytkownicy mogą poproszeni o pomoc w przypadku wystąpienia problemów. Najlepszym rozwiązaniem jest przechwycenie i tymczasowe przechowywanie dzienników oraz zapewnienie lokalizacji, w której użytkownicy mogą je przekazywać. MSAL udostępnia rozszerzenia rejestrowania do przechwytywania szczegółowych informacji o uwierzytelnianiu.
- Jeśli jest dostępna, Włącz telemetrię za poorednictwem MSAL, aby zebrać dane dotyczące sposobu logowania użytkowników do aplikacji.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

Wypróbuj dodatkowe przykłady dostępne w [przykładach | Publiczne aplikacje dla komputerów stacjonarnych i mobilnych](sample-v2-code.md#desktop-and-mobile-public-client-apps)
