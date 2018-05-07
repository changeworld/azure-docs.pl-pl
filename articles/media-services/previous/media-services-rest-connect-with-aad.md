---
title: "Uwierzytelnianie usługi Azure AD na dostęp do interfejsu API usługi multimediów Azure za pomocą REST | Dokumentacja firmy Microsoft"
description: "Dowiedz się, jak dostęp do interfejsu API usługi Azure Media Services przy użyciu uwierzytelniania usługi Azure Active Directory przy użyciu REST."
services: media-services
documentationcenter: 
author: willzhan
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/26/2017
ms.author: willzhan;juliako;johndeu
ms.openlocfilehash: ed78d6c6d4c695b841dbfbf917cd1681adc44ee7
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/14/2018
---
# <a name="use-azure-ad-authentication-to-access-the-azure-media-services-api-with-rest"></a>Azure Media Services API z POZOSTAŁĄ dostęp za pomocą uwierzytelniania usługi Azure AD

Podczas korzystania z uwierzytelniania usługi Azure AD z usługi Azure Media Services, można uwierzytelniać się w jednym z dwóch sposobów:

- **Uwierzytelnianie użytkownika** uwierzytelnia osoby, która korzysta z aplikacji do interakcji z zasobami usługi Azure Media Services. Interaktywna aplikacja powinna pierwszy monit o podanie poświadczeń. Przykładem jest aplikacja konsoli zarządzania używanym przez autoryzowanych użytkowników do monitorowania kodowania zadań lub transmisja strumieniowa na żywo. 
- **Uwierzytelnianie głównej usługi** uwierzytelnia usługi. Aplikacje, które zazwyczaj używają tej metody uwierzytelniania są aplikacji uruchamianych demon usługi, usługi warstwy środkowej lub zaplanowanych zadań, takich jak aplikacje sieci web funkcji aplikacji, aplikacji logiki, interfejsów API albo mikrousług.

    Ten samouczek pokazuje, jak przy użyciu usługi Azure AD **nazwy głównej usługi** uwierzytelniania dostępu AMS interfejs API REST. 

    > [!NOTE]
    > **Nazwy głównej usługi** jest zalecane dla większości aplikacji nawiązywania połączenia z usługą Azure Media Services. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Pobierz informacje o uwierzytelnianiu z portalu Azure
> * Uzyskaj token dostępu przy użyciu Postman
> * Test **zasoby** interfejsu API przy użyciu tokenu dostępu


