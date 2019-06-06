---
title: Aplikacja rozszerzeń w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Przywracanie b2c-extensions-app.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 9/06/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d9fbceee8fb0e565df668406f09df27272318dd0
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66509481"
---
# <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C: Aplikacja rozszerzeń

Po utworzeniu katalogu usługi Azure AD B2C aplikacja o nazwie `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` automatycznie jest tworzony w nowym katalogu. Tej aplikacji, nazywane **b2c-extensions-app**, jest widoczna w *rejestracje aplikacji*. Używane przez usługę Azure AD B2C do przechowywania informacji o użytkownikach i atrybuty niestandardowe. Usunięcie aplikacji usługi Azure AD B2C nie będzie działać prawidłowo, i będzie mieć wpływ na środowisko produkcyjne.

> [!IMPORTANT]
> Nie należy usuwać b2c-extensions-app, chyba że zamierzasz natychmiast usunąć dzierżawy. Jeśli aplikacja pozostaje usuniętych przez dłużej niż 30 dni, informacje o użytkowniku zostaną trwale utracone.

## <a name="verifying-that-the-extensions-app-is-present"></a>Weryfikowanie, czy aplikacja rozszerzeń jest obecna

Aby sprawdzić, czy występuje b2c-extensions-app:

1. W dzierżawie usługi Azure AD B2C, kliknij pozycję **wszystkich usług** w menu nawigacji po lewej stronie.
1. Wyszukaj i Otwórz **rejestracje aplikacji**.
1. Wyszukaj aplikację, która rozpoczyna się od **b2c-extensions-app**

## <a name="recover-the-extensions-app"></a>Odzyskiwanie aplikacji rozszerzeń

Jeśli przypadkowo usunięty b2c-extensions-app, dostępna 30 dni, aby ją odzyskać. Można przywrócić aplikację za pomocą interfejsu API programu Graph:

1. Przejdź do [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/).
1. Zaloguj się do witryny jako administratora globalnego dla katalogu usługi Azure AD B2C, który chcesz przywrócić usunięte aplikacji dla. Ten administrator globalny musi mieć adres e-mail, który jest podobny do następującego: `username@{yourTenant}.onmicrosoft.com`.
1. Wystawiać GET protokołu HTTP względem adresu URL `https://graph.windows.net/myorganization/deletedApplications` z wersją api-version = 1.6. Ta operacja spowoduje wyświetlenie listy wszystkich aplikacji, które zostały usunięte w ciągu ostatnich 30 dni.
1. Znajdź aplikację na liście, których nazwa rozpoczyna się od "b2c-rozszerzenia app" i skopiuj jej `objectid` wartości właściwości.
1. Wystawiać metodę POST protokołu HTTP względem adresu URL `https://graph.windows.net/myorganization/deletedApplications/{OBJECTID}/restore`. Zastąp `{OBJECTID}` część adresu URL za pomocą `objectid` z poprzedniego kroku. 

Teraz powinno być możliwe do [przywróconej aplikacja](#verifying-that-the-extensions-app-is-present) w witrynie Azure portal.

> [!NOTE]
> Aplikację można przywrócić tylko, jeśli został on usunięty w ciągu ostatnich 30 dni. Jeśli minęło ponad 30 dni, dane zostaną trwale utracone. Aby uzyskać dodatkową pomoc Utwórz bilet pomocy technicznej.
