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
ms.date: 01/28/2019
ms.author: juliako
ms.openlocfilehash: 257fe51cae245708816cd9a7bb0c33b6edf5aa05
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756003"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>Interfejs API usług Azure Media dostępu przy użyciu wiersza polecenia platformy Azure
 
Uwierzytelnianie jednostki usługi Azure AD należy używać, aby nawiązać połączenie z interfejsem API usługi Azure Media Services. Twoja aplikacja potrzebuje do wysłania żądania tokenu usługi Azure AD, która ma następujące parametry:

* Punkt końcowy dzierżawy usługi Azure AD
* Identyfikator URI zasobu usługi Media Services
* Identyfikator URI dla usługi Media Services REST zasobu
* Wartości aplikacji w usłudze Azure AD: identyfikator klienta oraz klucz tajny klienta

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

## <a name="next-steps"></a>Kolejne kroki

[Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
