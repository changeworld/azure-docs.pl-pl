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
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 5dbcf446a609adcd0f1902fcca2ac19ad87f17b1
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65779666"
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
- [Tworzenie konta usługi Media Services — interfejs wiersza polecenia](create-account-cli-how-to.md) 
- [Resetowanie poświadczeń konta — interfejs wiersza polecenia](cli-reset-account-credentials.md)
- [Tworzenie zasobów — interfejs wiersza polecenia](cli-create-asset.md)
- [Przekazywanie pliku — interfejs wiersza polecenia](cli-upload-file-asset.md)
- [Tworzenie transformacji — interfejs wiersza polecenia](cli-create-transform.md)
- [Kodowanie za pomocą niestandardowych przekształcenie — interfejs wiersza polecenia](custom-preset-cli-howto.md)
- [Tworzenie zadań — interfejs wiersza polecenia](cli-create-jobs.md)
- [Tworzenie usługi EventGrid — interfejs wiersza polecenia](job-state-events-cli-how-to.md)
- [Publikowanie elementu zawartości — interfejs wiersza polecenia](cli-publish-asset.md)
- [Filtr — interfejs wiersza polecenia](filters-dynamic-manifest-cli-howto.md)
- [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>Kolejne kroki

Punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi być w stanie uruchomiona. Następujące polecenie interfejsu wiersza polecenia uruchamia domyślny punkt końcowy przesyłania strumieniowego:

`az ams streaming-endpoint start -n default -a <amsaccount> -g <amsResourceGroup>`

