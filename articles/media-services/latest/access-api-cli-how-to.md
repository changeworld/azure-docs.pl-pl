---
title: Dostęp do Azure Media Services API — interfejs wiersza polecenia platformy Azure | Microsoft Docs
description: Wykonaj czynności opisane w tym artykule, aby uzyskać dostęp do interfejsu API Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: mvc
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: d66b3e1b6ed2c8eef9f5cd21c0657648ad550ebe
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896152"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>Interfejs API Azure Media Services dostępu za pomocą interfejsu wiersza polecenia platformy Azure
 
Aby używać uwierzytelniania podstawowego usługi Azure AD do nawiązywania połączenia z interfejsem API Azure Media Services, aplikacja musi zażądać tokenu usługi Azure AD, który ma następujące parametry:

* Punkt końcowy dzierżawy usługi Azure AD
* Identyfikator URI zasobu Media Services
* Identyfikator URI zasobu dla Media Services REST
* Wartości aplikacji usługi Azure AD: identyfikator klienta i klucz tajny klienta

Aby uzyskać szczegółowe wyjaśnienie, zobacz [dostęp do interfejsów api Media Services v3](media-services-apis-overview.md#accessing-the-azure-media-services-api).

W tym artykule pokazano, jak za pomocą interfejsu wiersza polecenia platformy Azure utworzyć aplikację usługi Azure AD i nazwę główną usług oraz uzyskać wartości, które są potrzebne w celu uzyskania dostępu do zasobów Azure Media Services.

## <a name="prerequisites"></a>Wymagania wstępne 

[Utwórz konto usługi Media Services](create-account-cli-how-to.md).

Koniecznie zapamiętaj wartości, które zostały użyte jako nazwa grupy zasobów i nazwa konta usługi Media Services.
 
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="see-also"></a>Zobacz także

- [Skalowanie jednostek zarezerwowanych multimediów — interfejs wiersza polecenia](media-reserved-units-cli-how-to.md)
- [Tworzenie konta usługi Media Services — interfejs wiersza polecenia](create-account-cli-how-to.md) 
- [Resetowanie poświadczeń konta — interfejs wiersza polecenia](cli-reset-account-credentials.md)
- [Tworzenie zasobów — interfejs wiersza polecenia](cli-create-asset.md)
- [Przekazywanie pliku — interfejs wiersza polecenia](cli-upload-file-asset.md)
- [Tworzenie transformacji — interfejs wiersza polecenia](cli-create-transform.md)
- [Kodowanie za pomocą niestandardowego przekształcenia interfejsu wiersza polecenia](custom-preset-cli-howto.md)
- [Tworzenie zadań — interfejs wiersza polecenia](cli-create-jobs.md)
- [Tworzenie usługi EventGrid — interfejs wiersza polecenia](job-state-events-cli-how-to.md)
- [Publikowanie elementu zawartości — interfejs wiersza polecenia](cli-publish-asset.md)
- [Filtr — interfejs wiersza polecenia](filters-dynamic-manifest-cli-howto.md)
- [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>Następne kroki

Punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi być w stanie uruchomienia. Następujące polecenie interfejsu wiersza polecenia uruchamia domyślny punkt końcowy przesyłania strumieniowego:

`az ams streaming-endpoint start -n default -a <amsaccount> -g <amsResourceGroup>`

