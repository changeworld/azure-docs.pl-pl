---
title: 'Samouczek: Inicjowanie obsługi administracyjnej dla usługi GitHub — usługa Azure AD'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego inicjowania obsługi administracyjnej i usuwania z obsługi administracyjnej kont użytkowników w usłudze GitHub.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82f7252f2d9cdd2c54fae593d8463bfe84bd6ce2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057655"
---
# <a name="tutorial-configure-github-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie usługi GitHub do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest pokazanie kroków, które należy wykonać w usłudze GitHub i usłudze Azure AD, aby automatycznie aprowizować i usuwać z nich konta użytkowników z usługi Azure AD do usługi GitHub.

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące elementy:

* Dzierżawa usługi Azure Active
* Organizacja usługi GitHub utworzona w [chmurze usługi GitHub dla przedsiębiorstw](https://help.github.com/articles/github-s-products/#github-enterprise), która wymaga [planu rozliczeniowego usługi GitHub Enterprise](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)
* Konto użytkownika w usłudze GitHub z uprawnieniami administratora do organizacji
* Upewnij się, że dostęp OAuth został udostępniony dla Twojej organizacji, jak opisano [tutaj](https://help.github.com/en/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization)

> [!NOTE]
> Integracja inicjowania obsługi administracyjnej usługi Azure AD opiera się na [interfejsie API SCIM usługi GitHub,](https://developer.github.com/v3/scim/)który jest dostępny dla klientów [usługi GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise) w planie [rozliczeniowym Usługi GitHub Enterprise.](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)

## <a name="assigning-users-to-github"></a>Przypisywanie użytkowników do usługi GitHub

Usługa Azure Active Directory używa pojęcia o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej konta użytkownika tylko użytkownicy i grupy, które zostały "przypisane" do aplikacji w usłudze Azure AD jest synchronizowana. 

Przed skonfigurowaniem i włączeniem usługi inicjowania obsługi administracyjnej należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do aplikacji GitHub. Po podjęciu decyzji możesz przypisać tych użytkowników do aplikacji GitHub, postępując zgodnie z instrukcjami tutaj:

[Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-github"></a>Ważne wskazówki dotyczące przypisywania użytkowników do usługi GitHub

* Zaleca się, że jeden użytkownik usługi Azure AD jest przypisany do usługi GitHub, aby przetestować konfigurację inicjowania obsługi administracyjnej. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Podczas przypisywania użytkownika do usługi GitHub należy wybrać rolę **użytkownika** lub inną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Rola **domyślnego dostępu** nie działa w przypadku inicjowania obsługi administracyjnej, a ci użytkownicy są pomijane.

## <a name="configuring-user-provisioning-to-github"></a>Konfigurowanie inicjowania obsługi administracyjnej użytkownika w usłudze GitHub

W tej sekcji można zainicjować połączenie usługi Azure AD z interfejsem API inicjowania obsługi administracyjnej konta użytkownika usługi GitHub i konfigurowanie usługi inicjowania obsługi administracyjnej w celu tworzenia, aktualizowania i wyłączania przypisanych kont użytkowników w usłudze GitHub na podstawie przypisania użytkownika i grupy w usłudze Azure AD.

> [!TIP]
> Można również włączyć samooczyszczone logowanie jednokrotne dla usługi GitHub oparte na saml, postępując zgodnie z instrukcjami podanymi w [witrynie Azure portal.](https://portal.azure.com) Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej, chociaż te dwie funkcje wzajemnie się uzupełniają.

### <a name="configure-automatic-user-account-provisioning-to-github-in-azure-ad"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika w usłudze GitHub w usłudze Azure AD

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do sekcji **Azure Active Directory > Enterprise Apps > wszystkie aplikacje.**

2. Jeśli system GitHub został już skonfigurowany do logowania jednokrotnego, wyszukaj wystąpienie usługi GitHub przy użyciu pola wyszukiwania. W przeciwnym razie wybierz pozycję **Dodaj** i wyszukaj **gitHub** w galerii aplikacji. Wybierz GitHub z wyników wyszukiwania i dodaj go do listy aplikacji.

3. Wybierz wystąpienie gitHub, a następnie wybierz kartę **Inicjowanie obsługi administracyjnej.**

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Inicjowanie obsługi administracyjnej usługi GitHub](./media/github-provisioning-tutorial/GitHub1.png)

5. W sekcji **Poświadczenia administratora** kliknij pozycję **Autoryzuj**. Ta operacja otwiera okno dialogowe autoryzacji GitHub w nowym oknie przeglądarki. Należy pamiętać, że należy upewnić się, że są zatwierdzone do autoryzowania dostępu. Postępuj zgodnie ze wskazówkami opisanymi [tutaj](https://help.github.com/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization).

6. W nowym oknie zaloguj się do usługi GitHub przy użyciu konta administratora. W oknie dialogowym autoryzacji wynikowej wybierz zespół GitHub, dla którego chcesz włączyć inicjowanie obsługi administracyjnej, a następnie wybierz pozycję **Autoryzuj**. Po zakończeniu wróć do witryny Azure Portal, aby ukończyć konfigurację inicjowania obsługi administracyjnej.

    ![Okno dialogowe autoryzacji](./media/github-provisioning-tutorial/GitHub2.png)

7. W witrynie Azure portal wprowadź **adres URL dzierżawy** i kliknij przycisk **Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z aplikacją GitHub. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto GitHub ma uprawnienia administratora, a **url dzierżawy** jest poprawnie wprowadzone, a następnie spróbuj ponownie wykonać krok "Autoryzuj" (możesz stanowić **adres URL dzierżawy** według zasady: `https://api.github.com/scim/v2/organizations/<Organization_name>`, możesz znaleźć swoje organizacje pod swoim kontem GitHub: **Ustawienia** > **organizacji**).

    ![Okno dialogowe autoryzacji](./media/github-provisioning-tutorial/GitHub3.png)

8. Wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej w polu **Wiadomości e-mail powiadomień,** i zaznacz pole wyboru "Wyślij powiadomienie e-mail w przypadku wystąpienia błędu".

9. Kliknij przycisk **Zapisz**.

10. W sekcji Mapowania wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z usługą GitHub**.

11. W sekcji **Mapowania atrybutów** przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do gitHub. Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w usłudze GitHub do operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić wszelkie zmiany.

12. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla usługi GitHub, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia**

13. Kliknij przycisk **Zapisz**.

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup przypisanych do usługi GitHub w sekcji Użytkownicy i grupy. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile usługa jest uruchomiona. Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i obserwować łącza do dzienników działań inicjowania obsługi administracyjnej, które opisują wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)
