---
title: Nieoczekiwany monit o zgodę podczas logowania się do aplikacji | Dokumenty firmy Microsoft
description: Jak rozwiązywać problemy, gdy użytkownik zobaczy monit o zgodę dla aplikacji zintegrowanej z usługą Azure AD, której nie można się spodziewać
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83d043ecef152f977437e21e2caec40d1c40ce0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65781149"
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>Nieoczekiwany monit o zgodę podczas logowania się do aplikacji

Wiele aplikacji, które integrują się z usługą Azure Active Directory wymagają uprawnień do różnych zasobów w celu uruchomienia. Gdy te zasoby są również zintegrowane z usługą Azure Active Directory, uprawnienia dostępu do nich jest wymagane przy użyciu platformy zgody usługi Azure AD. 

Powoduje to wyświetlenie monitu zgody przy pierwszym użyciu aplikacji, która często jest operacją jednorazową. 

## <a name="scenarios-in-which-users-see-consent-prompts"></a>Scenariusze, w których użytkownicy widzą monity o zgodę

W różnych scenariuszach można spodziewać się dodatkowych monitów:

* Zmieniono zestaw uprawnień wymaganych przez aplikację.

* Użytkownik, który pierwotnie wyraził zgodę na aplikację, nie był administratorem, a teraz inny (nie-admin) Użytkownik korzysta z aplikacji po raz pierwszy.

* Użytkownik, który pierwotnie wyraził zgodę na aplikację, był administratorem, ale nie wyraził zgody w imieniu całej organizacji.

* Aplikacja używa [przyrostowej i dynamicznej zgody,](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare#incremental-and-dynamic-consent) aby zażądać dodatkowych uprawnień po udzieleniu zgody. Jest to często używane, gdy opcjonalne funkcje aplikacji dodatkowe wymagają uprawnień poza te wymagane dla funkcji linii bazowej.

* Zgoda została cofnięta po udzieleniu początkowo.

* Deweloper skonfigurował aplikację tak, aby wymagała monitu o zgodę za każdym razem, gdy jest używana (uwaga: nie jest to najlepsze rozwiązanie).

## <a name="next-steps"></a>Następne kroki

-   [Aplikacje, uprawnienia i zgoda w usłudze Azure Active Directory (punkt końcowy w wersji 1.0)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)

-   [Zakresy, uprawnienia i zgody w usłudze Azure Active Directory (punkt końcowy w wersji 2.0)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


