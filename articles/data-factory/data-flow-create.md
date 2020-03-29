---
title: Tworzenie przepływu danych mapowania
description: Jak utworzyć przepływ danych mapowania usługi Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/12/2019
ms.openlocfilehash: 2eb455ba6fa40538bfa03018be47232066036c23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930424"
---
# <a name="create-azure-data-factory-data-flow"></a>Tworzenie przepływu danych usługi Azure Data Factory



Mapowanie przepływów danych w ujmiju ADF umożliwiają przekształcanie danych na dużą skalę bez konieczności kodowania. Zadanie przekształcania danych można zaprojektować w projektancie przepływu danych, konstruując serię przekształceń. Zacznij od dowolnej liczby przekształceń źródłowych, a następnie kroków przekształcania danych. Następnie należy ukończyć przepływ danych z zlewu do lądowania wyniki w miejscu docelowym.

Rozpocznij od pierwszego utworzenia nowej fabryki danych w wersji 2 z witryny Azure portal. Po utworzeniu nowej fabryki kliknij kafelek "Autor & Monitor", aby uruchomić interfejs użytkownika fabryki danych.

![Opcje przepływu danych](media/data-flow/v2portal.png "tworzenie przepływu danych")

Gdy jesteś w interfejsie użytkownika fabryki danych, można użyć przykładowego przepływów danych. Przykłady są dostępne w Galerii szablonów podajnika ADF. W ujmij podajnika ADF utwórz "Potok z szablonu" i wybierz kategorię Przepływ danych z galerii szablonów.

![Opcje przepływu danych](media/data-flow/template.png "tworzenie przepływu danych")

Zostanie wyświetlony monit o wprowadzenie informacji o koncie usługi Azure Blob Storage.

![Opcje przepływu danych](media/data-flow/template2.png "przepływ danych tworzy 2")

[Dane używane dla tych próbek można znaleźć tutaj](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata). Pobierz przykładowe dane i przechowuj pliki na kontach magazynu obiektów Blob platformy Azure, dzięki czemu można wykonać przykłady.

## <a name="create-new-data-flow"></a>Tworzenie nowego przepływu danych

Użyj przycisku Utwórz znak "plus" w interfejsie użytkownika usługi ADF, aby utworzyć przepływy danych.

![Opcje przepływu danych](media/data-flow/newresource.png "Nowy zasób")

## <a name="next-steps"></a>Następne kroki

Rozpocznij tworzenie transformacji danych za pomocą [transformacji źródłowej](data-flow-source.md).
