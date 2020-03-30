---
title: Korzystanie z uwierzytelniania usługi Azure AD w celu uzyskania dostępu do interfejsu API usługi Azure w usłudze Media Services za pomocą restu | Dokumenty firmy Microsoft
description: Dowiedz się, jak uzyskać dostęp do interfejsu API usługi Azure Media Services za pomocą uwierzytelniania usługi Azure Active Directory przy użyciu funkcji REST.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.reviewer: willzhan; johndeu
ms.openlocfilehash: a693eb374365670da3fe8c4b2bb8ce664a024217
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295441"
---
# <a name="use-azure-ad-authentication-to-access-the-media-services-api-with-rest"></a>Używanie uwierzytelniania usługi Azure AD do uzyskiwania dostępu do interfejsu API usługi Media Services za pomocą architektury REST

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Sprawdź najnowszą wersję usługi [Media Services w wersji 3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówki dotyczące migracji z wersji 2 do v3](../latest/migrate-from-v2-to-v3.md)

Korzystając z uwierzytelniania usługi Azure AD za pomocą usługi Azure Media Services, można uwierzytelnić się na jeden z dwóch sposobów:

- **Uwierzytelnianie użytkownika** uwierzytelnia osobę, która używa aplikacji do interakcji z zasobami usługi Azure Media Services. Interaktywna aplikacja powinna najpierw monitować użytkownika o poświadczenia. Przykładem jest aplikacja konsoli zarządzania, która jest używana przez autoryzowanych użytkowników do monitorowania zadań kodowania lub przesyłania strumieniowego na żywo. 
- **Uwierzytelnianie jednostkowe usługi** uwierzytelnia usługę. Aplikacje, które często używają tej metody uwierzytelniania są aplikacje, które uruchamiają usługi demona, usługi warstwy środkowej lub zaplanowane zadania, takie jak aplikacje sieci web, aplikacje funkcji, aplikacje logiki, interfejsy API lub mikrousług.

    W tym samouczku pokazano, jak używać **uwierzytelniania jednostkowego usługi** Azure AD, aby uzyskać dostęp do interfejsu API usługi AMS za pomocą rest. 

    > [!NOTE]
    > **Podmiot zabezpieczeń usługi** jest zalecane najlepsze rozwiązanie dla większości aplikacji łączących się z usługą Azure Media Services. 

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Pobierz informacje uwierzytelniania z witryny Azure portal
> * Pobierz token dostępu za pomocą Listonosza
> * Testowanie interfejsu API **zasobów** przy użyciu tokenu dostępu


