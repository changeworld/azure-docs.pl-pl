---
title: Usługa Azure SQL Data Warehouse — informacje o wersji sierpnia 2018 | Dokumentacja firmy Microsoft
description: Informacje o wersji dla usługi Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 08/06/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 1f3b17f3163c29f9b9e1e47e14ccdbc1e37e1010
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39635005"
---
# <a name="whats-new-in-azure-sql-data-warehouse-august-2018"></a>Co nowego w usłudze Azure SQL Data Warehouse? Sierpnia 2018
Usługa Azure SQL Data Warehouse odbiera ulepszenia stale. W tym artykule opisano nowe funkcje i zmiany, które zostały wprowadzone w sierpnia 2018.

## <a name="automatic-intelligent-insights"></a>Automatyczne Intelligent Insights
Firma Microsoft wprowadziła [automatyczne intelligent insights](https://azure.microsoft.com/blog/automatic-intelligent-insights-to-optimize-performance-with-sql-data-warehouse/) spełniającej promise chmury automatyzacji dla magazynu danych. Nie jest już konieczne będzie monitorować magazyn danych dla danych tabeli niesymetryczność i nieoptymalne statystyk. Bez dodatkowych kosztów usługa SQL Data Warehouse udostępnia intelligent insights dla wszystkich wystąpień Gen2. Dzięki integracji z usługą [usługi Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations), możesz automatycznie otrzymywać zalecenia dotyczące najlepszych rozwiązań zwiększa wydajność obciążeń active. Usługa SQL Data Warehouse analizuje obciążenia i powierzchnie zalecenia na podstawie użycia. Ta analiza odbywa się w codziennych pozwala monitorować raporty użycia i zalecenia dotyczące ulepszenia do obciążenia.

Zalecenia można wyświetlić w portalu usługi Azure Advisor: ![Azure Advisor Portal zalecenia dotyczące usługi Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/4e205b6d-df04-48db-8eec-d591f2592cf4.png)

Możesz przejść do każdej kategorii, aby wyświetlić zalecenia dotyczące określonego alertu: ![szczegóły zalecenia portalu usługi Azure Advisor, Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/3c42426e-6969-46e3-9025-c34c0755a302.png)

## <a name="next-steps"></a>Kolejne kroki
Po użytkownik podstawową wiedzę na temat usługi SQL Data Warehouse, Dowiedz się, jak szybko [utworzyć SQL Data Warehouse][create a SQL Data Warehouse]. Jeśli dopiero zaczynasz korzystać z platformy Azure, [słownik platformy Azure][Azure glossary] może pomóc Ci zaznajomić się z nową terminologią. Możesz też zwrócić uwagę na inne zasoby dotyczące usługi SQL Data Warehouse.  

* [Historie sukcesu klientów]
* [Blogi]
* [Żądania funkcji]
* [Filmy wideo]
* [Blogi zespołu doradczego klientów]
* [Forum Stack Overflow]
* [Twitter]


[Blogi]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogi zespołu doradczego klientów]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Historie sukcesu klientów]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Żądania funkcji]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Forum Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Wideo]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md