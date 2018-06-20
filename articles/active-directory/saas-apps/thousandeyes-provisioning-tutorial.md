---
title: 'Samouczek: Konfigurowanie ThousandEyes dla użytkownika automatycznego inicjowania obsługi administracyjnej z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory, aby automatycznie zapewnianie i usuwanie kont użytkowników do ThousandEyes.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: mtillman
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser-msft
ms.openlocfilehash: 71101ef119db719d281f3e6db4e0c3dee023c4d4
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36210143"
---
# <a name="tutorial-configure-thousandeyes-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie ThousandEyes dla użytkownika automatycznego inicjowania obsługi administracyjnej.


Celem tego samouczka jest opisano czynności, które należy wykonać w ThousandEyes i Azure AD, aby automatycznie zapewnianie i usuwanie kont użytkowników z usługi Azure AD ThousandEyes. 

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że już następujące elementy:

*   Dzierżawy usługi Azure Active directory
*   Dzierżawcy ThousandEyes z [planu Standard](https://www.thousandeyes.com/pricing) lub lepiej jest włączone 
*   Konto użytkownika z uprawnieniami administratora w ThousandEyes 

> [!NOTE]
> Zależy od usługi Azure AD, inicjowania obsługi administracyjnej integracji [ThousandEyes SCIM API](https://success.thousandeyes.com/PublicArticlePage?articleIdParam=kA044000000CnWrCAK), który jest dostępny dla zespołów ThousandEyes w planie Standard lub większą.

## <a name="assigning-users-to-thousandeyes"></a>Przypisywanie użytkowników do ThousandEyes

Usługi Azure Active Directory używa pojęcie o nazwie "przypisania" w celu określenia, którzy użytkownicy powinien otrzymać dostęp do wybranej aplikacji. W kontekście użytkownika automatyczne Inicjowanie obsługi konta tylko użytkownicy i grupy, które "przypisano" do aplikacji w usłudze Azure AD jest zsynchronizowany. 

Przed Skonfiguruj i włącz usługę inicjowania obsługi administracyjnej, należy zdecydować, jakie użytkownicy i/lub grup w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do aplikacji ThousandEyes. Po decyzję, postępując zgodnie z instrukcjami w tym miejscu można przypisać tych użytkowników do aplikacji ThousandEyes:

[Przypisanie użytkownika lub grupę do aplikacji w przedsiębiorstwie](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-thousandeyes"></a>Ważne porady dotyczące przypisywania użytkowników do ThousandEyes

*   Zalecane jest jeden jest przypisany użytkownik usługi Azure AD ThousandEyes do testowania konfiguracji inicjowania obsługi administracyjnej. Później można przypisać dodatkowych użytkowników i/lub grup.

*   Przypisanie użytkownika do ThousandEyes, należy wybrać opcję **użytkownika** rola, lub inny prawidłowy specyficzne dla aplikacji (jeśli jest dostępny) w oknie dialogowym przypisania. **Domyślnego dostępu** roli nie działa w przypadku inicjowania obsługi administracyjnej, a użytkownicy są pomijane.


## <a name="configuring-user-provisioning-to-thousandeyes"></a>Konfigurowanie inicjowania obsługi administracyjnej ThousandEyes użytkownika 

Ta sekcja przeprowadzi Cię przez łączenie usługi Azure AD z konta użytkownika w ThousandEyes inicjowania obsługi interfejsu API i konfigurowanie inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączania konta użytkowników przypisane w ThousandEyes w oparciu o przypisania użytkowników i grup w usłudze Azure AD .

> [!TIP]
> Można też włączyć na języku SAML logowania jednokrotnego dla ThousandEyes, wykonując instrukcje podane w [portalu Azure](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej, chociaż te dwie funkcje uzupełniania siebie nawzajem.


### <a name="configure-automatic-user-account-provisioning-to-thousandeyes-in-azure-ad"></a>Skonfiguruj użytkownika automatyczne Inicjowanie obsługi konta do ThousandEyes w usłudze Azure AD


1. W [portalu Azure](https://portal.azure.com), przejdź do **usługi Azure Active Directory > aplikacje przedsiębiorstwa > wszystkie aplikacje** sekcji.

2. Jeśli ThousandEyes został już skonfigurowany dla logowania jednokrotnego, wyszukiwanie wystąpieniem ThousandEyes przy użyciu pola wyszukiwania. W przeciwnym razie wybierz **Dodaj** i wyszukaj **ThousandEyes** w galerii aplikacji. Wybierz ThousandEyes w wynikach wyszukiwania, a następnie dodaj go do listy aplikacji.

3. Wybierz wystąpienia programu ThousandEyes, a następnie wybierz **inicjowania obsługi administracyjnej** kartę.

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![ThousandEyes inicjowania obsługi administracyjnej](./media/thousandeyes-provisioning-tutorial/ThousandEyes1.png)

5. W obszarze **poświadczeń administratora** sekcji wejściowych **klucz tajny tokenu** generowane przez konto użytkownika ThousandEyes (token znajduje się na koncie ThousandEyes: **zabezpieczeń & Uwierzytelnianie**). 

    ![ThousandEyes inicjowania obsługi administracyjnej](./media/thousandeyes-provisioning-tutorial/ThousandEyes2.png)

6. W portalu Azure kliknij **Testuj połączenie** zapewniające usługi Azure AD mogą łączyć się z aplikacji ThousandEyes. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto ThousandEyes ma uprawnienia administratora i spróbuj ponownie wykonać krok 5.

7. Wprowadź adres e-mail osoby lub grupy, który powinien zostać wyświetlony inicjowania obsługi administracyjnej powiadomienia o błędach w **wiadomość E-mail z powiadomieniem** pola, a następnie zaznacz pole wyboru "Wyślij wiadomość e-mail z powiadomieniem, gdy wystąpi błąd".

8. Kliknij pozycję **Zapisz**. 

9. W sekcji mapowania wybierz **synchronizacji Azure Active Directory użytkownikom ThousandEyes**.

10. W **mapowań atrybutów** Przejrzyj atrybutów użytkowników, które są synchronizowane z usługi Azure AD ThousandEyes. Atrybuty wybrany jako **pasujące** właściwości są używane do dopasowania kont użytkowników w ThousandEyes dla operacji update. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

11. Aby włączyć usługi Azure AD usługi dla ThousandEyes inicjowania obsługi administracyjnej, zmień **stan inicjowania obsługi administracyjnej** do **na** w **ustawienia** sekcji

12. Kliknij pozycję **Zapisz**. 

Ta operacja uruchamia wstępnej synchronizacji użytkowników i/lub grupy przypisane do ThousandEyes w sekcji Użytkownicy i grupy. Synchronizacji początkowej zajmuje więcej czasu wykonywania niż kolejne synchronizacje, występujące co około 40 minut tak długo, jak usługa jest uruchomiona. Można użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi administracyjnej Dzienniki aktywności, które opisują wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej.

Aby uzyskać więcej informacji na temat usługi Azure AD, inicjowanie obsługi dzienników do odczytu, zobacz [raportowania na użytkownika automatyczne Inicjowanie obsługi konta](../active-directory-saas-provisioning-reporting.md).


## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowizacja konta użytkowników dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak należy przejrzeć dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../active-directory-saas-provisioning-reporting.md)
