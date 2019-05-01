---
title: Użyj usługi Azure AD uwierzytelniania dostępu do interfejsu API usługi multimediów Azure z użyciem usług REST | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dostępu do interfejsu API usługi Azure Media Services przy użyciu uwierzytelniania usługi Azure Active Directory za pomocą interfejsu REST.
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
ms.author: willzhan;juliako;johndeu
ms.openlocfilehash: 6284a1aa0cc3a49291553309b058e4d9f65b24c6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64701013"
---
# <a name="use-azure-ad-authentication-to-access-the-media-services-api-with-rest"></a>Uwierzytelnianie usługi Azure AD umożliwia dostęp do interfejsu API Media Services za pomocą architektury REST

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówek dotyczących migracji od v2 do v3](../latest/migrate-from-v2-to-v3.md)

Podczas korzystania z uwierzytelniania usługi Azure AD za pomocą usługi Azure Media Services, można uwierzytelniać się w jeden z dwóch sposobów:

- **Uwierzytelnianie użytkownika** uwierzytelnia osobę, która jest za pomocą aplikacji do interakcji z zasobami usługi Azure Media Services. Interaktywna aplikacja najpierw powinien zostać wyświetlony monit o podanie poświadczeń użytkownika. Przykładem jest aplikacja do konsoli zarządzania, która jest używana przez autoryzowanych użytkowników do monitorowania zadań kodowania lub transmisja strumieniowa na żywo. 
- **Uwierzytelnianie jednostki usługi** uwierzytelnia usługi. Aplikacje, które często używają tej metody uwierzytelniania są aplikacje, które działają usługi demona, usługi warstwy środkowej lub zaplanowanego zadania, takie jak aplikacje sieci web, aplikacji funkcji, logic apps, interfejsów API lub mikrousług.

    W tym samouczku pokazano, jak używać usługi Azure AD **nazwy głównej usługi** uwierzytelniania dostępu do interfejsu API usługi AMS, z użyciem usług REST. 

    > [!NOTE]
    > **Nazwa główna usługi** jest zalecanym najlepszym rozwiązaniem w przypadku większości aplikacji nawiązywania połączenia z usługi Azure Media Services. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Uzyskaj informacje o uwierzytelnianiu w witrynie Azure portal
> * Uzyskiwanie tokenu dostępu przy użyciu narzędzia Postman
> * Test **zasoby** interfejsu API przy użyciu tokenu dostępu


