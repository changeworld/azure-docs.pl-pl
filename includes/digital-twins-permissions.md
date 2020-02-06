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
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77029314"
---
>[!NOTE]
>Ta sekcja zawiera instrukcje dotyczące [rejestracji aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app).

1. W [Azure Portal](https://portal.azure.com)Otwórz **Azure Active Directory** z menu rozwijanego po lewej stronie, a następnie otwórz okienko **rejestracje aplikacji** . 

    [![zaznacz okienko Azure Active Directory](./media/digital-twins-permissions/azure-portal-select-aad-pane.png)](./media/digital-twins-permissions/azure-portal-select-aad-pane.png#lightbox)

1. Wybierz przycisk **+ Nowa rejestracja** .

    [![wybrać nowy przycisk rejestracji](./media/digital-twins-permissions/aad-app-register.png)](./media/digital-twins-permissions/aad-app-register.png#lightbox)

1. Wprowadź przyjazną nazwę tej rejestracji aplikacji w polu **Nazwa**. 

    1. W sekcji **Identyfikator URI przekierowania (opcjonalnie)** wprowadź `https://microsoft.com` w polu tekstowym.     

    1. Sprawdź, które konta i dzierżawcy są obsługiwane przez aplikację Azure Active Directory.

    1. Wybierz pozycję **Zarejestruj**.

    [![Utwórz okienko](./media/digital-twins-permissions/aad-app-reg-create.png)](./media/digital-twins-permissions/aad-app-reg-create.png#lightbox)

1. Blok **uwierzytelnianie** określa ważne ustawienia konfiguracji uwierzytelniania. 

    1. Dodaj **identyfikatory URI przekierowania** i skonfiguruj **tokeny dostępu** , wybierając pozycję **+ Dodaj platformę**.

    1. Wybierz pozycję **tak** , aby określić, że aplikacja jest **klientem publicznym**.

    1. Sprawdź, które konta i dzierżawcy są obsługiwane przez aplikację Azure Active Directory.

    [![ustawienia konfiguracji klienta publicznego](./media/digital-twins-permissions/aad-configure-public-client.png)](./media/digital-twins-permissions/aad-configure-public-client.png#lightbox)

1. Po wybraniu odpowiedniej platformy Skonfiguruj **identyfikatory URI przekierowania** i **tokeny dostępu** w panelu po prawej stronie interfejsu użytkownika.

    1. **Identyfikatory URI przekierowania** muszą być zgodne z adresem podanym w żądaniu uwierzytelniania:

        * W przypadku aplikacji hostowanych w lokalnym środowisku programistycznym wybierz pozycję **Klient publiczny (mobile & Desktop)** . Upewnij się, że ustawiono opcję **Klient publiczny** na **wartość tak**.
        * W przypadku aplikacji jednostronicowych hostowanych w Azure App Service wybierz pozycję **Sieć Web**.

    1. Ustal, czy **adres URL wylogowania** jest odpowiedni.

    1. Włącz przepływ niejawnego udzielenia, sprawdzając **tokeny dostępu** lub **tokeny identyfikatorów**.
                
    [![skonfigurować identyfikatory URI przekierowania](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png)](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png#lightbox)

    Kliknij przycisk **Konfiguruj**, a następnie **Zapisz**.

1.  Otwórz okienko **Przegląd** zarejestrowanej aplikacji i skopiuj wartości następujących jednostek do pliku tymczasowego. Te wartości będą używane do konfigurowania przykładowej aplikacji w poniższych sekcjach.

    - **Identyfikator aplikacji (klienta)**
    - **Identyfikator katalogu (dzierżawcy)**

    [Identyfikator aplikacji Azure Active Directory ![](./media/digital-twins-permissions/aad-app-reg-app-id.png)](./media/digital-twins-permissions/aad-app-reg-app-id.png#lightbox)

1. Otwórz okienko **uprawnień interfejsu API** dla rejestracji aplikacji. Wybierz pozycję **+ Dodaj przycisk uprawnienia** . W okienku **uprawnienia żądania interfejsu API** wybierz opcję **interfejsy API Moja organizacja używa** karty, a następnie wyszukaj jedną z następujących opcji:
    
    1. `Azure Digital Twins`. Wybierz interfejs API **Digital bliźniaczych reprezentacji platformy Azure** .

        [Interfejs API wyszukiwania ![lub usługa Azure Digital bliźniaczych reprezentacji](./media/digital-twins-permissions/aad-aap-search-api-dt.png)](./media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. Możesz też wyszukać `Azure Smart Spaces Service`. Wybierz interfejs API **usługi Azure Smart Spaces** .

        [Interfejs API wyszukiwania ![dla usługi Azure Smart Spaces](./media/digital-twins-permissions/aad-app-search-api.png)](./media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!IMPORTANT]
    > Nazwa i identyfikator interfejsu API usługi Azure AD, który będzie wyświetlany, zależy od dzierżawcy:
    > * Przeszukaj `Azure Digital Twins`.
    > * Inne konta Microsoft powinny szukać `Azure Smart Spaces Service`.

1. Jeden z interfejsów API będzie wyświetlany jako **usługa Azure Digital bliźniaczych reprezentacji** w tym samym okienku **uprawnień interfejsu API żądania** . Wybierz opcję **Odczyt Odczytaj** , a następnie zaznacz pole wyboru **Odczyt. zapis** . Wybierz przycisk **Dodaj uprawnienia** .

    [![Dodawanie uprawnień do interfejsu API](./media/digital-twins-permissions/aad-app-req-permissions.png)](./media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. W zależności od ustawień organizacji może być konieczne wykonanie dodatkowych czynności w celu udzielenia administratorowi dostępu do tego interfejsu API. Aby uzyskać więcej informacji, skontaktuj się z administratorem. Po zatwierdzeniu dostępu administratora do kolumny **wymagana jest zgoda administratora** w okienku **uprawnienia interfejsu API** zostaną wyświetlone Twoje uprawnienia. 

    [![zatwierdzenie zgody administratora](./media/digital-twins-permissions/aad-app-admin-consent.png)](./media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)

    Sprawdź, czy zostanie wyświetlona **Usługa Digital bliźniaczych reprezentacji** .
