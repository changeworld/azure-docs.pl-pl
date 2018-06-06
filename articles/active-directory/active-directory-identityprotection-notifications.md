---
title: Azure Active Directory Identity Protection powiadomienia | Dokumentacja firmy Microsoft
description: Dowiedz się, jak powiadomienia obsługują działaniach dochodzenia.
services: active-directory
keywords: ochronę tożsamości usługi Azure active directory, usługa cloud app discovery, zarządzanie aplikacjami, zabezpieczeń, ryzyka, poziom ryzyka, luki w zabezpieczeniach, zasady zabezpieczeń
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 65ca79b9-4da1-4d5b-bebd-eda776cc32c7
ms.service: active-directory
ms.component: protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: e72cfab8886f28982e5451362704d6a7a9fe59e4
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34713817"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Azure Active Directory Identity Protection powiadomienia

Azure AD Identity Protection wysyła dwa rodzaje automatycznych powiadomień e-mail w celu zarządzania ryzykiem użytkownika i zdarzenia ryzyka:

- Zagrożonych użytkowników wykryto poczty e-mail
- Wiadomość e-mail z podsumowaniem co tydzień

Ten artykuł zawiera omówienie zarówno wiadomości e-mail z powiadomieniem.


## <a name="users-at-risk-detected-email"></a>Zagrożonych użytkowników wykryto poczty e-mail

W odpowiedzi na koncie Wykryto zagrożone, Azure AD Identity Protection generuje alerty w wiadomościach e-mail z **zagrożonych wykryto użytkowników** jako podmiotu. Wiadomości e-mail zawiera łącze do **[użytkownicy oflagowani ryzyka](active-directory-reporting-security-user-at-risk.md)** raportu. Najlepszym rozwiązaniem powinien natychmiast Zbadaj zagrożonych użytkowników.

![Zagrożonych użytkowników wykryto poczty e-mail](./media/active-directory-identityprotection-notifications/01.png)


### <a name="configuration"></a>Konfigurowanie

Jako administrator można ustawić:

- **Poziom ryzyka, który wyzwala generowania tej wiadomości e-mail** — Domyślnie poziom ryzyka jest ustawiony na "High" ryzyka.
- **Adresaci wiadomości e-mail** — domyślnie adresatów zawierają wszystkich administratorów globalnych. Administratorzy globalni można również dodawać innych administratorów globalnych, administratorów zabezpieczeń czytelnicy zabezpieczeń jako adresatów.  


Aby otworzyć okno dialogowe powiązane, kliknij przycisk **alerty** w **ustawienia** sekcji **Identity Protection** strony.

![Zagrożonych użytkowników wykryto poczty e-mail](./media/active-directory-identityprotection-notifications/05.png)


## <a name="weekly-digest-email"></a>Wiadomość e-mail z podsumowaniem co tydzień

Co tydzień wiadomość e-mail z podsumowaniem zawiera podsumowanie nowych zdarzeń ryzyka.  
Obejmuje on:

- Narażeni użytkownicy

- Podejrzane działania

- Wykryto luk w zabezpieczeniach

- Linki do powiązanych raportów w Identity Protection

    ![Korygowanie](./media/active-directory-identityprotection-notifications/400.png "korygowania")

### <a name="configuration"></a>Konfigurowanie

Jako administrator możesz przełączyć wysyłania co tydzień wiadomość e-mail z podsumowaniem.

![Ryzyko użytkownika](./media/active-directory-identityprotection-notifications/62.png "zagrożeń użytkownika")

Aby otworzyć okno dialogowe powiązane, kliknij przycisk **cotygodniowe podsumowanie** w **ustawienia** sekcji **Identity Protection** strony.

![Zagrożonych użytkowników wykryto poczty e-mail](./media/active-directory-identityprotection-notifications/04.png)


## <a name="see-also"></a>Zobacz także

- [Ochronę tożsamości usługi Azure Active Directory](active-directory-identityprotection.md)
