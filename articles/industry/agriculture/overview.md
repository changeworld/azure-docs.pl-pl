---
title: Omówienie usługi Azure FarmBeats
description: Zawiera omówienie usługi Azure FarmBeats
author: uhabiba04
ms.topic: overview
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 12882860b8a9beadd4e56cbb151d670fac4da43b
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73798697"
---
# <a name="overview-of-azure-farmbeats"></a>Omówienie usługi Azure FarmBeats

Azure FarmBeats to zbiór usług i możliwości platformy Azure, które ułatwiają szybkie tworzenie inteligentnych rozwiązań opartych na danych w rolnictwie. Azure FarmBeats to oferta portalu Azure Marketplace, która umożliwia uzyskiwanie, agregowanie i przetwarzanie danych związanych z rolnictwem z różnych źródeł, takich jak czujniki, dronom, kamery, satelity, bez inwestowania w szczegółowe zasoby inżynierów danych.

> [!NOTE]
> Usługa Azure FarmBeats jest obecnie dostępna w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Usługa Azure FarmBeats jest świadczona bez umowy dotyczącej poziomu usług. Skorzystaj z [Forum usługi Azure FarmBeats](https://aka.ms/FarmBeatsMSDN ) , aby uzyskać pomoc techniczną.

Korzystając z usługi Azure FarmBeats, będziesz mieć możliwość pobierania danych z różnych źródeł, takich jak czujniki, satelity, dronom, wszystkie w kontekście farmy (obszar geograficzny zainteresowania).

Będzie można:

- Agregowanie rolniczych zestawów danych z różnych dostawców
- Szybko Kompiluj sztuczne modele analizy/maszyn (AI), odrzucając różne zestawy danych

Usługa Azure FarmBeats zapewnia niezawodny i łatwy sposób wykonywania następujących czynności:

- Utwórz mapę dla farmy przy użyciu pliku GEOJSON.
- Oceniaj kondycję farmy przy użyciu indeksu roślinności i indeksu wodnego na podstawie obrazów satelitarnych.
- Zapoznaj się z zaleceniami dotyczącymi liczby czujników do użycia i miejsca, w których należy je umieścić.
- Śledź warunek farmy, wizualizowając dane naziemne zbierane przez czujniki od różnych dostawców czujników.
- Pobierz mapę wilgotności gleby na podstawie łączenia danych satelitarnych i czujników.
- Uzyskaj szczegółowe informacje umożliwiające podjęcie odpowiednich działań, tworząc modele AI/ML na agregacji zagregowanych zestawów danych.
- Utwórz lub Uzupełnij rozwiązanie do elektronicznego rolnictwu, dostarczając doradcy kondycji farmy.

Składniki usługi Azure FarmBeat zostały omówione w poniższych sekcjach tego artykułu.

## <a name="data-hub"></a>Centrum danych

Warstwa interfejsu API, która umożliwia agregację, normalizację i contextualization różnych zestawów danych rolnictwa między dostawcami. W ramach tej wersji zapoznawczej można korzystać z dwóch dostawców czujników, [narzędzi](https://www.davisinstruments.com/product/enviromonitor-gateway/), [Teralytic](https://teralytic.com/) [, jednego dostawcy obrazów](https://sentinel.esa.int/web/sentinel/home)satelitarnych i dwóch dostawców obrazów drona [senseFly](https://www.sensefly.com/) , [SlantRange](https://slantrange.com/). Usługa Data Hub jest zaprojektowana jako platforma interfejsu API i pracujemy nad wieloma dostawcami, aby zintegrować się z usługą Azure FarmBeats, dzięki czemu możesz wybierać więcej informacji podczas kompilowania rozwiązania.

## <a name="accelerator"></a>Skrót

Przykładowe rozwiązanie, które jest oparte na centrum danych, które Jumpstarts interfejs użytkownika i projektowanie modeli. Ta aplikacja sieci Web wykorzystuje interfejsy API, aby przedstawić wizualizację pozyskiwanych danych z czujnika jako wykresów i wizualizacji wyników modelu jako mapy. Można na przykład użyć akceleratora, aby szybko utworzyć farmę i łatwo uzyskać mapę indeksu roślinności lub mapę rozmieszczenia czujnika dla tej farmy.

## <a name="resources"></a>Zasoby

Odwiedź [blog](https://aka.ms/AzureFarmBeats) i [fora](https://aka.ms/FarmBeatsMSDN)dotyczące FarmBeats.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z usługą Azure FarmBeats, odwiedź witrynę [Azure Marketplace](https://aka.ms/FarmBeatsMarketplace) , aby ją wdrożyć.
