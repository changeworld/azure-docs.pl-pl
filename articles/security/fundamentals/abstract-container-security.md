---
title: Zabezpieczenia kontenera abstrakcyjnego w Microsoft Azure
description: Streszczenie dla zabezpieczeń kontenera w Microsoft Azure oficjalny dokument.
author: TomShinder
ms.author: TomSh
ms.date: 09/05/2018
ms.topic: article
ms.service: security
ms.subservice: security-fundamentals
ms.openlocfilehash: b70744f403c483448a844d3f3decf6ce26f48f06
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727776"
---
# <a name="container-security-in-microsoft-azure"></a>Zabezpieczenia kontenera w Microsoft Azure
## <a name="abstract"></a>Abstrakcyjny

Technologia kontenera jest przyczyną zmiany strukturalnej w świecie przetwarzania w chmurze. Kontenery umożliwiają uruchamianie wielu wystąpień aplikacji w pojedynczym wystąpieniu systemu operacyjnego, a tym samym wydajniej korzystając z zasobów. Kontenery zapewniają spójność organizacji i elastyczność. Umożliwiają one ciągłe wdrażanie, ponieważ aplikacja może zostać opracowana na pulpicie, przetestowana na maszynie wirtualnej, a następnie wdrożona w środowisku produkcyjnym w chmurze. Kontenery zapewniają elastyczność, usprawnioną eksploatację i skalowalność oraz obniżone koszty związane z optymalizacją zasobów.

Ponieważ technologia kontenera jest stosunkowo nowa, wielu specjalistów IT ma problemy z bezpieczeństwem dotyczące braku widoczności i użycia w środowisku produkcyjnym. Zespoły programistyczne często nie znają najlepszych rozwiązań w zakresie zabezpieczeń. Ten oficjalny dokument może pomóc zespołom operacji zabezpieczeń i deweloperom w wyborze podejścia do zabezpieczania projektowania i wdrażania kontenerów na platformie Microsoft Azure.

W tym dokumencie opisano kontenery, wdrażanie kontenerów i zarządzanie oraz natywne usługi platformy. Opisano w nim również problemy z zabezpieczeniami środowiska uruchomieniowego, które powstają w przypadku korzystania z kontenerów na platformie Azure. W ilustracjach i przykładach ten dokument koncentruje się na platformie Docker jako model kontenera i Kubernetes jako koordynator kontenerów. Większość zaleceń dotyczących zabezpieczeń ma zastosowanie także do innych modeli kontenera od partnerów firmy Microsoft na platformie Azure.

[Pobierz oficjalny dokument](https://azure.microsoft.com/mediahandler/files/resourcefiles/container-security-in-microsoft-azure/Open%20Container%20Security%20in%20Microsoft%20Azure.pdf)