---
title: Rozwiązywanie problemów z płatnościami na platformie Azure
description: Rozwiązywanie problemu podczas aktualizowania konta informacji o płatności w Microsoft Azure Portal lub centrum konta.
author: v-miegge
manager: na
editor: v-jesits
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: jaserano
ms.openlocfilehash: 45f6f181ddbf7e76662f76c433d8ff68c18f0de1
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657088"
---
# <a name="troubleshoot-azure-payment-issues"></a>Rozwiązywanie problemów z płatnościami na platformie Azure

Podczas próby zaktualizowania konta informacji o płatności w Microsoft Azure Portal lub centrum konta platformy Azure może wystąpić problem. Przed rozwiązaniu problemu należy wziąć pod uwagę następujące wytyczne:

- Upewnij się, że informacje podane dla profilu konta platformy Azure (w tym adres e-mail kontaktu, adres ulicy i numer telefonu) są poprawne.
- Upewnij się, że informacje o karcie kredytowej są poprawne.
- Upewnij się, że nie masz jeszcze konta Microsoft z tymi samymi informacjami.

## <a name="issues"></a>Problemy

Aby rozwiązać wszelkie błędy, wybierz problem, który wystąpi podczas próby zarejestrowania się na platformie Azure.

### <a name="unable-to-remove-a-credit-card-from-a-saved-billing-payment-method"></a>Nie można usunąć karty kredytowej z zapisanej metody płatności rozliczeń

Po zaprojektowaniu nie można usunąć karty kredytowej z aktywnej subskrypcji.

Jeśli istniejąca karta musi zostać usunięta, do subskrypcji należy dodać nową kartę, aby można było pomyślnie usunąć stary instrument płatniczy lub anulować subskrypcję. Spowoduje to trwałe usunięcie subskrypcji i usunięcie karty.

### <a name="unable-to-delete-an-old-payment-method-after-adding-a-new-payment-method"></a>Nie można usunąć starej metody płatności po dodaniu nowej metody płatności

Nowy instrument płatniczy może nie być skojarzony z subskrypcją. Aby ułatwić skojarzenie instrumentu płatniczego z subskrypcją, zobacz [Dodawanie, aktualizowanie lub usuwanie karty kredytowej lub debetowej na platformie Azure](billing-how-to-change-credit-card.md).

Aby rozwiązać problemy dotyczące odrzuconej karty, zobacz Jak rozwiązywać problemy z odrzuconym [kartą na platformie Azure](billing-troubleshoot-declined-card.md).

### <a name="unable-to-delete-a-payment-method-because-of-cannot-delete-payment-method-error"></a>Nie można usunąć formy płatności z powodu błędu *nie można usunąć metody płatności*

Dzieje się tak ze względu na zaległe saldo. Przed usunięciem metody płatności Wyczyść wszystkie zaległe saldo.

### <a name="unable-to-see-subscriptions-under-my-account-to-update-the-payment-method"></a>Nie można wyświetlić subskrypcji w ramach mojego konta, aby zaktualizować metodę płatności

Być może używasz identyfikatora poczty e-mail, który jest inny niż ten, który jest używany przez subskrypcje.

Aby rozwiązać ten problem, zobacz [nie znaleziono żadnych subskrypcji błąd logowania dla Azure Portal lub centrum konta platformy Azure](billing-no-subscriptions-found.md).

### <a name="unable-to-make-payment-for-a-subscription"></a>Nie można dokonać płatności za subskrypcję

Jeśli zostanie wyświetlony komunikat o błędzie: *Upłynął termin płatności. Wystąpił problem z Twoją formą płatności* lub *Niestety, nie można zapisać informacji. Zamknij przeglądarkę i spróbuj ponownie.* oznacza to, że na karcie istnieje Oczekująca płatność, ponieważ karta została odrzucona przez instytucję finansową.

Sprawdź, czy karta kredytowa ma wystarczające saldo, aby dokonać płatności. Jeśli tak nie jest, użyj innej karty, aby dokonać płatności, lub skontaktuj się z instytucją finansową, aby rozwiązać ten problem.

Skontaktuj się z bankiem, aby uzyskać następujące problemy:

- Transakcje międzynarodowe nie są włączone.
- Karta ma limit kredytu, a saldo musi być rozliczone.
- Na karcie jest włączona cykliczna płatność.

### <a name="unable-to-change-payment-method-because-of-browser-issues-browser-does-not-respond-does-not-load-and-so-on"></a>Nie można zmienić formy płatności z powodu problemów z przeglądarką (przeglądarka nie odpowiada, nie ładuje i tak dalej)

Wyloguj się ze wszystkich aktywnych sesji platformy Azure, a następnie wykonaj kroki opisane w [artykule przeglądanie InPrivate w programie Microsoft Edge](https://support.microsoft.com/help/4026200/microsoft-edge-browse-inprivate) , aby uruchomić sesję nieprywatną w przeglądarce Microsoft Edge lub Internet Explorer.

W sesji prywatnej postępuj zgodnie z instrukcjami, [Aby zmienić kartę kredytową](billing-how-to-change-credit-card.md) w celu zaktualizowania lub zmiany informacji o karcie kredytowej.

Możesz również spróbować wykonać następujące czynności:

- Odśwież przeglądarkę
- Użyj innej przeglądarki
- Usuwanie buforowanych plików cookie

### <a name="my-subscription-is-still-disabled-after-updating-the-payment-method"></a>Moja subskrypcja jest nadal wyłączona po zaktualizowaniu formy płatności.

Ten problem występuje ze względu na zaległe saldo. Przed usunięciem metody płatności Wyczyść wszystkie zaległe saldo.

### <a name="unable-to-change-payment-method-because-of-an-xml-error-response-page"></a>Nie można zmienić formy płatności z powodu strony odpowiedzi na błąd XML

Ten komunikat jest wyświetlany, jeśli używasz [Azure Portal](https://portal.azure.com/) do dodania nowej karty kredytowej.

Aby dodać szczegóły karty, zaloguj się do portalu konta platformy Azure przy użyciu adresu e-mail administratora konta.

## <a name="additional-help-resources"></a>Dodatkowe zasoby pomocy

Inne artykuły dotyczące rozwiązywania problemów dotyczących rozliczeń i subskrypcji platformy Azure

- [Odrzucona karta](billing-troubleshoot-declined-card.md)
- [Problemy z logowaniem do subskrypcji](billing-troubleshoot-sign-in-issue.md)
- [No subscriptions found (Nie odnaleziono żadnych subskrypcji)](billing-no-subscriptions-found.md)
- [Wyłączony widok kosztów przedsiębiorstwa](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Skontaktuj się z nami, aby uzyskać pomoc

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Następne kroki

- [Dokumentacja dotycząca rozliczeń platformy Azure](index.md)
