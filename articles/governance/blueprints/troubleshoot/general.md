---
title: Rozwiązywanie typowych problemów
description: Dowiedz się, jak rozwiązywać problemy z tworzeniem, przypisywaniem i usuwaniem planów.
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/11/2018
ms.topic: troubleshooting
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 8cbefcbadc14c1249d2783f1539e40c99c3be66c
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231571"
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

### <a name="policy-violation"></a>Scenariusz Naruszenie zasad

#### <a name="issue"></a>Problem

Wdrożenie szablonu nie powiodło się z powodu naruszenia zasad.

#### <a name="cause"></a>Przyczyna

Zasady mogą powodować konflikt z wdrożeniem z kilku powodów:

- Tworzony zasób jest ograniczony przez zasady (zazwyczaj ograniczenia jednostki SKU lub lokalizacji)
- Wdrożenie jest ustawieniem pól skonfigurowanych za pomocą zasad (wspólne ze znacznikami)

#### <a name="resolution"></a>Rozwiązanie

Zmień plan, aby nie powodował konfliktu z zasadami w szczegółach błędu. Jeśli ta zmiana nie jest możliwa, alternatywna opcja polega na tym, że zakres przypisania zasad został zmieniony, w związku z czym plan nie jest już w konflikcie z zasadami.

### <a name="escape-function-parameter"></a>Scenariusz Parametr strategii jest funkcją

#### <a name="issue"></a>Problem

Parametry planu, które są funkcjami są przetwarzane przed przekazaniem do artefaktów.

#### <a name="cause"></a>Przyczyna

Przekazywanie parametru planu, który używa funkcji, `[resourceGroup().tags.myTag]`na przykład, do artefaktu, powoduje przetworzony wynik funkcji ustawionej dla artefaktu zamiast funkcji dynamicznej.

#### <a name="resolution"></a>Rozwiązanie

Aby przekazać funkcję przez parametr, należy wypróbować cały ciąg w `[` taki sposób, aby parametr strategii `[[resourceGroup().tags.myTag]`wyglądał następująco. Znak ucieczki powoduje, że plany traktują wartość jako ciąg podczas przetwarzania planu. Plany następnie umieszczają funkcję na artefaktie, umożliwiając jej dynamiczne działanie zgodnie z oczekiwaniami. Aby uzyskać więcej informacji, zobacz [Struktura pliku szablonu — składnia](../../../azure-resource-manager/resource-group-authoring-templates.md#syntax).

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

- Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [forów platformy Azure](https://azure.microsoft.com/support/forums/).
- Połącz się z kontem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnym kontem platformy Microsoft Azure utworzonym w celu podniesienia jakości obsługi klientów przez połączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
- Jeśli potrzebujesz więcej pomocy, możesz obsłużyć zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną**.