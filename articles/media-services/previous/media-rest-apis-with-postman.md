---
title: Konfigurowanie programu Poster do Azure Media Services wywołań interfejsu API REST
description: W tym artykule opisano sposób konfigurowania programu Poster dla Media Services wywołań interfejsu API REST.
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
ms.openlocfilehash: f55b3d5767f38c8fba2e79925f6f6e1e390aad2d
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74887797"
---
# <a name="configure-postman-for-media-services-rest-api-calls"></a>Konfigurowanie programu Poster do Media Services wywołań interfejsu API REST  

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-from-v2-to-v3.md)

W tym samouczku pokazano, jak skonfigurować program **pocztowy** , aby można było go użyć do wywołania interfejsów api REST Azure Media Services (AMS). W tym samouczku przedstawiono sposób importowania plików środowiska i kolekcji do programu **Poster**. Kolekcja zawiera zgrupowane definicje żądań HTTP, które wywołują Azure Media Services (AMS) interfejsy API REST. Plik środowiska zawiera zmienne, które są używane przez kolekcję.

To środowisko i kolekcja są używane w artykułach, które pokazują, jak osiągnąć różne zadania za pomocą interfejsów API REST Azure Media Services.

## <a name="prerequisites"></a>Wymagania wstępne

- Zainstaluj klienta REST programu [Postman](https://www.getpostman.com/), aby uruchomić interfejsy API REST przedstawione w niektórych samouczkach dotyczących AMS REST. 

    W przykładzie użyto programu **Postman**, ale odpowiednie będzie każde narzędzie REST. Można również użyć: programu **Visual Studio Code** z wtyczką REST lub programu **Telerik Fiddler**. 

## <a name="configure-the-environment"></a>Konfigurowanie środowiska 

1. Utwórz plik JSON zawierający zmienne środowiskowe używane w samouczkach AMS. Nazwij plik (na przykład **AzureMediaServices. postman_environment. JSON**). Otwórz plik i wklej kod, który definiuje środowisko Poster na podstawie [tej listy kodu](postman-environment.md). 
2. Otwórz program **Postman**.
3. Po prawej stronie ekranu wybierz opcję **Zarządzaj środowiskiem**.

    ![Przekazywanie pliku](./media/media-services-rest-upload-files/postman-create-env.png)
4. W oknie dialogowym **Zarządzaj środowiskiem** kliknij opcję **Importuj**.
5. Przeglądaj i wybierz plik **AzureMediaServices. postman_environment. JSON** .
6. Środowisko **AzureMedia** jest dodawane.
7. Zamknij okno dialogowe.
8. Wybierz środowisko **AzureMedia** .

    ![Przekazywanie pliku](./media/media-services-rest-upload-files/postman-choose-env.png)

## <a name="configure-the-collection"></a>Konfigurowanie kolekcji

1. Utwórz plik JSON zawierający kolekcję programu **Poster** ze wszystkimi operacjami, które są konieczne do przekazania pliku do Media Services. Nazwij plik (na przykład **AzureMediaServicesOperations. postman_collection. JSON**). Otwórz plik i wklej kod, który definiuje kolekcję **Poster** z [tej listy kodu](postman-collection.md).
2. Kliknij przycisk **Importuj**, aby zaimportować plik kolekcji.
3. Wybierz plik **AzureMediaServicesOperations. postman_collection. JSON** .

    ![Przekazywanie pliku](./media/media-services-rest-upload-files/postman-import-collection.png)

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z artykułem [przekazywanie elementów zawartości](media-services-rest-upload-files.md) .  
