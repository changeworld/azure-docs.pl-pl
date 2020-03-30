---
title: Ocena wpływu nowych zasad platformy Azure
description: Poznaj proces, który należy wykonać podczas wprowadzania nowej definicji zasad w środowisku platformy Azure.
ms.date: 09/23/2019
ms.topic: conceptual
ms.openlocfilehash: 562fa2378356ddc1eac48b6ea5c160ebf655d525
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74463530"
---
# <a name="evaluate-the-impact-of-a-new-azure-policy"></a>Ocena wpływu nowych zasad platformy Azure

Usługa Azure Policy to zaawansowane narzędzie do zarządzania zasobami platformy Azure zgodnie ze standardami biznesowymi i w celu spełnienia wymagań dotyczących zgodności. Gdy osoby, procesy lub potoki tworzą lub aktualizują zasoby, usługa Azure Policy przegląda żądanie. Gdy efekt definicji zasad jest [Dołącz](./effects.md#deny) lub [DeployIfNotExists](./effects.md#deployifnotexists), Zasada zmienia żądanie lub dodaje do niego. Gdy efekt definicji zasad jest [Inspekcja](./effects.md#audit) lub [AuditIfNotExists](./effects.md#auditifnotexists), Zasada powoduje wpis dziennika działania do utworzenia. A gdy efekt definicji zasad jest [Odmów](./effects.md#deny), Zasady zatrzymuje tworzenie lub zmiany żądania.

Te wyniki są dokładnie tak pożądane, gdy wiesz, że zasady są zdefiniowane poprawnie. Jednak ważne jest, aby sprawdzić poprawność nowych zasad działa zgodnie z przeznaczeniem przed zezwoleniem na zmianę lub zablokowanie pracy. Sprawdzanie poprawności musi upewnić się, że tylko zamierzone zasoby są określane jako niezgodne i nie zgodne zasoby są niepoprawnie uwzględnione (znany jako _fałszywie dodatnie)_ w wynikach.

Zalecane podejście do sprawdzania poprawności nowej definicji zasad jest następujące kroki:

- Ścisłe zdefiniowanie zasad
- Inspekcja istniejących zasobów
- Inspekcja nowych lub zaktualizowanych żądań zasobów
- Wdrażanie zasad w zasobach
- Monitorowanie ciągłe

## <a name="tightly-define-your-policy"></a>Ścisłe zdefiniowanie zasad

Ważne jest, aby zrozumieć, jak zasady biznesowe są implementowane jako definicja zasad i relacji zasobów platformy Azure z innymi usługami platformy Azure. Ten krok jest realizowany przez [identyfikację wymagań](../tutorials/create-custom-policy-definition.md#identify-requirements) i [określenie właściwości zasobu](../tutorials/create-custom-policy-definition.md#determine-resource-properties).
Ale ważne jest również, aby wyjść poza wąską definicję polityki biznesowej. Czy stan zasad na przykład "Wszystkie maszyny wirtualne muszą..."? Co z innymi usługami platformy Azure, które korzystają z maszyn wirtualnych, takich jak HDInsight lub AKS? Podczas definiowania zasad, musimy rozważyć, jak ta zasada wpływa na zasoby, które są używane przez inne usługi.

Z tego powodu definicje zasad powinny być tak ściśle zdefiniowane i skoncentrowane na zasobach i właściwościach, które należy ocenić pod kątem zgodności, jak to możliwe.

## <a name="audit-existing-resources"></a>Inspekcja istniejących zasobów

Przed rozpoczęciem zarządzania nowymi lub zaktualizowanymi zasobami za pomocą nowej definicji zasad najlepiej jest zobaczyć, jak ocenia ona ograniczony podzbiór istniejących zasobów, takich jak grupa zasobów testowych. [effect](./effects.md) Użyj trybu
 [wymuszania](./assignment-structure.md#enforcement-mode)_wyłączone (DoNotEnforce)_ w przypisaniu zasad, aby zapobiec wyzwalaniu lub tworzenia wpisów dziennika aktywności.

Ten krok daje możliwość oceny wyników zgodności nowych zasad dotyczących istniejących zasobów bez wpływu na przepływ pracy. Sprawdź, czy żadne zgodne zasoby nie są oznaczone jako niezgodne _(fałszywie dodatnie)_ i czy wszystkie zasoby, których oczekujesz, że będą niezgodne, są poprawnie oznaczone.
Po początkowym podzbiorze zasobów sprawdza poprawność zgodnie z oczekiwaniami, powoli rozszerzaj ocenę do wszystkich istniejących zasobów.

Ocena istniejących zasobów w ten sposób daje również możliwość korygowania niezgodnych zasobów przed pełnym wdrożeniem nowej polityki. To oczyszczanie można wykonać ręcznie lub za pomocą [zadania korygowania,](../how-to/remediate-resources.md) jeśli efektem definicji zasad jest _DeployIfNotExists_.

## <a name="audit-new-or-updated-resources"></a>Inspekcja nowych lub zaktualizowanych zasobów

Po sprawdzeniu poprawności nowa definicja zasad jest raportowanie poprawnie istniejących zasobów, nadszedł czas, aby przyjrzeć się wpływ zasad, gdy zasoby są tworzone lub aktualizowane. Jeśli definicja zasad obsługuje parametryzację efektów, należy użyć opcji [Inspekcja](./effects.md#audit). Ta konfiguracja umożliwia monitorowanie tworzenia i aktualizowania zasobów, aby sprawdzić, czy nowa definicja zasad wyzwala wpis w dzienniku aktywności platformy Azure dla zasobu, który jest niezgodny bez wpływu na istniejącą pracę lub żądania.

Zaleca się zarówno zaktualizować i utworzyć nowe zasoby, które pasują do definicji zasad, aby zobaczyć, że _efekt inspekcji_ jest poprawnie wyzwalane, gdy oczekuje. Bądź na poszukiwania żądań zasobów, które nie powinny mieć wpływu na nową definicję zasad, które wyzwalają _efekt inspekcji._
Te zasoby, których dotyczy problem, są kolejnym przykładem _fałszywych alarmów_ i muszą zostać ustalone w definicji zasad przed pełną implementacją.

W przypadku zmiany definicji zasad na tym etapie testowania zaleca się rozpoczęcie procesu sprawdzania poprawności wraz z inspekcją istniejących zasobów. Zmiana definicji zasad dla _fałszywie dodatniego_ dla nowych lub zaktualizowanych zasobów może mieć również wpływ na istniejące zasoby.

## <a name="deploy-your-policy-to-resources"></a>Wdrażanie zasad w zasobach

Po zakończeniu sprawdzania poprawności nowej definicji zasad z istniejącymi zasobami i nowymi lub zaktualizowanymi żądaniami zasobów rozpoczynasz proces wdrażania zasad. Zaleca się najpierw utworzenie przypisania zasad dla nowej definicji zasad do podzbioru wszystkich zasobów, takich jak grupa zasobów. Po sprawdzeniu poprawności wdrożenia początkowego należy rozszerzyć zakres zasad na szersze i szersze poziomy, takie jak subskrypcje i grupy zarządzania. To rozszerzenie jest osiągane przez usunięcie przypisania i utworzenie nowego w zakresach docelowych, dopóki nie zostanie przypisany do pełnego zakresu zasobów przeznaczonych do objęcia nową definicją zasad.

Podczas wdrażania, jeśli znajdują się zasoby, które powinny być wyłączone z nowej definicji zasad, należy je rozwiązać w jeden z następujących sposobów:

- Aktualizowanie definicji zasad w celu zwiększenia ich wyraźnego stanu, aby zmniejszyć niezamierzony wpływ
- Zmienianie zakresu przypisania zasad (przez usunięcie i utworzenie nowego przypisania)
- Dodawanie grupy zasobów do listy wykluczeń dla przypisania zasad

Wszelkie zmiany zakresu (poziom lub wykluczenia) powinny być w pełni zweryfikowane i komunikowane z organizacjami zabezpieczeń i zgodności, aby upewnić się, że nie ma żadnych luk w zasięgu.

## <a name="monitor-your-policy-and-compliance"></a>Monitorowanie zasad i zgodności

Implementowanie i przypisywanie definicji zasad nie jest ostatnim krokiem. Stale monitoruj poziom [zgodności](../how-to/get-compliance-data.md) zasobów do nowej definicji zasad i konfiguruj odpowiednie [alerty i powiadomienia usługi Azure Monitor,](../../../azure-monitor/platform/alerts-overview.md) gdy są identyfikowane urządzenia niezgodne. Zaleca się również, aby ocenić definicję zasad i powiązanych przydziałów na podstawie zaplanowanej, aby sprawdzić poprawność definicji zasad jest spełnienie zasad biznesowych i zgodności potrzeb. Zasady powinny zostać usunięte, jeśli nie są już potrzebne. Zasady również wymagają aktualizacji od czasu do czasu, jak podstawowe zasoby platformy Azure ewoluować i dodać nowe właściwości i możliwości.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [strukturze definicji zasad](./definition-structure.md).
- Dowiedz się więcej o [strukturze przypisywania zasad](./assignment-structure.md).
- Dowiedz się, jak [programowo tworzyć zasady](../how-to/programmatically-create.md).
- Dowiedz się, jak [uzyskać dane dotyczące zgodności](../how-to/get-compliance-data.md).
- Dowiedz się, jak [korygować niezgodne zasoby](../how-to/remediate-resources.md).
- Sprawdź, czym jest grupa zarządzania, [organizuj swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).