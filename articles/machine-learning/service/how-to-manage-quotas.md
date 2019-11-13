---
title: Zarządzanie zasobami & przydziałów
titleSuffix: Azure Machine Learning
description: Dowiedz się więcej na temat przydziałów zasobów dla Azure Machine Learning i zażądaj więcej przydziału.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 11/04/2019
ms.openlocfilehash: d44fb94978c450808c8a1c0852d4c771a100857e
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961717"
---
# <a name="manage-and-request-quotas-for-azure-resources"></a>Zarządzanie i zażądać przydziały dla zasobów platformy Azure
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ten artykuł zawiera szczegółowe informacje dotyczące wstępnie skonfigurowanych limitów zasobów platformy Azure dla Twojej subskrypcji. Uwzględniono również instrukcje dotyczące sposobu żądania ulepszeń przydziału dla każdego typu zasobu. Limity te są umieszczane w miejscu, aby zapobiec budżetu przepełnień z powodu oszustw i uwzględnić ograniczenia pojemność na platformie Azure.

Podobnie jak w przypadku innych usług platformy Azure, istnieją ograniczenia dotyczące niektórych zasobów skojarzonych z Azure Machine Learning. Te limity mieszczą się w zakresie od limitu liczby obszarów roboczych, które są ograniczone do rzeczywistego obliczenia bazowego, który jest używany do uczenia lub wnioskowania/oceny modelu. 

Podczas projektowania i skalowania zasobów Azure Machine Learning dla obciążeń produkcyjnych należy wziąć pod uwagę te limity. Na przykład jeśli klaster nie osiągnie docelowej liczby węzłów, być może osiągnięto limit liczby rdzeni obliczeniowych Azure Machine Learning dla subskrypcji. Jeśli chcesz zwiększyć limit lub limitu przydziału przekracza Limit domyślne, otwórz żądanie obsługi klienta online bez dodatkowych opłat. Nie można zwiększyć limity powyżej maksymalny Limit pokazanymi w poniższych tabelach ze względu na ograniczenia pojemności na platformie Azure. W przypadku żadnej kolumny maksymalny Limit zasób nie ma limitów dostosowywalnych.

## <a name="special-considerations"></a>Specjalne uwagi

+ Przydział to limit środków, nie gwarancję możliwości obliczeniowych. Jeśli masz zapotrzebowanie na pojemność, skontaktuj się z działem pomocy technicznej platformy Azure.

+ Przydział jest współużytkowany przez wszystkie usługi w Twoich subskrypcjach, w tym Azure Machine Learning. Jedyny wyjątek to Azure Machine Learning COMPUTE, który ma oddzielny przydział od podstawowego limitu przydziału. Pamiętaj obliczyć użycie przydziału dla wszystkich usług podczas obliczania Twoje potrzeby związane z pojemnością.

+ Domyślne limity różnią się w zależności od typu kategorii oferty, takich jak bezpłatna wersja próbna, płatność zgodnie z rzeczywistym użyciem i seria maszyn wirtualnych, takie jak Dv2, F, G i tak dalej.

## <a name="default-resource-quotas"></a>Domyślne przydziały zasobów

Poniżej przedstawiono podział limitów przydziału przez różnych typów zasobów w ramach subskrypcji platformy Azure.

