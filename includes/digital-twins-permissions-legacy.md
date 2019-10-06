---
title: plik dołączany
description: plik dołączany
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 10/03/2019
ms.custom: include file
ms.openlocfilehash: 7e0396c032a9f3dc26b82648604624446d6ad191
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2019
ms.locfileid: "71978639"
---
## <a name="azure-active-directory-legacy-registration"></a>Azure Active Directory rejestracji starszej wersji

1. W [Azure Portal](https://portal.azure.com)Otwórz **Azure Active Directory** z okienka po lewej stronie, a następnie otwórz okienko **Właściwości** . Skopiuj **Identyfikator katalogu** do pliku tymczasowego. Ta wartość będzie używana do konfigurowania przykładowej aplikacji w następnej sekcji.

    [![Azure Active Directory Directory ID](./media/digital-twins-permissions-legacy/aad-app-reg-tenant.png)](./media/digital-twins-permissions-legacy/aad-app-reg-tenant.png#lightbox)

1. W [Azure Portal](https://portal.azure.com)Otwórz **Azure Active Directory** z okienka po lewej stronie, a następnie otwórz okienko **rejestracje aplikacji (starsza wersja)** . Wybierz przycisk **rejestracja nowej aplikacji** .

1. Nadaj przyjazną nazwę tej rejestracji aplikacji w polu **Nazwa** . Wybierz **Typ aplikacji** jako **natywny**i **przekieruj identyfikator URI** jako `https://microsoft.com`. Wybierz pozycję **Utwórz**.

    [@no__t — okienko 1Create](./media/digital-twins-permissions-legacy/aad-app-reg-create.png)](./media/digital-twins-permissions-legacy/aad-app-reg-create.png#lightbox)

1.  Otwórz zarejestrowaną aplikację i skopiuj wartość pola **Identyfikator aplikacji** do pliku tymczasowego. Ta wartość identyfikuje aplikację Azure Active Directoryową. Identyfikator aplikacji zostanie użyty do skonfigurowania przykładowej aplikacji w poniższych sekcjach.

    [@no__t — identyfikator aplikacji Active Directory 1Azure](./media/digital-twins-permissions-legacy/aad-app-reg-app-id.png)](./media/digital-twins-permissions-legacy/aad-app-reg-app-id.png#lightbox)

1. Otwórz okienko rejestracji aplikacji. Wybierz pozycję **ustawienia** > **wymagane uprawnienia**, a następnie:

   a. Wybierz pozycję **Dodaj** w lewym górnym rogu, aby otworzyć okienko **Dodaj dostęp do interfejsu API** .

   b. Wybierz pozycję **Wybierz interfejs API** i Wyszukaj **usługę Azure Digital bliźniaczych reprezentacji**. Jeśli wyszukiwanie nie lokalizuje interfejsu API, Wyszukaj w zamian funkcję **Azure Smart Spaces** .

   s. Wybierz opcję **Azure Digital bliźniaczych reprezentacji (usługa Azure Smart Spaces)** i wybierz **pozycję Wybierz**.

   Wykres. Wybierz **pozycję Wybierz uprawnienia**. Zaznacz pole wyboru uprawnienia **dostępu do odczytu i zapisu** , a następnie wybierz **pozycję Wybierz**.

   adres. Wybierz pozycję **gotowe** w okienku **Dodaj dostęp do interfejsu API** .

   n. W okienku **wymagane uprawnienia** wybierz przycisk **Udziel uprawnień** i Zaakceptuj wyświetlone potwierdzenie. Jeśli uprawnienie nie zostanie przyznane dla tego interfejsu API, skontaktuj się z administratorem.

      [okienko uprawnień @no__t 1Required](./media/digital-twins-permissions-legacy/aad-app-req-permissions.png)](./media/digital-twins-permissions-legacy/aad-app-req-permissions.png#lightbox)

 
