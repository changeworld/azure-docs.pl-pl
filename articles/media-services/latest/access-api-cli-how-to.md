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
ms.openlocfilehash: 5d7c338dc9d5028552d9b33802982ffda97a56f0
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49389854"
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

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do [witryny Azure portal](http://portal.azure.com) i uruchom **CloudShell** do wykonywania poleceń interfejsu wiersza polecenia, jak pokazano w następnych krokach.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, z jakiej wersji korzystasz. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Strumieniowe przesyłanie pliku](stream-files-dotnet-quickstart.md)

## <a name="see-also"></a>Zobacz także

[Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
