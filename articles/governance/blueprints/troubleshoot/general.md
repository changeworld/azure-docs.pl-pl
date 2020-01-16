---
title: Rozwiązywanie typowych problemów
description: Dowiedz się, jak rozwiązywać problemy z tworzeniem, przypisywaniem i usuwaniem planów, takich jak naruszenia zasad i funkcje parametrów planu.
ms.date: 11/22/2019
ms.topic: troubleshooting
ms.openlocfilehash: 5b8a20b0757934bbd356ab037a22521a248a7eb2
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982491"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Rozwiązywanie problemów z błędami przy użyciu planów platformy Azure

Podczas tworzenia lub przypisywania planów mogą wystąpić błędy. W tym artykule opisano różne błędy, które mogą wystąpić i sposoby ich rozwiązywania.

## <a name="finding-error-details"></a>Znajdowanie szczegółów błędu

Wiele błędów będzie wynikiem przypisywania strategii do zakresu. Jeśli przypisanie nie powiedzie się, plan zawiera szczegółowe informacje dotyczące niepowodzenia wdrożenia. Te informacje wskazują, że problem może zostać naprawiony, a następne wdrożenie powiodło się.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Wybierz pozycję **przypisane plany** na stronie z lewej strony i użyj pola wyszukiwania, aby odfiltrować przypisania strategii w celu znalezienia nieudanego przypisania. Możesz również posortować tabelę przypisań według kolumny **stan aprowizacji** , aby zobaczyć wszystkie niezakończone przydziały zgrupowane razem.

1. Kliknij prawym przyciskiem myszy plan o stanie _Niepowodzenie_ lub kliknij, a następnie wybierz pozycję **Wyświetl szczegóły przydziału**.

1. Ostrzeżenie o czerwoniu baneru, którego przypisanie nie powiodło się, znajduje się w górnej części strony przypisanie strategii. Kliknij gdziekolwiek na banerze, aby uzyskać więcej szczegółów.

Często przyczyną błędu może być artefakt, a nie plan jako całość. Jeśli artefakt tworzy Key Vault i Azure Policy zapobiega tworzeniu Key Vault, całe przypisanie zakończy się niepowodzeniem.

## <a name="general-errors"></a>Błędy ogólne

### <a name="policy-violation"></a>Scenariusz: naruszenie zasad

#### <a name="issue"></a>Problem

Wdrożenie szablonu nie powiodło się z powodu naruszenia zasad.

#### <a name="cause"></a>Przyczyna

Zasady mogą powodować konflikt z wdrożeniem z kilku powodów:

- Tworzony zasób jest ograniczony przez zasady (zazwyczaj ograniczenia jednostki SKU lub lokalizacji)
- Wdrożenie jest ustawieniem pól skonfigurowanych za pomocą zasad (wspólne ze znacznikami)

#### <a name="resolution"></a>Rozdzielczość

Zmień plan, aby nie powodował konfliktu z zasadami w szczegółach błędu. Jeśli ta zmiana nie jest możliwa, alternatywna opcja polega na tym, że zakres przypisania zasad został zmieniony, w związku z czym plan nie jest już w konflikcie z zasadami.

### <a name="escape-function-parameter"></a>Scenariusz: parametr strategii jest funkcją

#### <a name="issue"></a>Problem

Parametry planu, które są funkcjami są przetwarzane przed przekazaniem do artefaktów.

#### <a name="cause"></a>Przyczyna

Przekazywanie parametru planu, który używa funkcji, takiej jak `[resourceGroup().tags.myTag]`, do artefaktu skutkuje przetworzonym wynikiem funkcji ustawionej dla artefaktu zamiast funkcji dynamicznej.

#### <a name="resolution"></a>Rozdzielczość

Aby przekazać funkcję przez parametr, należy wypróbować cały ciąg z `[` w taki sposób, aby parametr strategii wyglądał jak `[[resourceGroup().tags.myTag]`. Znak ucieczki powoduje, że plany traktują wartość jako ciąg podczas przetwarzania planu. Plany następnie umieszczają funkcję na artefaktie, umożliwiając jej dynamiczne działanie zgodnie z oczekiwaniami. Aby uzyskać więcej informacji, zobacz [składnia i wyrażenia w szablonach Azure Resource Manager](../../../azure-resource-manager/templates/template-expressions.md).

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

- Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [forów platformy Azure](https://azure.microsoft.com/support/forums/).
- Połącz się z kontem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnym kontem platformy Microsoft Azure utworzonym w celu podniesienia jakości obsługi klientów przez połączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
- Jeśli potrzebujesz więcej pomocy, możesz obsłużyć zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną**.