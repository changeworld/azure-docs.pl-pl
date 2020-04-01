---
title: 'Samouczek: Konfigurowanie programu Zscaler Three do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: W tym samouczku dowiesz się, jak skonfigurować usługę Azure Active Directory do automatycznego inicjowania obsługi administracyjnej i usuwania kont użytkowników usługi Zscaler Three.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 385a1153-0f47-4e41-8f44-da1b49d7629e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 177bc34162c2b5e4dadc54e1166c5f6061068bae
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77064108"
---
# <a name="tutorial-configure-zscaler-three-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie programu Zscaler Three do automatycznego inicjowania obsługi administracyjnej przez użytkowników

W tym samouczku dowiesz się, jak skonfigurować usługę Azure Active Directory (Azure AD) do automatycznego inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej użytkowników i/lub grup do programu Zscaler Three.

> [!NOTE]
> W tym samouczku opisano łącznik, który jest zbudowany na usłudze inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat tego, co robi ta usługa i jak działa, oraz odpowiedzi na często zadawane pytania, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym samouczku, potrzebujesz następujących czynności:

* Dzierżawa usługi Azure AD.
* Zscaler Trzy dzierżawy.
* Konto użytkownika w Zscaler Trzy z uprawnieniami administratora.

> [!NOTE]
> Integracja inicjowania obsługi administracyjnej usługi Azure AD zależy od interfejsu API SCIM Zscaler Zscaler, który jest dostępny dla kont Enterprise.

## <a name="adding-zscaler-three-from-the-gallery"></a>Dodawanie Zscaler Three z galerii

Przed skonfigurowaniem programu Zscaler Three do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD należy dodać program Zscaler Three z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

