---
title: Administracja i rozwoju zadań listy usługi BizTalk | Dokumentacja firmy Microsoft
description: Planowanie i zadania pomocy dotyczące wdrażania usług BizTalk Azure.
services: biztalk-services
documentationcenter: ''
author: msftman
manager: erikre
editor: ''
ms.assetid: 0ab70b5b-1a88-4ba5-b329-ec51b785010e
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: deonhe
ms.openlocfilehash: 9edd7261ca62f505ffb4854e3132fae916768f67
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2017
ms.locfileid: "26626591"
---
# <a name="administration-and-development-task-list-in-biztalk-services"></a>Administrowanie i Lista zadań rozwoju w usługi BizTalk Services

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

## <a name="getting-started"></a>Wprowadzenie
Podczas pracy z usługi Microsoft Azure BizTalk, istnieje kilka lokalnych i chmurze należy rozważyć użycie składników. Aby rozpocząć pracę, należy wziąć pod uwagę następujące przepływ procesu:  

| Krok | Kto jest odpowiedzialny | Zadanie | Linki pokrewne |
| --- | --- | --- | --- |
| 1. |Administrator |Utwórz subskrypcją platformy Microsoft Azure przy użyciu konta Microsoft lub konta organizacyjnego |[Witryna Azure Portal](https://portal.azure.com) |
| 2. |Administrator |Utwórz lub alokowanie usługi BizTalk. |[Utwórz usługę BizTalk](https://msdn.microsoft.com/library/azure/dn232347.aspx) |
| 3. |Administrator |Zarejestruj możesz lub wdrożenia usługi BizTalk Services w firmie |[Rejestrowanie i aktualizowanie wdrożenia usługi BizTalk w portalu usługi BizTalk](https://msdn.microsoft.com/library/azure/hh689837.aspx) |
| 4. |Administrator |Ma zastosowanie, jeśli aplikacja używa Usługa karty BizTalk do nawiązania połączenia lokalnego systemu — biznesowych (LOB) lub używa kolejka lub temat docelowy.  Utwórz Namespace magistrali usługi Azure. Nadaj tej przestrzeni nazw, nazwa wystawcy magistrali usługi i wartości klucza wystawcy magistrali usługi do deweloperów. |[Porady: Tworzenie lub modyfikowanie Namespace usługi magistrali usługi](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md) i [wartości pobieranie nazwy wystawcy i klucza wystawcy](biztalk-issuer-name-issuer-key.md) |
| 5. |Dla deweloperów |Zainstaluj zestaw SDK i utworzyć projekt usługi BizTalk w programie Visual Studio. |[Zainstaluj usługi Azure BizTalk SDK](https://msdn.microsoft.com/library/azure/hh689760.aspx) i [tworzenie zaawansowanych wiadomości punktów końcowych na platformie Azure](https://msdn.microsoft.com/library/azure/hh689766.aspx) |
| 6. |Dla deweloperów |Wdrażanie usługi BizTalk projekt z usługą BizTalk hostowanej na platformie Azure. |[Wdrażanie i odświeżanie projektu usługi BizTalk](https://msdn.microsoft.com/library/azure/hh689881.aspx) |
| 7. |Administrator |W przypadku korzystania z EDI.  Możesz dodać partnerami i Tworzenie umów w portalu usług Microsoft Azure BizTalk. Podczas tworzenia umowy, możesz dodać mostek i/lub transformacje utworzony przez dewelopera w ustawieniach umowy. |[Konfigurowanie EDI i AS2, EDIFACT w portalu usługi BizTalk](https://msdn.microsoft.com/library/azure/hh689853.aspx) |
| 8. |Administrator |Przy użyciu [REST](https://msdn.microsoft.com/library/azure/dn232347.aspx), monitorowanie kondycji usługi BizTalk, w tym metryki wydajności. |[BizTalk Services: Dashboard, Monitor and Scale tabs (Usługa BizTalk Services: karty Pulpit nawigacyjny, Monitor i Skalowanie)](http://go.microsoft.com/fwlink/p/?LinkID=302281) |
| 9. |Administrator |Za pomocą portalu usługi Microsoft Azure BizTalk, zarządzanie artefakty używanych przez usługi BizTalk i śledzenie wiadomości, jak są przetwarzane przez mostek pliki. |[Za pomocą portalu usługi BizTalk](https://msdn.microsoft.com/library/azure/dn874043.aspx) |
| 10. |Administrator |Tworzenie planu tworzenia kopii zapasowych, aby utworzyć kopię zapasową usługi BizTalk. |[Ciągłość prowadzenia działalności biznesowej i odzyskiwania po awarii w usługi BizTalk Services](https://msdn.microsoft.com/library/azure/dn509557.aspx) |

## <a name="next-steps"></a>Następne kroki
[Samouczki i przykłady](https://msdn.microsoft.com/library/azure/hh689895.aspx)

[Utwórz projekt w programie Visual Studio](https://msdn.microsoft.com/library/azure/hh689811.aspx)

[Zainstaluj usługi Azure BizTalk SDK](https://msdn.microsoft.com/library/azure/hh689760.aspx)

## <a name="concepts"></a>Pojęcia
[Utwórz projekt w programie Visual Studio](https://msdn.microsoft.com/library/azure/hh689811.aspx)  
[EDI, AS2 i EDIFACT wiadomości (Business Business)](https://msdn.microsoft.com/library/azure/hh689898.aspx)  

## <a name="other-resources"></a>Inne zasoby
[Dodaj źródłowego, docelowego i Mostek punktów końcowych do obsługi komunikatów](https://msdn.microsoft.com/library/azure/hh689877.aspx)  
[Dowiedz się więcej i tworzenie mapy komunikatów i przekształcenia](https://msdn.microsoft.com/library/azure/hh689905.aspx)  
[Usługi BizTalk karty (BAS)](https://msdn.microsoft.com/library/azure/hh689889.aspx)  
[Usługi Azure BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=303664)

