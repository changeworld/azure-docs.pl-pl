---
title: Zarządzanie ustawieniami użytkownika dla uwierzytelniania wieloskładnikowego platformy Azure — usługa Azure Active Directory
description: Dowiedz się, jak skonfigurować ustawienia użytkownika usługi Azure Active Directory dla uwierzytelniania wieloskładnikowego platformy Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 048224a55c2bbcbc99281d070d88d34e2dc77168
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309771"
---
# <a name="manage-user-settings-for-azure-multi-factor-authentication"></a>Zarządzanie ustawieniami użytkownika dla uwierzytelniania wieloskładnikowego platformy Azure

Aby ułatwić zarządzanie użytkownikami uwierzytelniania wieloskładnikowego platformy Azure, można wymagać od użytkowników zresetowania hasła, ponownej rejestracji dla usługi MFA lub odwołania istniejących sesji usługi MFA. W przypadku użytkowników, którzy zdefiniowali hasła aplikacji, można również usunąć te hasła, powodując niepowodzenie uwierzytelniania w tych aplikacjach. Te akcje mogą być konieczne, jeśli potrzebujesz pomocy dla użytkownika lub chcesz zresetować jego stan zabezpieczeń.

## <a name="manage-user-authentication-options"></a>Zarządzanie opcjami uwierzytelniania użytkowników

Jeśli przypisano ci rolę *Administrator uwierzytelniania,* możesz wymagać od użytkowników zresetowania hasła, ponownej rejestracji dla usługi MFA lub odwołania istniejących sesji usługi MFA z obiektu użytkownika. Aby zarządzać ustawieniami użytkownika, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie wybierz pozycję**Użytkownicy** >  **usługi Azure Active Directory** > **Wszyscy użytkownicy**.
1. Wybierz użytkownika, na którym chcesz wykonać akcję, i wybierz **pozycję Metody uwierzytelniania**. W górnej części okna wybierz jedną z następujących opcji dla użytkownika:
   - **Resetuj hasło** resetuje hasło użytkownika i przypisuje tymczasowe hasło, które musi zostać zmienione przy następnym loguch.
   - **Wymagaj ponownego rejestrowania usługi MFA** sprawia, że tak, gdy użytkownik zaloguje się następnym razem, są one wymagane, aby skonfigurować nową metodę uwierzytelniania usługi MFA.
   - **Odwołaj sesje usługi MFA** czyści zapamiętane sesje usługi MFA użytkownika i wymaga od nich wykonywania usługi MFA przy następnym wymaganiu przez zasady na urządzeniu.

   ![Zarządzanie metodami uwierzytelniania z witryny Azure portal](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

## <a name="delete-users-existing-app-passwords"></a>Usuwanie istniejących haseł aplikacji przez użytkowników

W razie potrzeby możesz usunąć wszystkie hasła aplikacji utworzone przez użytkownika. Aplikacje inne niż przeglądarki, które były skojarzone z tymi hasłami aplikacji, przestają działać do momentu utworzenia nowego hasła aplikacji. *Uprawnienia administratora globalnego* są wymagane do wykonania tej akcji.

Aby usunąć hasła aplikacji użytkownika, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie wybierz pozycję **Użytkownicy usługi Azure Active Directory** > **Users** > **Wszyscy użytkownicy**.
1. Wybierz pozycję **Uwierzytelnianie wieloskładnikowe**. Może być konieczne przewinięcie w prawo, aby wyświetlić tę opcję menu. Wybierz przykładowy zrzut ekranu poniżej, aby wyświetlić pełne okno witryny Azure portal i lokalizację menu:[![](media/howto-mfa-userstates/selectmfa-cropped.png "Wybieranie uwierzytelniania wieloskładnikowego w oknie Użytkownicy w usłudze Azure AD")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Zaznacz pole obok użytkownika lub użytkowników, którymi chcesz zarządzać. Po prawej stronie pojawi się lista opcji szybkiego kroku.
1. Wybierz **pozycję Zarządzaj ustawieniami użytkownika**, a następnie zaznacz pole wyboru Usuń wszystkie istniejące hasła aplikacji **wygenerowane przez wybranych użytkowników**, jak pokazano na poniższym przykładzie: ![Usuń wszystkie istniejące hasła aplikacji](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. Wybierz **zapisz**, a następnie **zamknij**.

## <a name="next-steps"></a>Następne kroki

Ten artykuł pomógł skonfigurować indywidualne ustawienia użytkownika. Aby skonfigurować ustawienia usługi Azure Multi-Factor Authentication, zobacz [Konfigurowanie ustawień uwierzytelniania wieloskładnikowego platformy Azure](howto-mfa-mfasettings.md)

Jeśli użytkownicy potrzebują pomocy, zapoznaj się z [podręcznikiem użytkownika dla usługi Azure Multi-Factor Authentication](../user-help/multi-factor-authentication-end-user.md).
