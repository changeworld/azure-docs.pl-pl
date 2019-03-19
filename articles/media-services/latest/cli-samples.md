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
ms.date: 03/11/2019
ms.author: juliako
ms.openlocfilehash: dee7f831562dc1f4b2478d13b204aab1d8455e1e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "57840634"
---
# <a name="azure-cli-examples-for-azure-media-services"></a>Przykłady interfejsu wiersza polecenia platformy Azure dla usługi Azure Media Services

Poniższa tabela zawiera linki do przykładów interfejsu wiersza polecenia platformy Azure dla usługi Azure Media Services.

## <a name="examples"></a>Przykłady

|  |  |
|---|---|
|**Skalowanie**||
| [Jednostki zarezerwowane multimediów skalowania](media-reserved-units-cli-how-to.md)|Analiza Audio i wideo zadania analizy, które są wyzwalane przez usługi Media Services v3 lub Video Indexer, aby uzyskać zaleca aprowizację swojego konta za pomocą 10 S3 lokalizacje MRU. <br/>Skrypt pokazuje, jak skalować jednostki zarezerwowane multimediów (lokalizacje MRU) za pomocą interfejsu wiersza polecenia.|
|**Konto**||
| [Konto usługi Media Services](create-account-cli-how-to.md) | Skrypt tworzy konto usługi Azure Media Services. |
| [Resetowanie poświadczeń konta](./scripts/cli-reset-account-credentials.md)|Resetuje Twoje poświadczenia konta, a następnie pobiera ustawienia app.config ponownie.|
|**Zasoby**||
| [Tworzenie zasobów](./scripts/cli-create-asset.md)|Tworzy zasób usługi multimediów, można przekazać zawartości do.|
| [Przekaż plik](./scripts/cli-upload-file-asset.md)|Przekazuje plik lokalny do kontenera magazynu.|
| **Przekształca** i **zadania**||
| [Utwórz przekształceń](./scripts/cli-create-transform.md)|Pokazuje, jak utworzyć przekształcenia. Przekształcenia opisują prosty przepływ pracy zadań podczas przetwarzania plików wideo lub audio (często określany jako „przepis”).<br/> Należy zawsze sprawdzić, jeśli przekształcenie z żądaną nazwą i "przepisu" już istnieje. Jeśli tak jest, należy użyć ponownie. |
| [Kodowanie za pomocą niestandardowej transformacji](custom-preset-cli-howto.md) | Pokazuje, jak utworzyć niestandardowe ustawienie wstępne pod kątem określonych wymagań scenariusza lub urządzenia.|
| [Tworzenie zadań](./scripts/cli-create-jobs.md)|Przesyła zadanie proste przekształcanie kodowania przy użyciu adresu URL HTTPs.|
| [Utwórz EventGrid](./scripts/cli-create-event-grid.md)|Tworzy konto poziomu subskrypcji usługi Event Grid dla zmiany stanu zadania.|
| **Dostarczanie**||
| [Publikowanie elementu zawartości](./scripts/cli-publish-asset.md)| Tworzy Lokalizator przesyłania strumieniowego i otrzymuje w URL przesyłania strumieniowego. |
| [Filtr](filters-dynamic-manifest-cli-howto.md)| Konfiguruje filtru dla elementu zawartości wideo na żądanie i pokazuje, jak używać interfejsu wiersza polecenia do tworzenia [filtrów kont](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) i [filtry zasobów](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

## <a name="see-also"></a>Zobacz także

- [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
- [Szybki start: Pliki wideo Stream — interfejs wiersza polecenia](stream-files-cli-quickstart.md)
