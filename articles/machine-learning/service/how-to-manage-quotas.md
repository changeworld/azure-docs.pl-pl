---
title: Jak zarządzać i żądania przydziałów dla usługi Azure Machine Learning
description: W tym przewodniku opisano różne przydziały dla zasobów usługi Azure Machine Learning i sposób wyświetlania i zażądać zwiększenia przydziału.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 9/24/2018
ms.openlocfilehash: 0ee0a29be6a313e09cd77051dc29bc0131b57a40
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46997815"
---
# <a name="manage-and-request-quotas-for-azure-resources"></a>Zarządzanie i zażądać przydziały dla zasobów platformy Azure

Jak z innymi usługami platformy Azure istnieją limity określonych zasobów skojarzonych z usługą Azure Machine Learning. Te limity należeć do zakresu od dzienny limit liczby obszarów roboczych, można utworzyć w celu ograniczenia dotyczące rzeczywistego podstawowe zasoby obliczeniowe, które pobiera używane do celów szkoleniowych lub wnioskowania modeli. Ten artykuł zawiera szczegółowe informacje dotyczące wstępnie skonfigurowane limity dla różnych zasobów platformy Azure dla Twojej subskrypcji i zawiera także przydatne linki, które żądania udoskonalenia limitu przydziału dla każdego typu zasobu.

Należy pamiętać tych limitów przydziału podczas projektowania i skalowania w górę swoje zasoby usługi Azure ML. Na przykład jeśli klaster nie dociera do docelowej liczby węzłów, który określiłeś, następnie może osiągnięto limit rdzeni usługi Batch AI dla subskrypcji. Jeśli planujesz uruchamiania obciążeń produkcji na sztucznej Inteligencji usługi Batch, może być konieczne zwiększenie co najmniej jeden z limitów przydziału ponad wartość domyślną. Jeśli chcesz zwiększyć limit lub limitu przydziału przekracza Limit domyślne, otwórz żądanie obsługi klienta online bez dodatkowych opłat. Nie można zwiększyć limity powyżej wartości maksymalny Limit pokazano w poniższych tabelach. W przypadku żadnej kolumny maksymalny Limit zasób nie ma limitów dostosowywalnych. 

## <a name="special-considerations"></a>Specjalne uwagi

+ Przydział to limit środków, nie gwarancję możliwości obliczeniowych. Jeśli masz zapotrzebowanie na pojemność, skontaktuj się z działem pomocy technicznej platformy Azure.

+ Limit przydziału jest współużytkowany przez wszystkie usługi, z wyjątkiem usługi Batch AI w ramach subskrypcji usługi Azure Machine Learning jest jeden z nich. Pamiętaj obliczyć użycie przydziału dla wszystkich usług podczas obliczania Twoje potrzeby związane z pojemnością.

+ Domyślne limity zależą od typu kategorii oferty, takie jak bezpłatna wersja próbna, płatności i serii, na przykład Dv2, F, G i tak dalej.

## <a name="default-resource-quotas"></a>Domyślne przydziały zasobów

Poniżej przedstawiono podział limitów przydziału przez różnych typów zasobów w ramach subskrypcji platformy Azure. 

