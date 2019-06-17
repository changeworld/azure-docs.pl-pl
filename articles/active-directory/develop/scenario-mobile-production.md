---
title: Aplikacja mobilna, że wywołuje interfejsy API sieci web (przenoszenie do środowiska produkcyjnego) — Platforma tożsamości usługi Microsoft
description: Informacje o sposobie tworzenia aplikacji mobilnych, połączeń internetowych interfejsów API (przenoszenie do środowiska produkcyjnego)
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
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d8b6a5c2a29228de806088ea93e197d42bf1ab47
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65962352"
---
# <a name="mobile-app-that-calls-web-apis---move-to-production"></a>Aplikacja mobilna, która wywołuje sieci web interfejsy API — przenoszenie do środowiska produkcyjnego

Ten artykuł zawiera szczegółowe informacje na temat poprawić jakość i niezawodność aplikacji przed przeniesieniem do środowiska produkcyjnego.

## <a name="handling-errors-in-mobile-applications"></a>Obsługa błędów w aplikacjach mobilnych.

Liczba błędów może wystąpić w tym momencie w swojej aplikacji. Główne scenariusze w celu obsługi są błędy w trybie dyskretnym i przejścia do interakcji. Inne warunki, które należy wziąć pod uwagę w środowisku produkcyjnym obejmują sytuacjach nie sieci, przerwy w działaniu usługi, wymagania dotyczące zgody administratora i innych przypadkach specyficzne dla scenariusza.

Każda biblioteka MSAL zawiera przykładowy kod i typu wiki biblioteki zawartości w tym artykule opisano sposób obsługi tych warunków:

- [Biblioteka MSAL Wiki dla systemu Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [Biblioteka MSAL dla systemu iOS typu Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [Platformy MSAL.NET witryny typu Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigating-and-investigating-issues"></a>Ograniczanie ryzyka i badania problemów

Aby zdiagnozować problemy w swojej aplikacji, ułatwia zbieranie danych. Uzyskać informacji na temat typów danych można zbierać, zobacz Biblioteka MSAL Wiki platformy.

- Użytkownicy mogą poprosić o pomoc, gdy występują problemy. Najlepszym rozwiązaniem jest do przechwytywania i tymczasowego przechowywania dzienników i podaj lokalizację, w którym użytkownicy mogą przekazać je. Biblioteka MSAL udostępnia rozszerzenia rejestrowania przechwycić szczegółowe informacje dotyczące uwierzytelniania.
- Jeśli jest dostępna, należy włączyć telemetrię przy użyciu biblioteki MSAL do zbierania danych o jak użytkownicy logują się aplikacji.

## <a name="next-steps"></a>Kolejne kroki

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
