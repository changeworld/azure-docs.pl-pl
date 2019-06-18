---
title: Konfigurowanie i zatwierdzić dostęp just in time dla usługi Azure Managed Applications
description: W tym artykule opisano, jak użytkownicy usługi Azure Managed Applications zatwierdzenie żądania dostępu just in time do aplikacji zarządzanej.
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 55fbf7292ab894cad3de3de9e96ddc96fe0b79b3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66481717"
---
# <a name="configure-and-approve-just-in-time-access-for-azure-managed-applications"></a>Konfigurowanie i zatwierdzić dostęp just in time dla usługi Azure Managed Applications

Jako użytkownik aplikacji zarządzanej, może okazać się komfortowo, jednocześnie zapewniając wydawcy stały dostęp do zarządzanej grupy zasobów. Daje większą kontrolę nad udzielanie dostępu do zasobów zarządzanych, usługi Azure Managed Applications udostępnia funkcję dostępu just in time (JIT), która jest obecnie dostępna w wersji zapoznawczej. Go umożliwia zatwierdzenia, kiedy i jak długo wydawca ma dostęp do grupy zasobów. Wydawca może spowodować wymaganych aktualizacji w tym samym czasie, ale po upływie tego czasu wygaśnięcia dostępu wydawcy.

Przepływ pracy do przyznawania dostępu jest następujący:

1. Wydawca dodaje aplikację zarządzaną w portalu marketplace i określa, że dostęp JIT jest dostępna.

1. Podczas wdrażania możesz włączyć dostęp JIT dla swojego wystąpienia usługi zarządzanej aplikacji.

1. Po wdrożeniu możesz zmienić ustawienia dostępu JIT.

1. Wydawca wysyła żądanie dostępu.

1. Należy zatwierdzić żądanie.

Ten artykuł koncentruje się na akcjach, które konsumentów podjąć w celu Włącz dostęp JIT i zatwierdzać żądania. Aby dowiedzieć się więcej o publikowaniu aplikacji zarządzanej dostęp JIT, zobacz [żądania dostępu just in time w usłudze Azure Managed Applications](request-just-in-time-access.md).

> [!NOTE]
> Aby korzystać z dostępu just in time, konieczne jest posiadanie [licencji usługi Azure Active Directory P2](../active-directory/privileged-identity-management/subscription-requirements.md).

## <a name="enable-during-deployment"></a>Włącz podczas wdrażania

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Znajdź wpis witryny marketplace dla aplikacji zarządzanej, używając JIT włączone. Wybierz pozycję **Utwórz**.

1. Przy jednoczesnym zapewnieniu wartości dla nowej aplikacji zarządzanej, **Konfiguracja JIT** krok pozwala włączyć lub wyłączyć dostęp JIT dla aplikacji zarządzanej. Wybierz **tak** dla **Włącz dostęp JIT**. Ta opcja jest domyślnie zaznaczone do zarządzanych aplikacji, które zdefiniowane przy użyciu JIT włączone w portalu marketplace.

   ![Konfigurowanie dostępu](./media/approve-just-in-time-access/configure-jit-access.png)

   Dostęp JIT można włączyć tylko podczas wdrażania. Jeśli wybierzesz **nie**, wydawca pobiera stały dostęp do zarządzanej grupy zasobów. Nie można włączyć dostępu JIT do później.

1. Aby zmienić domyślne ustawienia zatwierdzania, wybierz **dostosować Konfiguracja JIT**.

   ![Dostosowywanie dostępu](./media/approve-just-in-time-access/customize-jit-access.png)

   Domyślnie zarządzanych aplikacji z włączoną JIT ma następujące ustawienia:

   * Tryb zatwierdzania — automatyczne
   * Czas trwania maksymalny dostęp – 8 godzin
   * Osoby zatwierdzające — Brak

   Jeśli tryb zatwierdzania jest równa **automatyczne**, osób zatwierdzających otrzymywać powiadomienia dla każdego żądania, ale żądanie zostanie automatycznie zatwierdzone. Po ustawieniu **ręczne**osób zatwierdzających otrzymywać powiadomienia dla każdego żądania i jeden z nich musi zatwierdzić ją.

   Maksymalny czas trwania aktywacji określa maksymalną ilość czasu, którego wydawcy mogą żądać dostępu do zarządzanej grupy zasobów.

   Lista osób zatwierdzających jest użytkowników usługi Azure Active Directory, którzy mogą zatwierdzać żądania dostępu JIT. Aby dodać osobę zatwierdzającą, wybierz **Dodawanie osoby zatwierdzającej** i Wyszukaj użytkownika.

   Po zaktualizowaniu ustawienie, wybierz **Zapisz**.

## <a name="update-after-deployment"></a>Aktualizuj po wdrożeniu

Można zmienić wartości jak żądania są zatwierdzane. Jednak jeśli dostęp JIT nie zostały włączone podczas wdrażania, nie można włączyć je później.

Aby zmienić ustawienia dla wdrożonej aplikacji zarządzanych:

1. W portalu wybierz aplikację, zarządzanie.

1. Wybierz **Konfiguracja JIT** i zmień ustawienia zgodnie z potrzebami.

   ![Zmień ustawienia dostępu](./media/approve-just-in-time-access/change-settings.png)

1. Po zakończeniu wybierz pozycję **Zapisz**.

## <a name="approve-requests"></a>Zatwierdzanie żądań

Gdy wydawca żąda dostępu, są powiadamiani o wniosku. Możesz zatwierdzać żądania dostępu JIT bezpośrednio za pomocą aplikacji zarządzanej lub we wszystkich aplikacjach zarządzanych przez usługę Azure AD Privileged Identity Management. Aby korzystać z dostępu just in time, konieczne jest posiadanie [licencji usługi Azure Active Directory P2](../active-directory/privileged-identity-management/subscription-requirements.md).

Aby zatwierdzić żądań za pośrednictwem zarządzanej aplikacji:

1. Wybierz **dostępu JIT do** dla aplikacji zarządzanej, a następnie wybierz **zatwierdzanie żądań**.

   ![Zatwierdzanie żądań](./media/approve-just-in-time-access/approve-requests.png)
 
1. Zaznacz żądania do zatwierdzenia.

   ![Wybierz żądanie](./media/approve-just-in-time-access/select-request.png)

1. W formularzu, podaj przyczynę zatwierdzenia i wybierz pozycję **Zatwierdź**.

Aby zatwierdzić żądań za pośrednictwem usługi Azure AD Privileged Identity Management:

1. Wybierz **wszystkich usług** i rozpocząć wyszukiwanie **usługi Azure AD Privileged Identity Management**. Wybierz go z listy dostępnych opcji.

   ![Wyszukiwanie usługi](./media/approve-just-in-time-access/search.png)

1. Wybierz **zatwierdzanie żądań**.

   ![Wybieranie pozycji Zatwierdź żądania](./media/approve-just-in-time-access/select-approve-requests.png)

1. Wybierz **usługi Azure managed applications**i zaznacz żądania do zatwierdzenia.

   ![Zaznacz żądania](./media/approve-just-in-time-access/view-requests.png)

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o publikowaniu aplikacji zarządzanej dostęp JIT, zobacz [żądania dostępu just in time w usłudze Azure Managed Applications](request-just-in-time-access.md).
