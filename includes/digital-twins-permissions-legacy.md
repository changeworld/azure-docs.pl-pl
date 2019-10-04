---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 08/16/2019
ms.custom: include file
ms.openlocfilehash: 690ff402c632bfc28f1a9a218677e9772f5a3200
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949940"
---
1. W witrynie [Azure Portal](https://portal.azure.com) otwórz usługę **Azure Active Directory** w okienku po lewej stronie, a następnie otwórz okienko **Właściwości**. Skopiuj wartość **identyfikatora katalogu** do pliku tymczasowego. Użyjesz tej wartości, aby skonfigurować aplikację przykładową w następnej sekcji.

    [![Azure Active Directory Directory ID](./media/digital-twins-permissions-legacy/aad-app-reg-tenant.png)](./media/digital-twins-permissions-legacy/aad-app-reg-tenant.png#lightbox)

1. W [Azure Portal](https://portal.azure.com)Otwórz **Azure Active Directory** z okienka po lewej stronie, a następnie otwórz okienko **rejestracje aplikacji (starsza wersja)** . Wybierz przycisk **rejestracja nowej aplikacji** .

1. Wprowadź przyjazną nazwę tej rejestracji aplikacji w polu **Nazwa**. W polu **Typ aplikacji** wybierz opcję **Natywna** a w polu **Identyfikator URI przekierowania** opcję `https://microsoft.com`. Wybierz pozycję **Utwórz**.

    [@no__t — okienko 1Create](./media/digital-twins-permissions-legacy/aad-app-reg-create.png)](./media/digital-twins-permissions-legacy/aad-app-reg-create.png#lightbox)

1.  Otwórz zarejestrowaną aplikację, a następnie skopiuj wartość pola **Identyfikator aplikacji** do pliku tymczasowego. Ta wartość identyfikuje aplikację usługi Azure Active Directory. Identyfikatora aplikacji użyjesz do skonfigurowania aplikacji przykładowej w poniższych sekcjach.

    [@no__t — identyfikator aplikacji Active Directory 1Azure](./media/digital-twins-permissions-legacy/aad-app-reg-app-id.png)](./media/digital-twins-permissions-legacy/aad-app-reg-app-id.png#lightbox)

1. Otwórz okienko rejestracji Twojej aplikacji. Wybierz pozycję **Ustawienia** > **Wymagane uprawnienia**, a następnie:

   a. Wybierz przycisk **Dodaj** w lewym górnym rogu, aby otworzyć okienko **Dodaj dostęp do interfejsu API**.

   b. Wybierz przycisk **Wybierz interfejs API** i wyszukaj usługę **Azure Digital Twins**. Jeśli wyszukiwanie nie znajdzie interfejsu API, zamiast tego wyszukaj frazę **Azure Smart Spaces**.

   d. Wybierz opcję **Azure Digital Twins (Azure Smart Spaces Service)** i wybierz pozycję **Wybierz**.

   d. Wybierz pozycję **Wybierz uprawnienia**. Zaznacz pole wyboru uprawnień delegowanych **Dostęp do odczytu/zapisu** i wybierz przycisk **Wybierz**.

   e. Wybierz przycisk **Gotowe** w okienku **Dodaj dostęp do interfejsu API**.

   f. W okienku **wymagane uprawnienia** wybierz przycisk **Udziel uprawnień** i Zaakceptuj wyświetlone potwierdzenie. Jeśli uprawnienie nie zostanie przyznane dla tego interfejsu API, skontaktuj się z administratorem.

      [okienko uprawnień @no__t 1Required](./media/digital-twins-permissions-legacy/aad-app-req-permissions.png)](./media/digital-twins-permissions-legacy/aad-app-req-permissions.png#lightbox)

 
