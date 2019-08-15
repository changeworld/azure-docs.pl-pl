---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/28/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5df0fe23183b53f13d6c6545e248724324434cde
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/14/2019
ms.locfileid: "69029835"
---
Opcje replikacji dla konta magazynu obejmują:

* [Magazyn lokalnie nadmiarowy (LRS)](../articles/storage/common/storage-redundancy-lrs.md): Prosta, niska w niskich kosztach strategia replikacji. Dane są replikowane synchronicznie trzy razy w regionie podstawowym.
* [Magazyn strefowo nadmiarowy (ZRS)](../articles/storage/common/storage-redundancy-zrs.md): Replikacja dla scenariuszy wymagających wysokiej dostępności. Dane są replikowane synchronicznie w trzech strefach dostępności platformy Azure w regionie podstawowym.
* [Magazyn Geograficznie nadmiarowy (GRS)](../articles/storage/common/storage-redundancy-grs.md): Replikacja między regionami w celu ochrony przed awarią regionalną. Dane są replikowane synchronicznie trzy razy w regionie podstawowym, a następnie replikowane asynchronicznie do regionu pomocniczego. Aby uzyskać dostęp do odczytu do danych w regionie pomocniczym, Włącz magazyn Geograficznie nadmiarowy z dostępem do odczytu (RA-GRS).
* [Magazyn Geograficznie nadmiarowy (GZRS) (wersja zapoznawcza)](../articles/storage/common/storage-redundancy-gzrs.md): Replikacja dla scenariuszy wymagających zarówno wysokiej dostępności, jak i maksymalnej trwałości. Dane są replikowane synchronicznie w trzech strefach dostępności platformy Azure w regionie podstawowym, a następnie replikowane asynchronicznie do regionu pomocniczego. Aby uzyskać dostęp do odczytu do danych w regionie pomocniczym, Włącz strefę geograficzną z dostępem do odczytu (RA-GZRS).
