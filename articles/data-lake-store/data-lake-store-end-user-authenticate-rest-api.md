---
title: 'Uwierzytelnianie użytkowników końcowych: interfejs API REST z usługą Azure Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak osiągnąć uwierzytelnianie użytkowników końcowych za pomocą usługi Azure Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory przy użyciu interfejsu API REST
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
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

W tym artykule dowiesz się, jak używać interfejsu API REST do uwierzytelniania użytkowników końcowych za pomocą usługi Azure Data Lake Storage Gen1. Aby uzyskać uwierzytelnianie między usługami przy użyciu usługi Lake Storage Gen1 przy użyciu interfejsu API REST, zobacz [Uwierzytelnianie usługi do usługi przy użyciu usługi Lake Storage Gen1 przy użyciu interfejsu API REST](data-lake-store-service-to-service-authenticate-rest-api.md).

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Utwórz "natywną" aplikację usługi Azure Active Directory**. Kroki w uwierzytelnianiu użytkowników końcowych za [pomocą usługi Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

* **[cURL](https://curl.haxx.se/)**. W tym artykule użyto cURL, aby zademonstrować, jak wykonać wywołania interfejsu API REST na koncie Data Lake Storage Gen1.

## <a name="end-user-authentication"></a>Uwierzytelnianie użytkowników końcowych
Uwierzytelnianie użytkowników końcowych jest zalecane podejście, jeśli chcesz, aby użytkownik zalogował się do aplikacji przy użyciu usługi Azure AD. Aplikacja może uzyskać dostęp do zasobów platformy Azure z tym samym poziomem dostępu, co zalogowany użytkownik. Użytkownik musi okresowo podawać swoje poświadczenia, aby aplikacja zachowała dostęp.

Wynik logowania użytkownika końcowego jest, że aplikacja otrzymuje token dostępu i token odświeżania. Token dostępu zostanie dołączony do każdego żądania złożonego do usługi Data Lake Storage Gen1 lub Data Lake Analytics i jest domyślnie ważny przez jedną godzinę. Token odświeżania może służyć do uzyskania nowego tokenu dostępu i jest on ważny przez maksymalnie dwa tygodnie domyślnie, jeśli jest używany regularnie. Można użyć dwóch różnych metod logowania użytkownika końcowego.

W tym scenariuszu aplikacja wyświetla monit o zalogowanie się i wówczas wszystkie operacje są wykonywane w kontekście zalogowanego użytkownika. Wykonaj poniższe czynności:

1. Za pomocą aplikacji przekieruj użytkownika pod następujący adres URL:

        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<APPLICATION-ID>&response_type=code&redirect_uri=<REDIRECT-URI>

   > [!NOTE]
   > Identyfikator \<REDIRECT-URI> musi być zakodowany na potrzeby adresu URL. Tak więc, https://localhost `https%3A%2F%2Flocalhost`dla , użyj )

    Na potrzeby tego samouczka możesz zastąpić symbole zastępcze w powyższym adresie URL i wkleić go w pasku adresu przeglądarki sieci web. Nastąpi przekierowanie w celu uwierzytelniania przy użyciu identyfikatora logowania do platformy Azure. Gdy pomyślnie się zalogujesz, odpowiedź zostanie wyświetlona na pasku adresu przeglądarki. Odpowiedź będzie miała następujący format:

        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>

2. Przechwyć kod autoryzacji z odpowiedzi. W tym samouczku można skopiować kod autoryzacji z paska adresu przeglądarki sieci web i przekazać go w żądaniu POST do punktu końcowego tokenu, jak pokazano w następującym urywek:

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

3. Odpowiedź jest obiektem JSON, który zawiera token `"access_token": "<ACCESS_TOKEN>"`dostępu (na przykład) `"refresh_token": "<REFRESH_TOKEN>"`i token odświeżania (na przykład). Aplikacja używa tokenu dostępu podczas uzyskiwania dostępu do usługi Azure Data Lake Storage Gen1 i tokenu odświeżania, aby uzyskać inny token dostępu po wygaśnięciu tokenu dostępu.

        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}

4. Po wygaśnięciu tokenu dostępu można zażądać nowego tokenu dostępu przy użyciu tokenu odświeżania, jak pokazano w następującym urywek:

        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
             -F grant_type=refresh_token \
             -F resource=https://management.core.windows.net/ \
             -F client_id=<APPLICATION-ID> \
             -F refresh_token=<REFRESH-TOKEN>

Więcej informacji na temat interakcyjnego uwierzytelniania użytkownika zawiera temat [Authorization code grant flow](https://msdn.microsoft.com/library/azure/dn645542.aspx) (Przepływ udzielania kodu autoryzacji).

## <a name="next-steps"></a>Następne kroki
W tym artykule dowiesz się, jak używać uwierzytelniania usługi do usługi do uwierzytelniania przy użyciu usługi Azure Data Lake Storage Gen1 przy użyciu interfejsu API REST. Teraz można spojrzeć na następujące artykuły, które mówią o tym, jak używać interfejsu API REST do pracy z usługą Azure Data Lake Storage Gen1.

* [Operacje zarządzania kontami w usłudze Data Lake Storage Gen1 przy użyciu interfejsu REST API](data-lake-store-get-started-rest-api.md)
* [Operacje danych w usłudze Data Lake Storage Gen1 przy użyciu interfejsu REST API](data-lake-store-data-operations-rest-api.md)

