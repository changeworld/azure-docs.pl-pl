---
title: Usługi Azure Event Hubs — wyjątki Menedżera zasobów | Dokumenty firmy Microsoft
description: Lista wyjątków usługi Azure Event Hubs ujmowanych przez usługę Azure Resource Manager i sugerowane akcje.
services: service-bus-messaging
documentationcenter: na
author: spelluru
editor: spelluru
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/08/2019
ms.author: spelluru
ms.openlocfilehash: e6ee1137fce97cbe5a64aa5287223f6ba09dcf47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74936089"
---
# <a name="azure-event-hubs---resource-manager-exceptions"></a>Usługi Azure Event Hubs — wyjątki Menedżera zasobów
W tym artykule wymieniono wyjątki generowane podczas interakcji z usługą Azure Event Hubs przy użyciu usługi Azure Resource Manager — za pośrednictwem szablonów lub bezpośrednich wywołań.

> [!IMPORTANT]
> Ten dokument jest często aktualizowany. Sprawdź aktualizacje.

W poniższych sekcjach przedstawiono różne wyjątki/błędy, które są uwydrone za pośrednictwem usługi Azure Resource Manager.

## <a name="error-code-conflict"></a>Kod błędu: Konflikt

| Kod błędu | Podkod błędu | Komunikat o błędzie | Opis | Zalecenie |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Konflikt | 40300 | Osiągnięto lub przekroczono maksymalną liczbę zasobów typu EventHub. Rzeczywisty: #, Max dozwolone: # | Obszar nazw osiągnął [przydział](event-hubs-quotas.md) dla liczby centrów zdarzeń, które może zawierać. | Usuń wszystkie nieużywane lub obce centra zdarzeń z obszaru nazw lub rozważ uaktualnienie do [dedykowanego klastra](event-hubs-dedicated-overview.md). |
| Konflikt | brak | Nie można usunąć konfiguracji odzyskiwania po awarii (DR), ponieważ replikacja jest w toku. Przed próbą usunięcia konfiguracji dr. DR config można je przewinąć w tryb fail over lub przerwać. | [Replikacja GeoDR](event-hubs-geo-dr.md) jest w toku, więc konfiguracji nie można usunąć w tej chwili. | Aby odblokować usunięcie konfiguracji, poczekaj na zakończenie replikacji, wyzwij przełącz się w tryb failover lub przerwij parowanie GeoDR. |
| Konflikt | brak | Aktualizacja obszaru nazw nie powiodła się z konfliktem w wewnętrznej bazy danych. | Inna operacja jest obecnie wykonywana w tej przestrzeni nazw. | Poczekaj na zakończenie bieżącej operacji, a następnie ponów próbę. |

## <a name="error-code-429"></a>Kod błędu: 429

| Kod błędu | Podkod błędu | Komunikat o błędzie | Opis | Zalecenie |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | brak | Inicjowanie obsługi administracyjnej obszaru nazw w okresie przejściowym | Inna operacja jest obecnie wykonywana w tej przestrzeni nazw. | Poczekaj na zakończenie bieżącej operacji, a następnie ponów próbę. |
| 429 | brak | Trwa operacja odzyskiwania po awarii. | Operacja [GeoDR](event-hubs-geo-dr.md) jest obecnie wykonywana w tym obszarze nazw lub parowaniu. | Poczekaj na zakończenie bieżącej operacji GeoDR, a następnie ponów próbę. |

## <a name="error-code-badrequest"></a>Kod błędu: BadRequest

