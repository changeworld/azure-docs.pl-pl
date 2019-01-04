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
ms.date: 12/18/2018
ms.author: juliako
ms.openlocfilehash: 06ecdf31c2627eda5bf651667703ddcf39c9d307
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/19/2018
ms.locfileid: "53604103"
---
# <a name="configure-postman-for-media-services-rest-api-calls"></a>Konfigurowanie narzędzia Postman dla wywołań interfejsu API REST usługi Media Services

W tym artykule dowiesz się, jak skonfigurować **Postman** , dzięki czemu może służyć do wywołania interfejsów API REST usługi Azure Media Services (AMS). W tym artykule opisano sposób importowania plików środowiska i kolekcji do **Postman**. Kolekcja zawiera definicje pogrupowanych żądań HTTP przetwarzanych wywoływać interfejsy API REST usługi Azure Media Services (AMS). Plik środowisko zawiera zmienne, które są używane przez kolekcję.

## <a name="prerequisites"></a>Wymagania wstępne

- [Utwórz konto usługi Media Services](create-account-cli-how-to.md). Upewnij się, że do zapamiętania nazwę grupy zasobów i nazwę konta usługi Media Services. 
- Uzyskaj informacje potrzebne do [dostęp do interfejsów API](access-api-cli-how-to.md)
- Zainstaluj klienta REST programu [Postman](https://www.getpostman.com/), aby uruchomić interfejsy API REST przedstawione w niektórych samouczkach dotyczących AMS REST. 

    W przykładzie użyto programu **Postman**, ale odpowiednie będzie każde narzędzie REST. Inne alternatywy są: **Visual Studio Code** za pomocą wtyczki REST lub **narzędzia Telerik Fiddler**. 

## <a name="download-postman-files"></a>Pobieranie plików Postman

Sklonuj repozytorium GitHub zawierające kolekcję programu Postman oraz pliki środowiska.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

## <a name="configure-postman"></a>Konfigurowanie programu Postman

W tej sekcji skonfigurowano program Postman.

### <a name="configure-the-environment"></a>Konfigurowanie środowiska 

1. Otwórz program **Postman**.
2. Po prawej stronie ekranu wybierz opcję **Zarządzaj środowiskiem**.

    ![Zarządzanie środowiskiem](./media/develop-with-postman/postman-import-env.png)
4. W oknie dialogowym **Zarządzaj środowiskiem** kliknij opcję **Importuj**.
2. Przejdź do pliku `Azure Media Service v3 Environment.postman_environment.json`, który został pobrany podczas klonowania `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`.
6. Zostanie dodane środowisko **Azure Media Service v3 Environment**.

    > [!Note]
    > Zaktualizuj zmienne dostępu przy użyciu wartości z sekcji **Uzyskiwanie dostępu do interfejsu API usługi Media Services** powyżej.

7. Kliknij dwukrotnie wybrany plik, a następnie wprowadź wartości uzyskane podczas wykonywania kroków [uzyskiwania dostępu do interfejsu API](#access-the-media-services-api).
8. Zamknij okno dialogowe.
9. Z listy rozwijanej wybierz środowisko **Azure Media Service v3 Environment**.

    ![Wybór środowiska](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>Konfigurowanie kolekcji

1. Kliknij przycisk **Importuj**, aby zaimportować plik kolekcji.
1. Przejdź do pliku `Media Services v3.postman_collection.json`, który został pobrany podczas klonowania `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`
3. Wybierz plik **Media Services v3.postman_collection.json**.

    ![Importowanie pliku](./media/develop-with-postman/postman-import-collection.png)

## <a name="get-azure-ad-token"></a>Pobieranie tokenu usługi Azure AD 

Przed rozpoczęciem manipulowanie AMS v3 zasobów należy do pobierania i ustawiania usługi Azure AD tokenu uwierzytelniania jednostki usługi.

1. W lewym okienku okna narzędzia Postman, wybierz pozycję "krok 1: Pobierz uwierzytelniania usługi AAD token".
2. Następnie wybierz pozycję „Pobieranie tokenu usługi Azure AD do uwierzytelniania jednostki usługi”.
3. Kliknij pozycję **Wyślij**.

    Zostanie wysłana następująca operacja **POST**.

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. Odpowiedź wróci z tokenem i ustawi zmienną środowiskową „AccessToken” na wartość tokenu.  

    ![Pobieranie tokenu usługi AAD](./media/develop-with-postman/postman-get-aad-auth-token.png)

## <a name="next-steps"></a>Kolejne kroki

[Stream plików z użyciem usług REST](stream-files-tutorial-with-rest.md).  
