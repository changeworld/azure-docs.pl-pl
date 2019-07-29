---
title: Co to są dokumenty równoległe? — Niestandardowy w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Dokumenty równoległe to pary dokumentów, które są tłumaczeniami innych. Jeden dokument w parze zawiera zdania w języku źródłowym, a drugi dokumentu zawiera te zdania tłumaczone na język docelowy.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: fb54df2e1eb89d30e62ae80355635356343994ee
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595448"
---
# <a name="what-are-parallel-documents"></a>Co to są dokumenty równoległe?

Dokumenty równoległe to pary dokumentów, które są tłumaczeniami innych. Jeden dokument w parze zawiera zdania w języku źródłowym, a drugi dokumentu zawiera te zdania tłumaczone na język docelowy.
Nie ma znaczenia, który język jest oznaczony jako "Źródło" i który język jest oznaczony jako "target" — dokument równoległy może służyć do uczenia systemu tłumaczenia w dowolnym kierunku.

## <a name="requirements"></a>Wymagania

Do uczenia systemu będzie wymagane co najmniej 10 000 unikatowych zdań równoległych. Najlepszym rozwiązaniem jest ciągłe dodawanie bardziej równoległych zawartości i ponowne uczenie w celu zwiększenia jakości systemu tłumaczenia.

Firma Microsoft wymaga, aby dokumenty przekazane do translatora niestandardowego nie naruszają praw autorskich lub własności intelektualnych osób trzecich. Aby uzyskać więcej informacji, zobacz [warunki użytkowania](https://azure.microsoft.com/support/legal/cognitive-services-terms/).
Przekazywanie dokumentu przy użyciu portalu nie zmienia własności własności intelektualnej w samym dokumencie.

## <a name="use-of-parallel-documents"></a>Korzystanie z dokumentów równoległych

Dokumenty równoległe są używane przez system:

1.  Aby dowiedzieć się, jak wyrazy, wyrażenia i zdania są zwykle mapowane między tymi dwoma językami.

2.  Aby dowiedzieć się, jak przetworzyć odpowiedni kontekst w zależności od otaczających fraz. Słowo może nie zawsze być tłumaczone na dokładnie ten sam wyraz w innym języku.

Najlepszym rozwiązaniem jest upewnienie się, że istnieje zgodność z 1:1 zdania między źródłową i docelową wersją językową dokumentów.

Jeśli projekt jest specyficzny dla domeny (kategoria), dokumenty powinny być spójne w terminologii w tej kategorii. Jakość uzyskanego systemu tłumaczeń zależy od liczby zdań w zestawie dokumentów oraz jakości zdań. Im więcej przykładów zawierają dokumenty z różnymi użyciami dla wyrazów określonych dla danej kategorii, tym lepszym zadaniem może być system podczas tłumaczenia.

Przekazane dokumenty są prywatne dla każdego obszaru roboczego i mogą być używane w dowolnej liczbie projektów lub szkoleń. Zdania wyodrębnione z dokumentów są przechowywane oddzielnie w repozytorium jako zwykłe pliki tekstowe w formacie Unicode i są dostępne do usunięcia. Nie należy używać translatora niestandardowego jako repozytorium dokumentów, ale nie będzie można pobrać dokumentów przekazanych w formacie, który został przekazany.



## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak używać [słownika](what-is-dictionary.md) w translatoru niestandardowym.
