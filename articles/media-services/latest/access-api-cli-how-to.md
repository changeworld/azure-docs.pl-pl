---
title: Dostęp do interfejsu API usługi Azure Media Services — interfejs wiersza polecenia Azure | Dokumentacja firmy Microsoft
description: Wykonaj kroki Instruktaż, aby uzyskać dostęp do interfejsu API usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cflower
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: mvc
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: e20cac5f1063589bdbfee0f384ac6af5a39811ed
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47096795"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>Interfejs API usług Azure Media dostępu przy użyciu wiersza polecenia platformy Azure
 
Uwierzytelnianie jednostki usługi Azure AD należy używać, aby nawiązać połączenie z interfejsem API usługi Azure Media Services. Twoja aplikacja potrzebuje do wysłania żądania tokenu usługi Azure AD, która ma następujące parametry:

* Punkt końcowy dzierżawy usługi Azure AD
* Identyfikator URI zasobu usługi Media Services
* Identyfikator URI dla usługi Media Services REST zasobu
* Wartości aplikacji w usłudze Azure AD: identyfikator klienta oraz klucz tajny klienta

W tym artykule pokazano, jak utworzyć jednostkę usługi aplikacji Azure AD i usługi i pobierają wartości, które są wymagane do dostępu do zasobów usługi Azure Media Services za pomocą wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne 

Utworzenie nowego konta usługi Azure Media Services zgodnie z opisem w [tym przewodniku Szybki start](create-account-cli-quickstart.md).

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się w witrynie [Azure Portal](http://portal.azure.com), a następnie uruchom usługę **CloudShell**, aby wykonywać polecenia interfejsu wiersza polecenia, jak pokazano w następnych krokach.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten temat wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, z jakiej wersji korzystasz. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Strumieniowe przesyłanie pliku](stream-files-dotnet-quickstart.md)

## <a name="see-also"></a>Zobacz także

[Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/en-us/cli/azure/ams?view=azure-cli-latest)
