---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b1287f9c7e946c7b4d035b2ad6301947ffad3cea
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717124"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Usługi Azure premium storage: Projektowanie pod kątem wysokiej wydajności

Ten artykuł zawiera wskazówki dotyczące tworzenia aplikacji o wysokiej wydajności przy użyciu usługi Azure Premium Storage. Można użyć w instrukcjach podanych w tym dokumencie, w połączeniu z najlepsze rozwiązania w zakresie wydajności mające zastosowanie do technologii wykorzystywanych przez aplikację. Aby zilustrować wytycznych, użyliśmy SQL Server uruchomiony na usługę Premium Storage, na przykład w tym dokumencie.

Gdy zajmujemy się scenariuszy wydajności dla warstwy magazynu, w tym artykule, należy zoptymalizować warstwy aplikacji. Na przykład jeśli hostujesz farmy programu SharePoint w usłudze Azure Premium Storage umożliwia przykładów programu SQL Server z tego artykułu zoptymalizować serwera bazy danych. Ponadto można zoptymalizować, serwer sieci Web farmy programu SharePoint i serwera aplikacji, aby uzyskać większość wydajność.

Ten artykuł pomoże odpowiedzi następujące często zadawane pytania na temat optymalizowania wydajności aplikacji w magazynie Azure Premium Storage

* Jak zmierzyć wydajność aplikacji?  
* Dlaczego użytkownik nie ma oczekiwanego o wysokiej wydajności?  
* Jakie czynniki mają wpływ na wydajność aplikacji w magazynie Premium Storage?  
* Jak te czynniki mają wpływ wydajność aplikacji w magazynie Premium Storage?  
* Jak można zoptymalizować operacje We/Wy, przepustowości i opóźnienia  

Te wytyczne zostały zamieszczone specjalnie dla usługi Premium Storage, ponieważ obciążeń działających na usługę Premium Storage o wysokiej wydajności poufnych. Przykłady zostały zamieszczone, gdzie jest to odpowiednie. Można również zastosować niektóre z poniższych wskazówek do aplikacji działających na maszynach wirtualnych IaaS z dysków magazynu w warstwie standardowa.