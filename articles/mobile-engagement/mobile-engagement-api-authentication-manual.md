---
title: 'Uwierzytelniania za pomocą interfejsów API REST usługi Engagement Mobile: instalacji ręcznej'
description: Opisuje, jak ręcznie skonfigurować uwierzytelnianie dla interfejsów API REST usługi Engagement Mobile
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 2e79f9c9-41e4-45ac-b427-3b8338675163
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 0d71908b34ddf8313aa45014420c9e63a00078c9
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis---manual-setup"></a>Uwierzytelniania w usłudze Mobile Engagement REST API - instalacji ręcznej
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

Niniejsza dokumentacja jest dodatek do [Uwierzytelnij z interfejsów API REST usługi Engagement Mobile](mobile-engagement-api-authentication.md). Upewnij się, że przeczytaniu tego artykułu, aby najpierw zrozumieć kontekstu. Omówiono także alternatywny sposób celu ustawienia uwierzytelniania jednorazowe dla interfejsów API REST Mobile Engagement przy użyciu portalu Azure.

> [!NOTE]
> Poniższe instrukcje są oparte na [tego przewodnika usługi Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md). Są one dostosowaną do wymagania uwierzytelniania dla interfejsów API, Mobile Engagement. Jeśli chcesz zrozumieć następujące kroki szczegółowo odwołać się do nich.

1. Zaloguj się do konta platformy Azure za pośrednictwem [portalu Azure](https://portal.azure.com/).
2. Wybierz **usługi Active Directory** w lewym okienku.

   ![Wybierz usługi Active Directory][1]

3. Aby wyświetlić aplikacje w katalogu, wybierz **rejestracji aplikacji**.

   ![Wyświetl aplikacje][3]

4. Wybierz **nowej rejestracji aplikacji**.

   ![Dodawanie aplikacji][4]

5. Wprowadź nazwę aplikacji. Pozostaw typ aplikacji jako **aplikacji/interfejs API sieci Web**, a następnie wybierz **dalej** przycisku. Możesz podać wszelkie fikcyjny adresy URL dla **adres URL logowania**. Nie są używane w tym scenariuszu, a same adresy URL nie są weryfikowane.

   Po zakończeniu masz aplikacji usługi Azure Active Directory (Azure AD) o nazwie podane. Jest Twoje **AD\_aplikacji\_nazwa**, dlatego należy koniecznie zanotuj go.

   ![Nazwa aplikacji][8]

7. Wybierz nazwę aplikacji.

8. Znajdź **identyfikator aplikacji** i zapisz go. Jest to identyfikator klienta, która będzie służyć jako **klienta\_identyfikator** do interfejsu API wywołania.

   ![Znajdź identyfikator aplikacji][10]

9. Znajdź **klucze** sekcji po prawej stronie.

   ![Sekcja kluczy][11]

10. Utwórz nowy klucz, a następnie natychmiast skopiuj go. Nie jest wyświetlany ponownie.

    ![Okienko szczegółów klucza kluczy][12]

    > [!IMPORTANT]
    > Ten klucz wygaśnie z końcem okresu określony okres ważności. Upewnij się, że Odnów go w czasie, w przeciwnym razie uwierzytelnianie interfejsu API nie będzie już działać. Jeśli uważasz, że ten klucz został złamany, możesz usunąć i utworzyć go ponownie.
    >
    
11. Wybierz **punkty końcowe** u góry strony. Następnie skopiuj **KOŃCOWYM TOKENÓW OAUTH 2.0**.

    ![Skopiuj punktu końcowego][14]

16. Ten punkt końcowy jest w następującym formacie, gdzie jest identyfikator GUID w adresie URL Twojego **TENANT_ID**: `https://login.microsoftonline.com/<GUID>/oauth2/token`

17. Następnie należy skonfigurować uprawnienia w tej aplikacji. Aby uruchomić proces, przejdź do [portalu Azure](https://portal.azure.com).

18. Wybierz **grup zasobów**, a następnie znajdź **MobileEngagement** grupy zasobów.

    ![Znajdź MobileEngagement][15]

19. Wybierz **MobileEngagement** zasobu, grupy, a następnie wybierz **wszystkie ustawienia**.

    ![Przejdź do ustawień MobileEngagement][16]

20. Wybierz **użytkowników** w **ustawienia** sekcji. Aby dodać użytkownika, wybierz **Dodaj**.

    ![Dodawanie użytkownika][17]

21. Kliknij przycisk **wybierz rolę**.

    ![Wybieranie roli][18]

22. Wybierz **właściciela**.

    ![Wybierz rolę właściciela][19]

23. Wyszukaj nazwę aplikacji, **AD\_aplikacji\_nazwa**, w polu wyszukiwania. Ta nazwa nie jest tutaj domyślnie. Po znalezieniu go, zaznacz go. Następnie kliknij przycisk **wybierz** w dolnej części sekcji.

    ![Wybierz nazwę][20]

24. W **Dodawanie dostępu** sekcji jest widoczny jako **użytkownika 1, 0 grupy**. Aby potwierdzić zmianę, wybierz **OK**.

    ![Upewnij się, dodano użytkownika][21]

Ukończono wymaganych konfiguracji usługi Azure AD i są ustawioną na wywoływanie interfejsów API.

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication-manual/active-directory.png
[2]: ./media/mobile-engagement-api-authentication-manual/active-directory-details.png
[3]: ./media/mobile-engagement-api-authentication-manual/view-applications.png
[4]: ./media/mobile-engagement-api-authentication-manual/add-icon.png
[5]: ./media/mobile-engagement-api-authentication-manual/what-do-you-want-to-do.png
[6]: ./media/mobile-engagement-api-authentication-manual/tell-us-about-your-application.png
[7]: ./media/mobile-engagement-api-authentication-manual/app-properties.png
[8]: ./media/mobile-engagement-api-authentication-manual/aad-app.png
[9]: ./media/mobile-engagement-api-authentication-manual/configure-menu.png
[10]: ./media/mobile-engagement-api-authentication-manual/client-id.png
[11]: ./media/mobile-engagement-api-authentication-manual/client-secret.png
[12]: ./media/mobile-engagement-api-authentication-manual/keys.png
[13]: ./media/mobile-engagement-api-authentication-manual/view-endpoints.png
[14]: ./media/mobile-engagement-api-authentication-manual/app-endpoints.png
[15]: ./media/mobile-engagement-api-authentication-manual/resource-groups.png
[16]: ./media/mobile-engagement-api-authentication-manual/resource-groups-settings.png
[17]: ./media/mobile-engagement-api-authentication-manual/add-users.png
[18]: ./media/mobile-engagement-api-authentication-manual/add-role.png
[19]: ./media/mobile-engagement-api-authentication-manual/select-role.png
[20]: ./media/mobile-engagement-api-authentication-manual/add-user-select.png
[21]: ./media/mobile-engagement-api-authentication-manual/add-access-final.png



