---
title: "Skonfiguruj Postman dla wywołań interfejsu API REST Azure Media Services"
description: "Dowiedz się, jak skonfigurować Postman dla wywołań interfejsu API REST usług Media."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2017
ms.author: juliako
ms.openlocfilehash: 72b110cac8d4945c958d760ff98e2da2f2796b62
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/22/2018
---
# <a name="configure-postman-for-media-services-rest-api-calls"></a>Skonfiguruj Postman dla wywołań interfejsu API REST usługi multimediów

W tym samouczku przedstawiono sposób konfigurowania **Postman** dzięki mogą być używane do wywoływania interfejsów API REST usługi Azure Media Services (AMS). Samouczek pokazuje, jak do importowania plików środowiska i kolekcji do **Postman**. Kolekcja zawiera definicje grupowanych żądań HTTP, które mogą wywoływać interfejsy API REST usługi Azure Media Services (AMS). Plik środowisko zawiera zmiennych, które są używane przez połączenie.

To środowisko i kolekcji jest używany w artykułach, które pokazują, jak uzyskać różne zadania związane z interfejsów API usługi Azure Media Services REST.

## <a name="prerequisites"></a>Wymagania wstępne

- Zainstaluj [Postman](https://www.getpostman.com/) klienta REST do wykonania wyświetlany w niektórych samouczki AMS REST interfejsów API REST. 

    Używamy **Postman** , ale odpowiednia może być dowolnego narzędzia REST. Inne alternatywne to: **Visual Studio Code** z wtyczki REST lub **Fiddler strony firmy Telerik**. 

## <a name="configure-the-environment"></a>Konfigurowanie środowiska 

1. Utwórz plik JSON zawierający zmiennych środowiskowych w samouczkach AMS. Nazwa pliku (na przykład **AzureMediaServices.postman_environment.json**). Otwórz plik i Wklej kod, który definiuje środowiska Postman [ten przykładowy kod](postman-environment.md). 
2. Otwórz **Postman**.
3. Po prawej stronie ekranu wybierz **środowiska Zarządzaj** opcji.

    ![Przekazywanie pliku](./media/media-services-rest-upload-files/postman-create-env.png)
4. Z **środowiska Zarządzaj** okna dialogowego, kliknij przycisk **importu**.
5. Wyszukaj i wybierz **AzureMediaServices.postman_environment.json** pliku.
6. **AzureMedia** środowiska zostanie dodany.
7. Zamknij okno dialogowe.
8. Wybierz **AzureMedia** środowiska.

    ![Przekazywanie pliku](./media/media-services-rest-upload-files/postman-choose-env.png)

## <a name="configure-the-collection"></a>Konfigurowanie kolekcji

1. Utwórz plik JSON zawierający **Postman** kolekcji z wszystkie operacje, które są wymagane do przekazania pliku do usługi Media Services. Nazwa pliku (na przykład **AzureMediaServicesOperations.postman_collection.json**). Otwórz plik i Wklej kod, który definiuje **Postman** kolekcji z [ten przykładowy kod](postman-collection.md).
2. Kliknij przycisk **zaimportować** można zaimportować pliku kolekcji.
3. Wybierz **AzureMediaServicesOperations.postman_collection.json** pliku.

    ![Przekazywanie pliku](./media/media-services-rest-upload-files/postman-import-collection.png)

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z [Przekaż zasoby](media-services-rest-upload-files.md) artykułu.  
