---
title: Kroki konfigurowania aplikacji OpenID/OAuth z galerii aplikacji Azure AD | Dokumentacja firmy Microsoft
description: Kroki konfigurowania aplikacji OpenID/OAuth z galerii aplikacji Azure AD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: eedebb76-e78c-428f-9cf0-5891852e79fb
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2018
ms.author: jeedes
ms.openlocfilehash: 176af41197810059a17daf5ab09d29e0169e9640
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36225011"
---
# <a name="steps-to-configure-an-openidoauth-application-from-azure-ad-app-gallery"></a>Kroki konfigurowania aplikacji OpenID/OAuth z galerii aplikacji Azure AD

## <a name="process-of-open-id-application-addition-from-gallery"></a>Proces dodawania aplikacji Open ID z galerii

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory](./media/openidoauth-tutorial/tutorial_general_01.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa](./media/openidoauth-tutorial/tutorial_general_02.png)

3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji](./media/openidoauth-tutorial/tutorial_general_03.png)

4. W polu wyszukiwania wpisz **Nazwa aplikacji**, wybierz pozycję **żądanego aplikacji** w panelu wyników, zaloguj się do aplikacji.

    ![Dodawanie aplikacji](./media/openidoauth-tutorial/addfromgallery.png)

    > [!NOTE]
    > W przypadku aplikacji Open ID Connect i OAuth Dodaj przycisk jest domyślnie wyłączona. W tym miejscu kliknij Administrator dzierżawy **rejestracji** przycisk i podaj zgody do aplikacji. Z tym że aplikacja będzie zostaną dodane do dzierżawy klienta, nie trzeba dodawać jawnie i czy konfiguracje.

    ![Przycisk dodawania](./media/openidoauth-tutorial/addbutton.png)

5. Po kliknięciu w link do rejestracji, nastąpi przekierowanie do strony usługi Azure AD dla poświadczeń logowania.

6. Po pomyślnym uwierzytelnieniu użytkownik musi zaakceptować zgody ze strony zgody, a następnie, wyświetli się Strona główna aplikacji.

    > [!NOTE]
    > Klienci mogą dodawać tylko jedno wystąpienie aplikacji. Jeśli masz już dodane i próbował Podaj zgody ponownie go nie zostanie dodany ponownie w dzierżawie. Dlatego logicznie użyciem tylko jedno wystąpienie aplikacji w dzierżawie.

## <a name="authentication-flow-using-openid-connect"></a>Przepływ uwierzytelniania przy użyciu protokołu OpenID Connect

Najbardziej podstawowa przepływu logowania zawiera następujące czynności — każdy z nich jest szczegółowo opisana poniżej.

![Przepływ uwierzytelniania przy użyciu protokołu OpenID Connect](./media/openidoauth-tutorial/authenticationflow.png)

