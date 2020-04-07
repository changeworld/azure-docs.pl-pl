---
title: Zarządzanie przydziałami & zasobów
titleSuffix: Azure Machine Learning
description: Dowiedz się więcej o przydziałach zasobów dla usługi Azure Machine Learning i o tym, jak zażądać więcej przydziału.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 03/05/2020
ms.openlocfilehash: 530647c3d32b62f0cac250795ccce580b182fa92
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756606"
---
# <a name="manage-and-request-quotas-for-azure-resources"></a>Zarządzanie przydziałami dla platformy Azure i żądanie ich
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ten artykuł zawiera szczegółowe informacje na temat wstępnie skonfigurowanych limitów zasobów platformy Azure dla twojej subskrypcji. W zestawie znajdują się również instrukcje dotyczące żądania ulepszeń przydziału dla każdego typu zasobu. Te limity są wprowadzane w celu zapobiegania nadmiernych przebiegów budżetu z powodu oszustw i w celu przestrzegania ograniczeń zdolności produkcyjnych platformy Azure.

Podobnie jak w przypadku innych usług platformy Azure, istnieją ograniczenia dotyczące niektórych zasobów skojarzonych z usługą Azure Machine Learning. Te limity wahają się od limitu liczby obszarów roboczych do limitów rzeczywistych podstawowych obliczeń, które są używane do szkolenia modelu lub wnioskowania/oceniania. 

Podczas projektowania i skalowania zasobów usługi Azure Machine Learning dla obciążeń produkcyjnych należy wziąć pod uwagę te limity. Na przykład jeśli klaster nie osiągnie docelowej liczby węzłów, być może osiągnięto limit rdzeni obliczeniowych usługi Azure Machine Learning dla subskrypcji. Jeśli chcesz podnieść limit lub przydział powyżej domyślnego limitu, otwórz żądanie obsługi klienta online bez żadnych opłat. Limity nie mogą być wywoływane powyżej maksymalnej wartości granicznej wyświetlanej w poniższych tabelach z powodu ograniczeń pojemności platformy Azure. Jeśli nie ma kolumny Maksymalny limit, zasób nie ma regulowanych limitów.

## <a name="special-considerations"></a>Uwagi szczególne

+ Przydział jest limitem kredytowym, a nie gwarancją zdolności produkcyjnych. Jeśli masz potrzeby pojemności na dużą skalę, skontaktuj się z pomocą techniczną platformy Azure.

+ Przydział jest współużytkowane przez wszystkie usługi w subskrypcjach, w tym usługi Azure Machine Learning. Jedynym wyjątkiem jest obliczenie usługi Azure Machine Learning, które ma oddzielny przydział od podstawowego przydziału obliczeń. Pamiętaj, aby obliczyć użycie przydziału we wszystkich usługach podczas oceny potrzeb w zakresie pojemności.

+ Domyślne limity różnią się w zależności od typu kategorii oferty, takiej jak bezpłatna wersja próbna, płatność zgodnie z rzeczywistym i seria maszyn wirtualnych, takie jak Dv2, F, G itd.

## <a name="default-resource-quotas"></a>Domyślne przydziały zasobów

Oto podział limitów przydziałów według różnych typów zasobów w ramach subskrypcji platformy Azure.

> [!IMPORTANT]
> Limity mogą ulec zmianie. Najnowsze zawsze można znaleźć w [dokumencie](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/) przydziału na poziomie usługi dla całej platformy Azure.

### <a name="virtual-machines"></a>Maszyny wirtualne
Dla każdej subskrypcji platformy Azure istnieje limit liczby maszyn wirtualnych, które można mieć w usługach lub autonomicznych. Limit ten jest na poziomie regionu zarówno na podstawie całkowitej liczby rdzeni, jak i na rodzinę.

Rdzenie maszyny wirtualnej mają regionalny limit całkowity i regionalny limit serii na rozmiar (Dv2, F itp.), z których oba są wymuszane oddzielnie. Rozważmy na przykład subskrypcję z całkowitym limitem rdzeni maszyn wirtualnych dla regionu Wschodnie stany USA wynoszącym 30, limitem rdzeni dla serii A wynoszącym 30 i limitem rdzeni dla serii D wynoszącym 30. W przypadku tej subskrypcji dozwolone będzie wdrożenie 30 maszyn wirtualnych A1 lub 30 maszyn wirtualnych D1 albo kombinacji obu rodzajów maszyn wirtualnych, pod warunkiem, że liczba rdzeni nie przekroczy 30 (na przykład 10 maszyn wirtualnych A1 i 20 maszyn wirtualnych D1).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

