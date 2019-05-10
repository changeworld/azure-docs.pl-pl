---
title: Zwiększanie zgodności z przepisami przy użyciu usługi Azure Security Center | Microsoft Docs
description: 'Samouczek: Informacje na temat zwiększania zgodności z przepisami przy użyciu usługi Azure Security Center.'
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 5f50c4dc-ea42-418d-9ea8-158ffeb93706
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/30/2019
ms.author: monhaber
ms.openlocfilehash: 111a32983472d18c1435feaf2456e5b9416b5112
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65442306"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Samouczek: Zwiększanie zgodności z przepisami
---

Usługa Azure Security Center usprawnia proces spełniania wymagań dotyczących zgodności z przepisami dzięki możliwości korzystania z pulpitu nawigacyjnego Zgodność z przepisami. Ten pulpit nawigacyjny usługi Security Center zapewnia wgląd w poziom zgodności w oparciu o ciągłe oceny środowiska platformy Azure. Oceny wykonywane przez usługę Security Center analizują czynniki ryzyka w środowisku chmury hybrydowej zgodnie z najlepszymi rozwiązaniami w zakresie zabezpieczeń. Te oceny są mapowane na kontrolki zgodności z obsługiwanego zestawu standardów. Pulpit nawigacyjny Zgodność z przepisami zapewnia przejrzysty widok stanu wszystkich tych ocen w Twoim środowisku w kontekście określonego standardu lub przepisu. W miarę podejmowania działań zgodnych z rekomendacjami i ograniczania czynników ryzyka w Twoim środowisku możesz obserwować poprawę poziomu zgodności.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

-   Przeprowadzanie oceny zgodności z przepisami za pomocą pulpitu nawigacyjnego Zgodność z przepisami

