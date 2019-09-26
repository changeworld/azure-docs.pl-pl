---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: include
ms.date: 09/24/2019
ms.author: dkshir
ms.custom: include file
ms.openlocfilehash: 1fc30ea5aa843329b6227bfa564b3d10e2273cd7
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310496"
---
>[!NOTE]
>Ta sekcja zawiera instrukcje dotyczące [nowej rejestracji aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app). Jeśli nadal masz starszą rejestrację aplikacji natywnych, możesz jej użyć, o ile jest obsługiwana. Ponadto, jeśli z jakiegoś powodu nowy sposób rejestracji aplikacji nie działa w konfiguracji, możesz spróbować utworzyć starszą natywną aplikację usługi AAD. Aby uzyskać więcej informacji, zapoznaj [się z artykułem rejestrowanie aplikacji Digital bliźniaczych reprezentacji systemu Azure Azure Active Directory za pomocą starszej wersji](../articles/digital-twins/how-to-use-legacy-aad.md) . 

1. W [Azure Portal](https://portal.azure.com)Otwórz **Azure Active Directory** z okienka po lewej stronie, a następnie otwórz okienko **rejestracje aplikacji** . Wybierz przycisk **Nowa rejestracja** .

    [![Zarejestrowana aplikacja](./media/digital-twins-permissions/aad-app-register.png)](./media/digital-twins-permissions/aad-app-register.png#lightbox)

1. Wprowadź przyjazną nazwę tej rejestracji aplikacji w polu **Nazwa**. W sekcji **Identyfikator URI przekierowania (opcjonalnie)** wybierz pozycję **Klient publiczny (Mobile & Desktop)** na liście rozwijanej po lewej stronie, a następnie `https://microsoft.com` wprowadź tekst w polu tekstowym po prawej stronie. Wybierz pozycję **Zarejestruj**.

    [![Utwórz okienko](./media/digital-twins-permissions/aad-app-reg-create.png)](./media/digital-twins-permissions/aad-app-reg-create.png#lightbox)

1. Aby upewnić się, że [aplikacja jest zarejestrowana jako *aplikacja natywna*](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration), Otwórz okienko **uwierzytelnianie** dla rejestracji aplikacji i przewiń w dół w tym okienku. W sekcji **domyślny typ klienta** wybierz pozycję **tak** dla **aplikacji Traktuj jako klienta publicznego**. 

    [![Domyślny natywny](./media/digital-twins-permissions/aad-app-default-native.png)](./media/digital-twins-permissions/aad-app-default-native.png#lightbox)

1.  Otwórz okienko **Przegląd** zarejestrowanej aplikacji i skopiuj wartości następujących jednostek do pliku tymczasowego. Te wartości będą używane do konfigurowania przykładowej aplikacji w poniższych sekcjach.

    - **Identyfikator aplikacji (klienta)**
    - **Identyfikator katalogu (dzierżawcy)**

    [![Identyfikator aplikacji Azure Active Directory](./media/digital-twins-permissions/aad-app-reg-app-id.png)](./media/digital-twins-permissions/aad-app-reg-app-id.png#lightbox)

1. Otwórz okienko **uprawnień interfejsu API** dla rejestracji aplikacji. Wybierz przycisk **Dodaj uprawnienie** . W okienku **uprawnienia żądania interfejsu API** wybierz pozycję **interfejsy API Moja organizacja używa** karty, a następnie wyszukaj:
    
    1. **Usługa Azure Digital bliźniaczych reprezentacji**. Wybierz interfejs API **Digital bliźniaczych reprezentacji platformy Azure** .

        [![Interfejs API wyszukiwania lub usługa Azure Digital bliźniaczych reprezentacji](./media/digital-twins-permissions/aad-aap-search-api-dt.png)](./media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. Możesz też wyszukać pozycję **Azure Smart Spaces**. Wybierz interfejs API **usługi Azure Smart Spaces** .

        [![Interfejs API wyszukiwania w usłudze Azure Smart Spaces](./media/digital-twins-permissions/aad-app-search-api.png)](./media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!NOTE]
    > Dokładna nazwa, która będzie wyświetlana podczas przeszukiwania, może się różnić w zależności od dzierżawy platformy Azure, do której należysz.

1. Wybrany interfejs API jest wyświetlany jako **usługa Azure Digital bliźniaczych reprezentacji** w tym samym okienku **uprawnień interfejsu API żądania** . Wybierz listę rozwijaną **Odczyt (1)** , a następnie wybierz pole wyboru **Odczyt. zapis** . Wybierz przycisk **Dodaj uprawnienia** .

    [![Dodawanie uprawnień do interfejsu API](./media/digital-twins-permissions/aad-app-req-permissions.png)](./media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. W zależności od ustawień organizacji może być konieczne wykonanie dodatkowych czynności w celu udzielenia administratorowi dostępu do tego interfejsu API. Aby uzyskać więcej informacji, skontaktuj się z administratorem. Po zatwierdzeniu dostępu administratora kolumna **wymagana zgoda administratora** w okienku **uprawnienia interfejsu API** będzie wyglądać podobnie do poniższego dla interfejsów API:

    [![Dodawanie uprawnień do interfejsu API](./media/digital-twins-permissions/aad-app-admin-consent.png)](./media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)

