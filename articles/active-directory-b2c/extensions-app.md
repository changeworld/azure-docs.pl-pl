---
title: Aplikacja Rozszerzenia w usłudze Azure Active Directory B2C | Dokumenty firmy Microsoft
description: Przywracanie b2c-extensions-app.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/06/2017
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 547b625996a65999c32c1b73699e3b408be01de3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188600"
---
# <a name="azure-ad-b2c-extensions-app"></a>Aplikacja usługi Azure AD B2C: Rozszerzenia

Po utworzeniu katalogu usługi Azure AD B2C aplikacja wywoływana `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` jest automatycznie tworzona wewnątrz nowego katalogu. Ta aplikacja, dalej **b2c-extensions-app,** jest widoczna w *rejestracjach aplikacji*. Jest on używany przez usługę Azure AD B2C do przechowywania informacji o użytkownikach i atrybutów niestandardowych. Jeśli aplikacja zostanie usunięta, usługa Azure AD B2C nie będzie działać poprawnie i środowisko produkcyjne zostanie naruszone.

> [!IMPORTANT]
> Nie należy usuwać b2c-extensions-app, chyba że planujesz natychmiast usunąć dzierżawę. Jeśli aplikacja pozostanie usunięta przez ponad 30 dni, informacje o użytkowniku zostaną trwale utracone.

## <a name="verifying-that-the-extensions-app-is-present"></a>Sprawdzanie obecności aplikacji rozszerzeń

Aby sprawdzić, czy aplikacja b2c-extensions jest obecna:

1. Wewnątrz dzierżawy usługi Azure AD B2C kliknij **wszystkie usługi** w menu nawigacji po lewej stronie.
1. Wyszukaj i otwórz **rejestracje aplikacji**.
1. Poszukaj aplikacji, która zaczyna się od **b2c-extensions-app**

## <a name="recover-the-extensions-app"></a>Odzyskiwanie aplikacji rozszerzeń

Jeśli przypadkowo usuniesz aplikację b2c-extensions, masz 30 dni na jej odzyskanie. Aplikację można przywrócić za pomocą interfejsu API wykresu:

1. Przejdź do [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/)pliku .
1. Zaloguj się do witryny jako administrator globalny dla katalogu usługi Azure AD B2C, dla którego chcesz przywrócić usuniętą aplikację. Ten administrator globalny musi mieć adres e-mail podobny do następującego: `username@{yourTenant}.onmicrosoft.com`.
1. Wystaw HTTP GET względem `https://graph.windows.net/myorganization/deletedApplications` adresu URL z api-version=1.6. Ta operacja wyświetli listę wszystkich aplikacji, które zostały usunięte w ciągu ostatnich 30 dni.
1. Znajdź aplikację na liście, gdzie nazwa zaczyna się od "b2c-extension-app" i skopiuj jej `objectid` wartość właściwości.
1. Wystawiaj wpis HTTP `https://graph.windows.net/myorganization/deletedApplications/{OBJECTID}/restore`względem adresu URL . Zastąp `{OBJECTID}` część adresu `objectid` URL z poprzedniego kroku.

Teraz powinna być widoczna [przywrócona aplikacja](#verifying-that-the-extensions-app-is-present) w witrynie Azure portal.

> [!NOTE]
> Aplikację można przywrócić tylko wtedy, gdy została usunięta w ciągu ostatnich 30 dni. Jeśli minęło więcej niż 30 dni, dane zostaną trwale utracone. Aby uzyskać więcej pomocy, złóż bilet pomocy technicznej.
