---
title: Przykłady interfejsu wiersza polecenia platformy Azure — usłudze Azure Media Services | Dokumentacja firmy Microsoft
description: Przykłady interfejsu wiersza polecenia platformy Azure dla usługi Azure Media Services
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 04/15/2018
ms.author: juliako
ms.openlocfilehash: 3328403f5366f168f979a14951da938f26e1aee9
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35938584"
---
# <a name="azure-cli-examples-for-azure-media-services"></a>Przykłady interfejsu wiersza polecenia platformy Azure dla usługi Azure Media Services

Poniższa tabela zawiera linki do przykładów interfejsu wiersza polecenia platformy Azure dla usługi Azure Media Services.

|  |  |
|---|---|
|**Konto**||
| [Utwórz konto usługi Media Services](./scripts/cli-create-account.md) | Tworzy konto usługi Azure Media Services. Ponadto tworzy usługę podmiotu zabezpieczeń, która umożliwia dostęp do interfejsów API można programowo zarządzać kontem. |
| [Resetowanie poświadczeń konta](./scripts/cli-reset-account-credentials.md)|Resetuje Twoje poświadczenia konta, a następnie pobiera ustawienia app.config ponownie.|
|**Zasoby**||
| [Tworzenie zasobów](./scripts/cli-create-asset.md)|Tworzy zasób usługi multimediów, można przekazać zawartości do.|
| [Przekaż plik](./scripts/cli-upload-file-asset.md)|Przekazuje plik lokalny do kontenera magazynu.|
| [Publikowanie elementu zawartości](./scripts/cli-publish-asset.md)| Tworzy Lokalizator przesyłania strumieniowego i otrzymuje w URL przesyłania strumieniowego. |
| **Przekształca** i **zadania**||
| [Utwórz przekształceń](./scripts/cli-create-transform.md)|Pokazuje, jak utworzyć przekształcenia. Przekształcenia opisują prosty przepływ pracy zadań podczas przetwarzania plików wideo lub audio (często określany jako „przepis”).<br/> Zawsze należy sprawdzić, czy przekształcenie z wybraną nazwą i „przepisem” już istnieje. Jeśli tak jest, należy użyć ponownie. |
| [Tworzenie zadań](./scripts/cli-create-jobs.md)|Przesyła zadanie proste przekształcanie kodowania przy użyciu adresu URL HTTPs.|
| [Utwórz EventGrid](./scripts/cli-create-event-grid.md)|Tworzy konto poziomu subskrypcji usługi Event Grid dla zmiany stanu zadania.|

## <a name="see-also"></a>Zobacz także

[Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/en-us/cli/azure/ams?view=azure-cli-latest)
