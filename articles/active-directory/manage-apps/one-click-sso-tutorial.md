---
title: Konfigurowanie jeden kliknij Usługa rejestracji Jednokrotnej do aplikacji z galerii aplikacji Azure AD | Dokumentacja firmy Microsoft
description: Kroki, aby skonfigurować jeden kliknij logowania jednokrotnego do aplikacji z galerii aplikacji Azure AD.
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
ms.openlocfilehash: 358240823da469551e254356fc0613bea20d78c5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057848"
---
# <a name="one-click-sso-feature-for-azure-ad-gallery-applications"></a>Jedna z funkcji kliknij przycisk logowania jednokrotnego do aplikacji galerii usługi Azure AD

 W tym samouczku dowiesz się, jak wykonać jeden kliknij logowania jednokrotnego dla wszystkich aplikacji SAML, które zapewniają interfejs użytkownika konfiguracji logowania jednokrotnego.

## <a name="introduction-to-one-click-sso"></a>Wprowadzenie do jednego kliknięcia logowania jednokrotnego

Aby skonfigurować logowanie jednokrotne dla aplikacji galerii usługi Azure AD, które obsługują protokół SAML wprowadzono jedną funkcję kliknij logowania jednokrotnego. Na stronie konfiguracji logowania jednokrotnego usługi Azure AD udostępniliśmy tę opcję, aby umożliwić klientom do automatycznego konfigurowania metadanych usługi Azure AD na stronie aplikacji. Celem jest, aby ułatwić klientom Konfigurowanie logowania jednokrotnego szybko w prosty sposób ręcznego. 

## <a name="advantages-of-the-one-click-sso"></a>Korzyści wynikające z nich kliknij przycisk logowania jednokrotnego

- Szybkie konfiguracji logowania jednokrotnego aplikacji galerii, klienci muszą wykonać instalację ręczną na stronie aplikacji.
- Bardziej wydajne i dokładne sposób konfiguracji.
- Brak komunikacji partnera lub pomocy technicznej, wymagany do instalacji, ponieważ aplikacja udostępnia interfejs wielokrotnego użytku plik konfiguracji SAML.

## <a name="prerequisites"></a>Wymagania wstępne

- Aktywna subskrypcja aplikacji przy użyciu poświadczeń administracyjnych, które chcesz skonfigurować przy użyciu logowania jednokrotnego OneClick.
- **Moje rozszerzenie aplikacji bezpiecznego logowania w przeglądarce** firmy Microsoft w przeglądarce. Jeśli chcesz dowiedzieć się więcej o tym rozszerzeniu, zapoznaj się z tym [łącze](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="one-click-sso-feature-step-by-step-details"></a>Szczegółowe informacje krok po kroku są wyposażone w jeden kliknij logowania jednokrotnego

1. Dodaj aplikację z galerii aplikacji usługi Azure AD.

2. Polecenie logowania jednokrotnego.

3. Kliknij pozycję Włącz logowanie jednokrotne.

4. Wypełnienie wartości wymagana konfiguracja, w sekcji podstawową konfigurację protokołu SAML.

    > [!NOTE] 
    > Jeśli aplikacja wymaga konfiguracji oświadczenia niestandardowe, należy skonfigurować je przed wykonaniem OneClick logowania jednokrotnego.

5. Jeśli funkcja jeden kliknij logowania jednokrotnego została zaimplementowana dla aplikacji z galerii, zostanie wyświetlony następujący ekran. Jeśli **rozszerzenia przeglądarki do bezpiecznego Moje aplikacje logowania** jest nie jest jeszcze zainstalowana, należy kliknąć **zainstalować rozszerzenie** opcji.

    ![Instalowanie rozszerzenia przeglądarki do bezpiecznego Moje aplikacje logowania](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. Po dodaniu rozszerzenia do przeglądarki, kliknij pozycję **Nazwa aplikacji Instalatora** której nastąpi przekierowanie do portalu administracyjnego usługi aplikacji. Należy zalogować się jako administrator dostępu do aplikacji.

    ![Konfigurowanie nazwy aplikacji](./media/one-click-sso-tutorial/setup-sso.png)

7. Rozszerzenie przeglądarki teraz automatycznie konfiguruje aplikację dla Ciebie. Najpierw sprawdza, czy potwierdzenie, jeśli chcesz kontynuować. Kliknij przycisk **Yes** (Tak).

    ![Zapisywanie automatycznie wypełnione danych](./media/one-click-sso-tutorial/save-autopopulate.png)

    > [!NOTE]
    > Jeśli dowolnej aplikacji wymaga dodatkowych nagivation lub kroki, powinny być widoczne odpowiednie komunikaty z prośbą do wykonania tych kroków. 

8. Po zakończeniu konfiguracji kliknij **Ok** Aby zapisać zmiany.

    ![Zapisz dane wypełniana automatycznie](./media/one-click-sso-tutorial/save-data.png)

9. Potwierdzenie pomyślnego komunikat podręczny jest wyświetlany i ustawień logowania jednokrotnego zostaną pomyślnie skonfigurowane. Następnie można przetestować aplikację.

    ![Skonfigurowane logowanie Jednokrotne](./media/one-click-sso-tutorial/sso-configured.png)

10. Po pomyślnym zakończeniu konfiguracji, aplikacji zostaną wylogowani i nastąpi powrót do witryny Azure portal.

11. Można kliknąć przycisk Testuj, aby przetestować logowanie jednokrotne.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
* [Co to jest Mój zabezpieczyć aplikacje logowania rozszerzenia przeglądarki](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)
 