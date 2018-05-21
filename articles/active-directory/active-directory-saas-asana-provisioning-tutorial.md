---
title: 'Samouczek: Konfigurowanie Asana dla użytkownika automatycznego inicjowania obsługi administracyjnej z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory, aby automatycznie zapewnianie i usuwanie kont użytkowników do Asana.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: sakula
ms.assetid: 0b38ee73-168b-42cb-bd8b-9c5e5126d648
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser
ms.reviewer: asmalser
ms.openlocfilehash: c7e33a46940c9bf0cda5e48c07d42db9f0c7cd96
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-configure-asana-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Asana dla użytkownika automatycznego inicjowania obsługi administracyjnej.

Celem tego samouczka jest opisano czynności, które należy wykonać w Asana i Azure Active Directory (Azure AD), aby automatycznie zapewnianie i usuwanie kont użytkowników z usługi Azure AD Asana.

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że już następujące elementy:

*   Dzierżawa usługi Azure AD
*   Dzierżawę Asana z [Enterprise](https://www.asana.com/pricing) plan lub lepiej jest włączone 
*   Konto użytkownika z uprawnieniami administratora w Asana 

> [!NOTE] 
> Zależy od usługi Azure AD inicjowania obsługi administracyjnej integracji [Asana API](https://app.asana.com/api/1.0/scim/Users), które są dostępne dla Asana.

## <a name="assign-users-to-asana"></a>Przypisywanie użytkowników do Asana

Usługi Azure AD używa pojęcie o nazwie "przypisania" w celu określenia, którzy użytkownicy powinien otrzymać dostęp do wybranej aplikacji. W kontekście użytkownika automatyczne Inicjowanie obsługi konta użytkowników przypisane do aplikacji w usłudze Azure AD są synchronizowane. 

Aby skonfigurować i włączyć usługę inicjowania obsługi administracyjnej, należy zdecydować, użytkowników w usłudze Azure AD, którzy potrzebują dostępu do aplikacji Asana. Następnie można przypisać tych użytkowników do aplikacji Asana, postępując zgodnie z instrukcjami poniżej:

[Przypisz użytkownika do aplikacji w przedsiębiorstwie](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-asana"></a>Ważne porady dotyczące przypisywania użytkowników do Asana

Firma Microsoft zaleca, aby przypisać jedną usługę Azure AD użytkownikowi Asana do testowania konfiguracji inicjowania obsługi administracyjnej. Później można przypisać dodatkowych użytkowników.

## <a name="configure-user-provisioning-to-asana"></a>Konfigurowanie inicjowania obsługi administracyjnej Asana użytkownika 

Ta sekcja przeprowadzi Cię przez usługę Azure AD nawiązywania połączenia z kontem użytkownika Asana inicjowania obsługi interfejsu API. Możesz również skonfigurować usługę inicjowania obsługi administracyjnej do tworzenia, aktualizacji i wyłączenie konta użytkowników przypisane w oparciu o przypisania użytkownika w usłudze Azure AD Asana.

> [!TIP]
> Aby włączyć na języku SAML rejestracji jednokrotnej dla Asana, postępuj zgodnie z instrukcjami podanymi w [portalu Azure](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej, mimo że te dwie funkcje uzupełniają.

### <a name="to-configure-automatic-user-account-provisioning-to-asana-in-azure-ad"></a>Aby skonfigurować użytkownika automatyczne Inicjowanie obsługi konta do Asana w usłudze Azure AD

1. W [portalu Azure](https://portal.azure.com), przejdź do **usługi Azure Active Directory** > **aplikacje przedsiębiorstwa** > **wszystkie aplikacje** sekcji.

2. Jeśli skonfigurowano już Asana dla logowania jednokrotnego, wyszukiwanie wystąpienia programu Asana przy użyciu pola wyszukiwania. W przeciwnym razie wybierz **Dodaj** i wyszukaj **Asana** w galerii aplikacji. Wybierz **Asana** z wyników wyszukiwania i dodaj go do listy aplikacji.

3. Wybierz wystąpienia programu Asana, a następnie wybierz **inicjowania obsługi administracyjnej** kartę.

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Asana inicjowania obsługi administracyjnej.](./media/active-directory-saas-asana-provisioning-tutorial/asanaazureprovisioning.png)

5. W obszarze **poświadczeń administratora** sekcji, wykonaj te instrukcje do wygenerowania tokenu i wprowadź go w **klucz tajny tokenu**:

    a. Zaloguj się do [Asana](https://app.asana.com) przy użyciu konta administratora.

    b. Wybierz zdjęcia profilu, z górnym pasku, a następnie wybierz bieżące ustawienia nazwę organizacji.

    c. Przejdź do **kont usług** kartę.

    d. Wybierz **Dodaj konto usługi**.

    e. Aktualizacja **nazwa** i **o** i zdjęcia profilu, zgodnie z potrzebami. Skopiuj token w **tokenu**i zaznacz je w **Zapisz zmiany**.

6. W portalu Azure wybierz **Testuj połączenie** aby upewnić się, czy aplikacja Asana połączenie usługi Azure AD. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto Asana ma uprawnienia administratora i spróbuj **Testuj połączenie** krok ponownie.

7. Wprowadź adres e-mail osoby lub grupy, które chcesz otrzymywać inicjowania obsługi administracyjnej powiadomienia o błędach w **wiadomość E-mail z powiadomieniem**. Zaznacz pole wyboru poniżej.

8. Wybierz pozycję **Zapisz**. 

9. W obszarze **mapowania** zaznacz **synchronizacji Azure Active Directory użytkownikom Asana**.

10. W **mapowań atrybutów** Przejrzyj atrybuty użytkownika mają być synchronizowane z usługi Azure AD do Asana. Atrybuty wybrany jako **pasujące** właściwości są używane do dopasowania kont użytkowników w Asana dla operacji update. Wybierz **zapisać** aby zatwierdzić zmiany. Aby uzyskać więcej informacji, zobacz [mapowań atrybutów użytkownika należy dostosować](active-directory-saas-customizing-attribute-mappings.md).

11. Aby włączyć usługi Azure AD, inicjowania obsługi usługi dla Asana, w **ustawienia** Zmień **stan inicjowania obsługi administracyjnej** do **na**.

12. Wybierz pozycję **Zapisz**. 

Po rozpoczęciu początkowej synchronizacji dla wszystkich użytkowników przypisanych do Asana w **użytkowników** sekcji. Synchronizacji początkowej zajmuje więcej czasu wykonywania niż kolejne synchronizacje, występujące co około 40 minut tak długo, jak usługa jest uruchomiona. Użyj **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi administracyjnej Dzienniki aktywności. Dzienniki inspekcji opisują wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej na Asana aplikacji.

Aby uzyskać więcej informacji na temat usługi Azure AD, inicjowanie obsługi dzienników do odczytu, zobacz [raport dotyczący użytkownika automatyczne Inicjowanie obsługi konta](active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowizacja konta użytkowników dla aplikacji przedsiębiorstwa](active-directory-enterprise-apps-manage-provisioning.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie rejestracji jednokrotnej](active-directory-saas-asana-tutorial.md)
