---
title: Rozwiązywanie problemów z rejestrowaniem na platformie Azure
description: Rozwiązywanie problemu podczas próby zarejestrowania się w celu uzyskania nowego konta w centrum konta Microsoft Azure Portal.
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
ms.author: v-miegge
ms.openlocfilehash: 57dc9c05778563bc0c5df1a8ac8895b51008b01f
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657075"
---
# <a name="troubleshoot-azure-sign-up"></a>Rozwiązywanie problemów z rejestrowaniem na platformie Azure

Wystąpił problem podczas próby zarejestrowania się w celu uzyskania nowego konta w Microsoft Azure Portal lub centrum konta platformy Azure. Przed rozwiązaniu problemu należy najpierw sprawdzić następujące kwestie:

- Informacje podane dla profilu konta platformy Azure (w tym adres e-mail kontaktu, ulica i numer telefonu) są poprawne.
- Informacje o karcie kredytowej są poprawne.
- Nie masz jeszcze konto Microsoft, który ma te same informacje.

## <a name="resolutions"></a>Rozwiązania

Aby rozwiązać wszelkie błędy, wybierz problem, który wystąpi podczas próby zarejestrowania się na platformie Azure.

### <a name="error-we-cannot-proceed-with-sign-up-due-to-an-issue-with-your-account-please-contact-billing-support"></a>Błąd: *Nie można kontynuować rejestracji z powodu problemu z Twoim kontem. Skontaktuj się z pomocą techniczną.*

Aby rozwiązać ten problem, wykonaj następujące kroki:

