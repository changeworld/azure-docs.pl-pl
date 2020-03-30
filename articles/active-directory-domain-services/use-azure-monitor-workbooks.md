---
title: Używanie skoroszytów usługi Azure Monitor w usługach domenowych usługi Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak używać skoroszytów usługi Azure Monitor do przeglądania inspekcji zabezpieczeń i zrozumienia problemów w domenie zarządzanej usług domenowych Usługi active directory platformy Azure.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: iainfou
ms.openlocfilehash: 4bf6926651a0e2e9289b3c729abea16b1c66bce6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80108642"
---
# <a name="review-security-audit-events-in-azure-ad-domain-services-using-azure-monitor-workbooks"></a>Przeglądanie zdarzeń inspekcji zabezpieczeń w usługach domenowych usługi Azure AD przy użyciu skoroszytów usługi Azure Monitor

Aby ułatwić zrozumienie stanu domeny zarządzanej usług domenowych Usługi active directory platformy Azure (Usługi Azure AD DS), można włączyć zdarzenia inspekcji zabezpieczeń. Te zdarzenia inspekcji zabezpieczeń można następnie przeglądać przy użyciu skoroszytów usługi Azure Monitor, które łączą tekst, zapytania analityczne i parametry w zaawansowane raporty interaktywne. Usługa Azure AD DS zawiera szablony skoroszytów do przeglądu zabezpieczeń i aktywności konta, które umożliwiają wykopianie zdarzeń inspekcji i zarządzanie środowiskiem.

W tym artykule pokazano, jak używać skoroszytów usługi Azure Monitor do przeglądania zdarzeń inspekcji zabezpieczeń w usługach Azure AD DS.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby ukończyć ten artykuł, potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem tylko w chmurze.
    * W razie potrzeby [utwórz dzierżawę usługi Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z kontem.][associate-azure-ad-tenant]
* Domena zarządzana usługami domenowymi Usługi Active Directory platformy Azure włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie potrzeby wykonaj samouczek, aby [utworzyć i skonfigurować wystąpienie usług domenowych Usługi domenowe Active Directory][create-azure-ad-ds-instance]platformy Azure .
* Zdarzenia inspekcji zabezpieczeń włączone dla domeny zarządzanej usług domenowych Usługi active directory platformy Azure, które przesyłają strumieniowo dane do obszaru roboczego usługi Log Analytics.
    * W razie potrzeby [włącz inspekcje zabezpieczeń usług domenowych Active Directory platformy Azure][enable-security-audits].

## <a name="azure-monitor-workbooks-overview"></a>Omówienie skoroszytów monitora platformy Azure

Gdy zdarzenia inspekcji zabezpieczeń są włączone w usługach Azure AD DS, może być trudne do przeanalizowania i zidentyfikowania problemów w domenie zarządzanej. Usługa Azure Monitor umożliwia agregowanie tych zdarzeń inspekcji zabezpieczeń i wykonywanie zapytań o dane. Dzięki skoroszytom usługi Azure Monitor można wizualizować te dane, aby ułatwić ich identyfikowanie.

Szablony skoroszytów są wyselekcjonowanymi raportami, które są przeznaczone do elastycznego ponownego użycia przez wielu użytkowników i zespoły. Po otwarciu szablonu skoroszytu dane ze środowiska usługi Azure Monitor są ładowane. Szablonów można używać bez wpływu na innych użytkowników w organizacji i zapisywać własne skoroszyty na podstawie szablonu.

Usługi Azure AD DS obejmują następujące dwa szablony skoroszytu:

* Raport przeglądu zabezpieczeń
* Raport aktywności konta

Aby uzyskać więcej informacji na temat edytowania skoroszytów i zarządzania nimi, zobacz [Omówienie skoroszytów monitora Platformy Azure](../azure-monitor/platform/workbooks-overview.md).

## <a name="use-the-security-overview-report-workbook"></a>Korzystanie ze skoroszytu raportu przeglądu zabezpieczeń

Aby lepiej zrozumieć użycie i zidentyfikować potencjalne zagrożenia bezpieczeństwa, raport przeglądowy zabezpieczeń podsumowuje dane logowania i identyfikuje konta, które można sprawdzić. Zdarzenia w określonym zakresie dat można wyświetlić i przejść do szczegółów określonych zdarzeń logowania, takich jak próby nieprawidłowego hasła lub wyłączenie konta.

Aby uzyskać dostęp do szablonu skoroszytu raportu przeglądu zabezpieczeń, wykonaj następujące czynności:

1. Wyszukaj i wybierz **usługi domenowe Usługi domenowe Active Directory** platformy Azure w witrynie Azure portal.
1. Wybierz domenę zarządzana, na przykład *aaddscontoso.com*
1. Z menu po lewej stronie wybierz pozycję **Monitorowanie > skoroszyty**

    ![Wybierz opcję menu Skoroszyty w witrynie Azure portal](./media/use-azure-monitor-workbooks/select-workbooks-in-azure-portal.png)

