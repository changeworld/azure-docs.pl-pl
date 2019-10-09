---
title: Przenoszenie węzłów przepływu danych Azure Data Factory
description: Jak przenieść węzły na diagramie przepływu danych mapowania Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 853fff955a953052b94ad4e7e1dd55371069c30b
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030189"
---
# <a name="mapping-data-flow-move-nodes"></a>Mapowanie węzłów przenoszenia przepływu danych



(media/data-flow/agghead.png "Nagłówek agregatora") ![agregacji opcji transformacji]

Obszar projektowania przepływu danych Azure Data Factory to powierzchnia "konstrukcja", w której tworzysz przepływy danych w dół, od lewej do prawej. Istnieje Przybornik dołączony do każdego przekształcenia ze znakiem plus (+). Skoncentrowanie się na logice biznesowej zamiast łączenia węzłów za pomocą krawędzi w wolnym środowisku DAG.

Tak więc, bez modelu przeciągania i upuszczania, sposób "Przenieś" węzeł transformacji, ma zmienić strumień przychodzący. Zamiast tego przechodzą przekształcenia, zmieniając "strumień przychodzący".

## <a name="streams-of-data-inside-of-data-flow"></a>Strumienie danych w przepływie danych

W przepływie danych Azure Data Factory strumienie reprezentują przepływ danych. W okienku Ustawienia transformacji zostanie wyświetlona wartość pola "przychodząca para. Oznacza to, że przychodzący strumień danych ma na celu przekształcenie. Lokalizację fizyczną węzła transformacji można zmienić na wykresie, klikając nazwę strumienia przychodzącego i wybierając inny strumień danych. Bieżąca transformacja wraz ze wszystkimi kolejnymi transformacjemi w tym strumieniu zostanie przeniesiona do nowej lokalizacji.

Jeśli przenosisz transformację przy użyciu co najmniej jednego przekształcenia po nim, Nowa lokalizacja w przepływie danych zostanie przyłączona za pośrednictwem nowej gałęzi.

Jeśli po wybranym węźle nie ma żadnych kolejnych przekształceń, tylko ta transformacja zostanie przeniesiona do nowej lokalizacji.

## <a name="next-steps"></a>Następne kroki

Po ukończeniu projektu przepływu danych Włącz przycisk Debuguj i przetestuj go w trybie debugowania bezpośrednio w [Projektancie przepływu danych](concepts-data-flow-debug-mode.md) lub w [debugowaniu potoku](control-flow-execute-data-flow-activity.md).
