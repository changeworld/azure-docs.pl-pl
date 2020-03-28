---
author: bucurb
ms.author: bobuc
ms.date: 09/18/2019
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 574003a150ef294aa6a2ebc035fe48cf877dec66
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76545220"
---
## <a name="configure-the-cloud-spatial-anchor-session"></a>Konfigurowanie sesji zakotwiczenia przestrzennego chmury

Zajmiemy się konfiguracją sesji zakotwiczenia przestrzennego chmury w następnej kolejności. W pierwszej linii ustawiamy dostawcę czujników na sesji. Od teraz każda kotwica, którą tworzymy podczas sesji, będzie skojarzona z zestawem odczytów czujników. Następnie możemy utworzyć wystąpienie kryteriów lokalizowania w pobliżu urządzenia i zainicjować je, aby spełnić wymagania aplikacji. Na koniec poinstruujemy sesję, aby używała danych z czujników podczas lokalizowania kotew, tworząc obserwatora z naszych kryteriów zbliżonych do urządzenia.