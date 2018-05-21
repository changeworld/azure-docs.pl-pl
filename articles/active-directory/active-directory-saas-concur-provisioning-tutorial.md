---
title: 'Samouczek: Konfigurowanie Concur dla użytkownika automatycznego inicjowania obsługi administracyjnej z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Concur.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: df47f55f-a894-4e01-a82e-0dbf55fc8af1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: c439e6ca784bf031cd4102a6cb15b5e210130810
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-configure-concur-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Concur dla użytkownika automatycznego inicjowania obsługi administracyjnej.

Celem tego samouczka jest opisano czynności, które należy wykonać w Concur i Azure AD, aby automatycznie zapewnianie i usuwanie kont użytkowników z usługi Azure AD Concur.

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że już następujące elementy:

*   Dzierżawy usługi Azure Active directory.
*   Concur logowanie jednokrotne włączone subskrypcji.
*   Konto użytkownika w Concur z uprawnieniami administratora zespołu.

## <a name="assigning-users-to-concur"></a>Przypisywanie użytkowników do Concur

Usługi Azure Active Directory używa pojęcie o nazwie "przypisania" w celu określenia, którzy użytkownicy powinien otrzymać dostęp do wybranej aplikacji. W kontekście użytkownika automatyczne Inicjowanie obsługi konta tylko użytkownicy i grupy, które "przypisano" do aplikacji w usłudze Azure AD jest zsynchronizowany.

Przed Skonfiguruj i włącz usługę inicjowania obsługi administracyjnej, należy zdecydować, jakie użytkownicy i/lub grup w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do aplikacji Concur. Po decyzję, postępując zgodnie z instrukcjami w tym miejscu można przypisać tych użytkowników do aplikacji Concur:

