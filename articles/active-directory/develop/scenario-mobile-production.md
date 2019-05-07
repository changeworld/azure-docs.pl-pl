---
title: Aplikacja mobilna, że wywołuje interfejsy API sieci web (przenoszenie do środowiska produkcyjnego) — Platforma tożsamości usługi Microsoft
description: Dowiedz się, jak utworzyć aplikację mobilną, która wywołuje interfejsy API sieci Web (przenoszenie do środowiska produkcyjnego)
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d37d2de561a6f5841bf17a47fef86ad7639750d5
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074953"
---
# <a name="mobile-app-that-calls-web-apis---move-to-production"></a>Aplikacja mobilna, która wywołuje sieci web interfejsy API — przenoszenie do środowiska produkcyjnego

Ten artykuł zawiera szczegółowe informacje na temat zwiększania jakości i niezawodności aplikacji w taki sposób, aby przenieść go do środowiska produkcyjnego.

## <a name="handling-errors-in-mobile-applications"></a>Obsługa błędów w aplikacjach mobilnych.

W różnych przepływów, które firma Microsoft wyróżnienia do tej pory istnieją różne warunki błędów, które mogą być spowodowane. Podstawowy scenariusz obsługi jest dyskretnej błędów i powrót do interakcji. Istnieją dodatkowe warunki, które również należy rozważyć w środowisku produkcyjnym, w tym sytuacje nie sieci, przerwy w działaniu usługi, wymagana jest zgoda administratora i innych przypadkach specyficzne dla scenariusza.

Każdej biblioteki MSAL zawiera przykładowy kod i witryny typu wiki zawartości omawia lepszą obsługę tych warunków.

- [Biblioteka MSAL Wiki dla systemu Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [Biblioteka MSAL dla systemu iOS typu Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [Platformy MSAL.NET witryny typu Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigating-and-investigating-issues"></a>Ograniczanie ryzyka i badania problemów

Zbieranie danych może pomóc w aplikacji diagnozować problemy. Aby uzyskać więcej szczegółów na typ danych, można zbierać, zobacz poszczególnych stron typu wiki biblioteki MSAL platformy.

- Użytkownicy mogą poprosić o pomoc w przypadku napotkania problemu. Najlepszym rozwiązaniem jest do przechwytywania i tymczasowego przechowywania dzienników i Zezwalaj użytkownikom na przekazywanie je gdzieś. Biblioteki MSAL udostępnia rozszerzenia rejestrowania, aby przechwycić szczegółowe informacje na temat uwierzytelniania
- Jeśli to możliwe, należy włączyć telemetrię przy użyciu biblioteki MSAL do zbierania danych o jak użytkownicy logują się do aplikacji.

## <a name="testing-your-app"></a>Testowanie aplikacji

Pamiętaj przetestować Twojej aplikacji [listę kontrolną integracji](identity-platform-integration-checklist.md).

## <a name="next-steps"></a>Kolejne kroki

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
