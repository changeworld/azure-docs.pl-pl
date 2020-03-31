---
title: 'Samouczek: Konfigurowanie funkcji MindTickle do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego inicjowania obsługi administracyjnej i usuwania obsługi konta użytkowników mindtickle.
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
ms.date: 07/23/2019
ms.author: Zhchia
ms.openlocfilehash: 6ae7b6ac9c6feb539e4da3627ebbabd3c5b628fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061241"
---
# <a name="tutorial-configure-mindtickle-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie mindtickle do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest zademonstrowanie kroków, które należy wykonać w MindTickle i usłudze Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD do automatycznego inicjowania obsługi administracyjnej i deekwowania użytkowników i/lub grup do MindTickle.

> [!NOTE]
> W tym samouczku opisano łącznik utworzony na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych warunków korzystania z platformy Microsoft Azure dla funkcji w wersji Zapoznawczej, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Najemca MindTickle](https://www.mindtickle.com/)
* Konto użytkownika w MindTickle z uprawnieniami administratora.

## <a name="assigning-users-to-mindtickle"></a>Przypisywanie użytkowników do MindTickle

Usługa Azure Active Directory używa koncepcji o nazwie *przydziały,* aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do MindTickle. Po podjęciu decyzji, można przypisać tych użytkowników i / lub grup do MindTickle, postępując zgodnie z instrukcjami tutaj:
* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-mindtickle"></a>Ważne wskazówki dotyczące przypisywania użytkowników do MindTickle

* Zaleca się, że jeden użytkownik usługi Azure AD jest przypisany do MindTickle do testowania konfiguracji automatycznego inicjowania obsługi administracyjnej użytkownika. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Podczas przypisywania użytkownika do MindTickle, należy wybrać dowolną prawidłową rolę specyficzne dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z rolą **dostępu domyślnego** są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="setup-mindtickle-for-provisioning"></a>Konfigurowanie MindTickle do inicjowania obsługi administracyjnej

Przed skonfigurowaniem MindTickle do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD należy włączyć inicjowanie obsługi administracyjnej scim na MindTickle.


1.  Skontaktuj się z [zespołem pomocy technicznej MindTickle,](mailto:help@mindtickle.com) aby uzyskać token JWT potrzebny do skonfigurowania inicjowania obsługi administracyjnej scim.


## <a name="add-mindtickle-from-the-gallery"></a>Dodaj MindTickle z galerii

Aby skonfigurować MindTickle do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD, należy dodać MindTickle z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać MindTickle z galerii aplikacji usługi Azure AD, wykonaj następujące kroki:**

1. W **[witrynie Azure portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji enterprise**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** u góry okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **mindtickle**, wybierz **MindTickle** w panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![Aplikacja MindTickle na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-mindtickle"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej w mindtickle 

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD do tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w MindTickle na podstawie przypisania użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Można również włączyć samooceny jednokrotne oparte na SAML dla MindTickle , postępując zgodnie z instrukcjami podanymi w [MindTickle Samouczek logowania jednokrotnego.](mindtickle-tutorial.md) Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej przez użytkowników, chociaż te dwie funkcje wzajemnie się uzupełniają

### <a name="to-configure-automatic-user-provisioning-for-mindtickle-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej dla mindtickle w usłudze Azure AD:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **MindTickle**.

    ![Link do aplikacji MindTickle na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning-automatic.png)

5. W sekcji **Poświadczenia administratora** wprowadź w `https://admin.mindtickle.com/scim` **adresie URL dzierżawy**. Wprowadź wartość **tokenu JWT** pobraną wcześniej w polu tekstowym tajnego tokenu wprowadź wartość **tokenu JWT,** która została podana przez zespół pomocy technicznej MindTickle. Kliknij **przycisk Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z myPolicies. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto MindTickle ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru - **Wyślij powiadomienie e-mail, gdy wystąpi błąd.**

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Kliknij przycisk **Zapisz**.

8. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z mindtickle**.

    ![Mapowania użytkowników MindTickle](media/mindtickle-provisioning-tutorial/usermapping.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do MindTickle w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w MindTickle dla operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Mapowania użytkowników MindTickle](media/mindtickle-provisioning-tutorial/userattribute.png)

12. Aby skonfigurować filtry zakresu, zapoznaj się z poniższymi instrukcjami podanymi w [samouczku filtru zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla MindTickle, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Stan inicjowania obsługi administracyjnej włączony](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić MindTickle, wybierając żądane wartości w **zakresie** w sekcji **Ustawienia.**

    ![Zakres inicjowania obsługi administracyjnej](common/provisioning-scope.png)

15. Gdy będziesz gotowy do aprowienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji inicjowania obsługi administracyjnej](common/provisioning-configuration-save.png)

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje. Aby uzyskać więcej informacji na temat czasu działania użytkowników i/lub grup w celu udostępnienia, zobacz [Jak długo potrwa aprowizję użytkowników](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users). 

Możesz użyć sekcji **Bieżący stan** do monitorowania postępu i obserwowanie łączy do raportu aktywności inicjowania obsługi administracyjnej, w którym opisano wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w mindtickle. Aby uzyskać więcej informacji, zobacz [Sprawdzanie stanu inicjowania obsługi administracyjnej przez użytkowników](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Aby zapoznać się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
