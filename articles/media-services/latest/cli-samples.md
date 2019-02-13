---
title: Przykłady interfejsu wiersza polecenia platformy Azure — usłudze Azure Media Services | Dokumentacja firmy Microsoft
description: Przykłady interfejsu wiersza polecenia platformy Azure dla usługi Azure Media Services
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 02/11/2019
ms.author: juliako
ms.openlocfilehash: 919dbd057f2fa1d762775ed799505796a420864a
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2019
ms.locfileid: "56109252"
---
# <a name="azure-cli-examples-for-azure-media-services"></a>Przykłady interfejsu wiersza polecenia platformy Azure dla usługi Azure Media Services

Poniższa tabela zawiera linki do przykładów interfejsu wiersza polecenia platformy Azure dla usługi Azure Media Services.

## <a name="examples"></a>Przykłady

|  |  |
|---|---|
|**Skalowanie**||
| [Jednostki zarezerwowane multimediów skalowania](media-reserved-units-cli-how-to.md)|Analiza Audio i wideo zadania analizy, które są wyzwalane przez usługi Media Services v3 lub Video Indexer, aby uzyskać zaleca aprowizację swojego konta za pomocą 10 S3 lokalizacje MRU. <br/>Skrypt pokazuje, jak skalować jednostki zarezerwowane multimediów (lokalizacje MRU) za pomocą interfejsu wiersza polecenia.|
|**Konto**||
| [Utwórz konto usługi Media Services](./scripts/cli-create-account.md) | Tworzy konto usługi Azure Media Services. Ponadto tworzy usługę podmiotu zabezpieczeń, która umożliwia dostęp do interfejsów API można programowo zarządzać kontem. |
| [Resetowanie poświadczeń konta](./scripts/cli-reset-account-credentials.md)|Resetuje Twoje poświadczenia konta, a następnie pobiera ustawienia app.config ponownie.|
|**Zasoby**||
| [Tworzenie zasobów](./scripts/cli-create-asset.md)|Tworzy zasób usługi multimediów, można przekazać zawartości do.|
| [Przekaż plik](./scripts/cli-upload-file-asset.md)|Przekazuje plik lokalny do kontenera magazynu.|
| **Przekształca** i **zadania**||
| [Utwórz przekształceń](./scripts/cli-create-transform.md)|Pokazuje, jak utworzyć przekształcenia. Przekształcenia opisują prosty przepływ pracy zadań podczas przetwarzania plików wideo lub audio (często określany jako „przepis”).<br/> Zawsze należy sprawdzić, czy przekształcenie z wybraną nazwą i „przepisem” już istnieje. Jeśli tak jest, należy użyć ponownie. |
| [Tworzenie zadań](./scripts/cli-create-jobs.md)|Przesyła zadanie proste przekształcanie kodowania przy użyciu adresu URL HTTPs.|
| [Utwórz EventGrid](./scripts/cli-create-event-grid.md)|Tworzy konto poziomu subskrypcji usługi Event Grid dla zmiany stanu zadania.|
| **Dostarczanie**||
| [Publikowanie elementu zawartości](./scripts/cli-publish-asset.md)| Tworzy Lokalizator przesyłania strumieniowego i otrzymuje w URL przesyłania strumieniowego. |
| [Filtr](filters-dynamic-manifest-cli-howto.md)| Konfiguruje filtru dla elementu zawartości wideo na żądanie i pokazuje, jak używać interfejsu wiersza polecenia do tworzenia [filtrów kont](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) i [filtry zasobów](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

## <a name="see-also"></a>Zobacz także

[Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
