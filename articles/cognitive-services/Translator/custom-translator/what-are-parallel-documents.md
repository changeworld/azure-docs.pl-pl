---
title: Co to są dokumenty równoległe? - Tłumacz niestandardowy
titleSuffix: Azure Cognitive Services
description: Dokumenty równoległe są parami dokumentów, w których jedna jest tłumaczeniem drugiego. Jeden dokument w parze zawiera zdania w języku źródłowym, a drugi zawiera te zdania przetłumaczone na język docelowy.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: d7c38a44e3111a319e4146b3c9b71a22b0d31bfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "72675465"
---
# <a name="what-are-parallel-documents"></a>Co to są dokumenty równoległe?

Dokumenty równoległe są parami dokumentów, w których jedna jest tłumaczeniem drugiego. Jeden dokument w parze zawiera zdania w języku źródłowym, a drugi zawiera te zdania przetłumaczone na język docelowy.
Nie ma znaczenia, który język jest oznaczony jako "źródło", a który jest oznaczony jako "cel" - dokument równoległy może służyć do szkolenia systemu tłumaczenia w obu kierunkach.

## <a name="requirements"></a>Wymagania

Aby wyszkolić system, potrzebne będzie co najmniej 10 000 unikatowych zdań równoległych wyrównanych. To ograniczenie jest siatką bezpieczeństwa, aby upewnić się, że zdania równoległe zawierają wystarczającą ilość unikalnego słownictwa, aby skutecznie wyszkolić model tłumaczenia. Najlepszym rozwiązaniem jest ciągłe dodawanie większej ilości zawartości równoległej i przekwalifikowanie w celu poprawy jakości systemu tłumaczenia. Zapoznaj się z [ustawieniami zdania](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/sentence-alignment).

Firma Microsoft wymaga, aby dokumenty przesłane do Translatora niestandardowego nie naruszały praw autorskich ani własności intelektualnej osób trzecich. Aby uzyskać więcej informacji, zapoznaj się [z Warunkami użytkowania](https://azure.microsoft.com/support/legal/cognitive-services-terms/).
Przesłanie dokumentu za pomocą portalu nie zmienia własności własności intelektualnej w samym dokumencie.

## <a name="use-of-parallel-documents"></a>Korzystanie z dokumentów równoległych

Dokumenty równoległe są używane przez system:

1.  Aby dowiedzieć się, jak słowa, frazy i zdania są często mapowane między dwoma językami.

2.  Aby dowiedzieć się, jak przetworzyć odpowiedni kontekst w zależności od otaczających fraz. Słowo nie zawsze może tłumaczyć się dokładnie na to samo słowo w innym języku.

Najlepszym rozwiązaniem jest upewnienie się, że istnieje korespondencja zdań 1:1 między źródłową i docelową wersją językową dokumentów.

Jeśli projekt jest specyficzny dla domeny (kategorii), dokumenty powinny być spójne w terminologii w tej kategorii. Jakość powstałego systemu tłumaczenia zależy od liczby zdań w zestawie dokumentów i jakości zdań. Im więcej przykładów dokumentów zawiera z różnorodnymi zwyczajami dla słowa specyficznego dla danej kategorii, tym lepsze zadanie, które system może wykonać podczas tłumaczenia.

Przesłane dokumenty są prywatne w każdym obszarze roboczym i mogą być używane w tylu projektach lub szkoleniach, ile chcesz. Zdania wyodrębnione z dokumentów są przechowywane oddzielnie w repozytorium jako zwykłe pliki tekstowe Unicode i są dostępne do usunięcia. Nie używaj tłumacza niestandardowego jako repozytorium dokumentów, nie będzie można pobrać przesłanych dokumentów w przesłanym formacie.



## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak używać [słownika](what-is-dictionary.md) w umykaczu niestandardowym.