> [!IMPORTANT]
> Obecnie usługa Media Services obsługuje model uwierzytelniania usługi kontroli dostępu platformy Azure. Jednak kontroli dostępu uwierzytelniania zostaną wycofane 1 czerwca 2018. Zalecamy jak najszybszą migrację do modelu uwierzytelniania za pomocą usługi Azure AD.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Tworzenie konta usługi Azure Media Services przy użyciu portalu Azure](media-services-portal-create-account.md).
- Przegląd [podczas uzyskiwania dostępu do usługi Azure Media usług interfejsu API za pomocą omówienie uwierzytelniania usługi AAD](media-services-use-aad-auth-to-access-ams-api.md) artykułu.
- Zainstaluj [Postman](https://www.getpostman.com/) klienta REST do wykonania interfejsów API REST przedstawiono w tym artykule. 

    W tym samouczku są nam uring **Postman** , ale odpowiednia może być dowolnego narzędzia REST. Inne alternatywne to: **Visual Studio Code** z wtyczki REST lub **Fiddler strony firmy Telerik**. 

## <a name="get-the-authentication-information-from-the-azure-portal"></a>Pobierz informacje o uwierzytelnianiu z portalu Azure

### <a name="overview"></a>Przegląd

Aby uzyskać dostęp do interfejsu API usług Media Services, które należy zebrać następujących punktów danych.

|Ustawienie|Przykład|Opis|
|---|-------|-----|
|Domena dzierżawy usługi Azure Active Directory|microsoft.onmicrosoft.com|Azure AD jako punktu końcowego Secure Token Service (STS) jest tworzony w następującym formacie: https://login.microsoftonline.com/{your-aad-tenant-name.onmicrosoft.com}/oauth2/token. Usługa Azure AD wystawia token JWT, aby uzyskać dostęp do zasobów (token dostępu).|
|Punkt końcowy interfejsu API REST|https://amshelloworld.restv2.westus.media.azure.net/api/|To jest punkt końcowy, z których wszystkie API REST usługi Media w aplikacji wywołań.|
|Identyfikator klienta (identyfikator aplikacji)|f7fbbb29-a02d-4d91-bbc6-59a2579259d2|Identyfikatora aplikacji (klienta) w usłudze Azure AD Identyfikator klienta jest wymagana do uzyskania tokenu dostępu. |
|Klucz tajny klienta|+mUERiNzVMoJGggD6aV1etzFGa1n6KeSlLjIq+Dbim0=|Azure AD kluczy aplikacji (klucz tajny klienta). Klucz tajny klienta jest wymagana do uzyskania tokenu dostępu.|

### <a name="get-aad-auth-info-from-the-azure-portal"></a>Pobierz informacje uwierzytelniania usługi AAD w portalu Azure

Aby uzyskać informacje, wykonaj następujące kroki:

1. Zaloguj się do witryny [Azure Portal](http://portal.azure.com).
2. Przejdź do swojego wystąpienia usługi AMS.
3. Wybierz **dostępu do interfejsu API**.
4. Polecenie **nawiązywanie Azure Media Services API z nazwy głównej usługi**.

    ![Dostęp do interfejsu API](./media/connect-with-rest/connect-with-rest01.png)

5. Wybierz istniejący **aplikacji usługi Azure AD** lub Utwórz nową (pokazana poniżej).

    > [!NOTE]
    > Dla żądania usługi Azure Media REST została wykonana pomyślnie, wywołujący użytkownik musi mieć **współautora** lub **właściciela** rolę dla konta usługi Media Services próbuje uzyskać dostęp. Jeśli otrzymasz wyjątek mówi "serwer zdalny zwrócił błąd: (401) nieautoryzowane" zobacz [kontrola dostępu](media-services-use-aad-auth-to-access-ams-api.md#access-control).

    Jeśli chcesz utworzyć nową aplikację usługi AD, wykonaj następujące kroki:
    
    1. Naciśnij klawisz **tworzenia nowych**.
    2. Wprowadź nazwę.
    3. Naciśnij klawisz **Utwórz nowy** ponownie.
    4. Naciśnij klawisz **zapisać**.

    ![Dostęp do interfejsu API](./media/connect-with-rest/new-app.png)

    Nowa aplikacja zostaną wyświetlone na stronie.

6. Pobierz **identyfikator klienta** (identyfikator aplikacji).
    
    1. Wybierz aplikację.
    2. Pobierz **identyfikator klienta** w oknie po prawej stronie. 

    ![Dostęp do interfejsu API](./media/connect-with-rest/existing-client-id.png).

7.  Pobierz aplikację **klucza** (klucz tajny klienta). 

    1. Kliknij przycisk **Zarządzanie aplikacją** przycisk (Zwróć uwagę, że informacje o Identyfikatora klienta jest poniżej **identyfikator aplikacji**). 
    2. Naciśnij klawisz **klucze**.
    
        ![Dostęp do interfejsu API](./media/connect-with-rest/manage-app.png)
    3. Generowanie klucza aplikacji (klucz tajny klienta), wypełniając **opis** i **EXPIRES** i naciskając klawisz **zapisać**.
    
        Raz **zapisać** przycisk jest naciśnięty, zostanie wyświetlona wartość klucza. Skopiuj wartość klucza przed opuszczeniem bloku.

    ![Dostęp do interfejsu API](./media/connect-with-rest/connect-with-rest03.png)

Wartości parametrów połączenia AD można dodać do pliku web.config lub app.config, w celu późniejszego użycia w kodzie.

> [!IMPORTANT]
> **Klucz klienta** jest ważny klucz tajny i powinny być prawidłowo zabezpieczone w magazynie kluczy i szyfrowane w środowisku produkcyjnym.

## <a name="get-the-access-token-using-postman"></a>Uzyskaj token dostępu przy użyciu Postman

W tej sekcji przedstawiono sposób użycia **Postman** do wykonania interfejsu API REST, która zwraca tokenów Bearer JWT (token dostępu). Aby wywołać jakiegokolwiek interfejsu API REST usługi nośnika, należy dodać nagłówka "Autoryzacji" do wywołania, a następnie dodaj wartość "Bearer *your_access_token*" dla każdego wywołania (jak pokazano w następnej sekcji tego samouczka). 

1. Otwórz **Postman**.
2. Wybierz pozycję **POST**.
3. Wprowadź adres URL, który zawiera nazwę dzierżawy w następującym formacie: Nazwa dzierżawy powinna kończyć się **. onmicrosoft.com** i adres URL powinien kończyć się **tokenuoauth2/**: 

    https://login.microsoftonline.com/{your-aad-tenant-name.onmicrosoft.com}/oauth2/token

4. Wybierz **nagłówki** kartę.
5. Wprowadź **nagłówki** informacji za pomocą "Klucz/wartość" siatka danych. 

    ![Siatki danych](./media/connect-with-rest/headers-data-grid.png)

    Alternatywnie kliknij **zbiorczo edytować** łącza po prawej stronie okna Postman i wklej następujący kod.

        Content-Type:application/x-www-form-urlencoded
        Keep-Alive:true

6. Naciśnij klawisz **treści** kartę.
7. Wprowadź informacje dotyczące treści przy użyciu siatki danych "Klucz/wartość" (Zastąp identyfikator klienta i tajne wartości). 

    ![Siatki danych](./media/connect-with-rest/data-grid.png)

    Alternatywnie kliknij **zbiorczo edytować** po prawej stronie okna Postman i wklej następujący treści (Zastąp identyfikator klienta i wartości tajne):

        grant_type:client_credentials
        client_id:{Your Client ID that you got from your AAD Application}
        client_secret:{Your client secret that you got from your AAD Application's Keys}
        resource:https://rest.media.azure.net

8. Kliknij pozycję **Wyślij**.

    ![Pobierz token](./media/connect-with-rest/connect-with-rest04.png)

Zwrócona odpowiedź zawiera **token dostępu** trzeba użyć dostępu do żadnych interfejsów API usług AMS.

## <a name="test-the-assets-api-using-the-access-token"></a>Test **zasoby** interfejsu API przy użyciu tokenu dostępu

W tej sekcji przedstawiono sposób uzyskiwania dostępu do **zasoby** przy użyciu interfejsu API **Postman**.

1. Otwórz **Postman**.
2. Wybierz pozycję **GET**.
3. Wklej (na przykład punkt końcowy interfejsu API REST https://amshelloworld.restv2.westus.media.azure.net/api/Assets)
4. Wybierz **autoryzacji** kartę. 
5. Wybierz **tokenu elementu nośnego**.
6. Wklej token, który został utworzony w poprzedniej sekcji.

    ![Pobierz token](./media/connect-with-rest/connect-with-rest05.png)

    > [!NOTE]
    > Postman UX może być różna Mac i komputera. Jeśli wersja Mac nie ma opcji "Tokenów Bearer" **uwierzytelniania** sekcji listy rozwijanej, należy dodać **autoryzacji** nagłówka ręcznie na kliencie Mac.

   ![Nagłówek uwierzytelniania](./media/connect-with-rest/auth-header.png)

7. Wybierz **nagłówki**.
5. Kliknij przycisk **zbiorczo edytować** łącza po prawej stronie okna Postman.
6. Wklej następujące nagłówki:

        x-ms-version:2.15
        Accept:application/json
        Content-Type:application/json
        DataServiceVersion:3.0
        MaxDataServiceVersion:3.0

7. Kliknij pozycję **Wyślij**.

Zwrócona odpowiedź zawiera zasobach, które są w ramach Twojego konta.

## <a name="next-steps"></a>Kolejne kroki

* Ten przykładowy kod w spróbuj [Azure AD Authentication Azure Media Services dostępu: zarówno za pośrednictwem interfejsu API REST](https://github.com/willzhan/WAMSRESTSoln)
* [Przekazywanie plików z platformą .NET](media-services-dotnet-upload-files.md)
