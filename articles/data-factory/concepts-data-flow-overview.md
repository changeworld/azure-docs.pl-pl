---
title: Mapowanie Przegląd przepływu danych w usłudze Azure Data Factory
description: Mapowanie przepływu danych w usłudze Azure Data Factory wyjaśnienie — omówienie
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: ac07c0207f9463107b0c354bcd690cd9cc3981f4
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56213486"
---
# <a name="what-are-mapping-data-flows-in-azure-data-factory"></a>Co to jest mapowanie przepływu danych w usłudze Azure Data Factory?

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Mapowanie dane przepływają umożliwić inżynierom danych tworzenie logiki przekształcania danych graficznych, bez konieczności pisania kodu. Wynikowy przepływów danych są wykonywane jako działań w ramach potoki fabryki danych Azure przy użyciu klastrów usługi Azure Databricks skalowanych w poziomie.

Celem przepływu danych fabryki danych Azure jest zapewnienie w pełni wygląd bez konieczności kodowania. Przepływu danych zostaną wykonane zgodnie z własnego klastra wykonywania, przetwarzanie danych skalowanych w poziomie. Usługa Azure Data Factory obsługuje wszystkie translacji kodu, optymalizacja ścieżki i wykonanie zadań przepływu danych.

Rozpocznij od utworzenia przepływów danych w trybie debugowania, dzięki czemu można sprawdzić poprawność logiki przekształcania interaktywnie. Następnie Dodaj działanie przepływu danych do potoku do wykonania i test dane przepływu w potoku debugowania lub za pomocą "Wyzwól teraz" w potoku przetestowanie przepływu danych z potokiem działania.

A następnie będzie planowanie i monitorowanie działań przepływu danych przy użyciu potoków usługi Azure Data Factory, które są wykonywane działania przepływu danych.

Przełącznik tryb debugowania na powierzchni projektowej przepływ danych umożliwia tworzenie interaktywnych przekształcenia danych. Tryb debugowania udostępnia środowisko przygotowywania danych do tworzenia przepływu danych.
