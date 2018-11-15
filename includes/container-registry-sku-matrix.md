---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 11/09/2018
ms.author: danlep
ms.openlocfilehash: 286159aee00b018d0cacae3858f92cdf0e79cfbc
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572534"
---
| SKU | Opis |
|---|---|
| Podstawowa | Zoptymalizowany pod kątem kosztów punkt wejścia dla deweloperów poznających usługę Azure Container Registry. Rejestry w warstwie Podstawowa mają te same możliwości programowe co w warstwach Standardowa i Premium (integracja uwierzytelniania usługi Azure Active Directory, usuwanie obrazu i elementy webhook), istnieją jednak ograniczenia dotyczące wielkości i użycia. |
| Standardowa (Standard) | Rejestr w warstwie Standardowa oferuje te same możliwości co w warstwie Podstawowa, ale ze zwiększonymi limitami dotyczącymi magazynu i przepływności obrazu. Rejestry w warstwie Standardowa powinny spełniać wymagania większości scenariuszy produkcyjnych. |
| Premium | Rejestry w warstwie Premium mają wyższe limity ograniczeń, na przykład dla magazynu i operacji współbieżnych, w tym większe możliwości obsługi przez magazyn scenariuszy z dużą ilością danych. Oprócz większej przepływności obrazu w warstwie Premium dodano inne funkcje, takie jak replikacja geograficzna umożliwiająca zarządzanie pojedynczym rejestrem w wielu regionach, a także obsługę rejestru w pobliskiej sieci dla poszczególnych wdrożeń. |
