---
title: 'Samouczek: Konfigurowanie programu Zscaler One do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej kont użytkowników do programu Zscaler One.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 72f6ba2b-73ed-420a-863a-aff672f26fa3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 87413932acee576934ee50b59546371b03ceaf7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064176"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie programu Zscaler One do automatycznego inicjowania obsługi administracyjnej przez użytkowników

W tym samouczku przedstawiono kroki do wykonania w zscaler jeden i azure usługi Active Directory (Azure AD) do konfigurowania usługi Azure AD do automatycznego inicjowania obsługi administracyjnej i deprovision użytkowników i grup do Zscaler One.

> [!NOTE]
> W tym samouczku opisano łącznik, który jest zbudowany na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji typu oprogramowanie jako usługa (SaaS) za pomocą usługi Azure Active Directory](../active-directory-saas-app-provisioning.md).


## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz:

* Dzierżawa usługi Azure AD.
* Dzierżawa Zscaler One.
* Konto użytkownika w zscaler one z uprawnieniami administratora.

> [!NOTE]
> Integracja inicjowania obsługi administracyjnej usługi Azure AD zależy od interfejsu API SCIM Zscaler One. Ten interfejs API jest dostępny dla deweloperów Zscaler One dla kont z pakietem Enterprise.

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>Dodawanie zscalera One z portalu Azure Marketplace

Przed skonfigurowaniem programu Zscaler One do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD należy dodać program Zscaler One z portalu Azure Marketplace do listy zarządzanych aplikacji SaaS.

Aby dodać zscaler One z Marketplace, wykonaj następujące kroki.

1. W [witrynie Azure portal](https://portal.azure.com)w okienku nawigacji po lewej stronie wybierz pozycję **Azure Active Directory**.

    ![Ikona usługi Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji enterprise**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **zscaler one** i wybierz **Zscaler One** z panelu wyników. Aby dodać aplikację, wybierz pozycję **Dodaj**.

    ![Zscaler One na liście wyników](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>Przypisywanie użytkowników do zscaler one

Usługa Azure Active Directory używa koncepcji o nazwie *przydziały,* aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej, zdecydować, którzy użytkownicy lub grupy w usłudze Azure AD potrzebują dostępu do Zscaler One. Aby przypisać tych użytkowników lub grupy do aplikacji Zscaler One, postępuj zgodnie z instrukcjami w aplikacji [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Zscaler One

* Zaleca się przypisanie jednego użytkownika usługi Azure AD do programu Zscaler One w celu przetestowania konfiguracji automatycznego inicjowania obsługi administracyjnej użytkownika. Później można przypisać dodatkowych użytkowników lub grupy.

* Po przypisaniu użytkownika do programu Zscaler One wybierz dowolną prawidłową rolę specyficzną dla aplikacji, jeśli jest dostępna, w oknie dialogowym przypisywania. Użytkownicy z rolą **dostępu domyślnego** są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej w zscaler one

W tej sekcji znajdziesz instrukcje konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD. Służy do tworzenia, aktualizowania i wyłączania użytkowników lub grup w programie Zscaler One na podstawie przypisania użytkowników lub grup w usłudze Azure AD.

> [!TIP]
> Można również włączyć samol oparte logowanie jednokrotne dla Zscaler One. Postępuj zgodnie z instrukcjami w [samouczku logowania jednokrotnego Zscaler One](zscaler-One-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej użytkownika, chociaż te dwie funkcje wzajemnie się uzupełniają.

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej dla programu Zscaler One w usłudze Azure AD

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz **aplikacje dla przedsiębiorstw** > **Wszystkie aplikacje** > **Zscaler One**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Zscaler One**.

    ![Łącze Zscaler One na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Inicjowanie obsługi administracyjnej zscaler one](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Tryb inicjowania obsługi administracyjnej Zscaler One](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. W sekcji **Poświadczenia administratora** wypełnij pola **ADRES URL dzierżawy** i **Tajny token** ustawienia konta Zscaler One zgodnie z opisem w kroku 6.

6. Aby uzyskać adres URL dzierżawy i token tajny, przejdź do**ustawień uwierzytelniania** **administracyjnego** > w interfejsie użytkownika portalu Zscaler One. W obszarze **Typ uwierzytelniania**wybierz pozycję **SAML**.

    ![Ustawienia uwierzytelniania Zscaler One](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. Wybierz **pozycję Konfiguruj SAML,** aby otworzyć opcje **Konfigurowanie SAML.**

    ![Zscaler One Konfigurowanie SAML](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. Wybierz **pozycję Włącz inicjowanie obsługi administracyjnej oparte na uściach,** aby uzyskać ustawienia w **podstawowym adresie URL** i **tokenie nośnika**. Następnie zapisz ustawienia. Skopiuj ustawienie **podstawowy adres URL** do adresu URL **dzierżawy** w witrynie Azure portal. Skopiuj ustawienie **tokenu nośnego** do **tokenu tajnego** w witrynie Azure portal.

7. Po wypełnieniu pól wyświetlanych w kroku 5 wybierz **opcję Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z programem Zscaler One. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto Zscaler One ma uprawnienia administratora i spróbuj ponownie.

    ![Zscaler Jedno połączenie testowe](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, aby otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej. Zaznacz pole wyboru **Wyślij powiadomienie e-mail w** przypadku wystąpienia błędu.

    ![Zscaler jeden e-mail z powiadomieniem](./media/zscaler-one-provisioning-tutorial/notification.png)

9. Wybierz **pozycję Zapisz**.

10. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z programem Zscaler One**.

    ![Synchronizacja użytkownika Zscaler One](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Zscaler One w sekcji **Mapowania atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w Zscaler One dla operacji aktualizacji. Aby zapisać zmiany, wybierz pozycję **Zapisz**.

    ![Zscaler Jeden pasujący do atrybutów użytkownika](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. W sekcji **Mapowania** wybierz pozycję **Synchronizuj grupy usługi Azure Active Directory z programem Zscaler One**.

    ![Synchronizacja grupy Zscaler One](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do Zscaler One w sekcji **Mapowania atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania grup w Zscaler One dla operacji aktualizacji. Aby zapisać zmiany, wybierz pozycję **Zapisz**.

    ![Zscaler Jeden pasujący atrybuty grupy](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. Aby skonfigurować filtry zakresu, postępuj zgodnie z instrukcjami zawartymi w [samouczku filtrowania zakresu](./../active-directory-saas-scoping-filters.md).

15. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla programu Zscaler One, w sekcji **Ustawienia** zmień **stan inicjowania obsługi administracyjnej** **na Włączone**.

    ![Stan inicjowania obsługi administracyjnej programu Zscaler One](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. Zdefiniuj użytkowników lub grupy, które chcesz udostępnić Zscaler One. W sekcji **Ustawienia** wybierz wartości, które chcesz **zaznaczyć**w polu .

    ![Zscaler jeden zakres](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. Gdy będziesz gotowy do aprowienia, wybierz pozycję **Zapisz**.

    ![Zscaler Jeden zapis](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż później synchronizuje. Występują one co około 40 minut, tak długo, jak usługa inicjowania obsługi administracyjnej usługi Azure AD jest uruchamiana. 

Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i śledzić łącza do raportu aktywności inicjowania obsługi administracyjnej. Raport opisuje wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w programie Zscaler One.

Aby uzyskać informacje na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../active-directory-saas-provisioning-reporting.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowewaniem kont użytkowników dla aplikacji przedsiębiorstwa](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png
