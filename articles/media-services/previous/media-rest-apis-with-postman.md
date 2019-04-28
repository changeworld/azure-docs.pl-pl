---
title: Konfigurowanie narzędzia Postman dla wywołań interfejsu API REST usługi Azure Media Services
description: Dowiedz się, jak skonfigurować narzędzia Postman dla wywołań interfejsu API REST usługi Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 5d0b5a57f3fe587a06a102c958b17dbf2a73225c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61466713"
---
# <a name="configure-postman-for-media-services-rest-api-calls"></a>Konfigurowanie narzędzia Postman dla wywołań interfejsu API REST usługi Media Services  

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówek dotyczących migracji od v2 do v3](../latest/migrate-from-v2-to-v3.md)

W tym samouczku dowiesz się, jak skonfigurować **Postman** , dzięki czemu może służyć do wywołania interfejsów API REST usługi Azure Media Services (AMS). Samouczek pokazuje, jak do importowania plików środowiska i kolekcji do **Postman**. Kolekcja zawiera definicje pogrupowanych żądań HTTP przetwarzanych wywoływać interfejsy API REST usługi Azure Media Services (AMS). Plik środowisko zawiera zmienne, które są używane przez kolekcję.

To środowisko i kolekcji są używane w artykułach, które pokazują, jak osiągnąć różne zadania za pomocą interfejsów API REST usługi Azure Media.

## <a name="prerequisites"></a>Wymagania wstępne

- Zainstaluj klienta REST programu [Postman](https://www.getpostman.com/), aby uruchomić interfejsy API REST przedstawione w niektórych samouczkach dotyczących AMS REST. 

    W przykładzie użyto programu **Postman**, ale odpowiednie będzie każde narzędzie REST. Inne możliwości: Program **Visual Studio Code** z wtyczką REST lub program **Telerik Fiddler**. 

## <a name="configure-the-environment"></a>Konfigurowanie środowiska 

1. Utwórz plik JSON, który zawiera zmienne środowiskowe, używaną w samouczkach usługi AMS. Nadaj plikowi nazwę (na przykład **AzureMediaServices.postman_environment.json**). Otwórz plik i Wklej kod, który definiuje środowiska Postman [tym fragmencie kodu](postman-environment.md). 
2. Otwórz program **Postman**.
3. Po prawej stronie ekranu wybierz opcję **Zarządzaj środowiskiem**.

    ![Przekazywanie pliku](./media/media-services-rest-upload-files/postman-create-env.png)
4. W oknie dialogowym **Zarządzaj środowiskiem** kliknij opcję **Importuj**.
5. Wyszukaj i wybierz **AzureMediaServices.postman_environment.json** pliku.
6. **AzureMedia** środowiska zostanie dodany.
7. Zamknij okno dialogowe.
8. Wybierz **AzureMedia** środowiska.

    ![Przekazywanie pliku](./media/media-services-rest-upload-files/postman-choose-env.png)

## <a name="configure-the-collection"></a>Konfigurowanie kolekcji

1. Utwórz plik JSON, który zawiera **Postman** kolekcji z operacjami, które są niezbędne do przekazania pliku do usługi Media Services. Nadaj plikowi nazwę (na przykład **AzureMediaServicesOperations.postman_collection.json**). Otwórz plik i Wklej kod, który definiuje **Postman** kolekcji z [tym fragmencie kodu](postman-collection.md).
2. Kliknij przycisk **Importuj**, aby zaimportować plik kolekcji.
3. Wybierz **AzureMediaServicesOperations.postman_collection.json** pliku.

    ![Przekazywanie pliku](./media/media-services-rest-upload-files/postman-import-collection.png)

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z [przekazuj zasoby](media-services-rest-upload-files.md) artykułu.  
