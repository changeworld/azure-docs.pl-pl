---
title: Dostęp do interfejsu API usługi Azure Media Services — interfejs wiersza polecenia Azure | Dokumentacja firmy Microsoft
description: Wykonaj kroki Instruktaż, aby uzyskać dostęp do interfejsu API usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: mvc
ms.date: 05/01/2019
ms.author: juliako
ms.openlocfilehash: a27f7597ddc934b925d63507a816ac5816b682d6
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65151044"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>Interfejs API usług Azure Media dostępu przy użyciu wiersza polecenia platformy Azure
 
Aby połączyć się z interfejsem API usługi Azure Media Services, należy użyć uwierzytelniania jednostki usługi Azure AD, aplikacja musi żądać tokenu usługi Azure AD, która ma następujące parametry:

* Punkt końcowy dzierżawy usługi Azure AD
* Identyfikator URI zasobu usługi Media Services
* Identyfikator URI dla usługi Media Services REST zasobu
* Wartości aplikacji w usłudze Azure AD: identyfikator klienta oraz klucz tajny klienta

Aby uzyskać szczegółowe informacje na ten temat, zobacz [uzyskiwania dostępu do usługi Media Services v3 API](media-services-apis-overview.md#accessing-the-azure-media-services-api).

W tym artykule pokazano, jak utworzyć jednostkę usługi aplikacji Azure AD i usługi i pobierają wartości, które są wymagane do dostępu do zasobów usługi Azure Media Services za pomocą wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne 

[Utwórz konto usługi Media Services](create-account-cli-how-to.md).

Koniecznie zapamiętaj wartości, które zostały użyte jako nazwa grupy zasobów i nazwa konta usługi Media Services.
 
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="see-also"></a>Zobacz także

- [Skalowanie jednostek zarezerwowanych multimediów — interfejs wiersza polecenia](media-reserved-units-cli-how-to.md)
- [Tworzenie konta usługi Media Services — interfejs wiersza polecenia](./scripts/cli-create-account.md) 
- [Resetowanie poświadczeń konta — interfejs wiersza polecenia](./scripts/cli-reset-account-credentials.md)
- [Tworzenie zasobów — interfejs wiersza polecenia](./scripts/cli-create-asset.md)
- [Przekazywanie pliku — interfejs wiersza polecenia](./scripts/cli-upload-file-asset.md)
- [Tworzenie transformacji — interfejs wiersza polecenia](./scripts/cli-create-transform.md)
- [Tworzenie zadań — interfejs wiersza polecenia](./scripts/cli-create-jobs.md)
- [Tworzenie usługi EventGrid — interfejs wiersza polecenia](./scripts/cli-create-event-grid.md)
- [Publikowanie elementu zawartości — interfejs wiersza polecenia](./scripts/cli-publish-asset.md)
- [Filtr — interfejs wiersza polecenia](filters-dynamic-manifest-cli-howto.md)
- [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>Kolejne kroki

Punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi być w stanie uruchomiona. Następujące polecenie interfejsu wiersza polecenia uruchamia domyślny punkt końcowy przesyłania strumieniowego:


`az ams streaming-endpoint start -n default -a <amsaccount> -g <amsResourceGroup>`
