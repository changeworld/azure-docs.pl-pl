---
title: "Użyj usługi Power BI z usługą Magazyn danych SQL | Dokumentacja firmy Microsoft"
description: "Porady dotyczące korzystania z usługi Power BI z usługą Magazyn danych SQL Azure związane z opracowywaniem rozwiązań."
services: sql-data-warehouse
documentationcenter: NA
author: mlee3gsd
manager: jhubbard
editor: 
ms.assetid: b12bee87-2268-40c2-81bf-ab27588b32e8
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: martinle;barbkess
ms.openlocfilehash: 4ea9a2ff0c95a73b348d3b48e9e62957d5cce31c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2017
---
# <a name="use-power-bi-with-sql-data-warehouse"></a>Użyj usługi Power BI z usługą Magazyn danych SQL
Jako z bazy danych SQL Azure SQL danych magazynu bezpośrednie połączenie umożliwia użytkownikom korzystać z zaawansowanych przekazywanie logicznej obok możliwości analityczne usługi Power BI.  Za pomocą bezpośrednich Connect zapytania są wysyłane do usługi Azure SQL Data Warehouse w czasie rzeczywistym Ci poznać platformę danych.  Połączone, skali usługi SQL Data Warehouse umożliwia użytkownikom tworzenie dynamicznych raportów w minutach przed terabajtów danych.  Ponadto wprowadzenie przycisku Otwórz w usłudze Power BI umożliwia użytkownikom bezpośrednio z usługi Power BI bez zbierania informacji z innych części Azure ich SQL Data Warehouse.

Podczas używania połączenia bezpośredniego należy Uwaga:

* Określ nazwę FQDN serwera podczas łączenia z (Aby uzyskać więcej informacji zobacz poniżej)
* Upewnij się, że reguły zapory dla bazy danych są skonfigurowane do "Zezwalaj na dostęp do usług platformy Azure".
* Wszystkie akcje, takich jak wybierając kolumnę lub Dodawanie filtru bezpośrednio wysyła kwerendy do magazynu danych
* Kafelki są odświeżane co około 15 minut (odświeżania nie są wymagane do zaplanowania)
* Funkcja pytania i odpowiedzi nie jest dostępna dla bezpośrednie łączenie zestawów danych
* Zmiany schematu nie są odczytywane automatycznie
* Wszystkie zapytania bezpośredniego połączenia limit czasu będzie 2 minuty

Te ograniczenia i uwagi może zmienić będziemy kontynuować proces ulepszania procesy. Czynności, aby podłączyć szczegóły są przedstawione poniżej.  

## <a name="using-the-open-in-power-bi-button"></a>Przy użyciu przycisku "Otwórz w usłudze Power BI"
Najprostszym sposobem przenoszenia między magazyn danych SQL i usługi Power BI jest otwarty w usłudze Power BI przycisku. Ten przycisk pozwala na łatwe rozpocząć tworzenie nowych pulpitów nawigacyjnych w usłudze Power BI.  

1. Aby rozpocząć przejdź do Twojego wystąpienia usługi SQL Data Warehouse w portalu Azure.
2. Kliknij przycisk „Otwórz w usłudze Power BI”.
3. Jeśli nie możemy zalogować Cię bezpośrednio lub jeśli nie masz konta usługi Power BI, należy się zalogować.  
4. Nastąpi przekierowanie do strony połączenie SQL Data Warehouse przy użyciu informacji z usługą SQL Data Warehouse wstępnie wypełnione.
5. Po wprowadzeniu poświadczeń można będzie pełni połączony z usługą SQL Data Warehouse.

## <a name="connecting-through-the-power-bi-portal"></a>Łączenie za pośrednictwem portalu usługi Power BI
Oprócz przy użyciu przycisku Otwórz w usłudze Power BI, użytkownicy mogą również łączyć się ich SQL Data Warehouse za pośrednictwem portalu Power BI.

1. Kliknij przycisk "Pobierz dane" u dołu okienka nawigacji.
2. Wybierz "Bazy danych".
3. Raz na stronie baz danych, wybierz "Azure SQL Data Warehouse", a następnie kliknij przycisk "Połącz".
4. Wprowadź informacje niezbędne połączenia.  Nazwa serwera, a nazwa bazy danych można znaleźć w portalu Azure.
5. Zostanie skierowany do strony głównej usługi Power BI, a po nawiązaniu połączenia z nowego wpisu w obszarze "Zestaw danych" pojawi się nazwą wystąpienia.  
6. Możesz kliknąć na nowy zestaw danych, aby poznać wszystkie tabele i widoki w bazie danych. Wybierając kolumnę będzie wysyłać zapytanie do źródła, dynamicznego tworzenia wizualny. Te elementy wizualne można zapisane w nowy raport i ponownie przypięty do pulpitu nawigacyjnego.

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse development overview]:  ./sql-data-warehouse-overview-develop/
[SQL Data Warehouse integration overview]:  ./sql-data-warehouse-overview-integration/

<!--MSDN references-->

<!--Other Web references-->
