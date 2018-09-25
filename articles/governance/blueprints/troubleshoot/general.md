---
title: Rozwiązywanie problemów przy użyciu planów usługi Azure
description: Dowiedz się, jak rozwiązywać problemy dotyczące tworzenia i przypisywania plany
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: dd1163ece225c2e9a9b082f5e8364f34b06a10ae
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982294"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Rozwiązywanie problemów przy użyciu planów usługi Azure

Mogą wystąpić błędy, podczas tworzenia lub przypisywanie schematy. W tym artykule opisano różne błędy, które mogą występować i ich rozwiązania.

## <a name="finding-error-details"></a>Znajdowanie szczegóły błędu

Wiele błędów będzie wynik przypisanie planu do zakresu. Jeśli przypisanie zakończy się niepowodzeniem, plan zapewnia szczegółowe informacje o wdrożenie nie powiodło się. Te informacje będą wskazywać problem, aby można było usunąć i kolejnym wdrożeniu zostanie wykonane pomyślnie.

1. Uruchom usługę Azure schematy w witrynie Azure portal, klikając **wszystkich usług** wyszukiwanie i wybieranie **zasad** w okienku po lewej stronie. Na **zasad** kliknij na **plany**.

1. Wybierz **przypisane plany** ze strony po lewej stronie i stosowania pole wyszukiwania, aby filtrować przypisań planu można znaleźć przypisania nie powiodło się. Możesz również sortować tabelę przypisania według **stan inicjowania obsługi** kolumny, aby wyświetlić wszystkie nieudane przypisania zgrupowane.

1. Kliknięcie lewym przyciskiem myszy na planu z _niepowodzenie_ stanu lub kliknij prawym przyciskiem myszy i wybierz **Wyświetl szczegóły przydziału**.

1. W górnej części planu przypisania strona jest czerwonego banera, ostrzeżenie, że przypisanie nie powiodło się. Kliknij w dowolnym miejscu na banerze, aby uzyskać więcej szczegółów.

Bardzo często błąd spowodowane artefakt objęte planu, a nie planu jako całości. Na przykład jeśli schemat zawiera artefakty do tworzenia usługi Key Vault, ale Tworzenie usługi Key Vault jest chroniony przez zasady usługi Azure, cały przypisanie zakończy się niepowodzeniem.

## <a name="general-errors"></a>Ogólne błędy

### <a name="policy-violation"></a>Scenariusz: Naruszenie zasad

#### <a name="issue"></a>Problem

Wdrożenie szablonu nie powiodło się z powodu naruszenia zasad.

#### <a name="cause"></a>Przyczyna

Zasady może spowodować konflikt z wdrożenia z kilku powodów:

- Zasobów tworzona jest ograniczona przez zasady (często ograniczenia jednostki SKU lub lokalizację)
- Wdrożenie jest ustawienie pola, które zostały skonfigurowane przez zasady (częsty przy użyciu tagów)

#### <a name="resolution"></a>Rozwiązanie

Dostosuj planu nie są w konflikcie z zasadami, informacje o błędzie na liście. Jeśli nie jest to możliwe, alternatywnych opcji jest zakres przypisania zasad zmienione, aby planu nie jest już jest w konflikcie z zasadami.

## <a name="next-steps"></a>Kolejne kroki

Jeśli nie została uwzględniona w rozwiązaniu problemu, lub nie można rozwiązać problem, odwiedź jedną z następujących kanałów obsługi więcej:

- Uzyskaj odpowiedzi od ekspertów w zakresie platformy Azure na [forach dotyczących platformy Azure](https://azure.microsoft.com/support/forums/)
- Połącz się z kontem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnym kontem platformy Microsoft Azure utworzonym w celu podniesienia jakości obsługi klientów przez połączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
- Jeśli potrzebujesz więcej pomocy, mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz **uzyskiwanie pomocy technicznej**.