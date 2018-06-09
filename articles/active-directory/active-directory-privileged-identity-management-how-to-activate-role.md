---
title: Jak aktywować lub dezaktywować rolę | Dokumentacja firmy Microsoft
description: Dowiedz się, jak aktywować role uprzywilejowane tożsamości w aplikacji Azure Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: protection
ms.date: 02/14/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: ebbac74f68142f7d0528c89f21f8c7cecbb82289
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233400"
---
# <a name="how-to-activate-or-deactivate-roles-in-azure-ad-privileged-identity-management"></a>Jak aktywować lub dezaktywować role w programie Azure AD Privileged Identity Management
Azure Active Directory (AD) Privileged Identity Management upraszcza, jak zarządzać uprzywilejowanego dostępu do zasobów w usłudze Azure AD i innych usług online firmy Microsoft, takich jak usługi Office 365 lub Microsoft Intune w przedsiębiorstwach.  

Jeśli użytkownik wprowadzono uprawnia do skorzystania z rolą administracyjną, oznacza to, że można aktywować tej roli, gdy potrzebne do wykonania uprzywilejowanych akcji. Na przykład jeśli zarządzasz czasami funkcje usługi Office 365, organizacji ról uprzywilejowanych administratorów może nie mieć można stałe administratora globalnego, ponieważ ta rola ma wpływ na inne usługi, za. Zamiast tego należy podjąć ich możesz kwalifikuje się do usługi Azure AD ról, takich jak Exchange Online Administrator. Można zażądać do aktywowania tej roli, gdy potrzebne są uprawnienia jego, a następnie będziesz mieć administratorowi kontrolowanie ustalonej okresu.

Ten artykuł jest dla administratorów, który chcesz aktywować ich roli w usłudze Azure AD Privileged Identity Management (PIM). Przeprowadza użytkownika przez kroki, aby aktywować rolę, gdy potrzebne uprawnienia, a dezaktywować rolę, gdy wszystko będzie gotowe. Ponadto administratorzy ról uprzywilejowanych mogą wymagać zatwierdzenia, aby aktywować rolę (wersja zapoznawcza). Dowiedz się więcej o [przepływów pracy PIM](./privileged-identity-management/azure-ad-pim-approval-workflow.md) tutaj.

## <a name="add-the-privileged-identity-management-application"></a>Dodawanie aplikacji Privileged Identity Management
Korzystanie z aplikacji Azure AD Privileged Identity Management w [portalu Azure](https://portal.azure.com/) żądanie uaktywnienia roli, nawet jeśli chcesz zacząć działać w innym portalu lub programu PowerShell. Jeśli nie masz aplikacji usługi Azure AD Privileged Identity Management w portalu Azure, wykonaj następujące kroki, aby rozpocząć pracę.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Wybierz swoją nazwę użytkownika w prawym górnym rogu portalu Azure i wybierz katalog, w której będzie można działać.
3. Wybierz pozycję **Wszystkie usługi** i użyj pola tekstowego filtru, aby wyszukać usługę **Azure AD Privileged Identity Management**.
4. Zaznacz opcję **Przypnij do pulpitu nawigacyjnego**, a następnie kliknij pozycję **Utwórz**. Nastąpi otwarcie aplikacji Privileged Identity Management.

## <a name="activate-a-role"></a>Aktywować rolę
Gdy potrzebne do podjęcia roli można żądania aktywacji, wybierając **Moje ról** opcji nawigacji w aplikacji Azure AD Privileged Identity Management lewej formantu kolumny nawigacji.

1. Zaloguj się do [portalu Azure](https://portal.azure.com/) i wybierz Kafelek usługi Azure AD Privileged Identity Management.
2. Wybierz **Moje ról**. Lista przypisane role kwalifikujących się pojawiają się w grupowaniu w górnej części strony.
3. Wybierz rolę do aktywacji.
4. Wybierz **aktywować**. **Żądania aktywacji roli** zostanie wyświetlony blok.
5. Niektóre role wymagają uwierzytelniania wieloskładnikowego (MFA), zanim będzie można aktywować rolę. Wystarczy do uwierzytelniania na początku sesji.
   
    ![Sprawdź za pomocą usługi MFA przed Aktywacja roli — zrzut ekranu][2]
6. Wprowadź przyczynę żądania aktywacji w polu tekstowym.  Niektóre role wymagają podania numeru biletów problemów.
7. Kliknij przycisk **OK**.  Jeśli rola wymaga zatwierdzenia, jest uaktywniony i roli zostanie wyświetlona na liście aktywnych ról (bezpośrednio pod listę przypisań ról kwalifikujących się). Jeśli [rola wymaga zatwierdzenia](./privileged-identity-management/azure-ad-pim-approval-workflow.md) aby aktywować, wyskakujące powiadomienie krótko pojawi się w prawym górnym narożniku przeglądarki informujące o tym, żądanie oczekuje na zatwierdzenie.

    ![Żądania oczekujące powiadomienia — zrzut ekranu][3]

## <a name="deactivate-a-role"></a>Dezaktywować rolę
Po uaktywnieniu roli automatycznie dezaktywuje to, gdy zostanie osiągnięty limit czasu (kwalifikujących się czas trwania).

Po ukończeniu zadań administracyjnych wcześniej, można również dezaktywować rolę ręcznie w aplikacji Azure AD Privileged Identity Management.  Wybierz **Moje ról**, wybierz rolę, wszystko gotowe przy użyciu z **przypisania roli Active** grupowania i wybierz **Dezaktywuj**.  

## <a name="cancel-a-pending-request"></a>Anulowanie oczekującego żądania
W przypadku, gdy nie jest wymagana aktywacja roli, która wymaga zatwierdzenia, możesz anulować w dowolnym momencie oczekującego żądania. Po prostu zaznacz **Moje ról** opcji nawigacji w aplikacji Azure AD Privileged Identity Management lewej formantu kolumny nawigacji.

1. Zaloguj się do [portalu Azure](https://portal.azure.com/) i wybierz Kafelek usługi Azure AD Privileged Identity Management.
2. Wybierz **Moje ról**. Lista przypisane role kwalifikujących się pojawiają się w grupowaniu w górnej części strony.
3. Wybierz rolę.
4. Wybierz **aktywacji oczekuje na zatwierdzenie** transparent roli aktywacji szczegóły blok.
5. Wybierz **anulować** w górnej części **oczekują na zatwierdzenie** bloku.

   ![Anulowanie oczekującego żądania zrzut ekranu][4]

## <a name="next-steps"></a>Kolejne kroki
Jeśli chcesz dowiedzieć się więcej o usłudze Azure AD Privileged Identity Management, poniższe linki mają więcej informacji.

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_activation_MFA.png
[3]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_Request_Pending_Toast2.png
[4]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_Request_Pending_Banner_Cancel.png
