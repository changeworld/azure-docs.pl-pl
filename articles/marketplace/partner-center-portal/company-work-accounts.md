---
title: Kontami służbowymi firmy i Centrum partnerskiego
description: Jak sprawdzić, czy Twoja firma ma konto służbowe, skonfiguruj z firmą Microsoft, Utwórz nowe konto służbowe lub skonfigurować wiele kont służbowych za pomocą Centrum partnerskiego.
author: mattwojo
manager: evansma
ms.author: parthp
ms.service: marketplace
ms.topic: how-to
ms.date: 05/30/2019
ms.openlocfilehash: df8ab370e8874e07f8985ecfb3a772848a2e2b21
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65806284"
---
# <a name="company-work-accounts-and-partner-center"></a>Kontami służbowymi firmy i Centrum partnerskiego

Centrum partnerskie firmy kont służbowych, znany także jako dzierżaw usługi Azure Active Directory (AD), zarządzanie dostępem do konta, dla wielu użytkowników, kontrolować uprawnienia, grupy hostów i aplikacje i używa zachowania danych profilu. Tworząc konto w Centrum partnerskim domeny konta e-mail pracę w firmie, pracownikami firmy zalogować się do Centrum partnerskiego do zarządzania przy użyciu nazwy pracy konta użytkowników i haseł oferty w witrynie marketplace.

## <a name="check-whether-your-company-already-has-a-work-account"></a>Sprawdź, czy Twoja firma dysponuje już konto służbowe

Jeśli Twoja firma subskrybuje usługi w chmurze firmy Microsoft, takich jak Azure, Microsoft Intune i Office 365, następnie masz już pracy wiadomości e-mail konta domeny (nazywane również dzierżawy usługi Azure Active Directory), które mogą być używane z Centrum partnerskiego.

Wykonaj następujące kroki, aby sprawdzić:
1. Zaloguj się do portalu administracyjnego platformy Azure w https://portal.azure.com.
2. Wybierz **usługi Azure Active Directory** z menu nawigacji po lewej stronie, a następnie wybierz pozycję **niestandardowe nazwy domen**.
3. Jeśli masz już konto służbowe, zostaną wyświetlone nazwy domeny.

Jeśli Twoja firma nie ma jeszcze konta służbowego, zostanie utworzony automatycznie podczas procesu rejestracji Centrum partnerskiego.

## <a name="set-up-multiple-work-accounts"></a>Konfigurowanie wielu kont służbowych

Przed podjęciem decyzji użyć istniejącego konta służbowego, należy wziąć pod uwagę liczbę użytkowników, konta służbowego, musisz dostęp do Centrum partnerskiego. Jeśli istnieją użytkownicy, konto służbowe, które nie mają dostęp do Centrum partnerskiego, warto rozważyć utworzenie większej liczby kont służbowych, tak, że tylko użytkownicy, którzy mają dostęp do Centrum partnerskiego są reprezentowane na określone konto.

## <a name="create-a-new-work-account"></a>Utwórz nowe konto służbowe

Aby utworzyć nowe konto służbowe dla Twojej firmy, wykonaj następujące czynności. Może być konieczne zażądać pomocy od kto ma uprawnienia administracyjne do firmy Microsoft Azure.

1. Zaloguj się do [Portalu Microsoft Azure](https://portal.azure.com).
2. W menu nawigacji po lewej stronie wybierz **usługi Azure Active Directory** -> **użytkowników**.
3. Wybierz **nowego użytkownika** i utworzyć nowe konto platformy Azure, wprowadzając nazwy i adresu e-mail. Upewnij się, że **roli w katalogu** ustawiono **użytkownika** i wybierz **Pokaż hasło** pole wyboru na dole, aby wyświetlić i zanotuj hasło wygenerowany automatycznie.
4. Wybierz **Utwórz** można zapisać nowego użytkownika.

Adres e-mail dla konta użytkownika musi być zweryfikowaną nazwę domeny w katalogu. Możesz wyświetlić listę zweryfikowanych domen w katalogu, wybierając **usługi Azure Active Directory** -> **niestandardowe nazwy domen** w menu nawigacji po lewej stronie.

Aby dowiedzieć się więcej na temat dodawania domen niestandardowych w usłudze Azure Active Directory, zobacz [Dodawanie lub kojarzenie domeny w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain).

## <a name="troubleshoot-work-email-sign-in"></a>Rozwiązywanie problemów z logowania w wiadomości e-mail pracy

Jeśli występują problemy z logowaniem do swojego konta służbowego (znany także jako dzierżawy usługi Azure AD), Dowiedz się, że scenariusz na diagramie poniżej najlepiej odpowiada danej sytuacji i wykonaj zalecane czynności.

![Diagram do rozwiązywania problemów logowania konta pracy](./media/onboarding-aad-flow.png)

## <a name="next-steps"></a>Kolejne kroki

- [Zarządzanie kontem komercyjnych Marketplace Centrum partnerskiego](./manage-account.md) 
