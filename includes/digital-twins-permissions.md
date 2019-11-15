---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2019
ms.custom: include file
ms.openlocfilehash: 832c0e6080b82f3c38beaf051669fbdacd37081c
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74101300"
---
>[!NOTE]
>Ta sekcja zawiera instrukcje dotyczące [rejestracji aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app).

1. W [Azure Portal](https://portal.azure.com)Otwórz **Azure Active Directory** z menu rozwijanego po lewej stronie, a następnie otwórz okienko **rejestracje aplikacji** . 

    [![zaznacz okienko Azure Active Directory](./media/digital-twins-permissions/select-aad-pane.png)](./media/digital-twins-permissions/select-aad-pane.png#lightbox)

1. Wybierz przycisk **+ Nowa rejestracja** .

    [![wybrać nowy przycisk rejestracji](./media/digital-twins-permissions/aad-app-register.png)](./media/digital-twins-permissions/aad-app-register.png#lightbox)

1. Wprowadź przyjazną nazwę tej rejestracji aplikacji w polu **Nazwa**. W sekcji **Identyfikator URI przekierowania (opcjonalnie)** wybierz pozycję **Klient publiczny/natywny (Mobile & Desktop)** z menu rozwijanego po lewej stronie, a następnie wprowadź `https://microsoft.com` w polu tekstowym po prawej stronie. Wybierz pozycję **Zarejestruj**.

    [![Utwórz okienko](./media/digital-twins-permissions/aad-app-reg-create.png)](./media/digital-twins-permissions/aad-app-reg-create.png#lightbox)

1. Aby upewnić się, że [aplikacja jest zarejestrowana jako **Klient publiczny**](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration), Otwórz okienko **uwierzytelnianie** dla rejestracji aplikacji i przewiń w dół w tym okienku. W sekcji **domyślny typ klienta** wybierz pozycję **tak** dla **aplikacji Traktuj jako klient publiczny**i naciśnij przycisk **Zapisz**.

    Sprawdź **tokeny dostępu** , aby włączyć ustawienie **Oauth2AllowImplicitFlow** w pliku manifest. JSON.

    [![ustawienia konfiguracji klienta publicznego](./media/digital-twins-permissions/aad-public-client.png)](./media/digital-twins-permissions/aad-public-client.png#lightbox)

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

1. Każdy wybrany interfejs API jest wyświetlany jako **usługa Azure Digital bliźniaczych reprezentacji** w tym samym okienku **uprawnień interfejsu API żądania** . Wybierz opcję **Odczyt Odczytaj** , a następnie zaznacz pole wyboru **Odczyt. zapis** . Wybierz przycisk **Dodaj uprawnienia** .

    [![Dodawanie uprawnień do interfejsu API](./media/digital-twins-permissions/aad-app-req-permissions.png)](./media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. W zależności od ustawień organizacji może być konieczne wykonanie dodatkowych czynności w celu udzielenia administratorowi dostępu do tego interfejsu API. Aby uzyskać więcej informacji, skontaktuj się z administratorem. Po zatwierdzeniu dostępu administratora do kolumny **wymagana jest zgoda administratora** w okienku **uprawnienia interfejsu API** zostaną wyświetlone Twoje uprawnienia. 

    [![zatwierdzenie zgody administratora](./media/digital-twins-permissions/aad-app-admin-consent.png)](./media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)

    Sprawdź, czy zostanie wyświetlona **Usługa Digital bliźniaczych reprezentacji** .
