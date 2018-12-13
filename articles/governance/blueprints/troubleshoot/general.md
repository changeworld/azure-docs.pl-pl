---
title: Rozwiązywanie typowych problemów
description: Dowiedz się, jak rozwiązywać problemy dotyczące tworzenia i przypisywania plany
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/25/2018
ms.topic: troubleshooting
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 40668fed2fcc2a04e39fa3a4d7e8e8923c75ae05
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53315531"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Rozwiązywanie problemów przy użyciu planów usługi Azure

Mogą występować błędy podczas tworzenia lub przypisywanie schematy. W tym artykule opisano różne błędy, które mogą występować i ich rozwiązania.

## <a name="finding-error-details"></a>Znajdowanie szczegóły błędu

Wiele błędów będzie wynik przypisanie planu do zakresu. Jeśli przypisanie zakończy się niepowodzeniem, planu zawiera szczegółowe informacje o wdrożenie nie powiodło się. Te informacje wskazują problem, aby można było usunąć i dalej wdrożenie zakończy się pomyślnie.

1. Kliknij pozycję **Wszystkie usługi**, a następnie wyszukaj i wybierz pozycję **Zasady** w okienku po lewej stronie. Na stronie **Zasady** kliknij pozycję **Strategie**.

1. Wybierz **przypisane plany** ze strony po lewej stronie i stosowania pole wyszukiwania, aby filtrować przypisań planu można znaleźć przypisania nie powiodło się. Możesz również sortować tabelę przypisania według **stan inicjowania obsługi** kolumny, aby wyświetlić wszystkie nieudane przypisania zgrupowane.

1. Kliknięcie lewym przyciskiem myszy na planu z _niepowodzenie_ stanu lub kliknij prawym przyciskiem myszy i wybierz **Wyświetl szczegóły przydziału**.

1. Jest czerwonego banera, ostrzeżenie, że przypisanie nie powiodło się w górnej części strony przypisanie planu. Kliknij w dowolnym miejscu na banerze, aby uzyskać więcej szczegółów.

Bardzo często błąd spowodowane artefaktu, a nie planu jako całości. Artefakt tworzy usługę Key Vault i usługi Azure Policy uniemożliwia tworzenie usługi Key Vault, cały przypisanie zakończy się niepowodzeniem.

## <a name="general-errors"></a>Ogólne błędy

### <a name="policy-violation"></a>Scenariusz: Naruszenie zasad

#### <a name="issue"></a>Problem

Wdrożenie szablonu nie powiodło się z powodu naruszenia zasad.

#### <a name="cause"></a>Przyczyna

Zasady może spowodować konflikt z wdrożenia z kilku powodów:

- Zasobów tworzona jest ograniczona przez zasady (często ograniczenia jednostki SKU lub lokalizację)
- Wdrożenie jest ustawienie pola, które zostały skonfigurowane przez zasady (częsty przy użyciu tagów)

#### <a name="resolution"></a>Rozwiązanie

Zmień plan, aby nie koliduje to z zasadami w szczegóły dotyczące błędu. Jeśli ta zmiana nie jest możliwe, alternatywnych opcji jest zakres przypisania zasad zmienione, aby planu nie jest już jest w konflikcie z zasadami.

## <a name="next-steps"></a>Kolejne kroki

Jeśli nie był widoczny problemu lub są w stanie rozwiązać problemu, odwiedź jedną z następujących kanałów obsługi więcej:

- Uzyskaj odpowiedzi od ekspertów w zakresie platformy Azure na [forach dotyczących platformy Azure](https://azure.microsoft.com/support/forums/)
- Połącz się z kontem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnym kontem platformy Microsoft Azure utworzonym w celu podniesienia jakości obsługi klientów przez połączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
- Jeśli potrzebujesz więcej pomocy, mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz **uzyskiwanie pomocy technicznej**.