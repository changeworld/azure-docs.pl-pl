---
title: Aktywowanie ról usługi Azure AD w usłudze PIM — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Dowiedz się, jak aktywować role usługi Azure AD w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/28/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f95a1a08189668e5b6f88941069566b00a73bce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77499192"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>Aktywowanie ról usługi Azure AD w usłudze PIM

Zarządzanie tożsamościami uprzywilejowanymi usługi Azure Directory (Azure AD) upraszcza sposób zarządzania przez przedsiębiorstwa uprzywilejowanym dostępem do zasobów w usłudze Azure AD i innych usługach online firmy Microsoft, takich jak Office 365 lub Microsoft Intune.  

Jeśli masz prawo do roli administracyjnej, oznacza to, że można aktywować tę rolę, gdy trzeba wykonać akcje uprzywilejowane. Jeśli na przykład od czasu do czasu zarządzasz funkcjami usługi Office 365, administratorzy ról uprzywilejowanych w organizacji mogą nie uczynić Cię stałym administratorem globalnym, ponieważ ta rola ma wpływ również na inne usługi. Zamiast tego sprawiają, że kwalifikujesz się do ról usługi Azure AD, takich jak Administrator usługi Exchange Online. Możesz poprosić o aktywowanie tej roli, gdy potrzebujesz jej uprawnień, a następnie będziesz mieć kontrolę administratora przez określony okres czasu.

Ten artykuł jest przeznaczony dla administratorów, którzy muszą aktywować swoją rolę usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi.

## <a name="determine-your-version-of-pim"></a>Określanie wersji programu PIM

