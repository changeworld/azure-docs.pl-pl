---
title: Tworzenie obszarów roboczych dla portali my Apps w Azure Active Directory | Microsoft Docs
description: Za pomocą obszarów roboczych moje aplikacje Dostosuj strony Moje aplikacje, aby uprościć środowisko moje aplikacje dla użytkowników końcowych. Organizuj aplikacje w grupy z osobnymi kartami.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9efe76f15e2f07495a9d95f69b0c21d2bca4ea8
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73180552"
---
# <a name="create-workspaces-on-the-my-apps-preview-portal"></a>Tworzenie obszarów roboczych w portalu My Apps (wersja zapoznawcza)

Użytkownicy mogą korzystać z portalu My Apps (wersja zapoznawcza) w celu wyświetlania i uruchamiania aplikacji opartych na chmurze, do których mają dostęp. Domyślnie wszystkie aplikacje, do których użytkownik ma dostęp, są wyświetlane razem na jednej stronie. Aby lepiej zorganizować Tę stronę dla użytkowników, jeśli masz licencję na Azure AD — wersja Premium P1 lub P2, możesz skonfigurować obszary robocze. Za pomocą obszaru roboczego można grupować wspólnie powiązane aplikacje (na przykład według roli zadania, zadania lub projektu) i wyświetlać je na osobnej karcie. Obszar roboczy zasadniczo stosuje filtr do aplikacji, do których użytkownik może mieć dostęp, więc użytkownik widzi tylko te aplikacje w obszarze roboczym, które zostały do nich przypisane.

> [!NOTE]
> W tym artykule opisano sposób, w jaki administrator może włączyć i utworzyć obszary robocze. Aby uzyskać informacje dotyczące korzystania przez użytkownika końcowego z portalu Moje aplikacje i obszarów roboczych, zobacz temat [dostęp i używanie obszarów roboczych](https://docs.microsoft.com/azure/active-directory/user-help/).

## <a name="enable-my-apps-preview-features"></a>Włącz funkcje w wersji zapoznawczej moich aplikacji

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako administrator użytkownika lub Administrator globalny.

2. Przejdź do pozycji **Azure Active Directory** > **Ustawienia użytkownika**.

3. W obszarze **Podgląd funkcji użytkownika**wybierz pozycję **Zarządzaj ustawieniami wersji zapoznawczej funkcji użytkownika**.

4. W obszarze **Użytkownicy mogą używać funkcji w wersji zapoznawczej dla moich aplikacji**, wybierz jedną z następujących opcji:
   * **Wybrane** — włącza funkcje w wersji zapoznawczej dla określonej grupy. Użyj opcji **Wybierz grupę** , aby wybrać grupę, dla której chcesz włączyć funkcje w wersji zapoznawczej.  
   * **Wszystkie** — włącza funkcje w wersji zapoznawczej dla wszystkich użytkowników.

   ![Funkcje w wersji zapoznawczej użytkownika](media/access-panel-workspaces/user-preview-features.png)

> [!NOTE]
> Aby otworzyć Portal moje aplikacje, użytkownicy mogą korzystać z linku `https://myapps.microsoft.com` lub niestandardowego linku dla organizacji, takiego jak `https://myapps.microsoft.com/contoso.com`. Jeśli użytkownicy nie zostaną przekierowani do wersji zapoznawczej moje aplikacje, użytkownicy powinni próbować `https://myapplications.microsoft.com` lub `https://myapplications.microsoft.com/contoso.com`.

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Aby utworzyć obszar roboczy, musisz mieć licencję na Azure AD — wersja Premium P1 lub P2.

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako administrator z licencją Azure AD — wersja Premium P1 lub P2.

2. Przejdź do **Azure Active Directory** > **aplikacje dla przedsiębiorstw**.

3. W obszarze **Zarządzaj**wybierz pozycję **obszary robocze (wersja zapoznawcza)** .

4. Wybierz pozycję **Nowy obszar roboczy**. Na stronie **Nowy obszar roboczy** wprowadź **nazwę** obszaru roboczego (w nazwie nie zaleca się używania "obszaru roboczego"). Następnie wprowadź **Opis**.

   ![Utwórz nowy obszar roboczy](media/access-panel-workspaces/new-workspace.png)

5. Wybierz pozycję **Przegląd + utwórz**. Zostaną wyświetlone właściwości nowego obszaru roboczego.

6. Wybierz kartę **aplikacje** . W obszarze **Dodaj aplikacje**zaznacz wszystkie aplikacje, które chcesz dodać do obszaru roboczego, lub użyj pola **wyszukiwania** , aby znaleźć aplikacje. 

   ![Dodawanie aplikacji do obszaru roboczego](media/access-panel-workspaces/add-applications.png)

7. Wybierz pozycję **Dodaj**. Zostanie wyświetlona lista wybranych aplikacji. Możesz użyć strzałek w górę i w dół, aby zmienić kolejność aplikacji na liście.

   ![Lista aplikacji w obszarze roboczym](media/access-panel-workspaces/add-applications-list.png)

8. Wybierz kartę **Użytkownicy i grupy** . Aby dodać użytkownika lub grupę, wybierz pozycję **Dodaj użytkownika**. 

9. Na stronie **Wybierz członków** wybierz użytkowników lub grupy, do których chcesz przypisać obszar roboczy. Lub użyj pola **wyszukiwania** , aby znaleźć użytkowników lub grupy.

   ![Przypisywanie użytkowników i grup do obszaru roboczego](media/access-panel-workspaces/add-users-and-groups.png)

10. Po zakończeniu wybierania pozycji Użytkownicy i grupy wybierz **pozycję Wybierz**.

11. Aby zmienić rolę użytkownika z dostępu do **odczytu** na **właściciela** lub odwrotnie, kliknij bieżącą rolę i wybierz nową rolę.

    ![Przypisywanie ról do użytkowników i grup](media/access-panel-workspaces/users-groups-list-role.png)

## <a name="view-audit-logs"></a>Wyświetlanie dzienników inspekcji

Dzienniki inspekcji rejestrują operacje obszarów roboczych aplikacji, w tym tworzenie obszaru roboczego — akcje użytkownika końcowego. Następujące zdarzenia są generowane przez moje aplikacje:

* Tworzenie obszaru roboczego
* Edytowanie obszaru roboczego
* Usuwanie obszaru roboczego
* Uruchom aplikację (użytkownik końcowy)
* Dodawanie aplikacji samoobsługowej (użytkownik końcowy)
* Usuwanie aplikacji samoobsługowej (użytkownik końcowy)

Możesz uzyskać dostęp do dzienników inspekcji w [Azure Portal](https://portal.azure.com) , wybierając pozycję **Azure Active Directory** > **aplikacje dla przedsiębiorstw** > **inspekcje dzienników** w sekcji działanie. W obszarze **Usługa**wybierz pozycję **Moje aplikacje**.

   ![Przypisywanie ról do użytkowników i grup](media/access-panel-workspaces/audit-log-myapps.png)


## <a name="next-steps"></a>Następne kroki
[Środowisko użytkownika końcowego dla aplikacji w Azure Active Directory](end-user-experiences.md)