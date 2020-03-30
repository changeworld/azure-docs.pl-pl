---
title: 'Samouczek: Konfigurowanie programu Zscaler do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego inicjowania obsługi administracyjnej i usuwania z aprowizowania kont użytkowników do programu Zscaler.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 31f67481-360d-4471-88c9-1cc9bdafee24
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 8add1f57b566d746d464c1ca165938fc112a9784
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062714"
---
# <a name="tutorial-configure-zscaler-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie programu Zscaler do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest zademonstrowanie kroków, które należy wykonać w zscaler i usługi Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD do automatycznego inicjowania obsługi administracyjnej i de-provision użytkowników i/lub grup do Zscaler.

> [!NOTE]
> W tym samouczku opisano łącznik utworzony na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../active-directory-saas-app-provisioning.md).
>

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące czynności:

* Dzierżawa usługi Azure AD.
* Dzierżawa Zscaler.
* Konto użytkownika w Zscaler z uprawnieniami administratora.

> [!NOTE]
> Integracja inicjowania obsługi administracyjnej usługi Azure AD opiera się na interfejsie API SCIM Zscaler, który jest dostępny dla deweloperów Zscaler dla kont z pakietem Enterprise.

## <a name="adding-zscaler-from-the-gallery"></a>Dodawanie zscalera z galerii

Przed skonfigurowaniem programu Zscaler do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD należy dodać program Zscaler z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać program Zscaler z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Zscaler**, wybierz **Zscaler** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![Zscaler na liście wyników](common/search-new-app.png)

## <a name="assigning-users-to-zscaler"></a>Przypisywanie użytkowników do zscalera

Usługa Azure Active Directory używa pojęcia o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy i/lub grupy, które zostały "przypisane" do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do programu Zscaler. Po podjęciu decyzji, można przypisać tych użytkowników i / lub grup do Zscaler, postępując zgodnie z instrukcjami tutaj:

* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler"></a>Ważne wskazówki dotyczące przypisywania użytkowników do zscalera

* Zaleca się, że jeden użytkownik usługi Azure AD jest przypisany do Zscaler do testowania konfiguracji automatycznego inicjowania obsługi administracyjnej użytkownika. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Podczas przypisywania użytkownika do programu Zscaler należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z rolą **dostępu domyślnego** są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="configuring-automatic-user-provisioning-to-zscaler"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej dla programu Zscaler

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD do tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie Zscaler na podstawie przypisaniów użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Można również włączyć samooceny jednokrotne logowania oparte na SAML dla Zscaler, postępując zgodnie z instrukcjami podanymi w [samouczku logowania jednokrotnego Zscaler](zscaler-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej użytkownika, chociaż te dwie funkcje wzajemnie się uzupełniają.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej dla programu Zscaler w usłudze Azure AD:

1. Zaloguj się do [portalu Azure](https://portal.azure.com) i wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję Wszystkie **aplikacje**, a następnie wybierz **Zscaler**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Zscaler**.

    ![Łącze Zscaler na liście Aplikacje](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Inicjowanie obsługi administracyjnej zscalera](./media/zscaler-provisioning-tutorial/provisioning-tab.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Inicjowanie obsługi administracyjnej zscalera](./media/zscaler-provisioning-tutorial/provisioning-credentials.png)

5. W sekcji **Poświadczenia administratora** wprowadź **adres URL dzierżawy** i **tajny token** konta Zscaler, zgodnie z opisem w kroku 6.

6. Aby uzyskać **adres URL dzierżawy** i **token tajny,** przejdź do **ustawień uwierzytelniania > administracji** w interfejsie użytkownika portalu Zscaler i kliknij **saml** w obszarze **Typ uwierzytelniania**.

    ![Inicjowanie obsługi administracyjnej zscalera](./media/zscaler-provisioning-tutorial/secret-token-1.png)

    Kliknij **przycisk Konfiguruj SAML,** aby otworzyć opcje **SAML konfiguracji.**

    ![Inicjowanie obsługi administracyjnej zscalera](./media/zscaler-provisioning-tutorial/secret-token-2.png)

    Wybierz **pozycję Włącz inicjowanie obsługi administracyjnej oparte na uściśle,** aby pobrać **podstawowy adres URL** i **token nośnika,** a następnie zapisz ustawienia. Skopiuj **podstawowy adres URL** do adresu URL **dzierżawy**i **tokenu nośnika** do **tokenu tajnego** w witrynie Azure portal.

7. Po zapełnieniu pól wyświetlanych w kroku 5 kliknij przycisk **Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z programem Zscaler. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto Zscaler ma uprawnienia administratora i spróbuj ponownie.

    ![Inicjowanie obsługi administracyjnej zscalera](./media/zscaler-provisioning-tutorial/test-connection.png)

8. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru **Wyślij powiadomienie e-mail po wystąpieniu błędu**.

    ![Inicjowanie obsługi administracyjnej zscalera](./media/zscaler-provisioning-tutorial/notification.png)

9. Kliknij przycisk **Zapisz**.

10. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z programem Zscaler**.

    ![Inicjowanie obsługi administracyjnej zscalera](./media/zscaler-provisioning-tutorial/user-mappings.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Zscaler w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w Zscaler dla operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Inicjowanie obsługi administracyjnej zscalera](./media/zscaler-provisioning-tutorial/user-attribute-mappings.png)

12. W sekcji **Mapowania** wybierz pozycję **Synchronizuj grupy usługi Azure Active Directory z programem Zscaler**.

    ![Inicjowanie obsługi administracyjnej zscalera](./media/zscaler-provisioning-tutorial/group-mappings.png)

13. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do Zscaler w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania grup w Zscaler dla operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Inicjowanie obsługi administracyjnej zscalera](./media/zscaler-provisioning-tutorial/group-attribute-mappings.png)

14. Aby skonfigurować filtry zakresu, zapoznaj się z poniższymi instrukcjami podanymi w [samouczku filtru zakresu](./../active-directory-saas-scoping-filters.md).

15. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla programu Zscaler, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Inicjowanie obsługi administracyjnej zscalera](./media/zscaler-provisioning-tutorial/provisioning-status.png)

16. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić Zscaler, wybierając żądane wartości w **zakresie** w sekcji **Ustawienia.**

    ![Inicjowanie obsługi administracyjnej zscalera](./media/zscaler-provisioning-tutorial/scoping.png)

17. Gdy będziesz gotowy do aprowienia, kliknij przycisk **Zapisz**.

    ![Inicjowanie obsługi administracyjnej zscalera](./media/zscaler-provisioning-tutorial/save-provisioning.png)

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile jest uruchomiona usługa inicjowania obsługi administracyjnej usługi Azure AD. Można użyć sekcji **Szczegóły synchronizacji** do monitorowania postępu i obserwowanie łączy do raportu aktywności inicjowania obsługi administracyjnej, który opisuje wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w programie Zscaler.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../active-directory-saas-provisioning-reporting.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-provisioning-tutorial/tutorial-general-03.png
