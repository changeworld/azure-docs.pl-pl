---
title: Przykłady Azure CLI — usługi Azure Media Services | Dokumentacja firmy Microsoft
description: Azure CLI przykłady dla usługi Azure Media Services
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
ms.openlocfilehash: bbf69bdcc92316642f6b37d267cdea2aad920316
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="azure-cli-examples-for-azure-media-services"></a>Azure CLI przykłady dla usługi Azure Media Services

Poniższa tabela zawiera łącza do przykładów z wiersza polecenia platformy Azure dla usługi Azure Media Services.

|  |  |
|---|---|
|**Konto**||
| [Utwórz konto usługi Media Services](./scripts/cli-create-account.md) | Tworzy konto usługi Azure Media Services. Ponadto tworzy usługę podmiotu zabezpieczeń, można uzyskać dostępu do interfejsów API do programowego zarządzania konta. |
| [Resetowanie poświadczeń konta](./scripts/cli-reset-account-credentials.md)|Resetuje poświadczeń konta i pobiera ustawienia app.config ponownie.|
|**Zasoby**||
| [Utwórz zasoby](./scripts/cli-create-asset.md)|Tworzy zasób Media Services można przekazać zawartości do.|
| [Przekaż plik](./scripts/cli-upload-file-asset.md)|Przekazuje plik lokalny do kontenera magazynu.|
| [Publikowanie zasobów](./scripts/cli-publish-asset.md)| Tworzy Lokalizator przesyłania strumieniowego i otrzymuje w odpowiedzi przesyłania strumieniowego adresów URL. |
| **Przekształca** i **zadań**||
| [Utwórz transformacji](./scripts/cli-create-transform.md)|Przedstawia sposób tworzenia transformacji. Transformacje opisano prosty przepływu pracy zadań przetwarzania plików wideo lub audio (często określany jako "przepisu").<br/> Należy zawsze sprawdzić, jeśli przekształcenie z żądaną nazwą i "przepisu" już istnieje. Jeśli tak, użyć go ponownie. |
| [Tworzenie zadań](./scripts/cli-create-jobs.md)|Przesyła zadanie do prostych transformacji kodowania przy użyciu adresu URL HTTPs.|
| [Utwórz EventGrid](./scripts/cli-create-event-grid.md)|Tworzy subskrypcję konta poziomu siatki zdarzeń zmian stanu zadania.|