| Kod błędu | Podkod błędu | Komunikat o błędzie | Opis | Zalecenie |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| BadRequest | 40000 | Nie można zmienić konta na partycje dla centrum zdarzeń. | Podstawowa lub standardowa warstwa usługi Azure Event Hubs nie obsługuje zmieniających się partycji. | Utwórz nowe centrum zdarzeń z pożądaną liczbą partycji w obszarze nazw warstwy podstawowej lub standardowej. Skalowanie w poziomie partycji jest obsługiwane dla [dedykowanych klastrów.](event-hubs-dedicated-overview.md) |
| BadRequest | 40000 | Wartość "#" dla MessageRetentionInDays nie jest prawidłowa dla warstwy Podstawowa. wartość nie może przekraczać dnia (1" (dni). | Podstawowe obszary nazw centrum zdarzeń warstwy obsługują tylko przechowywanie komunikatów do 1 dnia. | Jeśli pożądane jest przechowywanie wiadomości więcej niż jeden dzień, [utwórz standardowy obszar nazw Centrum zdarzeń](event-hubs-create.md). | 
| BadRequest | brak | Określona nazwa jest niedostępna. | Nazwy obszaru nazw muszą być unikatowe, a określona nazwa jest już zajęta. | Jeśli jesteś właścicielem istniejącego obszaru nazw o określonej nazwie, możesz go usunąć, co spowoduje utratę danych. Następnie spróbuj ponownie o tej samej nazwie. Jeśli obszar nazw nie jest bezpieczny do usunięcia (lub nie jesteś właścicielem), wybierz inną nazwę obszaru nazw. |
| BadRequest | brak | Określona subskrypcja osiągnęła przydział obszarów nazw. | Subskrypcja osiągnęła [przydział](event-hubs-quotas.md) dla liczby obszarów nazw, które może pomieścić. | Należy rozważyć usunięcie nieużywanych obszarów nazw w tej subskrypcji, utworzenie innej subskrypcji lub uaktualnienie do [dedykowanego klastra.](event-hubs-dedicated-overview.md) |
| BadRequest | brak | Nie można zaktualizować pomocniczego obszaru nazw | Nie można zaktualizować obszaru nazw, ponieważ jest to pomocnicza przestrzeń nazw w [parowaniu geodrów.](event-hubs-geo-dr.md) | W razie potrzeby należy wprowadzić zmiany w podstawowym obszarze nazw w tym parowaniu zamiast tego. W przeciwnym razie przerwać parowanie GeoDR, aby wprowadzić zmiany. |
| BadRequest | brak | Nie można ustawić automatycznego nadmuchania w podstawowej jednostce SKU | Nie można włączyć automatycznego nadmuchiwanie w podstawowych obszarach nazw centrów zdarzeń warstwy podstawowej. | Aby [włączyć automatyczne nadmuchiwanie](event-hubs-auto-inflate.md) w obszarze nazw, upewnij się, że jest to warstwa standardowa. |
| BadRequest | brak | Nie ma wystarczającej pojemności, aby utworzyć obszar nazw. Skontaktuj się z administratorem centrów zdarzeń. | Wybrany region ma pojemność i nie można utworzyć większej liczby obszarów nazw. | Wybierz inny region, w który chcesz pomieścić obszar nazw. |
| BadRequest | brak | Operacji nie można wykonać na typie jednostki "ConsumerGroup", ponieważ "nazwa obszaru nazw" używa warstwy "Basic".  | Podstawowe obszary nazw centrów zdarzeń warstwy mają [quota](event-hubs-quotas.md#event-hubs-basic-and-standard---quotas-and-limits) jednej grupy odbiorców (domyślnie). Tworzenie większej liczby grup odbiorców nie jest obsługiwane. | Kontynuuj korzystanie z domyślnej grupy odbiorców ($Default) lub jeśli potrzebne są dalsze, należy rozważyć użycie obszaru nazw centrum zdarzeń warstwy standardowej. | 
| BadRequest | brak | Obszar nazw "nazwa obszaru nazw" nie istnieje. | Nie można odnaleźć dostarczonego obszaru nazw. | Sprawdź, czy nazwa obszaru nazw jest poprawna i można ją znaleźć w subskrypcji. Jeśli tak nie jest, [utwórz obszar nazw Centrum zdarzeń](event-hubs-create.md). | 
| BadRequest | brak | Właściwość lokalizacji zasobu nie jest zgodna z jego zawierającą obszar nazw. | Tworzenie centrum zdarzeń w określonym regionie nie powiodło się, ponieważ nie było zgodne z regionem obszaru nazw. | Spróbuj utworzyć centrum zdarzeń w tym samym regionie co obszar nazw. | 

## <a name="error-code-internal-server-error"></a>Kod błędu: Wewnętrzny błąd serwera

| Kod błędu | Podkod błędu | Komunikat o błędzie | Opis | Zalecenie |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Błąd serwera wewnętrznego | brak | Wewnętrzny błąd serwera. | Usługa Event Hubs miała błąd wewnętrzny. | Ponów próbę wykonania nieudanej operacji. Jeśli operacja nadal zakończy się niepowodzeniem, skontaktuj się z pomocą techniczną. |