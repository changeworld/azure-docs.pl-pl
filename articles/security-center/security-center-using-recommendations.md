---
title: Usługa Azure Security Center zaleceń, aby zwiększyć poziom bezpieczeństwa | Dokumentacja firmy Microsoft
description: " Dowiedz się, jak używać zasad zabezpieczeń i zalecenia w usłudze Azure Security Center aby ułatwić uniknięcie atak na zabezpieczenia. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: terrylan
ms.openlocfilehash: 978ef8087665edb3f0bfaa0bd0a55f4d8ab9a12d
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51251512"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Usługa Azure Security Center zaleceń, aby zwiększyć poziom bezpieczeństwa
Konfigurowanie zasad zabezpieczeń, a następnie wdrażanie zaleceń dotyczących, dostarczone przez usługę Azure Security Center może zmniejszyć prawdopodobieństwo zdarzeń zabezpieczeń. W tym artykule przedstawiono sposób używania zasad zabezpieczeń i zalecenia w usłudze Security Center, aby ułatwić uniknięcie atak na zabezpieczenia.

> [!NOTE]
> W tym artykule jest oparta na rolach i pojęć wprowadzonych w usłudze Security Center [przewodnik planowania i obsługi](security-center-planning-and-operations-guide.md). To dobry pomysł, aby przejrzeć przewodnik planowania, przed kontynuowaniem.
>
>

## <a name="managing-security-recommendations"></a>Zarządzanie zaleceniami dotyczącymi zabezpieczeń
Zasady zabezpieczeń określają zestaw mechanizmów kontrolnych, które są zalecane dla zasobów w ramach określonej subskrypcji lub grupy zasobów. W usłudze Security Center można zdefiniować zasady zgodnie z wymaganiami bezpieczeństwa w firmie. Aby dowiedzieć się więcej, zobacz [Ustawianie zasad zabezpieczeń w usłudze Security Center](security-center-policies.md).

Zasady zabezpieczeń dla grupy zasobów są dziedziczone z poziomu subskrypcji.

![Dziedziczenie zasad zabezpieczeń][1]

Jeśli potrzebujesz niestandardowych zasad w określonych grupach zasobów, można wyłączyć dziedziczenia w grupie zasobów. Aby wyłączyć, ustaw dziedziczenie unikatowe w bloku zasad zabezpieczeń i dostosować formanty, które usługa Security Center zawiera zalecenia dotyczące.

Na przykład w przypadku obciążeń, które nie wymagają zasad SQL Database przezroczystego szyfrowania danych (TDE), Wyłącz zasady na poziomie subskrypcji i ją włączyć tylko w grupach zasobów, których funkcji SQL TDE jest wymagany.

> [!NOTE]
> Jeśli między zasadami poziomu subskrypcji i zasadami poziomu grupy zasobów występuje konflikt, pierwszeństwo ma poziom zasad grupy zasobów.
>
>

Usługa Security Center analizuje stan zabezpieczeń zasobów platformy Azure. Gdy usługa Security Center zidentyfikuje potencjalnych luk w zabezpieczeniach, tworzy zalecenia w zależności od kontrolek w zasadach zabezpieczeń. Zaleceń prowadzi przez proces konfigurowania kontroli zabezpieczeń potrzebne.

Bieżący zaleceń dotyczących zasad w Centrum zabezpieczeń trybie koncentracji uwagi na aktualizacje systemu, konfiguracja systemu operacyjnego, sieciowe grupy zabezpieczeń w podsieci i maszyn wirtualnych (VM), inspekcja bazy danych SQL TDE bazy danych SQL, oraz zapory aplikacji sieci web. Aby uzyskać najbardziej aktualne pokrycia zaleceń usługi Security Center, zobacz [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w usłudze Security Center](security-center-recommendations.md).

