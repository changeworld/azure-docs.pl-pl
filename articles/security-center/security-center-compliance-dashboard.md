---
title: 'Samouczek: Sprawdzanie zgodności z przepisami — Usługa Azure Security Center'
description: 'Samouczek: Dowiedz się, jak poprawić zgodność z przepisami za pomocą usługi Azure Security Center.'
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5f50c4dc-ea42-418d-9ea8-158ffeb93706
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2019
ms.author: memildin
ms.openlocfilehash: 1a6999c05c0b3dbaf572b376412f666c50c23df7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77604457"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Samouczek: Poprawa zgodności z przepisami
---

Usługa Azure Security Center pomaga usprawnić proces spełniania wymagań dotyczących zgodności z przepisami przy użyciu **pulpitu nawigacyjnego zgodności z przepisami.** Ten pulpit nawigacyjny usługi Security Center zapewnia wgląd w poziom zgodności w oparciu o ciągłe oceny środowiska platformy Azure. Usługa Security Center analizuje czynniki ryzyka w środowisku chmury hybrydowej zgodnie z najlepszymi rozwiązaniami w zakresie zabezpieczeń. Te oceny są mapowane na kontrolki zgodności z obsługiwanego zestawu standardów. Na pulpicie nawigacyjnym zgodności z przepisami można zobaczyć stan wszystkich ocen w danym środowisku w kontekście określonego standardu lub rozporządzenia. Gdy działasz zgodnie z zaleceniami i zmniejszasz czynniki ryzyka w swoim środowisku, poprawia się postawa zgodności.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

-   Przeprowadzanie oceny zgodności z przepisami za pomocą pulpitu nawigacyjnego Zgodność z przepisami