Aby uzyskać bardziej szczegółową i aktualną listę limitów przydziałów, zapoznaj się z artykułem o przydziałach dla całej platformy Azure [tutaj](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).

### <a name="azure-machine-learning-compute"></a>Środowisko obliczeniowe usługi Azure Machine Learning
W przypadku obliczeń usługi Azure Machine Learning istnieje domyślny limit przydziału zarówno dla liczby rdzeni, jak i liczby unikatowych zasobów obliczeniowych dozwolonych dla regionu w ramach subskrypcji. Ten przydział jest oddzielony od przydziału podstawowego maszyny Wirtualnej powyżej i podstawowe limity nie są współużytkowane między dwoma typami zasobów, ponieważ AmlCompute jest usługą zarządzaną, która wdraża zasoby w modelu hostowanym w imieniu.

Dostępne zasoby:
+ Dedykowane rdzenie na region mają domyślny limit 24- 300 w zależności od typu oferty subskrypcji z wyższymi wartościami domyślnymi dla typów ofert EA i CSP.  Liczba dedykowanych rdzeni na subskrypcję może zostać zwiększona i różni się od siebie dla każdej rodziny maszyn wirtualnych. Niektóre wyspecjalizowane rodziny maszyn wirtualnych, takie jak NCv2, NCv3 lub ND serii zaczynają się od domyślnego zera rdzeni. Skontaktuj się z pomocą techniczną platformy Azure, podnosząc żądanie przydziału w celu omówienia opcji zwiększenia.

+ Rdzenie o niskim priorytecie na region mają domyślny limit 100- 3000 w zależności od typu oferty subskrypcji z wyższymi wartościami domyślnymi dla typów ofert EA i Dostawcy usług kryptograficznych. Liczba rdzeni o niskim priorytecie na subskrypcję może zostać zwiększona i jest pojedynczą wartością w rodzinach maszyn wirtualnych. Skontaktuj się z pomocą techniczną platformy Azure, aby omówić opcje zwiększania.

+ Klastry na region mają domyślny limit 200. Są one współużytkowane między klastrem szkoleniowym a wystąpieniem obliczeniowym (które jest uważane za klaster pojedynczego węzła do celów przydziału). Skontaktuj się z pomocą techniczną platformy Azure, jeśli chcesz zażądać zwiększenia poza tym limitem.

+ Istnieją inne ścisłe limity, których nie można przekroczyć po trafieniu.

| **Zasobów** | **Maksymalny limit** |
| --- | --- |
| Maksymalna liczba obszarów roboczych na grupę zasobów | 800 |
| Maksymalna liczba węzłów w jednym zasobie obliczeniowym usługi Azure Machine Learning Compute (AmlCompute) | 100 węzłów |
| Maksymalna liczba procesów MPI gpu na węzeł | 1-4 |
| Maksymalna liczba pracowników GPU na węzeł | 1-4 |
| Maksymalny okres istnienia zadania | 90 dni<sup>1</sup> |
| Maksymalny okres istnienia zadania w węźle o niskim priorytecie | 7 dni<sup>2</sup> |
| Maksymalna liczba serwerów parametrów na węzeł | 1 |

<sup>1</sup> Maksymalny okres istnienia odnosi się do czasu rozpoczęcia biegu i zakończenia. Ukończone przebiegi utrzymują się przez czas nieokreślony; dane dla uruchomień, które nie zostały ukończone w maksymalnym okresie istnienia, nie są dostępne.
<sup>2</sup> Zadania w węźle o niskim priorytecie mogą być wywłaszczone w dowolnym momencie ograniczenia pojemności. Zalecamy wdrożenie punktów kontrolnych w swojej pracy.

### <a name="azure-machine-learning-pipelines"></a>Potoki usługi Azure Machine Learning
W przypadku potoków usługi Azure Machine Learning istnieje limit przydziału liczby kroków w potoku i liczby przebiegów opartych na harmonogramie opublikowanych potoków na region w ramach subskrypcji.
- Maksymalna liczba kroków dozwolonych w potoku wynosi 30 000
- Maksymalna liczba sumy przebiegów opartych na harmonogramie i ciągnie obiektów blob dla harmonogramów wyzwalanych przez bloga opublikowanych potoków na subskrypcję miesięcznie wynosi 100 000

