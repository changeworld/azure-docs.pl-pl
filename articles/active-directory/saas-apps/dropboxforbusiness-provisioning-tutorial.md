---
title: 'Samouczek: Konfigurowanie skrzynki dla użytkowników automatycznego inicjowania obsługi administracyjnej z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i skrzynki dla firm.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 0f3a42e4-6897-4234-af84-b47c148ec3e1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: cd7d4a50060c971e6e09eb07a8006e5cd6fffd4e
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35943434"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie skrzynki dla firm dla użytkownika automatycznego inicjowania obsługi administracyjnej.

Celem tego samouczka jest opisano czynności, które należy wykonać w Dropbox dla firm i Azure AD automatycznie zapewnianie i usuwanie kont użytkowników z usługi Azure AD do usługi Dropbox dla firm.

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że już następujące elementy:

*   Dzierżawy usługi Azure Active directory.
*   Dropbox dla firm jednokrotnego włączone subskrypcji.
*   Konto użytkownika w Dropbox dla firm z uprawnieniami administratora zespołu.

## <a name="assigning-users-to-dropbox-for-business"></a>Przypisywanie użytkowników do usługi Dropbox dla firm

Usługi Azure Active Directory używa pojęcie o nazwie "przypisania" w celu określenia, którzy użytkownicy powinien otrzymać dostęp do wybranej aplikacji. W kontekście użytkownika automatyczne Inicjowanie obsługi konta tylko użytkownicy i grupy, które "przypisano" do aplikacji w usłudze Azure AD jest zsynchronizowany.

Przed Skonfiguruj i włącz usługę inicjowania obsługi administracyjnej, należy zdecydować, jakie użytkownicy i/lub grup w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do Twojej skrzynki dla aplikacji biznesowych. Po decyzję, można przypisać tych użytkowników do Twojej skrzynki dla aplikacji biznesowych, postępując zgodnie z instrukcjami poniżej:

[Przypisanie użytkownika lub grupę do aplikacji w przedsiębiorstwie](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>Ważne porady dotyczące przypisywania użytkowników do usługi Dropbox dla firm

*   Zalecane jest jeden użytkownik usługi Azure AD jest przypisany do skrzynki dla firm, aby przetestować konfigurację inicjowania obsługi administracyjnej. Później można przypisać dodatkowych użytkowników i/lub grup.

*   Przypisanie użytkownika do skrzynki dla firm, należy wybrać poprawnej roli użytkownika. Rola "Domyślnego dostępu" nie działa w przypadku inicjowania obsługi administracyjnej.

## <a name="enable-automated-user-provisioning"></a>Włącz automatyczne Inicjowanie obsługi użytkowników

Ta sekcja przeprowadzi użytkownika przez nawiązywania połączenia z usługi Azure AD Dropbox dla konta użytkownika firmy inicjowania obsługi interfejsu API i konfigurowanie usługi inicjowania obsługi administracyjnej do tworzenia, aktualizacji i wyłączania konta użytkowników przypisane w Dropbox dla firm, w oparciu o użytkowników i grup przypisania w usłudze Azure AD.

>[!Tip]
>Można też włączyć na języku SAML logowania jednokrotnego dla skrzynki dla firm, wykonując instrukcje podane w [portalu Azure](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej, chociaż te dwie funkcje uzupełniania siebie nawzajem.

### <a name="to-configure-automatic-user-account-provisioning"></a>Aby skonfigurować konto użytkownika automatycznego inicjowania obsługi administracyjnej:

1. W [portalu Azure](https://portal.azure.com), przejdź do **usługi Azure Active Directory > aplikacje przedsiębiorstwa > wszystkie aplikacje** sekcji.

2. Jeśli skonfigurowano już program Dropbox dla firm dla logowania jednokrotnego, wyszukaj dla swojego wystąpienia usługi Dropbox dla firm przy użyciu pola wyszukiwania. W przeciwnym razie wybierz **Dodaj** i wyszukaj **Dropbox dla firm** w galerii aplikacji. Wybierz Dropbox dla firm z wyników wyszukiwania, a następnie dodaj go do listy aplikacji.

3. Wybierz wystąpienie usługi Dropbox dla firm, a następnie wybierz **inicjowania obsługi administracyjnej** kartę.

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**. 

    ![aprowizowanie](./media/dropboxforbusiness-provisioning-tutorial/provisioning.png)

5. W obszarze **poświadczeń administratora** kliknij **autoryzacji**. Dropbox dla okna dialogowego logowania biznesowych zostanie otwarty w nowym oknie przeglądarki.

6. Na **logowania do usługi Dropbox, aby połączyć się z usługą Azure AD** okna dialogowego, zalogować się w usłudze Dropbox dla dzierżawy biznesowych.

     ![Inicjowanie obsługi użytkowników](./media/dropboxforbusiness-provisioning-tutorial/ic769518.png "Inicjowanie obsługi użytkowników")

7. Upewnij się, czy chcesz zezwolić usłudze Azure Active Directory Aby wprowadzić zmiany w usłudze Dropbox dla dzierżawy biznesowych. Kliknij przycisk **Zezwalaj**.
    
      ![Inicjowanie obsługi użytkowników](./media/dropboxforbusiness-provisioning-tutorial/ic769519.png "Inicjowanie obsługi użytkowników")

8. W portalu Azure kliknij **Testuj połączenie** zapewniające usługi Azure AD mogą łączyć się z Twojej skrzynki dla aplikacji biznesowych. Jeśli połączenie nie powiedzie się, zapewnić usłudze Dropbox konto firmowe, ma uprawnienia administratora zespołu i spróbuj **"Autoryzuj"** krok ponownie.

9. Wprowadź adres e-mail osoby lub grupy, który powinien zostać wyświetlony inicjowania obsługi administracyjnej powiadomienia o błędach w **wiadomość E-mail z powiadomieniem** pola, a następnie zaznacz pole wyboru.

10. Kliknij przycisk **zapisać.**

11. W sekcji mapowania wybierz **synchronizacji Azure użytkownicy usługi Active Directory do usługi Dropbox dla firm.**

12. W **mapowań atrybutów** Przejrzyj atrybutów użytkowników, które są synchronizowane z usługi Azure AD Dropbox dla firm. Atrybuty wybrany jako **pasujące** właściwości są używane do dopasowania kont użytkowników w Dropbox dla firm dla operacji update. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

13. Aby włączyć usługi Azure AD, inicjowania obsługi usługi Dropbox dla firm, zmień **stan inicjowania obsługi administracyjnej** do **na** w sekcji Ustawienia

14. Kliknij przycisk **zapisać.**

Rozpoczyna się wstępnej synchronizacji użytkowników i/lub grupy przypisane do skrzynki dla firm w sekcji Użytkownicy i grupy. Synchronizacji początkowej zajmuje więcej czasu wykonywania niż kolejne synchronizacje, występujące co około 40 minut tak długo, jak usługa jest uruchomiona. Można użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi administracyjnej Dzienniki aktywności, które opisują wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej w usłudze Dropbox dla aplikacji biznesowych.

Aby uzyskać więcej informacji na temat usługi Azure AD, inicjowanie obsługi dzienników do odczytu, zobacz [raportowania na użytkownika automatyczne Inicjowanie obsługi konta](../active-directory-saas-provisioning-reporting.md).


## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowizacja konta użytkowników dla aplikacji przedsiębiorstwa](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie rejestracji jednokrotnej](dropboxforbusiness-tutorial.md)