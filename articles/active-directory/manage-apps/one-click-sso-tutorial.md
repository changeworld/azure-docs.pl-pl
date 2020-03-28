---
title: Konfiguracja aplikacji usługi Azure Marketplace za pomocą jednego kliknięcia i logowania jednokrotnego | Dokumenty firmy Microsoft
description: Kroki konfiguracji jedno kliknięcia jedno kliknięcia dla aplikacji z portalu Azure Marketplace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: e0416991-4b5d-4b18-89bb-91b6070ed3ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a83d27af4fd783b95c53ef3a9169cb72bfc29d34
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67872428"
---
# <a name="one-click-app-configuration-of-single-sign-on"></a>Konfiguracja logowania jednokrotnego za pomocą jednego kliknięcia

 W tym samouczku dowiesz się, jak wykonać konfigurację logowania jedno kliknięcia(SSO) dla aplikacji obsługujących saml, usługi Azure Active Directory (Azure AD) z portalu Azure Marketplace.

## <a name="introduction-to-one-click-sso"></a>Wprowadzenie do jedno kliknięcia jedno kliknięcia

Funkcja logowania jedno kliknięciem jest przeznaczona do konfigurowania logowania jednokrotnego dla aplikacji portalu Azure Marketplace obsługujących protokół SAML. Na stronie konfiguracji sytuacyjnego usługi Azure AD ta opcja umożliwia automatyczne konfigurowanie metadanych usługi Azure AD po stronie aplikacji. W ten sposób można szybko skonfigurować sytę przy minimalnym wysiłku ręcznym.

## <a name="advantages-of-one-click-sso"></a>Zalety jedno kliknięcia jedno kliknięcia

- Szybka konfiguracja aplikacji usługi Azure Marketplace, które wymagają ręcznej konfiguracji po stronie aplikacji.
- Wydajniejsza i dokładniejsza konfiguracja SSO.
- Do konfiguracji nie jest wymagana żadna komunikacja z partnerami ani wsparcie. Aplikacja zapewnia interfejs użytkownika dla konfiguracji SAML.

## <a name="prerequisites"></a>Wymagania wstępne

- Aktywna subskrypcja aplikacji do skonfigurowania za pomocą sytego zgłoszenia. Potrzebne są również poświadczenia administratora.
- **Rozszerzenie Moje aplikacje bezpieczne logowanie** firmy Microsoft zainstalowane w przeglądarce. Aby uzyskać więcej informacji, zobacz [Uzyskiwanie dostępu do aplikacji i korzystanie z nich w portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="one-click-sso-configuration-steps"></a>Kroki konfiguracji jednokrotnego kliknięcia

1. Dodaj aplikację z portalu Azure Marketplace.

2. Wybierz **opcję Logowanie jednokrotne**.

3. Wybierz **pozycję Włącz logowanie jednokrotne**.

4. Wypełnij obowiązkowe wartości konfiguracji w sekcji **Podstawowa konfiguracja SAML.**

    > [!NOTE]
    > Jeśli aplikacja ma niestandardowe oświadczenia, które należy skonfigurować, obsłużyć je przed wykonaniem jedno kliknięcia jednokrotnego kliknięcia.

5. Jeśli funkcja jedno kliknięcia jest dostępna dla aplikacji usługi Azure Marketplace, zobaczysz następujący ekran. Może być trzeba zainstalować **rozszerzenie przeglądarki My Apps Secure Sign-in,** wybierając pozycję Zainstaluj **rozszerzenie**.

   ![Instalowanie rozszerzenia przeglądarki Moje aplikacje Bezpieczne logowanie](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. Po dodaniu rozszerzenia do przeglądarki wybierz pozycję **Nazwa aplikacji \<\>instalacyjnej**. Po przekierowaniu do portalu administracyjnego aplikacji zaloguj się jako administrator.

   ![Nazwa aplikacji instalacyjnej](./media/one-click-sso-tutorial/setup-sso.png)

7. Rozszerzenie przeglądarki automatycznie konfiguruje wpis SSO w aplikacji. Potwierdź, wybierając **tak**.

   ![Zapisywanie automatycznie wypełnianych danych](./media/one-click-sso-tutorial/save-autopopulate.png)

   > [!NOTE]
   > Jeśli konfiguracja SSO dla aplikacji wymaga dodatkowych kroków, wykonując monity, aby wykonać kroki.

8. Po zakończeniu konfiguracji wybierz przycisk **OK,** aby zapisać zmiany.

   ![Zapisywanie automatycznie wypełnionych danych](./media/one-click-sso-tutorial/save-data.png)

9. Zostanie wyświetlone okno potwierdzenia informujące o pomyślnym skonfigurowanie ustawień logowania przyuśtym.

   ![SSO skonfigurowane](./media/one-click-sso-tutorial/sso-configured.png)

10. Po pomyślnym zakończeniu konfiguracji użytkownik jest wylogowywane z aplikacji i zwracany do witryny Azure portal.

11. Można wybrać **test,** aby przetestować logowanie jednokrotne.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
* [Co to jest rozszerzenie przeglądarki My Apps Secure Sign-in?](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)
 
