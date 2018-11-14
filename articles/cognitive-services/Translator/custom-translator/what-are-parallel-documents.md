---
title: Co to są równoległe dokumenty? — Niestandardowy w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Równoległe dokumenty są pary dokumenty, gdy jest ona tłumaczenia, z drugiej strony. Jeden dokument w parze zawiera zdań w języku źródła i innego dokumentu zawiera te zdania przetłumaczony na język docelowy.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: e9214314bab40e3ce402fed84f429f3542519240
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51627687"
---
# <a name="what-are-parallel-documents"></a>Co to są równoległe dokumenty?

Równoległe dokumenty są pary dokumenty, gdy jest ona tłumaczenia, z drugiej strony. Jeden dokument w parze zawiera zdań w języku źródła i innego dokumentu zawiera te zdania przetłumaczony na język docelowy.
Nieważne, jaki język jest oznaczony jako "źródło" i język, który jest oznaczony jako "target" — równoległe dokumentu może służyć do szkolić system tłumaczeń w dowolnym kierunku.

## <a name="use-of-parallel-documents"></a>Korzystanie z dokumentów równoległe

Równoległe dokumentów są używane przez system:

1.  Aby dowiedzieć się, jak słów i fraz zdania często są mapowane między dwa języki.

2.  Aby dowiedzieć się, jak przetwarzać odpowiedniego kontekstu, w zależności od otaczającego wyrażenia. Słowo może nie zawsze wykonuje translację elementu do dokładnie tego samego wyrazu w innym języku.

Najlepszym rozwiązaniem upewnij się, to połączenie zdania 1:1 między źródłem i język docelowy wersje dokumentów.

Przekazane dokumenty prywatne do każdego obszaru roboczego i mogą być używane w dowolną liczbę projektów lub szkoleniach, jak chcesz. Zdania wyodrębnione ze swoich dokumentów są przechowywane osobno w repozytorium jako zwykłe pliki tekstowe Unicode i są dostępne do usuwania. Nie należy używać niestandardowego w usłudze Translator jako repozytorium dokumentów, nie można pobrać dokumenty przekazany w formacie został przekazany.

## <a name="recommendations"></a>Zalecenia

Jeśli projekt jest określonych dla domeny (kategoria), dokumenty powinny być zgodne w terminologii w ramach tej kategorii. Jakość wynikowy system tłumaczeń zależy od tego, liczbę zdań w zestawie dokumentów oraz określa jego jakość zdania. Więcej przykładów dokumentów zawierają z różnych wartości użycia wyrazu specyficzne dla kategorii, lepiej sobie radzą systemu można zrobić podczas tłumaczenia.

Należy co najmniej 10 000 równoległe zdania w celu szkolić system. Najlepszym rozwiązaniem jest stale można dodać większej ilości zawartości równoległe i ponownego próbkowania w celu poprawiania jakości tłumaczenia systemu.

Firma Microsoft wymaga, dokumenty przekazane do niestandardowych w usłudze Translator naruszają właściwości praw autorskich lub własności intelektualnej innych firm. Aby uzyskać więcej informacji, zobacz [warunki użytkowania](https://azure.microsoft.com/support/legal/cognitive-services-terms/).
Przekazywanie dokumentu za pomocą portalu nie zmienia własności intelektualnej w dokumencie.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak używać [słownika](what-is-dictionary.md) w niestandardowych w usłudze Translator.