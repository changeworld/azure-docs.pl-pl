---
title: Rozwiązywanie typowych problemów
description: Dowiedz się, jak rozwiązywać problemy z tworzeniem, przypisywaniem i usuwaniem planów, takich jak naruszenia zasad i funkcje parametrów planu.
ms.date: 01/15/2020
ms.topic: troubleshooting
ms.openlocfilehash: 7306e344a479008a87164a954c4444d375950b0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157087"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Rozwiązywanie problemów z błędami przy użyciu planów platformy Azure

Podczas tworzenia, przypisywania lub usuwania planów mogą wystąpić błędy. W tym artykule opisano różne błędy, które mogą wystąpić i jak je rozwiązać.

## <a name="finding-error-details"></a>Znajdowanie szczegółów błędu

Wiele błędów będzie wynikiem przypisania planu do zakresu. Gdy przypisanie nie powiedzie się, plan zawiera szczegółowe informacje na temat wdrożenia nie powiodło się. Te informacje wskazują problem, dzięki czemu można go rozwiązać, a następne wdrożenie zakończy się pomyślnie.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Wybierz **przypisane plany** ze strony po lewej stronie i użyj pola wyszukiwania, aby filtrować przypisania planu, aby znaleźć nieudane przypisanie. Tabelę przypisań można również sortować według kolumny **Stan inicjowania obsługi administracyjnej,** aby wyświetlić wszystkie nieudane przypisania pogrupowane razem.

1. Kliknij lewym przyciskiem myszy plan ze stanem _Niepowodzenie_ lub kliknij prawym przyciskiem myszy i wybierz polecenie **Wyświetl szczegóły przydziału**.

1. Czerwony baner ostrzegający, że przypisanie nie powiodło się, znajduje się w górnej części strony przypisania planu. Kliknij dowolne miejsce na banerze, aby uzyskać więcej szczegółów.

Często błąd jest spowodowany przez artefakt, a nie plan jako całość. Jeśli artefakt tworzy magazyn kluczy, a usługa Azure Policy uniemożliwia utworzenie usługi Key Vault, całe przypisanie zakończy się niepowodzeniem.

## <a name="general-errors"></a>Błędy ogólne

### <a name="scenario-policy-violation"></a><a name="policy-violation"></a>Scenariusz: Naruszenie zasad

#### <a name="issue"></a>Problem

Wdrożenie szablonu nie powiodło się z powodu naruszenia zasad.

#### <a name="cause"></a>Przyczyna

Zasady mogą być w konflikcie z wdrożeniem z kilku powodów:

- Tworzony zasób jest ograniczony przez zasady (często sku lub ograniczenia lokalizacji)
- Wdrożenie jest ustawienie pól, które są skonfigurowane przez zasady (wspólne z tagami)

#### <a name="resolution"></a>Rozwiązanie

Zmień plan, aby nie kolidować z zasadami w szczegółach błędu. Jeśli ta zmiana nie jest możliwa, alternatywną opcją jest zmiana zakresu przypisania zasad, aby plan nie był już w konflikcie z zasadami.

### <a name="scenario-blueprint-parameter-is-a-function"></a><a name="escape-function-parameter"></a>Scenariusz: Parametr Blueprint jest funkcją

#### <a name="issue"></a>Problem

Parametry planu, które są funkcje są przetwarzane przed przekazaniem do artefaktów.

#### <a name="cause"></a>Przyczyna

Przekazywanie parametru planu, który używa `[resourceGroup().tags.myTag]`funkcji, takich jak , do artefaktu powoduje przetworzony wynik funkcji ustawionej na artefakt zamiast funkcji dynamicznej.

#### <a name="resolution"></a>Rozwiązanie

Aby przekazać funkcję jako parametr, należy uciec `[` od całego ciągu `[[resourceGroup().tags.myTag]`z takim, że parametr blueprint wygląda tak, aby wyglądał . Znak ucieczki powoduje plany traktować wartość jako ciąg podczas przetwarzania planu. Plany następnie umieszcza funkcję na artefakt, dzięki czemu jest dynamiczny zgodnie z oczekiwaniami. Aby uzyskać więcej informacji, zobacz [Składnia i wyrażenia w szablonach usługi Azure Resource Manager](../../../azure-resource-manager/templates/template-expressions.md).

## <a name="delete-errors"></a>Usuwanie błędów

### <a name="scenario-assignment-deletion-timeout"></a><a name="assign-delete-timeout"></a>Scenariusz: Limit czasu usuwania przydziału

#### <a name="issue"></a>Problem

Usunięcie przypisania planu nie jest zakończone.

#### <a name="cause"></a>Przyczyna

Przypisanie planu może utknąć w stanie nienajmowym po usunięciu. Ten stan jest spowodowany, gdy zasoby utworzone przez przydział planu są nadal oczekują na usunięcie lub nie zwracają kodu stanu do planu azure.

#### <a name="resolution"></a>Rozwiązanie

Przypisania planu w stanie nienajmowym są automatycznie **oznaczane niepowodzeniem** po upływie limitu czasu _6 godzin._ Po przesunieniu limitu czasu można ponowić stan przypisania planu, można ponowić usunięcie.

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

- Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [forów platformy Azure](https://azure.microsoft.com/support/forums/).
- Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
- Jeśli potrzebujesz więcej pomocy, możesz zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc **techniczną**.