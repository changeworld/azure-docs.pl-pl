---
title: 'Samouczek: Konfigurowanie funkcji proxyclick do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej kont użytkowników w witrynie Proxyclick.
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
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: 95cb0371c4b2181d8f09991fe6e652c0e939f3e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063360"
---
# <a name="tutorial-configure-proxyclick-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie proxyclick do automatycznego inicjowania obsługi administracyjnej użytkownika

Celem tego samouczka jest zademonstrowanie kroków, które należy wykonać w proxyclick i usługi Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD do automatycznego aprowizowania i de-provision użytkowników i/lub grup do proxyclick.

> [!NOTE]
> W tym samouczku opisano łącznik utworzony na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych warunków korzystania z platformy Microsoft Azure dla funkcji w wersji Zapoznawczej, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawa proxyclick](https://www.proxyclick.com/pricing)
* Konto użytkownika w proxyclick z uprawnieniami administratora.

## <a name="add-proxyclick-from-the-gallery"></a>Dodaj proxyclick z galerii

Przed skonfigurowaniem proxyclick do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD, należy dodać proxyclick z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać proxyclick z galerii aplikacji usługi Azure AD, wykonaj następujące kroki:**

1. W **[witrynie Azure portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji enterprise**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** u góry okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **proxyclick**, wybierz **Proxyclick** w panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![Proxyclick na liście wyników](common/search-new-app.png)

## <a name="assigning-users-to-proxyclick"></a>Przypisywanie użytkowników do proxyclick

Usługa Azure Active Directory używa koncepcji o nazwie *przydziały,* aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do funkcji Proxyclick. Po podjęciu decyzji, można przypisać tych użytkowników i / lub grup do Proxyclick, postępując zgodnie z instrukcjami tutaj:

* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-proxyclick"></a>Ważne wskazówki dotyczące przypisywania użytkowników do proxyclick

* Zaleca się, że jeden użytkownik usługi Azure AD jest przypisany do proxyclick do testowania konfiguracji automatycznego inicjowania obsługi administracyjnej użytkownika. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Podczas przypisywania użytkownika do aplikacji Proxyclick należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z rolą **dostępu domyślnego** są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="configuring-automatic-user-provisioning-to-proxyclick"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej w trybie proxyclick 

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD do tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w proxyclick na podstawie przypisania użytkownika i/lub grupy w usłudze Azure AD.

> [!TIP]
> Można również włączyć samoocenę jednokrotną opartą na SAML dla proxyclick, postępując zgodnie z instrukcjami podanymi w [samouczku logowania jednokrotnego proxyclick](proxyclick-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej użytkownika, chociaż te dwie funkcje wzajemnie się uzupełniają.

### <a name="to-configure-automatic-user-provisioning-for-proxyclick-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej dla proxyclick w usłudze Azure AD:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Proxyclick**.

    ![Łącze Proxyclick na liście Aplikacje](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning-automatic.png)

5. Aby pobrać **adres URL dzierżawy** i **tajny token** konta proxyclick, postępuj zgodnie z instruktażem, jak opisano w kroku 6.

6. Zaloguj się do [konsoli administracyjnej proxyclick](https://app.proxyclick.com/login//?destination=%2Fdefault). Przejdź do **ustawień** > **Integracje** > **Przeglądaj Marketplace**.

    ![Ustawienia proxyclick](media/proxyclick-provisioning-tutorial/proxyclick09.png)

    ![Integracje proxyclick](media/proxyclick-provisioning-tutorial/proxyclick01.png)

    ![Proxyclick Marketplace](media/proxyclick-provisioning-tutorial/proxyclick02.png)

    Wybierz **usługę Azure AD**. Kliknij **pozycję Zainstaluj teraz**.

    ![Proxyclick Azure AD](media/proxyclick-provisioning-tutorial/proxyclick03.png)

    ![Proxyclick zainstalować](media/proxyclick-provisioning-tutorial/proxyclick04.png)

    Wybierz **pozycję Inicjowanie obsługi administracyjnej użytkownika** i kliknij przycisk **Rozpocznij integrację**. 

    ![Zainicjowanie obsługi administracyjnej użytkownika zaliczania obsługi administracyjnej przez użytkownika w proxy](media/proxyclick-provisioning-tutorial/proxyclick05.png)

    W obszarze**Integracje**ustawień powinien być teraz pokazywał się odpowiedni interfejs użytkownika konfiguracji **ustawień** > . Wybierz **pozycję Ustawienia** w obszarze Azure AD **(Inicjowanie obsługi administracyjnej)**.

    ![Proxyclick Tworzenie](media/proxyclick-provisioning-tutorial/proxyclick06.png)

    Adres URL **dzierżawy** i **token tajny** można znaleźć tutaj.

    ![Proxyclick Utwórz token](media/proxyclick-provisioning-tutorial/proxyclick07.png)

7. Po zapełnieniu pól wyświetlanych w kroku 5 kliknij przycisk **Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z żądaniem proxy. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto proxyclick ma uprawnienia administratora i spróbuj ponownie.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

8. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru - **Wyślij powiadomienie e-mail, gdy wystąpi błąd.**

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

9. Kliknij przycisk **Zapisz**.

10. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z poleceniem proxyclick**.

    ![Mapowania użytkowników proxyclick](media/proxyclick-provisioning-tutorial/Proxyclick-user-mappings.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do proxyclick w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w proxyclick dla operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Proxyclick atrybuty użytkownika](media/proxyclick-provisioning-tutorial/Proxyclick-user-attribute.png)

13. Aby skonfigurować filtry zakresu, zapoznaj się z poniższymi instrukcjami podanymi w [samouczku filtru zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla proxyclick, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Stan inicjowania obsługi administracyjnej włączony](common/provisioning-toggle-on.png)

15. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić proxyclick, wybierając żądane wartości w **zakresie** w **ustawieniach** sekcji.

    ![Zakres inicjowania obsługi administracyjnej](common/provisioning-scope.png)

16. Gdy będziesz gotowy do aprowienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji inicjowania obsługi administracyjnej](common/provisioning-configuration-save.png)

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile jest uruchomiona usługa inicjowania obsługi administracyjnej usługi Azure AD. Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i śledzić łącza do raportu aktywności inicjowania obsługi administracyjnej, w którym opisano wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w witrynie Proxyclick.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Ograniczenia złącza

* Proxyclick wymaga **wiadomości e-mail** i **userName** mieć taką samą wartość źródła. Wszelkie aktualizacje do obu atrybutów zmodyfikuje inną wartość.
* Proxyclick nie obsługuje inicjowania obsługi administracyjnej dla grup.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)

