---
title: 'Samouczek: Konfigurowanie usługi Salesforce dla użytkownika automatycznego inicjowania obsługi administracyjnej z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 49384b8b-3836-4eb1-b438-1c46bb9baf6f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: b5a85dd7d4ac6bdabcbab49f654ebfe4adae73cd
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-configure-salesforce-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie usług Salesforce dla użytkownika automatycznego inicjowania obsługi administracyjnej.

Celem tego samouczka jest Pokaż kroki wymagane do przeprowadzenia w Salesforce i Azure AD, aby automatycznie udostępniać i kont usuwanie użytkowników z usługi Azure AD do usługi Salesforce.

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że już następujące elementy:

*   Dzierżawy usługi Azure Active directory
*   Dzierżawy witryny Salesforce.com

>[!IMPORTANT] 
>Jeśli używane konto próbne witryny Salesforce.com, będzie on mógł skonfigurować użytkownika automatycznego inicjowania obsługi administracyjnej. Konta wersji próbnej nie mieć włączony, dopóki nabywane są niezbędne dostępu do interfejsu API. Przy użyciu bezpłatny można uzyskać wokół tego ograniczenia [konta dewelopera](https://developer.salesforce.com/signup) do ukończenia tego samouczka.

Jeśli używasz środowiska izolowanego Salesforce, zobacz [samouczek integracji usług Salesforce piaskownicy](https://go.microsoft.com/fwLink/?LinkID=521879).

## <a name="assigning-users-to-salesforce"></a>Przypisywanie użytkowników do usługi Salesforce

Usługi Azure Active Directory używa pojęcie o nazwie "przypisania" w celu określenia, którzy użytkownicy powinien otrzymać dostęp do wybranej aplikacji. W kontekście użytkownika automatyczne Inicjowanie obsługi konta tylko użytkownicy i grupy, które "przypisano" do aplikacji w usłudze Azure AD jest zsynchronizowany.

Przed Skonfiguruj i włącz usługę inicjowania obsługi administracyjnej, należy zdecydować, których użytkowników lub grup w usłudze Azure AD będą potrzebować dostępu do aplikacji Salesforce. Po utworzeniu tej decyzji, tych użytkowników można przypisać do aplikacji Salesforce zgodnie z instrukcjami w [przypisać użytkownika lub grupy do aplikacji w przedsiębiorstwie](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-salesforce"></a>Ważne porady dotyczące przypisywania użytkowników do usługi Salesforce

*   Zalecane jest pojedynczego użytkownika usługi Azure AD jest przypisana do usług Salesforce, aby przetestować konfigurację inicjowania obsługi administracyjnej. Później można przypisać dodatkowych użytkowników i/lub grup.

*  Przypisanie użytkownika do usług Salesforce, musisz wybrać poprawnej roli użytkownika. Rola "Domyślnego dostępu" nie działa w przypadku inicjowania obsługi administracyjnej

    > [!NOTE]
    > Ta aplikacja importuje role niestandardowe z Salesforce jako część procesu inicjowania obsługi administracyjnej klienta może chcesz wybrać podczas przypisywania użytkowników

## <a name="enable-automated-user-provisioning"></a>Włącz inicjowanie obsługi użytkowników automatycznych

Ta sekcja przeprowadzi Cię przez łączenie usługi Azure AD z konta użytkownika w Salesforce inicjowania obsługi interfejsu API i konfigurowanie inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączania konta użytkowników przypisane w usłudze Salesforce w oparciu o przypisania użytkowników i grup w usłudze Azure AD.

>[!Tip]
>Można też włączone na języku SAML logowania jednokrotnego dla usług Salesforce, postępując zgodnie z instrukcjami zawarte w [portalu Azure](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej, chociaż te dwie funkcje uzupełniania siebie nawzajem.

### <a name="configure-automatic-user-account-provisioning"></a>Skonfiguruj użytkownika automatyczne Inicjowanie obsługi konta

Celem tej sekcji jest przedstawiają sposób włączania Inicjowanie obsługi użytkowników, kont użytkowników usługi Active Directory do usługi Salesforce.

1. W [portalu Azure](https://portal.azure.com), przejdź do **usługi Azure Active Directory > aplikacje przedsiębiorstwa > wszystkie aplikacje** sekcji.

2. Usługa Salesforce została już skonfigurowana dla logowania jednokrotnego, wyszukaj wystąpienie usługi Salesforce za pomocą pola wyszukiwania. W przeciwnym razie wybierz **Dodaj** i wyszukaj **Salesforce** w galerii aplikacji. Wybierz usługi Salesforce z wyników wyszukiwania i dodaj go do listy aplikacji.

3. Wybierz wystąpienie usługi Salesforce, a następnie wybierz **inicjowania obsługi administracyjnej** kartę.

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![aprowizowanie](./media/active-directory-saas-salesforce-provisioning-tutorial/provisioning.png)

5. W obszarze **poświadczeń administratora** sekcji, skonfiguruj następujące ustawienia konfiguracji:
   
    a. W **administratora** tekstowym, wpisz nazwę, która zawiera konta usług Salesforce **administratorem** profilu w witrynie Salesforce.com przypisane.
   
    b. W **hasło administratora** tekstowym, wpisz hasło dla tego konta.

6. Aby uzyskać token zabezpieczeń usług Salesforce, otwórz nową kartę i zaloguj do tego samego konta administratora usługi Salesforce. W prawym górnym rogu strony, kliknij swoją nazwę, a następnie kliknij **ustawienia**.

     ![Włącz inicjowanie obsługi użytkowników](./media/active-directory-saas-salesforce-provisioning-tutorial/sf-my-settings.png "Włącz inicjowanie obsługi użytkowników")

7. W lewym okienku nawigacji, kliknij polecenie **Moje informacje osobiste** rozwiń sekcję powiązane, a następnie kliknij przycisk **zresetować moje tokenu zabezpieczeń**.
  
    ![Włącz inicjowanie obsługi użytkowników](./media/active-directory-saas-salesforce-provisioning-tutorial/sf-personal-reset.png "Włącz inicjowanie obsługi użytkowników")

8. Na **zresetować tokenu zabezpieczeń** kliknij przycisk **zresetować tokenu zabezpieczeń** przycisku.

    ![Włącz inicjowanie obsługi użytkowników](./media/active-directory-saas-salesforce-provisioning-tutorial/sf-reset-token.png "Włącz inicjowanie obsługi użytkowników")

9. Sprawdź skrzynki odbiorczej poczty e-mail, skojarzone z tym kontem administratora. Poszukaj wiadomości e-mail z witryny Salesforce.com, który zawiera nowy token zabezpieczający.

10. Skopiuj token, przejdź do okna usługi Azure AD i wklej ją do **klucz tajny tokenu** pola.

11. **Adres URL dzierżawy** powinien zostać wprowadzony, jeśli wystąpienie usług Salesforce jest w chmurze dla instytucji rządowych Salesforce. W przeciwnym razie wartość jest opcjonalna. Wprowadź adres URL dzierżawy w formacie https://your-instance.my.salesforce.com, zastępując Twoje wystąpienie nazwy wystąpienia usług Salesforce.

12. W portalu Azure kliknij **Testuj połączenie** zapewniające usługi Azure AD mogą łączyć się z aplikacji Salesforce.

13. W **wiadomość E-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, który powinien otrzymywać powiadomienia błąd inicjowania obsługi administracyjnej i zaznacz pole wyboru poniżej.

14. Kliknij przycisk **zapisać.**  
    
15.  W sekcji mapowania wybierz **synchronizacji Azure Active Directory użytkownikom Salesforce.**

16. W **mapowań atrybutów** Przejrzyj atrybuty użytkowników, które są synchronizowane z usługi Azure AD do usługi Salesforce. Należy pamiętać, że atrybuty wybrany jako **pasujące** właściwości są używane do dopasowania kont użytkowników w usłudze Salesforce dla operacji update. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

17. Aby włączyć usługi Azure AD, świadczenie usługi dla usług Salesforce, zmień **stan inicjowania obsługi administracyjnej** do **na** w sekcji Ustawienia

18. Kliknij przycisk **zapisać.**

Spowoduje to uruchomienie synchronizacji początkowej użytkowników i/lub grupy przypisane do usługi Salesforce w sekcji Użytkownicy i grupy. Należy pamiętać, że synchronizacji początkowej dłużej, aby wykonać niż kolejne synchronizacje, występujące co około 40 minut tak długo, jak usługa jest uruchomiona. Można użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi administracyjnej Dzienniki aktywności, które opisują wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej w aplikacji Salesforce.

Aby uzyskać więcej informacji na temat usługi Azure AD, inicjowanie obsługi dzienników do odczytu, zobacz [raportowania na użytkownika automatyczne Inicjowanie obsługi konta](active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowizacja konta użytkowników dla aplikacji przedsiębiorstwa](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Konfigurowanie rejestracji jednokrotnej](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-tutorial)
