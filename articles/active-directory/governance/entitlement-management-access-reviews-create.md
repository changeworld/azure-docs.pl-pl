---
title: Tworzenie przeglądu dostępu pakietu dostępu w zarządzaniu uprawnieniami usługi Azure AD
description: Dowiedz się, jak utworzyć zasady przeglądu dostępu dla pakietów dostępu do zarządzania uprawnieniami w przeglądach dostępu usługi Azure Active Directory (Wersja zapoznawcza).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 11/01/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: a862bbb1f574e4adab2f7d8e59a1abe8e5a5fa2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73608841"
---
# <a name="create-an-access-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Tworzenie przeglądu dostępu pakietu dostępu w zarządzaniu uprawnieniami usługi Azure AD

Aby zmniejszyć ryzyko nieaktualnego dostępu, należy włączyć okresowe przeglądy użytkowników, którzy mają aktywne przypisania do pakietu dostępu w zarządzaniu uprawnieniami usługi Azure AD. Przeglądy można włączyć podczas tworzenia nowego pakietu dostępu lub edytowania istniejącego pakietu dostępu. W tym artykule opisano, jak włączyć przeglądy dostępu pakietów dostępu.

## <a name="prerequisites"></a>Wymagania wstępne

Aby włączyć przeglądy pakietów dostępu, należy spełnić wymagania wstępne dotyczące tworzenia pakietu dostępu:
- Usługa Azure AD — warstwa Premium P2
- Administrator globalny, administrator użytkownika, właściciel katalogu lub menedżer pakietów programu Access

Aby uzyskać więcej informacji, zobacz [Wymagania licencyjne](entitlement-management-overview.md#license-requirements).


## <a name="create-an-access-review-of-an-access-package"></a>Tworzenie przeglądu dostępu pakietu dostępu

Przeglądy dostępu można włączyć podczas [tworzenia nowego pakietu dostępu](entitlement-management-access-package-create.md) lub edytowania istniejących zasad pakietu [dostępu.](entitlement-management-access-package-lifecycle-policy.md) Wykonaj następujące kroki, aby włączyć przeglądy dostępu do pakietu dostępu:

1. Otwórz kartę **Cykl życia** dla pakietu dostępu i przewiń w dół do programu **Access Reviews**.

1. Przesuń przełącznik **Wymagaj recenzji dostępu** do **Tak**.

    ![Dodawanie recenzji dostępu](./media/entitlement-management-access-reviews/access-reviews-pane.png)

1. Określ datę rozpoczęcia przeglądów obok **pozycji Uruchamianie na**.

1. Następnie ustaw **częstotliwość przeglądu** na **roczną,** **biroczną,** **kwartalną** lub **miesięczną.**
To ustawienie określa, jak często będą miały miejsce przeglądy dostępu.

1. Ustaw **czas trwania,** aby określić, ile dni każdy przegląd serii cyklicznej będzie otwarty dla danych wejściowych od recenzentów. Na przykład można zaplanować roczny przegląd, który rozpoczyna się 1 stycznia i jest otwarty do przeglądu przez 30 dni, aby recenzenci mieli czas do końca miesiąca na udzielenie odpowiedzi.

1. Obok **pozycji Recenzenci**wybierz pozycję **Self-review,** jeśli chcesz, aby użytkownicy mogli przeprowadzić własną recenzję dostępu, lub wybierz **pozycję Określony recenzent,** jeśli chcesz wyznaczyć recenzenta.

    ![Wybierz pozycję Dodaj recenzentów](./media/entitlement-management-access-reviews/access-reviews-add-reviewer.png)

1. Jeśli wybrano **określonego recenzenta,** określ, którzy użytkownicy wykonają przegląd dostępu:
    1. Wybierz **pozycję Dodaj recenzentów**.
    1. W okienku **Wybierz recenzenci** wyszukaj i wybierz użytkowników, których chcesz być recenzentem.
    1. Po wybraniu recenzentów kliknij przycisk **Wybierz.**

    ![Określ recenzentów](./media/entitlement-management-access-reviews/access-reviews-select-reviewer.png)

1. Kliknij **pozycję Recenzja + Utwórz,** jeśli tworzysz nowy pakiet dostępu lub **Aktualizuj,** jeśli edytujesz pakiet dostępu, u dołu strony.

## <a name="view-the-status-of-the-access-review"></a>Wyświetlanie stanu przeglądu dostępu

Po dacie rozpoczęcia przegląd dostępu zostanie wyświetlony w sekcji **Przeglądy programu Access.** Wykonaj następujące kroki, aby wyświetlić stan przeglądu dostępu:

1. W **obszarze Zarządzanie tożsamościami**kliknij pozycję **Pakiety programu Access,** a następnie wybierz pakiet dostępu ze stanem przeglądu dostępu, który chcesz sprawdzić.   

1. Gdy już znajdziesz się w przeglądzie pakietu dostępu, kliknij pozycję **Przeglądy programu Access** w menu po lewej stronie.
    
    ![Wybieranie recenzji dostępu](./media/entitlement-management-access-reviews/access-review-status-access-package-overview.png)

1. Zostanie wyświetlona lista zawierająca wszystkie zasady, które mają skojarzone z nimi przeglądy dostępu. Kliknij recenzję, aby zobaczyć jej raport.

    ![Lista opinii o dostępie](./media/entitlement-management-access-reviews/access-review-status-select-access-reviews.png)
   
1. Podczas wyświetlania raportu pokazuje liczbę użytkowników przeglądanych i działania podjęte przez recenzenta na nich.

    ![Wyświetl stan recenzji](./media/entitlement-management-access-reviews/access-review-status.png)
 

## <a name="access-reviews-email-notifications"></a>Dostęp do recenzji powiadomień e-mail
Możesz wyznaczyć recenzentów lub użytkownicy mogą sami przeglądać ich dostęp. Domyślnie usługa Azure AD wyśle wiadomość e-mail do recenzentów lub recenzentów wkrótce po rozpoczęciu przeglądu.

Wiadomość e-mail będzie zawierać instrukcje dotyczące sposobu przeglądania dostępu do pakietów dostępu. Jeśli przegląd jest dla użytkowników, aby przejrzeć ich dostęp, pokaż im instrukcje dotyczące wykonywania samodzielnego przeglądu swoich pakietów dostępu.
  
Jeśli użytkownicy-goście zostali przypisani jako recenzenci i nie zaakceptowali zaproszenia gościa usługi Azure AD, nie otrzymają oni wiadomości e-mail z recenzji dostępu usługi Azure AD. Muszą najpierw zaakceptować zaproszenie i utworzyć konto w usłudze Azure AD, zanim będą mogli odbierać wiadomości e-mail. 

## <a name="next-steps"></a>Następne kroki

- [Przegląd dostępu do pakietów dostępu](entitlement-management-access-reviews-review-access.md)
- [Własny przegląd pakietów dostępu](entitlement-management-access-reviews-self-review.md)
