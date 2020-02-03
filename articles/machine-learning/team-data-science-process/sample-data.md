---
title: Przykładowe dane w różnych lokalizacjach usługi Azure Storage — proces nauki danych zespołu
description: Przykładowe dane w usłudze Azure blob kontenerów, programu SQL Server i tabel, aby zmniejszyć jego rozmiar mniejszy, ale reprezentatywny i łatwiejsze w obsłudze programu Hive.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718607"
---
# <a name="heading"></a>Przykładowe dane w kontenerach obiektów blob platformy Azure, SQL Server i tabelach Hive

Następujące artykuły zawierają opis przykładowe dane są przechowywane w jednej z trzech różnych lokalizacji platformy Azure:

* [**Dane kontenera obiektów blob platformy Azure**](sample-data-blob.md) są próbkowane przez pobranie go programowo, a następnie próbkowanie go przy użyciu przykładowego kodu w języku Python.
* [**SQL Server dane**](sample-data-sql-server.md) są próbkowane przy użyciu języka SQL i programowania w języku Python. 
* Próbkowanie [**danych tabeli Hive**](sample-data-hive.md) przy użyciu zapytań programu Hive.

To zadanie próbkowania jest krokiem w [procesie nauki o danych zespołowych (przetwarzania TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

**Dlaczego dane przykładowe?**

Jeśli zestaw danych, która ma zostać analizowanie jest duża, zazwyczaj przyczyną jest dobrym pomysłem jest obniżenie częstotliwości próbkowania danych, aby zmniejszyć jego rozmiar mniejszy, ale reprezentatywny i łatwiejsze w zarządzaniu. Downsizing mogą ułatwić zrozumienie, eksplorację i inżynierowanie danych. Ta rola próbkowania w procesie Cortana Analytics ma na celu umożliwienie szybkiego tworzenia prototypów funkcji przetwarzania danych i modeli uczenia maszynowego.

