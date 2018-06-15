---
title: Korzystanie z Centrum zabezpieczeń Azure zalecenia, aby zwiększyć bezpieczeństwo | Dokumentacja firmy Microsoft
description: " Dowiedz się, jak używać zasad zabezpieczeń i zalecenia w Centrum zabezpieczeń Azure do zmniejszenia atak na zabezpieczenia. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: terrylan
ms.openlocfilehash: 0616f5e501324bfd821c1455ce234602f1fcf1bd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866667"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Korzystanie z Centrum zabezpieczeń Azure zaleceń w celu zwiększenia zabezpieczeń
Konfigurowanie zasad zabezpieczeń, a następnie wdrażanie zaleceń dotyczących, świadczona przez Centrum zabezpieczeń Azure można zmniejszyć prawdopodobieństwo zdarzeń zabezpieczeń. W tym artykule przedstawiono sposób użycia zasady zabezpieczeń i zalecenia w Centrum zabezpieczeń, aby ułatwić uniknięcie atak na zabezpieczenia.

> [!NOTE]
> W tym artykule opiera się na role i założenia w Centrum zabezpieczeń [przewodnik dotyczący planowania i operacje](security-center-planning-and-operations-guide.md). Należy dobrze jest przejrzeć przewodnik planowania przed kontynuowaniem.
>
>

## <a name="managing-security-recommendations"></a>Zarządzanie zaleceniami dotyczącymi zabezpieczeń
Zasady zabezpieczeń określają zestaw kontrolek, które są zalecane dla zasobów w określonej subskrypcji lub grupy zasobów. W Centrum zabezpieczeń można zdefiniować zasady zgodnie z wymaganiami firmy dotyczącymi zabezpieczeń. Aby dowiedzieć się więcej, zobacz [ustawić zasady zabezpieczeń w Centrum zabezpieczeń](security-center-policies.md).

Zasady zabezpieczeń dla grupy zasobów są dziedziczone z poziomu subskrypcji.

![Dziedziczenie zasad zabezpieczeń][1]

Jeśli potrzebujesz niestandardowych zasad w określonych grupach zasobów, można wyłączyć dziedziczenia w grupie zasobów. Aby wyłączyć, ustaw dziedziczenie Unique w bloku zasady zabezpieczeń i dostosowywanie Centrum zabezpieczeń zawiera zalecenia dotyczące kontrolek.

Na przykład jeśli masz obciążeń, które nie wymagają zasad SQL bazy danych przezroczysty danych szyfrowania (funkcji TDE), Wyłącz zasady na poziomie subskrypcji i włącz je tylko w grupach zasobów, w którym wymagany jest funkcji SQL TDE.

> [!NOTE]
> Jeśli między zasadami poziomu subskrypcji i zasadami poziomu grupy zasobów występuje konflikt, pierwszeństwo ma poziom zasad grupy zasobów.
>
>

Usługa Security Center analizuje stan zabezpieczeń zasobów platformy Azure. Jeśli Centrum zabezpieczeń zostanie zidentyfikowana potencjalnych luk w zabezpieczeniach, tworzy zalecenia dotyczące kontrolek w zasadach zabezpieczeń. Zalecenia prowadzące przez proces konfigurowania opcji zabezpieczeń wymagane.

Bieżące zasady zalecenia w Centrum zabezpieczeń fokus na aktualizacje systemu, konfiguracja systemu operacyjnego, sieciowej grupy zabezpieczeń w podsieci i maszyn wirtualnych (VM), SQL Database Auditing funkcji TDE bazy danych SQL, i zapory aplikacji sieci web. Najbardziej aktualne pokrycia zaleceń Centrum zabezpieczeń, zobacz [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Centrum zabezpieczeń](security-center-recommendations.md).

