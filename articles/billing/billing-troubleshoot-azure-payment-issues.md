---
title: Rozwiązywanie problemów z płatnościami na platformie Azure
description: Rozwiązywanie problemu dotyczącego aktualizowania konta z informacjami o płatności w witrynie Microsoft Azure Portal lub w Centrum konta.
services: azure
author: v-miegge
manager: dcscontentpm
editor: v-jesits
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: jaserano
ms.openlocfilehash: b449dbf455807aab8436f42c25f21f3eea43a678
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225845"
---
# <a name="troubleshoot-azure-payment-issues"></a>Rozwiązywanie problemów z płatnościami na platformie Azure

Przy próbie aktualizacji konta z informacjami o płatności w witrynie Microsoft Azure Portal lub w Centrum konta może wystąpić problem lub błąd.

Aby rozwiązać problem, wybierz poniżej temat, który najlepiej odpowiada występującemu błędowi.

## <a name="unable-to-remove-a-credit-card-from-a-saved-billing-payment-method"></a>Nie można usunąć karty kredytowej z zapisanych form płatności

Niemożność usunięcia karty kredytowej z aktywnej subskrypcji jest zachowaniem celowym.

Jeśli istniejąca karta musi zostać usunięta, do subskrypcji należy dodać nową kartę, aby można było pomyślnie usunąć stary instrument płatniczy. Można też anulować subskrypcję. Spowoduje to trwałe usunięcie subskrypcji i usunięcie karty.

## <a name="unable-to-delete-an-old-payment-method-after-adding-a-new-payment-method"></a>Nie można usunąć starej formy płatności po dodaniu nowej

Nowy instrument płatniczy może nie być skojarzony z subskrypcją. Aby ułatwić skojarzenie instrumentu płatniczego z subskrypcją, zobacz temat [Add, update, or remove a credit or debit card for Azure (Dodawanie, aktualizowanie lub usuwanie karty kredytowej lub debetowej dla platformy Azure)](billing-how-to-change-credit-card.md).

Aby rozwiązać problemy dotyczące odrzuconej karty, zobacz temat [How to troubleshoot a declined card at Azure sign-up](billing-troubleshoot-declined-card.md) (Jak rozwiązywać problemy z odrzuconą kartą podczas rejestracji na platformie Azure).

## <a name="unable-to-delete-a-payment-method-because-of-cannot-delete-payment-method-error"></a>Nie można usunąć formy płatności z powodu komunikatu o błędzie *Nie można usunąć formy płatności*

Dzieje się tak z powodu zaległego salda. Ureguluj zaległe salda przed usunięciem formy płatności.

## <a name="unable-to-see-subscriptions-under-my-account-to-update-the-payment-method"></a>Nie można wyświetlić subskrypcji w obszarze mojego konta w celu zaktualizowania formy płatności

Być może używasz identyfikatora poczty e-mail, który jest inny niż ten używany na potrzeby subskrypcji.

Aby rozwiązać ten problem, zobacz temat [No subscriptions found sign-in error for Azure portal or Azure account center](billing-no-subscriptions-found.md) (Błąd rejestracji „Nie odnaleziono żadnych subskrypcji” w witrynie Azure Portal lub w Centrum konta).

## <a name="unable-to-make-payment-for-a-subscription"></a>Nie można dokonać płatności za subskrypcję

Jeśli zostanie wyświetlony komunikat o błędzie: *Upłynął termin płatności. Wystąpił problem z Twoją formą płatności* lub *Niestety, nie można zapisać informacji. Zamknij przeglądarkę i spróbuj ponownie.* , na karcie znajduje się oczekująca płatność, ponieważ karta została odrzucona przez instytucję finansową.

Sprawdź, czy karta kredytowa ma wystarczające saldo, aby była możliwość dokonania płatności. Jeśli tak nie jest, użyj innej karty, aby dokonać płatności, lub skontaktuj się z instytucją finansową, aby rozwiązać ten problem.

Skontaktuj się z bankiem w przypadku następujących problemów:

- Transakcje międzynarodowe nie są włączone.
- Karta ma limit środków, a saldo musi być rozliczone.
- Na karcie jest włączona płatność cykliczna.

## <a name="unable-to-change-payment-method-because-of-browser-issues-browser-does-not-respond-does-not-load-and-so-on"></a>Nie można zmienić formy płatności z powodu problemów z przeglądarką (przeglądarka nie odpowiada, nie wczytuje strony itd.)

Wyloguj się ze wszystkich aktywnych sesji platformy Azure, a następnie wykonaj kroki opisane w artykule [Przeglądanie InPrivate w Microsoft Edge](https://support.microsoft.com/help/4026200/microsoft-edge-browse-inprivate), aby uruchomić sesję InPrivate w przeglądarce Microsoft Edge lub Internet Explorer.

W sesji prywatnej postępuj zgodnie z instrukcjami opisanymi w artykule [How to change a credit card](billing-how-to-change-credit-card.md) (Jak zmienić kartę kredytową), aby zaktualizować lub zmienić informacje o karcie kredytowej.

Możesz również wykonać następujące czynności:

- Odśwież przeglądarkę.
- Użyj innej przeglądarki.
- Usuń pliki cookie z pamięci podręcznej.

## <a name="my-subscription-is-still-disabled-after-updating-the-payment-method"></a>Moja subskrypcja jest nadal wyłączona po zaktualizowaniu formy płatności.

Ten problem występuje z powodu zaległego salda. Ureguluj zaległe salda przed usunięciem formy płatności.

## <a name="unable-to-change-payment-method-because-of-an-xml-error-response-page"></a>Nie można zmienić formy płatności z powodu strony odpowiedzi błędu XML

Ten komunikat jest wyświetlany, kiedy próbujesz dodać nową kartę kredytową w [witrynie Azure Portal](https://portal.azure.com/).

Aby dodać szczegóły karty, zaloguj się do portalu konta platformy Azure przy użyciu adresu e-mail administratora konta.

## <a name="additional-help-resources"></a>Dodatkowe zasoby pomocy

Inne artykuły dotyczące rozwiązywania problemów z rozliczeniami i subskrypcjami platformy Azure

- [Odrzucona karta](billing-troubleshoot-declined-card.md)
- [Subscription sign-in issues (Problemy z logowaniem do subskrypcji)](billing-troubleshoot-sign-in-issue.md)
- [No subscriptions found (Nie odnaleziono żadnych subskrypcji)](billing-no-subscriptions-found.md)
- [Wyłączony widok kosztów przedsiębiorstwa](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Skontaktuj się z nami, aby uzyskać pomoc

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Następne kroki

- [Dokumentacja dotycząca rozliczeń platformy Azure](index.md)
