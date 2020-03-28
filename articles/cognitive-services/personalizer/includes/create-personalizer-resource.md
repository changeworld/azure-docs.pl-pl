---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 01/15/2020
ms.author: diberry
ms.openlocfilehash: 11abd52681d7c9962af4e5bf0728f97b256223c1
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122901"
---
## <a name="create-a-personalizer-azure-resource"></a>Tworzenie zasobu platformy Azure personalizatora

Utwórz zasób dla personalizatora przy użyciu [witryny Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na komputerze lokalnym. Możesz również wykonać następujące czynności:

* Pobierz [klucz wersji próbnej](https://azure.microsoft.com/try/cognitive-services) ważny przez 7 dni za darmo. Po zarejestrowaniu się będzie on dostępny w [witrynie sieci Web platformy Azure.](https://azure.microsoft.com/try/cognitive-services/my-apis/)
* Wyświetl swój zasób w [witrynie Azure portal](https://portal.azure.com/).

Po dostaniu klucza z subskrypcji próbnej lub zasobu utwórz dwie [zmienne środowiskowe:](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)

* `PERSONALIZER_RESOURCE_KEY`dla klucza zasobu.
* `PERSONALIZER_RESOURCE_ENDPOINT`dla punktu końcowego zasobu.

W witrynie Azure portal wartości klucza i punktu końcowego są dostępne na stronie **szybkiego startu.**
