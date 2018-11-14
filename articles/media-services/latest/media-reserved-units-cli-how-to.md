---
title: Skalowanie Media zastrzeżone jednostki — Azure | Dokumentacja firmy Microsoft
description: W tym temacie przedstawiono omówienie skalowanie przetwarzania multimediów za pomocą usługi Azure Media Services.
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
ms.date: 11/11/2018
ms.author: juliako
ms.openlocfilehash: 7c99cdbda2eeb2fc7f8aa615bbdb8b77f84e57be
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2018
ms.locfileid: "51617867"
---
# <a name="scaling-media-processing"></a>Skalowanie przetwarzania multimediów

Usługa Azure Media Services umożliwia skalowanie przetwarzania multimediów w ramach Twojego konta, umożliwiając zarządzanie jednostek zarezerwowanych multimediów (lokalizacje MRU). Aby uzyskać szczegółowym omówieniem, zobacz [skalowanie przetwarzania multimediów](../previous/media-services-scale-media-processing-overview.md). W tym artykule pokazano, jak [interfejsu wiersza polecenia usługi Media Services v3](https://aka.ms/ams-v3-cli-ref) skalowania lokalizacje MRU.

> [!IMPORTANT]
> Zapoznaj się z uwagami opisanego w [w tej sekcji](#considerations).  
> 
>

## <a name="prerequisites"></a>Wymagania wstępne 

- Zainstaluj i użyć interfejsu wiersza polecenia lokalnie, ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, z jakiej wersji korzystasz. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

    Obecnie nie wszystkie [interfejsu wiersza polecenia usługi Media Services v3](https://aka.ms/ams-v3-cli-ref) polecenia działają w usłudze Azure Cloud Shell. Zalecane jest użycie interfejsu wiersza polecenia lokalnie.

- [Utwórz konto usługi Media Services](create-account-cli-how-to.md).

## <a name="scale-media-reserved-units-with-cli"></a>Zarezerwowane multimediów skalowania jednostek przy użyciu interfejsu wiersza polecenia

Następujące [az ams account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) zestawy jednostek zarezerwowanych multimediów na "amsaccount" konta przy użyciu polecenia **liczba** i **typu** parametrów.

```azurecli
az account set mru -n amsaccount -g amsResourceGroup --count 10 type S3
```

## <a name="considerations"></a>Zagadnienia do rozważenia

- Analiza Audio i wideo zadania analizy, które są wyzwalane przez usługi Media Services v3 lub Video Indexer, aby uzyskać zaleca aprowizację swojego konta za pomocą 10 S3 lokalizacje MRU.
- Jeśli potrzebujesz więcej niż 10 S3 lokalizacje MRU, otwórz bilet pomocy technicznej przy użyciu [witryny Azure portal](https://portal.azure.com/).

## <a name="billing"></a>Rozliczenia

Opłaty są naliczane na podstawie liczby, typ i ilość czasu, które są obsługiwane lokalizacje MRU na Twoim koncie. Opłaty są naliczane, czy można uruchomić jakiekolwiek zadanie. Aby uzyskać szczegółowy opis, zobacz sekcję Często zadawane pytania dotyczące [cennika usługi Media Services](https://azure.microsoft.com/pricing/details/media-services/) strony.   

## <a name="next-step"></a>Następny krok

[Analizowanie plików wideo](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>Zobacz także

[Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
