---
title: Samouczek — Dostosowywanie interfejsu użytkownika aplikacji w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dostosować interfejs użytkownika aplikacji w usłudze Azure Active Directory B2C w witrynie Azure portal.
services: B2C
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 9c206ac7a13ea222a01cac78c447c0764f753517
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/31/2018
ms.locfileid: "50634059"
---
# <a name="tutorial-customize-the-user-interface-of-your-applications-in-azure-active-directory-b2c"></a>Samouczek: Dostosowywanie interfejsu użytkownika aplikacji w usłudze Azure Active Directory B2C

Dla bardziej powszechne doświadczeń użytkowników takie jak rejestracji, logowania i edytowania profilu, możesz użyć [wbudowane zasady](active-directory-b2c-reference-policies.md) w usłudze Azure Active Directory (Azure AD) B2C. Informacje zawarte w tym samouczku ułatwiają Dowiedz się, jak [Dostosowywanie interfejsu użytkownika (UI)](customize-ui-overview.md) tych środowisk przy użyciu własnych plików HTML i CSS.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Utwórz pliki dostosowania interfejsu użytkownika
> * Utwórz zasady rejestracji i logowania, które używa plików
> * Testowanie dostosowanego interfejsu użytkownika

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie zostało jeszcze utworzone własne [dzierżawy usługi Azure AD B2C](tutorial-create-tenant.md), utwórz ją teraz. Jeśli został on utworzony w poprzednim samouczku, można użyć istniejącej dzierżawy.

## <a name="create-customization-files"></a>Utwórz pliki dostosowania

Tworzenie konta magazynu platformy Azure i kontenerów, a następnie umieść podstawowych plików HTML i CSS w kontenerze.

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Mimo że można przechowywać pliki na wiele sposobów, w ramach tego samouczka, możesz przechowywać je w [usługi Azure Blob storage](../storage/blobs/storage-blobs-introduction.md).

1. Upewnij się, że używasz katalogu, który zawiera subskrypcję platformy Azure. Wybierz **filtr katalogów i subskrypcji** w górnym menu i wybierz katalog, który zawiera Twoją subskrypcję. Ten katalog jest inny niż ten, który zawiera dzierżawy usługi Azure B2C.

    ![Przejdź do katalogu subskrypcji](./media/tutorial-customize-ui/switch-directories.png)

2. Wybierz wszystkie usługi, w lewym górnym rogu witryny Azure portal, wyszukiwanie i wybieranie **kont magazynu**. 
3. Wybierz pozycję **Dodaj**.
4. W obszarze **grupy zasobów**, wybierz opcję **Utwórz nową**, wprowadź nazwę dla nowej grupy zasobów, a następnie kliknij przycisk **OK**.
5. Wprowadź nazwę konta magazynu. Wybrana nazwa musi być unikatowa w obrębie platformy Azure, musi mieć długość od 3 do 24 znaków oraz może zawierać tylko cyfry i małe litery.
6. Wybierz lokalizację konta magazynu, lub zaakceptuj lokalizację domyślną. 
7. Zaakceptuj pozostałe wartości domyślne, wybierz **przeglądu + Utwórz**, a następnie kliknij przycisk **Utwórz**.
8. Po utworzeniu konta magazynu, wybierz **przejdź do zasobu**.

### <a name="create-a-container"></a>Tworzenie kontenera

1. Na stronie Przegląd konta usługi storage wybierz **obiektów blob**.
2. Wybierz **kontenera**, wprowadź nazwę kontenera, wybierz pozycję **obiektów Blob (anonimowy dostęp do odczytu tylko dla obiektów blob.)**, a następnie kliknij przycisk **OK**.

### <a name="enable-cors"></a>Włączanie mechanizmu CORS

 Kod platformy Azure AD B2C w przeglądarce używa nowoczesnych i standardowego podejścia do załadowania zawartości niestandardowej z adresu URL, który określisz w zasadach. Współużytkowanie zasobów między źródłami (cors) umożliwia ograniczone zasoby na stronie sieci web, aby zażądać z innych domen.

