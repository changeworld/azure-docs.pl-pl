---
title: Konta służbowe i Centrum partnerów
description: Jak sprawdzić, czy firma ma konto służbowe skonfigurowane w firmie Microsoft, utworzyć nowe konto służbowe lub skonfigurować wiele kont służbowych do użycia w Centrum partnerów.
author: dsindona
ms.author: parthp
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: c4e7427d87c5f88d8c686b867ef88ceb05f28286
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281429"
---
# <a name="company-work-accounts-and-partner-center"></a>Konta służbowe i Centrum partnerów

Centrum partnerów używa kont służbowych firmy, znanych również jako dzierżawy usługi Azure Active Directory (AD), do zarządzania dostępem do kont dla wielu użytkowników, kontroli uprawnień, grup hostów i aplikacji oraz obsługi danych profilu. Łącząc firmową domenę służbowego konta e-mail z kontem Centrum partnerów, pracownicy firmy mogą zalogować się do Centrum partnerskiego, aby zarządzać ofertami w portalu marketplace przy użyciu własnych nazw użytkowników konta służbowego i haseł.

## <a name="check-whether-your-company-already-has-a-work-account"></a>Sprawdź, czy Twoja firma ma już konto służbowe

Jeśli Twoja firma zasubskrybowała usługę w chmurze firmy Microsoft, taką jak Azure, Microsoft Intune lub Office 365, masz już domenę służbowego konta e-mail (nazywaną również dzierżawą usługi Azure Active Directory), która może być używana z Centrum partnerów.

Wykonaj następujące kroki, aby sprawdzić:
1. Zaloguj się do portalu https://portal.azure.comadministracyjnego platformy Azure pod adresem .
2. Wybierz **pozycję Azure Active Directory** z menu nawigacji po lewej stronie, a następnie wybierz pozycję **Niestandardowe nazwy domen**.
3. Jeśli masz już konto służbowe, twoja nazwa domeny zostanie wyświetlona.

Jeśli Twoja firma nie ma jeszcze konta służbowego, zostanie utworzone podczas procesu rejestracji Centrum partnerów.

## <a name="set-up-multiple-work-accounts"></a>Konfigurowanie wielu kont służbowych

Przed podjęciem decyzji o użyciu istniejącego konta służbowego należy wziąć pod uwagę, ilu użytkowników na koncie służbowym będzie musiało uzyskać dostęp do Centrum partnerów. Jeśli masz na koncie służbowym użytkowników, którzy nie będą musieli uzyskiwać dostępu do Centrum partnerów, warto rozważyć utworzenie wielu kont służbowych, tak aby tylko ci użytkownicy, którzy będą musieli uzyskać dostęp do Centrum partnerów, są reprezentowani na określonym koncie.

## <a name="create-a-new-work-account"></a>Tworzenie nowego konta służbowego

Aby utworzyć nowe konto służbowe dla swojej firmy, wykonaj poniższe czynności. Może być konieczne żądanie pomocy od osoby, która ma uprawnienia administracyjne na koncie microsoft azure firmy.

1. Zaloguj się do [Portalu Microsoft Azure](https://portal.azure.com).
2. Z lewego menu nawigacyjnego wybierz pozycję**Użytkownicy** **usługi Azure Active Directory** -> .
3. Wybierz **pozycję Nowy użytkownik** i utwórz nowe konto służbowe platformy Azure, wprowadzając nazwę i adres e-mail. Upewnij się, że **rola katalog jest ustawiona** na **Użytkownik** i zaznacz pole wyboru **Pokaż hasło** u dołu, aby wyświetlić i zanotować hasło z automatycznymwygenerowanym.
4. Wybierz **pozycję Utwórz,** aby zapisać nowego użytkownika.

Adres e-mail konta użytkownika musi być zweryfikowaną nazwą domeny w katalogu. Możesz wyświetlić listę wszystkich zweryfikowanych domen w katalogu, wybierając**niestandardowe nazwy domen** usługi Azure Active **Directory** -> w menu nawigacji po lewej stronie.

Aby dowiedzieć się więcej o dodawaniu domen niestandardowych w usłudze Azure Active Directory, zobacz [Dodawanie lub kojarzenie domeny w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain).

## <a name="troubleshoot-work-email-sign-in"></a>Rozwiązywanie problemów z umowy logowania się za służbową pocztą e-mail

Jeśli masz problemy z zalogowaniem się do konta służbowego (znanego również jako dzierżawa usługi Azure AD), znajdź scenariusz na poniższym diagramie, który najlepiej pasuje do Twojej sytuacji i wykonaj zalecane kroki.

![Diagram rozwiązywania problemów z logowaniem do konta służbowego](./media/onboarding-aad-flow.png)

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie kontem w portalu Marketplace w Centrum partnerskim](./manage-account.md) 
