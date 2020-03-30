---
title: Jak zamknąć jednostkę Microsoft Azure FXT Edge Filer
description: Procedury uruchamiania i bezpiecznego zamykania węzła plików usługi Azure FXT Edge
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: 91445c4219abaa67385105f52515f52c87e2cf06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72256002"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>Jak bezpiecznie wyłączyć sprzęt Azure FXT Edge Filer

Chociaż można użyć fizycznego przycisku zasilania, aby włączyć pojedynczy węzeł, nie należy go używać do wyłączania urządzenia w normalnych warunkach.

Po węźle Azure FXT Edge Filer jest używany jako część klastra, należy użyć oprogramowania panelu sterowania klastra, aby zamknąć sprzęt. 

> [!NOTE] 
> Aby uniknąć utraty lub uszkodzenia danych, zawsze użyj oprogramowania Panelu sterowania, aby zamknąć plik usługi Azure FXT Edge Filer. Nie należy używać fizycznego przycisku zasilania do zamykania systemu, chyba że zostanie to poinstruowane przez dział obsługi klienta i pomocy technicznej firmy Microsoft.
> 
> W sytuacji awaryjnej elektrycznej odłącz przewody zasilające lub użyj mechanizmu odłączania energii elektrycznej w centrum danych.

## <a name="shut-down-a-node-from-the-control-panel"></a>Zamykanie węzła z Panelu sterowania

Postępuj zgodnie z poniższymi instrukcjami, aby bezpiecznie wyłączyć węzeł filera usługi Azure FXT Edge:

1. Zaloguj się do Panelu sterowania klastra. (Wskazówki dojazdu [w otwórz strony Ustawienia)](fxt-cluster-create.md#open-the-settings-pages)
1. Kliknij kartę **Ustawienia,** a następnie załaduj stronę**Węzły FXT** **klastra.** > 
1. Na liście węzłów klastra znajdź ten, który chcesz zamknąć. Kliknij przycisk **Wyłącz zasilanie** w kolumnie **Akcje.** 
1. Poczekaj chwilę. Węzeł zostanie zamknięty i wyłączy się.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o diodach LED stanu i innych wskaźnikach w [stanie sprzętu Monitor Azure FXT Edge Filer](fxt-monitor.md).
* Dowiedz się więcej o zasilaczach Azure FXT Edge Filer w [połącz kable zasilające](fxt-network-power.md#connect-power-cables).
