---
title: 'Service-to-service authentication: Usługa Azure Data Lake Storage Gen1 za pomocą usługi Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak osiągnąć usługa Usługa uwierzytelniania za pomocą usługi Azure Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory
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
ms.openlocfilehash: 3fbf2f2540e8f1ca84aad2759b9a1fc790e4065d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66241375"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Usługa Usługa uwierzytelniania za pomocą usługi Azure Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory
> [!div class="op_single_selector"]
> * [Uwierzytelnianie użytkowników końcowych](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Uwierzytelnianie między usługami](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
>  

Azure Data Lake Storage Gen1 używa usługi Azure Active Directory do uwierzytelniania. Przed tworzenia aplikacji współdziałająca z usługą Data Lake Storage Gen1, należy określić sposób uwierzytelniania aplikacji za pomocą usługi Azure Active Directory (Azure AD). Główne dostępne są dwie opcje:

* Uwierzytelnianie użytkowników końcowych 
* Service-to-service authentication (w tym artykule) 

Obie te opcje spowodować dostarczany z token OAuth 2.0, który jest dołączany do każdego żądania wysłanego do programu Data Lake Storage Gen1 aplikacji.

W tym artykule zawiera informacje o sposobie tworzenia **aplikacji sieci web usługi Azure AD do uwierzytelniania service to service**. Aby uzyskać instrukcje dotyczące konfiguracji aplikacji usługi Azure AD do uwierzytelniania użytkowników końcowych, zobacz [uwierzytelnianie użytkowników końcowych za pomocą programu Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Wymagania wstępne
* Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-create-an-active-directory-web-application"></a>Krok 1: Tworzenie aplikacji sieci web usługi Active Directory

Tworzenie i konfigurowanie aplikacji sieci web usługi Azure AD do usługi uwierzytelniania za pomocą usługi Azure Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory. Aby uzyskać instrukcje, zobacz [Utwórz aplikację usługi Azure AD](../active-directory/develop/howto-create-service-principal-portal.md).

Podczas zgodnie z instrukcjami w powyższym linkiem, upewnij się, możesz wybrać **aplikacji sieci Web / interfejs API** dla typu aplikacji, jak pokazano na poniższym zrzucie ekranu:

![Tworzenie aplikacji internetowej](./media/data-lake-store-authenticate-using-active-directory/azure-active-directory-create-web-app.png "tworzenie aplikacji internetowej")

## <a name="step-2-get-application-id-authentication-key-and-tenant-id"></a>Krok 2: Pobierz identyfikator aplikacji, klucz uwierzytelniania i Identyfikatora dzierżawy
Podczas programowego logowania, potrzebujesz Identyfikatora aplikacji. Jeśli aplikacja zostanie uruchomiona w ramach własnej poświadczeń, należy również klucza uwierzytelniania.

* Aby uzyskać instrukcje dotyczące sposobu pobierania aplikacji Identyfikatora i klucza uwierzytelniania (nazywane również klucz tajny klienta) dla aplikacji, zobacz [Get aplikacji Identyfikatora i klucza uwierzytelniania](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).

* Aby uzyskać instrukcje dotyczące pobierania Identyfikatora dzierżawy, zobacz [uzyskanie Identyfikatora dzierżawy](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).

## <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-storage-gen1-account-file-or-folder"></a>Krok 3: Przypisz aplikację usługi Azure AD do usługi Azure Data Lake Storage Gen1 konta pliku lub folderu


1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Otwórz konto Data Lake Storage Gen1, który chcesz skojarzyć z aplikacją usługi Azure Active Directory, która została utworzona wcześniej.
2. W bloku konta usługi Data Lake Storage Gen1 kliknij **Eksplorator danych**.
   
    ![Tworzenie katalogów w ramach konta Data Lake Storage Gen1](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "Tworzenie katalogów w ramach konta usługi Data Lake")
3. W **Eksplorator danych** bloku, kliknij plik lub folder, dla którego chcesz zapewnić dostęp do aplikacji usługi Azure AD, a następnie kliknij przycisk **dostępu**. Aby skonfigurować dostęp do pliku, należy kliknąć przycisk **dostępu** z **Podgląd pliku** bloku.
   
    ![Ustaw listy kontroli dostępu w systemie plików usługi Data Lake](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "Ustaw listy kontroli dostępu w systemie plików usługi Data Lake")
4. **Dostępu** bloku wymieniono dostęp standardowy i niestandardowy dostęp już przypisany do katalogu głównego. Kliknij przycisk **Dodaj** ikonę, aby dodać listy ACL Poziom niestandardowy.
   
    ![Listę standardowych i niestandardowych dostępu](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "dostępu do standardowych i niestandardowych List")
5. Kliknij przycisk **Dodaj** ikonę, aby otworzyć **dodać niestandardowego dostępu** bloku. W tym bloku kliknij **Wybieranie użytkownika lub grupy**, a następnie w polu **Wybieranie użytkownika lub grupy** bloku, zwróć uwagę na wcześniej utworzoną aplikację usługi Azure Active Directory. Jeśli masz wiele grup rozpocznie się wyszukiwanie od, użyj pola tekstowego u góry, do filtrowania przesyłanej nazwy grupy. Kliknij grupę, którą chcesz dodać, a następnie kliknij przycisk **wybierz**.
   
    ![Dodaj grupę](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "Dodaj grupę")
6. Kliknij przycisk **wybierz uprawnienia**, wybierz uprawnienia i czy chcesz przypisać uprawnienia jako domyślne listy ACL dostępu do listy ACL i / lub. Kliknij przycisk **OK**.
   
    ![Przypisywanie uprawnień do grupy](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "przypisywanie uprawnień do grupy")
   
    Aby uzyskać więcej informacji na temat uprawnień w Data Lake Storage Gen1 i dostęp do i domyślnej listy ACL, zobacz [kontroli dostępu w Data Lake Storage Gen1](data-lake-store-access-control.md).
7. W **dodać niestandardowego dostępu** bloku kliknij **OK**. Nowo dodana grupa, przy użyciu skojarzonych z nimi uprawnień są wymienione w **dostępu** bloku.
   
    ![Przypisywanie uprawnień do grupy](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "przypisywanie uprawnień do grupy")

> [!NOTE]
> Jeśli planujesz ograniczenia do konkretnego folderu aplikacji usługi Azure Active Directory, również należy podać tej samej aplikacji usługi Azure Active directory **Execute** uprawnienia do katalogu głównego w celu umożliwienia dostępu do tworzenia plików za pomocą platformy .NET ZESTAW SDK.

> [!NOTE]
> Jeśli chcesz korzystać z zestawów SDK do utworzenia konta Data Lake Storage Gen1, należy przypisać do grupy zasobów, w którym opisano tworzenie konta Data Lake Storage Gen1 aplikacji sieci web usługi Azure AD jako rola.
> 
>

## <a name="step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications"></a>Krok 4: Pobierz punkt końcowy tokenu OAuth 2.0 (tylko w przypadku aplikacji opartych na języku Java)

1. Zaloguj się na [witryny Azure portal](https://portal.azure.com) i kliknij opcję usługi Active Directory z okienka po lewej stronie.

2. W okienku po lewej stronie kliknij **rejestracje aplikacji**.

3. W górnej części bloku rejestracje aplikacji, kliknij **punktów końcowych**.

    ![Punkt końcowy tokenu OAuth](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint.png "punkt końcowy tokenu OAuth")

4. Z listy punktów końcowych skopiuj punkt końcowy tokenu OAuth 2.0.

    ![Punkt końcowy tokenu OAuth](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint-1.png "punkt końcowy tokenu OAuth")   

## <a name="next-steps"></a>Kolejne kroki
W tym artykule utworzono aplikację sieci web usługi Azure AD i zebranych informacji potrzebnych w aplikacjach klienckich można tworzyć przy użyciu zestawu SDK platformy .NET, Java, Python, interfejs API REST itp. Możesz teraz przejść do następujących artykułów, które omawiają najpierw uwierzytelniania w usłudze Data Lake Storage Gen1, a następnie wykonać inne operacje w sklepie za pomocą aplikacji natywnej usługi Azure AD.

* [Uwierzytelnianie Service to service za pomocą programu Data Lake Storage Gen1 przy użyciu języka Java](data-lake-store-service-to-service-authenticate-java.md)
* [Uwierzytelnianie Service to service za pomocą programu Data Lake Storage Gen1 przy użyciu zestawu .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
* [Uwierzytelnianie Service to service za pomocą programu Data Lake Storage Gen1 przy użyciu języka Python](data-lake-store-service-to-service-authenticate-python.md)
* [Service-to-service uwierzytelnianie za pomocą programu Data Lake Storage Gen1 przy użyciu interfejsu API REST](data-lake-store-service-to-service-authenticate-rest-api.md)


