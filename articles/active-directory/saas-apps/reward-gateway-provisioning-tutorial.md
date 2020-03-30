---
title: 'Samouczek: Konfigurowanie bramy nagród do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego aprowizowania i deekwowania kont użytkowników do bramy nagrody.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 928d48907e43de5e65ca5604ff878bfb83d5e95b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061004"
---
# <a name="tutorial-configure-reward-gateway-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie bramy nagród do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest zademonstrowanie kroków, które należy wykonać w usłudze Gateway i usłudze Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizowania i deekwowania użytkowników i/lub grup do bramy nagrody.

> [!NOTE]
> W tym samouczku opisano łącznik utworzony na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych warunków korzystania z platformy Microsoft Azure dla funkcji w wersji Zapoznawczej, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD.
* [Dzierżawa bramy nagrody](https://www.rewardgateway.com/).
* Konto użytkownika w bramie nagród z uprawnieniami administratora.

## <a name="assigning-users-to-reward-gateway"></a>Przypisywanie użytkowników do bramy nagród 

Usługa Azure Active Directory używa koncepcji o nazwie *przydziały,* aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do bramy nagrody. Po podjęciu decyzji można przypisać tych użytkowników i/lub grupy do bramy nagród, postępując zgodnie z instrukcjami w [aplikacji Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa.](../manage-apps/assign-user-or-group-access-portal.md)


## <a name="important-tips-for-assigning-users-to-reward-gateway"></a>Ważne wskazówki dotyczące przypisywania użytkowników do bramy nagród 

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do bramy nagrody w celu przetestowania konfiguracji automatycznego inicjowania obsługi administracyjnej użytkownika. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Podczas przypisywania użytkownika do bramy nagrody należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z rolą **dostępu domyślnego** są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="setup-reward-gateway--for-provisioning"></a>Konfigurowanie bramy nagród do inicjowania obsługi administracyjnej
Przed skonfigurowaniem bramy nagrody do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD należy włączyć inicjowanie obsługi administracyjnej scim w bramie nagrody.

1. Zaloguj się do [konsoli administracyjnej bramy nagród](https://rewardgateway.photoshelter.com/login/). Kliknij pozycję **Integracje**.

    ![Konsola administracyjna bramy nagród](media/reward-gateway-provisioning-tutorial/image00.png)

2.  Wybierz **pozycję Moja integracja**.

    ![Konsola administracyjna bramy nagród](media/reward-gateway-provisioning-tutorial/image001.png)

3.  Skopiuj wartości **adresu URL SCIM (v2)** i **tokenu nośnika OAuth**. Te wartości zostaną wprowadzone w adresie URL dzierżawy i klucz tajny token pola na karcie inicjowania obsługi administracyjnej aplikacji bramy nagrody w witrynie Azure portal.

    ![Konsola administracyjna bramy nagród](media/reward-gateway-provisioning-tutorial/image03.png)

## <a name="add-reward-gateway-from-the-gallery"></a>Dodawanie bramy nagród z galerii

Aby skonfigurować bramę nagród do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD, należy dodać bramę nagrody z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać bramę nagrody z galerii aplikacji usługi Azure AD, wykonaj następujące kroki:**

1. W **[witrynie Azure portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji enterprise**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** u góry okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **bramę nagród**, wybierz **bramę nagród** w panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![Brama nagród na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-reward-gateway"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej bramy nagród  

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD do tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w bramie nagród na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć logowanie jednokrotne oparte na SAML dla bramy nagrody, postępując zgodnie z instrukcjami podanymi w [samouczku logowania jednokrotnego bramy nagród.](reward-gateway-tutorial.md) Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej użytkownika, chociaż te dwie funkcje wzajemnie się uzupełniają.

### <a name="to-configure-automatic-user-provisioning-for-reward-gateway-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej bramy nagród w usłudze Azure AD:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **pozycję Brama nagród**.

    ![Łącze Brama nagród na liście Aplikacje](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning-automatic.png)

5. W sekcji **Poświadczenia administratora** wprowadź **adres URL SCIM (v2)** i wartości **tokenu nośnika OAuth** pobrane wcześniej odpowiednio w **adresie URL dzierżawy** i **tokenie tajnym.** Kliknij **przycisk Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z bramą nagrody. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto bramy nagrody ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru - **Wyślij powiadomienie e-mail, gdy wystąpi błąd.**

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Kliknij przycisk **Zapisz**.

8. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory, aby przegrodzić bramę**.

    ![Konsola administracyjna bramy nagród](media/reward-gateway-provisioning-tutorial/user-mappings.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do bramy nagrody w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **właściwości dopasowania** są używane do dopasowania kont użytkowników w bramie nagród do operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Konsola administracyjna bramy nagród](media/reward-gateway-provisioning-tutorial/user-attributes.png)

10. Aby skonfigurować filtry zakresu, zapoznaj się z poniższymi instrukcjami podanymi w [samouczku filtru zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla bramy nagrody, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Stan inicjowania obsługi administracyjnej włączony](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić bramie nagrody, wybierając żądane wartości w **zakresie** w sekcji **Ustawienia.**

    ![Zakres inicjowania obsługi administracyjnej](common/provisioning-scope.png)

13. Gdy będziesz gotowy do aprowienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji inicjowania obsługi administracyjnej](common/provisioning-configuration-save.png)

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile jest uruchomiona usługa inicjowania obsługi administracyjnej usługi Azure AD. Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i śledzić łącza do raportu aktywności inicjowania obsługi administracyjnej, w którym opisano wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w bramie nagrody.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Ograniczenia złącza

Brama nagród nie obsługuje obecnie inicjowania obsługi administracyjnej grupy.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

[Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)
