---
title: Rozwiązywanie problemów z logowaniem do subskrypcji platformy Azure
description: Pomaga w rozwiązywaniu problemów, w których nie można zalogować się na koncie Azure Portal lub centrum konta platformy Azure.
author: v-miegge
manager: na
editor: na
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: v-miegge
ms.openlocfilehash: ca641813e8b01a39d31a56e3730424b0fa1d6436
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657049"
---
# <a name="troubleshoot-azure-subscription-sign-in-issues"></a>Rozwiązywanie problemów z logowaniem do subskrypcji platformy Azure

Ten przewodnik pomaga w rozwiązywaniu problemów, w których nie można zalogować się do konta Azure Portal lub centrum kont platformy Azure. 

## <a name="issues"></a>Problemy

### <a name="page-hangs-in-the-loading-status"></a>Strona zawiesza się w stanie ładowania

Jeśli strona przeglądarki internetowej się zawiesza, spróbuj wykonać następujące czynności, aby uzyskać dostęp do Azure Portal.

- Odśwież stronę.
- Użyj innej przeglądarki internetowej.
- Użyj trybu przeglądania prywatnego dla przeglądarki. Dla programu Internet Explorer: Kliknij kolejno pozycje **Narzędzia** > **zabezpieczenia** > **InPrivate przeglądanie**, a następnie Przeglądaj i zaloguj się [Azure Portal](https://portal.azure.com/) do [centrum konta platformy Azure](https://account.azure.com/Subscriptions).

### <a name="you-are-automatically-signed-in-as-a-different-user"></a>Użytkownik jest automatycznie zalogowany jako inny użytkownik

Ten problem może wystąpić, jeśli w przeglądarce internetowej używasz więcej niż jednego konta użytkownika.

Aby rozwiązać ten problem, wypróbuj jedną z następujących metod:

- Wyczyść pamięć podręczną i Usuń pliki cookie z Internetu. W programie Internet Explorer kliknij kolejno pozycje **Narzędzia** > **Opcje** > internetowe**Usuń**. Upewnij się, że zaznaczone są pola wyboru dla plików tymczasowych, plików cookie, hasła i historii przeglądania, a następnie kliknij przycisk Usuń.
- Zresetuj ustawienia programu Internet Explorer, aby przywrócić wszystkie ustawienia osobiste, które zostały wprowadzone. Kliknij kolejno pozycje **Narzędzia** > **Opcje** > internetowe**Zaawansowane** > zaznacz pole **Usuń ustawienia osobiste** > **Zresetuj**.
- Użyj trybu przeglądania prywatnego dla przeglądarki. Dla programu Internet Explorer:  Kliknij kolejno pozycje **Narzędzia** > **zabezpieczenia** > **InPrivate przeglądanie**, a następnie Przeglądaj i zaloguj się [Azure Portal](https://portal.azure.com/) do [centrum konta platformy Azure](https://account.azure.com/Subscriptions).

### <a name="i-can-sign-in-but-i-see-no-subscriptions-found"></a>Mogę się zalogować, ale *nie znaleziono żadnych subskrypcji*

Ten problem występuje w przypadku wybrania niewłaściwego katalogu, czy Twoje konto nie ma wystarczających uprawnień.

**Scenariusz 1:** W [Azure Portal](https://portal.azure.com/) zostanie wyświetlony komunikat o błędzie

Aby rozwiązać ten problem:

- Upewnij się, że wybrano prawidłowy katalog usługi Azure, klikając swoje konto w prawym górnym rogu.
- Jeśli wybrano odpowiedni katalog platformy Azure, ale nadal pojawia się komunikat o błędzie, Twoje konto zostało [dodane jako właściciel](billing-add-change-azure-subscription-administrator.md).

**Scenariusz 2:** W [centrum konta platformy Azure](https://account.windowsazure.com/Subscriptions) zostanie wyświetlony komunikat o błędzie

Sprawdź, czy konta, którego użyto jest administratora konta. Aby sprawdzić, kim jest Administrator konta, wykonaj następujące kroki:

1. Zaloguj się do [subskrypcje wyświetlić w witrynie Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Wybierz subskrypcję, aby sprawdzić, a następnie sprawdź w obszarze **ustawienia**.

3. Wybierz **właściwości**. Administrator konta subskrypcji są wyświetlane w **administrator konta** pole.

## <a name="additional-help-resources"></a>Dodatkowe zasoby pomocy

Inne artykuły dotyczące rozwiązywania problemów dotyczących rozliczeń i subskrypcji platformy Azure

- [Odrzucona karta](billing-troubleshoot-declined-card.md)
- [Problemy z rejestracją subskrypcji](billing-troubleshoot-azure-sign-up.md)
- [No subscriptions found (Nie odnaleziono żadnych subskrypcji)](billing-no-subscriptions-found.md)
- [Wyłączony widok kosztów przedsiębiorstwa](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Skontaktuj się z nami, aby uzyskać pomoc

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Następne kroki

- [Dokumentacja dotycząca rozliczeń platformy Azure](index.md)
