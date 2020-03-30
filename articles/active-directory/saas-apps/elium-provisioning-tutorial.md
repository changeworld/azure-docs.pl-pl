---
title: 'Samouczek: Konfigurowanie elium do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego aprowizowania i usuwania obsługi administracyjnej kont użytkowników do elium.
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
ms.date: 08/19/2019
ms.author: Zhchia
ms.openlocfilehash: a3e1c936d46e0d9b0ad3b98dafeda7df0b4a23db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058493"
---
# <a name="tutorial-configure-elium-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Elium do automatycznego inicjowania obsługi administracyjnej przez użytkowników

W tym samouczku pokazano, jak skonfigurować elium i usługi Azure Active Directory (Azure AD) do automatycznego inicjowania obsługi administracyjnej i usuwania z obsługi administracyjnej użytkowników lub grup do Elium.

> [!NOTE]
> W tym samouczku opisano łącznik, który jest zbudowany na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje o tym, co robi ta usługa i jak działa, a także w przypadku często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w wersji zapoznawczej. Aby zapoznać się z ogólnymi warunkami użytkowania funkcji platformy Azure w wersji zapoznawczej, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku przyjęto założenie, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Najemca Elium](https://www.elium.com/pricing/)
* Konto użytkownika w Elium z uprawnieniami administratora

## <a name="assigning-users-to-elium"></a>Przypisywanie użytkowników do Elium

Usługa Azure AD używa koncepcji o nazwie *przydziały,* aby określić, którzy użytkownicy otrzymują dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy i grupy, które zostały przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej, zdecydować, którzy użytkownicy i grupy w usłudze Azure AD potrzebują dostępu do Elium. Następnie przypisz tych użytkowników i grupy do Elium, wykonując czynności opisane w [aplikacji Przypisywanie użytkownika lub grupy do aplikacji korporacyjnej.](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-elium"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Elium 

Zaleca się przypisanie jednego użytkownika usługi Azure AD do Elium, aby przetestować konfigurację automatycznego inicjowania obsługi administracyjnej. Więcej użytkowników i grup można przypisać później.

Podczas przypisywania użytkownika do Elium należy wybrać prawidłową rolę specyficzną dla aplikacji (jeśli są dostępne) w oknie dialogowym przypisywania. Użytkownicy, którzy mają rolę **dostępu domyślnego** są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="set-up-elium-for-provisioning"></a>Konfigurowanie elium do inicjowania obsługi administracyjnej

Przed skonfigurowaniem Elium do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD należy włączyć inicjowanie obsługi administracyjnej systemu dla wielu domen (SCIM) w elium. Wykonaj następujące kroki:

1. Zaloguj się do Elium i przejdź do **sekcji Moje** > **ustawienia**profilu .

    ![Pozycja menu Ustawienia w Elium](media/Elium-provisioning-tutorial/setting.png)

1. W lewym dolnym rogu w obszarze **ZAAWANSOWANE**wybierz pozycję **Zabezpieczenia**.

    ![Łącze bezpieczeństwa w Elium](media/Elium-provisioning-tutorial/security.png)

1. Skopiuj wartości **adresu URL dzierżawy** i **tajnego tokenu.** Te wartości użyjesz później, w odpowiednich polach na karcie **Inicjowanie obsługi administracyjnej** aplikacji Elium w witrynie Azure portal.

    ![Adres URL dzierżawy i tajne pola tokenów w Elium](media/Elium-provisioning-tutorial/token.png)

## <a name="add-elium-from-the-gallery"></a>Dodaj Elium z galerii

Aby skonfigurować Elium do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD, należy również dodać Elium z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji typu oprogramowanie jako usługa (SaaS). Wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com)w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Element menu usługi Azure Active Directory](common/select-azuread.png)

1. Przejdź do **aplikacji enterprise**, a następnie wybierz pozycję **Wszystkie aplikacje**.

     ![Blok aplikacji usługi Azure AD Enterprise](common/enterprise-applications.png)

1. Aby dodać nową aplikację, wybierz **pozycję Nowa aplikacja** w górnej części okienka.

    ![Nowe łącze aplikacji](common/add-new-app.png)

