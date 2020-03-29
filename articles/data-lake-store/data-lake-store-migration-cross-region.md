---
title: Migracja międzyregionowa usługi Azure Data Lake Storage Gen1 | Dokumenty firmy Microsoft
description: Dowiedz się więcej o migracji między regionami dla usługi Azure Data Lake Storage Gen1.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60518459"
---
# <a name="migrate-azure-data-lake-storage-gen1-across-regions"></a>Migrowanie usługi Azure Data Lake Storage Gen1 między regionami

Jak usługa Azure Data Lake Storage Gen1 staje się dostępna w nowych regionach, można wybrać opcję jednorazowej migracji, aby skorzystać z nowego regionu. Dowiedz się, co należy wziąć pod uwagę podczas planowania i ukończenia migracji.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Aby uzyskać więcej informacji, zobacz [Tworzenie bezpłatnego konta platformy Azure już dziś](https://azure.microsoft.com/pricing/free-trial/).
* **Konto Data Lake Storage Gen1 w dwóch różnych regionach**. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do usługi Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).
* **Fabryka danych platformy Azure**. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do usługi Azure Data Factory](../data-factory/introduction.md).


## <a name="migration-considerations"></a>Zagadnienia dotyczące migracji

Najpierw zidentyfikuj strategię migracji, która działa najlepiej dla aplikacji, która zapisuje, odczytuje lub przetwarza dane w umiarze magazynu usługi Data Lake Gen1. Po wybraniu strategii należy wziąć pod uwagę wymagania dotyczące dostępności aplikacji i przestojów, które występują podczas migracji. Na przykład najprostszym podejściem może być użycie modelu migracji do chmury "lift-and-shift". W tym podejściu należy wstrzymać aplikację w istniejącym regionie, podczas gdy wszystkie dane są kopiowane do nowego regionu. Po zakończeniu procesu kopiowania należy wznowić aplikację w nowym regionie, a następnie usunąć stare konto Usługi Data Lake Storage Gen1. Wymagany jest przestój podczas migracji.

Aby skrócić czas przestojów, można natychmiast rozpocząć pozyskiwania nowych danych w nowym regionie. Jeśli masz wymagane minimalne dane, uruchom aplikację w nowym regionie. W tle kontynuuj kopiowanie starszych danych z istniejącego konta Data Lake Storage Gen1 do nowego konta Data Lake Storage Gen1 w nowym regionie. Korzystając z tego podejścia, można dokonać przejścia do nowego regionu z niewielkimi przestojami. Po skopiowaniu wszystkich starszych danych usuń stare konto Usługi Data Lake Storage Gen1.

Inne ważne szczegóły, które należy wziąć pod uwagę podczas planowania migracji, to:

* **Objętość danych**. Ilość danych (w gigabajtach, liczba plików i folderów itd.) wpływa na czas i zasoby potrzebne do migracji.

* **Data Lake Storage Gen1 nazwa konta**. Nowa nazwa konta w nowym regionie musi być unikatowa globalnie. Na przykład nazwa starego konta Data Lake Storage Gen1 we wschodniej części US 2 może być contosoeastus2.azuredatalakestore.net. Nowe konto Data Lake Storage Gen1 można nazwać w contosonortheu.azuredatalakestore.net Północnej UE.

* **Narzędzia**. Zaleca się użycie [działania kopiowania usługi Azure Data Factory Copy do](../data-factory/connector-azure-data-lake-store.md) kopiowania plików Data Lake Storage Gen1. Usługa Data Factory obsługuje przenoszenie danych z wysoką wydajnością i niezawodnością. Należy pamiętać, że usługa Data Factory kopiuje tylko hierarchię folderów i zawartość plików. Do nowego konta należy ręcznie zastosować wszystkie listy kontroli dostępu (ACL), których używasz na starym koncie. Aby uzyskać więcej informacji, w tym cele dotyczące wydajności dla scenariuszy najlepszych przypadków, zobacz [przewodnik Wydajność działania kopiowania i dostrajanie](../data-factory/copy-activity-performance.md). Jeśli chcesz, aby dane były kopiowane szybciej, może być konieczne użycie dodatkowych jednostek przenoszenia danych w chmurze. Niektóre inne narzędzia, takie jak AdlCopy, nie obsługują kopiowania danych między regionami.  

* **Opłaty za przepustowość**. [Opłaty za przepustowość](https://azure.microsoft.com/pricing/details/bandwidth/) mają zastosowanie, ponieważ dane są przesyłane z regionu platformy Azure.

* **listy ACL dotyczące danych**. Zabezpiecz swoje dane w nowym regionie, stosując listy ACL do plików i folderów. Aby uzyskać więcej informacji, zobacz [zabezpieczanie danych przechowywanych w usłudze Azure Data Lake Storage Gen1](data-lake-store-secure-data.md). Zaleca się użycie migracji do aktualizowania i dostosowywania list ACL. Możesz użyć ustawień podobnych do bieżących ustawień. Można wyświetlić listy ACL, które są stosowane do dowolnego pliku przy użyciu portalu Azure, [poleceń cmdlet programu PowerShell](/powershell/module/az.datalakestore/get-azdatalakestoreitempermission)lub zestawów SDK.  

* **Lokalizacja usług analitycznych**. Aby uzyskać najlepszą wydajność, usługi analityczne, takie jak usługa Azure Data Lake Analytics lub Usługa Azure HDInsight, powinny znajdować się w tym samym regionie co dane.  

## <a name="next-steps"></a>Następne kroki
* [Omówienie usługi Azure Data Lake Storage Gen1](data-lake-store-overview.md)