-   Zwiększanie poziomu zgodności przez podejmowanie działań zgodnych z rekomendacjami

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wypróbować funkcje omówione w tym samouczku, musisz korzystać ze standardowej warstwy cenowej usługi Security Center. Warstwę Standardowa usługi Security Center możesz wypróbować bezpłatnie.
Aby dowiedzieć się więcej, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/security-center/). Przewodnik szybkiego startu [Dołączanie subskrypcji platformy Azure do standardowej usługi Security Center](https://docs.microsoft.com/azure/security-center/security-center-get-started) przeprowadzi Cię przez procedurę uaktualniania do warstwy standardowej.

##  <a name="assess-your-regulatory-compliance"></a>Przeprowadzanie oceny zgodności z przepisami

Usługa Security Center w sposób ciągły ocenia konfigurację zasobów, aby identyfikować problemy dotyczące zabezpieczeń i luki w zabezpieczeniach. Oceny są prezentowane w postaci rekomendacji, które koncentrują się na poprawie stanu zabezpieczeń. Na pulpicie nawigacyjnym Zgodność z przepisami możesz wyświetlić zestaw standardów zgodności wraz ze wszystkimi wymaganiami. Obsługiwane wymagania są mapowane na odpowiednie oceny zabezpieczeń. Pozwala to sprawdzać poziom zgodności względem standardu w oparciu o stan tych ocen.

Widok pulpitu nawigacyjnego Zgodność z przepisami ułatwia skoncentrowanie uwagi na lukach w zgodności z ważnym dla Ciebie standardem lub przepisem. Ten skoncentrowany widok umożliwia także stałe monitorowanie oceny zgodności w miarę upływu czasu w chmurze dynamicznej i środowiskach hybrydowych.

>[!NOTE]
> Obecnie obsługiwane standardy prawne to: Azure CIS, PCI DSS 3.2, ISO 27001 i SOC TSP. Dodatkowe standardy będą odzwierciedlane na pulpicie nawigacyjnym w miarę ich rozwijania.
1.  W usłudze Security Center menu głównego w obszarze **zasady i zgodność** wybierz **zgodność z przepisami**. <br>
W górnej części ekranu zostanie wyświetlony pulpit nawigacyjny zawierający przegląd stanu zgodności z zestawem obsługiwanych przepisów dotyczących zgodności. Widoczna jest ogólna ocena zgodności oraz liczba ocen zakończonych powodzeniem i niepowodzeniem skojarzonych z każdym standardem.

    ![Opis komputera — godny zaufania](./media/security-center-compliance-dashboard/compliance-dashboard.png)


2.  Wybierz kartę standardu zgodności, który Cię dotyczy. Zostanie wyświetlona lista wszystkich kontrolek dla tego standardu. W przypadku właściwych kontrolek możesz wyświetlić szczegóły ocen zakończonych powodzeniem i niepowodzeniem skojarzonych z daną kontrolką. Niektóre kontrolki są wyszarzone. Z tymi kontrolkami nie są skojarzone żadne oceny usługi Security Center. Musisz przeanalizować dotyczące ich wymagania i oceniać je w Twoim środowisku samodzielnie. Niektóre z nich mogą być powiązane z procesem, a nie techniczne.

    ![Karta zgodności](./media/security-center-compliance-dashboard/compliance-pci.png)

3. Wybierz kartę **Wszystkie**, aby wyświetlić widok wszystkich odpowiednich rekomendacji usługi Security Center i skojarzonych z nimi standardów. Ten widok może ułatwić zidentyfikowanie wszystkich standardów, których dotyczy określona rekomendacja. <br> Korzystając z tego widoku, możesz również nadawać priorytety rekomendacjom, które trzeba wprowadzić w życie. Na przykład jeśli widzisz, że zalecenie **Włącz uwierzytelnianie wieloskładnikowe dla kont z uprawnieniami właściciela w Twojej subskrypcji** kończy się niepowodzeniem na wielu zasobach i jest skojarzone z wieloma standardami, wprowadzenie tej rekomendacji będzie mieć bardzo duży wpływ na ogólną ocenę zgodności.

    ![Wpływ na ocenę zgodności](./media/security-center-compliance-dashboard/compliance-all-tabs.png)

1. Kliknij, aby wygenerować i pobrać raportu w formacie PDF podsumowywanie bieżący stan zgodności dla określonej warstwy standardowa **Pobierz raport**.

    Raport zawiera podsumowanie wysokiego poziomu usługi stanu zgodności dla wybranej warstwy standardowa, na podstawie danych ocen w usłudze Security Center i są zorganizowane według kontrolki tego konkretnego standard. Raport, mogą być współużytkowane z właściwym uczestnikom projektu i może obsługiwać dostarczanie dowodu do wewnętrznych i zewnętrznych audytorów.

    ![pobierz](./media/security-center-compliance-dashboard/download-report.png)

## <a name="improve-your-compliance-posture"></a>Zwiększanie poziomu zgodności

Dzięki informacjom wyświetlanym na pulpicie nawigacyjnym Zgodność z przepisami możesz zwiększyć poziom zgodności, wprowadzając w życie te rekomendacje bezpośrednio z poziomu pulpitu nawigacyjnego.

1.  Kliknij dowolną ocenę zakończoną niepowodzeniem, która jest widoczna na pulpicie nawigacyjnym, aby wyświetlić szczegóły dotyczące tej rekomendacji. Każda rekomendacja obejmuje zestaw czynności zaradczych, które należy wykonać, aby rozwiązać problem.

2.  Możesz wybrać określony zasób, aby wyświetlić więcej szczegółów i zastosować rekomendację dotyczącą tego zasobu. <br>Na przykład na karcie **Standard Azure CIS** możesz kliknąć rekomendację **Wymagaj bezpiecznego transferu na konto magazynu**.

    ![Rekomendacje dotyczące zgodności](./media/security-center-compliance-dashboard/compliance-recommendation.png)

3. Klikanie informacji dotyczących rekomendacji i wybieranie zasobów w złej kondycji prowadzi bezpośrednio do środowiska włączania **bezpiecznego transferu magazynu** w witrynie Azure Portal.<br>Więcej informacji dotyczących stosowania rekomendacji można znaleźć w temacie [Wdrażanie zaleceń dotyczących zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md).

    ![Rekomendacje dotyczące zgodności](./media/security-center-compliance-dashboard/compliance-remediate-recommendation.png)

4.  Gdy zastosujesz się do rekomendacji, w raporcie pulpitu nawigacyjnego zgodności zostanie wyświetlony ich wpływ, ponieważ zwiększy się ocena zgodności.

    > [!NOTE]
    > Oceny są przeprowadzane mniej więcej co 12 godzin, dlatego wpływ na dane dotyczące zgodności zobaczysz dopiero po uruchomieniu oceny.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku omówiono używanie pulpitu nawigacyjnego Zgodność z przepisami usługi Security Center do wykonywania następujących czynności:

-   Wyświetlanie i monitorowanie poziomu zgodności względem istotnych dla Ciebie standardów i przepisów.

-   Zwiększanie stanu zgodności przez wprowadzanie w życie odpowiednich rekomendacji i obserwowanie poprawy oceny zgodności.

Pulpit nawigacyjny Zgodność z przepisami może znacznie uprościć proces zapewniania zgodności i zauważalnie skrócić czas wymagany do uzyskania dowodu zgodności dla platformy Azure i środowiska hybrydowego.

Aby dowiedzieć się więcej na temat usługi Security Center, zobacz:

-   [Monitorowanie kondycji zabezpieczeń w Centrum zabezpieczeń Azure](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.

-   [Zarządzanie rekomendacjami dotyczącymi zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md) — informacje o tym, jak korzystając z rekomendacji w usłudze Azure Security Center, ułatwić sobie ochronę zasobów platformy Azure.

-   [Zwiększanie oceny zabezpieczeń w usłudze Azure Security Center](security-center-secure-score.md) — informacje o tym, jak określać priorytety luk w zabezpieczeniach i rekomendacji dotyczących zabezpieczeń, aby jak najbardziej zwiększyć poziom bezpieczeństwa.

-   [Centrum zabezpieczeń Azure — często zadawane pytania](security-center-faq.md) — odpowiedzi na najczęstsze pytania dotyczące korzystania z usługi.
