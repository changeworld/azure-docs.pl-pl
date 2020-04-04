---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 07405ad0da9c9ba280810402a638395a7feb8554
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637284"
---
Obsługa skompresowanego dźwięku jest zaimplementowana za pomocą [GStreamer](https://gstreamer.freedesktop.org). Ze względów licencyjnych pliki binarne GStreamer nie są kompilowane i połączone z zestawem SDK mowy. Deweloperzy muszą zainstalować kilka zależności i wtyczek, zobacz [Instalowanie w systemie Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c). Pliki binarne Gstreamer muszą znajdować się w ścieżce systemowej, aby sdk mowy można załadować pliki binarne gstreamer w czasie wykonywania. Jeśli sdk mowy jest w stanie znaleźć libgstreamer-1.0-0.dll w czasie wykonywania oznacza, że pliki binarne gstreamer znajdują się w ścieżce systemowej.

