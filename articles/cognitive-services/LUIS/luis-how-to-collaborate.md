---
title: Współpraca z innymi osobami — USŁUGA LUIS
titleSuffix: Azure Cognitive Services
description: Właściciel aplikacji może dodawać współautorów do zasobu autora. Współautorzy mogą modyfikować model, trenować i publikować aplikację.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 913a2b26f67773d9fafbc0a8430d121fbabb97cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053454"
---
# <a name="add-contributors-to-your-app"></a>Dodawanie współautorów do aplikacji

Właściciel aplikacji może dodawać współautorów do aplikacji. Ci współpracownicy mogą modyfikować model, trenować i publikować aplikację. 

[!INCLUDE [Manage collaborators and contributors based on migrated or not-migrated apps](./includes/manage-contributor-collaborator-migration.md)]

## <a name="add-contributor-to-azure-authoring-resource"></a>Dodawanie współautora do zasobu tworzenia platformy Azure

Poniższa procedura jest dla wszystkich użytkowników, którzy **zmigrowali** do korzystania z zasobu tworzenia platformy Azure.

Migracja została zmigrowana, jeśli środowisko tworzenia usługi LUIS jest powiązane z zasobem tworzenia na stronie **Zarządzanie zasobami platformy Azure >** w portalu usługi LUIS.

1. W witrynie Azure portal znajdź zasób tworzenia języka (LUIS). Ma typ `LUIS.Authoring`.
1. Na stronie **Kontrola dostępu (IAM)** tego zasobu wybierz pozycję **+Dodaj,** a następnie wybierz pozycję **Dodaj przypisanie roli**.

    ![W witrynie Azure portal dodaj przypisanie roli do zasobu tworzenia.](./media/luis-how-to-collaborate/authoring-resource-access-control-add-role.png)

