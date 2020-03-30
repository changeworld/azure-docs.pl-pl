---
title: 'Samouczek: Konfigurowanie wersji Beta programu Zscaler do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego inicjowania obsługi administracyjnej i usuwania z aprowizowania kont użytkowników do wersji Beta programu Zscaler.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 83db6b8d-503b-48f3-b918-f9fba1369d53
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 505cd7a3350c937885f0f03268ef326a4f784258
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062743"
---
# <a name="tutorial-configure-zscaler-beta-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie wersji beta programu Zscaler do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest zademonstrowanie kroków, które należy wykonać w wersji Beta i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizowania i deekwowania użytkowników i/lub grup do wersji Beta programu Zscaler.

> [!NOTE]
> W tym samouczku opisano łącznik utworzony na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../active-directory-saas-app-provisioning.md).
>


## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące czynności:

* Dzierżawa usługi Azure AD
* Dzierżawa Zscaler Beta
* Konto użytkownika w wersji Beta programu Zscaler z uprawnieniami administratora

> [!NOTE]
> Integracja inicjowania obsługi administracyjnej usługi Azure AD opiera się na interfejsie API SCIM beta programu Zscaler, który jest dostępny dla deweloperów Zscaler Beta dla kont z pakietem Enterprise.

## <a name="adding-zscaler-beta-from-the-gallery"></a>Dodawanie usługi Zscaler Beta z galerii

Przed skonfigurowaniem Zscaler Beta do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD należy dodać wersję Beta programu Zscaler z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać wersję Beta programu Zscaler z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Zscaler Beta**, wybierz pozycję **Zscaler Beta** na panelu wyników i kliknij przycisk **Dodaj**, aby dodać aplikację.

    ![Usługa Zscaler Beta na liście wyników](common/search-new-app.png)

## <a name="assigning-users-to-zscaler-beta"></a>Przypisywanie użytkowników do wersji Beta programu Zscaler

Usługa Azure Active Directory używa pojęcia o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy i/lub grupy, które zostały "przypisane" do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do wersji Beta programu Zscaler. Po podjęciu decyzji możesz przypisać tych użytkowników i/lub grupy do Zscaler Beta, postępując zgodnie z instrukcjami tutaj:

* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler-beta"></a>Ważne wskazówki dotyczące przypisywania użytkowników do wersji beta Zscaler

* Zaleca się, że jeden użytkownik usługi Azure AD jest przypisany do wersji beta Zscaler, aby przetestować konfigurację automatycznego inicjowania obsługi administracyjnej użytkownika. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Podczas przypisywania użytkownika do wersji Beta programu Zscaler należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z rolą **dostępu domyślnego** są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="configuring-automatic-user-provisioning-to-zscaler-beta"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej dla wersji beta programu Zscaler

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD do tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w wersji Beta Zscaler na podstawie przypisaniów użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć jednokrotne logowanie oparte na SAML dla Zscaler Beta, postępując zgodnie z instrukcjami podanymi w [samouczku logowania jednokrotnego Zscaler Beta.](zscaler-beta-tutorial.md) Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej użytkownika, chociaż te dwie funkcje wzajemnie się uzupełniają.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-beta-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej dla wersji Beta zscaler w usłudze Azure AD:

1. Zaloguj się do [portalu Azure](https://portal.azure.com) i wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję Wszystkie **aplikacje**, a następnie wybierz **pozycję Zscaler Beta**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Zscaler Beta**.

    ![Link usługi Zscaler Beta na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Aprowowanie wersji beta zscaler](./media/zscaler-beta-provisioning-tutorial/provisioning-tab.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Aprowowanie wersji beta zscaler](./media/zscaler-beta-provisioning-tutorial/provisioning-credentials.png)

5. W sekcji **Poświadczenia administratora** wprowadź **adres URL dzierżawy** i **tajny token** konta Beta zscaler, zgodnie z opisem w kroku 6.

6. Aby uzyskać **adres URL dzierżawy** i **token tajny,** przejdź do **ustawień uwierzytelniania > administracji** w interfejsie użytkownika portalu Zscaler Beta i kliknij **saml** w obszarze **Typ uwierzytelniania**.

    ![Aprowowanie wersji beta zscaler](./media/zscaler-beta-provisioning-tutorial/secret-token-1.png)

    Kliknij **przycisk Konfiguruj SAML,** aby otworzyć opcje **SAML konfiguracji.**

    ![Aprowowanie wersji beta zscaler](./media/zscaler-beta-provisioning-tutorial/secret-token-2.png)

    Wybierz **pozycję Włącz inicjowanie obsługi administracyjnej oparte na uściśle,** aby pobrać **podstawowy adres URL** i **token nośnika,** a następnie zapisz ustawienia. Skopiuj **podstawowy adres URL** do adresu URL **dzierżawy**i **tokenu nośnika** do **tokenu tajnego** w witrynie Azure portal.

7. Po zapełnieniu pól wyświetlanych w kroku 5 kliknij przycisk **Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z platformą Zscaler Beta. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto Zscaler Beta ma uprawnienia administratora i spróbuj ponownie.

    ![Aprowowanie wersji beta zscaler](./media/zscaler-beta-provisioning-tutorial/test-connection.png)

8. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru **Wyślij powiadomienie e-mail po wystąpieniu błędu**.

    ![Aprowowanie wersji beta zscaler](./media/zscaler-beta-provisioning-tutorial/notification.png)

9. Kliknij przycisk **Zapisz**.

10. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z platformą Beta programu Zscaler**.

    ![Aprowowanie wersji beta zscaler](./media/zscaler-beta-provisioning-tutorial/user-mappings.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do wersji Beta Zscaler w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w wersji Beta Zscaler dla operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Aprowowanie wersji beta zscaler](./media/zscaler-beta-provisioning-tutorial/user-attribute-mappings.png)

12. W sekcji **Mapowania** wybierz pozycję **Synchronizuj grupy usługi Azure Active Directory z platformą Beta programu Zscaler**.

    ![Aprowowanie wersji beta zscaler](./media/zscaler-beta-provisioning-tutorial/group-mappings.png)

13. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do wersji Beta Zscaler w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania grup w wersji beta Zscaler do operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Aprowowanie wersji beta zscaler](./media/zscaler-beta-provisioning-tutorial/group-attribute-mappings.png)

14. Aby skonfigurować filtry zakresu, zapoznaj się z poniższymi instrukcjami podanymi w [samouczku filtru zakresu](./../active-directory-saas-scoping-filters.md).

15. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla wersji beta programu Zscaler, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Aprowowanie wersji beta zscaler](./media/zscaler-beta-provisioning-tutorial/provisioning-status.png)

16. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić Zscaler Beta, wybierając żądane wartości w **zakresie** w sekcji **Ustawienia.**

    ![Aprowowanie wersji beta zscaler](./media/zscaler-beta-provisioning-tutorial/scoping.png)

17. Gdy będziesz gotowy do aprowienia, kliknij przycisk **Zapisz**.

    ![Aprowowanie wersji beta zscaler](./media/zscaler-beta-provisioning-tutorial/save-provisioning.png)

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile jest uruchomiona usługa inicjowania obsługi administracyjnej usługi Azure AD. Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i śledzić łącza do raportu aktywności inicjowania obsługi administracyjnej, w którym opisano wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w wersji Beta programu Zscaler.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../active-directory-saas-provisioning-reporting.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-03.png
