---
title: Abstrakcyjne Azure Functions i zabezpieczenia platformy bezserwerowej
description: Abstrakcyjne dla Azure Functions i bezserwerowego dokumentu dotyczącego zabezpieczeń.
author: TomShinder
ms.author: TomSh
ms.date: 06/21/2018
ms.topic: article
ms.service: security
ms.subservice: security-fundamentals
ms.openlocfilehash: cf59f6343960a164be66ae27170d722eb26dc173
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727696"
---
# <a name="azure-functions-and-serverless-platform-security"></a>Azure Functions i zabezpieczenia platformy bezserwerowej
## <a name="abstract"></a>Abstrakcyjny
Większość przedsiębiorstw potrzebuje dużej ilości zasobów i czasu na zarządzanie serwerami, co zwiększa koszt. Jeśli przedsiębiorstwa mogą używać mniejszej ilości zasobów do zarządzania serwerami, mogą skupić się na tworzeniu wspaniałych aplikacji.  

Obliczanie bezserwerowe pomaga to tylko, ponieważ Infrastruktura potrzebna do uruchamiania i skalowania aplikacji jest zarządzana. Operacje obliczeniowe bez użycia serwera to abstrakcja serwerów, infrastruktury i systemów operacyjnych. Przetwarzanie bezserwerowe jest sterowane odpowiedzią na zdarzenia i wyzwalacze, które są wykonywane niemal w czasie rzeczywistym — w chmurze. 

Jako w pełni zarządzana usługa, zarządzanie serwerem i planowanie pojemności są niewidoczne dla deweloperów. Platforma bezserwerowa ułatwia tworzenie i wdrażanie aplikacji bezserwerowych za pomocą Azure Functions. Jest to interfejs wiersza polecenia (CLI), który oferuje strukturę i automatyzację ułatwiającą tworzenie zaawansowanych, opartych na zdarzeniach architektur, które składają się z funkcji i zdarzeń. Funkcja platformy Azure to niezależna jednostka wdrożenia, taka jak mikrousługa. Jest to tylko kod, który został wdrożony w chmurze, który jest najczęściej pisany w celu wykonania jednego zadania.

Pomimo korzyści, zabezpieczenia bezserwerowe mają własne czynniki ryzyka, z którymi należy się zająć. Podejście bezserwerowe nie wprowadza nowych problemów związanych z bezpieczeństwem, ale wymaga podejścia do istniejących zagadnień związanych z bezpieczeństwem. Ten oficjalny dokument koncentruje się na następujących kwestiach dotyczących zabezpieczeń: 
* Zalety platformy bezserwerowej
* Problemy z zabezpieczeniami w przypadku obliczeń bezserwerowych
* Krytyczne problemy z zabezpieczeniami i środki zaradcze w kontekście platformy Azure
* Zabezpieczanie platformy bezserwerowej firmy Microsoft

[Pobierz oficjalny dokument](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-functions-serverless-platform-security/Microsoft%20Serverless%20Platform.pdf)