## <a name="scenario"></a>Scenariusz
W tym scenariuszu pokazano, jak zmniejszają prawdopodobieństwo zdarzenia zabezpieczeń, monitorując zaleceń usługi Security Center i podjęcia działań za pomocą usługi Security Center. Scenariuszu fikcyjnej firmy, Contoso i ról znajdujące się w Centrum zabezpieczeń [przewodnik planowania i obsługi](security-center-planning-and-operations-guide.md#security-roles-and-access-controls). Role reprezentują osób oraz zespołów, które mogą korzystać z usługi Security Center do wykonywania różnych zadań związanych z zabezpieczeniami. Dostępne są następujące role:

![Scenariusz ról][2]

Contoso ostatnio migracji niektórych swoich zasobów lokalnych na platformę Azure. Firma Contoso chce wdrażanie i obsługę ochrony, które zmniejszają ich podatności na atak na zabezpieczenia swoich zasobów w chmurze.

## <a name="recommended-solution"></a>Zalecane rozwiązanie
To rozwiązanie jest usługa Security Center umożliwia zapobieganie i Wykrywanie luk w zabezpieczeniach. Firma Contoso ma dostęp do usługi Security Center za pośrednictwem swojej subskrypcji platformy Azure. [w warstwie bezpłatna](security-center-pricing.md) z usługi Security Center jest automatycznie włączona na wszystkich subskrypcji platformy Azure i zbieranie danych jest włączone na wszystkich maszynach wirtualnych w ramach swojej subskrypcji.

David działu zabezpieczeń IT firmy Contoso, konfiguruje **zasady zabezpieczeń** przy użyciu usługi Security Center. Usługa Security Center analizuje stan zabezpieczeń zasobów platformy Azure z firmy Contoso. Gdy usługa Security Center zidentyfikuje potencjalnych luk w zabezpieczeniach, tworzy **zalecenia** oparte na kontrolki, w ramach zasad zabezpieczeń.

Jeff, właściciel obciążenia chmury, jest odpowiedzialny za zaimplementowanie i utrzymanie ochrony zgodnie z zasadami zabezpieczeń firmy Contoso. Jeff może monitorować zalecenia utworzone przez usługę Security Center, aby zastosować ochronę. Przewodnik dotyczący zaleceń prowadzi Jeff przez proces konfigurowania kontroli zabezpieczeń potrzebne.

Aby Jan do wdrożenia i utrzymują zabezpieczenia i eliminowanie luk w zabezpieczeniach co potrzeba, aby:

- Zalecenia dotyczące zabezpieczeń monitorowania dostarczane przez usługę Security Center
- Oceń zalecenia dotyczące zabezpieczeń i zdecydować, jeśli on powinien Zastosuj lub Odrzuć
- Stosowanie zalecenia dotyczące zabezpieczeń

Spróbujmy wykonaj kroki Jeffa, aby zobaczyć, jak zaleceń usługi Security Center używa mu prowadzące przez proces konfigurowania kontrolek w celu wyeliminowania luki w zabezpieczeniach.

## <a name="how-to-implement-this-solution"></a>Sposób implementacji rozwiązania
Jeff loguje się do [witryny Azure portal](https://azure.microsoft.com/features/azure-portal/) i otwiera konsolę usługi Security Center. W ramach jego codziennych działań monitorowania ADAM sprawdza, czy zalecenia dotyczące zabezpieczeń, wykonując następujące czynności:

1. Jan wybiera **zalecenia** Kafelek, aby otworzyć **zalecenia**.
   ![Wybierz Kafelek zalecenia][3]
2. Jan przegląda listę zaleceń. Zauważył, że usługa Security Center udostępnił lista zaleceń opisanych w kolejności priorytetów utworzona z najwyższym priorytetem do najniższego priorytetu. Postanawia zastosowania zalecenia o wysokim priorytecie, na liście. Wybiera **Zainstaluj program Endpoint Protection** w obszarze **zalecenia**.
3. **Zainstaluj program Endpoint Protection** otwiera, wyświetlanie listy maszyn wirtualnych bez ochrony przed złośliwym kodem jest włączona. Jeff przeglądy listy maszyn wirtualnych, wybiera wszystkie maszyny wirtualne, a następnie wybiera **zainstalować na maszynach wirtualnych 3**.
   ![Instalowanie ochrony punktu końcowego][4]
4. **Wybór ochrony punktu końcowego** otwiera, dostarczając Jeff dwa rozwiązania w zakresie ochrony przed złośliwym oprogramowaniem. Jan wybiera **Microsoft Antimalware** rozwiązania.
5. Dodatkowe informacje na temat rozwiązania chroniące przed złośliwym kodem jest wyświetlany. Jan wybiera **Utwórz**.
   ![Firmy Microsoft chroniąca przed złośliwym kodem][5]
6. Jeff wprowadza wymaganych ustawień konfiguracji w ramach **zainstalować** i wybiera **OK**.

[Microsoft Antimalware](../security/azure-security-antimalware.md) jest teraz aktywna dla wybranych maszyn wirtualnych.

Jeff w dalszym ciągu poruszania się o wysokim priorytecie i zalecenia średni priorytet, podejmowania decyzji o implementacji. Jeff odwołuje się do [Zarządzanie zaleceniami dotyczącymi zabezpieczeń](security-center-recommendations.md) artykuł, aby zrozumieć, zalecenia i każdy z nich działanie, jeśli ma on zastosowanie.

Jeff uczy się, że [Microsoft Security Response Center (MSRC)](../security/azure-security-response-center.md) wykonuje monitorowania zabezpieczeń wybierz sieć platformy Azure i infrastrukturą i odbiera skarg analizy i nadużyć zagrożenia pochodzące od innych firm. Jeśli Jeff zawiera szczegóły dotyczące kontaktu zabezpieczeń dla firmy Contoso subskrypcji platformy Azure, Microsoft kontaktów firmy Contoso, jeśli centrum MSRC umożliwia odnalezienie danych klientów firmy Contoso ma prawem uzyskały dostęp do nieupoważnione lub. Teraz Jeff postępuj zgodnie z ma on zastosowanie **podawanie szczegółów dotyczących kontaktu zabezpieczeń** zalecenie (zalecenia o ważności nośnika powyższe zalecenia na liście).

1. Jan wybiera **podawanie szczegółów dotyczących kontaktu zabezpieczeń** w obszarze **zalecenia**, co spowoduje otwarcie **podawanie szczegółów dotyczących kontaktu zabezpieczeń**.
2. Jan wybiera subskrypcji platformy Azure, podaj informacje kontaktowe na. Sekundy **podawanie szczegółów dotyczących kontaktu zabezpieczeń** zostanie otwarty blok.
   ![Szczegóły dotyczące kontaktu zabezpieczeń][6]
3. W obszarze **podawanie szczegółów dotyczących kontaktu zabezpieczeń**, wprowadza Jeff:

  - adresy kontaktowy adres e-mail zabezpieczeń, które są rozdzielane przecinkami (nie ma limitu liczby adresów e-mail, które może wprowadzić)
  - numer telefonu kontaktowego zabezpieczeniami

4. Jeff również włącza opcję **Wyślij do mnie wiadomości e-mail o alertach** do odbierania wiadomości e-mail o alertach o wysokiej ważności.
5. Jan wybiera **OK** dotyczą informacje kontaktowe zabezpieczeń subskrypcji firmy Contoso.

Na koniec Jan przegląda zalecenia o niskim priorytecie **OS korygowanie luk w zabezpieczeniach** i określa, czy to zalecenie nie dotyczy. Chce odrzucić zalecenia. Jan wybiera trzy kropki, które pojawiają się po prawej stronie, a następnie wybiera **Odrzuć**.
   ![Odrzuć zalecenia][7]

## <a name="conclusion"></a>Podsumowanie
Monitorowanie zalecenia w usłudze Security Center mogą pomóc wyeliminować luk w zabezpieczeniach, zanim wystąpi atak. Zdarzenie naruszenia zabezpieczeń można zapobiec przez zaimplementowanie i utrzymanie ochrony za pomocą zasad zabezpieczeń w usłudze Security Center.

## <a name="next-steps"></a>Kolejne kroki
W tym scenariuszu pokazano sposób używania zasad zabezpieczeń i zalecenia w usłudze Security Center, aby ułatwić uniknięcie atak na zabezpieczenia. Zobacz [scenariuszach reagowania na zdarzenia](security-center-incident-response.md) Aby dowiedzieć się, jak planem przed wystąpieniem ataku reagowania na zdarzenia.

Aby dowiedzieć się więcej o usłudze Security Center, zobacz:

* [Monitorowanie kondycji zabezpieczeń](security-center-monitoring.md) — informacje o sposobie monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi](security-center-managing-and-responding-alerts.md) — Dowiedz się, jak zarządzać i reagować na alerty zabezpieczeń.
* [Monitorowanie i przetwarzania zdarzeń związanych z zabezpieczeniami](security-center-events-dashboard.md) — Dowiedz się, jak monitorować i przetwarzania zdarzeń zabezpieczeń są zbierane wraz z upływem czasu.
* [Monitorowanie rozwiązań partnerskich](security-center-partner-solutions.md) — informacje o sposobie monitorowania stanu kondycji rozwiązań partnerskich.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) — Uzyskaj najnowsze informacje o zabezpieczeniach platformy Azure i informacji.

<!--Image references-->
[1]: ./media/security-center-using-recommendations/security-center-policy-inheritance.png
[2]: ./media/security-center-using-recommendations/scenario-roles.png
[3]: ./media/security-center-using-recommendations/select-recommendations-tile.png
[4]: ./media/security-center-using-recommendations/install-endpoint-protection.png
[5]:./media/security-center-using-recommendations/microsoft-antimalware.png
[6]: ./media/security-center-using-recommendations/provide-security-contact-details.png
[7]: ./media/security-center-using-recommendations/dismiss-recommendation.png