1. W oknie **Dodawanie przypisania roli** wybierz **rolę** współautora. W opcji **Przypisz dostęp do** wybierz **pozycję Użytkownik, grupa lub podmiot usługi Azure AD**. W opcji **Wybierz** wprowadź adres e-mail użytkownika. Jeśli użytkownik jest znany z więcej niż 1 adresu e-mail dla tej samej domeny, upewnij się, że wprowadź _podstawowe_ konto e-mail.

    ![Dodawanie wiadomości e-mail użytkownika do roli współautora usługi Azure AD](./media/luis-how-to-collaborate/add-role-assignment-for-contributor.png)

    Po znalezieniu wiadomości e-mail użytkownika wybierz konto i wybierz pozycję **Zapisz**. 

    Jeśli masz problemy z tym przypisaniem roli, przejrzyj [przypisania ról platformy Azure](../../role-based-access-control/role-assignments-portal.md) i [rozwiązywanie problemów z kontrolą dostępu platformy Azure.](../../role-based-access-control/troubleshooting.md#problems-with-azure-role-assignments)

## <a name="add-collaborator-to-luis-app"></a>Dodawanie współpracownika do aplikacji usługi LUIS

Poniższa procedura jest dla wszystkich użytkowników, którzy **nie zostały zmigrowane** do korzystania z zasobu tworzenia platformy Azure.

Nie zostały zmigrowane, jeśli środowisko tworzenia usługi LUIS nie jest powiązane z zasobem tworzenia na stronie **Zarządzanie zasobami platformy Azure >** w portalu usługi LUIS.

Aplikacja ma jednego autora, właściciela, ale może mieć wielu współpracowników. Aby umożliwić współpracownikom edytowanie aplikacji usługi LUIS, należy dodać wiadomości e-mail, których używają do uzyskiwania dostępu do portalu usługi LUIS do listy współpracowników. Po dodaniu aplikacja jest wyświetlana w portalu usługi LUIS.

1. Wybierz **pozycję Zarządzaj** z prawego górnego menu, a następnie wybierz polecenie **Współpracownicy** w menu po lewej stronie.

1. Wybierz **pozycję Dodaj współpracownika** na pasku narzędzi.

1. Wprowadź adres e-mail używany przez współpracownika do logowania się do portalu usługi LUIS.

    ![Dodawanie adresu e-mail współpracownika](./media/luis-how-to-collaborate/add-collaborator-pop-up.png)


### <a name="users-with-multiple-emails"></a>Użytkownicy z wieloma wiadomościami e-mail 

Jeśli dodasz współautorów/współpracowników do aplikacji usługi LUIS, określasz dokładny adres e-mail. Usługa Azure Active Directory (Azure AD) umożliwia jednemu użytkownikowi zamiennie używane więcej niż jedno konto e-mail, usługa LUIS wymaga od użytkownika zalogowania się przy użyciu adresu e-mail określonego podczas dodawania współautora/współpracownika.

<a name="owner-and-collaborators"></a>

### <a name="azure-active-directory-resources"></a>Zasoby usługi Azure Active Directory

Jeśli używasz [usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) (Azure AD) w organizacji, zrozumienie języka (LUIS) wymaga uprawnień do informacji o dostępie użytkowników, gdy chcą korzystać z usługi LUIS. Zasoby, które wymaga usługi LUIS są minimalne. 

Szczegółowy opis jest widoczny podczas próby zarejestrowania się przy zaliczeniu konta, które ma zgodę administratora lub nie wymaga zgody administratora, na przykład zgody administratora:

* Umożliwia zalogowanie się do aplikacji za pomocą konta organizacji i umożliwienie aplikacji odczytu profilu. Pozwala również aplikacji na odczytanie podstawowych informacji o firmie. Daje to uprawnienie usługi LUIS do odczytu podstawowych danych profilu, takich jak identyfikator użytkownika, adres e-mail, nazwa
* Umożliwia aplikacji wyświetlanie i aktualizowanie danych, nawet jeśli nie korzystasz z aplikacji. Uprawnienie jest wymagane do odświeżenia tokenu dostępu użytkownika.


### <a name="azure-active-directory-tenant-user"></a>Użytkownik dzierżawy usługi Azure Active Directory

Usługa LUIS korzysta ze standardowego przepływu zgody usługi Azure Active Directory (Azure AD). 

Administrator dzierżawy powinien współpracować bezpośrednio z użytkownikiem, który potrzebuje dostępu przyznanego do korzystania z usługi LUIS w usłudze Azure AD. 

* Najpierw użytkownik loguje się do usługi LUIS i widzi wyskakujące okno dialogowe wymagające zatwierdzenia przez administratora. Użytkownik kontaktuje się z administratorem dzierżawy przed kontynuowaniem. 
* Po drugie administrator dzierżawy loguje się do usługi LUIS i widzi wyskakujące okno dialogowe przepływu zgody. Jest to okno dialogowe, które administrator musi udzielić użytkownikowi uprawnień. Gdy administrator zaakceptuje uprawnienie, użytkownik może kontynuować korzystanie z usługi LUIS. Jeśli administrator dzierżawy nie zaloguje się do usługi LUIS, administrator może uzyskać dostęp do [zgody](https://account.activedirectory.windowsazure.com/r#/applications) na usługę LUIS, pokazano na poniższym zrzucie ekranu. Zwróć uwagę, że lista jest filtrowana do elementów, które zawierają nazwę `LUIS`.

![Uprawnienie usługi Azure active directory przez witrynę sieci Web aplikacji](./media/luis-how-to-collaborate/tenant-permissions.png)

Jeśli administrator dzierżawy chce tylko niektórych użytkowników do korzystania z usługi LUIS, istnieje kilka możliwych rozwiązań:
* Udzielanie "zgody administratora" (zgoda dla wszystkich użytkowników usługi Azure AD), ale następnie ustawić na "Tak" "Wymagane przypisanie użytkownika" w obszarze Właściwości aplikacji przedsiębiorstwa, a na koniec przypisać/dodać tylko pożądanych użytkowników do aplikacji. Dzięki tej metodzie administrator nadal udziela aplikacji "zgody administratora", jednak można kontrolować użytkowników, którzy mają do niej dostęp.
* Drugim rozwiązaniem jest użycie [interfejsu API zarządzania tożsamościami i dostępem usługi Azure AD w programie Microsoft Graph w](https://docs.microsoft.com/graph/azuread-identity-access-management-concept-overview) celu udzielenia zgody każdemu określonej użytkownikowi. 

Dowiedz się więcej o użytkownikach usługi Azure active directory i zgodę: 
* [Ograniczanie aplikacji](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) do zestawu użytkowników

## <a name="next-steps"></a>Następne kroki

* Dowiedz [się, jak używać wersji](luis-how-to-manage-versions.md) do kontrolowania cyklu życia aplikacji.
* Poznaj pojęcia, w tym [zasobów autora](luis-concept-keys.md#authoring-key) i [współautorów](luis-concept-keys.md#contributions-from-other-authors) tego zasobu.
* Dowiedz [się, jak tworzyć](luis-how-to-azure-subscription.md) zasoby tworzenia i środowiska wykonawczego
* Migrowanie do nowego [zasobu autora](luis-migration-authoring.md) 
