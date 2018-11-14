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
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: 54a92e56df21b59430ed12f191a9cf7a918e14c9
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2018
ms.locfileid: "51612903"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>Interfejs API usług Azure Media dostępu przy użyciu wiersza polecenia platformy Azure
 
Uwierzytelnianie jednostki usługi Azure AD należy używać, aby nawiązać połączenie z interfejsem API usługi Azure Media Services. Twoja aplikacja potrzebuje do wysłania żądania tokenu usługi Azure AD, która ma następujące parametry:

* Punkt końcowy dzierżawy usługi Azure AD
* Identyfikator URI zasobu usługi Media Services
* Identyfikator URI dla usługi Media Services REST zasobu
* Wartości aplikacji w usłudze Azure AD: identyfikator klienta oraz klucz tajny klienta

W tym artykule pokazano, jak utworzyć jednostkę usługi aplikacji Azure AD i usługi i pobierają wartości, które są wymagane do dostępu do zasobów usługi Azure Media Services za pomocą wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne 

- Zainstaluj i użyć interfejsu wiersza polecenia lokalnie, ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, z jakiej wersji korzystasz. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

    Obecnie nie wszystkie [interfejsu wiersza polecenia usługi Media Services v3](https://aka.ms/ams-v3-cli-ref) polecenia działają w usłudze Azure Cloud Shell. Zalecane jest użycie interfejsu wiersza polecenia lokalnie.

- [Utwórz konto usługi Media Services](create-account-cli-how-to.md).

    Upewnij się, że należy pamiętać, wartości, które były używane nazwy grupy zasobów i nazwę konta usługi Media Services.

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="next-steps"></a>Kolejne kroki

[Strumieniowe przesyłanie pliku](stream-files-dotnet-quickstart.md)

## <a name="see-also"></a>Zobacz także

[Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
