---
title: 'Uwierzytelnianie użytkowników końcowych: Interfejs API REST przy użyciu usługi Azure Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak wykonać uwierzytelnianie użytkowników końcowych za pomocą usługi Azure Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory przy użyciu interfejsu API REST
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 0ef65c23ee1bf4f064695779b71c8616427da204
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60877826"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Uwierzytelnianie użytkowników końcowych za pomocą usługi Azure Data Lake Storage Gen1 przy użyciu interfejsu API REST
> [!div class="op_single_selector"]
> * [Korzystanie z języka Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Korzystanie z zestawu SDK dla platformy .NET](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Korzystanie z języka Python](data-lake-store-end-user-authenticate-python.md)
> * [Korzystanie z interfejsu API REST](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

Ten artykuł zawiera informacje o sposobie używania interfejsu API REST w celu uwierzytelniania użytkowników końcowych za pomocą usługi Azure Data Lake Storage Gen1. Do usługi do uwierzytelniania przy użyciu Data Lake Storage Gen1 przy użyciu interfejsu API REST, zobacz [Service to service uwierzytelnianie za pomocą programu Data Lake Storage Gen1 przy użyciu interfejsu API REST](data-lake-store-service-to-service-authenticate-rest-api.md).

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Tworzenie aplikacji usługi Azure Active Directory "Natywnego"**. Zostały wykonane kroki opisane w [uwierzytelnianie użytkowników końcowych za pomocą programu Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

* **[Adres cURL](https://curl.haxx.se/)**. W tym artykule używa programu cURL w celu zademonstrowania sposobu wykonywania wywołań interfejsu API REST względem konta Data Lake Storage Gen1.

## <a name="end-user-authentication"></a>Uwierzytelnianie użytkowników końcowych
Uwierzytelnianie użytkowników końcowych jest zalecaną metodą, jeśli chcesz, aby użytkownika do logowania do aplikacji za pomocą usługi Azure AD. Aplikacja jest w stanie uzyskać dostęp do zasobów platformy Azure na tym samym poziomie dostępu jako zalogowanego użytkownika. Użytkownik musi podawać swoich poświadczeń kolei okresowo dla swojej aplikacji zachować dostęp.

Wynik o logowaniu użytkownika jest, że aplikacja otrzymuje token dostępu i token odświeżania. Token dostępu jest dołączany do każdego żądania wysłanego do programu Data Lake Storage Gen1 lub Data Lake Analytics i jest on prawidłowy dla jednej godziny domyślnie. Token odświeżania można uzyskać nowy token dostępu i jest on prawidłowy dla do dwóch tygodni, domyślnie, jeśli używane regularnie. Można użyć dwa różne podejścia dla nazwy logowania użytkownika końcowego.

W tym scenariuszu aplikacja wyświetla monit o zalogowanie się i wówczas wszystkie operacje są wykonywane w kontekście zalogowanego użytkownika. Wykonaj poniższe czynności:

1. Za pomocą aplikacji przekieruj użytkownika pod następujący adres URL:

        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<APPLICATION-ID>&response_type=code&redirect_uri=<REDIRECT-URI>

   > [!NOTE]
   > Identyfikator \<REDIRECT-URI> musi być zakodowany na potrzeby adresu URL. Tak, aby uzyskać https://localhost, użyj `https%3A%2F%2Flocalhost`)

    Na potrzeby tego samouczka możesz zastąpić symbole zastępcze w powyższym adresie URL i wkleić go w pasku adresu przeglądarki sieci web. Nastąpi przekierowanie w celu uwierzytelniania przy użyciu identyfikatora logowania do platformy Azure. Gdy pomyślnie się zalogujesz, odpowiedź zostanie wyświetlona na pasku adresu przeglądarki. Odpowiedź będzie miała następujący format:

        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>

2. Przechwyć kod autoryzacji z odpowiedzi. W tym samouczku można skopiować kod autoryzacji z paska adresu przeglądarki sieci web i przekazać go we WPISIE żądania do punktu końcowego tokenu, jak pokazano w poniższym fragmencie kodu:

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

3. Odpowiedzią jest obiekt JSON, który zawiera token dostępu (na przykład `"access_token": "<ACCESS_TOKEN>"`) i tokenu odświeżania (na przykład `"refresh_token": "<REFRESH_TOKEN>"`). Aplikacja używa tokenu dostępu podczas uzyskiwania dostępu do usługi Azure Data Lake Storage Gen1 i token odświeżania, aby uzyskać inny token dostępu po wygaśnięciu token dostępu.

        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}

4. Po wygaśnięciu tokenu dostępu możesz zażądać nowego tokenu dostępu przy użyciu tokenu odświeżania, jak pokazano w poniższym fragmencie kodu:

        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
             -F grant_type=refresh_token \
             -F resource=https://management.core.windows.net/ \
             -F client_id=<APPLICATION-ID> \
             -F refresh_token=<REFRESH-TOKEN>

Więcej informacji na temat interakcyjnego uwierzytelniania użytkownika zawiera temat [Authorization code grant flow](https://msdn.microsoft.com/library/azure/dn645542.aspx) (Przepływ udzielania kodu autoryzacji).

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób uwierzytelniania za pomocą usługi Azure Data Lake Storage Gen1 za pomocą uwierzytelniania service to service przy użyciu interfejsu API REST. Możesz teraz przejrzeć następujące artykuły, które omówiono sposób użycia interfejsu API REST do pracy z usługi Azure Data Lake Storage Gen1.

* [Operacje zarządzania kontem w Data Lake Storage Gen1 przy użyciu interfejsu API REST](data-lake-store-get-started-rest-api.md)
* [Operacje na danych na Data Lake Storage Gen1 przy użyciu interfejsu API REST](data-lake-store-data-operations-rest-api.md)