1. Wybierz **raport Przegląd zabezpieczeń**.
1. Z menu rozwijanych u góry skoroszytu wybierz subskrypcję platformy Azure, a następnie obszar roboczy usługi Azure Monitor. Wybierz **zakres czasu**, na przykład *Ostatnie 7 dni*.

    ![Wybierz opcję menu Skoroszyty w witrynie Azure portal](./media/use-azure-monitor-workbooks/select-query-filters.png)

    Opcje **widoku kafelków** i **widoku wykresu** można również zmienić w celu analizowania i wizualizowania danych zgodnie z potrzebami

1. Aby przejść do określonego typu zdarzenia, wybierz jedną z kart **wyników logowania,** takich jak *Konto zablokowane,* jak pokazano w poniższym przykładzie:

    ![Przykładowe dane raportu przeglądu zabezpieczeń wizualizowane w skoroszytach monitora Platformy Azure](./media/use-azure-monitor-workbooks/example-security-overview-report.png)

1. Dolna część raportu przeglądu zabezpieczeń poniżej wykresu następnie rozkłada wybrany typ działania. Możesz filtrować według nazw użytkowników zaangażowanych po prawej stronie, jak pokazano w poniższym przykładowym raporcie:

    [![](./media/use-azure-monitor-workbooks/account-lockout-details-cropped.png "Details of account lockouts in Azure Monitor Workbooks")](./media/use-azure-monitor-workbooks/account-lockout-details.png#lightbox)

## <a name="use-the-account-activity-report-workbook"></a>Korzystanie ze skoroszytu raportu aktywności konta

Aby ułatwić rozwiązywanie problemów z określonym kontem użytkownika, raport aktywności konta dzieli szczegółowe informacje dziennika zdarzeń inspekcji. Możesz przejrzeć, kiedy podczas logowania została podana nieprawidłowa nazwa użytkownika lub hasło, oraz źródło próby logowania.

Aby uzyskać dostęp do szablonu skoroszytu raportu aktywności konta, wykonaj następujące czynności:

1. Wyszukaj i wybierz **usługi domenowe Usługi domenowe Active Directory** platformy Azure w witrynie Azure portal.
1. Wybierz domenę zarządzana, na przykład *aaddscontoso.com*
1. Z menu po lewej stronie wybierz pozycję **Monitorowanie > skoroszyty**
1. Wybierz **raport aktywności konta**.
1. Z menu rozwijanych u góry skoroszytu wybierz subskrypcję platformy Azure, a następnie obszar roboczy usługi Azure Monitor. Wybierz **zakres czasu**, na przykład *Ostatnie 30 dni,* a następnie sposób reprezentowania danych **w widoku kafelka.** Możesz filtrować według **nazwy użytkownika konta,** na przykład *felix,* jak pokazano w poniższym przykładowym raporcie:

    [![](./media/use-azure-monitor-workbooks/account-activity-report-cropped.png "Account activity report in Azure Monitor Workbooks")](./media/use-azure-monitor-workbooks/account-activity-report.png#lightbox)

    Obszar poniżej wykresu zawiera poszczególne zdarzenia logowania wraz z informacjami, takimi jak wynik działania i źródłowsza stacja robocza. Te informacje mogą pomóc w określeniu powtarzających się źródeł zdarzeń logowania, które mogą powodować blokady konta lub wskazywać na potencjalny atak.

Podobnie jak w raporcie przeglądu zabezpieczeń, można przejść do szczegółów różnych kafelków w górnej części raportu, aby wizualizować i analizować dane w razie potrzeby.

## <a name="save-and-edit-workbooks"></a>Zapisywanie i edytowanie skoroszytów

Dwa skoroszyty szablonów dostarczone przez usługi Azure AD DS są dobrym miejscem do rozpoczęcia od własnej analizy danych. Jeśli chcesz uzyskać bardziej szczegółowe w kwerendach danych i dochodzeniach, możesz zapisać własne skoroszyty i edytować zapytania.

1. Aby zapisać kopię jednego z szablonów skoroszytu, wybierz pozycję **Edytuj > Zapisz jako > udostępnione raporty,** a następnie podaj nazwę i zapisz ją.
1. Z własnej kopii szablonu wybierz pozycję **Edytuj,** aby przejść do trybu edycji. Możesz wybrać niebieski przycisk **Edytuj** obok dowolnej części raportu i zmienić go.

Wszystkie wykresy i tabele w skoroszytach usługi Azure Monitor są generowane przy użyciu zapytań Kusto. Aby uzyskać więcej informacji na temat tworzenia własnych zapytań, zobacz [Kwerendy dziennika usługi Azure Monitor][azure-monitor-queries] i [samouczek zapytań Kusto][kusto-queries].

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz dostosować zasady haseł i blokady, zobacz [Zasady blokowania haseł i kont w domenach zarządzanych][password-policy].

Aby uzyskać problemy z użytkownikami, dowiedz się, jak rozwiązać problemy z [logowaniem][troubleshoot-sign-in] do konta lub [problemy z blokadą konta][troubleshoot-account-lockout].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-security-audits]: security-audit-events.md
[password-policy]: password-policy.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-monitor-queries]: ../azure-monitor/log-query/query-language.md
[kusto-queries]: https://docs.microsoft.com/azure/kusto/query/tutorial?pivots=azuredataexplorer
