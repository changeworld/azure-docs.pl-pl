---
title: Powiadomienia o raportach usługi Azure Active Directory
description: Jak używać usługi Azure Active Directory raportowania powiadomienia o podejrzanych znak dodatków.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: ae6d4b0e-5931-4cb3-98bf-9be91b381c92
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: dhanyahk;markvi
ms.custom: oldportal
ms.reviewer: dhanyahk
ms.openlocfilehash: c279b923ed4e63d810861c579dd54b3a2b7f415e
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="azure-active-directory-reporting-notifications"></a>Powiadomienia o raportach usługi Azure Active Directory
## <a name="what-reports-generate-email-notifications"></a>Jakie raporty generowanie powiadomień e-mail
W tej chwili nieregularne logowania wyzwalaczy raport aktywności wiadomości e-mail z powiadomieniami.

## <a name="what-is-an-irregular-sign-in"></a>Co to jest "nieregularne logowania"?
Nieregularne logowania są tymi, które zostały zidentyfikowane przez naszych algorytmów, na podstawie warunku "niemożliwa podróż", w połączeniu z nietypowych lokalizacji logowania i urządzenia uczenia maszynowego. Może to oznaczać, że haker próbuje zalogować się przy użyciu tego konta.

## <a name="who-receives-the-email-notifications"></a>Kto otrzymuje powiadomienia e-mail?
Wiadomości e-mail są wysyłane do wszystkich administratorów globalnych, którzy zostali przypisani licencji usługi Active Directory — wersja Premium. Aby upewnić się, że dostarczeniu, wyślemy go do administratorów alternatywny adres E-mail, jak również. Administratorzy powinna zawierać aad-alerts-noreply@mail.windowsazure.com na liście bezpiecznych, nie zostaną pominięte wiadomości e-mail.

## <a name="how-often-are-these-emails-sent"></a>Jak często są te wiadomości e-mail wysyłanych?
Wiadomości e-mail są wysyłane, jeśli 10 nowych nieregularne logowania działań występuje w ciągu ostatnich 30 dni lub od momentu ostatniego wiadomość e-mail została wysłana, ta wartość jest mniejsza.

## <a name="how-do-i-access-the-report-mentioned-in-the-email"></a>Jak uzyskać dostępu do raportów wymienionych w wiadomości e-mail
Po kliknięciu łącza nastąpi przekierowanie do strony raportu w portalu Azure. Aby uzyskać dostęp do raportu, musisz być:

* Administrator lub współadministratora subskrypcji platformy Azure
* Administrator globalny w katalogu i przypisaniu licencji usługi Active Directory — wersja Premium. Aby uzyskać więcej informacji, zobacz [Wersje usługi Azure Active Directory](active-directory-whatis.md).

## <a name="can-i-turn-off-these-emails"></a>Można wyłączyć te wiadomości e-mail?
Tak, aby wyłączyć powiadomienia dotyczące nietypowe logowania w portalu Azure, kliknij przycisk **Konfiguruj**, a następnie wybierz **wyłączone** w obszarze **powiadomienia** sekcji.

## <a name="whats-next"></a>Co dalej
* Zastanawiasz się, jak zabezpieczeń, inspekcji i raporty dotyczące działań są dostępne? Zapoznaj się z [zabezpieczeń platformy Azure AD, inspekcji i raporty dotyczące działań](active-directory-view-access-usage-reports.md)
* [Wprowadzenie do usługi Azure Active Directory — wersja Premium](active-directory-get-started-premium.md)
* [Dodawanie znakowania firmowego do stron logowania i panelu dostępu](customize-branding.md)