-   Zwiększanie poziomu zgodności przez podejmowanie działań zgodnych z rekomendacjami

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wypróbować funkcje omówione w tym samouczku, musisz korzystać ze standardowej warstwy cenowej usługi Security Center. Warstwę Standardowa usługi Security Center możesz wypróbować bezpłatnie.
Aby dowiedzieć się więcej, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/security-center/). Przewodnik szybkiego startu [Dołączanie subskrypcji platformy Azure do standardowej usługi Security Center](https://docs.microsoft.com/azure/security-center/security-center-get-started) przeprowadzi Cię przez procedurę uaktualniania do warstwy standardowej.

##  <a name="assess-your-regulatory-compliance"></a>Przeprowadzanie oceny zgodności z przepisami

Usługa Security Center w sposób ciągły ocenia konfigurację zasobów, aby identyfikować problemy dotyczące zabezpieczeń i luki w zabezpieczeniach. Oceny są prezentowane w postaci rekomendacji, które koncentrują się na poprawie stanu zabezpieczeń. Na pulpicie nawigacyjnym Zgodność z przepisami możesz wyświetlić zestaw standardów zgodności wraz ze wszystkimi wymaganiami. Obsługiwane wymagania są mapowane na odpowiednie oceny zabezpieczeń. Pozwala to sprawdzać poziom zgodności względem standardu w oparciu o stan tych ocen.

Widok pulpitu nawigacyjnego Zgodność z przepisami ułatwia skoncentrowanie uwagi na lukach w zgodności z ważnym dla Ciebie standardem lub przepisem. Ten skoncentrowany widok umożliwia także stałe monitorowanie oceny zgodności w miarę upływu czasu w chmurze dynamicznej i środowiskach hybrydowych.

>[!NOTE]
> Domyślnie usługa Security Center obsługuje następujące standardy regulacyjne: Azure CIS, PCI DSS 3.2, ISO 27001 i SOC TSP. 
>
> Funkcja [dynamicznych pakietów zgodności (wersja zapoznawcza)](update-regulatory-compliance-packages.md) umożliwia uaktualnienie standardów wyświetlanych na pulpicie nawigacyjnym zgodności z przepisami do nowych pakietów *dynamicznych.* Można również użyć tej samej funkcji podglądu, aby dodać nowe pakiety zgodności i monitorować zgodność z dodatkowymi standardami. 

1.  W menu głównym Centrum zabezpieczeń w obszarze **ZASADY & ZGODNOŚĆ** wybierz pozycję Zgodność z **przepisami**. <br>
W górnej części ekranu zostanie wyświetlony pulpit nawigacyjny zawierający przegląd stanu zgodności z zestawem obsługiwanych przepisów dotyczących zgodności. Widoczna jest ogólna ocena zgodności oraz liczba ocen zakończonych powodzeniem i niepowodzeniem skojarzonych z każdym standardem.

    ![Opis komputera — godny zaufania](./media/security-center-compliance-dashboard/compliance-dashboard.png)

2.  Wybierz kartę standardu zgodności, który Cię dotyczy. Zostanie wyświetlona lista wszystkich kontrolek dla tego standardu. W przypadku właściwych kontrolek możesz wyświetlić szczegóły ocen zakończonych powodzeniem i niepowodzeniem skojarzonych z daną kontrolką. Niektóre formanty są wyszarzone. Te formanty nie mają żadnych ocen usługi Security Center skojarzonych z nimi. Sprawdź wymagania dla nich i ocenić je w swoim środowisku na własną rękę. Niektóre z nich mogą być powiązane z procesem, a nie techniczne.

    ![Karta zgodności](./media/security-center-compliance-dashboard/compliance-pci.png)

1. Aby wygenerować i pobrać raport PDF podsumowujący bieżący stan zgodności dla określonego standardu, kliknij **przycisk Pobierz raport**.

    Raport zawiera podsumowanie wysokiego poziomu stanu zgodności dla wybranego standardu na podstawie danych oceny usługi Security Center i jest zorganizowany zgodnie z formantami tego konkretnego standardu. Sprawozdanie może być udostępniane odpowiednim zainteresowanym stronom i może służyć do dostarczania dowodów audytorom wewnętrznym i zewnętrznym.

    ![pobieranie](./media/security-center-compliance-dashboard/download-report.png)

## <a name="improve-your-compliance-posture"></a>Zwiększanie poziomu zgodności

Dzięki informacjom wyświetlanym na pulpicie nawigacyjnym Zgodność z przepisami możesz zwiększyć poziom zgodności, wprowadzając w życie te rekomendacje bezpośrednio z poziomu pulpitu nawigacyjnego.

1.  Kliknij dowolną ocenę zakończoną niepowodzeniem, która jest widoczna na pulpicie nawigacyjnym, aby wyświetlić szczegóły dotyczące tej rekomendacji. Każda rekomendacja obejmuje zestaw czynności zaradczych, które należy wykonać, aby rozwiązać problem.

1.  Możesz wybrać określony zasób, aby wyświetlić więcej szczegółów i zastosować rekomendację dotyczącą tego zasobu. <br>Na przykład na karcie **Standard Azure CIS** możesz kliknąć rekomendację **Wymagaj bezpiecznego transferu na konto magazynu**.

    ![Rekomendacje dotyczące zgodności](./media/security-center-compliance-dashboard/compliance-recommendation.png)

1. Klikanie informacji dotyczących rekomendacji i wybieranie zasobów w złej kondycji prowadzi bezpośrednio do środowiska włączania **bezpiecznego transferu magazynu** w witrynie Azure Portal.

    Więcej informacji dotyczących stosowania rekomendacji można znaleźć w temacie [Wdrażanie zaleceń dotyczących zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md).

    ![Rekomendacje dotyczące zgodności](./media/security-center-compliance-dashboard/compliance-remediate-recommendation.png)

1.  Gdy zastosujesz się do rekomendacji, w raporcie pulpitu nawigacyjnego zgodności zostanie wyświetlony ich wpływ, ponieważ zwiększy się ocena zgodności.

    > [!NOTE]
    > Oceny są przeprowadzane mniej więcej co 12 godzin, dlatego wpływ na dane dotyczące zgodności zobaczysz dopiero po uruchomieniu oceny.

## <a name="next-steps"></a>Następne kroki

W tym samouczku omówiono używanie pulpitu nawigacyjnego Zgodność z przepisami usługi Security Center do wykonywania następujących czynności:

-   Wyświetlanie i monitorowanie poziomu zgodności względem istotnych dla Ciebie standardów i przepisów.

-   Zwiększanie stanu zgodności przez wprowadzanie w życie odpowiednich rekomendacji i obserwowanie poprawy oceny zgodności.

Pulpit nawigacyjny Zgodność z przepisami może znacznie uprościć proces zapewniania zgodności i zauważalnie skrócić czas wymagany do uzyskania dowodu zgodności dla platformy Azure i środowiska hybrydowego.

Aby dowiedzieć się więcej, zobacz:

-   [Aktualizacja do dynamicznych pakietów zgodności na pulpicie nawigacyjnym zgodności z przepisami (Wersja zapoznawcza)](update-regulatory-compliance-packages.md) — dowiedz się więcej o tej funkcji w wersji zapoznawczej, która umożliwia aktualizację standardów wyświetlanych na pulpicie nawigacyjnym zgodności z przepisami do nowych pakietów *dynamicznych.* Można również tę samą funkcję podglądu, aby dodać nowe pakiety zgodności i monitorować zgodność z dodatkowymi standardami. 

-   [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md) — dowiedz się, jak monitorować kondycję zasobów platformy Azure.

-   [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md) — dowiedz się, jak używać zaleceń w usłudze Azure Security Center w celu ochrony zasobów platformy Azure.

-   [Popraw swój wynik bezpieczeństwa w usłudze Azure Security Center](security-center-secure-score.md) — dowiedz się, jak nadać priorytet lukom w zabezpieczeniach i zaleceniu dotyczące zabezpieczeń, aby w pełni poprawić stan bezpieczeństwa.