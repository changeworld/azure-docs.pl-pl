---
title: 'Uwierzytelnianie użytkowników końcowych: usługa Azure Data Lake Storage Gen1 z usługą Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak osiągnąć uwierzytelnianie użytkowników końcowych za pomocą usługi Azure Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory
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
ms.openlocfilehash: 4c2b774c304e46f9fc68f3beaf64218e614ecad1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66234054"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Uwierzytelnianie użytkowników końcowych przy użyciu usługi Azure Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory
> [!div class="op_single_selector"]
> * [Uwierzytelnianie użytkowników końcowych](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Uwierzytelnianie między usługami](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
> 

Usługa Azure Data Lake Storage Gen1 używa usługi Azure Active Directory do uwierzytelniania. Przed dokonaniem tworzenia aplikacji, która współpracuje z data lake storage gen1 lub usługi Azure Data Lake Analytics, należy zdecydować, jak uwierzytelnić aplikację za pomocą usługi Azure Active Directory (Azure AD). Dostępne są dwie główne opcje:

* Uwierzytelnianie użytkowników końcowych (ten artykuł)
* Uwierzytelnianie między usługami (wybierz tę opcję z listy rozwijanej powyżej)

Obie te opcje powodują, że aplikacja jest dostarczana z tokenem OAuth 2.0, który zostanie dołączony do każdego żądania złożonego do usługi Data Lake Storage Gen1 lub usługi Azure Data Lake Analytics.

W tym artykule o tym, jak utworzyć **natywną aplikację usługi Azure AD do uwierzytelniania użytkowników końcowych.** Aby uzyskać instrukcje dotyczące konfiguracji aplikacji usługi Azure AD do uwierzytelniania między usługami, zobacz [Uwierzytelnianie usługi do usługi za pomocą usługi Lake Storage Gen1 przy użyciu usługi Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Wymagania wstępne
* Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* Twój identyfikator subskrypcji. Można go pobrać z witryny Azure portal. Na przykład jest on dostępny z data lake magazyn gen1 bloku konta.
  
    ![Uzyskaj identyfikator subskrypcji](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)

* Twoja nazwa domeny usługi Azure AD. Można go pobrać, najeżdżając myszą w prawym górnym rogu witryny Azure portal. Na poniższym zrzucie ekranu nazwa domeny jest **contoso.onmicrosoft.com**, a identyfikator GUID w nawiasach jest identyfikatorem dzierżawy. 
  
    ![Uzyskaj domenę AAD](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

* Identyfikator dzierżawy platformy Azure. Aby uzyskać instrukcje dotyczące pobierania identyfikatora dzierżawy, zobacz [Pobieranie identyfikatora dzierżawy.](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in)

## <a name="end-user-authentication"></a>Uwierzytelnianie użytkowników końcowych
Ten mechanizm uwierzytelniania jest zalecane podejście, jeśli chcesz, aby użytkownik końcowy zalogować się do aplikacji za pośrednictwem usługi Azure AD. Aplikacja jest następnie w stanie uzyskać dostęp do zasobów platformy Azure z tym samym poziomem dostępu co użytkownik końcowy, który się zalogował. Użytkownik końcowy musi okresowo podawać swoje poświadczenia, aby aplikacja zachowała dostęp.

Wynik logowania użytkownika końcowego jest, że aplikacja otrzymuje token dostępu i token odświeżania. Token dostępu zostanie dołączony do każdego żądania złożonego do usługi Data Lake Storage Gen1 lub Data Lake Analytics i jest domyślnie ważny przez jedną godzinę. Token odświeżania może służyć do uzyskania nowego tokenu dostępu i jest domyślnie ważny przez maksymalnie dwa tygodnie. Można użyć dwóch różnych metod logowania użytkownika końcowego.

### <a name="using-the-oauth-20-pop-up"></a>Korzystanie z wyskakujących okienka OAuth 2.0
Aplikacja może wyzwolić wyskakujące okienko autoryzacji OAuth 2.0, w którym użytkownik końcowy może wprowadzić swoje poświadczenia. To wyskakujące okienko działa również z procesem uwierzytelniania dwuskładnikowego usługi Azure AD (2FA), jeśli to konieczne. 

> [!NOTE]
> Ta metoda nie jest jeszcze obsługiwana w bibliotece uwierzytelniania usługi Azure AD (ADAL) dla języka Python lub Java.
> 
> 

### <a name="directly-passing-in-user-credentials"></a>Bezpośrednie przekazywanie poświadczeń użytkownika
Aplikacja może bezpośrednio podać poświadczenia użytkownika do usługi Azure AD. Ta metoda działa tylko z kontami użytkowników identyfikatorów organizacji; nie jest kompatybilny z kontami użytkowników osobistych / "live ID", w tym z kontami kończącymi się na kontach @outlook.com lub @live.com. Ponadto ta metoda nie jest zgodna z kontami użytkowników, które wymagają uwierzytelniania dwuskładnikowego usługi Azure AD (2FA).

### <a name="what-do-i-need-for-this-approach"></a>Czego potrzebuję do takiego podejścia?
* Nazwa domeny usługi Azure AD. To wymaganie jest już wymienione w wymaganiach wstępnych tego artykułu.
* Identyfikator dzierżawy usługi Azure AD. To wymaganie jest już wymienione w wymaganiach wstępnych tego artykułu.
* **Natywna aplikacja** usługi Azure AD
* Identyfikator aplikacji dla aplikacji natywnej usługi Azure AD
* Przekierowywanie identyfikatora URI dla aplikacji natywnej usługi Azure AD
* Ustaw uprawnienia delegowane.


## <a name="step-1-create-an-active-directory-native-application"></a>Krok 1: Tworzenie aplikacji natywnej usługi Active Directory

Tworzenie i konfigurowanie aplikacji natywnej usługi Azure AD do uwierzytelniania użytkowników końcowych przy użyciu usługi Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory. Aby uzyskać instrukcje, zobacz [Tworzenie aplikacji usługi Azure AD](../active-directory/develop/howto-create-service-principal-portal.md).

Postępując zgodnie z instrukcjami zawartymi w łączu, upewnij się, że wybrano opcję **Natywna** dla typu aplikacji, jak pokazano na poniższym zrzucie ekranu:

![Tworzenie aplikacji internetowej](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-create-native-app.png "Tworzenie aplikacji natywnej")

## <a name="step-2-get-application-id-and-redirect-uri"></a>Krok 2: Uzyskaj identyfikator aplikacji i przekieruj identyfikator URI

Zobacz [Pobieranie identyfikatora aplikacji w](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in) celu pobrania identyfikatora aplikacji.

Aby pobrać identyfikator URI przekierowania, wykonaj następujące kroki.

1. W witrynie Azure Portal wybierz pozycję **Azure Active Directory**, kliknij pozycję **Rejestracje aplikacji**, a następnie znajdź i kliknij utworzoną aplikację natywną usługi Azure AD.

2. W bloku **Ustawienia** dla aplikacji kliknij pozycję **Przekieruj identyfikatory URI**.

    ![Pobierz identyfikator URI przekierowania](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-redirect-uri.png)

3. Skopiuj wyświetlaną wartość.


## <a name="step-3-set-permissions"></a>Krok 3: Ustawianie uprawnień

1. W witrynie Azure Portal wybierz pozycję **Azure Active Directory**, kliknij pozycję **Rejestracje aplikacji**, a następnie znajdź i kliknij utworzoną aplikację natywną usługi Azure AD.

2. W bloku **Ustawienia** aplikacji kliknij pozycję **Wymagane uprawnienia**, a następnie kliknij przycisk **Dodaj**.

    ![Identyfikator klienta](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)

3. W bloku **Dodaj dostęp do interfejsu API** kliknij pozycję Wybierz interfejs **API**, kliknij pozycję Usługa Azure **Data Lake**, a następnie kliknij przycisk **Wybierz**.

    ![Identyfikator klienta](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)
 
4.  W bloku **Dodaj dostęp do interfejsu API** kliknij pozycję Zaznacz **uprawnienia**, zaznacz pole wyboru, aby nadać pełny dostęp do magazynu **Data Lake**Store, a następnie kliknij przycisk **Wybierz**.

    ![Identyfikator klienta](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-3.png)

    Kliknij przycisk **Gotowe**.

5. Powtórz dwa ostatnie kroki, aby udzielić uprawnień dla **interfejsu API zarządzania usługami systemu Windows Azure.**
   
## <a name="next-steps"></a>Następne kroki
W tym artykule utworzono aplikację natywną usługi Azure AD i zebrano informacje potrzebne w aplikacjach klienckich, które są tworzone przy użyciu pliku .NET SDK, java SDK, REST API itp. Teraz można przejść do następujących artykułów, które mówią o tym, jak używać aplikacji sieci web usługi Azure AD, aby najpierw uwierzytelnić się przy użyciu usługi Data Lake Storage Gen1, a następnie wykonać inne operacje w magazynie.

* [Uwierzytelnianie użytkownika końcowego za pomocą usługi Data Lake Storage Gen1 przy użyciu zestawu Java SDK](data-lake-store-end-user-authenticate-java-sdk.md)
* [Uwierzytelnianie użytkownika końcowego przy użyciu usługi Data Lake Storage Gen1 przy użyciu sdk .NET](data-lake-store-end-user-authenticate-net-sdk.md)
* [Uwierzytelnianie użytkownika końcowego za pomocą usługi Data Lake Storage Gen1 przy użyciu języka Python](data-lake-store-end-user-authenticate-python.md)
* [Uwierzytelnianie użytkownika końcowego za pomocą usługi Data Lake Storage Gen1 przy użyciu interfejsu API REST](data-lake-store-end-user-authenticate-rest-api.md)

