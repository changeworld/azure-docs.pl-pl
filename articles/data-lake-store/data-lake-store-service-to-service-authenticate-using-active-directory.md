---
title: 'Uwierzytelnianie usługi do usługi: usługa Azure Data Lake Storage Gen1 z usługą Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak uzyskać uwierzytelnianie między usługami za pomocą usługi Azure Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66241375"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Uwierzytelnianie usługi do usługi przy użyciu usługi Azure Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory
> [!div class="op_single_selector"]
> * [Uwierzytelnianie użytkowników końcowych](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Uwierzytelnianie między usługami](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
>  

Usługa Azure Data Lake Storage Gen1 używa usługi Azure Active Directory do uwierzytelniania. Przed dokonaniem tworzenia aplikacji, która współpracuje z data lake storage gen1, należy zdecydować, jak uwierzytelnić aplikację za pomocą usługi Azure Active Directory (Azure AD). Dostępne są dwie główne opcje:

* Uwierzytelnianie użytkowników końcowych 
* Uwierzytelnianie usługi (ten artykuł) 

Obie te opcje powodują, że aplikacja jest dostarczana z tokenem OAuth 2.0, który zostanie dołączony do każdego żądania złożonego do usługi Data Lake Storage Gen1.

W tym artykule o tym, jak utworzyć **aplikację sieci Web usługi Azure AD do uwierzytelniania usługi do usługi.** Aby uzyskać instrukcje dotyczące konfiguracji aplikacji usługi Azure AD do uwierzytelniania użytkowników końcowych, zobacz [Uwierzytelnianie użytkowników końcowych za pomocą usługi Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Wymagania wstępne
* Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-create-an-active-directory-web-application"></a>Krok 1: Tworzenie aplikacji sieci Web usługi Active Directory

Tworzenie i konfigurowanie aplikacji sieci web usługi Azure AD do uwierzytelniania usługi do usługi za pomocą usługi Azure Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory. Aby uzyskać instrukcje, zobacz [Tworzenie aplikacji usługi Azure AD](../active-directory/develop/howto-create-service-principal-portal.md).

Postępując zgodnie z instrukcjami podanymi w poprzednim łączu, upewnij się, że wybrałeś **aplikację /INTERFEJS API** dla typu aplikacji, jak pokazano na poniższym zrzucie ekranu:

![Tworzenie aplikacji internetowej](./media/data-lake-store-authenticate-using-active-directory/azure-active-directory-create-web-app.png "Tworzenie aplikacji internetowej")

## <a name="step-2-get-application-id-authentication-key-and-tenant-id"></a>Krok 2: Uzyskaj identyfikator aplikacji, klucz uwierzytelniania i identyfikator dzierżawy
Podczas programowania logowania, potrzebujesz identyfikatora dla aplikacji. Jeśli aplikacja działa pod własnymi poświadczeniami, potrzebny jest również klucz uwierzytelniania.

* Aby uzyskać instrukcje dotyczące pobierania identyfikatora aplikacji i klucza uwierzytelniania (nazywanego również kluczem tajnym klienta) dla aplikacji, zobacz [Pobieranie identyfikatora aplikacji i klucza uwierzytelniania](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).

* Aby uzyskać instrukcje dotyczące pobierania identyfikatora dzierżawy, zobacz [Pobierz identyfikator dzierżawy.](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in)

## <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-storage-gen1-account-file-or-folder"></a>Krok 3: Przypisywanie aplikacji usługi Azure AD do pliku lub folderu konta usługi Azure Data Lake Storage Gen1


1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Otwórz konto Data Lake Storage Gen1, które chcesz skojarzyć z wcześniej utworzoną aplikacją usługi Azure Active Directory.
2. W bloku konta Data Lake Storage Gen1 kliknij pozycję **Eksplorator danych**.
   
    ![Tworzenie katalogów na koncie Data Lake Storage Gen1](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "Tworzenie katalogów na koncie usługi Data Lake")
3. W bloku **Eksploratora danych** kliknij plik lub folder, dla którego chcesz zapewnić dostęp do aplikacji usługi Azure AD, a następnie kliknij pozycję **Access**. Aby skonfigurować dostęp do pliku, należy kliknąć przycisk **Dostęp** z bloku **Podgląd pliku.**
   
    ![Ustawianie list ACL w systemie plików Usługi Data Lake](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "Ustawianie list ACL w systemie plików Usługi Data Lake")
4. **Blok programu Access** zawiera listę dostępu standardowego i dostępu niestandardowego już przypisanego do katalogu głównego. Kliknij ikonę **Dodaj,** aby dodać listy ACL na poziomie niestandardowym.
   
    ![Lista dostępu standardowego i niestandardowego](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "Lista dostępu standardowego i niestandardowego")
5. Kliknij ikonę **Dodaj,** aby otworzyć blok **Dodaj dostęp niestandardowy.** W tym bloku kliknij pozycję **Wybierz użytkownika lub grupę**, a następnie w bloku **Wybierz użytkownika lub grupę** poszukaj aplikacji usługi Azure Active Directory utworzonej wcześniej. Jeśli masz wiele grup do wyszukania, użyj pola tekstowego u góry, aby filtrować nazwę grupy. Kliknij grupę, którą chcesz dodać, a następnie kliknij pozycję **Wybierz**.
   
    ![Dodawanie grupy](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "Dodawanie grupy")
6. Kliknij **pozycję Wybierz uprawnienia**, wybierz uprawnienia i czy chcesz przypisać uprawnienia jako domyślnąclCL, dostęp do listy ACL lub obu. Kliknij przycisk **OK**.
   
    ![Przypisywanie uprawnień do grupy](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "Przypisywanie uprawnień do grupy")
   
    Aby uzyskać więcej informacji na temat uprawnień w obszarze Data Lake Storage Gen1 i Default/Access ACL, zobacz [Kontrola dostępu w programie Data Lake Storage Gen1](data-lake-store-access-control.md).
7. W bloku **Dodaj dostęp niestandardowy** kliknij przycisk **OK**. Nowo dodana grupa, z skojarzonymi uprawnieniami, są wymienione w **bloku programu Access.**
   
    ![Przypisywanie uprawnień do grupy](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "Przypisywanie uprawnień do grupy")

> [!NOTE]
> Jeśli planujesz ograniczyć aplikację usługi Azure Active Directory do określonego folderu, należy również nadać tej samej aplikacji usługi Azure Active Directory **Wykonywanie** uprawnień do katalogu głównego, aby włączyć dostęp do tworzenia plików za pośrednictwem .NET SDK.

> [!NOTE]
> Jeśli chcesz użyć zestawów SDK do utworzenia konta Data Lake Storage Gen1, musisz przypisać aplikację sieci web usługi Azure AD jako rolę do grupy zasobów, w której utworzysz konto Data Lake Storage Gen1.
> 
>

## <a name="step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications"></a>Krok 4: Pobierz punkt końcowy tokenu OAuth 2.0 (tylko dla aplikacji opartych na języku Java)

1. Zaloguj się do [portalu Azure](https://portal.azure.com) i kliknij pozycję Usługa Active Directory z lewego okienka.

2. W lewym okienku kliknij pozycję **Rejestracje aplikacji**.

3. U góry bloku Rejestracje aplikacji kliknij pozycję **Punkty końcowe**.

    ![Punkt końcowy tokenu OAuth](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint.png "Punkt końcowy tokenu OAuth")

4. Z listy punktów końcowych skopiuj punkt końcowy tokenu OAuth 2.0.

    ![Punkt końcowy tokenu OAuth](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint-1.png "Punkt końcowy tokenu OAuth")   

## <a name="next-steps"></a>Następne kroki
W tym artykule utworzono aplikację sieci web usługi Azure AD i zebrano informacje potrzebne w aplikacjach klienckich, które są tworzone przy użyciu pliku .NET SDK, Java, Python, REST API itp. Teraz można przejść do następujących artykułów, które mówią o tym, jak używać aplikacji natywnej usługi Azure AD, aby najpierw uwierzytelnić się przy użyciu usługi Data Lake Storage Gen1, a następnie wykonać inne operacje w magazynie.

* [Uwierzytelnianie usługi do usługi przy użyciu usługi Lake Storage Gen1 przy użyciu języka Java](data-lake-store-service-to-service-authenticate-java.md)
* [Uwierzytelnianie usługi do usługi przy użyciu usługi Data Lake Storage Gen1 przy użyciu sdk .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
* [Uwierzytelnianie usługi do usługi przy użyciu usługi Magazyn danych Gen1 przy użyciu języka Python](data-lake-store-service-to-service-authenticate-python.md)
* [Uwierzytelnianie usługi do usługi przy użyciu usługi Lake Storage Gen1 przy użyciu interfejsu API REST](data-lake-store-service-to-service-authenticate-rest-api.md)


