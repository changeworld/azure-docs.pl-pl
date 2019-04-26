---
title: Zarządzanie zagrożeniami do zasobów i danych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Poznaj techniki wykrywania i łagodzenia skutków ataków typu "odmowa usługi" i złamania hasła w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 65c74b7451c5a605ca8c2e866296c87c0320b730
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60316905"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>Zarządzanie zagrożeniami do zasobów i danych w usłudze Azure Active Directory B2C

Usługa Azure Active Directory (Azure AD) B2C ma wbudowane funkcje, które mogą pomóc w ochronie przed zagrożeniami dla zasobów i danych. Zagrożenia te obejmują ataków typu "odmowa usługi" i złamania hasła. Ataki typu "odmowa usługi" wprowadzać zasobów niedostępny oprogramowanie wyświetla odpowiednich użytkowników. Złamania hasła prowadzić do nieautoryzowanego dostępu do zasobów. 

## <a name="denial-of-service-attacks"></a>Ataki typu "odmowa usługi"

Usługa Azure AD B2C systemów przed atakami powódź SYN SYN pliki cookie. Usługa Azure AD B2C chroni także przed atakami typu "odmowa usługi" przy użyciu limitów szybkości i połączeń.

## <a name="password-attacks"></a>Złamania hasła

Hasła, które są ustawiane przez użytkowników muszą być zrealizowane złożone. Usługa Azure AD B2C ma ograniczenie technik w celu złamania hasła. Środki zaradcze obejmuje ataków siłowych hasła i złamania hasła słownika. Za pomocą różnych sygnały, usługi Azure AD B2C analizuje integralność żądań. Usługa Azure AD B2C jest przeznaczony do inteligentnie odróżnić oprogramowanie wyświetla odpowiednich użytkowników przed hakerami i botnetami. 

Usługa Azure AD B2C używa zaawansowanej strategii blokowania kont. Konta są zablokowane oparte na adresie IP żądania i wprowadzonym. Czas trwania blokady zwiększa także zależności od stopnia prawdopodobieństwa ataku. Po hasła jest podjęto 10 prób niepomyślnie, występuje blokady jednej minuty. Podczas następnego logowania zakończy się niepowodzeniem po odblokowaniu konta innego blokady jednominutowy występuje i kontynuuje dla każdego Nieudane logowanie. Wielokrotnego wprowadzania tego samego hasła nie są liczone jako wiele nieudanych logowań. 

Pierwszy okresy 10 blokady są długie minuty. Okresy 10 następnych blokady są nieco dłużej i zwiększenie czasu trwania po okresach co 10 blokady. Resetuje licznik blokady do zera po pomyślnym zalogowaniu, jeśli konto nie jest zablokowane. Okresy blokady może trwać do pięciu godzin. 

Obecnie nie jest możliwe:

- Wyzwalanie blokady z mniej niż 10 nieudanych prób logowania
- Pobierz listę blokady konta
- Konfigurowanie blokady zasad

Aby uzyskać więcej informacji, odwiedź stronę [Microsoft Trust Center](https://www.microsoft.com/trustcenter/default.aspx).
