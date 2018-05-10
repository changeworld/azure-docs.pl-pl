---
title: Uczenie maszynowe Azure i FPGAs
description: Dowiedz się, jak przyspieszanie modeli i sieci neuronowej głębokość z FPGAs.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/07/2018
ms.openlocfilehash: 493b3f8de617146af534349e18ed49b83c46dee8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="what-is-a-field-programmable-gate-array-fpga"></a>Co to jest tablicą bramek programowane pola (układu FPGA)?

Tablice bramek programowane (układu FPGA) są scalonych, które można tak skonfigurować, zgodnie z potrzebami. Układu FPGA można zmienić w razie potrzeby wdrożyć logikę nowe. Azure Machine Learning sprzętu przyspieszony modeli umożliwiają wdrażanie modeli przeszkolone do FPGAs w chmurze Azure.

Ta funkcja jest obsługiwana przez Brainwave projektu, która obsługuje tłumaczenia sieci neuronowe głębokość (DNN) do programu układu FPGA. 

## <a name="why-use-an-fpga"></a>Dlaczego warto używać układu FPGA?

FPGAs zapewniają bardzo małych opóźnień i są szczególnie przydatne do oceniania danych na rozmiar partii, jeden (nie jest wymagane dla rozmiaru duży wsadu).  Są one ekonomiczne i dostępnej na platformie Azure.  Brainwave projektu można parallelize DNNs wstępnie przeszkolone w tych FPGAs do skalowania w poziomie usługi.

## <a name="next-steps"></a>Kolejne kroki

[Wdróż model jako usługę sieci web na układu FPGA](how-to-deploy-fpga-web-service.md)

[Informacje o sposobie instalowania układu FPGA zestawu SDK](reference-fpga-package-overview.md)