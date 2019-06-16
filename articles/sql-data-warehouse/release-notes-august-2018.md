---
title: Usługa Azure SQL Data Warehouse — informacje o wersji sierpnia 2018 | Dokumentacja firmy Microsoft
description: Informacje o wersji dla usługi Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/13/2018
ms.author: anjangsh
ms.reviewer: jrasnick
ms.openlocfilehash: f0840e9b91c81b8a99e8c736c3c5db082c92fe76
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65912216"
---
# <a name="whats-new-in-azure-sql-data-warehouse-august-2018"></a>Co nowego w usłudze Azure SQL Data Warehouse? Sierpień 2018 r.
Usługa Azure SQL Data Warehouse odbiera ulepszenia stale. W tym artykule opisano nowe funkcje i zmiany, które zostały wprowadzone w sierpnia 2018.

## <a name="automatic-intelligent-insights"></a>Automatyczne Intelligent Insights
Firma Microsoft wprowadziła [automatyczne intelligent insights](https://azure.microsoft.com/blog/automatic-intelligent-insights-to-optimize-performance-with-sql-data-warehouse/) spełniającej promise chmury automatyzacji dla magazynu danych. Nie jest już konieczne będzie monitorować magazyn danych dla danych tabeli niesymetryczność i nieoptymalne statystyk. Bez dodatkowych kosztów usługa SQL Data Warehouse udostępnia intelligent insights dla wszystkich wystąpień Gen2. Dzięki integracji z usługą [usługi Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations), możesz automatycznie otrzymywać zalecenia dotyczące najlepszych rozwiązań zwiększa wydajność obciążeń active. Usługa SQL Data Warehouse analizuje obciążenia i powierzchnie zalecenia na podstawie użycia. Ta analiza odbywa się w codziennych pozwala monitorować raporty użycia i zalecenia dotyczące ulepszenia do obciążenia.

Zalecenia można wyświetlić w portalu usługi Azure Advisor: ![Usługa Azure Advisor portalu zalecenia dotyczące usługi Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/4e205b6d-df04-48db-8eec-d591f2592cf4.png)

Możesz przejść do szczegółów każdej kategorii, aby wyświetlić zalecenia dotyczące określonego alertu: ![Szczegóły portalu zalecenia doradcy platformy Azure dla usługi Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/3c42426e-6969-46e3-9025-c34c0755a302.png)


## <a name="bug-fixes"></a>Poprawki błędów

| Stanowisko | Opis |
|:---|:---|
| **Potencjalnych awarii zapytania, gdy liczba podziałów przekracza maksymalny limit** |Po przekroczeniu limitu pliku podziału 1 milion górną granicę nieobsługiwany wyjątek spowodowany aparatu SQL do porzucenia i wszystkie zapytania nie powiodło się. Ta poprawka rozwiązany problem poprawnie Obsługa wyjątku i zwróci błąd bez powodowania zapytania, aby zakończyć się niepowodzeniem. |
| **Zwiększona wartość domyślną ExternalMoveReadersPerNode, aby zwiększyć wydajność obciążeń** |Ten problem został spowodowany przez ustawienie właściwości ExternalMoveReadersPerNode są zsynchronizowane z usługą Service fabric, ustawienie. Ta regresji spowodowała pogorszenie wydajności obciążenia Gen2. Poprawka zapewnia wydajność ładowania Gen2 wróć tutaj parametry zoptymalizowane projektu.|


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
[Forum Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Filmy wideo]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
