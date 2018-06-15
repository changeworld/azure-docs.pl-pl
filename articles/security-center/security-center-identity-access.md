---
title: Monitorowanie tożsamości i dostępu w usłudze Azure Security Center | Microsoft Docs
description: Dowiedz się, jak korzystać z funkcji zarządzania tożsamościami i dostępem w usłudze Azure Security Center w celu monitorowania dostępu użytkowników i rozwiązywania problemów związanych z tożsamościami.
services: security-center
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: ''
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: yurid
ms.openlocfilehash: 5ee263ef8fb0f20049215eda53e0d58a45342b7e
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
ms.locfileid: "32774832"
---
# <a name="monitor-identity-and-access-in-azure-security-center"></a>Monitorowanie tożsamości i dostępu w usłudze Azure Security Center
Ten artykuł ułatwia korzystanie z usługi Azure Security Center w celu monitorowania tożsamości i dostępu użytkowników.

## <a name="why-monitor-identity-and-access"></a>Dlaczego warto monitorować tożsamość i dostęp?
Tożsamość powinna być warstwą kontroli w Twoim przedsiębiorstwie, a ochrona tożsamości powinna stanowić najwyższy priorytet. W przeszłości istniały strefy wokół organizacji i te strefy były jedną z podstawowych granic obrony. Dzisiaj, w związku z przeniesieniem większej ilości danych i aplikacji do chmury, tożsamość staje się nową strefą.

Dzięki monitorowaniu działań związanych z tożsamością możesz podejmować prewencyjne działania przed wystąpieniem zdarzenia i reagować na próby ataku. Pulpit nawigacyjny tożsamości i dostępu udostępnia przegląd stanu tożsamości, a w tym:

* Liczbę nieudanych prób logowania. 
* Konta użytkowników, które były używane podczas tych prób.
* Konta, które zostały zablokowane.
* Konta ze zmienionymi lub zresetowanymi hasłami. 
* Bieżącą liczbę zalogowanych kont.

## <a name="monitor-identity-and-access-activities"></a>Monitorowanie działań związanych z tożsamością i dostępem
Aby wyświetlić bieżące działania związane z tożsamościami i dostępem, musisz uzyskać dostęp do pulpitu nawigacyjnego **Tożsamość i dostęp**.

1. Otwórz pulpit nawigacyjny usługi **Security Center**.

2. W lewym okienku w obszarze **Zapobieganie** wybierz pozycję **Tożsamość i dostęp**. Jeśli masz wiele obszarów roboczych, zostanie wyświetlony selektor obszarów roboczych.

    ![Wybór obszaru roboczego](./media/security-center-identity-access\security-center-identity-access-fig1.png)

    > [!NOTE]
    > Jeśli skrajna prawa kolumna zawiera napis **PLAN UAKTUALNIENIA**, ten obszar roboczy używa bezpłatnej subskrypcji. Zmień subskrypcję na standardową, aby użyć tej funkcji. Jeśli skrajna prawa kolumna zawiera napis **WYMAGA AKTUALIZACJI**, zaktualizuj usługę [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview), aby użyć tej funkcji. Więcej informacji na temat planu cenowego zawiera cennik usługi Security Center. 
    > 
3. Jeśli masz więcej niż jeden obszar roboczy do sprawdzenia, możesz określić priorytet badania zgodnie z kolumną **NIEUDANE LOGOWANIA**. Przedstawia ona bieżącą liczbę nieudanych prób logowania w tym obszarze roboczym. Po wybraniu odpowiedniego obszaru roboczego pojawi się pulpit nawigacyjny **Tożsamość i dostęp**.

    ![Tożsamość i dostęp](./media/security-center-identity-access\security-center-identity-access-fig2.png)

4. Informacje zawarte na tym pulpicie nawigacyjnym mogą bezpośrednio pomóc w identyfikacji potencjalnie podejrzanych działań. Ten pulpit nawigacyjny dzieli się na trzy główne obszary:

    a. **Stan tożsamości**. Zawiera podsumowanie działań dotyczących tożsamości, które mają miejsce w danym obszarze roboczym.

    b. **Nieudane logowania**. Pomaga szybko określić główne przyczyny nieudanych prób logowania. Przedstawia listę 10 głównych kont, dla których nie powiodła się większość prób logowania.

    d. **Logowania w czasie**. Pomaga szybko zidentyfikować liczbę logowań w dłuższym czasie. Jest wyświetlana lista najważniejszych prób logowania do konta komputera.
    
Niezależnie od tego, który kafelek wybierzesz, wyświetlany pulpit nawigacyjny jest oparty na zapytaniu przeszukiwania dzienników. Jedyną różnicą jest typ zapytania i wynik. Nadal możesz wybrać element, na przykład komputer, i wyświetlić odpowiednie dane. 

## <a name="see-also"></a>Zobacz też
W tym artykule przedstawiono sposób monitorowania tożsamości i dostępu w usłudze Security Center. Aby dowiedzieć się więcej na temat usługi Security Center, zobacz następujące artykuły:

* [Zarządzanie alertami zabezpieczeń i reagowanie na nie w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Dowiedz się, jak zarządzać alertami i reagować na zdarzenia związane z bezpieczeństwem w usłudze Security Center.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md). Informacje na temat sposobu monitorowania kondycji zasobów platformy Azure.
* [Informacje o alertach zabezpieczeń w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Poznaj różne typy alertów zabezpieczeń.
* [Przewodnik rozwiązywania problemów z usługą Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Dowiedz się, jak rozwiązywać typowe problemy z usługą Security Center. 
* [Azure Security Center — często zadawane pytania](security-center-faq.md). Odpowiedzi na często zadawane pytania dotyczące korzystania z usługi Security Center.
* [Blog Azure Security](http://blogs.msdn.com/b/azuresecurity/). Wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.

