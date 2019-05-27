---
title: 'Samouczek: Konfigurowanie usługi GitHub dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników w usłudze GitHub.
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
ms.openlocfilehash: 356af6567cea96efc7edbe8bc4932182d35ebc07
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65963943"
---
# <a name="tutorial-configure-github-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie usługi GitHub dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest Wam czynności, które należy wykonać w witrynie GitHub i Azure AD, do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników z usługi Azure AD do usługi GitHub.

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, że masz następujące elementy:

* Dzierżawy usługi Azure Active directory
* Organizacja usługi GitHub utworzona w [chmurze usługi GitHub dla przedsiębiorstw](https://help.github.com/articles/github-s-products/#github-enterprise), która wymaga [planu rozliczeniowego usługi GitHub Enterprise](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)
* Konto użytkownika w usłudze GitHub z uprawnieniami administratora w organizacji

> [!NOTE]
> Inicjowanie obsługi administracyjnej integracji usługi Azure AD opiera się na [interfejsu API usługi GitHub Standard SCIM](https://developer.github.com/v3/scim/), który jest dostępny dla [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise) klientom [GitHub Enterprise, plan rozliczeniowy](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations) .

## <a name="assigning-users-to-github"></a>Przypisywanie użytkowników do usługi GitHub

Usługa Azure Active Directory używa koncepcji o nazwie "przypisania", aby określić, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście Inicjowanie obsługi administracyjnej konta użytkowników są synchronizowane tylko użytkowników i grup, które "przypisano" do aplikacji w usłudze Azure AD. 

Przed Skonfiguruj i włącz usługę aprowizacji, musisz zdecydować, jakie użytkowników i/lub grup w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do aplikacji usługi GitHub. Po decyzję, możesz przypisać tych użytkowników z aplikacją usługi GitHub, wykonując instrukcje podane w tym miejscu:

[Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-github"></a>Ważne wskazówki dotyczące przypisywania użytkowników do usługi GitHub

* Zalecane jest, że jeden użytkownik usługi Azure AD jest przypisany do usługi GitHub do testowania konfiguracji aprowizacji. Później można przypisać dodatkowych użytkowników i/lub grup.

* Podczas przypisywania użytkowników do usługi GitHub, należy wybrać **użytkownika** roli, lub inną prawidłową specyficzne dla aplikacji (jeśli jest dostępny) w oknie dialogowym przydział. **Domyślnego dostępu** roli nie działa w przypadku inicjowania obsługi administracyjnej i Ci użytkownicy są pomijane.

## <a name="configuring-user-provisioning-to-github"></a>Konfigurowanie aprowizowania użytkowników w usłudze GitHub

Ta sekcja przeprowadzi Cię przez połączenie usługi Azure AD do konta użytkownika usługi GitHub aprowizujący interfejs API i konfigurowanie inicjowania obsługi usługi do tworzenia, aktualizacji, a następnie wyłącz konta użytkowników przypisane w usłudze GitHub, na podstawie przypisania użytkowników i grup w usłudze Azure AD.

> [!TIP]
> Można też włączyć opartej na SAML logowania jednokrotnego usługi github, wykonując instrukcje podane w [witryny Azure portal](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatyczną aprowizację, chociaż te dwie funkcje uzupełnienie siebie nawzajem.

### <a name="configure-automatic-user-account-provisioning-to-github-in-azure-ad"></a>Skonfiguruj automatyczne aprowizowaniem kont użytkowników do usługi GitHub w usłudze Azure AD

1. W [witryny Azure portal](https://portal.azure.com), przejdź do **usługi Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje** sekcji.

2. Jeśli już skonfigurowano usługi GitHub dla logowania jednokrotnego, wyszukiwania dla swojego wystąpienia usługi GitHub za pomocą pola wyszukiwania. W przeciwnym razie wybierz **Dodaj** i wyszukaj **GitHub** w galerii aplikacji. Wybierz usługi GitHub z poziomu wyników wyszukiwania, a następnie dodaj go do listy aplikacji.

3. Wybierz wystąpienie usługi GitHub, a następnie wybierz **aprowizacji** kartę.

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Inicjowanie obsługi administracyjnej w usłudze GitHub](./media/github-provisioning-tutorial/GitHub1.png)

5. W obszarze **poświadczeń administratora** kliknij **Autoryzuj**. Ta operacja otwiera okno dialogowe autoryzacji usługi GitHub w nowym oknie przeglądarki. 

6. W nowym oknie Zaloguj się do usługi GitHub za pomocą konta administratora. W oknie dialogowym autoryzacji wynikowe, wybierz zespół usługi GitHub, który chcesz włączyć udostępnianie, a następnie wybierz **Autoryzuj**. Po zakończeniu wróć do witryny Azure portal, aby zakończyć konfigurowanie inicjowania obsługi administracyjnej.

    ![Okno dialogowe autoryzacji](./media/github-provisioning-tutorial/GitHub2.png)

7. W witrynie Azure portal wprowadź **adres URL dzierżawy** i kliknij przycisk **Testuj połączenie** zapewniające usługi Azure AD connect można do swojej aplikacji w usłudze GitHub. Jeśli połączenie nie powiedzie się, upewnij się, Twoje konto usługi GitHub ma uprawnienia administratora i **adres URl dzierżawy** jest wprowadzona poprawnie, a następnie spróbuj ponownie w kroku "Autoryzuj" (może stanowić **adres URL dzierżawy** przez regułę: `https://api.github.com/scim/v2/organizations/<Organization_name>` , możesz znaleźć swojej organizacji w ramach konta usługi GitHub: **Ustawienia** > **organizacje**).

    ![Okno dialogowe autoryzacji](./media/github-provisioning-tutorial/GitHub3.png)

8. Wprowadź adres e-mail osoby lub grupy, który powinien zostać wyświetlony inicjowania obsługi administracyjnej powiadomienia o błędach w **wiadomość E-mail z powiadomieniem** pola, a następnie zaznacz pole wyboru "Wyślij wiadomość e-mail z powiadomieniem, gdy wystąpi błąd."

9. Kliknij pozycję **Zapisz**.

10. W sekcji mapowania, wybierz **synchronizacji Azure użytkownicy usługi Active Directory do usługi GitHub**.

11. W **mapowania atrybutów** Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do usługi GitHub. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w usłudze GitHub dla operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

12. Aby włączyć usługi Azure AD, inicjowania obsługi usługi dla usługi GitHub, zmień **stanie aprowizacji** do **na** w **ustawienia** sekcji

13. Kliknij pozycję **Zapisz**.

Ta operacja uruchamia wstępnej synchronizacji użytkowników i/lub grupy przypisane do usługi GitHub w sekcji Użytkownicy i grupy. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak usługa jest uruchomiona. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi dzienników aktywności, które opisują każdą akcję wykonaną przez usługę aprowizacji.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak przeglądać dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../manage-apps/check-status-user-account-provisioning.md)