W [witrynie Azure portal](https://portal.azure.com)w lewym okienku wybierz pozycję **Azure Active Directory:**

![Wybierz pozycję Azure Active Directory](common/select-azuread.png)

Przejdź do **aplikacji enterprise,** a następnie wybierz **wszystkie aplikacje:**

![Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

Aby dodać aplikację, wybierz **pozycję Nowa aplikacja** w górnej części okna:

![Wybierz nową aplikację](common/add-new-app.png)

W polu wyszukiwania wpisz **Zscaler Three**. W wynikach wybierz pozycję **Zscaler Three,** a następnie wybierz pozycję **Dodaj**.

![Lista wyników](common/search-new-app.png)

## <a name="assign-users-to-zscaler-three"></a>Przypisywanie użytkowników do zscalera trzy

Użytkownicy usługi Azure AD muszą mieć przypisany dostęp do wybranych aplikacji, zanim będą mogli z nich korzystać. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy lub grupy, które są przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do programu Zscaler Three. Po podjęciu decyzji, że można przypisać tych użytkowników i grupy do Zscaler Trzy, postępując zgodnie z instrukcjami w [Przypisz użytkownika lub grupę do aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-zscaler-three"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Zscaler Three

* Zaleca się, aby najpierw przypisać jednego użytkownika usługi Azure AD do Zscaler Trzy, aby przetestować konfigurację automatycznego inicjowania obsługi administracyjnej użytkownika. Później można przypisać więcej użytkowników i grup.

* Po przypisaniu użytkownika do programu Zscaler Three należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisywania. Użytkownicy z rolą **dostępu domyślnego** są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="set-up-automatic-user-provisioning"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej użytkowników

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD w celu tworzenia, aktualizowania i wyłączania użytkowników i grup w programie Zscaler Three na podstawie przypisań użytkowników i grup w usłudze Azure AD.

> [!TIP]
> Można również włączyć samol oparte logowanie jednokrotne dla Zscaler Trzy. Jeśli to zrobisz, postępuj zgodnie z instrukcjami w [samouczku logowania jednokrotnego Zscaler Three](zscaler-three-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej użytkownika, ale dwie funkcje wzajemnie się uzupełniają.

1. Zaloguj się do [portalu Azure](https://portal.azure.com) i wybierz **aplikacje** > dla**przedsiębiorstw Wszystkie aplikacje** > **Zscaler Trzy:**

    ![Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Zscaler Trzy**:

    ![Lista aplikacji](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej:**

    ![Zscaler Trzy inicjowania obsługi administracyjnej](./media/zscaler-three-provisioning-tutorial/provisioning-tab.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **automatyczny:**

    ![Ustawianie trybu inicjowania obsługi administracyjnej](./media/zscaler-three-provisioning-tutorial/provisioning-credentials.png)

5. W sekcji **Poświadczenia administratora** wprowadź **adres URL dzierżawy** i **tajny token** konta Zscaler Three, zgodnie z opisem w następnym kroku.

6. Aby uzyskać **adres URL dzierżawy** i **token tajny,** przejdź do**ustawień uwierzytelniania** **administracyjnego** > w portalu Zscaler Three i wybierz **SAML** w obszarze **Typ uwierzytelniania:**

    ![Zscaler Trzy ustawienia uwierzytelniania](./media/zscaler-three-provisioning-tutorial/secret-token-1.png)

    Wybierz **opcję Konfiguruj SAML,** aby otworzyć okno **Konfigurowanie SAML:**

    ![Konfigurowanie okna SAML](./media/zscaler-three-provisioning-tutorial/secret-token-2.png)

    Wybierz **pozycję Włącz inicjowanie obsługi administracyjnej oparte na uściśle i** skopiuj podstawowy adres **URL** i **token nośnika,** a następnie zapisz ustawienia. W witrynie Azure portal wklej **podstawowy adres URL** w polu Adres URL **dzierżawy** i **tokenu nośnika** w polu **Token tajny.**

7. Po wprowadzeniu wartości w adresie **URL dzierżawy** i **tokenu tajnego** pola, wybierz **opcję Testuj połączenie,** aby upewnić się, że usługa Azure AD można połączyć się z Zscaler Trzy. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto Zscaler Three ma uprawnienia administratora i spróbuj ponownie.

    ![Testowanie połączenia](./media/zscaler-three-provisioning-tutorial/test-connection.png)

8. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej. Wybierz **pozycję Wyślij powiadomienie e-mail, gdy wystąpi błąd:**

    ![Konfigurowanie wiadomości e-mail z powiadomieniem](./media/zscaler-three-provisioning-tutorial/notification.png)

9. Wybierz **pozycję Zapisz**.

10. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z ZscalerThree:**

    ![Synchronizowanie użytkowników usługi Azure AD](./media/zscaler-three-provisioning-tutorial/user-mappings.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Zscaler Trzy w sekcji **Mapowania atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w Zscaler Trzy dla operacji aktualizacji. Wybierz **pozycję Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Mapowania atrybutów](./media/zscaler-three-provisioning-tutorial/user-attribute-mappings.png)

12. W sekcji **Mapowania** wybierz pozycję **Synchronizuj grupy usługi Azure Active Directory z ZscalerThree:**

    ![Synchronizowanie grup usługi Azure AD](./media/zscaler-three-provisioning-tutorial/group-mappings.png)

13. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do Zscaler Trzy w sekcji **Mapowania atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania grup w Zscaler Trzy dla operacji aktualizacji. Wybierz **pozycję Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Mapowania atrybutów](./media/zscaler-three-provisioning-tutorial/group-attribute-mappings.png)

14. Aby skonfigurować filtry zakresu, zapoznaj się z instrukcjami zawartymi w [samouczku filtru zakresu](./../active-directory-saas-scoping-filters.md).

15. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla programu Zscaler Three, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia:**

    ![Stan inicjowania obsługi administracyjnej](./media/zscaler-three-provisioning-tutorial/provisioning-status.png)

16. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić Zscaler Trzy, wybierając wartości, które chcesz w obszarze **zakresu** w sekcji **Ustawienia:**

    ![Wartości zakresu](./media/zscaler-three-provisioning-tutorial/scoping.png)

17. Gdy będziesz gotowy do aprowienia, wybierz pozycję **Zapisz:**

    ![Wybieranie pozycji Zapisz](./media/zscaler-three-provisioning-tutorial/save-provisioning.png)

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników i grup zdefiniowanych w obszarze **Zakres** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak usługa inicjowania obsługi administracyjnej usługi Azure AD jest uruchomiona. Postęp można monitorować w sekcji **Szczegóły synchronizacji.** Można również wykonać łącza do raportu aktywności inicjowania obsługi administracyjnej, który opisuje wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w programie Zscaler Three.

Aby uzyskać informacje na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../active-directory-saas-provisioning-reporting.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji przedsiębiorstwa](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-03.png