> [!IMPORTANT]
> Obecnie usługa Media Services obsługuje model uwierzytelniania usług Azure Access Control. Jednak uwierzytelnianie kontroli dostępu zostanie przestarzałe 1 czerwca 2018 r. Zalecamy jak najszybszą migrację do modelu uwierzytelniania za pomocą usługi Azure AD.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) przed rozpoczęciem.
- [Utwórz konto usługi Azure Media Services przy użyciu portalu Azure](media-services-portal-create-account.md).
- Przejrzyj [omówienie interfejsu API usługi Azure Media Services w usłudze Accessing za pomocą uwierzytelniania usługi Azure AD.](media-services-use-aad-auth-to-access-ams-api.md)
- Zainstaluj klienta [PONAWIACYJNY,](https://www.getpostman.com/) aby wykonać interfejsy API REST pokazane w tym artykule. 

    W tym samouczku używamy **Postman,** ale każde narzędzie REST byłoby odpowiednie. Inne alternatywy to: **Visual Studio Code** z wtyczką REST lub **Telerik Fiddler**. 

## <a name="get-the-authentication-information-from-the-azure-portal"></a>Pobierz informacje uwierzytelniania z witryny Azure portal

### <a name="overview"></a>Omówienie

Aby uzyskać dostęp do interfejsu API usługi Media Services, należy zebrać następujące punkty danych.

|Ustawienie|Przykład|Opis|
|---|-------|-----|
|Domena dzierżawy usługi Azure Active Directory|microsoft.onmicrosoft.com|Punkt końcowy usługi Azure AD jako usługi bezpiecznego tokenu (STS) jest tworzony w następującym formacie: <https://login.microsoftonline.com/{your-ad-tenant-name.onmicrosoft.com}/oauth2/token>. Usługa Azure AD wystawia JWT w celu uzyskania dostępu do zasobów (token dostępu).|
|Punkt końcowy interfejsu API REST|<https://amshelloworld.restv2.westus.media.azure.net/api/>|Jest to punkt końcowy, względem którego są wszystkie media services REST interfejsu API wywołania w aplikacji są dokonywane.|
|Identyfikator klienta (identyfikator aplikacji)|f7fbbb29-a02d-4d91-bbc6-59a2579259d2|Identyfikator aplikacji (klienta) usługi Azure AD. Identyfikator klienta jest wymagany do uzyskania tokenu dostępu. |
|Klucz tajny klienta|+mUERiNzVMoJGggD6aV1etzFGa1n6KeSlLjIq+Dbim0=|Klucze aplikacji usługi Azure AD (klucz tajny klienta). Klucz tajny klienta jest wymagany do uzyskania tokenu dostępu.|

### <a name="get-aad-auth-info-from-the-azure-portal"></a>Uzyskaj informacje auth AAD z witryny Azure portal

Aby uzyskać informacje, wykonaj następujące kroki:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
2. Przejdź do wystąpienia ams.
3. Wybierz **dostęp do interfejsu API**.
4. Kliknij pozycję **Połącz z interfejsem API usługi Azure Media Services z jednostką usługi**.

    ![Dostęp za pomocą interfejsu API](./media/connect-with-rest/connect-with-rest01.png)

5. Wybierz istniejącą **aplikację usługi Azure AD** lub utwórz nową (pokazaną poniżej).

    > [!NOTE]
    > Aby żądanie REST multimediów platformy Azure powiodło się, użytkownik wywołujący musi mieć rolę **współautora** lub **właściciela** dla konta usługi Media Services, do które próbuje uzyskać dostęp. Jeśli pojawi się wyjątek z napisem "Serwer zdalny zwrócił błąd: (401) Nieautoryzowane", zobacz [Kontrola dostępu](media-services-use-aad-auth-to-access-ams-api.md#access-control).

    Jeśli chcesz utworzyć nową aplikację AD, wykonaj następujące czynności:
    
   1. Naciśnij **przycisk Utwórz nowy**.
   2. Wprowadź nazwę.
   3. Naciśnij ponownie **pozycję Utwórz nowy.**
   4. Naciśnij pozycję **Zapisz**.

      ![Dostęp za pomocą interfejsu API](./media/connect-with-rest/new-app.png)

      Nowa aplikacja pojawi się na stronie.

6. Pobierz identyfikator **klienta** (identyfikator aplikacji).
    
   1. Wybierz aplikację.
   2. Pobierz identyfikator **klienta** z okna po prawej stronie. 

      ![Dostęp za pomocą interfejsu API](./media/connect-with-rest/existing-client-id.png)

7. Pobierz klucz aplikacji **(klucz** tajny klienta). 

   1. Kliknij przycisk **Zarządzaj aplikacją** (zwróć uwagę, że informacje o identyfikatorze klienta są pod **identyfikatorem aplikacji**). 
   2. Naciśnij **klawisze**.
    
       ![Dostęp za pomocą interfejsu API](./media/connect-with-rest/manage-app.png)
   3. Wygeneruj klucz aplikacji (klucz tajny klienta), wypełniając **OPIS** i **WYGASA** oraz naciskając **zapisz**.
    
       Po naciśnięciu przycisku **Zapisz** pojawi się wartość klawisza. Skopiuj wartość klucza przed opuszczeniem bloku.

   ![Dostęp za pomocą interfejsu API](./media/connect-with-rest/connect-with-rest03.png)

Do pliku web.config lub app.config można dodać wartości parametrów połączenia usługi AD, aby później użyć go w kodzie.

> [!IMPORTANT]
> **Klucz klienta** jest ważnym kluczem tajnym i powinien być odpowiednio zabezpieczony w magazynie kluczy lub zaszyfrowany w produkcji.

## <a name="get-the-access-token-using-postman"></a>Pobierz token dostępu za pomocą Listonosza

W tej sekcji pokazano, jak używać **postmana** do wykonywania interfejsu API REST, który zwraca token nośnika JWT (token dostępu). Aby wywołać dowolny interfejs API REST usługi Media Services, należy dodać nagłówek "Autoryzacja" do wywołań i dodać wartość *"Your_access_token*na okaziciela" do każdego wywołania (jak pokazano w następnej sekcji tego samouczka). 

1. Otwórz **listonosz**.
2. Wybierz pozycję **POST**.
3. Wprowadź adres URL zawierający nazwę dzierżawy przy użyciu następującego formatu: nazwa dzierżawy powinna kończyć się **na onmicrosoft.com,** a adres URL powinien kończyć **się oauth2/token:** 

    `https://login.microsoftonline.com/{your-aad-tenant-name.onmicrosoft.com}/oauth2/token`

4. Wybierz kartę **Nagłówki.**
5. Wprowadź informacje o **nagłówkach** przy użyciu siatki danych "Klucz/wartość". 

    ![siatka danych](./media/connect-with-rest/headers-data-grid.png)

    Możesz też kliknąć **łącze Edycja zbiorcza** po prawej stronie okna Listonosz i wkleić następujący kod.

        Content-Type:application/x-www-form-urlencoded
        Keep-Alive:true

6. Naciśnij kartę **Ciało.**
7. Wprowadź informacje o treści przy użyciu siatki danych "Klucz/wartość" (zastąp identyfikator klienta i wartości tajne). 

    ![siatka danych](./media/connect-with-rest/data-grid.png)

    Możesz też kliknąć **opcję Edycja zbiorcza** po prawej stronie okna Listonosz i wkleić następujący obiekt (zastąp identyfikator klienta i wartości tajne):

        grant_type:client_credentials
        client_id:{Your Client ID that you got from your Azure AD Application}
        client_secret:{Your client secret that you got from your Azure AD Application's Keys}
        resource:https://rest.media.azure.net

8. Kliknij pozycję **Wyślij**.

    ![pobierz token](./media/connect-with-rest/connect-with-rest04.png)

Zwrócona odpowiedź zawiera **token dostępu,** którego należy użyć, aby uzyskać dostęp do dowolnych interfejsów API usługi AMS.

## <a name="test-the-assets-api-using-the-access-token"></a>Testowanie interfejsu API **zasobów** przy użyciu tokenu dostępu

W tej sekcji pokazano, jak uzyskać dostęp do interfejsu API **zasobów** za pomocą **programu Postman**.

1. Otwórz **listonosz**.
2. Wybierz pozycję **GET**.
3. Wklej punkt końcowy interfejsu API REST (na przykładhttps://amshelloworld.restv2.westus.media.azure.net/api/Assets)
4. Wybierz kartę **Autoryzacja.** 
5. Wybierz **token na okaziciela**.
6. Wklej token, który został utworzony w poprzedniej sekcji.

    ![pobierz token](./media/connect-with-rest/connect-with-rest05.png)

    > [!NOTE]
    > Ux Listonosz może być inny między Mac i PC. Jeśli wersja dla komputerów Mac nie ma opcji "Token na nośniku" w sekcji **Uwierzytelnianie,** należy ręcznie dodać nagłówek **Autoryzacja** na kliencie komputera Mac.

   ![Nagłówek emocie](./media/connect-with-rest/auth-header.png)

7. Wybierz **nagłówki**.
5. Kliknij **pozycję Zbiorcze łącze Edycji** po prawej stronie okna Listonosz.
6. Wklej następujące nagłówki:

        x-ms-version:2.19
        Accept:application/json
        Content-Type:application/json
        DataServiceVersion:3.0
        MaxDataServiceVersion:3.0

7. Kliknij pozycję **Wyślij**.

Zwrócona odpowiedź zawiera zasoby, które znajdują się na twoim koncie.

## <a name="next-steps"></a>Następne kroki

* Wypróbuj ten przykładowy kod w [usłudze Azure AD Authentication for Azure Media Services Access: Oba za pośrednictwem interfejsu API REST](https://github.com/willzhan/WAMSRESTSoln)
* [Przekazywanie plików za pomocą platformy .NET](media-services-dotnet-upload-files.md)
