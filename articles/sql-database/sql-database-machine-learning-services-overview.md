---
title: Usługi Machine Learning (przy użyciu języka R) w Omówienie usługi Azure SQL Database (wersja zapoznawcza)
description: Ten temat zawiera opis usługi Azure SQL Database Machine Learning Services (przy użyciu języka R) i wyjaśniono, jak działa.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning-services
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 01/31/2019
ms.openlocfilehash: e2159e7cc59830c3d0d10f1c5b9697ab5b45b666
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824171"
---
# <a name="machine-learning-services-with-r-in-azure-sql-database-preview"></a>Usługi Machine Learning (przy użyciu języka R) w usłudze Azure SQL Database (wersja zapoznawcza)

Usługi Machine Learning to funkcja usługi Azure SQL Database, używany do wykonywania skryptów języka R w bazie danych. Ta funkcja obejmuje pakietów R firmy Microsoft o wysokiej wydajności, analizy predykcyjnej i uczenia maszynowego. Dane relacyjne może służyć w skryptów języka R za pomocą procedur składowanych, zawierający instrukcje języka R skryptu T-SQL lub kod R zawierający języka T-SQL.

> [!NOTE]
> Usługi Machine Learning (przy użyciu języka R) w usłudze Azure SQL Database jest obecnie w publicznej wersji zapoznawczej. [Zarejestruj się w wersji zapoznawczej](#signup) poniżej.

## <a name="what-you-can-do-with-r"></a>Co można zrobić przy użyciu języka R

Korzystaj z możliwości języka R do tworzenia zaawansowanych analiz i usługi machine learning w bazie danych. Ta możliwość udostępnia obliczeń i przetwarzania do miejsca przechowywania danych, eliminując konieczność łączenia się do pobierania danych przez sieć. Ponadto możliwościom pakietów języka R enterprise można dostarczać zaawansowane funkcje analityczne na dużą skalę.

Usługi Machine Learning obejmuje podstawowy dystrybucja języka r, nałożony za pomocą pakietów języka R enterprise firmy Microsoft. Funkcje języka R i algorytmy firmy Microsoft jest zaprojektowany z myślą o skali wraz z narzędziem, zapewniając analizy predykcyjnej, modelowanie statystyczne, wizualizacje danych i algorytmów uczenia maszynowego najwyższą.

### <a name="r-packages"></a>Pakiety języka R

Najpopularniejsze pakiety języka R typu open source są wstępnie zainstalowane w usługach Machine Learning. Dostępne są również następujące pakiety języka R od firmy Microsoft:

| Pakietu języka R | Opis|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | Microsoft R Open jest rozszerzona dystrybucja języka R od firmy Microsoft. Jest to platforma pełną typu open source do statystycznego analizy i analizy danych. Jest oparta na i 100% zgodny z języka R i zawiera dodatkowe możliwości w celu zwiększenia wydajności i powtarzalności. |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | Kolekcję funkcji RevoScaleR jest biblioteka podstawowa skalowalne funkcje języka R. w tej bibliotece znajdują się wśród najczęściej używanych. Przekształcenia danych i manipulowania, statystycznego podsumowania, wizualizacji i wiele form, modelowania i analiz, znajdują się w tych bibliotek. Ponadto funkcje w tych bibliotek automatycznie rozkładają obciążenie w obrębie dostępnych rdzeni dla przetwarzania z możliwością pracować nad fragmentów danych, które są koordynowane i zarządzana przez aparat obliczeń równoległych. |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | MicrosoftML dodaje algorytmów uczenia maszynowego, aby utworzyć niestandardowe modele analizy tekstu, analiza obrazu i analiza tonacji. |

Oprócz wstępnie zainstalowanych pakietów, możliwe jest również [zainstalować dodatkowe pakiety](sql-database-connect-query-r.md#add-package).

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>Tworzenie konta na potrzeby korzystania z wersji zapoznawczej

Aby zarejestrować się w publicznej wersji zapoznawczej, wykonaj następujące kroki:

1. Jeśli nie masz subskrypcji platformy Azure, [Tworzenie konta usługi](https://azure.microsoft.com/free/) przed przystąpieniem do wykonywania.

2. Wyślij wiadomość e-mail do firmy Microsoft na adres [sqldbml@microsoft.com](mailto:sqldbml@microsoft.com), aby utworzyć konto na potrzeby korzystania z publicznej wersji zapoznawczej. Publiczna wersja zapoznawcza usługi Machine Learning (z językiem R) w usłudze SQL Database nie jest włączona domyślnie.

Po zarejestrowaniu się w programie Microsoft będziesz dołączyć do publicznej wersji zapoznawczej i włączenia R do istniejącej lub nowej bazy danych.

Usługi Machine Learning (przy użyciu języka R) w usłudze SQL Database jest obecnie dostępny tylko w modelu zakupu opartego na rdzeniach wirtualnych w **ogólnego przeznaczenia** i **krytyczne dla działania firmy** warstwy dla autonomicznej usługi i Opcje wdrażania dla puli elastycznej. W tym początkowej publicznej wersji zapoznawczej **Hiperskali** warstwy usług i **wystąpienia zarządzanego** wdrożenia nie są obsługiwane.

Obecnie język R jest jedynym obsługiwanym językiem. W tej chwili język Python nie jest obsługiwany. Podgląd jest wstępnie dostępna w następujących regionach: w regionie Europa Zachodnia, Europa Północna, zachodnie stany USA 2, wschodnie stany USA, południowo-środkowe stany USA, północno-środkowe stany USA, Kanada Środkowa, Azja południowo-wschodnia, Indie Południowe i Australia południowo-wschodnia. Później zostaną dodane dodatkowe regiony.

Nie należy używać usługi Machine Learning przy użyciu języka R dla obciążeń produkcyjnych w publicznej wersji zapoznawczej.

## <a name="next-steps"></a>Kolejne kroki

- Zobacz [klucza różnice w stosunku do programu SQL Server Machine Learning Services](sql-database-machine-learning-services-differences.md)
- Aby dowiedzieć się, jak używać usługi Machine Learning (przy użyciu języka R) w usłudze Azure SQL Database, zobacz [przewodnika Szybki Start](sql-database-connect-query-r.md).
- Dowiedz się więcej dzięki [samouczki języka SQL, R Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sql-server-r-tutorials)