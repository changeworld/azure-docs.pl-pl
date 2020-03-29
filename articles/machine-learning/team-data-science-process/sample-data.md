---
title: Przykładowe dane w różnych lokalizacjach usługi Azure Storage — proces nauki o danych zespołu
description: Przykładowe dane w kontenerach obiektów blob platformy Azure, SQL Server i tabelach hive, aby zmniejszyć je do mniejszego, ale reprezentatywnego i łatwiejszego w zarządzaniu rozmiaru.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 22e91d50227fcb44c7b90478d76379c14161ae05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718607"
---
# <a name="sample-data-in-azure-blob-containers-sql-server-and-hive-tables"></a><a name="heading"></a>Przykładowe dane w kontenerach obiektów blob platformy Azure, programu SQL Server i tabel programu Hive

W poniższych artykułach opisano sposób przykładu danych przechowywanych w jednej z trzech różnych lokalizacji platformy Azure:

* [**Dane kontenera obiektów blob platformy Azure**](sample-data-blob.md) są próbkowane przez pobranie go programowo, a następnie próbkowanie go za pomocą przykładowego kodu języka Python.
* [**Dane programu SQL Server**](sample-data-sql-server.md) są próbkowany przy użyciu języka programowania SQL i języka programowania języka Python. 
* [**Dane tabeli gałęzi**](sample-data-hive.md) są próbkowanie przy użyciu zapytań hive.

To zadanie próbkowania jest krokiem w [procesie nauki o danych zespołu (TDSP).](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)

**Dlaczego przykładowe dane?**

Jeśli zestaw danych, który planujesz analizować, jest duży, zwykle warto pobrać próbkę w dół danych, aby zmniejszyć je do mniejszego, ale reprezentatywnego i łatwiejszego w zarządzaniu rozmiaru. Zmniejszenie rozmiaru może ułatwić zrozumienie danych, eksplorację i inżynierię funkcji. Ta rola próbkowania w procesie analizy Cortany polega na umożliwieniu szybkiego tworzenia prototypów funkcji przetwarzania danych i modeli uczenia maszynowego.

