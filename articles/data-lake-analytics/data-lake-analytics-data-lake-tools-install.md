---
title: Instalowanie narzędzi Azure Data Lake Tools for Visual Studio
description: W tym artykule opisano sposób instalowania narzędzi usługi Azure Data Lake tools dla programu Visual Studio.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 08f0456b6a8b663f0af68fbb179de14b9e3acf79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70914100"
---
# <a name="install-data-lake-tools-for-visual-studio"></a>Instalacja narzędzi Data Lake Tools for Visual Studio

Dowiedz się, jak tworzyć konta usługi Azure Data Lake Analytics za pomocą programu Visual Studio. Można zdefiniować zadania w [U-SQL](data-lake-analytics-u-sql-get-started.md) i przesłać zadania do usługi Data Lake Analytics. Aby uzyskać więcej informacji na temat usługi Data Lake Analytics, zobacz [omówienie usługi Azure Data Lake Analytics](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

* **Visual Studio**: obsługiwane są wszystkie wersje z wyjątkiem Express.

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Program Visual Studio 2013

* **Zestaw Microsoft Azure SDK dla platformy .NET** w wersji 2.7.1 lub nowszej. Można go zainstalować przy użyciu [Instalatora platformy internetowej](https://www.microsoft.com/web/downloads/platform.aspx).
* Konto usługi **Data Lake Analytics**. Aby utworzyć konto, zobacz artykuł [Wprowadzenie do pracy z usługą Azure Data Lake Analytics za pomocą witryny Azure Portal](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio-2017-or-visual-studio-2019"></a>Instalowanie narzędzi usługi Azure Data Lake tools dla programu Visual Studio 2017 lub Visual Studio 2019

Narzędzia usługi Azure Data Lake Tools for Visual Studio są obsługiwane w programie Visual Studio 2017 15.3 lub nowszym. Narzędzie jest częścią **przechowywania danych i przetwarzania** oraz obciążeń **deweloperskich platformy Azure.** Włącz jedno z tych dwóch obciążeń w ramach instalacji programu Visual Studio.

Włącz obciążenie związane z **przechowywaniem i przetwarzaniem danych,** jak pokazano na rysunku:

![Włączanie obciążenia związane z przechowywaniem i przetwarzaniem danych](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-01.png)

Włącz obciążenie **deweloperów platformy Azure,** jak pokazano na rysunku:

![Wybieranie obciążenia dewelopera platformy Azure](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-02.png)

## <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>Instalowanie narzędzi Azure Data Lake Tools for Visual Studio w wersji 2013 i 2015

Pobierz i zainstaluj [narzędzia Microsoft Azure Data Lake i Stream Analytics Tools dla programu Visual Studio ](https://aka.ms/adltoolsvs). Po instalacji program Visual Studio ma następujące zmiany:

* Węzeł**Azure** **eksploratora** > serwera zawiera węzeł analizy usługi **Data Lake.**
* Menu **Narzędzia** zawiera element **Data Lake**.

## <a name="next-steps"></a>Następne kroki

* Aby rejestrować informacje diagnostyczne, zobacz [Uzyskiwanie dostępu do dzienników diagnostycznych dla usługi Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Aby uzyskać informacje na temat bardziej złożonego zapytania, zobacz temat [Analizowanie dzienników witryn sieci Web przy użyciu usługi Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Aby użyć widoku wykonywania wierzchołków, zobacz [Użyj widoku wykonania wierzchołka w narzędziach usługi Data Lake dla programu Visual Studio.](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)
