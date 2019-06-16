---
title: Migracja między regionami na platformę Azure Data Lake Storage Gen1 | Dokumentacja firmy Microsoft
description: Więcej informacji na temat usługi Azure Data Lake Storage Gen1 migracja między regionami.
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
ms.openlocfilehash: 0bf0843314f38c0de28820c82e95b7921297bf40
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60518459"
---
# <a name="migrate-azure-data-lake-storage-gen1-across-regions"></a>Migrowanie z usługi Azure Data Lake Storage Gen1 między regionami

Azure Data Lake Storage Gen1 staje się dostępna w nowych regionach, można w celu przeprowadzenia jednorazowej migracji, aby móc korzystać z nowego regionu. Dowiedz się, co należy wziąć pod uwagę przy planowaniu i przeprowadzić migrację.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Aby uzyskać więcej informacji, zobacz [Utwórz bezpłatne konto platformy Azure już dziś](https://azure.microsoft.com/pricing/free-trial/).
* **Konta Data Lake Storage Gen1 w dwóch różnych regionach**. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z usługą Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).
* **Usługi Azure Data Factory**. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do usługi Azure Data Factory](../data-factory/introduction.md).


## <a name="migration-considerations"></a>Zagadnienia dotyczące migracji

Najpierw Zidentyfikuj strategię migracji, która jest najlepsza dla aplikacji, który zapisuje odczytuje i przetwarza dane w Data Lake Storage Gen1. W przypadku wybrania strategii, należy wziąć pod uwagę wymagania dotyczące dostępności aplikacji i czas przestoju, który występuje podczas migracji. Na przykład Twoje najprostsza metoda może być do korzystania z modelu migracji "lift-and-shift" chmury. W tym podejściu wstrzymaniu aplikacji w Twoim regionie istniejących podczas wszystkie Twoje dane zostały skopiowane do nowego regionu. Po zakończeniu procesu kopiowania Wznów swoją aplikację w nowym regionie, a następnie usuń stare konto Data Lake Storage Gen1. Wymagany jest także przestój podczas migracji.

Aby skrócić czas przestoju, może natychmiast uruchomić, umożliwiając pozyskiwanie nowych danych w nowym regionie. Jeśli masz minimalną ilość danych, które są potrzebne, uruchom aplikację w nowym regionie. W tle nadal mają zostać skopiowane do nowego konta Data Lake Storage Gen1 w nowym regionie istniejącego konta Data Lake Storage Gen1 starszych danych. Za pomocą tej metody, można wprowadzać przełącznika nowy region nieco przestojów. Gdy wszystkie starsze dane zostały skopiowane, Usuń stare konto Data Lake Storage Gen1.

Inne ważne szczegóły, które należy uwzględnić podczas planowania migracji są:

* **Ilość danych**. Ilość danych (w gigabajtach, liczbę plików i folderów i tak dalej) wpływa na czas i zasoby, które są potrzebne do migracji.

* **Nazwa konta usługi Data Lake Storage Gen1**. Nazwę nowego konta w nowym regionie musi być unikatowa w skali globalnej. Na przykład nazwa konta usługi Data Lake Storage Gen1 stare w regionie wschodnie stany USA 2, może być contosoeastus2.azuredatalakestore.net. Można nadać nazwę nowego konta Data Lake Storage Gen1 w contosonortheu.azuredatalakestore.net Europa Północna.

* **Narzędzia**. Firma Microsoft zaleca użycie [działania kopiowania w fabryce danych Azure](../data-factory/connector-azure-data-lake-store.md) można skopiować plików Data Lake Storage Gen1. Usługa Data Factory obsługuje przenoszenie danych z wysoką wydajność i niezawodność. Należy pamiętać, że usługi Data Factory kopiuje tylko hierarchii folderów i zawartości tych plików. Musisz ręcznie zastosować wszystkie listy kontroli dostępu (ACL), których używasz na koncie starego do nowego konta. Aby uzyskać więcej informacji, w tym cele wydajności w scenariuszach najlepszego przypadku zobacz [dostrajania przewodnik dotyczący wydajności działania kopiowania i](../data-factory/copy-activity-performance.md). Jeśli chcesz, aby dane skopiowane szybciej, użytkownik może być konieczne użycie dodatkowych jednostek przenoszenia danych w chmurze. Niektóre inne narzędzia, takie jak narzędzia AdlCopy, nie obsługują kopiowanie danych między regionami.  

* **Opłaty za przepustowość**. [Opłaty za przepustowość](https://azure.microsoft.com/pricing/details/bandwidth/) zastosować, ponieważ dane są przesyłane poza region platformy Azure.

* **Listy kontroli dostępu na podstawie posiadanych danych**. Zabezpieczanie danych w nowym regionie, stosując listy kontroli dostępu do plików i folderów. Aby uzyskać więcej informacji, zobacz [Zabezpieczanie danych przechowywanych w usłudze Azure Data Lake magazynu Gen1](data-lake-store-secure-data.md). Zalecamy użycie migracji do aktualizacji i dostosować swoje listy kontroli dostępu. Można użyć ustawień podobny do bieżących ustawień. Można wyświetlić listy kontroli dostępu, które są stosowane do dowolnego pliku przy użyciu witryny Azure portal [poleceń cmdlet programu PowerShell](/powershell/module/az.datalakestore/get-azdatalakestoreitempermission), lub zestawów SDK.  

* **Lokalizacja usługi analizy**. Aby uzyskać najlepszą wydajność usługi analityczne, takie jak Azure Data Lake Analytics lub Azure HDInsight, należy w tym samym regionie, w którym znajdują się dane.  

## <a name="next-steps"></a>Kolejne kroki
* [Omówienie usługi Azure Data Lake Storage Gen1](data-lake-store-overview.md)
