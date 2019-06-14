---
title: 'Samouczek: Konfigurowanie piaskownicy usługi Salesforce dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i piaskownicy usługi Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: bab73fda-6754-411d-9288-f73ecdaa486d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e0a4eed020728bea5de196eebe438947ae509e4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60515673"
---
# <a name="tutorial-configure-salesforce-sandbox-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie piaskownicy usługi Salesforce dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest Wam czynności, które należy wykonać w piaskownicy usługi Salesforce i Azure AD, do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników z usługi Azure AD do piaskownicy usługi Salesforce.

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, że masz następujące elementy:

*   Dzierżawy usługi Azure Active directory.
*   Nieprawidłowy dzierżawca "piaskownicy" usługi Salesforce do pracy lub piaskownicy usługi Salesforce dla instytucji edukacyjnych. Bezpłatne konto próbne może używać do któraś z tych usług.
*   Konto użytkownika w piaskownicy usługi Salesforce z uprawnieniami administratora zespołu.

## <a name="assigning-users-to-salesforce-sandbox"></a>Przypisywanie użytkowników do piaskownicy usługi Salesforce

Usługa Azure Active Directory używa koncepcji o nazwie "przypisania", aby określić, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście Inicjowanie obsługi administracyjnej konta użytkowników są synchronizowane tylko użytkowników i grup, które "przypisano" do aplikacji w usłudze Azure AD.

