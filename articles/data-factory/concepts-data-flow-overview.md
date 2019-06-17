---
title: Mapowanie Przegląd przepływu danych w usłudze Azure Data Factory
description: Mapowanie przepływu danych w usłudze Azure Data Factory wyjaśnienie — omówienie
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 051886f98d6d35594336291bbb2defb2a4acdfc5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65233048"
---
# <a name="what-are-mapping-data-flows"></a>Co to jest mapowanie przepływu danych?

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Mapowanie dane przepływają są Przekształcanie zaprojektować wizualnie danych w usłudze Azure Data Factory. Przepływy danych umożliwiają inżynierom danych tworzenie logiki przekształcania danych graficznych, bez konieczności pisania kodu. Wynikowy przepływów danych są wykonywane jako działań w ramach potoki fabryki danych Azure przy użyciu klastrów usługi Azure Databricks skalowanych w poziomie.

Celem przepływu danych fabryki danych Azure jest zapewnienie w pełni wygląd bez konieczności kodowania. Przepływu danych zostaną wykonane zgodnie z własnego klastra wykonywania, przetwarzanie danych skalowanych w poziomie. Usługa Azure Data Factory obsługuje wszystkie translacji kodu, optymalizacja ścieżki i wykonanie zadań przepływu danych.

Rozpocznij od utworzenia przepływów danych w trybie debugowania, dzięki czemu można sprawdzić poprawność logiki przekształcania interaktywnie. Następnie Dodaj działanie przepływu danych do potoku do wykonania i test dane przepływu w potoku debugowania lub za pomocą "Wyzwól teraz" w potoku przetestowanie przepływu danych z potokiem działania.

A następnie będzie planowanie i monitorowanie działań przepływu danych przy użyciu potoków usługi Azure Data Factory, które są wykonywane działania przepływu danych.

Przełącznik tryb debugowania na powierzchni projektowej przepływ danych umożliwia tworzenie interaktywnych przekształcenia danych. Tryb debugowania udostępnia środowisko przygotowywania danych do tworzenia przepływu danych.
