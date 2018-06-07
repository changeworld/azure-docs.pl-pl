---
title: 'Uwierzytelnianie użytkownika końcowego: usługi Data Lake Store w usłudze Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak wykonać uwierzytelnianie użytkownika końcowego z Data Lake Store za pomocą usługi Azure Active Directory
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 7280cd971e9857c494dfd1cb77d528e4737ed9d2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34624152"
---
# <a name="end-user-authentication-with-data-lake-store-using-azure-active-directory"></a>Uwierzytelnianie użytkownika końcowego za pomocą usługi Data Lake Store za pomocą usługi Azure Active Directory
> [!div class="op_single_selector"]
> * [Uwierzytelnianie użytkowników końcowych](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Uwierzytelnianie między usługami](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
> 

Azure Data Lake Store używa usługi Azure Active Directory do uwierzytelniania. Przed tworzenia aplikacji, która współdziała z usługi Azure Data Lake Store i usługą Azure Data Lake Analytics, można określić sposób uwierzytelniania aplikacji z usługą Azure Active Directory (Azure AD). Główne dostępne są dwie opcje:

* Uwierzytelnianie użytkownika końcowego (w tym artykule)
* Do usługi uwierzytelniania (Wybierz tę opcję z listy rozwijanej powyżej)

Obie te opcje spowodować aplikacji dostarczanej z token OAuth 2.0, który pobiera dołączony do każdego żądania kierowane do usługi Azure Data Lake Store lub usługi Azure Data Lake Analytics.

Ten artykuł zawiera informacje o sposobie tworzenia **natywnych aplikacji usługi Azure AD do uwierzytelniania użytkowników końcowych**. Aby uzyskać instrukcje dotyczące konfiguracji aplikacji usługi Azure AD do usługi uwierzytelniania, zobacz [do usługi uwierzytelniania za pomocą usługi Data Lake Store za pomocą usługi Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Wymagania wstępne
* Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* Do identyfikatora subskrypcji. Można go pobrać z portalu Azure. Na przykład jest dostępny w bloku konta usługi Data Lake Store.
  
    ![Pobierz identyfikator subskrypcji](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)

* Nazwa domeny usługi Azure AD. Można go pobrać, ustawiając kursor myszy w prawym górnym rogu portalu Azure. W poniższym zrzucie ekranu, nazwa domeny jest **contoso.onmicrosoft.com**, i identyfikator GUID w nawiasach kwadratowych jest identyfikator dzierżawcy. 
  
    ![Uzyskaj domenę usługi AAD](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

* Do identyfikatora dzierżawy usługi Azure. Aby uzyskać instrukcje, jak uzyskać identyfikator dzierżawy, zobacz [uzyskanie Identyfikatora dzierżawy](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).

## <a name="end-user-authentication"></a>Uwierzytelnianie użytkowników końcowych
Ten mechanizm uwierzytelniania jest zalecane podejście, jeśli chcesz, aby użytkownik końcowy logować się do aplikacji za pomocą usługi Azure AD. Aplikacja jest następnie uzyskiwać dostęp do zasobów platformy Azure z poziomu dostępu użytkownika końcowego rejestrującej w. Użytkownikowi końcowemu musi o podanie swoich poświadczeń okresowo w kolejności dla aplikacji do obsługi dostępu.

Wynik po logowaniu użytkownika jest nadawana aplikacji, token dostępu i token odświeżania. Token dostępu jest dołączony do każdego żądania kierowane do usługi Data Lake Store lub usługi Data Lake Analytics i okres ważności wynosi godzinę domyślnie. Token odświeżania pozwala uzyskać nowy token dostępu i jest prawidłowy, domyślnie maksymalnie dwa tygodnie. Dwa różne podejścia służy do logowania użytkownika końcowego.

### <a name="using-the-oauth-20-pop-up"></a>Za pomocą menu podręczne OAuth 2.0
Aplikacja może wyzwolić OAuth 2.0 autoryzacji wyskakujących, w którym użytkownicy mogą wprowadzić swoje poświadczenia. Są one współdziała również z procesu uwierzytelniania dwuskładnikowego Azure AD (2FA), jeśli to konieczne. 

> [!NOTE]
> Ta metoda nie jest jeszcze obsługiwana w Azure AD Authentication Library (ADAL) dla języka Python lub Java.
> 
> 

### <a name="directly-passing-in-user-credentials"></a>Przekazywanie bezpośrednio w poświadczeń użytkownika
Aplikacja może bezpośrednio podania poświadczeń użytkownika do usługi Azure AD. Ta metoda działa tylko z kontami użytkowników w organizacji identyfikator; nie jest zgodny z osobistego / "słowo live ID" kont użytkowników, włącznie z kontami kończy się rozszerzeniem @outlook.com lub @live.com. Ponadto ta metoda nie jest zgodny z kontami użytkowników, które wymagają uwierzytelniania dwuskładnikowego Azure AD (2FA).

### <a name="what-do-i-need-for-this-approach"></a>Co należy do tej metody?
* Nazwa domeny w usłudze Azure AD. To wymaganie jest wymienione w sekcji wymagań wstępnych w tym artykule.
* Identyfikator dzierżawy usługi Azure AD To wymaganie jest wymienione w sekcji wymagań wstępnych w tym artykule.
* Usługi Azure AD **aplikacji natywnej**
* Identyfikator aplikacji natywnej aplikacji usługi Azure AD
* Identyfikator URI przekierowania dla aplikacji natywnej usługi Azure AD
* Ustaw uprawnienia delegowane.


## <a name="step-1-create-an-active-directory-native-application"></a>Krok 1: Tworzenie natywnych aplikacji usługi Active Directory

Tworzenie i konfigurowanie usługi Azure AD aplikacji natywnej do uwierzytelniania użytkowników końcowych z usługi Azure Data Lake Store za pomocą usługi Azure Active Directory. Aby uzyskać instrukcje, zobacz [tworzy aplikację usługi Azure AD](../azure-resource-manager/resource-group-create-service-principal-portal.md).

Podczas zgodnie z instrukcjami zawartymi w łączu, upewnij się, że wybrano **natywnego** dla typu aplikacji, jak pokazano na poniższym zrzucie ekranu:

![Tworzenie aplikacji sieci web](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-create-native-app.png "tworzenie natywnych aplikacji")

## <a name="step-2-get-application-id-and-redirect-uri"></a>Krok 2: Uzyskiwanie Identyfikatora aplikacji i identyfikator URI przekierowania

Zobacz [uzyskiwanie Identyfikatora aplikacji](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key) można pobrać identyfikatora aplikacji.

Aby pobrać identyfikator URI przekierowania, wykonaj następujące kroki.

1. W portalu Azure wybierz **usługi Azure Active Directory**, kliknij przycisk **rejestracji aplikacji**, a następnie znajdź i kliknij utworzony aplikacji natywnej usługi Azure AD.

2. Z **ustawienia** bloku dla aplikacji, kliknij przycisk **identyfikator URI przekierowania**.

    ![Identyfikator URI przekierowania Get](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-redirect-uri.png)

3. Skopiuj wartość wyświetlana.


## <a name="step-3-set-permissions"></a>Krok 3: Ustawianie uprawnień

1. W portalu Azure wybierz **usługi Azure Active Directory**, kliknij przycisk **rejestracji aplikacji**, a następnie znajdź i kliknij utworzony aplikacji natywnej usługi Azure AD.

2. Z **ustawienia** bloku dla aplikacji, kliknij przycisk **wymagane uprawnienia**, a następnie kliknij przycisk **Dodaj**.

    ![Identyfikator klienta](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)

3. W **dodać dostępu do interfejsu API** bloku, kliknij przycisk **wybierz interfejs API**, kliknij przycisk **usługi Azure Data Lake**, a następnie kliknij przycisk **wybierz**.

    ![Identyfikator klienta](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)
 
4.  W **dodać dostępu do interfejsu API** bloku, kliknij przycisk **wybierz uprawnienia**, zaznacz pole wyboru, aby zapewnić **pełnego dostępu do usługi Data Lake Store**, a następnie kliknij przycisk **wybierz**.

    ![Identyfikator klienta](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-3.png)

    Kliknij przycisk **Gotowe**.

5. Powtórz ostatnie dwa kroki, aby udzielić uprawnień dla **interfejs API zarządzania usługami Windows Azure** również.
   
## <a name="next-steps"></a>Kolejne kroki
W tym artykule tworzone natywnych aplikacji usługi Azure AD i zebrane informacje potrzebne w aplikacjach klienckich tworzyć przy użyciu zestawu .NET SDK, zestaw SDK Java, interfejsu API REST,... itd. Możesz teraz przejść do następujące artykuły, które porozmawiać na temat sposobu korzystania z aplikacji sieci web usługi Azure AD najpierw uwierzytelnić się z usługą Data Lake Store i wykonywać inne operacje w magazynie.

* [Końcowego-użytkownikowi uwierzytelniania przy użyciu zestawu SDK Java usługi Data Lake Store](data-lake-store-end-user-authenticate-java-sdk.md)
* [Uwierzytelnianie użytkownika końcowego przy użyciu zestawu .NET SDK usługi Data Lake Store](data-lake-store-end-user-authenticate-net-sdk.md)
* [Uwierzytelnianie użytkownika końcowego za pomocą usługi Data Lake Store za pomocą języka Python](data-lake-store-end-user-authenticate-python.md)
* [Uwierzytelnianie użytkownika końcowego przy użyciu interfejsu API REST usługi Data Lake Store](data-lake-store-end-user-authenticate-rest-api.md)

