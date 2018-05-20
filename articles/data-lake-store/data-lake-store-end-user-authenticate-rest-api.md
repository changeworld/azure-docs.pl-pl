---
title: 'Uwierzytelnianie użytkownika końcowego: interfejsu API REST z usługą Data Lake Store za pomocą usługi Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak osiągnięcie uwierzytelniania użytkownika końcowego przy użyciu usługi Azure Active Directory przy użyciu interfejsu API REST usługi Data Lake Store
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 7bebe1948d26b26716262cafb92e3563f5c6b349
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="end-user-authentication-with-data-lake-store-using-rest-api"></a>Uwierzytelnianie użytkownika końcowego przy użyciu interfejsu API REST usługi Data Lake Store
> [!div class="op_single_selector"]
> * [Korzystanie z języka Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Korzystanie z zestawu SDK dla platformy .NET](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Korzystanie z języka Python](data-lake-store-end-user-authenticate-python.md)
> * [Korzystanie z interfejsu API REST](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

W tym artykule opisano sposób użycia interfejsu API REST w celu uwierzytelniania użytkowników końcowych za pomocą usługi Azure Data Lake Store. Usługa do uwierzytelniania przy użyciu interfejsu API REST usługi Data Lake Store, zobacz [do usługi uwierzytelniania przy użyciu interfejsu API REST usługi Data Lake Store](data-lake-store-service-to-service-authenticate-rest-api.md).

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Tworzenie aplikacji usługi Azure Active Directory "Native"**. Należy wykonać czynności opisane w [uwierzytelniania użytkowników końcowych za pomocą usługi Data Lake Store za pomocą usługi Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

* **[Adres cURL](http://curl.haxx.se/)**. W tym artykule jest używane narzędzie cURL w celu zademonstrowania sposobu wykonywania wywołań interfejsu API REST względem konta usługi Data Lake Store.

## <a name="end-user-authentication"></a>Uwierzytelnianie użytkowników końcowych
Uwierzytelnianie użytkownika końcowego jest zalecane podejście, jeśli użytkownik ma logować się do aplikacji przy użyciu usługi Azure AD. Aplikacja jest w stanie uzyskać dostęp do zasobów platformy Azure z tym samym poziomie dostępu jako zalogowany użytkownik. Użytkownik musi podać ich poświadczenia okresowo w kolejności dla aplikacji do obsługi dostępu.

Wynik po logowaniu użytkownika jest nadawana aplikacji, token dostępu i token odświeżania. Token dostępu jest dołączony do każdego żądania kierowane do usługi Data Lake Store lub usługi Data Lake Analytics i okres ważności wynosi godzinę domyślnie. Token odświeżania pozwala uzyskać nowy token dostępu i jest on prawidłowy przez maksymalnie dwa tygodnie domyślnie, jeśli regularnie używane. Dwa różne podejścia służy do logowania użytkownika końcowego.

W tym scenariuszu aplikacja wyświetla monit o zalogowanie się i wówczas wszystkie operacje są wykonywane w kontekście zalogowanego użytkownika. Wykonaj następujące czynności:

1. Za pomocą aplikacji przekieruj użytkownika pod następujący adres URL:
   
        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<APPLICATION-ID>&response_type=code&redirect_uri=<REDIRECT-URI>
   
   > [!NOTE]
   > Identyfikator \<REDIRECT-URI> musi być zakodowany na potrzeby adresu URL. Tak, aby uzyskać https://localhost, użyj `https%3A%2F%2Flocalhost`)
   > 
   > 
   
    Na potrzeby tego samouczka możesz zastąpić symbole zastępcze w powyższym adresie URL i wkleić go w pasku adresu przeglądarki sieci web. Nastąpi przekierowanie w celu uwierzytelniania przy użyciu identyfikatora logowania do platformy Azure. Gdy pomyślnie się zalogujesz, odpowiedź zostanie wyświetlona na pasku adresu przeglądarki. Odpowiedź będzie miała następujący format:
   
        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>

2. Przechwyć kod autoryzacji z odpowiedzi. W tym samouczku można skopiować kod autoryzacji z paska adresu przeglądarki sieci web i przekaż go pocztą żądania do punktu końcowego tokenu, jak pokazano w poniższy fragment kodu:
   
        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<APPLICATION-ID> \
        -F code=<AUTHORIZATION-CODE>
   
   > [!NOTE]
   > W takim przypadku identyfikatora \<REDIRECT-URI> nie trzeba kodować.
   > 
   > 

3. Odpowiedzią jest obiekt JSON, który zawiera token dostępu (na przykład `"access_token": "<ACCESS_TOKEN>"`) oraz token odświeżania (na przykład `"refresh_token": "<REFRESH_TOKEN>"`). Aplikacja używa tokenu dostępu podczas uzyskiwania dostępu do usługi Azure Data Lake Store i tokenu odświeżania, aby uzyskać inny token dostępu w przypadku wygaśnięcia tokenu dostępu.
   
        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}

4. Po wygaśnięciu tokenu dostępu możesz zażądać nowy token dostępu, używając tokenu odświeżania pokazane na poniższy fragment kodu:
   
        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
             -F grant_type=refresh_token \
             -F resource=https://management.core.windows.net/ \
             -F client_id=<APPLICATION-ID> \
             -F refresh_token=<REFRESH-TOKEN>

Więcej informacji na temat interakcyjnego uwierzytelniania użytkownika zawiera temat [Authorization code grant flow](https://msdn.microsoft.com/library/azure/dn645542.aspx) (Przepływ udzielania kodu autoryzacji).
   
## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób uwierzytelniania za pomocą usługi Azure Data Lake Store za pomocą usługi do uwierzytelniania przy użyciu interfejsu API REST. Teraz można przeglądać następujące artykuły, które opisano sposób użycia interfejsu API REST do pracy z usługą Azure Data Lake Store.

* [Operacji zarządzania kontem w usłudze Data Lake Store za pomocą interfejsu API REST](data-lake-store-get-started-rest-api.md)
* [Operacje na danych w usłudze Data Lake Store za pomocą interfejsu API REST](data-lake-store-data-operations-rest-api.md)

