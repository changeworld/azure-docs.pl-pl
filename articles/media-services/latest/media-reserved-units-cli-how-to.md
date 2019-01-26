---
title: Użyj interfejsu wiersza polecenia, aby skalować jednostki zarezerwowane multimediów — Azure | Dokumentacja firmy Microsoft
description: W tym temacie pokazano, jak skalowanie przetwarzania multimediów z użyciem usługi Azure Media Services za pomocą interfejsu wiersza polecenia.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 9f95be8a9d75065deedd3bd7c92907145e966494
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2019
ms.locfileid: "54913058"
---
# <a name="scaling-media-processing"></a>Skalowanie przetwarzania multimediów

Usługa Azure Media Services umożliwia skalowanie przetwarzania multimediów w ramach Twojego konta, umożliwiając zarządzanie jednostek zarezerwowanych multimediów (lokalizacje MRU). Aby uzyskać szczegółowym omówieniem, zobacz [skalowanie przetwarzania multimediów](../previous/media-services-scale-media-processing-overview.md). 

W tym artykule pokazano, jak [interfejsu wiersza polecenia usługi Media Services v3](https://aka.ms/ams-v3-cli-ref) skalowania lokalizacje MRU.

> [!NOTE]
> Analiza Audio i wideo zadania analizy, które są wyzwalane przez usługi Media Services v3 lub Video Indexer, aby uzyskać zaleca aprowizację swojego konta za pomocą 10 S3 lokalizacje MRU. <br/>Jeśli potrzebujesz więcej niż 10 S3 lokalizacje MRU, otwórz bilet pomocy technicznej przy użyciu [witryny Azure portal](https://portal.azure.com/).

## <a name="prerequisites"></a>Wymagania wstępne 

+ Zainstaluj [interfejs wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, z jakiej wersji korzystasz. 

    Możesz również użyć usługi [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest).
+ [Utwórz konto usługi Media Services](create-account-cli-how-to.md).

## <a name="scale-media-reserved-units-with-cli"></a>Zarezerwowane multimediów skalowania jednostek przy użyciu interfejsu wiersza polecenia

1. Uruchom polecenie `login`. Czy używasz usługi Azure cloud shell lub lokalnej powłoki interfejsu wiersza polecenia, uruchom to polecenie.

    ```azurecli
    az login
    ```
    
    Jeśli interfejs wiersza polecenia może otworzyć Twoją domyślną przeglądarkę, zrobi to i załaduje stronę logowania. W przeciwnym razie trzeba otworzyć stronę przeglądarki i postępuj zgodnie z instrukcjami w wierszu polecenia, aby wprowadzić kod autoryzacji po przejściu do [ https://aka.ms/devicelogin ](https://aka.ms/devicelogin) w przeglądarce.
2. Uruchom polecenie `mru`.

    Następujące [az ams account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) zestawy jednostek zarezerwowanych multimediów na "amsaccount" konta przy użyciu polecenia **liczba** i **typu** parametrów.

    ```azurecli
    az account set mru -n amsaccount -g amsResourceGroup --count 10 --type S3
    ```

## <a name="billing"></a>Rozliczenia

Opłaty są naliczane na podstawie liczby, typ i ilość czasu, które są obsługiwane lokalizacje MRU na Twoim koncie. Opłaty są naliczane, czy można uruchomić jakiekolwiek zadanie. Aby uzyskać szczegółowy opis, zobacz sekcję Często zadawane pytania dotyczące [cennika usługi Media Services](https://azure.microsoft.com/pricing/details/media-services/) strony.   

## <a name="next-step"></a>Następny krok

[Analizowanie plików wideo](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>Zobacz także

[Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
