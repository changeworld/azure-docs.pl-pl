---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: digital-twins
author: alinamstanciu
ms.service: digital-twins
ms.topic: include
ms.date: 06/26/2019
ms.author: alinast
ms.custom: include file
ms.openlocfilehash: f03ee57867185eac946be5b596477bf942643587
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67459164"
---
>[!NOTE]
>Należy pamiętać, wkrótce dostępny starszych rejestracji usługi AAD aplikacji jest używany w tym miejscu. W tej sekcji zostanie zaktualizowana po Twins cyfrowych platformy Azure jest w pełni zintegrowana z [Rejestracja nowej aplikacji usługi AAD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app). W międzyczasie możesz eksperymentować Rejestracja nowej aplikacji usługi AAD. Należy zauważyć, że będą musieli używać [klientem publicznym (mobile & destkop)](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types#mobile-and-native-apps) dla *identyfikator URI przekierowania*. 

1. W witrynie [Azure Portal](https://portal.azure.com) otwórz usługę **Azure Active Directory** w okienku po lewej stronie, a następnie otwórz okienko **Właściwości**. Skopiuj wartość **identyfikatora katalogu** do pliku tymczasowego. Użyjesz tej wartości, aby skonfigurować aplikację przykładową w następnej sekcji.

    ![Identyfikator katalogu usługi Azure Active Directory](./media/digital-twins-permissions/aad-app-reg-tenant.png)

1. W [witryny Azure portal](https://portal.azure.com), otwórz **usługi Azure Active Directory** z okienka po lewej stronie, a następnie otwórz **rejestracje aplikacji (starsza wersja)** okienka. Wybierz **rejestrowanie nowej aplikacji** przycisku.

1. Wprowadź przyjazną nazwę tej rejestracji aplikacji w polu **Nazwa**. W polu **Typ aplikacji** wybierz opcję **Natywna** a w polu **Identyfikator URI przekierowania** opcję `https://microsoft.com`. Wybierz pozycję **Utwórz**.

    ![Okienko tworzenia](./media/digital-twins-permissions/aad-app-reg-create.png)

1.  Otwórz zarejestrowaną aplikację, a następnie skopiuj wartość pola **Identyfikator aplikacji** do pliku tymczasowego. Ta wartość identyfikuje aplikację usługi Azure Active Directory. Identyfikatora aplikacji użyjesz do skonfigurowania aplikacji przykładowej w poniższych sekcjach.

    ![Identyfikator aplikacji usługi Azure Active Directory](./media/digital-twins-permissions/aad-app-reg-app-id.png)

1. Otwórz okienko rejestracji Twojej aplikacji. Wybierz pozycję **Ustawienia** > **Wymagane uprawnienia**, a następnie:

   a. Wybierz przycisk **Dodaj** w lewym górnym rogu, aby otworzyć okienko **Dodaj dostęp do interfejsu API**.

   b. Wybierz przycisk **Wybierz interfejs API** i wyszukaj usługę **Azure Digital Twins**. Jeśli wyszukiwanie nie znajdzie interfejsu API, zamiast tego wyszukaj frazę **Azure Smart Spaces**.

   c. Wybierz opcję **Azure Digital Twins (Azure Smart Spaces Service)** i wybierz pozycję **Wybierz**.

   d. Wybierz pozycję **Wybierz uprawnienia**. Zaznacz pole wyboru uprawnień delegowanych **Dostęp do odczytu/zapisu** i wybierz przycisk **Wybierz**.

   e. Wybierz przycisk **Gotowe** w okienku **Dodaj dostęp do interfejsu API**.

   f. W okienku **Wymagane uprawnienia** wybierz przycisk **Udziel uprawnień**, a następnie zaakceptuj wyświetlone potwierdzenie. Jeśli dla tego interfejsu API nie zostało udzielone uprawnienie, skontaktuj się z administratorem.

      ![Okienko Wymagane uprawnienia](./media/digital-twins-permissions/aad-app-req-permissions.png)

 