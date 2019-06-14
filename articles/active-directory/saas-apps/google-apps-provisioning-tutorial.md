---
title: 'Samouczek: Konfigurowanie usługi G Suite dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Informacje o sposobie automatycznego aprowizowania lub cofania aprowizacji kont użytkowników z usługi Azure AD do usługi G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea1f4d4a6b60961515826a1ba7409bf149b318e8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60276972"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie usługi G Suite dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest przedstawiają sposób automatycznego aprowizowania lub cofania aprowizacji kont użytkowników z usługi Azure Active Directory (Azure AD) do usługi G Suite.

> [!NOTE]
> W tym samouczku opisano łącznika, który został zbudowany na podstawie usługi aprowizacji użytkownika usługi Azure AD. Ważne szczegółowe informacje na temat tej usługi nie, jak działa i często zadawane pytania, [Automatyzowanie aprowizacji użytkowników i anulowania obsługi do aplikacji SaaS w usłudze Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą G Suite, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Subskrypcja usługi G Suite z obsługą logowania jednokrotnego
- Subskrypcja usługi Google Apps lub usługi Google Cloud Platform

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assign-users-to-g-suite"></a>Przypisywanie użytkowników do usługi G Suite

Usługa Azure Active Directory używa koncepcji o nazwie "przypisania", aby określić, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście Inicjowanie obsługi administracyjnej konta użytkowników są synchronizowane tylko użytkowników i grup, które "przypisano" do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączyć usługę aprowizacji, musisz zdecydować, którzy użytkownicy lub grupy w usłudze Azure AD muszą mieć dostęp do aplikacji. Po utworzeniu tej decyzji, tych użytkowników można przypisać do swojej aplikacji, postępując zgodnie z instrukcjami w [przypisać użytkownika lub grupy do aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

> [!IMPORTANT]
> Zaleca się pojedynczego użytkownika usługi Azure AD można przypisać do usługi G Suite, aby przetestować konfigurację aprowizacji. Później można przypisać dodatkowych użytkowników i grup.
> 
> Gdy użytkownik jest przypisany do usługi G Suite, wybierz pozycję **użytkownika** lub **grupy** roli w oknie dialogowym przydział. **Domyślnego dostępu** roli nie działa dla inicjowania obsługi administracyjnej.

## <a name="enable-automated-user-provisioning"></a>Włączanie użytkownika automatyczne Inicjowanie obsługi administracyjnej.

Ta sekcja przeprowadzi Cię przez proces nawiązywania połączenia z usługi Azure AD, do konta użytkownika, inicjowania obsługi interfejsu API usługi G Suite. Pomaga również możesz skonfigurować usługę aprowizacji, aby tworzyć, aktualizować i wyłączania kont przypisanych użytkowników w usłudze G Suite, w oparciu o użytkownika i przypisanie do grupy w usłudze Azure AD.

>[!TIP]
>Można także włączyć opartej na SAML logowanie jednokrotne dla zestawów G, postępując zgodnie z instrukcjami wyświetlanymi w [witryny Azure portal](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatyczną aprowizację, chociaż te dwie funkcje uzupełnienie siebie nawzajem.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurowanie automatycznej konta aprowizacji użytkowników

> [!NOTE]
> Automatyzowanie aprowizacji użytkowników do usługi G Suite innego rentowną opcją jest użycie [usługi Google Apps Directory Sync (GADS)](https://support.google.com/a/answer/106368?hl=en). GADS aprowizuje Twoich tożsamości: lokalnej usługi Active Directory do usługi G Suite. Z kolei rozwiązania, w tym samouczku aprowizuje użytkowników usługi Azure Active Directory (w chmurze) i grupy usługi G Suite i włączona obsługa poczty e-mail. 

1. Zaloguj się do [konsoli administracyjnej aplikacje Google](https://admin.google.com/) przy użyciu konta administratora, a następnie wybierz **zabezpieczeń**. Jeśli nie widzisz łącza może być ukryty pod przyciskiem **więcej formantów** menu wyświetlanym u dołu ekranu.

    ![Wybierz opcję Zabezpieczenia.][10]

1. Na **zabezpieczeń** wybierz opcję **dokumentacja interfejsu API**.

    ![Wybierz odwołanie do interfejsu API.][15]

1. Wybierz **dostęp do włączenia interfejsu API**.

    ![Wybierz odwołanie do interfejsu API.][16]

   > [!IMPORTANT]
   > Dla każdego użytkownika, który ma zostać aprowizować do usługi G Suite, nazwa ich użytkownika w usłudze Azure Active Directory *musi* ograniczeni do domeny niestandardowej. Na przykład użytkownik nazwy tego wyglądały bob@contoso.onmicrosoft.com nie są akceptowane przez usługi G Suite. Z drugiej strony bob@contoso.com jest akceptowane. Możesz zmienić istniejącego użytkownika domeny, edytując ich właściwości w usłudze Azure AD. Dołączyliśmy instrukcje dotyczące sposobu ustawiania domeny niestandardowej dla usługi Azure Active Directory i usługi G Suite w poniższych krokach.

1. Jeśli nie dodano jeszcze niestandardowej nazwy domeny do usługi Azure Active Directory, następnie wykonaj następujące czynności:
  
    a. W [witryny Azure portal](https://portal.azure.com), w okienku nawigacji po lewej stronie wybierz **usługi Active Directory**. Na liście katalogu wybierz swój katalog.

    b. Wybierz **nazwy domeny** w okienku nawigacji po lewej stronie, a następnie wybierz **Dodaj**.

    ![Domain](./media/google-apps-provisioning-tutorial/domain_1.png)

    ![Dodawanie domeny](./media/google-apps-provisioning-tutorial/domain_2.png)

    c. Wpisz nazwę domeny do **nazwy domeny** pola. Ta nazwa domeny powinny być tą samą nazwą domeny, który ma być używana dla usługi G Suite. Następnie wybierz pozycję **dodawania domeny** przycisku.

    ![Nazwa domeny](./media/google-apps-provisioning-tutorial/domain_3.png)

    d. Wybierz **dalej** aby przejść do strony weryfikacji. Aby sprawdzić, czy jesteś właścicielem tej domeny, należy edytować rekordy DNS w domenie, zgodnie z wartościami, które znajdują się na tej stronie. Można sprawdzić za pomocą **rekordów MX** lub **rekordów TXT**, w zależności od wybrania dla **typu rekordu** opcji.

    Aby uzyskać bardziej szczegółowe instrukcje dotyczące sposobu zweryfikować nazwy domeny z usługą Azure AD, zobacz [Dodaj własną nazwę domeny do usługi Azure AD](https://go.microsoft.com/fwLink/?LinkID=278919&clcid=0x409).

    ![Domain](./media/google-apps-provisioning-tutorial/domain_4.png)

    e. Powtórz te czynności dla wszystkich domen, które mają zostać dodane do katalogu.

    > [!NOTE]
    > Inicjowanie obsługi użytkowników, domena niestandardowa musi odpowiadać nazwa domeny źródła usługi Azure AD. Jeśli nie są zgodne, można rozwiązać problem, implementowanie dostosowywania mapowanie atrybutu.

1. Teraz, gdy sprawdzeniu wszystkich domen w usłudze Azure AD, należy sprawdzić je ponownie przy użyciu usługi Google Apps. Dla każdej domeny, który nie jest już zarejestrowany za pomocą usługi Google wykonaj następujące czynności:

    a. W [konsoli administracyjnej usługi Google Apps](https://admin.google.com/), wybierz opcję **domen**.

    ![Wybierz domeny][20]

    b. Wybierz **dodać domenę lub alias domeny**.

    ![Dodaj nową domenę][21]

    c. Wybierz **Dodaj inną domenę**, a następnie wpisz nazwę domeny, który chcesz dodać.

    ![Wpisz nazwę domeny][22]

    d. Wybierz **Kontynuuj i weryfikowanie własności domeny**. Następnie postępuj zgodnie z instrukcjami, aby sprawdzić, czy jesteś właścicielem nazwy domeny. Aby uzyskać kompleksowe instrukcje dotyczące sposobu weryfikowania Twojej domeny za pomocą usługi Google, zobacz [Sprawdź Twoja własność lokacji za pomocą usługi Google Apps](https://support.google.com/webmasters/answer/35179).

    e. Powtórz poprzednie kroki dla dodatkowych domenach, które mają zostać dodane do usługi Google Apps.

    > [!WARNING]
    > Jeśli zmienisz domenę podstawową dla dzierżawy usługi G Suite i skonfigurowano już logowanie jednokrotne z usługą Azure AD, musisz powtórzyć krok #3 w sekcji Krok 2: Włącz logowanie jednokrotne.

1. W [konsoli administracyjnej aplikacje Google](https://admin.google.com/), wybierz opcję **ról administratora**.

    ![Wybierz usługi Google Apps][26]

1. Określ konto administratora, które chcesz użyć do zarządzania, inicjowanie obsługi użytkowników. Aby uzyskać **rolę administratora** tego konta, Edytuj **uprawnienia** dla tej roli. Upewnij się włączyć wszystkie **uprawnień interfejsu API administratora** tak, aby to konto może służyć do inicjowania obsługi.

    ![Wybierz usługi Google Apps][27]

    > [!NOTE]
    > W przypadku konfigurowania środowiska produkcyjnego, najlepszym rozwiązaniem jest tworzenie konta administratora w usłudze G Suite specjalnie dla tego kroku. Te konta muszą mieć rolę administratora skojarzonych z nimi, które ma niezbędne uprawnienia interfejsu API.

1. W [witryny Azure portal](https://portal.azure.com), przejdź do **usługi Azure Active Directory** > **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje** sekcji.

1. Jeśli już skonfigurowano usługi G Suite dla logowania jednokrotnego, wyszukiwania dla swojego wystąpienia usługi G Suite, przy użyciu pola wyszukiwania. W przeciwnym razie wybierz **Dodaj**, a następnie wyszukaj **usługi G Suite** lub **Google Apps** w galerii aplikacji. Wybierz aplikację z poziomu wyników wyszukiwania, a następnie dodaj go do listy aplikacji.

1. Wybierz wystąpienie usługi G Suite, a następnie wybierz **aprowizacji** kartę.

1. Ustaw **tryb obsługi administracyjnej** do **automatyczne**. 

    ![Inicjowanie obsługi](./media/google-apps-provisioning-tutorial/provisioning.png)

1. W obszarze **poświadczeń administratora** zaznacz **Autoryzuj**. Otwiera okno dialogowe autoryzacji Google w nowym oknie przeglądarki.

1. Upewnij się, że chcesz przyznać uprawnienia usługi Azure Active Directory, aby wprowadzić zmiany do dzierżawy usługi G Suite. Wybierz pozycję **Zaakceptuj**.

    ![Upewnij się, uprawnienia.][28]

1. W witrynie Azure portal wybierz **Testuj połączenie** do upewnij się, że połączenie usługi Azure AD do swojej aplikacji. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto usługi G Suite, ma uprawnienia administratora zespołu. Spróbuj **Autoryzuj** krok ponownie.

1. Wprowadź adres e-mail osoby lub grupy, który powinien zostać wyświetlony inicjowania obsługi administracyjnej powiadomienia o błędach w **wiadomość E-mail z powiadomieniem** pola. Następnie zaznacz pole wyboru.

1. Wybierz przycisk **Save** (Zapisz).

1. W obszarze **mapowania** zaznacz **synchronizacji Azure użytkownicy usługi Active Directory do usługi Google Apps**.

1. W **mapowania atrybutów** Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do usługi G Suite. Atrybuty, które są **zgodne** właściwości są używane do dopasowania kont użytkowników w usłudze G Suite dla operacji aktualizacji. Wybierz **Zapisz** aby zatwierdzić zmiany.

1. Aby włączyć usługi Azure AD, inicjowania obsługi usługi G Suite, zmień **stanie aprowizacji** do **na** w **ustawienia**.

1. Wybierz pozycję **Zapisz**.

Ten proces jest uruchamiany początkowa synchronizacja jakichkolwiek użytkowników lub grup, które są przypisane do usługi G Suite w sekcji Użytkownicy i grupy. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które są wykonywane co około 40 minut w przypadku, gdy usługa jest uruchomiona. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi dzienników aktywności. Te dzienniki opisano wszystkie akcje, które są wykonywane przez usługę aprowizacji w aplikacji.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie logowania jednokrotnego](google-apps-tutorial.md)

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