> [!Important]
> Limity mogą ulec zmianie. Najnowsze informacje zawsze znajduje się w temacie przydział poziomu usługi [dokumentu](https://docs.microsoft.com/azure/azure-subscription-service-limits/) na wszystkie usługi platformy Azure.

### <a name="virtual-machines"></a>Maszyny wirtualne
Dla każdej subskrypcji platformy Azure istnieje limit liczby maszyn wirtualnych, które mogą być używane w ramach usług lub autonomicznej. Ten limit jest na poziomie regionu na łączna liczba rdzeni, a także na podstawie poszczególnych rodziny.

Rdzenie maszyn wirtualnych mają limit regionalny dla regionu i regionalny dla serii rozmiaru (Dv2, F itp.), które są osobno wymuszane. Rozważmy na przykład subskrypcję z całkowitym limitem rdzeni maszyn wirtualnych dla regionu Wschodnie stany USA wynoszącym 30, limitem rdzeni dla serii A wynoszącym 30 i limitem rdzeni dla serii D wynoszącym 30. W przypadku tej subskrypcji dozwolone będzie wdrożenie 30 maszyn wirtualnych A1 lub 30 maszyn wirtualnych D1 albo kombinacji obu rodzajów maszyn wirtualnych, pod warunkiem, że liczba rdzeni nie przekroczy 30 (na przykład 10 maszyn wirtualnych A1 i 20 maszyn wirtualnych D1).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

Listę bardziej szczegółowe i aktualne limity przydziału można znaleźć w artykule przydziału platformy Azure na poziomie [tutaj](https://docs.microsoft.com/azure/azure-subscription-service-limits).

### <a name="azure-machine-learning-compute"></a>Usługi Azure Machine Learning obliczeń
Azure obliczeniowego usługi Machine Learning ma domyślny limit przydziału liczby zasobów obliczeniowych unikatowy dozwolone na region w ramach subskrypcji i liczby rdzeni. Ten limit przydziału jest oddzielony od powyżej limitu przydziału rdzeni maszyny wirtualnej, a podstawowe limity nie są współużytkowane przez dwa typy zasobów, ponieważ AmlCompute jest zarządzaną usługą, która wdraża zasoby w modelu hostowanym w imieniu.

Dostępne zasoby:
+ Dedykowane rdzenie na region mają domyślny limit 24-300 w zależności od typu oferty subskrypcji z wyższymi wartościami domyślnymi dla typów ofert EA i CSP.  Liczbę rdzeni dedykowanych na subskrypcję można zwiększyć i różnią się w zależności od rodziny maszyn wirtualnych. Niektóre wyspecjalizowane rodziny maszyn wirtualnych, takie jak NCv2, Seria NCV3 lub ND, zaczynają się domyślnie od zera. Skontaktuj się z pomocą techniczną platformy Azure, wywołując żądanie limitu przydziału w celu omówienia opcji wzrostu.

+ Rdzenie o niskim priorytecie na region mają domyślny limit 100-3000 w zależności od typu oferty subskrypcji z wyższymi wartościami domyślnymi dla typów ofert EA i CSP. Liczbę rdzeni o niskim priorytecie na subskrypcję można zwiększyć i jest to jedna wartość w różnych rodzinach maszyn wirtualnych. Skontaktuj się z działem pomocy technicznej platformy Azure w celu omówienia możliwości zwiększenia.

+ Klastry na region mają domyślny limit 200. Są one współużytkowane przez klaster szkoleniowy i wystąpienie obliczeniowe (które jest traktowane jako klaster z pojedynczym węzłem w celu przekroczenia limitu przydziału). Jeśli chcesz poprosić o zwiększenie wykraczające poza ten limit, skontaktuj się z działem pomocy technicznej platformy Azure.

+ Istnieją * * inne ścisłe limity, których nie można przekroczyć po trafieniu.

| **Zasób** | **Maksymalny limit** |
| --- | --- |
| Maksymalna obszarów roboczych dla grupy zasobów | 800 |
| Maksymalna liczba węzłów w pojedynczym zasobie Azure obliczeniowego usługi Machine Learning (AmlCompute) | 100 węzłów |
| Maksymalna MPI GPU przetwarza każdy węzeł | 1 – 4 |
| Maksymalna pracowników procesora GPU na węzeł | 1 – 4 |
| Maksymalny okres istnienia | 90 dni<sup>1</sup> |
| Maksymalny okres istnienia zadania w węźle o niskim priorytecie | 7 dni<sup>2</sup> |
| Parametr maksymalnego serwerów na węzeł | 1 |

<sup>1</sup> maksymalny okres istnienia odwołuje się do czasu rozpoczęcia przebiegu, a po zakończeniu. Zakończone przebiegi utrwalić przez czas nieokreślony; dane dotyczące uruchomienia nieukończonych w ciągu maksymalnego okresu istnienia nie jest dostępny.
<sup>2</sup> zadania w węźle o niskim priorytecie można przewyższyć w dowolnym momencie, gdy istnieje ograniczenie pojemności. Zalecamy zaimplementowanie tworzenia punktów kontrolnych w zadaniu.

### <a name="azure-machine-learning-pipelines"></a>Potoki Azure Machine Learning
W przypadku potoków Azure Machine Learning obowiązuje limit przydziału liczby kroków w potoku oraz liczby uruchomień opublikowanych potoków dla regionu w ramach subskrypcji.
- Maksymalna liczba kroków dozwolona w potoku to 30 000
- Maksymalna liczba przebiegów opartych na harmonogramie i ściągania obiektów BLOB dla harmonogramów opublikowanych potoków na subskrypcję na miesiąc wynosi 100 000

> [!NOTE]
> Jeśli chcesz zwiększyć ten limit, skontaktuj się z [Pomoc techniczna firmy Microsoft](https://azure.microsoft.com/support/options/).

### <a name="container-instances"></a>Wystąpienia kontenerów

Istnieje również limit na liczbę wystąpień kontenera, które można zawsze wdrożyć w danym okresie czasu (co godzinę w zakresie) lub w ramach całej subskrypcji.

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

Listę bardziej szczegółowe i aktualne limity przydziału można znaleźć w artykule przydziału platformy Azure na poziomie [tutaj](https://docs.microsoft.com/azure/azure-subscription-service-limits#container-instances-limits).

### <a name="storage"></a>Magazyn
Istnieje limit liczby kont magazynu na region, jak również w ramach danej subskrypcji. Domyślny limit to 250 i obejmuje konta standardowe i Premium Storage. Jeśli potrzebujesz więcej niż 250 kont magazynu w danym regionie, Przekształć żądanie przez [Pomoc techniczną platformy Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). Zespół usługi Azure Storage przejrzy Twój przypadek biznesowy i może zatwierdzić do 250 kont magazynu dla danego regionu.


## <a name="workspace-level-quota"></a>Przydział poziomu obszaru roboczego

Aby lepiej zarządzać alokacjami zasobów dla Amlcompute między różnymi obszarami roboczymi, wprowadziliśmy funkcję umożliwiającą dystrybuowanie przydziałów na poziomie subskrypcji (przez rodzinę maszyn wirtualnych) i konfigurowanie ich na poziomie obszaru roboczego. Domyślnym zachowaniem jest to, że wszystkie obszary robocze mają ten sam limit przydziału dla każdej rodziny maszyn wirtualnych. Jednak w miarę zwiększania się liczby obszarów roboczych i obciążeń o różnym priorytecie zaczynają współużytkować te same zasoby, użytkownicy chcą mieć możliwość lepszego udostępniania pojemności i uniknięcia problemów z rywalizacją o zasoby. Azure Machine Learning udostępnia rozwiązanie z zarządzaną ofertą obliczeniową przez umożliwienie użytkownikom ustawiania maksymalnego limitu przydziału dla określonej rodziny maszyn wirtualnych w każdym obszarze roboczym. Jest to analogiczne do dystrybucji pojemności między obszarami roboczymi, a użytkownicy mogą wybrać opcję przydzielenia w celu uzyskania maksymalnego użycia. 

Aby ustawić przydziały na poziomie obszaru roboczego, przejdź do dowolnego obszaru roboczego w ramach subskrypcji, a następnie kliknij pozycję **użycie + przydziały** w okienku po lewej stronie. Następnie wybierz kartę **Konfigurowanie przydziałów** , aby wyświetlić przydziały, rozwiń każdą rodzinę maszyn wirtualnych i ustawić limit przydziału dla każdego obszaru roboczego wymienionego w ramach tej rodziny maszyn wirtualnych. Należy pamiętać, że nie można ustawić wartości ujemnej ani wartości większej niż limit przydziału poziomu subskrypcji. Ponadto w miarę obserwowania wszystkie obszary robocze są przypisywane do całego przydziału subskrypcji, aby umożliwić pełne wykorzystanie przydzielonego limitu przydziału.

[![Azure Machine Learning przydziału poziomu obszaru roboczego](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)


> [!NOTE]
> Jest to tylko funkcja w wersji Enterprise. Jeśli masz obszar roboczy Basic i Enterprise Edition w ramach subskrypcji, możesz użyć tego ustawienia, aby ustawić przydziały tylko dla obszarów roboczych przedsiębiorstwa. Twoje podstawowe obszary robocze będą nadal miały przydziały poziomu subskrypcji, które są zachowaniem domyślnym.
>
> Do ustawiania przydziału na poziomie obszaru roboczego wymagane są uprawnienia na poziomie subskrypcji. Jest to wymuszane w taki sposób, aby indywidualni właściciele obszaru roboczego nie edytowali ani nie zwiększają swoich przydziałów i nie encroaching się do zasobów ustawionych dla innego obszaru roboczego. W ten sposób administrator subskrypcji najlepiej przypisuje i dystrybuuje te przydziały w obszarach roboczych.



## <a name="view-your-usage-and-quotas"></a>Wyświetlanie użycia i przydziałów

Przeglądanie przydziału dla różnych zasobów, takich jak maszyny wirtualne, Magazyn, sieć, jest łatwa w witrynie Azure portal.

1. W okienku po lewej stronie wybierz pozycję **wszystkie usługi** , a następnie wybierz pozycję **subskrypcje** w kategorii Ogólne.

1. Z listy subskrypcji Wybierz subskrypcję, którego limit przydziału, którego szukasz.

   **Istnieje ostrzeżenie**, specjalnie do wyświetlania limitu przydziału obliczeniowego usługi Azure Machine Learning. Jak wspomniano powyżej, ten limit przydziału jest niezależna od limitu przydziału obliczeniowego w ramach Twojej subskrypcji.

1. W okienku po lewej stronie wybierz pozycję **usługa Machine Learning** a następnie wybierz dowolny obszar roboczy z wyświetlonej listy

1. W następnym bloku w obszarze **pomoc techniczna i rozwiązywanie problemów z sekcji** wybierz **użycie i przydziały** Aby wyświetlić bieżące limity przydziału i użycia.

1. Wybierz subskrypcję, aby wyświetlić limitów przydziału. Pamiętaj, aby odfiltrować region, który Cię interesuje.

1. Teraz można przełączać się między widokiem poziomu subskrypcji a widokiem poziomu obszaru roboczego:
    + **Widok subskrypcji:** Pozwala to na wyświetlenie użycia przydziału rdzeni przez rodzinę maszyn wirtualnych, rozwinięcie go według obszaru roboczego i dalsze rozszerzenie go o rzeczywiste nazwy klastra. Ten widok jest optymalny, aby szybko zapoznać się ze szczegółami dotyczącymi użycia podstawowego dla danej rodziny maszyn wirtualnych, aby zobaczyć podziały przez obszary robocze i inne klastry dla każdego z tych obszarów roboczych. Ogólna Konwencja w tym widoku to (użycie/przydział), gdzie użycie jest bieżącą liczbą skalowalnych rdzeni, a limit przydziału to logiczna Maksymalna liczba rdzeni, do których można skalować zasób. Dla każdego **obszaru roboczego**przydział będzie przydziałem na poziomie obszaru roboczego (jak wyjaśniono powyżej), co oznacza maksymalną liczbę rdzeni, które można skalować do określonej rodziny maszyn wirtualnych. W przypadku **klastra** w podobny sposób przydziały są w rzeczywistości rdzeniami odpowiadającymi maksymalnej liczbie węzłów, które klaster może skalować do określonych przez właściwość max_nodes.

    + **Widok obszaru roboczego:** Pozwala to na wyświetlenie użycia zasobów podstawowych według obszaru roboczego, rozszerzenie go przez rodzinę maszyn wirtualnych i dalsze rozszerzenie go o rzeczywiste nazwy klastra. Ten widok jest optymalny, aby szybko zapoznać się ze szczegółami dotyczącymi podstawowego użycia określonego obszaru roboczego, aby zobaczyć podział przez rodziny maszyn wirtualnych i inne klastry dla każdej z tych rodzin.

## <a name="request-quota-increases"></a>Zażądać zwiększenia limitu przydziału

Jeśli chcesz podnieść limit lub przydział powyżej domyślnego limitu, [Otwórz żądanie obsługi klienta online](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) bez dodatkowych opłat.

Limitów nie można zwiększyć powyżej wartości maksymalnego limitu pokazanej w tabelach. W przypadku braku maksymalnego limitu zasób nie ma dopuszczalnych limitów. [To](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors) artykule opisano proces zwiększenia limitu przydziału bardziej szczegółowo.

Przesyłając żądanie zwiększenia limitu przydziału, należy wybrać usługę żądasz, aby zwiększyć przydział, co może świadczyć usługi, takie jak limitu przydziału usługi Machine Learning, Container instances lub przydział magazynowania. Oprócz Azure Machine Learning obliczeń, można kliknąć przycisk **limitu przydziału** podczas wyświetlania przydziału, wykonując czynności opisane powyżej.

> [!NOTE]
> [Bezpłatna wersja próbna subskrypcji](https://azure.microsoft.com/offers/ms-azr-0044p) nie kwalifikują się do zwiększenia limitu lub przydziału. Jeśli masz [subskrypcji bezpłatnej wersji próbnej](https://azure.microsoft.com/offers/ms-azr-0044p), możesz przeprowadzić uaktualnienie do [płatność za rzeczywiste użycie](https://azure.microsoft.com/offers/ms-azr-0003p/) subskrypcji. Aby uzyskać więcej informacji, zobacz [uaktualnienia próbnej wersji platformy Azure do płatności](../../billing/billing-upgrade-azure-subscription.md) i [subskrypcji bezpłatnej wersji próbnej — często zadawane pytania](https://azure.microsoft.com/free/free-account-faq).