> [!Important]
> Limity mogą ulec zmianie. Najnowsze informacje zawsze znajduje się w temacie przydział poziomu usługi [dokumentu](https://docs.microsoft.com/azure/azure-subscription-service-limits/) na wszystkie usługi platformy Azure.

### <a name="virtual-machines"></a>Maszyny wirtualne 
Ma limitu liczby maszyn wirtualnych, które można udostępnić w subskrypcji platformy Azure dla usługi lub w sposób autonomiczny. Ten limit jest na poziomie regionu na łączna liczba rdzeni, a także na podstawie poszczególnych rodziny.

Należy podkreślić, że rdzenie maszyny wirtualnej mają regionalny limit całkowitej i regionalny limit rozmiaru serii (Dv2, F itp.), są wymuszane oddzielnie na. Rozważmy na przykład subskrypcję z całkowitym limitem rdzeni maszyn wirtualnych dla regionu Wschodnie stany USA wynoszącym 30, limitem rdzeni dla serii A wynoszącym 30 i limitem rdzeni dla serii D wynoszącym 30. W przypadku tej subskrypcji dozwolone będzie wdrożenie 30 maszyn wirtualnych A1 lub 30 maszyn wirtualnych D1 albo kombinacji obu rodzajów maszyn wirtualnych, pod warunkiem, że liczba rdzeni nie przekroczy 30 (na przykład 10 maszyn wirtualnych A1 i 20 maszyn wirtualnych D1).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

Listę bardziej szczegółowe i aktualne limity przydziału można znaleźć w artykule przydziału platformy Azure na poziomie [tutaj](https://docs.microsoft.com/azure/azure-subscription-service-limits#subscription-limits-1).

### <a name="batch-ai-clusters"></a>Klastry usługi batch AI
Usługa Batch AI ma domyślny limit przydziału liczby klastrów dozwolone na region w ramach subskrypcji i liczby rdzeni. Limit przydziału sztucznej Inteligencji usługi Batch jest wykonywane niezależnie od powyższych przydział rdzeni maszyny Wirtualnej i limity podstawowych nie są obecnie współdzielone między typami dwóch zasobów.

Dostępne zasoby:
+ Liczba rdzeni dedykowanych na region ma domyślny limit 10-24.  Można zwiększyć liczbę dedykowanych rdzeni na subskrypcję usługi Batch AI. Skontaktuj się z działem pomocy technicznej platformy Azure w celu omówienia możliwości zwiększenia.

+ Liczba rdzeni o niskim priorytecie, na region ma domyślny limit 10-24.  Można zwiększyć liczbę rdzeni o niskim priorytecie na subskrypcję usługi Batch AI. Skontaktuj się z działem pomocy technicznej platformy Azure w celu omówienia możliwości zwiększenia.

+ Klastry na region mają domyślny limit 20 i maksymalny limit 200. Jeśli chcesz poprosić o zwiększenie wykraczające poza ten limit, skontaktuj się z działem pomocy technicznej platformy Azure.

Listę bardziej szczegółowe i aktualne limity przydziału można znaleźć w artykule przydziału platformy Azure na poziomie [tutaj](https://docs.microsoft.com/azure/azure-subscription-service-limits#batch-ai-limits).

### <a name="container-instances"></a>Wystąpienia kontenerów

Istnieje również limit na liczbę wystąpień kontenera, które można zawsze wdrożyć w danym okresie czasu (co godzinę w zakresie) lub w ramach całej subskrypcji.

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

Listę bardziej szczegółowe i aktualne limity przydziału można znaleźć w artykule przydziału platformy Azure na poziomie [tutaj](https://docs.microsoft.com/azure/azure-subscription-service-limits#container-instances-limits).

### <a name="storage"></a>Magazyn
Istnieje limit liczby kont magazynu na region, jak również w ramach danej subskrypcji. Domyślny limit wynosi 200 i obejmuje konta Standard i Premium Storage. Jeśli potrzebujesz więcej niż 200 kont magazynu w danym regionie, należy wykonać żądanie za pośrednictwem [pomocy technicznej systemu Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). Zespół usługi Azure Storage przejrzy Twój przypadek biznesowy i może zatwierdzić do 250 kont magazynu dla danego regionu.


## <a name="find-your-quotas"></a>Znajdź limity przydziału

Przeglądanie przydziału dla różnych zasobów, takich jak maszyny wirtualne, Magazyn, sieć, jest łatwa w witrynie Azure portal.

1. W okienku po lewej stronie wybierz **wszystkich usług** , a następnie wybierz **subskrypcje** kategorii Ogólne.

1. Z listy subskrypcji Wybierz subskrypcję, którego limit przydziału, którego szukasz.

   **Istnieje ostrzeżenie**, specjalnie do wyświetlania przydziałów usługi Batch AI. Jak wspomniano powyżej, ten limit przydziału jest niezależna od limitu przydziału obliczeniowego w ramach Twojej subskrypcji. 
   Dla usługi Batch AI, po wybraniu **wszystkich usług**, wyszukaj usługi Batch AI i Otwórz usługę.

1. W obszarze **ustawienia**, wybierz opcję **użycie i przydziały** Aby wyświetlić bieżące limity przydziału i użycia.

1. Wybierz swoją subskrypcję, aby wyświetlić limitów przydziału. Pamiętaj, aby filtrować do usługi i regionu, który Cię interesuje.


## <a name="request-quota-increases"></a>Zażądać zwiększenia limitu przydziału

Jeśli chcesz zwiększyć limit lub limitu przydziału powyżej domyślny limit [Otwórz żądanie obsługi klienta online](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) bez dodatkowych opłat. 

Nie można zwiększyć limity powyżej maksymalny limit pokazanymi w tabelach. Jeśli nie ma żadnego limitu maksymalnej, zasób nie ma limitów dostosowywalnych. [To](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors) artykule opisano proces zwiększenia limitu przydziału bardziej szczegółowo.

Przesyłając żądanie zwiększenia limitu przydziału, musisz wybrać usługę żądasz, aby zwiększyć przydział, co może być usług takich jak limitu przydziału usługi Azure ml, usługi Batch AI limitu przydziału lub limit przydziału magazynu. 

> [!NOTE]
> [Bezpłatna wersja próbna subskrypcji](https://azure.microsoft.com/offers/ms-azr-0044p) nie kwalifikują się do zwiększenia limitu lub przydziału. Jeśli masz [subskrypcji bezpłatnej wersji próbnej](https://azure.microsoft.com/offers/ms-azr-0044p), możesz przeprowadzić uaktualnienie do [płatność za rzeczywiste użycie](https://azure.microsoft.com/offers/ms-azr-0003p/) subskrypcji. Aby uzyskać więcej informacji, zobacz [uaktualnienia próbnej wersji platformy Azure do płatności](../../billing/billing-upgrade-azure-subscription.md) i [subskrypcji bezpłatnej wersji próbnej — często zadawane pytania](https://azure.microsoft.com/free/free-account-faq).