> [!NOTE]
> Jeśli chcesz zwiększyć ten limit, skontaktuj się z [pomocą techniczną firmy Microsoft](https://azure.microsoft.com/support/options/).

### <a name="container-instances"></a>Wystąpienia kontenerów

Istnieje również limit liczby wystąpień kontenera, które można rozkręcić w danym okresie czasu (zakres godzinowy) lub w całej subskrypcji.

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

Aby uzyskać bardziej szczegółową i aktualną listę limitów przydziałów, zapoznaj się z artykułem o przydziałach dla całej platformy Azure [tutaj](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits).

### <a name="storage"></a>Magazyn
Istnieje limit liczby kont magazynu w danym regionie, a także w danej subskrypcji. Domyślny limit wynosi 250 i obejmuje zarówno konta magazynu standardowego, jak i magazynu w wersji premium. Jeśli potrzebujesz więcej niż 250 kont magazynu w danym regionie, zgładź żądanie za pośrednictwem [pomocy technicznej platformy Azure.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) Zespół usługi Azure Storage przejrzy Twój przypadek biznesowy i może zatwierdzić maksymalnie 250 kont magazynu dla danego regionu.


## <a name="workspace-level-quota"></a>Przydział na poziomie obszaru roboczego

Aby lepiej zarządzać alokacjami zasobów dla Amlcompute między różnymi obszarami roboczymi, wprowadziliśmy funkcję, która umożliwia dystrybucję przydziałów na poziomie subskrypcji (według rodziny maszyn wirtualnych) i skonfigurowanie ich na poziomie obszaru roboczego. Domyślne zachowanie jest to, że wszystkie obszary robocze mają taki sam przydział jak przydział na poziomie subskrypcji dla dowolnej rodziny maszyn wirtualnych. Jednak wraz ze wzrostem liczby obszarów roboczych i obciążeń o różnym priorytecie rozpocząć udostępnianie tych samych zasobów, użytkownicy chcą sposób, aby lepiej współużytkować pojemność i uniknąć problemów rywalizacji o zasoby. Usługa Azure Machine Learning zapewnia rozwiązanie z ofertą zarządzanych obliczeń, umożliwiając użytkownikom ustawienie maksymalnego przydziału dla określonej rodziny maszyn wirtualnych w każdym obszarze roboczym. Jest to analogiczne do dystrybucji pojemności między obszarami roboczymi, a użytkownicy mogą wybrać również nadmierne przydzielanie do maksymalnego wykorzystania dysku. 

Aby ustawić przydziały na poziomie obszaru roboczego, przejdź do dowolnego obszaru roboczego w ramach subskrypcji i kliknij **opcję Użyciae + przydziały** w lewym okienku. Następnie wybierz kartę **Konfiguruj przydziały,** aby wyświetlić przydziały, rozwinąć dowolną rodzinę maszyn wirtualnych i ustawić limit przydziału dla dowolnego obszaru roboczego wymienionego w tej rodzinie maszyn wirtualnych. Należy pamiętać, że nie można ustawić wartość ujemną lub wartość wyższą niż przydział na poziomie subskrypcji. Ponadto, jak można zaobserwować, domyślnie wszystkie obszary robocze są przypisywane cały przydział subskrypcji, aby umożliwić pełne wykorzystanie przydzielonego przydziału.

[![Przydział poziomu obszaru roboczego usługi Azure Machine Learning](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)


> [!NOTE]
> Jest to tylko funkcja w wersji Enterprise. Jeśli w subskrypcji znajduje się zarówno obszar roboczy wersji podstawowej, jak i enterprise, możesz użyć tego obszaru roboczego, można go użyć do ustawiania przydziałów tylko w obszarach roboczych przedsiębiorstwa. Podstawowe obszary robocze będą nadal mieć przydział poziomu subskrypcji, który jest zachowaniem domyślnym.
>
> Aby ustawić przydział na poziomie obszaru roboczego, potrzebujesz uprawnień na poziomie subskrypcji. Jest to wymuszane, aby właściciele poszczególnych obszarów roboczych nie edytowali ani nie zwiększali swoich przydziałów i nie rozpoczynali wkraczania do zasobów zarezerwowanych dla innego obszaru roboczego. W związku z tym administrator subskrypcji najlepiej nadaje się do przydzielania i dystrybucji tych przydziałów w obszarach roboczych.



## <a name="view-your-usage-and-quotas"></a>Wyświetlanie użycia i przydziałów

Wyświetlanie przydziału dla różnych zasobów, takich jak maszyny wirtualne, magazyn, sieć, jest łatwe za pośrednictwem witryny Azure portal.

1. W lewym okienku wybierz pozycję **Wszystkie usługi,** a następnie wybierz pozycję **Subskrypcje** w kategorii Ogólne.

1. Z listy subskrypcji wybierz subskrypcję, której przydziału szukasz.

   **Istnieje zastrzeżenie**, specjalnie do wyświetlania przydziału obliczeń usługi Azure Machine Learning. Jak wspomniano powyżej, ten przydział jest oddzielony od przydziału obliczeń w ramach subskrypcji.

1. W lewym okienku wybierz **usługę Uczenie maszynowe,** a następnie wybierz dowolny obszar roboczy z pokazanej listy

1. W następnym bloku w sekcji **Pomoc techniczna + rozwiązywanie problemów** wybierz **użycie + przydziały,** aby wyświetlić bieżące limity przydziałów i użycie.

1. Wybierz subskrypcję, aby wyświetlić limity przydziału. Pamiętaj, aby filtrować do regionu, który Cię interesuje.

1. Teraz można przełączać się między widokiem poziomu subskrypcji a widokiem na poziomie obszaru roboczego:
    + **Widok subskrypcji:** Dzięki temu można wyświetlić użycie przydziału podstawowego przez rodzinę maszyn wirtualnych, rozwinięcie go według obszaru roboczego i dalsze rozszerzenie go o rzeczywiste nazwy klastra. Ten widok jest optymalny, aby szybko uzyskać szczegółowe informacje na temat użycia rdzenia dla określonej rodziny maszyn wirtualnych, aby zobaczyć podział według obszarów roboczych i dalej przez klastry podstawowe dla każdego z tych obszarów roboczych. Ogólna konwencja w tym widoku jest (użycie/przydział), gdzie użycie jest bieżąca liczba skalowane rdzenie i przydział jest logiczną maksymalną liczbę rdzeni, które zasób można skalować do. Dla każdego **obszaru roboczego**przydział będzie przydziałem na poziomie obszaru roboczego (jak wyjaśniono powyżej), który oznacza maksymalną liczbę rdzeni, które można skalować dla określonej rodziny maszyn wirtualnych. W przypadku **klastra** podobnie przydział jest w rzeczywistości rdzeniami odpowiadającymi maksymalnej liczbie węzłów, które klaster może skalować do zdefiniowanej przez właściwość max_nodes.

    + **Widok obszaru roboczego:** Dzięki temu można wyświetlić użycie przydziału podstawowego według obszaru roboczego, rozwinięcie go według rodziny maszyn wirtualnych i dalsze rozszerzenie go o rzeczywiste nazwy klastra. Ten widok jest optymalny do szybkiego uzyskania szczegółowych informacji na temat użycia rdzenia dla określonego obszaru roboczego, aby zobaczyć podział przez rodziny maszyn wirtualnych i dalej przez podstawowe klastry dla każdej z tych rodzin.

## <a name="request-quota-increases"></a>Prośba o zwiększenie limitów przydziałów

Jeśli chcesz podnieść limit lub przydział powyżej domyślnego limitu, [otwórz żądanie obsługi klienta online](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) bez żadnych opłat.

Limitów nie można podnieść powyżej maksymalnej wartości granicznej pokazanej w tabelach. Jeśli nie ma limitu maksymalnego, zasób nie ma regulowanych limitów. [W tym](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors) artykule opisano proces zwiększania przydziału bardziej szczegółowo.

Żądając zwiększenia przydziału, należy wybrać usługę, której żądasz, aby podnieść przydział, która może być usługami, takimi jak przydział usługi uczenia maszynowego, wystąpienia kontenera lub przydział magazynu. Ponadto w przypadku przetwarzania usługi Azure Machine Learning można kliknąć przycisk **Żądanie przydziału** podczas wyświetlania przydziału zgodnie z powyższymi krokami.

> [!NOTE]
> [Bezpłatne subskrypcje próbne](https://azure.microsoft.com/offers/ms-azr-0044p) nie kwalifikują się do zwiększenia limitu lub przydziału. Jeśli masz [bezpłatną subskrypcję próbną,](https://azure.microsoft.com/offers/ms-azr-0044p)możesz uaktualnić subskrypcję [płatności zgodnie z rzeczywistymu.](https://azure.microsoft.com/offers/ms-azr-0003p/) Aby uzyskać więcej informacji, zobacz [Uaktualnianie bezpłatnej wersji próbnej platformy Azure do płatnej wersji](../billing/billing-upgrade-azure-subscription.md) próbnej i [bezpłatna wersja próbna — często zadawane pytania](https://azure.microsoft.com/free/free-account-faq).
