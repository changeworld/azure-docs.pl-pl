---
title: Wykresy przepływu danych
description: Jak korzystać z wykresów przepływu danych w usłudze Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/04/2019
ms.openlocfilehash: da180bfb1aec29fa15b070fd73ba84d708ada927
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928305"
---
# <a name="mapping-data-flow-graphs"></a>Mapowanie wykresów przepływu danych

Na powierzchni projektowej mapowania przepływów danych jest powierzchnia "konstrukcja", w której tworzysz przepływy danych w dół, od lewej do prawej. Istnieje Przybornik dołączony do każdego przekształcenia ze znakiem plus (+). Skoncentrowanie się na logice biznesowej zamiast łączenia węzłów za pomocą krawędzi w wolnym środowisku DAG.

Poniżej przedstawiono wbudowane mechanizmy zarządzania wykresem przepływu danych.

## <a name="move-nodes"></a>Przenoszenie węzłów

![Agregacja opcji transformacji](media/data-flow/agghead.png "Nagłówek agregatora")

Bez modelu typu "przeciągnij i upuść" sposób "Przenieś" węzeł przekształcenia polega na zmianie strumienia przychodzącego. Zamiast tego przechodzą przekształcenia, zmieniając "strumień przychodzący".

## <a name="streams-of-data-inside-of-data-flow"></a>Strumienie danych w przepływie danych

W przepływie danych Azure Data Factory strumienie reprezentują przepływ danych. W okienku Ustawienia transformacji zobaczysz pole "strumień przychodzący". Oznacza to, że przychodzący strumień danych ma na celu przekształcenie. Lokalizację fizyczną węzła transformacji można zmienić na wykresie, klikając nazwę strumienia przychodzącego i wybierając inny strumień danych. Bieżąca transformacja wraz ze wszystkimi kolejnymi transformacjemi w tym strumieniu zostanie przeniesiona do nowej lokalizacji.

Jeśli przenosisz transformację przy użyciu co najmniej jednego przekształcenia po nim, Nowa lokalizacja w przepływie danych zostanie przyłączona za pośrednictwem nowej gałęzi.

Jeśli po wybranym węźle nie ma żadnych kolejnych przekształceń, tylko ta transformacja zostanie przeniesiona do nowej lokalizacji.

## <a name="hide-graph-and-show-graph"></a>Ukryj wykres i Pokaż wykres

Istnieje przycisk w prawym górnym rogu dolnego okienka konfiguracji, w którym można rozwinąć dolne okienko do pełnego ekranu podczas pracy z konfiguracjami transformacji. Umożliwi to nawigowanie po konfiguracjach grafu za pomocą przycisków "Previous" i "dalej". Aby wrócić do widoku wykresu, kliknij przycisk w dół i wróć do ekranu podzielonego.

## <a name="search-graph"></a>Wyszukaj wykres

Wykres można wyszukać na powierzchni projektowej.

![Wyszukiwanie](media/data-flow/search001.png "Wyszukaj wykres")

## <a name="next-steps"></a>Następne kroki

Po ukończeniu projektu przepływu danych Włącz przycisk Debuguj i przetestuj go w trybie debugowania bezpośrednio w [Projektancie przepływu danych](concepts-data-flow-debug-mode.md) lub w [debugowaniu potoku](control-flow-execute-data-flow-activity.md).
