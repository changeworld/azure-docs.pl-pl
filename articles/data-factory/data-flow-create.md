---
title: Utwórz mapowanie przepływu danych w usłudze Azure Data Factory
description: Utwórz mapowanie przepływu danych w usłudze Azure Data Factory
author: WenJason
ms.author: v-jay
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
origin.date: 02/12/2019
ms.date: 04/22/2019
ms.openlocfilehash: bb6ae9f97d681625218118b8adca116de1c0fb21
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60883741"
---
# <a name="create-azure-data-factory-data-flow"></a>Utworzenie przepływu danych fabryki danych platformy Azure

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Mapowanie przepływu danych w usłudze ADF umożliwiają do przekształcania danych na dużą skalę bez pisania wymagane. Można zaprojektować zadanie przekształcania danych w Projektancie przepływu danych, tworząc szereg przekształcenia. Uruchom z dowolnej liczby przekształceń źródła, następuje kroki przekształcania danych. Następnie należy wykonać przepływu danych z obiektu sink z zawarciem wyniki w lokalizacji docelowej.

Rozpocznij pracę od utworzenia nowej fabryki danych V2 z witryny Azure Portal. Po utworzeniu Twojej nowej fabryki, kliknij Kafelek "Tworzenie i monitorowanie", aby uruchomić interfejs użytkownika usługi Data Factory.

![Opcje przepływu danych](media/data-flow/v2dataflowportal.png "utworzyć przepływ danych")

Gdy interfejs użytkownika usługi Data Factory, można użyć, przykładowe dane przepływają. Przykłady są dostępne w galerii szablonów usługi ADF. W usłudze ADF utworzyć "Potok za pomocą szablonu" i wybierz kategorię, przepływ danych w galerii szablonów.

![Opcje przepływu danych](media/data-flow/template.png "utworzyć przepływ danych")

Zostanie wyświetlony monit wpisz informacje o Twoim koncie usługi Azure Blob Storage.

![Opcje przepływu danych](media/data-flow/template2.png "przepływ danych tworzenia 2")

[Dane używane na potrzeby tych przykładów można znaleźć tutaj](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata). Pobierz przykładowe dane i przechowywać pliki na kontach usługi Azure Blob storage, dzięki czemu możliwe jest wykonanie przykładów.

## <a name="create-new-data-flow"></a>Tworzenie nowego przepływu danych

Utwórz zasób "znak plus" znajdujący się w Interfejsie ADF do utworzenia przepływu danych użycia

![Opcje przepływu danych](media/data-flow/newresource.png "nowy zasób")

## <a name="next-steps"></a>Kolejne kroki

Rozpocząć tworzenie swojej Przekształcanie danych przy użyciu [źródła przekształcenia](data-flow-source.md).