> [!IMPORTANT]
> Obecnie usługa Media Services obsługuje model uwierzytelniania usługi Azure Access Control. Jednak kontroli dostępu uwierzytelniania staną się przestarzałe 1 czerwca 2018 r. Zalecamy jak najszybszą migrację do modelu uwierzytelniania za pomocą usługi Azure AD.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Tworzenie konta usługi Azure Media Services przy użyciu witryny Azure portal](media-services-portal-create-account.md).
- Przegląd [uzyskiwania dostępu do usługi Azure Media Services interfejsu API za pomocą omówienie uwierzytelniania usługi Azure AD](media-services-use-aad-auth-to-access-ams-api.md) artykułu.
- Zainstaluj [Postman](https://www.getpostman.com/) klienta REST do wykonywania interfejsów API REST, przedstawione w tym artykule. 

    W tym samouczku używamy **Postman** , ale odpowiednia może być dowolne narzędzie REST. Inne możliwości: Program **Visual Studio Code** z wtyczką REST lub program **Telerik Fiddler**. 

## <a name="get-the-authentication-information-from-the-azure-portal"></a>Uzyskaj informacje o uwierzytelnianiu w witrynie Azure portal

### <a name="overview"></a>Omówienie

Aby uzyskać dostęp do interfejsu API usługi Media Services, należy zebrać następujących punktów danych.

|Ustawienie|Przykład|Opis|
|---|-------|-----|
|Domena dzierżawy usługi Azure Active Directory|microsoft.onmicrosoft.com|Usługa Azure AD jako punktu końcowego Secure Token Service (STS) jest tworzony przy użyciu następującego formatu: <https://login.microsoftonline.com/{your-ad-tenant-name.onmicrosoft.com}/oauth2/token>. Usługa Azure AD wystawia token JWT w celu uzyskania dostępu do zasobów (token dostępu).|
|Punkt końcowy interfejsu API REST|<https://amshelloworld.restv2.westus.media.azure.net/api/>|To jest punkt końcowy, względem których wszystkich nośników interfejsu API REST usług w Twojej aplikacji wywołań.|
|Identyfikator klienta (identyfikator aplikacji)|f7fbbb29-a02d-4d91-bbc6-59a2579259d2|Identyfikatora aplikacji (klienta) w usłudze Azure AD Identyfikator klienta jest wymagany do uzyskania tokenu dostępu. |
|Wpis tajny klienta|+mUERiNzVMoJGggD6aV1etzFGa1n6KeSlLjIq+Dbim0=|Usługa Azure AD klucze aplikacji (klucz tajny klienta). Klucz tajny klienta jest wymagany do uzyskania tokenu dostępu.|

### <a name="get-aad-auth-info-from-the-azure-portal"></a>Uzyskaj informacje o uwierzytelnianiu w usłudze AAD w witrynie Azure portal

Aby uzyskać informacje, wykonaj następujące kroki:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
2. Przejdź do swojego wystąpienia usługi AMS.
3. Wybierz **dostęp do interfejsu API**.
4. Kliknij pozycję **nawiązywanie połączenia z interfejsem API usług Media platformy Azure przy użyciu jednostki usługi**.

    ![Dostęp do interfejsu API](./media/connect-with-rest/connect-with-rest01.png)

5. Wybierz istniejącą **aplikacji usługi Azure AD** lub Utwórz nową (pokazana poniżej).

    > [!NOTE]
    > Dla żądania REST multimediów Azure została wykonana pomyślnie, musi mieć użytkownika wywołującego **Współautor** lub **właściciela** konto usługi Media Services próby uzyskania dostępu do roli. Jeśli pojawi się wyjątek, który jest wyświetlany komunikat "serwer zdalny zwrócił błąd: (401) nieautoryzowane,"zobacz [kontroli dostępu](media-services-use-aad-auth-to-access-ams-api.md#access-control).

    Jeśli musisz utworzyć nową aplikację usługi AD, wykonaj następujące czynności:
    
   1. Naciśnij klawisz **tworzenia nowych**.
   2. Wprowadź nazwę.
   3. Naciśnij klawisz **Utwórz nowy** ponownie.
   4. Naciśnij pozycję **Zapisz**.

      ![Dostęp do interfejsu API](./media/connect-with-rest/new-app.png)

      Nowa aplikacja jest wyświetlana na stronie.

6. Pobierz **identyfikator klienta** (identyfikator aplikacji).
    
   1. Wybierz aplikację.
   2. Pobierz **identyfikator klienta** w oknie po prawej stronie. 

      ![Dostęp do interfejsu API](./media/connect-with-rest/existing-client-id.png)

7. Pobieranie aplikacji **klucz** (klucz tajny klienta). 

   1. Kliknij przycisk **Zarządzanie aplikacją** przycisku (należy zauważyć, że informacje o identyfikator klienta podlega **identyfikator aplikacji**). 
   2. Naciśnij klawisz **klucze**.
    
       ![Dostęp do interfejsu API](./media/connect-with-rest/manage-app.png)
   3. Generowanie klucza aplikacji (klucz tajny klienta), wypełniając **opis** i **EXPIRES** i naciskając klawisz **Zapisz**.
    
       Gdy **Zapisz** przycisk jest wciśnięty, pojawi się wartość klucza. Skopiuj wartość klucza przed opuszczeniem bloku.

   ![Dostęp do interfejsu API](./media/connect-with-rest/connect-with-rest03.png)

Można dodać wartości dla parametrów połączenia usługi AD do pliku web.config lub app.config w celu późniejszego użycia w kodzie.

> [!IMPORTANT]
> **Klucz klienta** jest ważne wpis tajny i powinny być poprawnie zabezpieczone w magazynie kluczy i szyfrowane w środowisku produkcyjnym.

## <a name="get-the-access-token-using-postman"></a>Uzyskiwanie tokenu dostępu przy użyciu narzędzia Postman

W tej sekcji pokazano, jak używać **Postman** do wykonania interfejsu API REST, która zwraca tokenu elementu nośnego JWT (token dostępu). Do wywołania dowolnego interfejsu API REST usługi Media, musisz dodać nagłówek "Autoryzacja" do wywołań i Dodaj wartość "Bearer *your_access_token*" do każdego wywołania (jak pokazano w następnej sekcji tego samouczka). 

1. Otwórz **Postman**.
2. Wybierz pozycję **POST**.
3. Wprowadź adres URL, który zawiera nazwę dzierżawy w następującym formacie: Nazwa dzierżawy powinien kończyć się **. onmicrosoft.com** i adres URL powinien kończyć się znakiem **oauth2/token**: 

    https://login.microsoftonline.com/{your-aad-tenant-name.onmicrosoft.com}/oauth2/token

4. Wybierz **nagłówki** kartę.
5. Wprowadź **nagłówki** informacji za pomocą "Klucz/wartość" siatki danych. 

    ![Siatka danych](./media/connect-with-rest/headers-data-grid.png)

    Alternatywnie kliknij **zbiorczo edytować** łącza po prawej stronie okna Narzędzie Postman i wklej następujący kod.

        Content-Type:application/x-www-form-urlencoded
        Keep-Alive:true

6. Naciśnij klawisz **treści** kartę.
7. Wprowadź informacje o treści, przy użyciu siatki danych "Klucz/wartość" (Zastąp identyfikator klienta i wpisu tajnego wartości). 

    ![Siatka danych](./media/connect-with-rest/data-grid.png)

    Alternatywnie kliknij **zbiorczo edytować** po prawej stronie okna Narzędzie Postman i wklej następujące treści (Zastąp identyfikator klienta i wartościami wpisów tajnych):

        grant_type:client_credentials
        client_id:{Your Client ID that you got from your Azure AD Application}
        client_secret:{Your client secret that you got from your Azure AD Application's Keys}
        resource:https://rest.media.azure.net

8. Kliknij pozycję **Wyślij**.

    ![Pobierz token](./media/connect-with-rest/connect-with-rest04.png)

Zwrócona odpowiedź zawiera **token dostępu** konieczne umożliwia dostęp do dowolnych interfejsów API usługi AMS.

## <a name="test-the-assets-api-using-the-access-token"></a>Test **zasoby** interfejsu API przy użyciu tokenu dostępu

W tej sekcji pokazano, jak uzyskać dostęp do **zasoby** przy użyciu interfejsu API **Postman**.

1. Otwórz **Postman**.
2. Wybierz pozycję **GET**.
3. (Na przykład, wkleić punkt końcowy interfejsu API REST https://amshelloworld.restv2.westus.media.azure.net/api/Assets)
4. Wybierz **autoryzacji** kartę. 
5. Wybierz **tokenu elementu nośnego**.
6. Wklej token, który został utworzony w poprzedniej sekcji.

    ![Pobierz token](./media/connect-with-rest/connect-with-rest05.png)

    > [!NOTE]
    > Postman UX może być różna Mac i komputera. Jeśli wersja Mac nie ma opcji "Elementu nośnego tokenu" **uwierzytelniania** listy rozwijanej w sekcji, należy dodać **autoryzacji** nagłówka ręcznie na kliencie Mac.

   ![Nagłówek uwierzytelniania](./media/connect-with-rest/auth-header.png)

7. Wybierz **nagłówki**.
5. Kliknij przycisk **zbiorczo edytować** łącza po prawej stronie okna narzędzia Postman.
6. Wklej następujące nagłówki:

        x-ms-version:2.15
        Accept:application/json
        Content-Type:application/json
        DataServiceVersion:3.0
        MaxDataServiceVersion:3.0

7. Kliknij pozycję **Wyślij**.

Zwrócona odpowiedź zawiera zasoby, które są w ramach Twojego konta.

## <a name="next-steps"></a>Kolejne kroki

* Wypróbuj ten kod przykładowy w [uwierzytelnianie usługi Azure AD, aby uzyskać dostępu do usług Azure Media Services: Zarówno za pomocą interfejsu API REST](https://github.com/willzhan/WAMSRESTSoln)
* [Przekazywanie plików za pomocą platformy .NET](media-services-dotnet-upload-files.md)
