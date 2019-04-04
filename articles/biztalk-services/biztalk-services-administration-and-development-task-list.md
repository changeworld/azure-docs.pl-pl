---
title: Administracyjnych i programistycznych zadań listy w usłudze BizTalk Services | Dokumentacja firmy Microsoft
description: Planowanie i zadania pomocy dotyczące wdrażania usługi Azure BizTalk Services.
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
ms.openlocfilehash: 6b8e0fea73dc24f7e680482be1f06fba6d702804
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916244"
---
# <a name="administration-and-development-task-list-in-biztalk-services"></a>Administracja i tworzenie listy zadań w usłudze BizTalk Services

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]
> 
> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

## <a name="getting-started"></a>Wprowadzenie
Podczas pracy z usługą Microsoft Azure BizTalk Services, istnieje kilka lokalnych i opartych na chmurze składników, należy wziąć pod uwagę. Aby rozpocząć pracę, należy wziąć pod uwagę następujący przepływ procesu:  

| Krok | Kto jest odpowiedzialny | Zadanie | Linki powiązane |
| --- | --- | --- | --- |
| 1. |Administrator |Utwórz subskrypcję platformy Microsoft Azure przy użyciu konta Microsoft lub konta organizacyjnego |[Azure Portal](https://portal.azure.com) |
| 2. |Administrator |Tworzenie i aprowizowanie usługi BizTalk. |[Tworzenie usługi BizTalk](/previous-versions/azure/reference/dn232347(v=azure.100)) |
| 3. |Administrator |Zarejestruj użytkownik lub wdrożenia usługi BizTalk Services w firmie |[Rejestrowanie i aktualizowanie wdrożenia usługi BizTalk, w portalu usługi BizTalk Services](/previous-versions/azure/hh689837(v=azure.100)) |
| 4. |Administrator |Ma zastosowanie, jeśli aplikacja używa usługi BizTalk Adapter, aby nawiązać połączenie z lokalnym systemem Line of Business (LOB) lub korzysta z kolejki lub tematu docelowego.  Utwórz Namespace magistrali usług platformy Azure. Nadaj tej przestrzeni nazw, nazwa wystawcy magistrali usług i wartości klucza wystawcy magistrali usługi dla dewelopera. |[Instrukcje: Utwórz lub zmodyfikuj Namespace usługi Service Bus Service](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md) i [wartości pobieranie nazwy wystawcy i klucz wydawcy](biztalk-issuer-name-issuer-key.md) |
| 5. |Developer |Zainstaluj zestaw SDK i Utwórz projekt usługi BizTalk w programie Visual Studio. |[Instalowanie zestawu SDK usługi Azure BizTalk Services](/previous-versions/azure/hh689760(v=azure.100)) i [tworzenie rozbudowanych komunikatów z punktami końcowymi na platformie Azure](/previous-versions/azure/hh689766(v=azure.100)) |
| 6. |Developer |Wdróż usługę BizTalk projektu do usługi BizTalk hostowanej na platformie Azure. |[Wdrażanie i odświeżanie projektu usługi BizTalk](/previous-versions/azure/hh689881(v=azure.100)) |
| 7. |Administrator |W przypadku korzystania z integracji EDI.  Można dodać partnerów i Tworzenie umów w portalu usługi Microsoft Azure BizTalk Services. Podczas tworzenia umowy, można dodać mostek i/lub przekształcenia utworzone przez dewelopera w ustawieniach umowy. |[Konfigurowanie integracji EDI, AS2 i EDIFACT w portalu usługi BizTalk Services](/previous-versions/azure/hh689853(v=azure.100)) |
| 8. |Administrator |Za pomocą [REST](/previous-versions/azure/reference/dn232347(v=azure.100)), monitorowanie kondycji usługi BizTalk, w tym metryki wydajności. |[BizTalk Services: Karty Pulpit nawigacyjny, Monitor i Skala](https://go.microsoft.com/fwlink/p/?LinkID=302281) |
| 9. |Administrator |Za pomocą portalu usługi Microsoft Azure BizTalk Services, zarządzać artefaktów używane przez usługi BizTalk Services i śledzenie wiadomości przetworzeniu pliki mostka. |[Korzystanie z portalu usługi BizTalk Services](/previous-versions/azure/dn874043(v=azure.100)) |
| 10. |Administrator |Tworzenie planu wykonywania kopii zapasowych, aby utworzyć kopię zapasową usługi BizTalk. |[Ciągłość biznesową i odzyskiwanie po awarii w usłudze BizTalk Services](/previous-versions/azure/dn509557(v=azure.100)) |

## <a name="next-steps"></a>Następne kroki
[Samouczki i przykłady](/previous-versions/azure/hh689895(v=azure.100))

[Tworzenie projektu w programie Visual Studio](/previous-versions/azure/hh689811(v=azure.100))

[Instalowanie zestawu SDK usługi Azure BizTalk Services](/previous-versions/azure/hh689760(v=azure.100))

## <a name="concepts"></a>Pojęcia
[Tworzenie projektu w programie Visual Studio](/previous-versions/azure/hh689811(v=azure.100))  
[EDI, AS2 i EDIFACT komunikatów (firmami)](/previous-versions/azure/hh689898(v=azure.100))  

## <a name="other-resources"></a>Inne zasoby
[Dodaj źródło, miejsce docelowe i mostka komunikatów z punktami końcowymi](/previous-versions/azure/hh689877(v=azure.100))  
[Dowiedz się więcej i tworzenie mapy komunikatów i przekształcenia](/previous-versions/azure/hh689905(v=azure.100))  
[Przy użyciu usługi BizTalk Adapter (BAS)](/previous-versions/azure/hh689889(v=azure.100))  
[Usługa Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=303664)