## <a name="scenario"></a>Scenariusz
W tym scenariuszu pokazano sposób użycia Centrum zabezpieczeń, aby zmniejszyć prawdopodobieństwo zdarzenia zabezpieczeń przez monitorowanie zaleceń Centrum zabezpieczeń i podejmowania działań. Scenariusz używa fikcyjnej firmy Contoso i ról przedstawionych w Centrum zabezpieczeń [przewodnik dotyczący planowania i operacje](security-center-planning-and-operations-guide.md#security-roles-and-access-controls). Role reprezentują osób oraz zespołów, które mogą korzystać z Centrum zabezpieczeń do wykonywania różnych zadań związanych z zabezpieczeniami. Dostępne są następujące role:

![Scenariusz ról][2]

Contoso ostatnio migracji niektórych ich lokalnych zasobów na platformie Azure. Firma Contoso chce wdrożenia i konserwacji zabezpieczenia udostępniane przez zmniejszyć ich podatności na atak na zabezpieczenia ich zasobów w chmurze.

## <a name="recommended-solution"></a>Zalecane rozwiązanie
Rozwiązaniem jest Centrum zabezpieczeń umożliwia zapobieganie i Wykrywanie luk w zabezpieczeniach. Firma Contoso ma dostęp do Centrum zabezpieczeń za pośrednictwem ich subskrypcji platformy Azure. [Warstwę bezpłatna](security-center-pricing.md) z Centrum zabezpieczeń jest automatycznie włączone na wszystkich subskrypcji platformy Azure i zbieranie danych jest włączone na wszystkich maszynach wirtualnych w swoich subskrypcji.

Konfiguruje Dominik działu zabezpieczeń IT firmy Contoso, **zasady zabezpieczeń** przy użyciu Centrum zabezpieczeń. Centrum zabezpieczeń analizuje stan zabezpieczeń zasobów platformy Azure firmy Contoso. Jeśli Centrum zabezpieczeń zostanie zidentyfikowana potencjalnych luk w zabezpieczeniach, tworzy **zalecenia** oparte na formanty w zasadach zabezpieczeń.

Jan, właściciel obciążenia chmury, jest odpowiedzialny za wykonanie i utrzymywanie ochrony zgodnie z zasadami zabezpieczeń firmy Contoso. Jan można monitorować zalecenia utworzone przez Centrum zabezpieczeń w celu zastosowania ochrony. Zalecenia przeprowadzają Jeff przez proces konfigurowania opcji zabezpieczeń wymagane.

Aby Jan do wdrożenia i obsługę ochrony i eliminowania luk w zabezpieczeniach musi:

- Zalecenia dotyczące zabezpieczeń Monitor świadczona przez Centrum zabezpieczeń
- Oceń zalecenia dotyczące zabezpieczeń i zdecyduj, czy powinien on zastosować lub odrzucać
- Zastosuj zalecenia dotyczące zabezpieczeń

Załóżmy wykonaj jego Jeff kroki, aby zobaczyć, jak używa zaleceń Centrum zabezpieczeń mu prowadzące przez proces konfigurowania kontroli w celu wyeliminowania luki w zabezpieczeniach.

## <a name="how-to-implement-this-solution"></a>Sposób implementacji rozwiązania
Jan loguje się do [portalu Azure](https://azure.microsoft.com/features/azure-portal/) i otwiera konsolę Centrum zabezpieczeń. W ramach jego codziennych działań monitorowania on sprawdza, czy istnieją zalecenia dotyczące zabezpieczeń, wykonując następujące czynności:

1. Jan wybiera **zalecenia** Kafelek, aby otworzyć **zalecenia**.
   ![Wybierz Kafelek zalecenia][3]
2. Jan przegląda lista zaleceń. Będzie on widział podania w Centrum zabezpieczeń lista zaleceń według priorytetu z najwyższym priorytetem do najniższego priorytetu. Postanowił adresów zalecenie o wysokim priorytecie, na liście. Wybiera **Zainstaluj program Endpoint Protection** w obszarze **zalecenia**.
3. **Zainstaluj program Endpoint Protection** otwiera wyświetlanie listy maszyn wirtualnych bez ochrony przed złośliwym kodem jest włączona. Jan przegląda listy maszyn wirtualnych, wybiera wszystkich maszyn wirtualnych i następnie wybiera **zainstalować na maszynach wirtualnych 3**.
   ![Instalowanie ochrony punktu końcowego][4]
4. **Wybierz program Endpoint Protection** otwiera, zapewniając Jeff dwa rozwiązania chroniące przed złośliwym kodem. Jan wybiera **Microsoft Antimalware** rozwiązania.
5. Dodatkowe informacje dotyczące rozwiązania chroniące przed złośliwym kodem są wyświetlane. Jan wybiera **Utwórz**.
   ![Ochrony przed złośliwym oprogramowaniem firmy Microsoft][5]
6. Jan wprowadza wymaganych ustawień konfiguracyjnych w obszarze **zainstalować** i wybiera **OK**.

[Microsoft Antimalware](../security/azure-security-antimalware.md) jest teraz aktywny na wybranych maszyn wirtualnych.

Jan w dalszym ciągu poruszania się o wysokim priorytecie i zalecenia średni priorytet, podejmowanie decyzji o implementacji. Odwołuje się do Jeff [Zarządzanie zaleceniami dotyczącymi zabezpieczeń](security-center-recommendations.md) artykuł, aby zrozumieć zalecenia i co każdego z nich pojawia się on ma zastosowanie.

Jan uzyskuje informacje o który [Microsoft Security odpowiedzi Center (MSRC)](../security/azure-security-response-center.md) wykonuje monitorowanie zabezpieczeń wybierz sieć platformy Azure i infrastruktury i odbiera zagrożeń analizy i nadużycia utrudnień od osób trzecich. Jeśli Jeff zawiera szczegóły dotyczące kontaktu zabezpieczeń dla subskrypcji platformy Azure firmy Contoso, kontaktów programu Microsoft firmy Contoso, jeśli wykryje, gdy dane klienta firmy Contoso uzyskaniu dostępu do strony bezprawnego lub nieautoryzowane. Teraz należy wykonać Jeff dotyczy on **podać szczegóły dotyczące kontaktu zabezpieczeń** zalecenie (zalecenie o ważności nośnika na liście powyższe zalecenia).

1. Jan wybiera **podać szczegóły dotyczące kontaktu zabezpieczeń** w obszarze **zalecenia**, co spowoduje otwarcie **podać szczegóły dotyczące kontaktu zabezpieczeń**.
2. Jan wybiera subskrypcji platformy Azure, aby udostępnić informacje kontaktowe na. Drugi **podać szczegóły dotyczące kontaktu zabezpieczeń** zostanie otwarty blok.
   ![Szczegóły dotyczące kontaktu zabezpieczeń][6]
3. W obszarze **podać szczegóły dotyczące kontaktu zabezpieczeń**, Jeff wejścia:

  - adresy e-mail kontaktu zabezpieczeń rozdzielonych przecinkami (nie istnieje ograniczona liczba adresów e-mail, które może wprowadzić)
  - numer telefonu skontaktuj się z jednego zabezpieczeń

4. Jan włącza również opcję **Wyślij do mnie wiadomość e-mail o alertach** do odbierania wiadomości e-mail o alertach o wysokim znaczeniu.
5. Jan wybiera **OK** dotyczyć informacji kontaktowych zabezpieczeń subskrypcji firmy Contoso.

Na koniec Jan przegląda zalecenie o niskim priorytecie **luk w zabezpieczeniach skorygować OS** i określa, że nie ma zastosowania tego zalecenia. Chce odrzucić zalecenia. Jan wybiera z wielokropkiem po prawej stronie, a następnie wybiera **odrzucenia**.
   ![Odrzuć zalecenia][7]

## <a name="conclusion"></a>Podsumowanie
Monitorowanie zalecenia w Centrum zabezpieczeń może pomóc wyeliminować luk w zabezpieczeniach, zanim wystąpi atak. Zdarzenia zabezpieczeń można zapobiec przez wykonanie i utrzymywanie ochrony przy użyciu zasad zabezpieczeń w Centrum zabezpieczeń.

## <a name="next-steps"></a>Następne kroki
W tym scenariuszu pokazano, jak używać zasad zabezpieczeń i zalecenia w Centrum zabezpieczeń do zmniejszenia atak na zabezpieczenia. Zobacz [scenariusza odpowiedzi na zdarzenia](security-center-incident-response.md) informacje na temat ma planu przed dokonaniem atak odpowiedzi na zdarzenia.

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz:

* [Monitorowanie kondycji zabezpieczeń](security-center-monitoring.md) — informacje o sposobie monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi](security-center-managing-and-responding-alerts.md) — Dowiedz się, jak reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie i przetwarzania zdarzeń zabezpieczeń](security-center-events-dashboard.md) — informacje o monitorowaniu i przetwarzania zdarzeń zabezpieczeń zbierane wraz z upływem czasu.
* [Monitorowanie rozwiązań partnerskich](security-center-partner-solutions.md) — informacje o sposobie monitorowania stanu kondycji rozwiązań partnerskich.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) — Uzyskaj najnowsze informacje zabezpieczeń platformy Azure i informacje.

<!--Image references-->
[1]: ./media/security-center-using-recommendations/security-center-policy-inheritance.png
[2]: ./media/security-center-using-recommendations/scenario-roles.png
[3]: ./media/security-center-using-recommendations/select-recommendations-tile.png
[4]: ./media/security-center-using-recommendations/install-endpoint-protection.png
[5]:./media/security-center-using-recommendations/microsoft-antimalware.png
[6]: ./media/security-center-using-recommendations/provide-security-contact-details.png
[7]: ./media/security-center-using-recommendations/dismiss-recommendation.png