1. Zaloguj się do [centrum konta platformy Azure](https://account.azure.com/Profile) przy użyciu poświadczeń administratora konta.

2. Wybierz pozycję **Edytuj szczegóły**.

3. Upewnij się, że wszystkie pola adresu zostały wykonane i są prawidłowe.

4. Po zarejestrowaniu się w ramach subskrypcji platformy Azure upewnij się, że adres rozliczeniowy rejestracji karty kredytowej jest zgodny z rekordami banku.

Jeśli komunikat o błędzie będzie nadal wyświetlany, spróbuj zarejestrować się przy użyciu innej przeglądarki.

### <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>Pasek postępu zawiesza się w sekcji *weryfikacja tożsamości według karty* .

Aby ukończyć weryfikację tożsamości za pomocą karty, pliki cookie innych firm muszą być dozwolone w przeglądarce.

![Weryfikacja tożsamości przy użyciu karty](./media/billing-troubleshoot-azure-sign-up/identify-verification-by-card.png)
 
Wykonaj poniższe kroki, aby zaktualizować ustawienia plików cookie w przeglądarce.

1. Jeśli używasz programu Chrome, wybierz pozycję **Ustawienia** > **Pokaż ustawienia zaawansowane** > ustawienia**zawartości** **prywatności** > . Wyczyść pole wyboru **Blokuj pliki cookie innych firm i dane lokacji**.

2. Jeśli używasz przeglądarki Microsoft Edge, wybierz pozycję **Ustawienia** > **Widok ustawienia zaawansowane** > **pliki cookie** > **nie blokuj plików cookie**.

3. Odśwież stronę rejestracji platformy Azure, a następnie sprawdź, czy problem został rozwiązany.

4. Jeśli odświeżenie nie rozwiązało problemu, Zamknij i uruchom ponownie przeglądarkę, a następnie spróbuj ponownie.

### <a name="credit-card-form-doesnt-support-my-billing-address"></a>Formularz karty kredytowej nie obsługuje mojego adresu rozliczeniowego

Adres rozliczeniowy musi znajdować się w kraju, który został wybrany w sekcji **Informacje o użytkowniku** . Upewnij się, że wybrano prawidłowy kraj.

### <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>Brak komunikatów tekstowych lub wywołań podczas weryfikacji konta rejestracji

Chociaż proces jest zwykle szybki, może upłynąć do czterech minut, aby można było dostarczyć kod weryfikacyjny. Numer telefonu wprowadzony do weryfikacji nie jest przechowywany jako numer osoby kontaktowej dla konta.

Oto kilka dodatkowych porad:

- Nie można użyć numeru telefonu w trybie Voice-over-IP (VoiP) do procesu weryfikacji telefonu.
- Sprawdź dwukrotnie wprowadzony numer telefonu, w tym kod kraju wybrany w menu rozwijanym.
- Jeśli telefon nie odbiera wiadomości tekstowych (SMS), spróbuj użyć opcji **Zadzwoń do mnie** .
- Upewnij się, że telefon może odbierać wywołania lub wiadomości SMS z numeru telefonu Stany Zjednoczoneowego.

Po otrzymaniu wiadomości tekstowej lub połączenia telefonicznego wprowadź kod otrzymany w polu tekstowym.

### <a name="credit-card-declined-or-not-accepted"></a>Karta kredytowa została odrzucona lub nie została zaakceptowana

Karty kredytowe lub zaliczkowe nie są akceptowane jako płatność za subskrypcje platformy Azure. Aby zobaczyć, co jeszcze może spowodować odrzucenie Twojej karty, zobacz Karta debetowa lub karta kredytowa została [odrzucona przy rejestracji na platformie Azure](https://support.microsoft.com/help/4042960).

### <a name="free-trial-is-not-available"></a>Bezpłatna wersja próbna jest niedostępna

Czy w przeszłości użyto subskrypcji platformy Azure? Umowa dotycząca warunków użytkowania platformy Azure ogranicza aktywację bezpłatnej wersji próbnej tylko w przypadku nowych użytkowników platformy Azure. Jeśli masz dowolny inny typ subskrypcji platformy Azure, nie możesz aktywować bezpłatnej wersji próbnej. Rozważ zarejestrowanie się w celu uzyskania [subskrypcji z opcją płatność zgodnie z rzeczywistym](https://azure.microsoft.com/offers/ms-azr-0003p/)użyciem.

### <a name="i-saw-a-charge-on-my-free-trial-account"></a>Otrzymuję opłaty za bezpłatne konto próbne

Po zarejestrowaniu się na koncie karty kredytowej może zostać wyświetlone niewielkie wstrzymanie weryfikacji. To zostało usunięte w ciągu trzech do pięciu dni. Jeśli martwisz się się o zarządzanie kosztami, Przeczytaj więcej na temat [zapobiegania](billing-getting-started.md)nieoczekiwanym kosztom.

### <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>Nie można aktywować planu korzyści platformy Azure, takiego jak MSDN, BizSpark, BizSparkPlus lub MPN

Upewnij się, że używasz prawidłowych poświadczeń logowania. Następnie sprawdź program korzyści, aby upewnić się, że masz odpowiednie uprawnienia.

- MSDN 
  - Sprawdź swój status uprawnień na [stronie konta MSDN](https://msdn.microsoft.com/subscriptions/manage/default.aspx).
  - Jeśli nie możesz zweryfikować swojego stanu, skontaktuj się z [centrum obsługi klienta subskrypcji MSDN](https://msdn.microsoft.com/library/aa493452.aspx).
- Microsoft for Startups
  - Zaloguj się do [portalu Microsoft for Startups](https://startups.microsoft.com/#start-two) , aby zweryfikować swój status uprawnień dla firmy Microsoft podczas uruchamiania.
  - Jeśli nie możesz zweryfikować swojego stanu, możesz uzyskać pomoc dotyczącą [forów uruchamiania w firmie Microsoft](https://www.microsoftpartnercommunity.com/t5/Microsoft-for-Startups/ct-p/Microsoft_Startups).
- MPN 
  - Zaloguj się do [portalu MPN](https://mspartner.microsoft.com/Pages/Locale.aspx) , aby zweryfikować swój stan uprawnień. Jeśli masz odpowiednie kompetencje dotyczące [platformy w chmurze](https://mspartner.microsoft.com/pages/membership/cloud-platform-competency.aspx), możesz kwalifikować się do dodatkowych korzyści.
  - Jeśli nie możesz zweryfikować swojego stanu, skontaktuj się z [pomocą techniczną MPN](https://mspartner.microsoft.com/Pages/Support/Premium/contact-support.aspx).

### <a name="cant-activate-new-azure-in-open-subscription"></a>Nie można aktywować nowej subskrypcji platformy Azure w ramach programu licencjonowania Open

Aby utworzyć subskrypcję platformy Azure w ramach programu licencjonowania Open, musisz dysponować prawidłowym kluczem aktywacji usług online (OSA), który ma skojarzony co najmniej jeden token platformy Azure w ramach programu licencjonowania Open. Jeśli nie masz klucza OSA, skontaktuj się z przedstawicielem firmy Microsoft wymienionym w [witrynie Microsoft Pinpoint](http://pinpoint.microsoft.com/).

### <a name="error-you-are-not-eligible-for-an-azure-subscription"></a>Błąd: *Nie masz uprawnień do subskrypcji platformy Azure*

Aby rozwiązać ten problem, należy sprawdzić, czy są spełnione następujące elementy:

- Informacje podane dla profilu konta platformy Azure (w tym adres e-mail kontaktu, ulica i numer telefonu) są poprawne.
- Informacje o karcie kredytowej są poprawne.
- Nie masz jeszcze konto Microsoft, która używa tych samych informacji.

### <a name="error-your-current-account-type-is-not-supported"></a>Błąd: *Bieżący typ konta nie jest obsługiwany*

Ten problem może wystąpić, jeśli konto jest zarejestrowane w niezarządzanym [katalogu usługi Azure AD](../active-directory/users-groups-roles/directory-self-service-signup.md)i nie znajduje się w katalogu usługi Azure AD w organizacji. 

Aby rozwiązać ten problem, zaloguj się do konta platformy Azure przy użyciu innego konta lub Przejmij niezarządzany katalog usługi AD. Aby uzyskać więcej informacji, zobacz Przejmowanie niezarządzanego [katalogu jako administrator w Azure Active Directory](../active-directory/users-groups-roles/domains-admin-takeover.md).
 
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
