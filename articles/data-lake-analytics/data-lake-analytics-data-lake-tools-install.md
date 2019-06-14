---
title: Instalowanie narzędzi Azure Data Lake Tools for Visual Studio
description: W tym artykule opisano sposób instalowania narzędzi Azure Data Lake Tools dla programu Visual Studio.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: conceptual
ms.date: 05/03/2018
ms.openlocfilehash: 3269d38b691ec4dd9573a241c89dadc285787143
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60408140"
---
# <a name="install-data-lake-tools-for-visual-studio"></a>Instalacja narzędzi Data Lake Tools for Visual Studio

Poznaj sposoby tworzenia kont usługi Azure Data Lake Analytics przy użyciu programu Visual Studio, definiowania zadań w języku [U-SQL](data-lake-analytics-u-sql-get-started.md) oraz przesyłania zadań do usługi Data Lake Analytics. Więcej informacji na temat usługi Data Lake Analytics można znaleźć w artykule [Omówienie usługi Azure Data Lake Analytics](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

* **Visual Studio**: Obsługiwane są wszystkie wersje, z wyjątkiem Express.
    * Visual Studio 2017
    * Visual Studio 2015
    * Visual Studio 2013
* **Zestaw Microsoft Azure SDK dla platformy .NET** w wersji 2.7.1 lub nowszej.  Można go zainstalować przy użyciu [Instalatora platformy internetowej](https://www.microsoft.com/web/downloads/platform.aspx).
* Konto usługi **Data Lake Analytics**. Aby utworzyć konto, zobacz artykuł [Wprowadzenie do pracy z usługą Azure Data Lake Analytics za pomocą witryny Azure Portal](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio-2017"></a>Instalowanie narzędzi Azure Data Lake Tools for Visual Studio 2017

Narzędzia Azure Data Lake Tools for Visual Studio są obsługiwane w programie Visual Studio 2017 15.3 lub nowszym. Narzędzia te stanowią część obciążeń **Magazyn danych i przetwarzanie** oraz **Tworzenie aplikacji na platformie Azure** w instalatorze programu Visual Studio. Włącz jedno z tych dwóch obciążeń w ramach instalacji programu Visual Studio.  

Włącz **przechowywanie i przetwarzanie danych** obciążenia, jak pokazano: ![Włącz obciążenie przetwarzania i przechowywania danych](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2017-install-01.png)

Włącz **programowanie na platformie Azure** obciążenia, jak pokazano: ![Włącz obciążenie programistyczne platformy Azure](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2017-install-02.png)

## <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>Instalowanie narzędzi Azure Data Lake Tools for Visual Studio w wersji 2013 i 2015

Narzędzia Azure Data Lake Tools for Visual Studio możesz pobrać i zainstalować [z Centrum pobierania](https://aka.ms/adltoolsvs). Po instalacji pamiętaj, że:
* Węzeł **Eksplorator serwera** > **Azure** zawiera węzeł **Data Lake Analytics**. 
* Menu **Narzędzia** zawiera element **Data Lake**.


## <a name="next-steps"></a>Następne kroki
* Aby rejestrować informacje diagnostyczne, zobacz [Accessing diagnostics logs for Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md) (Dostęp do dzienników diagnostycznych usługi Azure Data Lake Analytics)
* Aby uzyskać informacje na temat bardziej złożonego zapytania, zobacz temat [Analizowanie dzienników witryn sieci Web przy użyciu usługi Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Aby użyć widoku wykonania wierzchołka, zobacz [korzystanie z widoku wykonania wierzchołka w narzędziach Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)
