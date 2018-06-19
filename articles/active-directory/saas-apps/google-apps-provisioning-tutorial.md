---
title: 'Samouczek: Skonfiguruj pakiet G dla użytkownika automatycznego inicjowania obsługi administracyjnej z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak automatycznie zapewnianie i usuwanie kont użytkowników z usługi Azure AD G pakietu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 7bd2cc871300ca9aa9e7cccb5222bb003d199780
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35942573"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie G Suite dla użytkownika automatycznego inicjowania obsługi administracyjnej.

Celem tego samouczka jest pokazują, jak automatycznie zapewnianie i usuwanie kont użytkowników z usługi Azure Active Directory (Azure AD) do zestawu G.

> [!NOTE]
> W tym samouczku opisano łącznika, rozszerzający usługi dostarczania użytkownika programu Azure AD. Uzyskać ważne szczegóły dotyczące tej usługi nie, jak to działa i często zadawane pytania, zobacz [zautomatyzować użytkownika alokowania i anulowania alokowania do aplikacji SaaS w usłudze Azure Active Directory](./../active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z pakietem G, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Pakiet G logowanie jednokrotne włączone subskrypcji
- Subskrypcja usługi Google Apps lub subskrypcji usługi Google Cloud Platform.

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assign-users-to-g-suite"></a>Przypisywanie użytkowników do zestawu G

Usługi Azure Active Directory używa pojęcie o nazwie "przypisania" w celu określenia, którzy użytkownicy powinien otrzymać dostęp do wybranej aplikacji. W kontekście użytkownika automatyczne Inicjowanie obsługi konta tylko użytkownicy i grupy, które "przypisano" do aplikacji w usłudze Azure AD są synchronizowane.

Aby skonfigurować i włączyć usługę inicjowania obsługi administracyjnej, należy zdecydować, których użytkowników lub grup w usłudze Azure AD będą potrzebować dostępu do aplikacji. Po utworzeniu tej decyzji, tych użytkowników można przypisać do aplikacji zgodnie z instrukcjami w [przypisać użytkownika lub grupy do aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

> [!IMPORTANT]
> Zalecamy, aby pojedynczego użytkownika usługi Azure AD można przypisać do zestawu G do testowania konfiguracji inicjowania obsługi administracyjnej. Później można przypisać dodatkowych użytkowników i grup.

> Gdy użytkownik jest przypisany do zestawu G, wybrać **użytkownika** lub **grupy** roli w oknie dialogowym przypisania. **Domyślnego dostępu** roli nie działa w przypadku inicjowania obsługi administracyjnej.

## <a name="enable-automated-user-provisioning"></a>Włącz inicjowanie obsługi użytkowników automatycznych

W tej sekcji przeprowadzi Cię przez proces nawiązywania połączenia inicjowania obsługi interfejsu API zestawu G konta użytkownika usługi Azure AD. Pomaga również skonfigurować usługę inicjowania obsługi administracyjnej do tworzenia, aktualizacji i wyłączenie konta użytkowników przypisane w zestawie G na podstawie użytkownika i przypisanie do grupy w usłudze Azure AD.

>[!TIP]
>Można także włączyć na języku SAML rejestracji jednokrotnej dla zestawów G, postępując zgodnie z instrukcjami w [portalu Azure](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej, chociaż te dwie funkcje uzupełniania siebie nawzajem.

### <a name="configure-automatic-user-account-provisioning"></a>Skonfiguruj użytkownika automatyczne Inicjowanie obsługi konta

> [!NOTE]
> Inny wygodną opcją do automatyzacji pakietu G Inicjowanie obsługi użytkowników jest użycie [Google Apps Directory Sync (GADS)](https://support.google.com/a/answer/106368?hl=en). GADS inicjuje Twoich tożsamości: lokalnej usługi Active Directory G pakietu. Natomiast rozwiązanie w tym samouczku inicjuje synchronizację użytkowników usługi Azure Active Directory (w chmurze) i włączona obsługa poczty e-mail grup G pakietu. 

1. Zaloguj się do [konsoli administracyjnej aplikacji Google](http://admin.google.com/) z konta administratora, a następnie wybierz **zabezpieczeń**. Jeśli nie widzisz łącza może być ukryty w obszarze **więcej formantów** menu u dołu ekranu.
   
    ![Wybierz opcję Zabezpieczenia.][10]

2. Na **zabezpieczeń** wybierz pozycję **dokumentacja interfejsu API**.
   
    ![Wybierz odwołanie do interfejsu API.][15]

3. Wybierz **dostęp włączyć API**.
   
    ![Wybierz odwołanie do interfejsu API.][16]

    > [!IMPORTANT]
    > Dla każdego użytkownika, który chcesz udostępnić pakietu G, nazwa użytkownika ich w usłudze Azure Active Directory *musi* ograniczeni do domeny niestandardowej. Na przykład użytkownik nazwy tego wygląd bob@contoso.onmicrosoft.com nie są akceptowane przez pakiet G. Z drugiej strony bob@contoso.com została zaakceptowana. Istniejącego użytkownika domeny można zmienić, edytując właściwości ich w usłudze Azure AD. Dodaliśmy instrukcje dotyczące sposobu ustawiania domeny niestandardowej dla usługi Azure Active Directory i G pakietu w kolejnych krokach.
      
4. Jeśli nie dodano jeszcze niestandardowej nazwy domeny do usługi Azure Active Directory, wykonaj następujące czynności:
  
    a. W [portalu Azure](https://portal.azure.com), w okienku nawigacji po lewej stronie wybierz **usługi Active Directory**. Na liście katalog wybierz swój katalog. 

    b. Wybierz **nazwy domeny** w lewym okienku nawigacji, a następnie wybierz **Dodaj**.
     
     ![Domena](./media/google-apps-provisioning-tutorial/domain_1.png)

     ![Dodawanie domeny](./media/google-apps-provisioning-tutorial/domain_2.png)

    c. Wpisz nazwę domeny do **nazwy domeny** pola. Ta nazwa domeny powinna być tą samą nazwą domeny, który ma być używany dla zestawu G. Następnie wybierz **Dodawanie domeny** przycisku.
     
     ![Nazwa domeny](./media/google-apps-provisioning-tutorial/domain_3.png)

    d. Wybierz **dalej** aby przejść do strony weryfikacji. Aby sprawdzić, czy jesteś właścicielem tej domeny, należy edytować rekordy DNS w domenie, zgodnie z wartościami, które znajdują się na tej stronie. Można sprawdzić za pomocą **rekordów MX** lub **rekordów TXT**w oparciu o wybór dla **typu rekordu** opcji. 
    
    Aby uzyskać bardziej szczegółowe instrukcje dotyczące sposobu Sprawdź nazwy domen z usługi Azure AD, zobacz [Dodaj własną nazwę domeny do usługi Azure AD](https://go.microsoft.com/fwLink/?LinkID=278919&clcid=0x409).
     
     ![Domena](./media/google-apps-provisioning-tutorial/domain_4.png)

    e. Powtórz poprzednie kroki dla wszystkich domen, które mają zostać dodane do katalogu.

    > [!NOTE]
    Inicjowanie obsługi użytkowników, niestandardowe domeny musi odpowiadać nazwę domeny źródła usługi Azure AD. Jeśli nie są zgodne, można rozwiązać problem, implementacja dostosowania mapowanie atrybutu.


5. Teraz, gdy została zweryfikowana wszystkich domen w usłudze Azure AD, należy sprawdzić je ponownie z usługi Google Apps. Dla każdej domeny, która nie jest już zarejestrowany w usłudze Google wykonaj następujące czynności:
   
    a. W [konsoli administracyjnej usługi Google Apps](http://admin.google.com/), wybierz pozycję **domen**.
     
     ![Wybierz domenę][20]

    b. Wybierz **Dodawanie domeny lub alias domeny**.
     
     ![Dodaj nową domenę][21]

    c. Wybierz **Dodaj inną domenę**, a następnie wpisz nazwę domeny, który chcesz dodać.
     
     ![Wpisz nazwę domeny][22]

    d. Wybierz **Kontynuuj i Zweryfikuj prawo własności do domeny**. Następnie wykonaj kroki, aby sprawdzić, czy jesteś właścicielem nazwy domeny. Kompleksowe instrukcje na temat sposobu Sprawdź domenę z serwisem Google, zobacz [zweryfikowanie prawa własności lokacji z usługi Google Apps](https://support.google.com/webmasters/answer/35179).

    e. Powtórz poprzednie kroki dla dodatkowe domeny, które mają zostać dodane do usługi Google Apps.
     
     > [!WARNING]
     > Jeśli zmienisz domena podstawowa dla dzierżawy G Suite i jeśli masz już skonfigurowane logowanie jednokrotne z usługą Azure AD, a następnie trzeba powtórzyć krok #3 w obszarze [krok 2: Włącz rejestrację jednokrotną](#step-two-enable-single-sign-on).
       
6. W [konsoli administracyjnej aplikacji Google](http://admin.google.com/), wybierz pozycję **ról administratora**.
   
     ![Wybierz usługi Google Apps][26]

7. Określ konto administratora, którego chcesz użyć do zarządzania, inicjowanie obsługi użytkowników. Aby uzyskać **rolę administratora** tego konta, Edytuj **uprawnienia** dla tej roli. Upewnij się włączyć wszystkie **uprawnień interfejsu API administratora** tak, aby to konto może służyć do inicjowania obsługi administracyjnej.
   
     ![Wybierz usługi Google Apps][27]
   
    > [!NOTE]
    > W przypadku konfigurowania środowiska produkcyjnego, najlepszym rozwiązaniem jest tworzenie konta administratora w zestawie G specjalnie dla tego kroku. Te konta musi mieć rolę administratora skojarzonych z nimi, który ma niezbędne uprawnienia interfejsu API.
     
8. W [portalu Azure](https://portal.azure.com), przejdź do **usługi Azure Active Directory** > **aplikacje przedsiębiorstwa** > **wszystkie aplikacje** sekcji.

9. Jeśli pakiet G został już skonfigurowany dla logowania jednokrotnego, wyszukiwanie wystąpienia zestawu G przy użyciu pola wyszukiwania. W przeciwnym razie wybierz **Dodaj**, a następnie wyszukaj **G Suite** lub **usługi Google Apps** w galerii aplikacji. Wybierz aplikację z wyników wyszukiwania, a następnie dodaj go do listy aplikacji.

10. Wybierz wystąpienia zestawu G, a następnie wybierz **inicjowania obsługi administracyjnej** kartę.

11. Ustaw **tryb obsługi administracyjnej** do **automatyczne**. 

     ![Inicjowanie obsługi](./media/google-apps-provisioning-tutorial/provisioning.png)

12. W obszarze **poświadczeń administratora** zaznacz **autoryzacji**. Okno dialogowe autoryzacji Google zostanie otwarty w nowym oknie przeglądarki.

13. Upewnij się, czy chcesz nadać uprawnień usługi Azure Active Directory do wprowadzania zmian w dzierżawie G Suite. Wybierz **zaakceptować**.
    
     ![Upewnij się, uprawnienia.][28]

14. W portalu Azure wybierz **Testuj połączenie** do upewnij się, że połączenie do aplikacji usługi Azure AD. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto G Suite ma uprawnienia administratora zespołu. Spróbuj **autoryzacji** krok ponownie.

15. Wprowadź adres e-mail osoby lub grupy, który powinien zostać wyświetlony inicjowania obsługi administracyjnej powiadomienia o błędach w **wiadomość E-mail z powiadomieniem** pola. Następnie zaznacz pole wyboru.

16. Wybierz **zapisać.**

17. W obszarze **mapowania** zaznacz **synchronizacji Azure użytkownicy usługi Active Directory do usługi Google Apps**.

18. W **mapowań atrybutów** Przejrzyj atrybuty użytkowników, które są synchronizowane z usługi Azure AD G pakietu. Atrybuty, które są **pasujące** właściwości są używane do dopasowania kont użytkowników w G Suite dla operacji aktualizacji. Wybierz **zapisać** aby zatwierdzić zmiany.

19. Aby włączyć usługi Azure AD, świadczenie usługi pakietu G, zmień **stan inicjowania obsługi administracyjnej** do **na** w **ustawienia**.

20. Wybierz pozycję **Zapisz**.

Ten proces jest uruchamiany wstępna synchronizacja jakichkolwiek użytkowników lub grup, które są przypisane do zestawu G w sekcji Użytkownicy i grupy. Synchronizacji początkowej zajmuje więcej czasu wykonywania niż kolejne synchronizacje, które są wykonywane co około 40 minut, gdy usługa jest uruchomiona. Można użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi administracyjnej Dzienniki aktywności. Te dzienniki opisano wszystkie akcje, które są wykonywane przez usługę inicjowania obsługi administracyjnej w aplikacji.

Aby uzyskać więcej informacji na temat usługi Azure AD, inicjowanie obsługi dzienników do odczytu, zobacz [raportowania na użytkownika automatyczne Inicjowanie obsługi konta](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowizacja konta użytkowników dla aplikacji przedsiębiorstwa](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie rejestracji jednokrotnej](google-apps-tutorial.md)



<!--Image references-->

[10]: ./media/google-apps-provisioning-tutorial/gapps-security.png
[15]: ./media/google-apps-provisioning-tutorial/gapps-api.png
[16]: ./media/google-apps-provisioning-tutorial/gapps-api-enabled.png
[20]: ./media/google-apps-provisioning-tutorial/gapps-domains.png
[21]: ./media/google-apps-provisioning-tutorial/gapps-add-domain.png
[22]: ./media/google-apps-provisioning-tutorial/gapps-add-another.png
[24]: ./media/google-apps-provisioning-tutorial/gapps-provisioning.png
[25]: ./media/google-apps-provisioning-tutorial/gapps-provisioning-auth.png
[26]: ./media/google-apps-provisioning-tutorial/gapps-admin.png
[27]: ./media/google-apps-provisioning-tutorial/gapps-admin-privileges.png
[28]: ./media/google-apps-provisioning-tutorial/gapps-auth.png
