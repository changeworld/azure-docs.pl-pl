---
title: Powiadomienia e-mail w programie PIM — Azure Active Directory | Microsoft Docs
description: Opisuje powiadomienia e-mail w Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 01/05/2019
ms.author: curtand
ms.reviewer: hanki
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0db4828ef7a6d3392ce53b2f2f95b0f7f014d65
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804545"
---
# <a name="email-notifications-in-pim"></a>Powiadomienia e-mail w usłudze PIM

Usługa Azure Active Directory (Azure AD) Privileged Identity Management (PIM) umożliwia informowanie o wystąpieniu ważnych zdarzeń, na przykład w przypadku przypisywania lub aktywowania roli. Program PIM pozwala uzyskać informacje o wysyłaniu powiadomień e-mail i innych uczestników. Te wiadomości e-mail mogą również zawierać linki do odpowiednich zadań, takich jak aktywowanie lub odnawianie roli. W tym artykule opisano, jak wyglądają te wiadomości e-mail, gdy są one wysyłane i kto je otrzymuje.

## <a name="sender-email-address-and-subject-line"></a>Adres e-mail nadawcy i wiersz tematu

Wiadomości e-mail wysyłane z usługi PIM dla ról usług Azure AD i Azure Resource mają następujący adres e-mail nadawcy:

- Adres e-mail: **Azure-noreply\@Microsoft.com**
- Nazwa wyświetlana: Microsoft Azure

Te wiadomości e-mail zawierają prefiks **PIM** w wierszu tematu. Oto przykład:

- PIM: Alain Charon ma trwale przypisaną rolę czytnika kopii zapasowych

## <a name="pim-emails-for-azure-ad-roles"></a>Wiadomości e-mail PIM dla ról usługi Azure AD

PIM wysyła wiadomości e-mail w przypadku wystąpienia następujących zdarzeń dla ról usługi Azure AD:

- Po oczekiwaniu na zatwierdzenie przez uprzywilejowaną rolę
- Po ukończeniu żądania aktywacji roli uprzywilejowanej
- Gdy usługa Azure AD PIM jest włączona

Kto odbiera te wiadomości e-mail dla ról usługi Azure AD zależy od roli, zdarzenia i ustawienia powiadomienia:

| Użytkownik | Aktywacja roli oczekuje na zatwierdzenie | Żądanie aktywacji roli zostało zakończone | Jest włączony PIM |
| --- | --- | --- | --- |
| Administrator uprawnionej roli</br>(Aktywowane/kwalifikujące się) | Tak</br>(tylko jeśli nie określono jawnych osób zatwierdzających) | Tak* | Tak |
| Administrator zabezpieczeń</br>(Aktywowane/kwalifikujące się) | Nie | Tak* | Tak |
| Administrator globalny</br>(Aktywowane/kwalifikujące się) | Nie | Tak* | Tak |

\*Jeśli ustawienie [ **powiadomienia** ](pim-how-to-change-default-settings.md#notifications) jest ustawione na wartość **Włącz**.

Poniżej przedstawiono przykładową wiadomość e-mail, która jest wysyłana, gdy użytkownik aktywuje rolę usługi Azure AD dla fikcyjnej organizacji contoso.

![Nowa poczta e-mail PIM dla ról usługi Azure AD](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-pim-digest-email-for-azure-ad-roles"></a>Cotygodniowa poczta e-mail programu PIM Digest dla ról usługi Azure AD

Cotygodniowe podsumowanie wiadomości e-mail w usłudze PIM dla ról usługi Azure AD jest wysyłane do administratorów ról uprzywilejowanych, administratorów zabezpieczeń i administratorów globalnych, którzy włączyli usługę PIM. Ten tygodniowy adres e-mail zawiera migawkę działań PIM dla danego tygodnia oraz przypisań ról uprzywilejowanych. Jest on dostępny tylko dla dzierżawców w chmurze publicznej. Oto przykład wiadomości e-mail:

![Cotygodniowa poczta e-mail programu PIM Digest dla ról usługi Azure AD](./media/pim-email-notifications/email-directory-weekly.png)

Wiadomość e-mail zawiera cztery kafelki:

| Sąsiadująco | Opis |
| --- | --- |
| **Aktywowani użytkownicy** | Liczba przypadków, w których użytkownicy aktywowali rolę kwalifikującą się do dzierżawy. |
| **Użytkownicy z stałymi** | Liczba przypadków, w których użytkownicy z uprawnionym przypisaniem są trwałe. |
| **Przypisania ról w usłudze PIM** | Liczba użytkowników, którym przypisano kwalifikującą się rolę w programie PIM. |
| **Przypisania ról poza usługą PIM** | Liczba użytkowników, którym przypisano stałą rolę poza usługą PIM (w ramach usługi Azure AD). |

Sekcja **najpopularniejszych ról** zawiera pięć najważniejszych ról w dzierżawie w oparciu o łączną liczbę stałych i kwalifikujących się administratorów dla każdej roli. Link **podejmij działanie** powoduje otwarcie [Kreatora PIM](pim-security-wizard.md) , w którym można skonwertować stałych administratorów do uprawnionych administratorów w partiach.

## <a name="pim-emails-for-azure-resource-roles"></a>Wiadomości e-mail PIM dla ról zasobów platformy Azure

Program PIM wysyła wiadomości e-mail do właścicieli i administratorów dostępu użytkowników, gdy wystąpią następujące zdarzenia dla ról zasobów platformy Azure:

- Gdy przypisanie roli oczekuje na zatwierdzenie
- Po przypisaniu roli
- Gdy rola wkrótce wygaśnie
- Gdy rola może zostać rozszerzona
- Gdy rola jest odnawiana przez użytkownika końcowego
- Po ukończeniu żądania aktywacji roli

Usługa PIM wysyła wiadomości e-mail do użytkowników końcowych w przypadku wystąpienia następujących zdarzeń dotyczących ról zasobów platformy Azure:

- Gdy rola jest przypisana do użytkownika
- Po wygaśnięciu roli użytkownika
- Gdy rola użytkownika jest rozszerzona
- Po ukończeniu żądania aktywacji roli użytkownika

Poniżej przedstawiono przykładową wiadomość e-mail, która jest wysyłana, gdy użytkownik ma przypisaną rolę zasobów platformy Azure dla fikcyjnej organizacji contoso.

![Nowa wiadomość e-mail PIM dla ról zasobów platformy Azure](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie ustawień roli usługi Azure AD w usłudze PIM](pim-how-to-change-default-settings.md)
- [Zatwierdzanie lub odrzucanie żądań dla ról usługi Azure AD w usłudze PIM](azure-ad-pim-approval-workflow.md)