Przed Skonfiguruj i włącz usługę aprowizacji, musisz zdecydować, którzy użytkownicy lub grupy w usłudze Azure AD muszą mieć dostęp do aplikacji Salesforce piaskownicy. Po utworzeniu tej decyzji, tych użytkowników można przypisać do aplikacji Salesforce piaskownicy, postępując zgodnie z instrukcjami w [przypisać użytkownika lub grupy do aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-salesforce-sandbox"></a>Ważne wskazówki dotyczące przypisywania użytkowników do piaskownicy usługi Salesforce

* Zalecane jest jeden użytkownik usługi Azure AD jest przypisane do piaskownicy usługi Salesforce do testowania konfiguracji aprowizacji. Później można przypisać dodatkowych użytkowników i/lub grup.

* Podczas przypisywania użytkowników do piaskownicy usługi Salesforce, należy wybrać poprawnej roli użytkownika. Rola "Domyślnego dostępu" nie działa w przypadku inicjowania obsługi administracyjnej.

> [!NOTE]
> Ta aplikacja umożliwia zaimportowanie ról niestandardowych z piaskownicy usługi Salesforce, jako część procesu inicjowania obsługi administracyjnej klienta, warto wybrać podczas przypisywania użytkowników.

## <a name="enable-automated-user-provisioning"></a>Włączanie użytkownika automatyczne Inicjowanie obsługi administracyjnej.

Ta sekcja przeprowadzi Cię przez połączenie usługi Azure AD do konta użytkownika usługi Salesforce piaskownicy aprowizujący interfejs API i konfigurowanie usługi aprowizacji, aby utworzyć, zaktualizować, a następnie wyłącz przypisanych użytkowników, kont w piaskownicy usługi Salesforce na podstawie których użytkowników i grup przypisania w usłudze Azure AD.

>[!Tip]
>Można też włączyć opartej na SAML logowania jednokrotnego dla piaskownicy usługi Salesforce, postępując zgodnie z instrukcjami dostarczone w [witryny Azure portal](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatyczną aprowizację, chociaż te dwie funkcje uzupełnienie siebie nawzajem.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurowanie automatycznej konta aprowizacji użytkowników

Jest celem tej sekcji omówiono sposób włączania aprowizacji użytkowników, kont użytkowników usługi Active Directory ją w piaskownicy usługi Salesforce.

1. W [witryny Azure portal](https://portal.azure.com), przejdź do **usługi Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje** sekcji.

1. Jeśli już skonfigurowano piaskownicy usługi Salesforce dla logowania jednokrotnego, wyszukiwanie wystąpienia programu Salesforce piaskownicy za pomocą pola wyszukiwania. W przeciwnym razie wybierz **Dodaj** i wyszukaj **piaskownicy usługi Salesforce** w galerii aplikacji. Wybierz piaskownicy usługi Salesforce z wyników wyszukiwania, a następnie dodaj go do listy aplikacji.

1. Wybierz wystąpienia programu Salesforce piaskownicy, a następnie wybierz **aprowizacji** kartę.

1. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Inicjowanie obsługi administracyjnej](./media/salesforce-sandbox-provisioning-tutorial/provisioning.png)

1. W obszarze **poświadczeń administratora** sekcji, skonfiguruj następujące ustawienia konfiguracji:
   
    a. W **nazwa użytkownika administratora** polu tekstowym w piaskownicy usługi Salesforce konta nazwę, która ma typ **Administrator systemu** profilu w witrynie Salesforce.com przypisane.
   
    b. W **hasło administratora** pole tekstowe, wpisz hasło dla tego konta.

1. Aby bezpieczeństwo piaskownicy usługi Salesforce należy uzyskać token, otwórz nową kartę i zaloguj do tego samego konta administratora w piaskownicy usługi Salesforce. W prawym górnym rogu strony, kliknij swoją nazwę użytkownika, a następnie kliknij przycisk **ustawienia**.

     ![Włączanie automatycznej aprowizacji użytkowników](./media/salesforce-sandbox-provisioning-tutorial/sf-my-settings.png "Włączanie automatycznej aprowizacji użytkowników")

1. W okienku nawigacji po lewej stronie kliknij **Moje dane osobowe** rozwiń sekcję powiązane, a następnie kliknij przycisk **zresetować moje tokenu zabezpieczeń**.
  
    ![Włączanie automatycznej aprowizacji użytkowników](./media/salesforce-sandbox-provisioning-tutorial/sf-personal-reset.png "Włączanie automatycznej aprowizacji użytkowników")

1. Na **zresetować tokenu zabezpieczeń** kliknij **zresetować tokenu zabezpieczeń** przycisku.

    ![Włączanie automatycznej aprowizacji użytkowników](./media/salesforce-sandbox-provisioning-tutorial/sf-reset-token.png "Włączanie automatycznej aprowizacji użytkowników")

1. Sprawdź skrzynkę odbiorczą poczty e-mail skojarzony z tym kontem administratora. Wyszukaj wiadomości e-mail z Salesforce Sandbox.com, który zawiera nowy token.

1. Skopiuj token, przejdź do okna w usłudze Azure AD i wklej go do **klucz tajny tokenu** pola.

1. W witrynie Azure portal kliknij pozycję **Testuj połączenie** zapewniające usługi Azure AD connect można do aplikacji Salesforce piaskownicy.

1. W **wiadomość E-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, który powinien otrzymywać powiadomienia o błędach aprowizacji i zaznacz pole wyboru.

1. Kliknij przycisk **Zapisz.**  
    
1.  W sekcji mapowania, wybierz **synchronizacji Azure użytkownicy usługi Active Directory ją w piaskownicy usługi Salesforce.**

1. W **mapowania atrybutów** Przejrzyj atrybutów użytkowników, które są synchronizowane z usługi Azure AD z piaskownicy usługi Salesforce. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w piaskownicy usługi Salesforce dla operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

1. Aby włączyć usługi Azure AD, inicjowania obsługi usługi dla piaskownicy usługi Salesforce, zmień **stanie aprowizacji** do **na** w sekcji Ustawienia

1. Kliknij przycisk **Zapisz.**

Rozpoczyna się wstępna synchronizacja użytkowników i/lub grupy przypisane do piaskownicy usługi Salesforce w sekcji Użytkownicy i grupy. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak usługa jest uruchomiona. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi dzienników aktywności, które opisują każdą akcję wykonaną przez usługę aprowizacji w aplikacji Salesforce piaskownicy.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie logowania jednokrotnego](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-sandbox-tutorial)