* **Wielodostępne aplikacji** -aplikacji wielodostępnych jest przeznaczony do użycia w wielu organizacjach nie tylko jednej z organizacji. Są to zazwyczaj oprogramowania jako — usługa (SaaS) aplikacje napisane przez niezależnego dostawcy oprogramowania (ISV). Aplikacje wielodostępne konieczne udostępniane w każdym katalogu gdzie będą one używane, wymaga zgody użytkownika lub administratora, aby je zarejestrować. Ten proces zgody uruchamiana, gdy aplikacja został zarejestrowany w katalogu i otrzyma dostęp do interfejsu API programu Graph lub być może inny interfejs API sieci web. Gdy użytkownik lub administrator z innej organizacji, zarejestruj się, aby korzystać z aplikacji, mają być przedstawiane z okna dialogowego, które wyświetla uprawnienia wymagane przez aplikację. Użytkownik lub administrator może następnie zgodę na aplikacji, która zapewnia dostęp do aplikacji do określonych danych, a na koniec rejestruje aplikację w ich katalogu.

    > [!NOTE]
    > Jeśli tworzysz aplikację dostępne dla użytkowników w wielu katalogach, należy mechanizm, aby określić, które dzierżawy są one w. Stosowanie pojedynczej dzierżawy musi tylko do przeszukania własnego katalogu dla użytkownika, gdy aplikacja wielodostępne musi zidentyfikować określonego użytkownika ze wszystkich katalogów w usłudze Azure AD. Aby wykonać to zadanie, usługa Azure AD zapewnia wspólnego punktu końcowego uwierzytelniania gdzie wszelkie aplikacje wielodostępne może kierować żądań logowania, zamiast punktu końcowego specyficznego dla dzierżawy. Ten punkt końcowy jest [ https://login.microsoftonline.com/common ](https://login.microsoftonline.com/common) dla wszystkich katalogów w usłudze Azure AD, punkt końcowy specyficznego dla dzierżawy może być [ https://login.microsoftonline.com/contoso.onmicrosoft.com ](https://login.microsoftonline.com/contoso.onmicrosoft.com). Typowe punkt końcowy jest szczególnie ważne podczas tworzenia aplikacji, ponieważ będą potrzebne logiki niezbędne do obsługi wielu dzierżawców podczas logowania, wylogowania i weryfikacji tokenu.

Azure AD zespołu domyślnie zamienia wielodostępnych aplikacji, który można łatwo uzyskać dostęp przez różnych organizacji i jest łatwy w użyciu po zaakceptowaniu zgody.

## <a name="what-is-consent-framework"></a>Co to jest zgoda Framework?

Framework zgody usługi Azure AD ułatwia opracowywanie wielodostępnej sieci web i aplikacje klienckie natywnego. Te aplikacje zezwalanie logowania według konta użytkowników z dzierżawy usługi Azure AD, innego niż ten, na którym aplikacja jest zarejestrowany. Może muszą uzyskać dostępu do sieci web API innych usług firmy Microsoft, oprócz własnego interfejsów API sieci web i interfejsów API, np. interfejsu API programu Microsoft Graph (Aby uzyskać dostęp do usługi Azure Active Directory, Intune i usług w usłudze Office 365). Platformę opiera się na użytkownika lub administratora wyrażenia zgody do aplikacji, która żąda rejestrowana w ich katalogu, które mogą dotyczyć uzyskiwanie dostępu do danych katalogu. Po zgodę, aplikacja kliencka będzie można wywołać interfejsu API programu Microsoft Graph w imieniu użytkownika i użyć tych informacji, zgodnie z potrzebami.

[Interfejsu API programu Microsoft Graph](https://graph.microsoft.io/) zapewnia dostęp do danych w usłudze Office 365 (na przykład kalendarzy i komunikaty z programu Exchange, witryn i list programu SharePoint, dokumentów z usługi OneDrive, komputery przenośne z programu OneNote, zadania planowania, skoroszyty programu Excel, itp.), a także użytkowników i grup z usługi Azure AD i innych obiektów danych z więcej usług chmurowych firmy Microsoft.

W poniższej procedurze pokazano, jak zgody wystąpić działa dla deweloperów aplikacji i użytkownika.

1. Załóżmy, że masz aplikacji klienta sieci web, która wymaga określonych uprawnień dostępu do zasobów/interfejs API do żądania. Portalu Azure służy do deklarowania żądania uprawnień w czasie konfigurowania. Podobnie jak inne ustawienia konfiguracji staną się częścią aplikacji usługi Azure AD rejestracji:

    ![Interfejs API programu Graph](./media/openidoauth-tutorial/graphapi.png)

2. Należy wziąć pod uwagę uprawnienia aplikacji zostały zaktualizowane, aplikacja jest uruchomiona, czy użytkownik ma być używany po raz pierwszy. Najpierw aplikacja musi uzyskać kod autoryzacji z usługi Azure AD / punktu końcowego autoryzacji. Aby uzyskać nowe dostępu i token odświeżania można następnie kod autoryzacji.

3. Jeśli użytkownik nie jest już uwierzytelniony, Azure AD / authorize monity punktu końcowego dla logowania.

    ![Authentication](./media/openidoauth-tutorial/authentication.png)

4. Po zalogowaniu się użytkownika, usługi Azure AD określi, jeśli użytkownik chce można wyświetlić strony zgody. To zależy od tego, czy użytkownik (lub jego organizację administratora) już udzielił zgody aplikacji. Jeśli zgody nie ma już udzielone, usługi Azure AD monituje użytkownika o zgodę i wyświetla wymaganych uprawnień wymaganych do działania. Zestaw uprawnień, który jest wyświetlany w oknie dialogowym zgody odpowiada wybranych w delegowane uprawnienia w portalu Azure.

    ![Stronę udzielania zgody](./media/openidoauth-tutorial/consentpage.png)

Niektóre uprawnienia można zgodę na przez zwykłego użytkownika, a inne wymagają zgody administratora dzierżawy.

## <a name="whats-the-difference-between-admin-consent-and-user-consent"></a>Jaka jest różnica między zgody administratora i zgody użytkownika?

Administrator może również wyrażenia zgody na aplikacji uprawnień delegowanych w imieniu wszystkich użytkowników w dzierżawie. Administracyjne zgody uniemożliwia okna dialogowego zgody pojawiające się dla wszystkich użytkowników w dzierżawie i może odbywać się w portalu Azure przez użytkowników z rolą administratora. Na stronie ustawień aplikacji kliknij wymagane uprawnienia, a następnie kliknij przycisk udzielanie uprawnień.

![Udziel uprawnień](./media/openidoauth-tutorial/grantpermission.png)

> [!NOTE]
> Udzielania zgody jawnej za pomocą przycisku udzielanie uprawnień jest obecnie wymagane dla aplikacji jednej strony (SPA), które używają ADAL.js. W przeciwnym razie aplikacja kończy się niepowodzeniem podczas żądania tokenu dostępu.

Uprawnienia tylko do aplikacji zawsze Wymagaj zgody administratora dzierżawy. Jeśli aplikacja żąda uprawnienia tylko do aplikacji, a użytkownik próbuje zalogować się do aplikacji, jest wyświetlany komunikat o błędzie informujący, że użytkownik nie jest w stanie zgody.

Jeśli aplikacja używa uprawnienia, które wymagają zgody administratora, należy mieć gestu, takich jak przycisk lub łącze którym administrator może zainicjować akcji. Żądanie aplikacji wysyła dla tej akcji jest zwykle OAuth2/OpenID Connect autoryzacji żądania, które obejmuje również monit = parametru ciągu zapytania admin_consent. Po zgodził administratora i nazwy głównej usługi jest tworzony w dzierżawy przez klienta, kolejne żądania logowania nie ma potrzeby monitu = admin_consent parametru. Ponieważ administrator zdecydował się, że żądane uprawnienia są dopuszczalne, nie innych użytkowników w dzierżawie monit o zgodę od tej pory. Administrator dzierżawy może wyłączyć przez regularne użytkownikom wyrazić zgodę na aplikacje. Jeśli ta funkcja jest wyłączona, zgody administratora jest zawsze wymagane dla aplikacji do użycia w dzierżawie. Jeśli chcesz przetestować aplikację za zgodą użytkownika końcowego wyłączone, możesz znaleźć przełącznik konfiguracji w [portalu Azure](https://portal.azure.com/) w [ustawienia użytkownika](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) w obszarze **przedsiębiorstwa aplikacje**

Monit = admin_consent parametru można również przez aplikacje, które zażądać uprawnień, które nie wymagają zgody administratora. Przykładem po tym będzie służyć jest, jeśli aplikacja wymaga środowisko, w którym administrator dzierżawy "zarejestrowaniu" jeden czasie, a nie inne użytkownicy otrzymają monit o zgodę od tego momentu.

Jeśli aplikacja wymaga zgody administratora, a administrator loguje się bez monitu = parametr admin_consent wysyłane, gdy administrator pomyślnie zgadza się na aplikacji dotyczy tylko dla swojego konta użytkownika. Regularne użytkownicy nadal nie będą mogli zalogować się lub wyrażenia zgody na aplikację. Ta funkcja jest przydatna, jeśli chcesz nadać możliwość Eksploruj aplikacji przed zezwoleniem na innym użytkownikom dostęp administratora dzierżawy.