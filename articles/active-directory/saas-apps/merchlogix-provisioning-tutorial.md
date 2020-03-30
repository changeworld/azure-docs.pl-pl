---
title: 'Samouczek: Konfigurowanie produktu MerchLogix do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego inicjowania obsługi administracyjnej i usuwania z obsługi administracyjnej kont użytkowników na platformie MerchLogix.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: 9df4c7c5-9a58-478e-93b7-2f77aae12807
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: zhchia
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4aa60fb565552961a3c85346c39c318a90c8adc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061332"
---
# <a name="tutorial-configure-merchlogix-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie merchlogix do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest zademonstrowanie kroków, które należy wykonać w merchlogix i usługi Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD do automatycznego inicjowania obsługi administracyjnej i de-provision użytkowników i/lub grup do MerchLogix.

> [!NOTE]
> W tym samouczku opisano łącznik utworzony na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* Najemca MerchLogix
* Kontakt techniczny w firmie MerchLogix, który może podać adres URL punktu końcowego SCIM i tajny token wymagany do inicjowania obsługi administracyjnej przez użytkownika

## <a name="adding-merchlogix-from-the-gallery"></a>Dodawanie MerchLogix z galerii

Przed skonfigurowaniem MerchLogix do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD należy dodać usługę MerchLogix z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać program MerchLogix z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[witrynie Azure portal](https://portal.azure.com)**, w lewym panelu nawigacyjnym kliknij ikonę **usługi Azure Active Directory.** 

    ![Przycisk Azure Active Directory][1]

2. Przejdź do **aplikacji** > Enterprise**Wszystkie aplikacje**.

    ![Sekcja Aplikacje dla przedsiębiorstw][2]

3. Aby dodać merchlogix, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

4. W polu wyszukiwania wpisz **MerchLogix**.

5. W panelu wyników wybierz pozycję **MerchLogix**, a następnie kliknij przycisk **Dodaj,** aby dodać MerchLogix do listy aplikacji SaaS.

    ![Inicjowanie obsługi administracyjnej merchlogix][4]

## <a name="assigning-users-to-merchlogix"></a>Przypisywanie użytkowników do produktu MerchLogix

Usługa Azure Active Directory używa pojęcia o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy i/lub grupy, które zostały "przypisane" do aplikacji w usłudze Azure AD są synchronizowane. 

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do usługi MerchLogix. Po podjęciu decyzji, można przypisać tych użytkowników i / lub grup do MerchLogix, postępując zgodnie z instrukcjami tutaj:

* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-merchlogix"></a>Ważne wskazówki dotyczące przypisywania użytkowników do MerchLogix

* Zaleca się, że jeden użytkownik usługi Azure AD jest przypisany do MerchLogix do testowania początkowej konfiguracji automatycznego inicjowania obsługi administracyjnej użytkownika. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później po pomyślnym zakończeniu testów.

* Podczas przypisywania użytkownika do MerchLogix, należy wybrać dowolną prawidłową rolę specyficzne dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z rolą **dostępu domyślnego** są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="configuring-automatic-user-provisioning-to-merchlogix"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej dla produktu MerchLogix 

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD do tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w Programie MerchLogix na podstawie przypisaniów użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Można również włączyć samooceny jednokrotne oparte na SAML dla MerchLogix, postępując zgodnie z instrukcjami podanymi w [samouczku merchlogix logowania jednokrotnego](merchlogix-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej użytkownika, chociaż te dwie funkcje wzajemnie się uzupełniają.

### <a name="to-configure-automatic-user-provisioning-for-merchlogix-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej dla usługi MerchLogix w usłudze Azure AD:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do **aplikacji Usługi Azure Active Directory > Enterprise > wszystkie aplikacje**.

2. Wybierz MerchLogix z listy aplikacji SaaS.

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Inicjowanie obsługi administracyjnej merchlogix](./media/merchlogix-provisioning-tutorial/Merchlogix1.png)

5. W sekcji **Poświadczenia administratora:**

    * W polu **Adres URL dzierżawy** wprowadź adres URL punktu końcowego SCIM podany przez kontakt techniczny merchlogix.

    * W polu **Tajny token** wprowadź tajny token dostarczony przez twój kontakt techniczny MerchLogix.

6. Po zapełnieniu pól wyświetlanych w kroku 5 kliknij przycisk **Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z usługą MerchLogix. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto MerchLogix ma uprawnienia administratora i spróbuj ponownie.

7. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru - **Wyślij powiadomienie e-mail, gdy wystąpi błąd.**

8. Kliknij przycisk **Zapisz**.

9. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z merchlogix**.

10. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do MerchLogix w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w MerchLogix dla operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

11. W sekcji **Mapowania** wybierz pozycję **Synchronizuj grupy usługi Azure Active Directory z merchlogixem**.

12. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do MerchLogix w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania grup w MerchLogix dla operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

13. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla merchlogix, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

14. Gdy będziesz gotowy do aprowienia, kliknij przycisk **Zapisz**.

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile jest uruchomiona usługa inicjowania obsługi administracyjnej usługi Azure AD. Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i śledzić łącza do raportu aktywności inicjowania obsługi administracyjnej, w którym opisano wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w programie MerchLogix.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: common/select-azuread.png
[2]: common/enterprise-applications.png
[3]: common/add-new-app.png
[4]: common/search-new-app.png
