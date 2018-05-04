---
title: Azure zalecenia usługi Advisor wydajności | Dokumentacja firmy Microsoft
description: Użyj usługi Advisor w celu zoptymalizowania wydajności wdrożeń platformy Azure.
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 00abb5aafc6f3aec2e2dd7326a307bee74d97cc1
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="advisor-performance-recommendations"></a>Zalecenia doradcy w zakresie wydajności

Azure zalecenia wydajności doradcy w zakresie zwiększyć szybkość i czas odpowiedzi aplikacji biznesowych o znaczeniu krytycznym. Zalecenia dotyczące wydajności z usługi Advisor można uzyskać **wydajności** pulpitu nawigacyjnego usługi Advisor.

## <a name="improve-database-performance-with-sql-db-advisor"></a>Poprawić wydajność bazy danych w usłudze Advisor bazy danych SQL

Advisor zapewnia spójne, skonsolidowanego widoku zaleceń dla wszystkich zasobów na platformie Azure. Umożliwia integrację z Doradcę bazy danych SQL, aby wyświetlić zalecenia dotyczące poprawy wydajności bazy danych SQL Azure. Doradca bazy danych programu SQL ocenia wydajności baz danych SQL Azure, analizując Twojej historii użycia. Oferuje zaleceń, które są najbardziej odpowiednie do uruchamiania typowych zadań bazy danych. 

> [!NOTE]
> Aby uzyskać zalecenia, bazy danych musi mieć o tydzień użycia, a w ciągu tygodnia musi być pewne spójnej działania. Doradca bazy danych SQL można zoptymalizować łatwiej wzorców zapytania spójna niż dla losowych seria działań.

Aby uzyskać więcej informacji o usłudze Advisor bazy danych SQL, zobacz [doradcy bazy danych SQL](https://azure.microsoft.com/documentation/articles/sql-database-advisor/).

## <a name="improve-redis-cache-performance-and-reliability"></a>Zwiększyć wydajność pamięci podręcznej Redis i niezawodności

Klasyfikator identyfikuje wystąpienia pamięci podręcznej Redis, gdzie mogą być niekorzystny wpływ na wydajność wysokie użycie pamięci, obciążenie serwera, przepustowości sieci lub dużej liczby połączeń klientów. Klasyfikator także najlepsze rozwiązania w zakresie zalecenia, aby uniknąć potencjalnych problemów. Aby uzyskać więcej informacji o pamięci podręcznej Redis, zobacz [Advisor pamięci podręcznej Redis](https://azure.microsoft.com/documentation/articles/cache-configure/#redis-cache-advisor).


## <a name="improve-app-service-performance-and-reliability"></a>Zwiększyć wydajność aplikacji usługi i niezawodności

Klasyfikator Azure integruje się poniżej rekomendowane najlepsze rozwiązania dla poprawy środowiska usługi aplikacji i wykrywania możliwości odpowiednie platformy. Przykłady zalecenia usługi aplikacji:
* Wykrywanie wystąpień, w którym na wyczerpaniu pamięci lub zasobów procesora CPU przez środowisk uruchomieniowych aplikacji z opcjami środki zaradcze.
* Wykrywanie wystąpień, w którym collocating zasoby, takie jak aplikacje sieci web i baz danych można zwiększyć wydajność i tańsze. 

Aby uzyskać więcej informacji na temat zalecenia usługi aplikacji, zobacz [najlepsze rozwiązania dotyczące usługi Azure App Service](https://azure.microsoft.com/documentation/articles/app-service-best-practices/).

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Jak uzyskać dostęp zalecenia dotyczące wydajności w usługi Advisor

1. Zaloguj się do [portalu Azure](https://portal.azure.com), a następnie otwórz [Advisor](https://aka.ms/azureadvisordashboard).

2.  Na pulpicie nawigacyjnym usługi Advisor, kliknij przycisk **wydajności** kartę.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat zalecenia doradcy w zakresie, zobacz:

* [Wprowadzenie do usługi Advisor](advisor-overview.md)
* [Wprowadzenie do usługi Advisor](advisor-get-started.md)
* [Zalecenia doradcy w zakresie koszt](advisor-performance-recommendations.md)
* [Zalecenia doradcy w zakresie wysokiej dostępności](advisor-high-availability-recommendations.md)
* [Zalecenia doradcy w zakresie zabezpieczeń](advisor-security-recommendations.md)

