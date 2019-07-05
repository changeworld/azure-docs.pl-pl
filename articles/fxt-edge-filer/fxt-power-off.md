---
title: Jak zamknąć jednostki filtr Edge FXT Azure firmy Microsoft
description: Procedury dotyczące uruchamiania i zamykania bezpieczne węzła filtr Edge FXT platformy Azure
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: how-to
ms.date: 07/01/2019
ms.author: v-erkell
ms.openlocfilehash: 8d779547040da9855409d3408827af2e4acd17a7
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542860"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>Jak bezpiecznie wyłączyć filtr Edge FXT Azure sprzętu

Mimo że można użyć przycisku zasilania fizycznych, aby przełączyć się do jednego węzła, nie należy jej używać do zamykania jednostki w normalnych warunkach.

Po węzłem filtr Edge FXT Azure jest używana jako część klastra, należy użyć oprogramowanie klastra do panelu sterowania do zamykania sprzętu. 

> [!NOTE] 
> Aby uniknąć utraty lub uszkodzenia danych to możliwe, należy zawsze używać oprogramowania Panel sterowania do zamykania filtr Edge FXT platformy Azure. Nie należy używać przycisku fizycznego zasilania zamknięcia systemu, chyba że należy to zrobić przez dział obsługi klienta firmy Microsoft i pomocy technicznej.
> 
> W nagłych elektrycznych rozłączyć przewodów zasilania lub Użyj własnych danych Centrum energii elektrycznej rozłączyć mechanizm.

## <a name="shut-down-a-node-from-the-control-panel"></a>Zamknij węzeł w Panelu sterowania

Wykonaj te instrukcje, aby bezpiecznie wyłączyć węzłem filtr Edge FXT platformy Azure:

1. Zaloguj się do klastra Panelu sterowania. (Ze wskazówkami [otwarcie strony ustawień](fxt-cluster-create.md#open-the-settings-pages))
1. Kliknij przycisk **ustawienia** kartę, a następnie załaduj **klastra** > **węzłów FXT** strony.
1. Lista węzły klastra zawiera ten, który ma zostać zamknięta. Kliknij przycisk **Wyłącz zasilanie** znajdujący się w jego **akcje** kolumny. 
1. Zaczekaj kilka minut. Węzeł zostanie zamknięty i automatycznie wyłączyć.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o diod LED stan i inne wskaźniki w [filtr Edge FXT Azure Monitor stanu sprzętu](fxt-monitor.md).
* Przeczytaj więcej na temat power filtr Edge FXT Azure dostarcza w [Podłącz kable zasilania](fxt-network-power.md#connect-power-cables).
