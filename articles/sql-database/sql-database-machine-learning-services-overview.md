---
title: Machine Learning Services z językiem R (wersja zapoznawcza)
description: W tym artykule opisano Azure SQL Database Machine Learning Services (z językiem R) i wyjaśniono, jak działa.
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
ms.date: 11/20/2019
ms.openlocfilehash: ca223de2bc0b26e4968d400ea418761a399dacae
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78364602"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>Azure SQL Database Machine Learning Services z językiem R (wersja zapoznawcza)

Machine Learning Services to funkcja Azure SQL Database używana do wykonywania skryptów języka R w bazie danych. Ta funkcja obejmuje pakiety Microsoft R na potrzeby analizy predykcyjnej i uczenia maszynowego o wysokiej wydajności. Dane relacyjne mogą być używane w skryptach języka R za pomocą procedur składowanych, skryptu T-SQL zawierającego instrukcje języka R lub kodu języka R zawierającego język T-SQL.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

> [!NOTE]
> Wersja zapoznawcza jest dostępna dla pojedynczych baz danych i pul elastycznych przy użyciu modelu zakupu opartego na rdzeń wirtualny w warstwach usług **ogólnych** i **krytycznych dla działalności biznesowej** . W tej początkowej wersji zapoznawczej **warstwa usługi i** wdrożenie **wystąpienia zarządzanego** nie są obsługiwane. Obecnie język R jest jedynym obsługiwanym językiem. W tej chwili język Python nie jest obsługiwany.
>
> Wersja zapoznawcza jest obecnie dostępna w następujących regionach: Europa Zachodnia, Europa Północna, zachodnie stany USA 2, Wschodnie stany USA, Południowo-środkowe stany USA, Północno-środkowe stany USA, Kanada środkowa, Azja Południowo-Wschodnia, Indie Południowe i Australia Południowo-Wschodnia.

## <a name="what-you-can-do-with-r"></a>Co możesz zrobić przy użyciu języka R

Korzystaj z możliwości języka R, aby dostarczać zaawansowaną analizę i uczenie maszynowe w bazie danych. W ten sposób obliczenia i przetwarzanie przenoszone są do miejsca przechowywania danych, co eliminuje konieczność ściągania danych przez sieć. Ponadto możesz wykorzystać możliwości pakietów języka R dla przedsiębiorstw, aby zapewnić zaawansowaną analizę na dużą skalę.

Usługa Machine Learning Services obejmuje podstawową dystrybucję języka R z nakładką w postaci korporacyjnych pakietów języka R firmy Microsoft. Funkcje i algorytmy języka R firmy Microsoft zostały opracowane z myślą o skalowaniu i użyteczności, zapewniając analizę predykcyjną, modelowanie statystyczne, wizualizacje danych i najwyższej klasy algorytmy uczenia maszynowego.

### <a name="r-packages"></a>Pakiety języka R

Najpopularniejsze pakiety języka R Open Source są wstępnie zainstalowane w Machine Learning Services. Dostępne są również następujące pakiety języka R firmy Microsoft:

| Pakiet języka R | Opis|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | Microsoft R Open to ulepszona dystrybucja języka R od firmy Microsoft. Jest to kompletna platforma typu "open source" umożliwiająca analizę statystyczną i naukę danych. Jest on oparty na i 100% zgodny z językiem R i oferuje dodatkowe możliwości w celu zwiększenia wydajności i odtwarzalności. |
| [Kolekcję funkcji revoscaler](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | Kolekcję funkcji revoscaler jest podstawową biblioteką skalowaln R. funkcje w tej bibliotece są między najczęściej używanymi. Przekształceń i manipulowanie danymi, podsumowanie statystyczne, Wizualizacja i wiele form modelowania i analiz można znaleźć w tych bibliotekach. Ponadto funkcje w tych bibliotekach automatycznie dystrybuują obciążenia na dostępne rdzenie do przetwarzania równoległego, dzięki czemu można korzystać z fragmentów danych, które są skoordynowane i zarządzane przez aparat obliczeń. |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | MicrosoftML dodaje algorytmy uczenia maszynowego do tworzenia niestandardowych modeli na potrzeby analizy tekstu, analizy obrazów i analizy tonacji. |

Oprócz wstępnie zainstalowanych pakietów można [zainstalować dodatkowe pakiety](sql-database-machine-learning-services-add-r-packages.md).

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>Tworzenie konta na potrzeby korzystania z wersji zapoznawczej

> [!IMPORTANT]
> Rejestracja w usłudze Azure SQL Database Machine Learning Services (wersja zapoznawcza) jest obecnie ZAMKNIĘTA.

Machine Learning Services przy użyciu języka R nie jest zalecane w przypadku obciążeń produkcyjnych w wersji zapoznawczej.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się [z kluczowymi różnicami w Machine Learning Services SQL Server](sql-database-machine-learning-services-differences.md).
- Aby dowiedzieć się, jak używać języka R do wykonywania zapytań w Azure SQL Database Machine Learning Services (wersja zapoznawcza), zobacz [Przewodnik Szybki Start](sql-database-connect-query-r.md).
- Aby zacząć korzystać z niektórych prostych skryptów języka R, zobacz [Tworzenie i Uruchamianie prostych skryptów języka r w Azure SQL Database Machine Learning Services (wersja zapoznawcza)](sql-database-quickstart-r-create-script.md).
