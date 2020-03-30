---
title: Aktywowanie ról zasobów platformy Azure w usłudze PIM — Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak aktywować role zasobów platformy Azure w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: d35c81f7bb478d91bd207327ea37c80aa1778142
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74023147"
---
# <a name="activate-my-azure-resource-roles-in-privileged-identity-management"></a>Uaktywnianie ról zasobów platformy Azure w zarządzania tożsamościami uprzywilejowanymi

Użyj zarządzania tożsamościami uprzywilejowanymi (PIM), aby umożliwić kwalifikującym się członkom roli zasobów platformy Azure planowanie aktywacji dla przyszłej daty i godziny. Mogą również wybrać określony czas trwania aktywacji w ramach maksymalnej (skonfigurowany przez administratorów).

Ten artykuł jest przeznaczony dla członków, którzy muszą aktywować swoją rolę zasobów platformy Azure w zarządzania tożsamościami uprzywilejowanymi.

## <a name="activate-a-role"></a>Aktywowanie roli

Jeśli musisz przejąć rolę zasobów platformy Azure, możesz zażądać aktywacji przy użyciu opcji nawigacji **Moje role** w zarządzania tożsamościami uprzywilejowanymi.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

1. Otwórz **zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD**. Aby uzyskać informacje dotyczące dodawania kafelka zarządzania tożsamościami uprzywilejowanymi do pulpitu nawigacyjnego, zobacz [Rozpoczynanie korzystania z zarządzania tożsamościami uprzywilejowanymi](pim-getting-started.md).

1. Wybierz **pozycję Moje role**.

    ![Strona Moje role z rolami, które możesz aktywować](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. Wybierz **role zasobów platformy Azure,** aby wyświetlić listę kwalifikujących się ról zasobów platformy Azure.

   ![Moje role — strona Role zasobów platformy Azure](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png) 

1. Na liście **Role zasobów platformy Azure** znajdź rolę, którą chcesz aktywować.

    ![Role zasobów platformy Azure — lista kwalifikujących się ról](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. Wybierz **pozycję Aktywuj,** aby otworzyć okienko Aktywuj.

1. Jeśli twoja rola wymaga uwierzytelniania wieloskładnikowego, wybierz pozycję **Zweryfikuj swoją tożsamość przed kontynuowaniem**. Wystarczy uwierzytelnić tylko raz na sesję.

    ![Weryfikowanie mojej tożsamości za pomocą usługi MFA przed aktywacją roli](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Wybierz **pozycję Zweryfikuj moją tożsamość** i postępuj zgodnie z instrukcjami, aby zapewnić dodatkową weryfikację zabezpieczeń.

    ![Ekran, aby zapewnić weryfikację zabezpieczeń, taką jak kod PIN](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Jeśli chcesz określić ograniczony zakres, wybierz **zakres,** aby otworzyć okienko filtru zasobów.

    Najlepszym rozwiązaniem jest żądanie dostępu tylko do potrzebnych zasobów. W okienku filtru zasobów można określić grupy zasobów lub zasoby, do których masz dostęp.

    ![Aktywuj — okienko filtru zasobów w celu określenia zakresu](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. W razie potrzeby określ niestandardową godzinę rozpoczęcia aktywacji. Element członkowski zostanie aktywowany po wybranym czasie.

1. W polu **Przyczyna** wprowadź przyczynę żądania aktywacji.

    ![Ukończono okienko Aktywacja z zakresem, czasem rozpoczęcia, czasem trwania i powodem](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. Wybierz **pozycję Aktywuj**.

    Jeśli rola nie wymaga zatwierdzenia, jest aktywowana i dodawana do listy aktywnych ról. Jeśli chcesz użyć tej roli, wykonaj kroki opisane w następnej sekcji.

    Jeśli [rola wymaga zatwierdzenia,](pim-resource-roles-approval-workflow.md) aby aktywować, w prawym górnym rogu przeglądarki pojawi się powiadomienie informujące o oczekiwanym na zatwierdzenie żądania.

    ![Żądanie aktywacji oczekuje na powiadomienie o zatwierdzeniu](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>Używanie roli natychmiast po aktywacji

W przypadku opóźnienia po aktywacji wykonaj następujące kroki po aktywowaniu, aby natychmiast użyć ról zasobów platformy Azure.

1. Otwórz zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD.

1. Wybierz **pozycję Moje role,** aby wyświetlić listę kwalifikujących się ról usługi Azure AD i ról zasobów platformy Azure.

1. Wybierz **role zasobów platformy Azure**.

1. Wybierz kartę **Aktywne role.**

1. Gdy rola jest aktywna, wyloguj się z portalu i zaloguj się ponownie.

    Rola powinna być teraz dostępna do użycia.

## <a name="view-the-status-of-your-requests"></a>Wyświetlanie stanu swoich żądań

Możesz wyświetlić stan oczekujących żądań do aktywacji.

1. Otwórz zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD.

1. Wybierz **pozycję Moje żądania,** aby wyświetlić listę żądań roli usługi Azure AD i roli zasobów platformy Azure.

    ![Moje żądania — strona zasobów platformy Azure z oczekującymi żądaniami](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

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

    Po aktywowaniu roli zasobów platformy Azure zostaną wyświetlenia etapów aktywacji. Po zakończeniu wszystkich etapów zobaczysz link **Wyloguj**. Możesz użyć tego linku, aby się wylogować. Rozwiąże to większość przypadków opóźnienia aktywacji.

1. W zarządzania tożsamościami uprzywilejowanymi sprawdź, czy jesteś wymieniony jako członek roli.

## <a name="next-steps"></a>Następne kroki

- [Rozszerzanie lub odnawianie ról zasobów platformy Azure w zarządzania tożsamościami uprzywilejowanymi](pim-resource-roles-renew-extend.md)
- [Uaktywnianie ról usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi](pim-how-to-activate-role.md)
