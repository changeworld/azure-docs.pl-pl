---
title: 'Samouczek: Konfigurowanie usługi Foodee do automatycznego inicjowania obsługi administracyjnej przez użytkownika przy użyciu usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego inicjowania obsługi administracyjnej i usuwania kont użytkowników usługi Foodee.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: 2195056ec66550063aba5ce5e2b977b51a6dc5e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057829"
---
# <a name="tutorial-configure-foodee-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie foodee do automatycznego inicjowania obsługi administracyjnej przez użytkowników

W tym artykule pokazano, jak skonfigurować usługę Azure Active Directory (Azure AD) w usłudze Foodee i usłudze Azure AD, aby automatycznie aprowizować lub usuwać użytkownikom lub grupom usługi Advision do usługi Foodee.

> [!NOTE]
> W tym artykule opisano łącznik, który jest zbudowany na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby dowiedzieć się, co robi ta usługa i jak działa, a także uzyskać odpowiedzi na często zadawane pytania, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w wersji zapoznawczej. Aby uzyskać więcej informacji na temat funkcji warunków użytkowania platformy Azure dla funkcji w wersji zapoznawczej, przejdź do [dodatkowych warunków użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku przyjęto założenie, że spełnione są następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Najemca Foodee](https://www.food.ee/about/)
* Konto użytkownika w użyno uprawnień administratora

## <a name="assign-users-to-foodee"></a>Przypisywanie użytkowników do foodee 

Usługa Azure AD używa koncepcji o nazwie *przydziały,* aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej, należy zdecydować, którzy użytkownicy lub grupy w usłudze Azure AD potrzebują dostępu do Usługi Foodee. Po dokonaniu tego ustalenia możesz przypisać tych użytkowników lub grupy do firmy Foodee, postępując zgodnie z instrukcjami w aplikacji [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa.](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-foodee"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Foodee 

Podczas przypisywania użytkowników należy pamiętać o następujących wskazówkach:

* Zaleca się przypisanie tylko jednego użytkownika usługi Azure AD do usługi Foodee w celu przetestowania konfiguracji automatycznego inicjowania obsługi administracyjnej użytkowników. Później można przypisać dodatkowych użytkowników lub grupy.

* Podczas przypisywania użytkownika do firmy Foodee wybierz dowolną prawidłową rolę specyficzną dla aplikacji, jeśli jest dostępna, w okienku **Przypisanie.** Użytkownicy, którzy mają rolę *dostępu domyślnego* są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="set-up-foodee-for-provisioning"></a>Konfigurowanie foodee do inicjowania obsługi administracyjnej

Przed skonfigurowaniem Foodee do automatycznego inicjowania obsługi administracyjnej przez przy użyciu usługi Azure AD, należy włączyć system dla międzydomenowych zarządzania tożsamościami (SCIM) inicjowania obsługi administracyjnej w Foodee.

1. Zaloguj się do [Foodee](https://www.food.ee/login/), a następnie wybierz identyfikator dzierżawy.

    ![Foodee](media/Foodee-provisioning-tutorial/tenant.png)

1. W obszarze **Portal przedsiębiorstwa**wybierz **pozycję Logowanie jednokrotne .**

    ![Menu lewego okienka foodee enterprise portal](media/Foodee-provisioning-tutorial/scim.png)

1. Skopiuj wartość w polu **tokenu interfejsu API** do późniejszego użycia. Wprowadź go w polu **Token tajny** na karcie **Inicjowanie obsługi administracyjnej** aplikacji Foodee w witrynie Azure portal.

    ![Foodee](media/Foodee-provisioning-tutorial/token.png)

## <a name="add-foodee-from-the-gallery"></a>Dodaj Foodee z galerii

Aby skonfigurować Foodee do automatycznego inicjowania obsługi administracyjnej przez przy użyciu usługi Azure AD, należy dodać Foodee z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

Aby dodać foodee z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:

1. W [witrynie Azure Portal](https://portal.azure.com) w okienku po lewej stronie wybierz pozycję **Azure Active Directory**.

    ![Polecenie usługi Azure Active Directory](common/select-azuread.png)

1. Wybierz **aplikacje** > enterprise**Wszystkie aplikacje**.

    ![Okienko Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Aby dodać nową aplikację, wybierz **pozycję Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

1. W polu wyszukiwania wprowadź **pozycję Jedzenie**, wybierz pozycję **Jedzenie** w okienku wyników, a następnie wybierz pozycję **Dodaj,** aby dodać aplikację.

    ![Foodee na liście wyników](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-foodee"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej dla użytkownika w użyczeniu 

W tej sekcji można skonfigurować usługę inicjowania obsługi administracyjnej usługi Azure AD do tworzenia, aktualizowania i wyłączania użytkowników lub grup w programie Foodee na podstawie przypisań użytkowników lub grup w usłudze Azure AD.

> [!TIP]
> Można również włączyć samooceny jednokrotne na podstawie SAML dla Foodee, postępując zgodnie z instrukcjami w [samouczku logowania jednokrotnego Foodee](Foodee-tutorial.md). Można skonfigurować logowanie jednokrotne niezależnie od automatycznego inicjowania obsługi administracyjnej użytkownika, chociaż te dwie funkcje wzajemnie się uzupełniają.

Skonfiguruj automatyczne inicjowanie obsługi administracyjnej dla użytkownika w usłudze Azure AD, wykonując następujące czynności:

1. W [portalu Azure](https://portal.azure.com)wybierz pozycję **Aplikacje** > dla**przedsiębiorstw Wszystkie aplikacje**.

    ![Okienko aplikacji dla przedsiębiorstw](common/enterprise-applications.png)

1. Na liście **Aplikacje** wybierz pozycję **Smakosz**.

    ![Łącze Foodee na liście Aplikacje](common/all-applications.png)

1. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning.png)

1. Z listy rozwijanej **Tryb inicjowania obsługi administracyjnej** wybierz pozycję **Automatyczne**.

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning-automatic.png)

1. W obszarze **Poświadczenia administratora**wykonaj następujące czynności:

   a. W polu **Adres URL dzierżawy** wprowadź **wartość https:\//concierge.food.ee/scim/v2,** która została pobrana wcześniej.

   b. W polu **Tajny token** wprowadź wartość **tokenu interfejsu API** pobraną wcześniej.
   
   d. Aby upewnić się, że usługa Azure AD może łączyć się z usługą Foodee, wybierz opcję **Testuj połączenie**. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto Foodee ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Łącze Test connection](common/provisioning-testconnection-tenanturltoken.png)

1. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail po wystąpieniu awarii.**

    ![Pole tekstowe Wiadomości e-mail z powiadomieniem](common/provisioning-notification-email.png)

1. Wybierz **pozycję Zapisz**.

1. W obszarze **Mapowania**wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z usługą foodee**.

    ![Mapowania użytkowników foodee](media/Foodee-provisioning-tutorial/usermapping.png)

1. W obszarze **Mapowania atrybutów**przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD na Foodee. Atrybuty, które są wybrane jako **pasujące** właściwości są używane do dopasowania *kont użytkowników* w Foodee dla operacji aktualizacji. 

    ![Mapowania użytkowników foodee](media/Foodee-provisioning-tutorial/userattribute.png)

1. Aby zatwierdzić zmiany, wybierz pozycję **Zapisz**.
1. W obszarze **Mapowania**wybierz pozycję **Synchronizuj grupy usługi Azure Active Directory z usługą gastronomiczną**.

    ![Mapowania użytkowników foodee](media/Foodee-provisioning-tutorial/groupmapping.png)

1. W obszarze **Mapowania atrybutów**przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD na Foodee. Atrybuty, które są wybrane jako **pasujące** właściwości są używane do dopasowania *kont grupy* w Foodee dla operacji aktualizacji.

    ![Mapowania użytkowników foodee](media/Foodee-provisioning-tutorial/groupattribute.png)

1. Aby zatwierdzić zmiany, wybierz pozycję **Zapisz**.
1. Skonfiguruj filtry zakresu. Aby dowiedzieć się, jak to zrobić, zapoznaj się z instrukcjami w [samouczku filtru zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla usługi Foodee, w sekcji **Ustawienia** zmień **stan inicjowania obsługi administracyjnej** **na Włączone**.

    ![Przełącznik Stanu inicjowania obsługi administracyjnej](common/provisioning-toggle-on.png)

1. W obszarze **Ustawienia**na liście rozwijanej **Zakres** zdefiniuj użytkowników lub grupy, które mają być aprowizowe dla użytkownika.

    ![Lista rozwijana Zakres inicjowania obsługi administracyjnej](common/provisioning-scope.png)

1. Gdy będziesz gotowy do aprowienia, wybierz pozycję **Zapisz**.

    ![Przycisk Zapisywanie konfiguracji inicjowania obsługi administracyjnej](common/provisioning-configuration-save.png)

Poprzednia operacja rozpoczyna początkową synchronizację użytkowników lub grup zdefiniowanych na liście rozwijanej **Zakres.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje. Aby uzyskać więcej informacji, zobacz [Jak długo potrwa aprowizje użytkowników?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Za pomocą sekcji **Bieżący stan** można monitorować postęp i śledzić łącza do raportu aktywności inicjowania obsługi administracyjnej. Raport opisuje wszystkie akcje, które są wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w witrynie Foodee. Aby uzyskać więcej informacji, zobacz [Sprawdzanie stanu inicjowania obsługi administracyjnej przez użytkowników](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Aby zapoznać się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowewaniem kont użytkowników dla aplikacji przedsiębiorstwa](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)
