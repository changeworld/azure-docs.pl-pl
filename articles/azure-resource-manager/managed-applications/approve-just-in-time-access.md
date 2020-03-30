---
title: Zatwierdzanie dostępu just in time
description: W tym artykule opisano, jak konsumenci aplikacji zarządzanych platformy Azure zatwierdzają żądania dostępu just-in-time do aplikacji zarządzanej.
author: MSEvanhi
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 4a3604a3256fb2d3f4253891bbc28c7685748cd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651022"
---
# <a name="configure-and-approve-just-in-time-access-for-azure-managed-applications"></a>Konfigurowanie i zatwierdzanie dostępu just-in-time dla aplikacji zarządzanych platformy Azure

Jako konsument zarządzanej aplikacji możesz nie być wygodny, dając wydawcy stały dostęp do zarządzanej grupy zasobów. Aby zapewnić większą kontrolę nad udzielaniem dostępu do zasobów zarządzanych, usługa Azure Managed Applications udostępnia funkcję o nazwie dostęp just-in-time (JIT), która jest obecnie w wersji zapoznawczej. Umożliwia zatwierdzanie, kiedy i jak długo wydawca ma dostęp do grupy zasobów. Wydawca może w tym czasie dokonywać wymaganych aktualizacji, ale po upływie tego czasu wygasa dostęp wydawcy.

Przepływ pracy w celu przyznania dostępu wynosi:

1. Wydawca dodaje zarządzaną aplikację do portalu marketplace i określa, że dostęp JIT jest dostępny.

1. Podczas wdrażania można włączyć dostęp JIT dla wystąpienia aplikacji zarządzanej.

1. Po wdrożeniu można zmienić ustawienia dostępu JIT.

1. Wydawca wysyła żądanie dostępu.

1. Zatwierdzić żądanie.

W tym artykule koncentruje się na działaniach podejmowanych przez konsumentów w celu umożliwienia dostępu JIT i zatwierdzania żądań. Aby dowiedzieć się więcej o publikowaniu aplikacji zarządzanej z dostępem JIT, zobacz [Żądanie dostępu just-in-time w aplikacjach zarządzanych platformy Azure.](request-just-in-time-access.md)

> [!NOTE]
> Aby korzystać z dostępu just-in-time, musisz mieć [licencję P2 usługi Azure Active Directory](../../active-directory/privileged-identity-management/subscription-requirements.md).

## <a name="enable-during-deployment"></a>Włączanie podczas wdrażania

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

1. Znajdź wpis w portalu marketplace dla aplikacji zarządzanej z włączoną funkcją JIT. Wybierz **pozycję Utwórz**.

1. Podczas podawania wartości dla nowej aplikacji zarządzanej, krok **konfiguracji JIT** umożliwia włączenie lub wyłączenie dostępu JIT dla aplikacji zarządzanej. Wybierz **pozycję Tak,** **aby włączyć dostęp JIT**. Ta opcja jest domyślnie wybierana dla zarządzanych aplikacji zdefiniowanych z włączonym JIT w portalu marketplace.

   ![Konfigurowanie dostępu](./media/approve-just-in-time-access/configure-jit-access.png)

   Dostęp JIT można włączyć tylko podczas wdrażania. Jeśli wybierzesz **opcję Nie,** wydawca uzyska stały dostęp do zarządzanej grupy zasobów. Nie można włączyć dostępu JIT później.

1. Aby zmienić domyślne ustawienia zatwierdzania, wybierz pozycję **Dostosuj konfigurację JIT**.

   ![Dostosowywanie dostępu](./media/approve-just-in-time-access/customize-jit-access.png)

   Domyślnie zarządzana aplikacja z włączonym JIT ma następujące ustawienia:

   * Tryb zatwierdzania – automatyczny
   * Maksymalny czas dostępu – 8 godzin
   * Osoby zatwierdzające – brak

   Gdy tryb zatwierdzania jest ustawiony na **automatyczny,** osoby zatwierdzające otrzymują powiadomienie dla każdego żądania, ale żądanie zostanie automatycznie zatwierdzone. Po ustawieniu **na ręczne,** osoby zatwierdzające otrzymują powiadomienie dla każdego żądania, a jeden z nich musi je zatwierdzić.

   Maksymalny czas trwania aktywacji określa maksymalny czas, przez jaki wydawca może zażądać dostępu do zarządzanej grupy zasobów.

   Lista osób zatwierdzających to użytkownicy usługi Azure Active Directory, którzy mogą zatwierdzać żądania dostępu JIT. Aby dodać osobę zatwierdzaącą, wybierz **pozycję Dodaj osobę zatwierdzaącą** i wyszukaj użytkownika.

   Po zaktualizowaniu ustawienia wybierz pozycję **Zapisz**.

## <a name="update-after-deployment"></a>Aktualizacja po wdrożeniu

Można zmienić wartości sposobu zatwierdzenia żądań. Jeśli jednak nie włączysz dostępu JIT podczas wdrażania, nie można go włączyć później.

Aby zmienić ustawienia wdrożonej aplikacji zarządzanej:

1. W portalu wybierz aplikację do zarządzania.

1. Wybierz **konfigurację JIT** i w razie potrzeby zmień ustawienia.

   ![Zmienianie ustawień dostępu](./media/approve-just-in-time-access/change-settings.png)

1. Po zakończeniu wybierz pozycję **Zapisz**.

## <a name="approve-requests"></a>Zatwierdzanie żądań

Gdy wydawca zażąda dostępu, zostaniesz o tym powiadomiony o żądaniu. Można zatwierdzać żądania dostępu JIT bezpośrednio za pośrednictwem aplikacji zarządzanej lub we wszystkich aplikacjach zarządzanych za pośrednictwem usługi Azure AD Privileged Identity Management. Aby korzystać z dostępu just-in-time, musisz mieć [licencję P2 usługi Azure Active Directory](../../active-directory/privileged-identity-management/subscription-requirements.md).

Aby zatwierdzić żądania za pośrednictwem aplikacji zarządzanej:

1. Wybierz **pozycję JIT Access** dla aplikacji zarządzanej i wybierz pozycję **Zatwierdź żądania**.

   ![Zatwierdzanie żądań](./media/approve-just-in-time-access/approve-requests.png)
 
1. Wybierz żądanie do zatwierdzenia.

   ![Wybierz żądanie](./media/approve-just-in-time-access/select-request.png)

1. W formularzu podaj przyczynę zatwierdzenia i wybierz opcję **Zatwierdź**.

Aby zatwierdzać żądania za pośrednictwem usługi Azure AD Privileged Identity Management:

1. Wybierz **pozycję Wszystkie usługi** i rozpocznij wyszukiwanie w **usłudze Azure AD Privileged Identity Management**. Wybierz go z dostępnych opcji.

   ![Szukaj usługi](./media/approve-just-in-time-access/search.png)

1. Wybierz **pozycję Zatwierdź żądania**.

   ![Wybierz żądania zatwierdzenia](./media/approve-just-in-time-access/select-approve-requests.png)

1. Wybierz **aplikacje zarządzane platformy Azure**i wybierz żądanie do zatwierdzenia.

   ![Wybieranie żądań](./media/approve-just-in-time-access/view-requests.png)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o publikowaniu aplikacji zarządzanej z dostępem JIT, zobacz [Żądanie dostępu just-in-time w aplikacjach zarządzanych platformy Azure.](request-just-in-time-access.md)
