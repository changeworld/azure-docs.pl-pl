---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 02/03/2020
ms.custom: include file
ms.openlocfilehash: cfe3eb4c0ac1378b7c519b3b34094945612d8508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77029314"
---
>[!NOTE]
>Ta sekcja zawiera instrukcje dotyczące [rejestracji aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app).

1. W [witrynie Azure portal](https://portal.azure.com)otwórz **usługę Azure Active Directory** z menu rozwijanego po lewej stronie, a następnie otwórz okienko **rejestracji aplikacji.** 

    [![Wybieranie okienka usługi Azure Active Directory](./media/digital-twins-permissions/azure-portal-select-aad-pane.png)](./media/digital-twins-permissions/azure-portal-select-aad-pane.png#lightbox)

1. Wybierz przycisk **+ Nowa rejestracja.**

    [![Wybierz przycisk Nowa rejestracja](./media/digital-twins-permissions/aad-app-register.png)](./media/digital-twins-permissions/aad-app-register.png#lightbox)

1. Wprowadź przyjazną nazwę tej rejestracji aplikacji w polu **Nazwa**. 

    1. W sekcji **Przekierowanie identyfikatora URI (opcjonalnie)** wprowadź w `https://microsoft.com` polu tekstowym.     

    1. Sprawdź, które konta i dzierżawy są obsługiwane przez aplikację usługi Azure Active Directory.

    1. Wybierz pozycję **Zarejestruj**.

    [![Okienko tworzenia](./media/digital-twins-permissions/aad-app-reg-create.png)](./media/digital-twins-permissions/aad-app-reg-create.png#lightbox)

1. **Blok Uwierzytelniania** określa ważne ustawienia konfiguracji uwierzytelniania. 

    1. Dodaj **identyfikatory URI przekierowania** i skonfiguruj **tokeny dostępu,** wybierając **pozycję + Dodaj platformę**.

    1. Wybierz **opcję Tak,** aby określić, że aplikacja jest **klientem publicznym**.

    1. Sprawdź, które konta i dzierżawy są obsługiwane przez aplikację usługi Azure Active Directory.

    [![Ustawienie konfiguracji klienta publicznego](./media/digital-twins-permissions/aad-configure-public-client.png)](./media/digital-twins-permissions/aad-configure-public-client.png#lightbox)

1. Po wybraniu odpowiedniej platformy należy skonfigurować **identyfikatory URI przekierowania** i **tokeny dostępu** w panelu bocznym po prawej stronie interfejsu użytkownika.

    1. **Przekierowanie identyfikatorów URI** musi być zgodne z adresem podanym przez żądanie uwierzytelnienia:

        * W przypadku aplikacji hostowanych w lokalnym środowisku programistycznym wybierz pozycję **Klient publiczny (mobilny & komputer)**. Upewnij się, że **klient publiczny** jest ustawiony na **Tak**.
        * W przypadku aplikacji jednostronicowych hostowanych w usłudze Azure App Service wybierz pozycję **Web**.

    1. Określ, czy **adres URL wylogowania** jest odpowiedni.

    1. Włącz niejawny przepływ dotacji, sprawdzając **tokeny programu Access** lub **tokeny identyfikatorów**.
                
    [![Konfigurowanie identyfikatorów URI przekierowania](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png)](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png#lightbox)

    Kliknij **pozycję Konfiguruj**, a następnie **pozycję Zapisz**.

1.  Otwórz **okienko Przegląd** zarejestrowanej aplikacji i skopiuj wartości następujących jednostek do pliku tymczasowego. Te wartości użyją do skonfigurowania przykładowej aplikacji w poniższych sekcjach.

    - **Identyfikator aplikacji (klienta)**
    - **Identyfikator katalogu (dzierżawcy)**

    [![Identyfikator aplikacji usługi Azure Active Directory](./media/digital-twins-permissions/aad-app-reg-app-id.png)](./media/digital-twins-permissions/aad-app-reg-app-id.png#lightbox)

1. Otwórz okienko **uprawnień interfejsu API** dla rejestracji aplikacji. Wybierz **+ Przycisk Dodaj uprawnienie.** W okienku **Uprawnień interfejsu API żądania** wybierz kartę Interfejsy API używane **przez moją organizację,** a następnie wyszukaj jedną z następujących opcji:
    
    1. `Azure Digital Twins`. Wybierz interfejs API **cyfrowych cyfrowych obiektów w wersji Azure.**

        [![Interfejs API wyszukiwania lub cyfrowe obrazy bliźniacze platformy Azure](./media/digital-twins-permissions/aad-aap-search-api-dt.png)](./media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. Alternatywnie, wyszukaj `Azure Smart Spaces Service`. Wybierz interfejs API **usługi Azure Smart Spaces.**

        [![Interfejs API wyszukiwania dla inteligentnych pomieszczeń usługi Azure](./media/digital-twins-permissions/aad-app-search-api.png)](./media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!IMPORTANT]
    > Nazwa interfejsu API usługi Azure AD i identyfikator, który pojawi się zależy od dzierżawy:
    > * Testowanie kont dzierżawy `Azure Digital Twins`i klientów należy wyszukać .
    > * Inne konta Microsoft `Azure Smart Spaces Service`powinny wyszukiwać .

1. Interfejs API będzie wyświetlany jako **usługa Azure Digital Twins** w tym samym okienku **uprawnień interfejsu API żądania** po wybraniu. Zaznacz opcję listy rozwijanej **Odczyt,** a następnie zaznacz pole wyboru **Odczyt.Zapis.** Wybierz przycisk **Dodaj uprawnienia.**

    [![Dodawanie uprawnień interfejsu API](./media/digital-twins-permissions/aad-app-req-permissions.png)](./media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. W zależności od ustawień organizacji może być konieczne podjęcie dodatkowych kroków w celu przyznania administratorowi dostępu do tego interfejsu API. Aby uzyskać więcej informacji, skontaktuj się z administratorem. Po zatwierdzeniu dostępu **administratora** w kolumnie Wymagana zgoda administratora w okienku **uprawnień interfejsu API** zostaną wyświetlone twoje uprawnienia. 

    [![Zatwierdzenie zgody administratora](./media/digital-twins-permissions/aad-app-admin-consent.png)](./media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)

    Sprawdź, czy **usługa Azure Digital Twins** jest wyświetlana.
