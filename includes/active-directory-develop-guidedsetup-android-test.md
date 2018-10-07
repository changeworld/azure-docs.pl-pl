---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 991709ee635872e33dc89dcededc7f6ac3b28ea3
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48843293"
---
## <a name="test-your-app"></a>Testowanie aplikacji

1. Uruchom kod, aby urządzenie/emulatora.

2. Spróbuj zalogować się przy użyciu konta usługi Azure Active Directory (konto służbowe lub szkolne) lub konta Microsoft (live.com, outlook.com). 

    ![Testowanie aplikacji](media/active-directory-develop-guidedsetup-android-test/mainwindow.png)
    <br/><br/>
    ![Wprowadź nazwę użytkownika i hasło](media/active-directory-develop-guidedsetup-android-test/usernameandpassword.png)

### <a name="consent-to-your-app"></a>Zgoda na aplikację
Gdy użytkownik loguje się do aplikacji po raz pierwszy zostanie wyświetlony monit, aby wyrazić zgodę na uprawnienia wymagane przez aplikację, jak pokazano poniżej: 

![Wyrażenie zgody na dostęp do aplikacji](media/active-directory-develop-guidedsetup-android-test/androidconsent.png)


### <a name="success"></a>To wszystko!
Po Zaloguj & wyrazić zgodę, aplikacja wyświetli odpowiedzi z interfejsu API programu Microsoft Graph. Jest to wywołanie określonych **/me** punktu końcowego i zwraca [profilu użytkownika](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_get). Aby uzyskać listę innych punktów końcowych programu Microsoft Graph, zobacz [dokumentacji dla deweloperów interfejsu API Microsoft Graph](https://developer.microsoft.com/graph/docs#common-microsoft-graph-queries).

<!--start-collapse-->
### <a name="scopes-and-delegated-permissions"></a>Zakresy i delegowane uprawnienia

Interfejsu API programu Microsoft Graph wymaga *User.Read* zakresu na odczytywanie profilu użytkownika. Ten zakres jest automatycznie w każdej aplikacji, który jest zarejestrowany w portalu rejestracji aplikacji. Inne interfejsy API będą wymagać dodatkowe zakresy. Na przykład interfejsu API programu Microsoft Graph wymaga *Calendars.Read* zakres, aby wyświetlić listę kalendarzy użytkownika. 

Aby uzyskać dostęp do kalendarzy użytkownika, należy dodać *Calendars.Read* delegowane uprawnienia do rejestrowania informacji o aplikacji. Następnie należy dodać *Calendars.Read* ograniczyć zakres do `acquireTokenSilent` wywołania. 

>[!NOTE]
>Użytkownicy mogą monit o dodatkowe zgody, jeśli zmienisz rejestracji aplikacji.

<!--end-collapse-->

[!INCLUDE [Help and support](active-directory-develop-help-support-include.md)]
