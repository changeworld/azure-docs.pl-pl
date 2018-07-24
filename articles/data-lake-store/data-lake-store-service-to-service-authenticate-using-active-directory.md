---
title: 'Service-to-service authentication: Data Lake Store za pomocą usługi Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak osiągnąć usługi do uwierzytelniania za pomocą usługi Data Lake Store przy użyciu usługi Azure Active Directory
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
ms.openlocfilehash: 1e59ed093417d8761135b946e2fa3f183bb085c9
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2018
ms.locfileid: "39215975"
---
# <a name="service-to-service-authentication-with-data-lake-store-using-azure-active-directory"></a>Service to service Authentication with Data Lake Store przy użyciu usługi Azure Active Directory uwierzytelnianie
> [!div class="op_single_selector"]
> * [Uwierzytelnianie użytkowników końcowych](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Uwierzytelnianie między usługami](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
>  

Azure Data Lake Store używa usługi Azure Active Directory do uwierzytelniania. Przed tworzenia aplikacji, która współdziała z usługi Azure Data Lake Store, musisz zdecydować, jak do uwierzytelniania aplikacji z usługą Azure Active Directory (Azure AD). Główne dostępne są dwie opcje:

* Uwierzytelnianie użytkowników końcowych 
* Service-to-service authentication (w tym artykule) 

Obie te opcje spowodować aplikacji dostarczanej z token OAuth 2.0, który jest dołączany do każdego żądania wysłanego do usługi Azure Data Lake Store.

W tym artykule zawiera informacje o sposobie tworzenia **aplikacji sieci web usługi Azure AD do uwierzytelniania service to service**. Aby uzyskać instrukcje dotyczące konfiguracji aplikacji usługi Azure AD do uwierzytelniania użytkowników końcowych, zobacz [End-user Authentication with Data Lake Store przy użyciu usługi Azure Active Directory uwierzytelnianie](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Wymagania wstępne
* Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-create-an-active-directory-web-application"></a>Krok 1: Tworzenie aplikacji sieci web usługi Active Directory

Utwórz i skonfiguruj aplikację sieci web usługi Azure AD do usługi uwierzytelniania za pomocą usługi Azure Data Lake Store, za pomocą usługi Azure Active Directory. Aby uzyskać instrukcje, zobacz [Utwórz aplikację usługi Azure AD](../azure-resource-manager/resource-group-create-service-principal-portal.md).

Podczas zgodnie z instrukcjami w powyższym linkiem, upewnij się, możesz wybrać **aplikacji sieci Web / interfejs API** dla typu aplikacji, jak pokazano na poniższym zrzucie ekranu:

![Tworzenie aplikacji internetowej](./media/data-lake-store-authenticate-using-active-directory/azure-active-directory-create-web-app.png "tworzenie aplikacji internetowej")

## <a name="step-2-get-application-id-authentication-key-and-tenant-id"></a>Krok 2: Uzyskanie Identyfikatora aplikacji, klucz uwierzytelniania i Identyfikatora dzierżawy
Podczas programowego logowania, potrzebujesz Identyfikatora aplikacji. Jeśli aplikacja zostanie uruchomiona w ramach własnej poświadczeń, należy również klucza uwierzytelniania.

* Aby uzyskać instrukcje dotyczące sposobu pobierania aplikacji Identyfikatora i klucza uwierzytelniania (nazywane również klucz tajny klienta) dla aplikacji, zobacz [Get aplikacji Identyfikatora i klucza uwierzytelniania](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key).

* Aby uzyskać instrukcje dotyczące pobierania Identyfikatora dzierżawy, zobacz [uzyskanie Identyfikatora dzierżawy](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).

## <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-store-account-file-or-folder"></a>Krok 3: Przypisywanie aplikacji usługi Azure AD do pliku konta usługi Azure Data Lake Store lub folderu


1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Otwórz konto usługi Azure Data Lake Store, który chcesz skojarzyć z aplikacją usługi Azure Active Directory, która została utworzona wcześniej.
2. W bloku konta usługi Data Lake Store kliknij pozycję **Eksplorator danych**.
   
    ![Tworzenie katalogów w ramach konta Data Lake Store](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "Tworzenie katalogów w ramach konta usługi Data Lake")
3. W **Eksplorator danych** bloku, kliknij plik lub folder, dla którego chcesz zapewnić dostęp do aplikacji usługi Azure AD, a następnie kliknij przycisk **dostępu**. Aby skonfigurować dostęp do pliku, należy kliknąć przycisk **dostępu** z **Podgląd pliku** bloku.
   
    ![Ustaw listy kontroli dostępu w systemie plików usługi Data Lake](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "Ustaw listy kontroli dostępu w systemie plików usługi Data Lake")
4. **Dostępu** bloku wymieniono dostęp standardowy i niestandardowy dostęp już przypisany do katalogu głównego. Kliknij przycisk **Dodaj** ikonę, aby dodać listy ACL Poziom niestandardowy.
   
    ![Listę standardowych i niestandardowych dostępu](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "dostępu do standardowych i niestandardowych List")
5. Kliknij przycisk **Dodaj** ikonę, aby otworzyć **dodać niestandardowego dostępu** bloku. W tym bloku kliknij **Wybieranie użytkownika lub grupy**, a następnie w polu **Wybieranie użytkownika lub grupy** bloku, zwróć uwagę na wcześniej utworzoną aplikację usługi Azure Active Directory. Jeśli masz wiele grup rozpocznie się wyszukiwanie od, użyj pola tekstowego u góry, do filtrowania przesyłanej nazwy grupy. Kliknij grupę, którą chcesz dodać, a następnie kliknij przycisk **wybierz**.
   
    ![Dodaj grupę](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "Dodaj grupę")
6. Kliknij przycisk **wybierz uprawnienia**, wybierz uprawnienia i czy chcesz przypisać uprawnienia jako domyślne listy ACL dostępu do listy ACL i / lub. Kliknij przycisk **OK**.
   
    ![Przypisywanie uprawnień do grupy](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "przypisywanie uprawnień do grupy")
   
    Aby uzyskać więcej informacji na temat uprawnień w Data Lake Store i dostęp do i domyślnej listy ACL, zobacz [kontroli dostępu w Data Lake Store](data-lake-store-access-control.md).
7. W **dodać niestandardowego dostępu** bloku kliknij **OK**. Nowo dodana grupa, przy użyciu skojarzonych z nimi uprawnień są wymienione w **dostępu** bloku.
   
    ![Przypisywanie uprawnień do grupy](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "przypisywanie uprawnień do grupy")

> [!NOTE]
> Jeśli planujesz ograniczenia do konkretnego folderu aplikacji usługi Azure Active Directory, również należy podać tej samej aplikacji usługi Azure Active directory **Execute** uprawnienia do katalogu głównego w celu umożliwienia dostępu do tworzenia plików za pomocą platformy .NET ZESTAW SDK.

> [!NOTE]
> Jeśli chcesz korzystać z zestawów SDK do utworzenia konta Data Lake Store, należy przypisać aplikacji sieci web usługi Azure AD jako rolę do grupy zasobów, w którym opisano tworzenie konta Data Lake Store.
> 
>

## <a name="step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications"></a>Krok 4: Pobieranie punktu końcowego tokenu OAuth 2.0 (tylko w przypadku aplikacji opartych na języku Java)

1. Zaloguj się na [witryny Azure portal](https://portal.azure.com) i kliknij opcję usługi Active Directory z okienka po lewej stronie.

2. W okienku po lewej stronie kliknij **rejestracje aplikacji**.

3. W górnej części bloku rejestracje aplikacji, kliknij **punktów końcowych**.

    ![Punkt końcowy tokenu OAuth](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint.png "punkt końcowy tokenu OAuth")

4. Z listy punktów końcowych skopiuj punkt końcowy tokenu OAuth 2.0.

    ![Punkt końcowy tokenu OAuth](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint-1.png "punkt końcowy tokenu OAuth")   

## <a name="next-steps"></a>Kolejne kroki
W tym artykule utworzono aplikację sieci web usługi Azure AD i zebranych informacji potrzebnych w aplikacjach klienckich można tworzyć przy użyciu zestawu SDK platformy .NET, Java, Python, interfejs API REST itp. Możesz teraz przejść do następujących artykułów, które omawiają najpierw uwierzytelniania w usłudze Data Lake Store i wykonywać inne operacje w sklepie za pomocą aplikacji natywnej usługi Azure AD.

* [Aby usługa uwierzytelniania za pomocą Data Lake Store przy użyciu języka Java](data-lake-store-service-to-service-authenticate-java.md)
* [Service to service Authentication with Data Lake Store przy użyciu zestawu .NET SDK uwierzytelnianie](data-lake-store-service-to-service-authenticate-net-sdk.md)
* [Service to service Authentication with Data Lake Store przy użyciu języka Python uwierzytelnianie](data-lake-store-service-to-service-authenticate-python.md)
* [Service to service Authentication with Data Lake Store przy użyciu interfejsu API REST uwierzytelnianie](data-lake-store-service-to-service-authenticate-rest-api.md)