[Przypisanie użytkownika lub grupę do aplikacji w przedsiębiorstwie](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-concur"></a>Ważne porady dotyczące przypisywania użytkowników do Concur

*   Zalecane jest pojedynczego użytkownika usługi Azure AD można przypisać do Concur do testowania konfiguracji inicjowania obsługi administracyjnej. Później można przypisać dodatkowych użytkowników i/lub grup.

*   Przypisanie użytkownika do Concur, musisz wybrać poprawnej roli użytkownika. Rola "Domyślnego dostępu" nie działa w przypadku inicjowania obsługi administracyjnej.

## <a name="enable-user-provisioning"></a>Włącz inicjowanie obsługi użytkowników

Ta sekcja przeprowadzi Cię przez łączenie usługi Azure AD z konta użytkownika w Concur inicjowania obsługi interfejsu API i konfigurowanie inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączania konta użytkowników przypisane w Concur w oparciu o przypisania użytkowników i grup w usłudze Azure AD.

> [!Tip] 
> Można też włączyć na języku SAML logowania jednokrotnego dla Concur, wykonując instrukcje podane w [portalu Azure](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej, chociaż te dwie funkcje uzupełniania siebie nawzajem.

### <a name="to-configure-user-account-provisioning"></a>Aby skonfigurować, inicjowanie obsługi konta użytkownika:

Celem tej sekcji jest przedstawiają sposób włączania obsługi administracyjnej kont użytkowników usługi Active Directory do Concur.

Aby włączyć aplikacje w usłudze wydatków, musi być prawidłową konfigurację i korzystania z profilem administrator usługi sieci Web. Nie dodawaj rolę administratora WS istniejącego profilu administratora używanego do T & j funkcji administracyjnych.

Cząstkowe konsultantów lub administrator klienta należy utworzyć różne profilu administratora usługi sieci Web i administrator klienta musi używać tego profilu funkcji administratora usług sieci Web (na przykład włączenie aplikacje). Te profile muszą być przechowywane oddzielnie od administratora klient codzienne T & E admin profilu (T & E profilu administratora nie powinna mieć rolę WSAdmin przypisane).

Podczas tworzenia profilu służący do włączania aplikacji, wprowadź nazwę administratora klienta do pól profilu użytkownika. W ten sposób własności do tego profilu. Po utworzeniu co najmniej jeden profil, klient musi Zaloguj się za pomocą tego profilu, kliknij "*włączyć*" przycisk aplikacji partnera w menu usługi sieci Web.

Z następujących powodów tej akcji nie można wykonać za pomocą profilu używanego do normalnej T & E administracji.

* Klient musi być elementem kliknie "*tak*" w oknie dialogu, które jest wyświetlane, gdy aplikacja zostanie włączona. Kliknij ten przycisk uznaje się, że klient jest gotowa dla aplikacji partnera dostępu do danych, więc możesz lub partnera nie kliknij ten przycisk Tak.

* Jeśli administrator klienta, która włączyła aplikację za pomocą administratora T & E profilu odchodzi z firmy (co w profilu jest dezaktywowane), włączyć za pomocą profilu nie działać do czasu włączenia aplikacji z innym profilem administratora WS aktywne dla wszystkich aplikacji. Jest to, dlaczego są powinien utworzyć różne Admin WS profile.

* Jeśli administrator odchodzi z firmy, można można zmienić nazwy skojarzone z profilem administratora WS administratorowi zastąpienia, w razie potrzeby bez wpływu na pewno włączonych aplikacji, ponieważ ten profil nie jest konieczne dezaktywowane.

**Aby skonfigurować, inicjowanie obsługi użytkowników, wykonaj następujące czynności:**

1. Zaloguj się do Twojego **Concur** dzierżawy.

2. Z **administracji** menu, wybierz opcję **usług sieci Web**.
   
    ![Dzierżawy Concur](./media/active-directory-saas-concur-provisioning-tutorial/IC721729.png "Concur dzierżawy")

3. Po lewej stronie z **usług sieci Web** okienku wybierz **Włącz aplikacji partnera**.
   
    ![Włączanie aplikacji partnera](./media/active-directory-saas-concur-provisioning-tutorial/ic721730.png "włączyć partnera aplikacji")

4. Z **Włącz aplikacji** listy, wybierz **usługi Azure Active Directory**, a następnie kliknij przycisk **włączyć**.
   
    ![Microsoft Azure Active Directory](./media/active-directory-saas-concur-provisioning-tutorial/ic721731.png "Microsoft Azure Active Directory")

5. Kliknij przycisk **tak** zamknąć **potwierdzenie akcji** okna dialogowego.
   
    ![Potwierdzenie akcji](./media/active-directory-saas-concur-provisioning-tutorial/ic721732.png "potwierdzenie akcji")

6. W [portalu Azure](https://portal.azure.com), przejdź do **usługi Azure Active Directory > aplikacje przedsiębiorstwa > wszystkie aplikacje** sekcji.

7. Jeśli Concur został już skonfigurowany dla logowania jednokrotnego, wyszukiwanie wystąpieniem Concur przy użyciu pola wyszukiwania. W przeciwnym razie wybierz **Dodaj** i wyszukaj **Concur** w galerii aplikacji. Wybierz Concur w wynikach wyszukiwania, a następnie dodaj go do listy aplikacji.

8. Wybierz wystąpienia programu Concur, a następnie wybierz **inicjowania obsługi administracyjnej** kartę.

9. Ustaw **tryb obsługi administracyjnej** do **automatyczne**. 
 
    ![aprowizowanie](./media/active-directory-saas-concur-provisioning-tutorial/provisioning.png)

10. W obszarze **poświadczeń administratora** wprowadź **nazwy użytkownika** i **hasło** administratora Concur.

11. W portalu Azure kliknij **Testuj połączenie** zapewniające usługi Azure AD mogą łączyć się z aplikacji Concur. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto Concur ma uprawnienia administratora zespołu.

12. Wprowadź adres e-mail osoby lub grupy, który powinien zostać wyświetlony inicjowania obsługi administracyjnej powiadomienia o błędach w **wiadomość E-mail z powiadomieniem** pola, a następnie zaznacz pole wyboru.

13. Kliknij przycisk **zapisać.**

14. W sekcji mapowania wybierz **synchronizacji Azure Active Directory użytkownikom Concur.**

15. W **mapowań atrybutów** Przejrzyj atrybutów użytkowników, które są synchronizowane z usługi Azure AD Concur. Atrybuty wybrany jako **pasujące** właściwości są używane do dopasowania kont użytkowników w Concur dla operacji update. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

16. Aby włączyć usługi Azure AD usługi dla Concur inicjowania obsługi administracyjnej, zmień **stan inicjowania obsługi administracyjnej** do **na** w **ustawienia** sekcji

17. Kliknij przycisk **zapisać.**

Można teraz utworzyć konta testowego. Aby sprawdzić, czy konto zostało zsynchronizowane z Concur poczekaj maksymalnie 20 minut.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowizacja konta użytkowników dla aplikacji przedsiębiorstwa](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie rejestracji jednokrotnej](active-directory-saas-concur-tutorial.md)