1. Wybierz z menu **CORS**.
2. Aby uzyskać **dozwolone źródła**, **dozwolone nagłówki**, i **udostępniane nagłówki**, wprowadź `your-tenant-name.b2clogin.com`. Zastąp `your-tenant-name` nazwą dzierżawy usługi Azure AD B2C. Na przykład `fabrikam.b2clogin.com`.
3. Aby uzyskać **dozwolonych zleceń**, zaznacz zarówno pozycję `GET` i `OPTIONS`.
4. Aby uzyskać **maksymalny wiek**, wprowadź 200.

    ![Włączanie mechanizmu CORS](./media/tutorial-customize-ui/enable-cors.png)

5. Kliknij pozycję **Zapisz**.

### <a name="create-the-customization-files"></a>Tworzenie plików dostosowania

Aby dostosować interfejsu użytkownika środowiska rejestracji, możesz rozpocząć od utworzenia prostego pliku HTML i CSS. Można skonfigurować kodzie HTML w dowolny sposób ma, ale musi mieć **div** elementu z identyfikatorem `api`. Na przykład `<div id="api"></div>`. Usługa Azure AD B2C wprowadza elementy do `api` kontenera, gdy ta strona jest wyświetlana.

1. W folderze lokalnym, utwórz następujący plik i upewnij się, że zmieniasz `your-storage-account` do nazwy konta magazynu i `your-container` do nazwy kontenera, który został utworzony. Na przykład `https://store1.blob.core.windows.net/b2c/style.css`.

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>My B2C Application</title>
        <link rel="stylesheet" href="https://your-storage-account.blob.core.windows.net/your-container/style.css">
      </head>
      <body>  
        <h1>My B2C Application</h1>
        <div id="api"></div>
      </body>
    </html>
    ```

    Strony mogą być projektowane w dowolny sposób, który ma, ale **interfejsu api** div element jest wymagana dla każdego pliku dostosowania HTML, który tworzysz. 

3. Zapisz plik jako *ui.html niestandardowe*.
4. Utwórz następujące proste arkusze CSS, który centra wszystkie elementy na stronie rejestracji lub logowania, w tym elementy, które wprowadza usługi Azure AD B2C.

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center; 
    }
    .entry {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center; 
    }
    .create {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

5. Zapisz plik jako *style.css*.

### <a name="upload-the-customization-files"></a>Przekaż pliki dostosowania

W tym samouczku przechowujesz pliki, które utworzono w ramach konta magazynu, aby usługa Azure AD B2C może uzyskiwać do nich dostęp.

1. Wybierz **wszystkich usług** w lewym górnym rogu witryny Azure portal Wyszukaj i wybierz pozycję **kont magazynu**.
2. Wybierz utworzone konto magazynu, wybierz pozycję **obiektów blob**, a następnie wybierz kontener, który został utworzony.
3. Wybierz **przekazywanie**, przejdź do i wybierz *ui.html niestandardowe* pliku, a następnie kliknij przycisk **przekazywanie**.

    ![Przekaż pliki dostosowania](./media/tutorial-customize-ui/upload-blob.png)

4. Skopiuj adres URL dla pliku, który został przekazany do użycia w dalszej części tego samouczka.
5. Powtórz kroki 3 i 4 dla *style.css* pliku.

## <a name="create-a-sign-up-and-sign-in-policy"></a>Tworzenie zasad rejestracji i logowania

Wykonanie czynności opisanych w tym samouczku, musisz utworzyć aplikacji testowej i zasad rejestracji lub logowania w usłudze Azure AD B2C. Można zastosować z zasadami opisanymi w tym samouczku, aby inne środowiska użytkownika, takie jak edytowanie profilu.

### <a name="create-an-azure-ad-b2c-application"></a>Tworzenie aplikacji usługi Azure AD B2C

Za pomocą usługi Azure AD B2C odbywa się przy użyciu aplikacji utworzonej w dzierżawie. Poniższe kroki umożliwiają utworzenie aplikacji, która przekierowuje token autoryzacji, które są zwracane do [ https://jwt.ms ](https://jwt.ms).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Pamiętaj, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który zawiera Twojej dzierżawy.
3. Wybierz **wszystkich usług** w lewym górnym rogu witryny Azure portal, a następnie wyszukaj i wybierz **usługi Azure AD B2C**.
4. Wybierz **aplikacje**, a następnie wybierz pozycję **Dodaj**.
5. Wprowadź nazwę aplikacji, na przykład *testapp1*.
6. Dla **aplikacji sieci Web / interfejs API sieci Web**, wybierz opcję `Yes`, a następnie wprowadź `https://jwt.ms` dla **adres URL odpowiedzi**.
7. Kliknij pozycję **Utwórz**.

