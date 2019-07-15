---
title: Jednym kliknięciem, pojedynczy konfiguracji logowania jednokrotnego (SSO) aplikacji portalu Azure Marketplace | Dokumentacja firmy Microsoft
description: Procedura konfiguracji jednym kliknięciem logowania jednokrotnego dla aplikacji w portalu Azure Marketplace.
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
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "67872428"
---
# <a name="one-click-app-configuration-of-single-sign-on"></a>Konfiguracja aplikacji jednym kliknięciem logowania jednokrotnego

 W tym samouczku dowiesz się, jak przeprowadzić jednym kliknięciem, jednej konfiguracji logowania jednokrotnego (SSO) obsługę protokołu SAML-, aplikacje usługi Azure Active Directory (Azure AD) w portalu Azure Marketplace.

## <a name="introduction-to-one-click-sso"></a>Wprowadzenie do logowania jednokrotnego w jednym kliknięciem

Funkcja logowania jednokrotnego jednym kliknięciem została zaprojektowana do skonfigurowania logowania jednokrotnego dla aplikacji w portalu Azure Marketplace, które obsługują protokół SAML. Na stronie konfiguracji logowania jednokrotnego usługi Azure AD ta opcja umożliwia automatycznie konfiguruje metadanych usługi Azure AD na stronie aplikacji. W ten sposób możesz szybko skonfigurować logowanie Jednokrotne przy minimalnym nakładzie pracy ręcznej.

## <a name="advantages-of-one-click-sso"></a>Korzyści wynikające z rejestracji Jednokrotnej w jednym kliknięciem

- Szybkie konfiguracji logowania jednokrotnego aplikacji w portalu Azure Marketplace, które wymagają ręcznej instalacji na stronie aplikacji.
- Konfiguracja logowania jednokrotnego bardziej wydajne i dokładne.
- Brak komunikacji partnera i pomoc niezbędne do instalacji. Aplikacja udostępnia interfejs wielokrotnego użytku plik konfiguracji SAML.

## <a name="prerequisites"></a>Wymagania wstępne

- Aktywna subskrypcja aplikacji można skonfigurować przy użyciu logowania jednokrotnego. Musisz również mieć poświadczenia administratora.
- **Rozszerzenie Secure Moje aplikacje logowania** firmy Microsoft w przeglądarce. Aby uzyskać więcej informacji, zobacz [dostępu i użycia aplikacji w portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="one-click-sso-configuration-steps"></a>Kroki konfiguracji logowania jednokrotnego w jednym kliknięciem

1. Dodaj aplikację z portalu Azure Marketplace.

2. Wybierz **logowanie jednokrotne**.

3. Wybierz **włączyć rejestrację jednokrotną**.

4. Wypełnianie wartości wymagana konfiguracja w **podstawową konfigurację protokołu SAML** sekcji.

    > [!NOTE]
    > Jeśli aplikacja ma oświadczenia niestandardowe, które należy skonfigurować, należy je obsłużyć, przed wykonaniem instrukcji w jednym kliknięciem logowania jednokrotnego.

5. Jeśli funkcja logowania jednokrotnego jednym kliknięciem jest dostępna dla aplikacji portalu Azure Marketplace, zostanie wyświetlony następujący ekran. Może być konieczne zainstalowanie **rozszerzenia przeglądarki do bezpiecznego Moje aplikacje logowania** , wybierając **zainstalować rozszerzenie**.

   ![Instalowanie rozszerzenia przeglądarki do bezpiecznego Moje aplikacje logowania](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. Po dodaniu rozszerzenia do przeglądarki, zaznacz **instalacji \<Nazwa aplikacji\>** . Po użytkownik jest przekierowany do portalu administracyjnego usługi aplikacji, zaloguj się jako administrator.

   ![Konfigurowanie nazwy aplikacji](./media/one-click-sso-tutorial/setup-sso.png)

7. Rozszerzenie przeglądarki automatycznie konfiguruje logowanie Jednokrotne w aplikacji. Upewnij się, wybierając **tak**.

   ![Zapisywanie danych wypełniane automatycznie](./media/one-click-sso-tutorial/save-autopopulate.png)

   > [!NOTE]
   > Jeśli Konfiguracja logowania jednokrotnego dla aplikacji wymaga wykonania dodatkowych kroków, zgodnie z monitami, aby wykonać kroki.

8. Po zakończeniu konfiguracji wybierz **OK** Aby zapisać zmiany.

   ![Zapisz dane wypełniane automatycznie](./media/one-click-sso-tutorial/save-data.png)

9. Wyświetla okno potwierdzenie z informacją, że pomyślnie skonfigurowano ustawienia logowania jednokrotnego.

   ![Skonfigurowane logowanie Jednokrotne](./media/one-click-sso-tutorial/sso-configured.png)

10. Po pomyślnym konfiguracji jest wylogowanie z aplikacji i powrót do witryny Azure portal.

11. Możesz wybrać **Test** do przetestowania logowania jednokrotnego.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
* [Co to jest rozszerzenie przeglądarki Moje zabezpieczenia aplikacji logowania?](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)
 
