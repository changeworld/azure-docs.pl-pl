---
title: Konfigurowanie listonosza dla wywołań interfejsu API rest usługi Azure Media Services w wersji 3
description: W tym artykule pokazano, jak skonfigurować postmana, dzięki czemu można go używać do wywoływania interfejsów API REST usługi Azure Media Services (AMS).
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
ms.date: 12/05/2019
ms.author: juliako
ms.openlocfilehash: 872dad95fc5b536c51e251612f40439da020a059
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75779641"
---
# <a name="configure-postman-for-media-services-v3-rest-api-calls"></a>Konfigurowanie postman dla usług Media Services w wersji 3 REST wywołania interfejsu API

W tym artykule pokazano, jak skonfigurować **postmana,** dzięki czemu można go używać do wywoływania interfejsów API REST usługi Azure Media Services (AMS). W tym artykule pokazano, jak zaimportować pliki środowiska i kolekcji do **programu Postman**. Kolekcja zawiera zgrupowane definicje żądań HTTP, które wywołują interfejsy API REST usługi Azure Media Services (AMS). Plik środowiska zawiera zmienne, które są używane przez kolekcję.

Przed rozpoczęciem tworzenia należy [przejrzeć programowanie z interfejsami API usługi Media Services w wersji 3](media-services-apis-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

- [Utwórz konto usługi Media Services](create-account-cli-how-to.md). Pamiętaj, aby zapamiętać nazwę grupy zasobów i nazwę konta usługi Media Services. 
- Uzyskiwanie informacji potrzebnych do [uzyskiwania dostępu do interfejsów API](access-api-cli-how-to.md)
- Zainstaluj klienta REST programu [Postman](https://www.getpostman.com/), aby uruchomić interfejsy API REST przedstawione w niektórych samouczkach dotyczących AMS REST. 

    W przykładzie użyto programu **Postman**, ale odpowiednie będzie każde narzędzie REST. Inne alternatywy to: **Visual Studio Code** z wtyczką REST lub **Telerik Fiddler**. 

> [!IMPORTANT]
> Przejrzyj [konwencje nazewnictwa](media-services-apis-overview.md#naming-conventions).

## <a name="download-postman-files"></a>Pobieranie plików Postman

Sklonuj repozytorium GitHub zawierające kolekcję programu Postman oraz pliki środowiska.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

## <a name="configure-postman"></a>Konfigurowanie programu Postman

### <a name="configure-the-environment"></a>Konfigurowanie środowiska 

1. Otwórz aplikację **Listonosz.**
2. Po prawej stronie ekranu wybierz opcję **Zarządzaj środowiskiem**.

    ![Zarządzanie środowiskiem](./media/develop-with-postman/postman-import-env.png)
4. W oknie dialogowym **Zarządzaj środowiskiem** kliknij opcję **Importuj**.
2. Przejdź do pliku `Azure Media Service v3 Environment.postman_environment.json`, który został pobrany podczas klonowania `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`.
6. Zostanie dodane środowisko **Azure Media Service v3 Environment**.

    > [!Note]
    > Zaktualizuj zmienne dostępu przy użyciu wartości z sekcji **Uzyskiwanie dostępu do interfejsu API usługi Media Services** powyżej.

7. Kliknij dwukrotnie wybrany plik, a następnie wprowadź wartości uzyskane podczas wykonywania kroków uzyskiwania dostępu do interfejsu API.
8. Zamknij okno dialogowe.
9. Z listy rozwijanej wybierz środowisko **Azure Media Service v3 Environment**.

    ![Wybór środowiska](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>Konfigurowanie kolekcji

1. Kliknij przycisk **Importuj**, aby zaimportować plik kolekcji.
1. Przejdź do pliku `Media Services v3.postman_collection.json`, który został pobrany podczas klonowania `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`
3. Wybierz plik **Media Services v3.postman_collection.json**.

    ![Importowanie pliku](./media/develop-with-postman/postman-import-collection.png)

## <a name="get-azure-ad-token"></a>Pobieranie tokenu usługi Azure AD 

Przed rozpoczęciem manipulowania zasobami usługi AMS w wersji 3 należy uzyskać i ustawić token usługi Azure AD dla uwierzytelniania głównego usługi.

1. W lewym oknie aplikacji Postman wybierz "Krok 1: Pobierz token AAD Auth".
2. Następnie wybierz pozycję „Pobieranie tokenu usługi Azure AD do uwierzytelniania jednostki usługi”.
3. Kliknij pozycję **Wyślij**.

    Zostanie wysłana następująca operacja **POST**.

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. Odpowiedź wróci z tokenem i ustawi zmienną środowiskową „AccessToken” na wartość tokenu.  

    ![Pobieranie tokenu usługi AAD](./media/develop-with-postman/postman-get-aad-auth-token.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów 

* Jeśli aplikacja nie powiedzie się z "HTTP 504: Limit czasu bramy", upewnij się, że zmienna lokalizacji nie została jawnie ustawiona na wartość inną niż oczekiwana lokalizacja konta usługi Media Services. 
* Jeśli pojawi się błąd "nie znaleziono konta", sprawdź również, czy właściwość lokalizacji w komunikacie JSON treści jest ustawiona na lokalizację, w której znajduje się konto usługi Media Services. 

## <a name="see-also"></a>Zobacz też

- [Przekazywanie plików na konto usługi Media Services — REST](upload-files-rest-how-to.md)
- [Tworzenie filtrów za pomocą usługi Media Services — REST](filters-dynamic-manifest-rest-howto.md)
- [Interfejs API REST bazujący na usłudze Azure Resource Manager](https://github.com/Azure-Samples/media-services-v3-arm-templates)

## <a name="next-steps"></a>Następne kroki

- [Przesyłaj strumieniowo pliki za pomocą programu REST](stream-files-tutorial-with-rest.md).  
- [Samouczek: Kodowanie zdalnego pliku na podstawie adresu URL i przesyłanie strumieniowe wideo - REST](stream-files-tutorial-with-rest.md)