### <a name="create-the-policy"></a>Tworzenie zasad

Aby przetestować pliki dostosowań, należy utworzyć wbudowanych zasad rejestracji lub logowania, które korzystają z aplikacji, która została wcześniej utworzona.

1. W ramach dzierżawy usługi Azure AD B2C wybierz **zasady rejestracji lub logowania**, a następnie kliknij przycisk **Dodaj**.
2. Wprowadź nazwę zasad. Na przykład *signup_signin*. Prefiks *B2C_1* jest automatycznie dodawany do nazwy po utworzeniu zasad.
3. Wybierz **dostawców tożsamości**ustaw **E-mail rejestracji** konta lokalnego, a następnie kliknij przycisk **OK**.
4. Wybierz **atrybuty tworzenia konta**, wybierz atrybuty, które mają być zbierane od klienta podczas rejestracji. Na przykład ustawić **Kraj/Region**, **nazwę wyświetlaną**, i **kod pocztowy**, a następnie kliknij przycisk **OK**.
5. Wybierz **oświadczeń aplikacji**, wybierz oświadczenia, które mają być zwracane w tokenach autoryzacji wysyłanych z powrotem do aplikacji po pomyślnej rejestracji lub logowania. Na przykład wybierz **nazwę wyświetlaną**, **dostawcy tożsamości**, **kod pocztowy**, **użytkownik jest nowy** i **identyfikator obiektu użytkownika** , a następnie kliknij przycisk **OK**.
6. Wybierz **dostosowanie interfejsu użytkownika strony**, wybierz opcję **ujednolicona strona rejestracji lub logowania**, a następnie kliknij **tak** dla **Użyj niestandardowej strony**.
7. W **identyfikator URI strony niestandardowe**, wprowadź adres URL *ui.html niestandardowe* pliku zapisaną wcześniej, a następnie kliknij przycisk **OK**.
8. Kliknij pozycję **Utwórz**.

## <a name="test-the-policy"></a>Testowanie zasad

1. W ramach dzierżawy usługi Azure AD B2C wybierz **zasady rejestracji lub logowania**, a następnie wybierz utworzone wcześniej zasady. Na przykład *B2C_1_signup_signin*.
2. Upewnij się, że jest zaznaczona utworzoną aplikację **wybierz aplikację**, a następnie kliknij przycisk **Uruchom teraz**.

    ![Uruchamianie zasad rejestracji lub logowania](./media/tutorial-customize-ui/signup-signin.png)

    Powinieneś widzieć stronę podobny do poniższego przykładu z elementami, a ich tematyka na podstawie pliku CSS, który został utworzony:

    ![Wyniki zasad](./media/tutorial-customize-ui/run-now.png) 

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób:

> [!div class="checklist"]
> * Utwórz pliki dostosowania interfejsu użytkownika
> * Utwórz zasady rejestracji i logowania, które używa plików
> * Testowanie dostosowanego interfejsu użytkownika

> [!div class="nextstepaction"]
> [Dostosowywanie języka w usłudze Azure Active Directory B2C](active-directory-b2c-reference-language-customization.md)