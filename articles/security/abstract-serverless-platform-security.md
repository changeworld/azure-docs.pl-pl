---
title: Abstract — usługa Azure Functions i zabezpieczenia z platformą bez użycia serwera
description: Ogólny dla usługi Azure Functions i bez użycia serwera zabezpieczeń platformy oficjalny dokument.
author: TomShinder
ms.author: TomSh
ms.date: 06/21/2018
ms.topic: article
ms.service: security
ms.openlocfilehash: 87010b3256fb8eef6871d76f80db2999760386b4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60587499"
---
# <a name="azure-functions-and-serverless-platform-security"></a>Usługa Azure Functions i zabezpieczenia z platformą bez użycia serwera
## <a name="abstract"></a>Abstrakcyjny
Większość przedsiębiorstw muszą znaczną ilość zasobów i do zarządzania serwerami, czasu, który dodaje kosztów. W przypadku przedsiębiorstw, można użyć mniejszej ilości zasobów do zarządzania serwerami, można skoncentrować się na tworzeniu wspaniałych aplikacji.  

Operacje obliczeniowe pomaga to po prostu, ponieważ infrastrukturę, która należy do uruchamiania i skalowania aplikacji jest zarządzana za Ciebie. Operacje obliczeniowe bez użycia serwera to abstrakcja serwerów, infrastruktury i systemów operacyjnych. Bezserwerowe obliczenia jest wymuszany przez reakcji na zdarzenia i wyzwalacze, które są dostępne miejsce w czasie prawie rzeczywistym — w chmurze. 

Jako w pełni zarządzanej usługi Zarządzanie serwerem i Planowanie wydajności jest niewidoczne dla dewelopera. Bez użycia serwera framework ułatwia tworzenie i wdrażanie aplikacji niewymagających użycia serwera za pomocą usługi Azure Functions. Jest interfejs wiersza polecenia (CLI) oferuje struktury i automatyzacji ułatwiające tworzenie architektur zaawansowane, oparte na zdarzeniach, bezserwerowe składa się z funkcji i zdarzeń. Funkcję platformy Azure jest jednostką niezależnie od wdrażania, takie jak mikrousługi. Jest tylko kod, wdrożonych w chmurze, które najczęściej są zapisywane do wykonania w jednym zadaniu.

Oprócz tych korzyści bez użycia serwera zabezpieczeń ma swój własny czynników ryzyka, aby poradzić sobie z. Bez użycia serwera podejście nie wprowadzają nowe problemy dotyczące zabezpieczeń, ale wymaga o podejście do istniejących obawy związane z bezpieczeństwem. Ten dokument koncentruje się na te kwestie bezpieczeństwa: 
* Korzyści z platformą bez użycia serwera
* Problemy z zabezpieczeniami w operacje obliczeniowe
* Ważnych kwestiach dotyczących zabezpieczeń i środki zaradcze w kontekście platformy Azure
* Zabezpieczanie platformą bez użycia serwera firmy Microsoft

[Pobierz oficjalny dokument](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-functions-serverless-platform-security/Microsoft%20Serverless%20Platform.pdf)

