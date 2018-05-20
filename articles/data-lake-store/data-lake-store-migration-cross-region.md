---
title: Migracja między regionu Azure Data Lake Store | Dokumentacja firmy Microsoft
description: Więcej informacji na temat migracji między regionu Azure Data Lake Store.
services: data-lake-store
documentationcenter: ''
author: swums
manager: amitkul
editor: swums
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: stewu
ms.openlocfilehash: 1199eca457c3f06fdd6a4b68a05da3210ea9a2c9
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="migrate-data-lake-store-across-regions"></a>Migracja usługi Data Lake Store w regionach

Jak usługa Azure Data Lake Store stają się dostępne w nowych regionów, można przeprowadzić migrację jednorazowe, aby móc korzystać z nowego regionu. Dowiedz się, co należy wziąć pod uwagę plan i ukończyć migrację.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Aby uzyskać więcej informacji, zobacz [utworzyć bezpłatne konto platformy Azure obecnie](https://azure.microsoft.com/pricing/free-trial/).
* **Konto usługi Data Lake Store w dwóch różnych regionach**. Aby uzyskać więcej informacji, zobacz [wprowadzenie do usługi Azure Data Lake Store](data-lake-store-get-started-portal.md).
* **Fabryka danych Azure**. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do usługi Azure Data Factory](../data-factory/introduction.md).


## <a name="migration-considerations"></a>Zagadnienia dotyczące migracji

Ustalenie strategii migracji, która jest najlepsza dla aplikacji, która zapisuje lub odczytuje przetwarza dane w usłudze Data Lake Store. W przypadku strategii, należy wziąć pod uwagę wymagania dostępności aplikacji i przestoju, która występuje podczas migracji. Na przykład Twoje najprostsza metoda może być do korzystania z modelu migracji "przyrostu i shift" chmury. W tej metody, w przypadku wstrzymania aplikacji w danym regionie istniejących podczas wszystkie Twoje dane zostaną skopiowane do nowego regionu. Po zakończeniu procesu kopiowania wznowić aplikacji w obszarze nowe, a następnie usuń stare konto usługi Data Lake Store. Wymagany jest także przestój podczas migracji.

Aby skrócić czas przestoju, mogą natychmiast uruchomić wprowadzania nowych danych w nowym regionie. Jeśli masz minimalną ilość danych, które są potrzebne, uruchom aplikację w nowym regionie. W tle nadal do skopiowania istniejącego konta usługi Data Lake Store starszych danych do nowego konta usługi Data Lake Store w nowym regionie. Przy użyciu tej metody, możesz wprowadzić przełącznik do nowego regionu małego przestojów. Po skopiowaniu starszych danych Usuń stare konto usługi Data Lake Store.

Inne ważne informacje, które należy uwzględnić podczas planowania migracji są:

* **Ilość danych**. Ilość danych (w gigabajtach, liczbę plików i folderów i tak dalej) wpływa na czas i zasoby, które są potrzebne do migracji.

* **Nazwa konta usługi Data Lake Store**. W nowym regionie nazwę nowego konta musi być globalnie unikatowe. Na przykład nazwę starego konta usługi Data Lake Store w wschodnie stany USA 2 może być contosoeastus2.azuredatalakestore.net. Możesz nazwać nowego konta usługi Data Lake Store w contosonortheu.azuredatalakestore.net Północna, Europa.

* **Narzędzia**. Firma Microsoft zaleca użycie [działanie kopiowania fabryki danych Azure](../data-factory/connector-azure-data-lake-store.md) do kopiowania plików usługi Data Lake Store. Fabryka danych obsługuje przenoszenie danych o wysokiej wydajności i niezawodności. Należy pamiętać, że fabryki danych kopiuje tylko hierarchii folderów i zawartość plików. Należy ręcznie zastosować wszystkie listy kontroli dostępu (ACL) używanych w ramach konta starego do nowego konta. Aby uzyskać więcej informacji, w tym cele wydajności dla scenariuszy najlepszym zobacz [wydajności działania kopiowania i dostrajania przewodnik](../data-factory/copy-activity-performance.md). Jeśli chcesz, aby dane skopiowane szybciej, konieczne może być za pomocą dodatkowe jednostek przepływu danych w chmurze. Kopiowanie danych między regionami innych narzędzi, takich jak AdlCopy, nie jest obsługiwane.  

* **Opłaty przepustowości**. [Opłaty przepustowości](https://azure.microsoft.com/pricing/details/bandwidth/) zastosować, ponieważ dane są przesyłane poza region platformy Azure.

* **Listy kontroli dostępu na podstawie danych**. Zabezpieczanie danych w nowym regionie, stosując listy kontroli dostępu do plików i folderów. Aby uzyskać więcej informacji, zobacz [Zabezpieczanie danych przechowywanych w usłudze Azure Data Lake Store](data-lake-store-secure-data.md). Firma Microsoft zaleca, należy użyć migracji do aktualizacji, a następnie Dostosuj Twojej listy kontroli dostępu. Można użyć ustawień podobny do bieżących ustawień. Można wyświetlić listy kontroli dostępu, które są stosowane do dowolnego pliku przy użyciu portalu Azure [poleceń cmdlet programu PowerShell](/powershell/module/azurerm.datalakestore/get-azurermdatalakestoreitempermission), ani zestawów SDK.  

* **Lokalizacja usługi analizy**. Aby uzyskać najlepszą wydajność usługi analytics, takich jak Azure Data Lake Analytics lub Azure HDInsight, należy w tym samym regionie, w którym znajdują się dane.  

## <a name="next-steps"></a>Kolejne kroki
* [Omówienie usługi Azure Data Lake Store](data-lake-store-overview.md)
