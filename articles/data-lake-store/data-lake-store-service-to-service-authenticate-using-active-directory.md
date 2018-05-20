---
title: 'Do usługi uwierzytelniania: usługi Data Lake Store w usłudze Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak wykonać uwierzytelnianie usługi do usługi z usługi Data Lake Store za pomocą usługi Azure Active Directory
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
ms.openlocfilehash: 7a4c94b5279962d9bf5524270c12e0c1ec512e3c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="service-to-service-authentication-with-data-lake-store-using-azure-active-directory"></a>Aby usługa uwierzytelniania za pomocą usługi Data Lake Store za pomocą usługi Azure Active Directory
> [!div class="op_single_selector"]
> * [Uwierzytelnianie użytkowników końcowych](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Uwierzytelnianie między usługami](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
>  

Azure Data Lake Store używa usługi Azure Active Directory do uwierzytelniania. Przed tworzenia aplikacji, która współdziała z usługi Azure Data Lake Store, należy określić sposób uwierzytelniania aplikacji z usługą Azure Active Directory (Azure AD). Główne dostępne są dwie opcje:

* Uwierzytelnianie użytkowników końcowych 
* Do usługi uwierzytelniania (w tym artykule) 

Obie te opcje spowodować aplikacji dostarczanej z token OAuth 2.0, który pobiera dołączony do każdego żądania skierowane do usługi Azure Data Lake Store.

Ten artykuł zawiera informacje o sposobie tworzenia **aplikacji sieci web usługi Azure AD do usługi uwierzytelniania**. Aby uzyskać instrukcje dotyczące konfiguracji aplikacji usługi Azure AD do uwierzytelniania użytkowników końcowych, zobacz [uwierzytelniania użytkowników końcowych za pomocą usługi Data Lake Store za pomocą usługi Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Wymagania wstępne
* Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-create-an-active-directory-web-application"></a>Krok 1: Tworzenie aplikacji sieci web usługi Active Directory

Tworzenie i konfigurowanie aplikacji sieci web usługi Azure AD do usługi uwierzytelniania z usługi Azure Data Lake Store za pomocą usługi Azure Active Directory. Aby uzyskać instrukcje, zobacz [tworzy aplikację usługi Azure AD](../azure-resource-manager/resource-group-create-service-principal-portal.md).

Podczas następujące instrukcje poprzedni element, należy koniecznie wybrać **aplikacji sieci Web / interfejs API** dla typu aplikacji, jak pokazano na poniższym zrzucie ekranu:

![Tworzenie aplikacji sieci web](./media/data-lake-store-authenticate-using-active-directory/azure-active-directory-create-web-app.png "tworzenie aplikacji sieci web")

## <a name="step-2-get-application-id-authentication-key-and-tenant-id"></a>Krok 2: Uzyskaj identyfikator aplikacji, klucz uwierzytelniania i Identyfikatora dzierżawcy
Podczas logowania programowo, potrzebny jest identyfikator aplikacji. Jeśli aplikacja jest uruchamiana własne poświadczenia, należy także klucz uwierzytelniania.

* Aby uzyskać instrukcje dotyczące pobierania klucz identyfikator i uwierzytelniania aplikacji (nazywane także klucz tajny klienta) dla aplikacji, zobacz [klucz identyfikator i uwierzytelniania aplikacji Get](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key).

* Aby uzyskać instrukcje, jak uzyskać identyfikator dzierżawy, zobacz [uzyskanie Identyfikatora dzierżawy](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).

## <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-store-account-file-or-folder"></a>Krok 3: Przypisz aplikację usługi Azure AD do usługi Azure Data Lake Store konta pliku lub folderu


1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Otwórz konta usługi Azure Data Lake Store, które chcesz skojarzyć z aplikacją usługi Azure Active Directory, utworzony wcześniej.
2. W bloku konta usługi Data Lake Store kliknij pozycję **Eksplorator danych**.
   
    ![Tworzenie katalogów w ramach konta usługi Data Lake Store](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "Tworzenie katalogów w ramach konta usługi Data Lake")
3. W **Eksploratora danych** bloku, kliknij plik lub folder, dla którego chcesz zapewnić dostęp do aplikacji usługi Azure AD, a następnie kliknij przycisk **dostępu**. Aby skonfigurować dostęp do pliku, należy kliknąć opcję **dostępu** z **podglądu pliku** bloku.
   
    ![Ustaw listy kontroli dostępu w systemie plików usługi Data Lake](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "Ustaw listy kontroli dostępu w systemie plików usługi Data Lake")
4. **Dostępu** bloku wymieniono dostępu standardowego i niestandardowego dostępu już przypisany do katalogu głównego. Kliknij przycisk **Dodaj** ikonę, aby dodać poziom niestandardowy listy kontroli dostępu.
   
    ![Lista dostępu standardowe i niestandardowe](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "listy dostępu standardowe i niestandardowe")
5. Kliknij przycisk **Dodaj** ikonę, aby otworzyć **dodać niestandardowe dostępu** bloku. W tym bloku, kliknij przycisk **Wybieranie użytkownika lub grupy**, a następnie w **Wybieranie użytkownika lub grupy** bloku, poszukaj wcześniej utworzoną aplikację usługi Azure Active Directory. Jeśli masz wiele grup wyszukiwanie od, użyj pola tekstowego u góry, aby odfiltrować nazwę grupy. Kliknij grupę, w której chcesz dodać, a następnie kliknij przycisk **wybierz**.
   
    ![Dodaj grupę](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "Dodaj grupę")
6. Kliknij przycisk **wybierz uprawnienia**, wybierz uprawnienia i czy chcesz przypisać uprawnienia domyślnie listy ACL, dostępu do listy ACL, lub obie. Kliknij przycisk **OK**.
   
    ![Przypisywanie uprawnień do grupy](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "przypisywanie uprawnień do grupy")
   
    Aby uzyskać więcej informacji na temat uprawnień w usłudze Data Lake Store i dostęp do i domyślnej listy kontroli dostępu, zobacz [kontroli dostępu w usłudze Data Lake Store](data-lake-store-access-control.md).
7. W **dodać niestandardowe dostępu** bloku, kliknij przycisk **OK**. Nowo dodana grupa, z skojarzone uprawnienia są wymienione w **dostępu** bloku.
   
    ![Przypisywanie uprawnień do grupy](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "przypisywanie uprawnień do grupy")

> [!NOTE]
> Jeśli planujesz ograniczanie aplikacji usługi Azure Active Directory do określonego folderu, należy również do udzielenia tej samej aplikacji usługi Azure Active directory **Execute** permisison do katalogu głównego, aby umożliwić dostęp do tworzenia plików za pomocą. ZESTAW SDK SIECI.

> [!NOTE]
> Jeśli chcesz korzystać z zestawów SDK, aby utworzyć konto usługi Data Lake Store, należy przypisać do grupy zasobów, w którym konto usługi Data Lake Store można utworzyć aplikacji sieci web usługi Azure AD jako rola.
> 
>

## <a name="step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications"></a>Krok 4: Pobrać punktu końcowego tokenu OAuth 2.0 (tylko dla aplikacji opartych na języku Java)

1. Zaloguj się [portalu Azure](https://portal.azure.com) i kliknij polecenie usługi Active Directory w okienku po lewej stronie.

2. W okienku po lewej stronie kliknij **rejestracji aplikacji**.

3. W górnej części bloku rejestracji aplikacji, kliknij polecenie **punkty końcowe**.

    ![Punkt końcowy tokenu OAuth](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint.png "końcowym tokenów OAuth")

4. Z listy punktów końcowych skopiuj punktu końcowego tokenu OAuth 2.0.

    ![Punkt końcowy tokenu OAuth](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint-1.png "końcowym tokenów OAuth")   

## <a name="next-steps"></a>Kolejne kroki
W tym artykule utworzyć aplikację sieci web platformy Azure AD i zebranych informacji w aplikacjach klienckich tworzyć przy użyciu zestawu SDK programu .NET, Java, Python, interfejsu API REST,... itd. Możesz teraz przejść do następujące artykuły, które porozmawiać na temat sposobu używania aplikacji natywnej usługi Azure AD do najpierw uwierzytelnić się z usługą Data Lake Store i wykonywać inne operacje w magazynie.

* [Do usługi uwierzytelniania za pomocą usługi Data Lake Store za pomocą języka Java](data-lake-store-service-to-service-authenticate-java.md)
* [Aby usługa uwierzytelniania za pomocą przy użyciu zestawu .NET SDK usługi Data Lake Store](data-lake-store-service-to-service-authenticate-net-sdk.md)
* [Aby usługa uwierzytelniania za pomocą Data Lake Store z użyciem języka Python](data-lake-store-service-to-service-authenticate-python.md)
* [Usługa do uwierzytelniania przy użyciu interfejsu API REST usługi Data Lake Store](data-lake-store-service-to-service-authenticate-rest-api.md)


