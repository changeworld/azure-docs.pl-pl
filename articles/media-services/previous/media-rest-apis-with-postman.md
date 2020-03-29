---
title: Konfigurowanie listonosza dla wywołań interfejsu API REST usługi Azure Media Services
description: W tym artykule opisano sposób konfigurowania wywołań interfejsu API usługi Postman dla usług MEDIA SERVICES.
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
ms.openlocfilehash: 11c9c26e7c0f36e1e3dba732e90a6aef95e6ee14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76694994"
---
# <a name="configure-postman-for-media-services-v2-rest-api-calls"></a>Konfigurowanie postman dla usług Media Services w wersji 2 REST wywołania interfejsu API  

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Sprawdź najnowszą wersję usługi [Media Services w wersji 3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówki dotyczące migracji z wersji 2 do v3](../latest/migrate-from-v2-to-v3.md)

W tym samouczku pokazano, jak skonfigurować **postmana,** dzięki czemu można go używać do wywoływania interfejsów API REST usługi Azure Media Services (AMS). W samouczku pokazano, jak zaimportować pliki środowiska i kolekcji do **programu Postman**. Kolekcja zawiera zgrupowane definicje żądań HTTP, które wywołują interfejsy API REST usługi Azure Media Services (AMS). Plik środowiska zawiera zmienne, które są używane przez kolekcję.

To środowisko i kolekcja jest używana w artykułach, które pokazują, jak osiągnąć różne zadania za pomocą interfejsów API REST usługi Azure Media Services.

## <a name="prerequisites"></a>Wymagania wstępne

- Zainstaluj klienta REST programu [Postman](https://www.getpostman.com/), aby uruchomić interfejsy API REST przedstawione w niektórych samouczkach dotyczących AMS REST. 

    W przykładzie użyto programu **Postman**, ale odpowiednie będzie każde narzędzie REST. Inne alternatywy to: **Visual Studio Code** z wtyczką REST lub **Telerik Fiddler**. 

## <a name="configure-the-environment"></a>Konfigurowanie środowiska 

1. Utwórz plik .json zawierający zmienne środowiskowe używane w samouczkach AMS. Nazwij plik (na przykład **AzureMediaServices.postman_environment.json).** Otwórz plik i wklej kod definiujący środowisko Listonosz z [tej listy kodu](postman-environment.md). 
2. Otwórz program **Postman**.
3. Po prawej stronie ekranu wybierz opcję **Zarządzaj środowiskiem**.

    ![Przekazywanie pliku](./media/media-services-rest-upload-files/postman-create-env.png)
4. W oknie dialogowym **Zarządzaj środowiskiem** kliknij opcję **Importuj**.
5. Przejrzyj i wybierz plik **AzureMediaServices.postman_environment.json.**
6. Środowisko **AzureMedia** jest dodawany.
7. Zamknij okno dialogowe.
8. Wybierz środowisko **AzureMedia.**

    ![Przekazywanie pliku](./media/media-services-rest-upload-files/postman-choose-env.png)

## <a name="configure-the-collection"></a>Konfigurowanie kolekcji

1. Utwórz plik .json zawierający kolekcję **Postman** ze wszystkimi operacjami potrzebnymi do przekazania pliku do usługi Media Services. Nazwij plik (na przykład **AzureMediaServicesOperations.postman_collection.json).** Otwórz plik i wklej kod definiujący kolekcję **Listonosz** z [tej listy kodu](postman-collection.md).
2. Kliknij przycisk **Importuj**, aby zaimportować plik kolekcji.
3. Wybierz plik **AzureMediaServicesOperations.postman_collection.json.**

    ![Przekazywanie pliku](./media/media-services-rest-upload-files/postman-import-collection.png)

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z artykułem [o przesłaniu zasobów.](media-services-rest-upload-files.md)  
