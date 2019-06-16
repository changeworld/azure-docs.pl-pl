---
title: Wiadomości e-mail z powiadomieniami w usłudze PIM — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: W tym artykule opisano powiadomienia e-mail w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 01/05/2019
ms.author: rolyon
ms.reviewer: hanki
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9aa9da83a7a6e97f5b721dad550831fe2645ffd3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60289189"
---
# <a name="email-notifications-in-pim"></a>Powiadomienia e-mail w usłudze PIM

Usługa Azure Active Directory (Azure AD) Privileged Identity Management (PIM) informuje o tym, kiedy ważne wydarzenia, takiego jak po przypisaniu lub aktywacji roli. Usługa PIM zapewnia wspieranemu przez wysyłanie powiadomień e-mail razem z innymi uczestnikami. Te wiadomości e-mail może również zawierać łącza do odpowiednich zadań, takich aktywowanie lub odnawianie ról. W tym artykule opisano wyglądać tych wiadomości e-mail, gdy są wysyłane i kto otrzymuje.

## <a name="sender-email-address-and-subject-line"></a>Wiersz adresu i temat wiadomości e-mail nadawcy

Wysłane wiadomości e-mail z usługi PIM dla usługi Azure AD i role zasobów platformy Azure ma następujący adres e-mail nadawcy:

- Adres e-mail: **azure noreply\@microsoft.com**
- Nazwa wyświetlana: Microsoft Azure

Te wiadomości e-mail zawierają **PIM** prefiks w wierszu tematu. Oto przykład:

- PIM: Alain Charon trwale została przypisana rola Czytelnik kopii zapasowej

## <a name="pim-emails-for-azure-ad-roles"></a>Wiadomości e-mail z usługi PIM dla ról usługi Azure AD

Usługa PIM wysyła wiadomości e-mail, gdy wystąpią następujące zdarzenia dla ról usługi Azure AD:

- Po aktywacji ról uprzywilejowanych oczekuje na zatwierdzenie
- Po wykonaniu żądania aktywacji roli uprzywilejowanej
- Po włączeniu usługi Azure AD PIM

Kto otrzymuje te wiadomości e-mail, usługi ról usługi Azure AD zależy od Twojej roli, zdarzenia i powiadomienia ustawienie:

| Użytkownik | Aktywacja roli oczekuje na zatwierdzenie | Żądanie aktywacji roli zostało zakończone. | Włączono usługę PIM |
| --- | --- | --- | --- |
| Administrator ról uprzywilejowanych</br>(Aktywowany/uprawnione) | Yes</br>(tylko wtedy, gdy określono nie jawnego osób zatwierdzających) | Tak* | Tak |
| Administrator zabezpieczeń</br>(Aktywowany/uprawnione) | Nie | Tak* | Tak |
| Administrator globalny</br>(Aktywowany/uprawnione) | Nie | Tak* | Tak |

\* Jeśli [ **powiadomienia** ustawienie](pim-how-to-change-default-settings.md#notifications) ustawiono **Włącz**.

Poniżej przedstawiono przykładową wiadomością e-mail jest wysyłana, gdy użytkownik aktywuje roli usługi Azure AD dla fikcyjnej firmy Contoso.

![Outlook.com PIM dla ról usługi Azure AD](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-pim-digest-email-for-azure-ad-roles"></a>Cotygodniowe wiadomości e-mail szyfrowanego PIM dla ról usługi Azure AD

Co tydzień e-mail podsumowania usługi PIM dla ról usługi Azure AD są wysyłane do administratorami ról uprzywilejowanych, Administratorzy zabezpieczeń i administratorów globalnych, które mają włączone usługi PIM. Ten cotygodniowe wiadomości e-mail zawiera migawkę działania PIM dla tygodnia, a także przypisań ról uprzywilejowanych. Jest on dostępny tylko dla dzierżaw w chmurze publicznej. Poniżej przedstawiono przykładową wiadomość e-mail:

![Cotygodniowe wiadomości e-mail szyfrowanego PIM dla ról usługi Azure AD](./media/pim-email-notifications/email-directory-weekly.png)

Wiadomość e-mail zawiera cztery Kafelki:

| Kafelek | Opis |
| --- | --- |
| **Użytkownicy aktywowany** | Liczba użytkowników aktywacji ich kwalifikowania się do roli w ramach dzierżawy. |
| **Trwałe użytkowników** | Liczba użytkowników z kwalifikującego się przypisania jest trwałe. |
| **Przypisania ról w usłudze PIM** | Liczba przypadków, gdy użytkownicy są przypisywani kwalifikowania się do roli w usłudze PIM. |
| **Przypisania ról poza usługą PIM** | Liczba przypadków, gdy użytkownicy są przypisywani trwałego roli poza usługą PIM (wewnątrz usługi Azure AD). |

**Przegląd najważniejszych role** sekcja zawiera informacje o pierwszych pięciu ról w ramach dzierżawy na podstawie całkowitej liczby administratorów trwałe i kwalifikujące się dla każdej roli. **Reakcję** link zostanie otwarty [kreatora PIM](pim-security-wizard.md) gdzie możesz przekształcić stałych administratorów uprawnionych administratorów w partiach.

## <a name="pim-emails-for-azure-resource-roles"></a>Wiadomości e-mail z usługi PIM dla ról zasobów platformy Azure

PIM wysyła wiadomości e-mail do właściciele i Administratorzy dostępu użytkownika, gdy wystąpią następujące zdarzenia dla ról zasobów platformy Azure:

- Gdy przypisanie roli oczekuje na zatwierdzenie
- Po przypisaniu roli
- Gdy rola to wkrótce wygaśnie
- Gdy rola jest uprawniona do rozszerzenia
- Gdy rola zostanie odnowiony przez użytkownika końcowego
- Po wykonaniu żądania uaktywnienia roli

PIM wysyła wiadomości e-mail do użytkowników końcowych, gdy wystąpią następujące zdarzenia dla ról zasobów platformy Azure:

- Gdy rola jest przypisywana do użytkownika
- Gdy rola użytkownika wygasła
- Gdy rola użytkownika jest rozszerzony
- Po wykonaniu żądania uaktywnienia roli użytkownika

Poniżej przedstawiono przykładową wiadomością e-mail jest wysyłana, gdy użytkownik przypisano rolę zasobu platformy Azure dla fikcyjnej firmy Contoso.

![Outlook.com PIM dla ról zasobów platformy Azure](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>Kolejne kroki

- [Konfigurowanie ustawień roli w usłudze Azure AD w usłudze PIM](pim-how-to-change-default-settings.md)
- [Zatwierdź lub Odrzuć żądania dla ról usługi Azure AD w usłudze PIM](azure-ad-pim-approval-workflow.md)
