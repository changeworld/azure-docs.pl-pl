---
title: Kontener abstrakcyjny zabezpieczeń w systemie Microsoft Azure
description: Abstrakcyjny zabezpieczeń kontenerów na platformie Microsoft Azure oficjalny dokument.
author: TomShinder
ms.author: TomSh
ms.date: 09/05/2018
ms.topic: article
ms.service: security
ms.openlocfilehash: 0f892767bb7ab8202a27f298b1cb708d9802aaa4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60590097"
---
# <a name="container-security-in-microsoft-azure"></a>Zabezpieczenia kontenerów na platformie Microsoft Azure
## <a name="abstract"></a>Abstrakcyjny

Technologia kontenerów jest przyczyną strukturalną zmianę w świecie chmury obliczeniowej. Kontenery, należy jednak uruchomić wiele wystąpień aplikacji w pojedynczym wystąpieniu systemu operacyjnego, a tym samym wydajniej korzysta z zasobów. Kontenery zapewniają organizacjom spójność i elastyczność. Umożliwiają one ciągłego wdrażania, ponieważ aplikacji może być opracowanych na komputer stacjonarny, przetestowane na maszynie wirtualnej i następnie wdrożyć do produkcji w chmurze. Kontenery zapewniają elastyczność, analizowaniu, skalowalność i mniejsze koszty ze względu na optymalizacji zasobów.

Ponieważ technologia kontenerów jest stosunkowo nowe, wielu specjalistów IT ma obawy związane z bezpieczeństwem o braku widoczności i użycia w środowisku produkcyjnym. Zespoły programistyczne często są rozpoznaje najlepszych rozwiązań dotyczących zabezpieczeń. Ten dokument może pomóc zespołom zabezpieczeń i deweloperów w Wybieranie podejścia do zabezpieczania tworzenie kontenerów i wdrożeń na platformie Microsoft Azure.

W tym dokumencie opisano kontenery, wdrażanie kontenerów i zarządzania oraz usługi platformy natywnej. Omówiono także problemów zabezpieczeń w czasie wykonywania, które wynikają z użyciem kontenery na platformie Azure. Cyframi i przykładów ten dokument zawiera omówienie modelu kontenera oraz rozwiązania Kubernetes jako orkiestrator kontenerów platformy Docker. Większość zalecenia dotyczące zabezpieczeń mają zastosowanie również do innych modeli kontenera partnerów firmy Microsoft na platformie Azure.

[Pobierz oficjalny dokument](https://azure.microsoft.com/mediahandler/files/resourcefiles/container-security-in-microsoft-azure/Open%20Container%20Security%20in%20Microsoft%20Azure.pdf)