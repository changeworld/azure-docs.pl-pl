---
title: Monit o zgodę nieoczekiwany po zalogowaniu się do aplikacji | Dokumentacja firmy Microsoft
description: Jak rozwiązywać problemy z, gdy użytkownik zobaczy monit o zgodę dla aplikacji, który jest zintegrowany z usługą Azure AD, która nie oczekiwano
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 0f24ca83922cc17e94683ed1bde5ed782b93df7c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2017
ms.locfileid: "26611812"
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>Monit o zgodę nieoczekiwany po zalogowaniu się do aplikacji

Wiele aplikacji, które integrują się z usługą Azure Active Directory wymaga uprawnień do różnych zasobów, aby można było uruchomić. Jeśli te zasoby również są zintegrowane z usługą Azure Active Directory, uprawnienia dostępu do nich żądania przy użyciu platformy zgody usługi Azure AD. 

Powoduje to zgody monit jest wyświetlany przy pierwszym uruchomieniu aplikacji jest używany, która często jest to jednorazowa operacja. 

## <a name="scenarios-in-which-users-see-consent-prompts"></a>Scenariusze, w których użytkownicy widzą zgody monitów

Dodatkowe monity można oczekiwać w różnych scenariuszach:

* Zmieniono zestaw uprawnień wymaganych przez aplikację.

* Administrator nie jest użytkownik, który pierwotnie zgodę na aplikacji, a teraz różnych (użytkownika niebędącego administratorem) używa aplikacji po raz pierwszy.

* Użytkownik, który pierwotnie zgodę na aplikację została administrator, ale nie one zgody w imieniu całej organizacji.

* Aplikacja używa [zgody przyrostowych i dynamicznych](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare#incremental-and-dynamic-consent) celu zażądania dodatkowych uprawnień po początkowym udzielono zgody. Jest to często używane, gdy funkcje opcjonalne dodatkowe aplikacji wymaga uprawnień niż wymagany do obsługi funkcji linii bazowej.

* Zgody został odwołany po początkowym pozwolenia.

* Deweloper skonfigurował aplikacji do wyświetlania monitów zgodę za każdym razem, gdy jest używany (Uwaga: to nie jest najlepszym rozwiązaniem).

## <a name="next-steps"></a>Następne kroki

-   [Aplikacje, uprawnienia i zgody w usłudze Azure Active Directory (punkt końcowy 1.0)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)

-   [Zakresy, uprawnienia i zgody w usłudze Azure Active Directory (punktu końcowego v2.0)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


