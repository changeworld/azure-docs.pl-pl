---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/27/2020
ms.author: mimart
ms.openlocfilehash: f4944881e6eff3e9a56a74bc997c280f9fff6be8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187007"
---
## <a name="ropc-flow-notes"></a>Uwagi dotyczące przepływu ROPC
W usłudze Azure Active Directory B2C (Azure AD B2C) obsługiwane są następujące opcje:

- **Klient macierzysty:** Interakcja użytkownika podczas uwierzytelniania ma miejsce, gdy kod jest uruchamiany na urządzeniu po stronie użytkownika. Urządzenie może być aplikacją mobilną działającą w natywnym systemie operacyjnym, takim jak Android i iOS.
- **Przepływ klienta publicznego:** Tylko poświadczenia użytkownika, zebrane przez aplikację, są wysyłane w wywołaniu interfejsu API. Poświadczenia aplikacji nie są wysyłane.
- **Dodaj nowe oświadczenia:** Zawartość tokenu identyfikatora można zmienić, aby dodać nowe oświadczenia.

Następujące przepływy nie są obsługiwane:

- **Serwer do serwera:** System ochrony tożsamości wymaga niezawodnego adresu IP zebranego od wywołującego (klienta macierzystego) w ramach interakcji. W wywołaniu interfejsu API po stronie serwera używany jest tylko adres IP serwera. Jeśli przekroczono próg dynamiczny nieudanych uwierzytelniania, system ochrony tożsamości może zidentyfikować powtarzający się adres IP jako osobę atakującą.
- **Przepływ klienta poufnego:** Identyfikator klienta aplikacji jest sprawdzany, ale klucz tajny aplikacji nie jest sprawdzany.

Korzystając z przepływu ROPC, należy wziąć pod uwagę następujące kwestie:

- ROPC nie działa, gdy występuje przerwa w przepływie uwierzytelniania, który wymaga interakcji z użytkownikiem. Na przykład, gdy hasło wygasło lub wymaga zmiany, wymagane jest uwierzytelnianie wieloskładnikowe lub gdy podczas logowania musi być gromadzonych więcej informacji (na przykład zgoda użytkownika).
- ROPC obsługuje tylko konta lokalne. Użytkownicy nie mogą logować się u dostawców tożsamości federacyjnych, takich jak Microsoft, Google+, Twitter, AD-FS lub Facebook.
- Zarządzanie sesjami, w tym keep me signed-in (KMSI), nie ma zastosowania.
