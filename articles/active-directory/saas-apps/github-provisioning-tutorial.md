---
title: 'Samouczek: Konfigurowanie usługi GitHub dla użytkownika automatycznego inicjowania obsługi administracyjnej z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory, aby automatycznie zapewnianie i usuwanie kont użytkowników do usługi GitHub.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: mtillman
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser-msft
ms.openlocfilehash: a7f4077f243d8d09483e36a77d58d546a91f6498
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35938434"
---
# <a name="tutorial-configure-github-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie GitHub dla użytkownika automatycznego inicjowania obsługi administracyjnej.


Celem tego samouczka jest opisano czynności, które należy wykonać w witrynie GitHub i usługi Azure AD, aby automatycznie zapewnianie i usuwanie kont użytkowników z usługi Azure AD do usługi GitHub. 

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że już następujące elementy:

*   Dzierżawy usługi Azure Active directory
*   Dzierżawcy Github z [planu firm](https://help.github.com/articles/organization-billing-plans/#business-plan) lub lepiej jest włączone 
*   Konto użytkownika z uprawnieniami administratora w usłudze GitHub 

> [!NOTE]
> Zależy od usługi Azure AD, inicjowania obsługi administracyjnej integracji [GitHub SCIM API](https://developer.github.com/v3/scim/), który jest dostępny dla zespołów Github dla planu biznesowych lub większą.

## <a name="assigning-users-to-github"></a>Przypisywanie użytkowników do usługi GitHub

Usługi Azure Active Directory używa pojęcie o nazwie "przypisania" w celu określenia, którzy użytkownicy powinien otrzymać dostęp do wybranej aplikacji. W kontekście użytkownika automatyczne Inicjowanie obsługi konta tylko użytkownicy i grupy, które "przypisano" do aplikacji w usłudze Azure AD jest zsynchronizowany. 

Przed Skonfiguruj i włącz usługę inicjowania obsługi administracyjnej, należy zdecydować, jakie użytkownicy i/lub grup w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do aplikacji GitHub. Po decyzję, postępując zgodnie z instrukcjami w tym miejscu można przypisać tych użytkowników do aplikacji GitHub:

[Przypisanie użytkownika lub grupę do aplikacji w przedsiębiorstwie](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-github"></a>Ważne porady dotyczące przypisywania użytkowników do usługi GitHub

*   Zalecane jest pojedynczego użytkownika usługi Azure AD jest przypisana do GitHub, aby przetestować konfigurację inicjowania obsługi administracyjnej. Później można przypisać dodatkowych użytkowników i/lub grup.

*   Przypisanie użytkownika do witryny GitHub, należy wybrać opcję **użytkownika** rola, lub inny prawidłowy specyficzne dla aplikacji (jeśli jest dostępny) w oknie dialogowym przypisania. **Domyślnego dostępu** roli nie działa w przypadku inicjowania obsługi administracyjnej, a użytkownicy są pomijane.


## <a name="configuring-user-provisioning-to-github"></a>Konfigurowanie Inicjowanie obsługi użytkowników do usługi GitHub 

Ta sekcja przeprowadzi Cię przez łączenie usługi Azure AD z konta użytkownika GitHub inicjowania obsługi interfejsu API i konfigurowanie inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączania konta użytkowników przypisane w usłudze GitHub, w oparciu o przypisania użytkowników i grup w usłudze Azure AD.

> [!TIP]
> Można też włączone na języku SAML logowania jednokrotnego dla GitHub, postępując zgodnie z instrukcjami zawarte w [portalu Azure](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej, chociaż te dwie funkcje uzupełniania siebie nawzajem.


### <a name="configure-automatic-user-account-provisioning-to-github-in-azure-ad"></a>Skonfiguruj automatyczne konta Inicjowanie obsługi użytkowników do usługi GitHub w usłudze Azure AD


1. W [portalu Azure](https://portal.azure.com), przejdź do **usługi Azure Active Directory > aplikacje przedsiębiorstwa > wszystkie aplikacje** sekcji.

2. Jeśli skonfigurowano już program GitHub dla logowania jednokrotnego, wyszukaj dla swojego wystąpienia usługi GitHub, za pomocą pola wyszukiwania. W przeciwnym razie wybierz **Dodaj** i wyszukaj **GitHub** w galerii aplikacji. Wybierz GitHub z wyników wyszukiwania, a następnie dodaj go do listy aplikacji.

3. Wybierz wystąpienie usługi GitHub, a następnie wybierz **inicjowania obsługi administracyjnej** kartę.

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Inicjowanie obsługi usługi GitHub](./media/github-provisioning-tutorial/GitHub1.png)

5. W obszarze **poświadczeń administratora** kliknij **autoryzacji**. Ta operacja otwiera okno dialogowe autoryzacji GitHub w nowym oknie przeglądarki. 

6. W nowym oknie Zaloguj się do usługi GitHub przy użyciu konta administratora. W oknie dialogowym autoryzacji wynikowy, wybierz zespół GitHub, który chcesz włączyć udostępnianie, a następnie wybierz **autoryzacji**. Po ukończeniu, wróć do portalu Azure, aby zakończyć konfigurację inicjowania obsługi administracyjnej.

    ![Okno dialogowe autoryzacji](./media/github-provisioning-tutorial/GitHub2.png)

7. W portalu Azure, wprowadź **adres URL dzierżawy** i kliknij przycisk **Testuj połączenie** zapewniające usługi Azure AD mogą łączyć się z aplikacji GitHub. Jeśli połączenie nie powiedzie się, upewnij się, Twoje konto GitHub ma uprawnienia administratora i **adres URl dzierżawy** jest wprowadzona poprawnie, a następnie ponownie spróbuj kroku "Autoryzuj" (może stanowić **adres URL dzierżawy** przez regułę: "https://api.github.com/scim/v2/organizations/ + < Organizations_name > ", można znaleźć Twojej organizacji na koncie usługi GitHub: **ustawienia** > **organizacji**).

    ![Okno dialogowe autoryzacji](./media/github-provisioning-tutorial/GitHub3.png)

8. Wprowadź adres e-mail osoby lub grupy, który powinien zostać wyświetlony inicjowania obsługi administracyjnej powiadomienia o błędach w **wiadomość E-mail z powiadomieniem** pola, a następnie zaznacz pole wyboru "Wyślij wiadomość e-mail z powiadomieniem, gdy wystąpi błąd".

9. Kliknij pozycję **Zapisz**. 

10. W sekcji mapowania wybierz **synchronizacji Azure Active Directory użytkownikom GitHub**.

11. W **mapowań atrybutów** Przejrzyj atrybuty użytkowników, które są synchronizowane z usługi Azure AD do usługi GitHub. Atrybuty wybrany jako **pasujące** właściwości są używane do dopasowania kont użytkowników w usłudze GitHub dla operacji update. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

12. Aby włączyć usługi Azure AD, inicjowania obsługi usługi GitHub, zmień **stan inicjowania obsługi administracyjnej** do **na** w **ustawienia** sekcji

13. Kliknij pozycję **Zapisz**. 

Ta operacja uruchamia wstępnej synchronizacji użytkowników i/lub grupy przypisane do usługi GitHub w sekcji Użytkownicy i grupy. Synchronizacji początkowej zajmuje więcej czasu wykonywania niż kolejne synchronizacje, występujące co około 40 minut tak długo, jak usługa jest uruchomiona. Można użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi administracyjnej Dzienniki aktywności, które opisują wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej.

Aby uzyskać więcej informacji na temat usługi Azure AD, inicjowanie obsługi dzienników do odczytu, zobacz [raportowania na użytkownika automatyczne Inicjowanie obsługi konta](../active-directory-saas-provisioning-reporting.md).


## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowizacja konta użytkowników dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak należy przejrzeć dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../active-directory-saas-provisioning-reporting.md)
