---
title: 'Samouczek: Konfigurowanie Pingboard dla użytkownika automatycznego inicjowania obsługi administracyjnej z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory, aby automatycznie zapewnianie i usuwanie kont użytkowników do Pingboard.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: sakula
ms.assetid: 0b38ee73-168b-42cb-bd8b-9c5e5126d648
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: asmalser
ms.reviewer: asmalser
ms.openlocfilehash: 6d9562697c8135ba4b92b1bbf304a41af8c426c0
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36220387"
---
# <a name="tutorial-configure-pingboard-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Pingboard dla użytkownika automatycznego inicjowania obsługi administracyjnej.

Celem tego samouczka jest opisano czynności, które należy wykonać, aby włączyć automatyczną aprowizację i anulowanie obsługi kont użytkowników z usługi Azure Active Directory (Azure AD) do Pingboard.

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że już następujące elementy:

*   Dzierżawa usługi Azure AD
*   Dzierżawy Pingboard [konta Pro](https://pingboard.com/pricing) 
*   Konto użytkownika z uprawnieniami administratora w Pingboard 

> [!NOTE] 
> Zależy od usługi Azure AD inicjowania obsługi administracyjnej integracji [Pingboard API](`https://your_domain.pingboard.com/scim/v2`), który jest dostępny do Twojego konta.

## <a name="assign-users-to-pingboard"></a>Przypisywanie użytkowników do Pingboard

Usługi Azure AD używa pojęcie o nazwie "przypisania" w celu określenia, użytkowników, którzy powinien otrzymać dostęp do wybranej aplikacji. W kontekście użytkownika automatyczne Inicjowanie obsługi konta użytkowników przypisane do aplikacji w usłudze Azure AD są synchronizowane. 

Aby skonfigurować i włączyć usługę inicjowania obsługi administracyjnej, należy zdecydować, użytkowników w usłudze Azure AD, którzy potrzebują dostępu do aplikacji Pingboard. Następnie można przypisać tych użytkowników do aplikacji Pingboard, postępując zgodnie z instrukcjami poniżej:

[Przypisz użytkownika do aplikacji w przedsiębiorstwie](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-pingboard"></a>Ważne porady dotyczące przypisywania użytkowników do Pingboard

Firma Microsoft zaleca, aby przypisać jedną usługę Azure AD użytkownikowi Pingboard do testowania konfiguracji inicjowania obsługi administracyjnej. Później można przypisać dodatkowych użytkowników.

## <a name="configure-user-provisioning-to-pingboard"></a>Konfigurowanie inicjowania obsługi administracyjnej Pingboard użytkownika 

Ta sekcja przeprowadzi Cię przez łączenie z kontem użytkownika Pingboard inicjowania obsługi interfejsu API usługi Azure AD. Możesz również skonfigurować usługę inicjowania obsługi administracyjnej do tworzenia, aktualizacji i wyłączenie konta użytkowników przypisane w oparciu o przypisania użytkownika w usłudze Azure AD Pingboard.

> [!TIP]
> Aby włączyć na języku SAML rejestracji jednokrotnej dla Pingboard, postępuj zgodnie z instrukcjami podanymi w [portalu Azure](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej, mimo że te dwie funkcje uzupełniają.

### <a name="to-configure-automatic-user-account-provisioning-to-pingboard-in-azure-ad"></a>Aby skonfigurować użytkownika automatyczne Inicjowanie obsługi konta do Pingboard w usłudze Azure AD

1. W [portalu Azure](https://portal.azure.com), przejdź do **usługi Azure Active Directory** > **aplikacje przedsiębiorstwa** > **wszystkie aplikacje** sekcji.

2. Jeśli skonfigurowano już Pingboard dla logowania jednokrotnego, wyszukiwanie wystąpienia programu Pingboard przy użyciu pola wyszukiwania. W przeciwnym razie wybierz **Dodaj** i wyszukaj **Pingboard** w galerii aplikacji. Wybierz **Pingboard** z wyników wyszukiwania i dodaj go do listy aplikacji.

3. Wybierz wystąpienia programu Pingboard, a następnie wybierz **inicjowania obsługi administracyjnej** kartę.

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Pingboard inicjowania obsługi administracyjnej.](./media/pingboard-provisioning-tutorial/pingboardazureprovisioning.png)
    
5. W obszarze **poświadczeń administratora** sekcji, wykonaj następujące czynności:

    a. W **adres URL dzierżawy**, wprowadź `https://your_domain.pingboard.com/scim/v2`i zastąp "format domena_użytkownika" domenę prawdziwe.

    b. Zaloguj się do [Pingboard](https://pingboard.com/) przy użyciu konta administratora.

    c. Wybierz **dodatki** > **integracji** > **usługi Azure Active Directory**.

    d. Przejdź do **Konfiguruj** , a następnie wybierz **Włącz inicjowanie obsługi użytkowników z usługi Azure**.

    e. Skopiuj token w **tokenu elementu nośnego OAuth**i wprowadź go w **klucz tajny tokenu**.

6. W portalu Azure wybierz **Testuj połączenie** zapewniające usługi Azure AD mogą łączyć się z aplikacji Pingboard. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto Pingboard ma uprawnienia administratora i spróbuj **Testuj połączenie** krok ponownie.

7. Wprowadź adres e-mail osoby lub grupy, które chcesz otrzymywać inicjowania obsługi administracyjnej powiadomienia o błędach w **wiadomość E-mail z powiadomieniem**. Zaznacz pole wyboru poniżej.

8. Wybierz pozycję **Zapisz**. 

9. W obszarze **mapowania** zaznacz **synchronizacji Azure Active Directory użytkownikom Pingboard**.

10. W **mapowań atrybutów** Przejrzyj atrybuty użytkownika mają być synchronizowane z usługi Azure AD do Pingboard. Atrybuty wybrany jako **pasujące** właściwości są używane do dopasowania kont użytkowników w Pingboard dla operacji update. Wybierz **zapisać** aby zatwierdzić zmiany. Aby uzyskać więcej informacji, zobacz [aprowizacji mapowań atrybutów użytkowników Dostosuj](../active-directory-saas-customizing-attribute-mappings.md).

11. Aby włączyć usługi Azure AD, inicjowania obsługi usługi dla Pingboard, w **ustawienia** Zmień **stan inicjowania obsługi administracyjnej** do **na**.

12. Wybierz **zapisać** można rozpocząć synchronizacji początkowej z użytkowników przypisanych do Pingboard.

Wstępna synchronizacja zajmuje więcej czasu wykonywania niż kolejne synchronizacje, występujące co około 40 minut tak długo, jak usługa jest uruchomiona. Użyj **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi administracyjnej Dzienniki aktywności. Dzienniki opisano wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej na Pingboard aplikacji.

Aby uzyskać więcej informacji na temat usługi Azure AD, inicjowanie obsługi dzienników do odczytu, zobacz [raport dotyczący użytkownika automatyczne Inicjowanie obsługi konta](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowizacja konta użytkowników dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie rejestracji jednokrotnej](pingboard-tutorial.md)