1. W polu wyszukiwania wpisz **Elium**, wybierz **elium** na liście wyników, a następnie wybierz pozycję **Dodaj,** aby dodać aplikację.

    ![Pole wyszukiwania galerii](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-elium"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej w elium

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD do tworzenia, aktualizowania i wyłączania użytkowników i grup w Elium, na podstawie przypisań użytkowników i grup w usłudze Azure AD.

> [!TIP]
> Można również włączyć logowanie jednokrotne dla Elium na podstawie języka znaczników oświadczeń zabezpieczeń (SAML), postępując zgodnie z instrukcjami zawartymi w [samouczku logowania jednokrotnego Elium](Elium-tutorial.md). Można skonfigurować logowanie jednokrotne niezależnie od automatycznego inicjowania obsługi administracyjnej użytkownika, chociaż dwie funkcje wzajemnie się uzupełniają.

Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej dla elium w usłudze Azure AD, wykonaj następujące kroki:

1. Zaloguj się do [portalu Azure,](https://portal.azure.com)wybierz **pozycję Aplikacje przedsiębiorstwa,** a następnie wybierz pozycję Wszystkie **aplikacje**.

    ![Blok aplikacji usługi Azure AD Enterprise](common/enterprise-applications.png)

1. Na liście aplikacji wybierz pozycję **Elium**.

    ![Lista Aplikacji w bloku Aplikacje przedsiębiorstwa](common/all-applications.png)

1. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Karta Inicjowanie obsługi administracyjnej w bloku Aplikacje przedsiębiorstwa](common/provisioning.png)

1. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Automatyczne ustawienie trybu inicjowania obsługi administracyjnej](common/provisioning-automatic.png)

1. W sekcji **Poświadczenia administratora** wpisz ** \<polecenie tenantURL\>/scim/v2** w polu Adres URL **dzierżawy.** **(TenantURL** jest wartością pobraną wcześniej z konsoli administracyjnej Elium). Wpisz również wartość **tokenu** Elium Secret w polu **Tajny token.** Na koniec wybierz **opcję Testuj połączenie,** aby sprawdzić, czy usługa Azure AD może łączyć się z Elium. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto Elium ma uprawnienia administratora i spróbuj ponownie.

    ![Pola Adres URL dzierżawy i token tajny w poświadczeniach administratora](common/provisioning-testconnection-tenanturltoken.png)

1. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która otrzyma powiadomienia o błędach inicjowania obsługi administracyjnej. Następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail, gdy wystąpi błąd.**

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

1. Kliknij przycisk **Zapisz**.

1. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z elium**.

    ![Synchronizowanie łącza do mapowania użytkowników usługi Azure AD na Elium](media/Elium-provisioning-tutorial/usermapping.png)

1. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Elium w sekcji **Mapowania atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w Elium do operacji aktualizacji. Wybierz **pozycję Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Mapowania atrybutów między usługą Azure AD i Elium](media/Elium-provisioning-tutorial/userattribute.png)

1. Aby skonfigurować filtry zakresu, postępuj zgodnie z instrukcjami zawartymi w [samouczku filtru zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla elium, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Stan inicjowania obsługi administracyjnej ustawiony na Włączone](common/provisioning-toggle-on.png)

1. Zdefiniuj użytkowników i grupy, które chcesz udostępnić Elium, zaznaczając wartości, które chcesz w polu listy rozwijanej **Zakres** w sekcji **Ustawienia.**

    ![Pole listy Zakres inicjowania obsługi administracyjnej](common/provisioning-scope.png)

1. Gdy będziesz gotowy do aprowienia, wybierz pozycję **Zapisz**.

    ![Przycisk Zapisz do inicjowania obsługi administracyjnej konfiguracji](common/provisioning-configuration-save.png)

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników i grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Ten proces synchronizacji początkowej trwa dłużej niż później synchronizuje. Aby uzyskać więcej informacji na temat czasu potrzebnego do inicjowania obsługi administracyjnej, zobacz [Jak długo potrwa inicjowanie obsługi administracyjnej użytkowników?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Użyj sekcji **Bieżący stan,** aby monitorować postęp i śledzić łącza do raportu aktywności inicjowania obsługi administracyjnej. Raport działania inicjowania obsługi administracyjnej opisuje wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w elium. Aby uzyskać więcej informacji, zobacz [Sprawdzanie stanu inicjowania obsługi administracyjnej przez użytkowników](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Aby zapoznać się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)
