---
title: Przenoszenie interfejsów API sieci Web wywołujących interfejsy API do produkcji — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak przenieść internetowy interfejs API, który wywołuje internetowe interfejsy API do produkcji.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: d557c70c09824f3aead41a654dc92c4ec46e9238
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885059"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Internetowy interfejs API, który wywołuje internetowe interfejsy API: przejście do produkcji

Po nabyciu tokenu do wywoływania internetowych interfejsów API można przenieść aplikację do produkcji.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Dowiedz się więcej

Teraz, gdy znasz podstawy sposobu wywoływania interfejsów API sieci web z własnego interfejsu API sieci web, może być zainteresowany następującym samouczkiem, który opisuje kod, który jest używany do tworzenia chronionego interfejsu API sieci web, który wywołuje interfejsy API sieci web.

| Sample | Platforma | Opis |
|--------|----------|-------------|
| [active-directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | ASP.NET Core 2.2 Web API, Desktop (WPF) | ASP.NET interfejs API sieci Web Core 2.2 wywołuje program Microsoft Graph, który wywołujesz z aplikacji WPF przy użyciu platformy tożsamości firmy Microsoft (wersja 2.0). |
