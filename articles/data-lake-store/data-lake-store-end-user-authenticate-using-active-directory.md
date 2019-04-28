---
title: 'Uwierzytelnianie użytkowników końcowych: Usługa Azure Data Lake Storage Gen1 za pomocą usługi Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak wykonać uwierzytelnianie użytkowników końcowych za pomocą usługi Azure Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory
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
ms.openlocfilehash: c0fe63e395ee08cb65e9bbbadc4ce1f03032ce95
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60878252"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Uwierzytelnianie użytkowników końcowych za pomocą usługi Azure Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory
> [!div class="op_single_selector"]
> * [Uwierzytelnianie użytkowników końcowych](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Uwierzytelnianie między usługami](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
> 

Azure Data Lake Storage Gen1 używa usługi Azure Active Directory do uwierzytelniania. Przed tworzenia aplikacji współdziałająca z usługą Data Lake Storage Gen1 lub Azure Data Lake Analytics, należy określić sposób uwierzytelniania aplikacji za pomocą usługi Azure Active Directory (Azure AD). Główne dostępne są dwie opcje:

* Uwierzytelnianie użytkowników końcowych (w tym artykule)
* Service-to-service authentication (Wybierz tę opcję z listy rozwijanej powyżej)

Obie te opcje spowodować aplikacji dostarczanej z token OAuth 2.0, który jest dołączany do każdego żądania wysłanego do Gen1 programu Data Lake Storage lub Azure Data Lake Analytics.

W tym artykule zawiera informacje o sposobie tworzenia **natywną aplikację Azure AD do uwierzytelniania użytkowników końcowych**. Aby uzyskać instrukcje dotyczące konfiguracji aplikacji usługi Azure AD do uwierzytelniania service-to-service, zobacz [Service to service uwierzytelnianie za pomocą programu Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Wymagania wstępne
* Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* Identyfikator subskrypcji. Można je pobrać w witrynie Azure portal. Na przykład jest dostępny w bloku konta usługi Data Lake Storage Gen1.
  
    ![Pobierz identyfikator subskrypcji](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)

* Nazwa domeny usługi Azure AD. Można je pobrać, ustawiając kursor myszy w prawym górnym rogu witryny Azure Portal. W poniższym zrzucie ekranu, nazwa domeny jest **contoso.onmicrosoft.com**, i identyfikator GUID w nawiasach jest identyfikator dzierżawy. 
  
    ![Pobierz domena usługi AAD](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

* Twoim identyfikatorem dzierżawy platformy Azure. Aby uzyskać instrukcje dotyczące pobierania Identyfikatora dzierżawy, zobacz [uzyskanie Identyfikatora dzierżawy](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-id).

## <a name="end-user-authentication"></a>Uwierzytelnianie użytkowników końcowych
Ten mechanizm uwierzytelniania jest zalecaną metodą, jeśli chcesz, aby użytkownik końcowy, aby zalogować się do aplikacji za pomocą usługi Azure AD. Będzie uzyskiwać dostęp do zasobów platformy Azure na tym samym poziomie dostępu jako użytkownik końcowy rejestrowania w aplikacji. Użytkownikowi końcowemu musi podawać swoich poświadczeń kolei okresowo dla swojej aplikacji zachować dostęp.

Wynik o logowaniu użytkownika jest, że aplikacja otrzymuje token dostępu i token odświeżania. Token dostępu jest dołączany do każdego żądania wysłanego do programu Data Lake Storage Gen1 lub Data Lake Analytics i jest on prawidłowy dla jednej godziny domyślnie. Token odświeżania można uzyskać nowy token dostępu i jest on prawidłowy dla domyślnie do dwóch tygodni. Można użyć dwa różne podejścia dla nazwy logowania użytkownika końcowego.

### <a name="using-the-oauth-20-pop-up"></a>Przy użyciu wyskakującego okienka OAuth 2.0
Aplikację można uruchomić OAuth 2.0 autoryzacji podręczne, w którym użytkownik może wprowadzić swoje poświadczenia. Są one współpracuje również z procesu usługi Azure AD uwierzytelniania dwuskładnikowego (2FA), jeśli to konieczne. 

> [!NOTE]
> Ta metoda nie jest jeszcze obsługiwana w usłudze Azure AD Authentication Library (ADAL) dla języka Python lub Java.
> 
> 

### <a name="directly-passing-in-user-credentials"></a>Bezpośrednie przekazanie poświadczenia użytkownika
Aplikacja może bezpośrednio podania poświadczeń użytkownika do usługi Azure AD. Ta metoda działa tylko z kontami użytkowników w organizacji identyfikator; nie jest zgodny z osobiste / "live ID" kont użytkowników, w tym kont kończy się rozszerzeniem @outlook.com lub @live.com. Ponadto ta metoda nie jest zgodny z konta użytkownika, które wymagają usługi Azure AD uwierzytelniania dwuskładnikowego (2FA).

### <a name="what-do-i-need-for-this-approach"></a>Co jest potrzebne, w tym podejściu?
* Azure AD domain name. To wymaganie już znajduje się w sekcji wymagań wstępnych w tym artykule.
* Identyfikatora dzierżawy usługi Azure AD To wymaganie już znajduje się w sekcji wymagań wstępnych w tym artykule.
* Usługa Azure AD **aplikacji natywnej**
* Identyfikator aplikacji na potrzeby natywną aplikację usługi Azure AD
* Identyfikator URI przekierowania aplikacji natywnej usługi Azure AD
* Ustaw uprawnienia delegowane.


## <a name="step-1-create-an-active-directory-native-application"></a>Krok 1: Utworzyć natywną aplikację usługi Active Directory

Tworzenie i konfigurowanie natywną aplikację usługi Azure AD do uwierzytelniania użytkowników końcowych przy użyciu Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory. Aby uzyskać instrukcje, zobacz [Utwórz aplikację usługi Azure AD](../active-directory/develop/howto-create-service-principal-portal.md).

Podczas postępując zgodnie z instrukcjami w linku, upewnij się, możesz wybrać **natywnych** dla typu aplikacji, jak pokazano na poniższym zrzucie ekranu:

![Tworzenie aplikacji internetowej](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-create-native-app.png "tworzenie natywnych aplikacji")

## <a name="step-2-get-application-id-and-redirect-uri"></a>Krok 2: Uzyskiwanie Identyfikatora aplikacji i identyfikator URI przekierowania

Zobacz [uzyskiwanie Identyfikatora aplikacji](../active-directory/develop/howto-create-service-principal-portal.md#get-application-id-and-authentication-key) można pobrać identyfikator aplikacji.

Aby pobrać identyfikator URI przekierowania, wykonaj następujące czynności.

1. W witrynie Azure portal, wybierz **usługi Azure Active Directory**, kliknij przycisk **rejestracje aplikacji**, a następnie znajdź i kliknij natywną aplikację Azure AD, który został utworzony.

2. Z **ustawienia** bloku dla aplikacji, kliknij przycisk **identyfikatory URI przekierowań**.

    ![Identyfikator URI przekierowania Get](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-redirect-uri.png)

3. Skopiuj wartość wyświetlaną.


## <a name="step-3-set-permissions"></a>Krok 3: Ustawianie uprawnień

1. W witrynie Azure portal, wybierz **usługi Azure Active Directory**, kliknij przycisk **rejestracje aplikacji**, a następnie znajdź i kliknij natywną aplikację Azure AD, który został utworzony.

2. Z **ustawienia** bloku dla aplikacji, kliknij przycisk **wymagane uprawnienia**, a następnie kliknij przycisk **Dodaj**.

    ![Identyfikator klienta](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)

3. W **Dodaj dostęp do interfejsu API** bloku kliknij **wybierz interfejs API**, kliknij przycisk **usługi Azure Data Lake**, a następnie kliknij przycisk **wybierz**.

    ![Identyfikator klienta](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)
 
4.  W **Dodaj dostęp do interfejsu API** bloku kliknij **wybierz uprawnienia**, zaznacz pole wyboru, aby zapewnić **pełny dostęp do usługi Data Lake Store**, a następnie kliknij przycisk **wybierz**.

    ![Identyfikator klienta](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-3.png)

    Kliknij przycisk **Gotowe**.

5. Powtórz ostatnie dwa kroki, aby udzielić uprawnień **interfejsu API zarządzania usługami Windows Azure** także.
   
## <a name="next-steps"></a>Kolejne kroki
W tym artykule utworzono natywną aplikację usługi Azure AD i zebrane informacje potrzebne w aplikacjach klienckich można tworzyć przy użyciu zestawu .NET SDK, zestaw Java SDK, interfejsu API REST itp. Możesz teraz przejść do następujących artykułów, które omawiają najpierw uwierzytelniania w usłudze Data Lake Storage Gen1, a następnie wykonać inne operacje w sklepie za pomocą aplikacji sieci web usługi Azure AD.

* [Końcowych-użytkowników — uwierzytelnianie za pomocą programu Data Lake Storage Gen1 przy użyciu zestawu SDK języka Java](data-lake-store-end-user-authenticate-java-sdk.md)
* [Uwierzytelnianie użytkowników końcowych za pomocą programu Data Lake Storage Gen1 przy użyciu zestawu .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
* [Uwierzytelnianie użytkowników końcowych za pomocą programu Data Lake Storage Gen1 przy użyciu języka Python](data-lake-store-end-user-authenticate-python.md)
* [Uwierzytelnianie użytkowników końcowych za pomocą programu Data Lake Storage Gen1 przy użyciu interfejsu API REST](data-lake-store-end-user-authenticate-rest-api.md)

