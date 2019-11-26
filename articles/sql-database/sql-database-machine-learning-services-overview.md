---
title: Machine Learning Services with R (preview)
description: This article describes Azure SQL Database Machine Learning Services (with R) and explains how it works.
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
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74462356"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>Azure SQL Database Machine Learning Services with R (preview)

Machine Learning Services is a feature of Azure SQL Database, used for executing in-database R scripts. The feature includes Microsoft R packages for high-performance predictive analytics and machine learning. The relational data can be used in R scripts through stored procedures, T-SQL script containing R statements, or R code containing T-SQL.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

> [!NOTE]
> The preview is available for single databases and elastic pools using the vCore-based purchasing model in the **general purpose** and **business critical** service tiers. In this initial preview, the **hyperscale** service tier and the **managed instance** deployment option are not supported. Obecnie język R jest jedynym obsługiwanym językiem. W tej chwili język Python nie jest obsługiwany.
>
> The preview is currently available in the following regions: West Europe, North Europe, West US 2, East US, South Central US, North Central US, Canada Central, Southeast Asia, India South, and Australia Southeast.

## <a name="what-you-can-do-with-r"></a>What you can do with R

Korzystaj z możliwości języka R, aby dostarczać zaawansowaną analizę i uczenie maszynowe w bazie danych. W ten sposób obliczenia i przetwarzanie przenoszone są do miejsca przechowywania danych, co eliminuje konieczność ściągania danych przez sieć. Also, you can leverage the power of enterprise R packages to deliver advanced analytics at scale.

Usługa Machine Learning Services obejmuje podstawową dystrybucję języka R z nakładką w postaci korporacyjnych pakietów języka R firmy Microsoft. Funkcje i algorytmy języka R firmy Microsoft zostały opracowane z myślą o skalowaniu i użyteczności, zapewniając analizę predykcyjną, modelowanie statystyczne, wizualizacje danych i najwyższej klasy algorytmy uczenia maszynowego.

### <a name="r-packages"></a>R packages

Most common open-source R packages are pre-installed in Machine Learning Services. The following R packages from Microsoft are also included:

| R package | Opis|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | Microsoft R Open is the enhanced distribution of R from Microsoft. It is a complete open-source platform for statistical analysis and data science. It is based on and 100% compatible with R, and includes additional capabilities for improved performance and reproducibility. |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | RevoScaleR is the primary library for scalable R. Functions in this library are among the most widely used. Data transformations and manipulation, statistical summarization, visualization, and many forms of modeling and analyses are found in these libraries. Additionally, functions in these libraries automatically distribute workloads across available cores for parallel processing, with the ability to work on chunks of data that are coordinated and managed by the calculation engine. |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | MicrosoftML adds machine learning algorithms to create custom models for text analysis, image analysis, and sentiment analysis. |

In addition to the pre-installed packages, you can [install additional packages](sql-database-machine-learning-services-add-r-packages.md).

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>Utwórz konto na potrzeby korzystania z wersji zapoznawczej

> [!IMPORTANT]
> Sign up for Azure SQL Database Machine Learning Services (preview) is currently closed.

Machine Learning Services with R is not recommended for production workload during the preview.

## <a name="next-steps"></a>Następne kroki

- See the [key differences from SQL Server Machine Learning Services](sql-database-machine-learning-services-differences.md).
- To learn how to use R to query Azure SQL Database Machine Learning Services (preview), see the [Quickstart guide](sql-database-connect-query-r.md).
- To get started with some simple R scripts, see [Create and run simple R scripts in Azure SQL Database Machine Learning Services (preview)](sql-database-quickstart-r-create-script.md).
