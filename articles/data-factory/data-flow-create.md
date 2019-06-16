---
title: Utwórz mapowanie przepływu danych w usłudze Azure Data Factory
description: Tworzenie usługi Azure Data Factory mapowanie przepływ danych
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 366ed60534544ebbf889e2f72fe703f9b821f871
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65235652"
---
# <a name="create-azure-data-factory-data-flow"></a>Utworzenie przepływu danych fabryki danych platformy Azure

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Mapowanie przepływu danych w usłudze ADF umożliwiają do przekształcania danych na dużą skalę bez pisania wymagane. Można zaprojektować zadanie przekształcania danych w Projektancie przepływu danych, tworząc szereg przekształcenia. Uruchom z dowolnej liczby przekształceń źródła, następuje kroki przekształcania danych. Następnie należy wykonać przepływu danych z obiektu sink z zawarciem wyniki w lokalizacji docelowej.

Rozpocznij pracę od utworzenia nowej fabryki danych V2 w witrynie Azure portal. Po utworzeniu Twojej nowej fabryki, kliknij Kafelek "Tworzenie i monitorowanie", aby uruchomić interfejs użytkownika usługi Data Factory.

![Opcje przepływu danych](media/data-flow/v2portal.png "utworzyć przepływ danych")

Gdy interfejs użytkownika usługi Data Factory, można użyć, przykładowe dane przepływają. Przykłady są dostępne w galerii szablonów usługi ADF. W usłudze ADF utworzyć "Potok za pomocą szablonu" i wybierz kategorię, przepływ danych w galerii szablonów.

![Opcje przepływu danych](media/data-flow/template.png "utworzyć przepływ danych")

Zostanie wyświetlony monit wpisz informacje o Twoim koncie usługi Azure Blob Storage.

![Opcje przepływu danych](media/data-flow/template2.png "przepływ danych tworzenia 2")

[Dane używane na potrzeby tych przykładów można znaleźć tutaj](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata). Pobierz przykładowe dane i przechowywać pliki na kontach usługi Azure Blob storage, dzięki czemu możliwe jest wykonanie przykładów.

## <a name="create-new-data-flow"></a>Tworzenie nowego przepływu danych

Utwórz zasób "znak plus" znajdujący się w Interfejsie ADF do utworzenia przepływu danych użycia.

![Opcje przepływu danych](media/data-flow/newresource.png "nowy zasób")

## <a name="next-steps"></a>Kolejne kroki

Rozpocząć tworzenie swojej Przekształcanie danych przy użyciu [źródła przekształcenia](data-flow-source.md).
