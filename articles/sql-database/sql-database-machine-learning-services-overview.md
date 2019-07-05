---
title: SQL Database usługi Azure Machine Learning przy użyciu języka R (wersja zapoznawcza) — omówienie
description: Ten artykuł zawiera opis usługi Azure SQL Database Machine Learning Services (przy użyciu języka R) i wyjaśniono, jak działa.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 03/01/2019
ms.openlocfilehash: 186b986fe1931365ee34efab2e04e58908402cc0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67427939"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>SQL Database usługi Azure Machine Learning przy użyciu języka R (wersja zapoznawcza)

Usługi Machine Learning to funkcja usługi Azure SQL Database, używany do wykonywania skryptów języka R w bazie danych. Ta funkcja obejmuje pakietów R firmy Microsoft o wysokiej wydajności, analizy predykcyjnej i uczenia maszynowego. Dane relacyjne może służyć w skryptów języka R za pomocą procedur składowanych, zawierający instrukcje języka R skryptu T-SQL lub kod R zawierający języka T-SQL.

> [!IMPORTANT]
> SQL Database usługi Azure Machine Learning (przy użyciu języka R) jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Publiczna wersja zapoznawcza jest dostępna dla pojedynczych baz danych i pul elastycznych za pomocą modelu zakupu opartego na rdzeniach wirtualnych w **ogólnego przeznaczenia** i **krytyczne dla działania** warstwy usług. W tym początkowej publicznej wersji zapoznawczej **hiperskali** warstwy usług i **wystąpienia zarządzanego** opcji wdrożenia nie są obsługiwane. Obecnie język R jest jedynym obsługiwanym językiem. W tej chwili język Python nie jest obsługiwany.
>
> Podgląd jest obecnie dostępna w następujących regionach: Europa Zachodnia, Europa Północna, zachodnie stany USA 2, wschodnie stany USA, południowo-środkowe stany USA, środkowe stany USA Północna, Kanada Środkowa, Azja południowo-wschodnia, Indie Południowe i Australia południowo-wschodnia.
>
> [Zarejestruj się w wersji zapoznawczej](#signup) poniżej.

## <a name="what-you-can-do-with-r"></a>Co można zrobić przy użyciu języka R

Korzystaj z możliwości języka R, aby dostarczać zaawansowaną analizę i uczenie maszynowe w bazie danych. W ten sposób obliczenia i przetwarzanie przenoszone są do miejsca przechowywania danych, co eliminuje konieczność ściągania danych przez sieć. Ponadto można wykorzystać możliwości pakiety języka R enterprise, aby dostarczać zaawansowane funkcje analityczne na dużą skalę.

Usługa Machine Learning Services obejmuje podstawową dystrybucję języka R z nakładką w postaci korporacyjnych pakietów języka R firmy Microsoft. Funkcje i algorytmy języka R firmy Microsoft zostały opracowane z myślą o skalowaniu i użyteczności, zapewniając analizę predykcyjną, modelowanie statystyczne, wizualizacje danych i najwyższej klasy algorytmy uczenia maszynowego.

### <a name="r-packages"></a>Pakiety języka R

Najpopularniejsze pakiety języka R typu open source są wstępnie zainstalowane w usługach Machine Learning. Dostępne są również następujące pakiety języka R od firmy Microsoft:

| Pakietu języka R | Opis|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | Microsoft R Open jest rozszerzona dystrybucja języka R od firmy Microsoft. Jest to kompletna platforma open source do statystycznego analizy i analizy danych. Jest oparta na i 100% zgodny z języka R i zawiera dodatkowe możliwości w celu zwiększenia wydajności i powtarzalności. |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | Kolekcję funkcji RevoScaleR jest biblioteka podstawowa skalowalne funkcje języka R. w tej bibliotece znajdują się wśród najczęściej używanych. Przekształcenia danych i manipulowania, statystycznego podsumowania, wizualizacji i wiele form, modelowania i analiz, znajdują się w tych bibliotek. Ponadto funkcje w tych bibliotek automatycznie rozkładają obciążenie w obrębie dostępnych rdzeni dla przetwarzania z możliwością pracować nad fragmentów danych, które są koordynowane i zarządzana przez aparat obliczeń równoległych. |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | MicrosoftML dodaje algorytmów uczenia maszynowego, aby utworzyć niestandardowe modele analizy tekstu, analiza obrazu i analiza tonacji. |

Oprócz wstępnie zainstalowanych pakietów, możliwe jest również [zainstalować dodatkowe pakiety](sql-database-machine-learning-services-add-r-packages.md).

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>Tworzenie konta na potrzeby korzystania z wersji zapoznawczej

Aby zarejestrować się w publicznej wersji zapoznawczej, wykonaj następujące kroki:

1. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz konto](https://azure.microsoft.com/free/).

2. Wyślij wiadomość e-mail do firmy Microsoft na adres [sqldbml@microsoft.com](mailto:sqldbml@microsoft.com), aby utworzyć konto na potrzeby korzystania z publicznej wersji zapoznawczej. Publiczna wersja zapoznawcza usługi Machine Learning (z językiem R) w usłudze SQL Database nie jest włączona domyślnie.

Po zarejestrowaniu w programie Microsoft będziesz dołączyć do publicznej wersji zapoznawczej i włączenia R do istniejącej lub nowej bazy danych.

Usługi Machine Learning przy użyciu języka R nie jest zalecane w przypadku obciążeń produkcyjnych w publicznej wersji zapoznawczej.

## <a name="next-steps"></a>Kolejne kroki

- Zobacz [klucza różnice w stosunku do programu SQL Server Machine Learning Services](sql-database-machine-learning-services-differences.md).
- Aby dowiedzieć się, jak używać języka R do wykonywania zapytań SQL bazy danych usług Azure Machine Learning (wersja zapoznawcza), zobacz [przewodnika Szybki Start](sql-database-connect-query-r.md).
- Aby rozpocząć korzystanie z niektórych prostych skryptów języka R, zobacz [tworzenia i uruchamiania prostych skryptów języka R w usłudze Azure SQL Database usług Machine Learning (wersja zapoznawcza)](sql-database-quickstart-r-create-script.md).
