---
title: 'Rozwiązywanie problemów: Element usługi Active Directory jest lub jest ona niedostępna | Dokumentacja firmy Microsoft'
description: Co należy zrobić, gdy element menu usługi Active Directory nie jest wyświetlane w portalu zarządzania Azure.
services: active-directory
documentationcenter: na
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
editor: ''
ms.assetid: 3383020d-6397-43ea-b7aa-c6a9d6a1e3df
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.openlocfilehash: 8996eaf1d780f957445c63eac96cf9a343b9ef90
ms.sourcegitcommit: c306be629977f6bc22bb7aefd0522a30ed591ff3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34181541"
---
# <a name="troubleshooting-active-directory-item-is-missing-or-not-available"></a>Rozwiązywanie problemów: Element usługi Active Directory jest lub jest niedostępna
Wiele instrukcje dotyczące korzystania z funkcji usługi Azure Active Directory i usług rozpoczynać się od "Przejdź do portalu zarządzania Azure i kliknij polecenie **usługi Active Directory**." Ale co zrobić, jeśli nie ma elementu menu lub rozszerzenie usługi Active Directory lub jest on oznaczony **nie jest dostępny**? W tym temacie jest przeznaczona do pomocy. Opisuje warunki, w którym **usługi Active Directory** nie pojawia się lub jest niedostępna i wyjaśniono sposób kontynuować.

## <a name="active-directory-is-missing"></a>Brak usługi Active Directory
Zazwyczaj **usługi Active Directory** element będzie wyświetlany w menu nawigacji po lewej stronie. Z instrukcjami w usłudze Azure Active Directory procedury założono, że ten element jest w danym widoku.

![Zrzut ekranu: usługi Active Directory na platformie Azure](./media/active-directory-troubleshooting/typical-view.png)

Element usługi Active Directory jest wyświetlany w menu nawigacji po lewej stronie, gdy spełniony jest dowolny z następujących warunków. W przeciwnym razie nie ma elementu.

* Bieżący użytkownik zalogować się przy użyciu konta Microsoft (znanego wcześniej jako identyfikator Windows Live ID).
  
    LUB
* Dzierżawy Azure ma katalog, a bieżące konto jest kontem administratora katalogu.
  
    LUB
* Dzierżawy Azure ma co najmniej jedną przestrzeń nazw kontroli dostępu usługi Azure AD (ACS). Aby uzyskać więcej informacji, zobacz [Namespace kontroli dostępu](https://msdn.microsoft.com/library/azure/gg185908.aspx).
  
    LUB
* Dzierżawy Azure ma co najmniej jednego dostawcę usługi Azure Multi-Factor Authentication. Aby uzyskać więcej informacji, zobacz [administrowanie dostawców uwierzytelniania wieloskładnikowego Azure](authentication/howto-mfa-getstarted.md).

Aby utworzyć przestrzeń nazw kontroli dostępu lub dostawcy uwierzytelniania wieloskładnikowego, kliknij przycisk **+ nowy** > **usługi aplikacji** > **usługi Active Directory**.

Aby uzyskać uprawnienia administracyjne do katalogu, poproś administratora o Przypisz rolę administratora do swojego konta. Aby uzyskać więcej informacji, zobacz [przypisywanie ról administratorów](active-directory-assign-admin-roles-azure-portal.md).

## <a name="active-directory-is-not-available"></a>Active Directory nie jest dostępna
Po kliknięciu **+ nowy** > **usługi aplikacji**, **usługi Active Directory** element będzie wyświetlany. W szczególności elementu usługi Active Directory jest wyświetlany, gdy funkcji usługi Active Directory, takich jak katalog, kontroli dostępu lub Dostawca uwierzytelniania MFA, są dostępne dla bieżącego użytkownika.

Jednak podczas ładowania strony element jest niedostępna i jest oznaczony jako **nie jest dostępny**. Jest to stan przejściowy. Jeśli Poczekaj kilka sekund, element staje się dostępna. Jeśli opóźnienie zostanie przedłużony, często odświeżania strony sieci web nie zostanie rozwiązany.

![Zrzut ekranu: Active Directory nie jest dostępna](./media/active-directory-troubleshooting/not-available.png)

