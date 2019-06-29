---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: include
ms.date: 06/28/2019
ms.author: dkshir
ms.custom: include file
ms.openlocfilehash: 324f41055cf333081f308a3ff533ff7df6b33038
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67479227"
---
>[!NOTE]
>Ta sekcja zawiera instrukcje dotyczące [nowej rejestracji aplikacji w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app). Jeśli nadal masz rejestracji starszych aplikacji natywnej, korzystać z niej tak długo, jak długo jest obsługiwany. Ponadto jeśli zaistnieje nowy sposób posiadanie aplikacji działa w ustawieniach, spróbuj tworzenie starszej wersji natywną aplikację usługi AAD. Odczyt [Zarejestruj swoją aplikację Twins cyfrowych platformy Azure przy użyciu starszej wersji usługi Azure Active Directory](../articles/digital-twins/how-to-use-legacy-aad.md) uzyskać dalsze instrukcje. 

1. W [witryny Azure portal](https://portal.azure.com), otwórz **usługi Azure Active Directory** z okienka po lewej stronie, a następnie otwórz **rejestracje aplikacji** okienka. Wybierz **nowej rejestracji** przycisku.

    ![Zarejestrowana aplikacja](./media/digital-twins-permissions/aad-app-register.png)

1. Wprowadź przyjazną nazwę tej rejestracji aplikacji w polu **Nazwa**. W obszarze **identyfikator URI przekierowania (opcjonalnie)** wybierz pozycję **klientem publicznym (mobilnych i klasycznych)** liście rozwijanej po lewej stronie, a następnie wprowadź `https://microsoft.com` w polu tekstowym po prawej stronie. Wybierz pozycję **Zarejestruj**.

    ![Okienko tworzenia](./media/digital-twins-permissions/aad-app-reg-create.png)

1. Aby upewnić się, że [aplikacja jest zarejestrowana jako *aplikacji natywnej*](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration), otwórz **uwierzytelniania** dla rejestracji aplikacji i przewiń w dół w okienku, w okienku. W **domyślny typ klienta** wybierz pozycję **tak** dla **traktować aplikacji w publicznych klienta**. 

    ![Domyślny natywny](./media/digital-twins-permissions/aad-app-default-native.png)

1.  Otwórz **Przegląd** okienko zarejestrowanych aplikacji i skopiuj wartości następujących jednostek do pliku tymczasowego. Te wartości zostaną wykorzystane, aby skonfigurować aplikację przykładową, w poniższych sekcjach.

    - **Identyfikator aplikacji (klienta)**
    - **Identyfikator katalogu (dzierżawcy)**

    ![Identyfikator aplikacji usługi Azure Active Directory](./media/digital-twins-permissions/aad-app-reg-app-id.png)

1. Otwórz **uprawnienia do interfejsu API** okienka rejestracji aplikacji. Wybierz **Dodaj uprawnienia** przycisku. W **uprawnienia do żądania interfejsu API** okienku wybierz **Moja organizacja korzysta z interfejsów API** kartę, a następnie wyszukaj **Azure Inteligentne spacje**. Wybierz **Azure inteligentnych spacje usługi** interfejsu API.

    ![Interfejs API wyszukiwania](./media/digital-twins-permissions/aad-app-search-api.png)

1. Wybranego interfejsu API jest wyświetlany jako **Twins cyfrowego Azure** w tym samym **uprawnienia do żądania interfejsu API** okienka. Wybierz **odczytu (1)** listę rozwijaną, a następnie wybierz **Read.Write** pole wyboru. Wybierz **Dodaj uprawnienia** przycisku.

    ![Dodaj uprawnienia do interfejsu API](./media/digital-twins-permissions/aad-app-req-permissions.png)

1. W zależności od ustawień organizacji może być konieczne wykonanie dodatkowych czynności, aby udzielić dostępu administratora do tego interfejsu API. Aby uzyskać więcej informacji, skontaktuj się z administratorem. Po zatwierdzeniu dostępu administratora **wymagana zgoda administratora** kolumny w **uprawnienia do interfejsu API** okienku zostaną wyświetlone podobne do następujących dla interfejsów API:

    ![Dodaj uprawnienia do interfejsu API](./media/digital-twins-permissions/aad-app-admin-consent.png)