Począwszy od listopada 2019 r. część ról usługi Azure AD usługi Zarządzanie tożsamościami uprzywilejowanymi jest aktualizowana do nowej wersji, która odpowiada środowiskom ról zasobów platformy Azure. Spowoduje to utworzenie dodatkowych funkcji, a także [zmiany w istniejącym interfejsie API](azure-ad-roles-features.md#api-changes). Gdy nowa wersja jest wdrażana, które procedury, które należy wykonać w tym artykule, zależą od wersji zarządzania tożsamościami uprzywilejowanymi, które obecnie masz. Wykonaj kroki opisane w tej sekcji, aby ustalić, którą wersję zarządzania tożsamościami uprzywilejowanymi masz. Po poznaniu wersji zarządzania tożsamościami uprzywilejowanymi można wybrać procedury w tym artykule, które są zgodne z tą wersją.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) za pomocą roli [administratora ról uprzywilejowanych.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)
1. Otwórz **zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD**. Jeśli u góry strony przeglądu masz baner, postępuj zgodnie z instrukcjami na karcie **Nowa wersja** tego artykułu. W przeciwnym razie postępuj zgodnie z instrukcjami na karcie **Poprzednia wersja.**

    [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[Nowa wersja](#tab/new)

## <a name="activate-a-role"></a>Aktywowanie roli

Jeśli musisz przyjąć rolę usługi Azure AD, możesz zażądać aktywacji przy użyciu opcji nawigacji **Moje role** w zarządzania tożsamościami uprzywilejowanymi.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

1. Otwórz **zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD**. Aby uzyskać informacje dotyczące dodawania kafelka zarządzania tożsamościami uprzywilejowanymi do pulpitu nawigacyjnego, zobacz [Rozpoczynanie korzystania z zarządzania tożsamościami uprzywilejowanymi](pim-getting-started.md).

1. Wybierz **pozycję Moje role**, a następnie wybierz pozycję Role usługi Azure **AD,** aby wyświetlić listę kwalifikujących się ról usługi Azure AD.

    ![Strona Moje role z rolami, które możesz aktywować](./media/pim-how-to-activate-role/my-roles.png)

1. Na liście **role usługi Azure AD** znajdź rolę, którą chcesz aktywować.

    ![Role usługi Azure AD — lista kwalifikujących się ról](./media/pim-how-to-activate-role/activate-link.png)

1. Wybierz **pozycję Aktywuj,** aby otworzyć okienko Aktywuj.

    ![Role usługi Azure AD — strona aktywacji zawiera czas trwania i zakres](./media/pim-how-to-activate-role/activate-page.png)

1. Jeśli twoja rola wymaga uwierzytelniania wieloskładnikowego, wybierz pozycję **Zweryfikuj swoją tożsamość przed kontynuowaniem**. Wystarczy uwierzytelnić tylko raz na sesję.

    ![Weryfikowanie mojej tożsamości za pomocą usługi MFA przed aktywacją roli](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Wybierz **pozycję Zweryfikuj moją tożsamość** i postępuj zgodnie z instrukcjami, aby zapewnić dodatkową weryfikację zabezpieczeń.

    ![Ekran, aby zapewnić weryfikację zabezpieczeń, taką jak kod PIN](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Jeśli chcesz określić ograniczony zakres, wybierz **zakres,** aby otworzyć okienko filtru. W okienku filtru można określić zasoby usługi Azure AD, do których masz dostęp. Najlepszym rozwiązaniem jest żądanie dostępu tylko do zasobów, których potrzebujesz.

1. W razie potrzeby określ niestandardową godzinę rozpoczęcia aktywacji. Rola usługi Azure AD zostanie aktywowana po wybranym czasie.

1. W polu **Przyczyna** wprowadź przyczynę żądania aktywacji.

1. Wybierz **pozycję Aktywuj**.

    Jeśli rola nie wymaga zatwierdzenia, jest aktywowana i dodawana do listy aktywnych ról. Jeśli chcesz użyć tej roli, wykonaj kroki opisane w następnej sekcji.

    ![Ukończono okienko Aktywacja z zakresem, czasem rozpoczęcia, czasem trwania i powodem](./media/pim-how-to-activate-role/azure-ad-activation-status.png)

    Jeśli [rola wymaga zatwierdzenia,](pim-resource-roles-approval-workflow.md) aby aktywować, w prawym górnym rogu przeglądarki pojawi się powiadomienie informujące o oczekiwanym na zatwierdzenie żądania.

    ![Żądanie aktywacji oczekuje na powiadomienie o zatwierdzeniu](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>Używanie roli natychmiast po aktywacji

W przypadku opóźnienia po aktywacji wykonaj następujące kroki po aktywowaniu, aby natychmiast użyć ról usługi Azure AD.

1. Otwórz zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD.

1. Wybierz **pozycję Moje role,** aby wyświetlić listę kwalifikujących się ról usługi Azure AD i ról zasobów platformy Azure.

1. Wybierz **role usługi Azure AD**.

1. Wybierz kartę **Aktywne role.**

1. Gdy rola jest aktywna, wyloguj się z portalu i zaloguj się ponownie.

    Rola powinna być teraz dostępna do użycia.

## <a name="view-the-status-of-your-requests"></a>Wyświetlanie stanu swoich żądań

Możesz wyświetlić stan oczekujących żądań do aktywacji.

1. Otwórz zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD.

1. Wybierz **pozycję Moje żądania,** aby wyświetlić listę żądań roli usługi Azure AD i roli zasobów platformy Azure.

    ![Moje żądania — strona usługi Azure AD przedstawiająca oczekujące żądania](./media/pim-how-to-activate-role/my-requests-page.png)

1. Przewiń w prawo, aby wyświetlić kolumnę **Stan żądania.**

## <a name="cancel-a-pending-request"></a>Anulowanie oczekującego żądania

Jeśli nie wymagasz aktywacji roli, która wymaga zatwierdzenia, możesz anulować oczekujące żądanie w dowolnym momencie.

1. Otwórz zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD.

1. Wybierz **pozycję Moje prośby**.

1. Dla roli, którą chcesz anulować, wybierz łącze **Anuluj.**

    Po wybraniu opcji Anuluj żądanie zostanie anulowane. Aby ponownie aktywować rolę, musisz przesłać nowe żądanie aktywacji.

   ![Moja lista żądań z wyróżnioną akcją Anuluj](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>Rozwiązywanie problemów

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Nie udzielono uprawnień po aktywowaniu roli

Po uaktywnieniu roli w zarządzanie tożsamością uprzywilejowaną aktywacja może nie natychmiast propagować do wszystkich portali, które wymagają roli uprzywilejowanej. Czasami nawet po rozpropagowaniu zmiany buforowanie internetowe w portalu może spowodować, że zmiany nie zostaną natychmiast uwzględnione. Jeśli aktywacja jest opóźniona, oto, co należy zrobić.

1. Wyloguj się z witryny Azure Portal, a następnie zaloguj się ponownie.

    Po aktywowaniu roli usługi Azure AD zostaną wyświetlenia etapów aktywacji. Po zakończeniu wszystkich etapów zobaczysz link **Wyloguj**. Możesz użyć tego linku, aby się wylogować. Rozwiąże to większość przypadków opóźnienia aktywacji.

1. W zarządzania tożsamościami uprzywilejowanymi sprawdź, czy jesteś wymieniony jako członek roli.

# <a name="previous-version"></a>[Poprzednia wersja](#tab/previous)

## <a name="activate-a-role"></a>Aktywowanie roli

Jeśli musisz przejąć rolę usługi Azure AD, możesz zażądać aktywacji przy użyciu opcji nawigacji **Moje role** w zarządzania tożsamościami uprzywilejowanymi.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

1. Otwórz **zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD**. Aby uzyskać informacje dotyczące dodawania kafelka zarządzania tożsamościami uprzywilejowanymi do pulpitu nawigacyjnego, zobacz [Rozpoczynanie korzystania z zarządzania tożsamościami uprzywilejowanymi](pim-getting-started.md).

1. Kliknij pozycję **Role usługi Azure AD**.

1. Kliknij **pozycję Moje role,** aby wyświetlić listę kwalifikujących się ról usługi Azure AD.

    ![Role usługi Azure AD — moje role z listą kwalifikujących się lub aktywnych ról](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. Znajdź rolę, którą chcesz aktywować.

    ![Role usługi Azure AD — lista kwalifikujących się ról z łączem Aktywuj](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. Kliknij **przycisk Aktywuj,** aby otworzyć okienko szczegółów aktywacji roli.

1. Jeśli twoja rola wymaga uwierzytelniania wieloskładnikowego (MFA), kliknij **zweryfikuj swoją tożsamość przed kontynuowaniem**. Wystarczy uwierzytelnić tylko raz na sesję.

    ![Weryfikowanie okienka tożsamości za pomocą usługi MFA przed aktywacją roli](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. Kliknij **zweryfikuj moją tożsamość** i postępuj zgodnie z instrukcjami, aby zapewnić dodatkową weryfikację zabezpieczeń.

    ![Dodatkowa strona weryfikacji zabezpieczeń z pytaniem, jak się z Tobą skontaktować](./media/pim-how-to-activate-role/additional-security-verification.png)

1. Kliknij **przycisk Aktywuj,** aby otworzyć okienko aktywacji.

    ![Okienko aktywacji określające czas rozpoczęcia, czas trwania, bilet i przyczynę](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. W razie potrzeby określ niestandardową godzinę rozpoczęcia aktywacji.

1. Określ czas trwania aktywacji.

1. W polu **Przyczyna aktywacji** wprowadź przyczynę żądania aktywacji. Niektóre role wymagają podania numeru biletu problemowego.

    ![Ukończone okienko aktywacji z niestandardowym czasem rozpoczęcia, czasem trwania, biletem i powodem](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. Kliknij pozycję **Aktywuj**.

    Jeśli rola nie wymaga zatwierdzenia, zostanie wyświetlone okienko **stanu aktywacji,** w które wyświetlają się stan aktywacji.

    ![Strona stanu aktywacji z trzema etapami aktywacji](./media/pim-how-to-activate-role/activation-status.png)

    Po zakończeniu wszystkich etapów kliknij łącze **Wyloguj,** aby wylogować się z witryny Azure portal. Po zalogowaniu się z powrotem do portalu, można teraz użyć roli.

    Jeśli [rola wymaga zatwierdzenia](./azure-ad-pim-approval-workflow.md) do aktywacji, powiadomienie platformy Azure pojawi się w prawym górnym rogu przeglądarki informując, że żądanie oczekuje na zatwierdzenie.

## <a name="view-the-status-of-your-requests"></a>Wyświetlanie stanu swoich żądań

Możesz wyświetlić stan oczekujących żądań do aktywacji.

1. Otwórz zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD.

1. Kliknij pozycję **Role usługi Azure AD**.

1. Kliknij **pozycję Moje prośby,** aby wyświetlić listę twoich żądań.

    ![Role usługi Azure AD — lista moich żądań](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

## <a name="deactivate-a-role"></a>Dezaktywowanie roli

Po aktywowaniu roli automatycznie dezaktywuje się po osiągnięciu limitu czasu (kwalifikowalny czas trwania).

Jeśli zadania administratora zostaną ukończone wcześniej, możesz również ręcznie dezaktywować rolę w usłudze Azure AD Privileged Identity Management.

1. Otwórz zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD.

1. Kliknij pozycję **Role usługi Azure AD**.

1. Kliknij **pozycję Moje role**.

1. Kliknij **pozycję Aktywne role,** aby wyświetlić listę aktywnych ról.

1. Znajdź rolę, której wykonasz, a następnie kliknij pozycję **Dezaktywuj**.

## <a name="cancel-a-pending-request"></a>Anulowanie oczekującego żądania

Jeśli nie wymagasz aktywacji roli, która wymaga zatwierdzenia, możesz anulować oczekujące żądanie w dowolnym momencie.

1. Otwórz zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD.

1. Kliknij pozycję **Role usługi Azure AD**.

1. Kliknij **pozycję Moje prośby**.

1. W przypadku roli, którą chcesz anulować, kliknij przycisk **Anuluj.**

    Po kliknięciu przycisku Anuluj żądanie zostanie anulowane. Aby ponownie aktywować rolę, musisz przesłać nowe żądanie aktywacji.

   ![Lista moich żądań z wyróżnionym przyciskiem Anuluj](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="troubleshoot"></a>Rozwiązywanie problemów

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Nie udzielono uprawnień po aktywowaniu roli

Po uaktywnieniu roli w zarządzanie tożsamością uprzywilejowaną aktywacja może nie natychmiast propagować do wszystkich portali, które wymagają roli uprzywilejowanej. Czasami nawet po rozpropagowaniu zmiany buforowanie internetowe w portalu może spowodować, że zmiany nie zostaną natychmiast uwzględnione. Jeśli aktywacja jest opóźniona, oto, co należy zrobić.

1. Wyloguj się z witryny Azure Portal, a następnie zaloguj się ponownie.

    Po aktywowaniu roli usługi Azure AD zostaną wyświetlenia etapów aktywacji. Po zakończeniu wszystkich etapów zobaczysz link **Wyloguj**. Możesz użyć tego linku, aby się wylogować. Rozwiąże to większość przypadków opóźnienia aktywacji.

1. W zarządzania tożsamościami uprzywilejowanymi sprawdź, czy jesteś wymieniony jako członek roli.

 ---

## <a name="next-steps"></a>Następne kroki

- [Uaktywnianie ról usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi](pim-how-to-activate-role.md)
