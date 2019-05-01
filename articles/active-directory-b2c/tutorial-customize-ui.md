---
title: Samouczek — Dostosowywanie interfejsu użytkownika środowiska — Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dostosować interfejs użytkownika aplikacji w usłudze Azure Active Directory B2C w witrynie Azure portal.
services: B2C
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: c2a84bf72ab68937224ac93bd9ffd035e32c603d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702542"
---
# <a name="tutorial-customize-the-interface-of-user-experiences-in-azure-active-directory-b2c"></a>Samouczek: Dostosowywanie interfejsu użytkownika środowiska w usłudze Azure Active Directory B2C

Dla bardziej powszechne doświadczeń użytkowników takie jak rejestracji, logowania i edytowania profilu, możesz użyć [przepływy użytkownika](active-directory-b2c-reference-policies.md) w usłudze Azure Active Directory (Azure AD) B2C. Informacje zawarte w tym samouczku ułatwiają Dowiedz się, jak [Dostosowywanie interfejsu użytkownika (UI)](customize-ui-overview.md) tych środowisk przy użyciu własnych plików HTML i CSS.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Utwórz pliki dostosowania interfejsu użytkownika
> * Aktualizacja przepływu użytkownika do użycia plików
> * Testowanie dostosowanego interfejsu użytkownika

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

[Utwórz przepływ użytkownika](tutorial-create-user-flows.md) aby użytkownicy mogli zarejestrować się i zaloguj się do aplikacji.

## <a name="create-customization-files"></a>Utwórz pliki dostosowania

Tworzenie konta magazynu platformy Azure i kontenerów, a następnie umieść podstawowych plików HTML i CSS w kontenerze.

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Mimo że można przechowywać pliki na wiele sposobów, w ramach tego samouczka, możesz przechowywać je w [usługi Azure Blob storage](../storage/blobs/storage-blobs-introduction.md).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Upewnij się, że używasz katalogu, który zawiera subskrypcję platformy Azure. Wybierz **filtr katalogów i subskrypcji** w górnym menu i wybierz katalog, który zawiera Twoją subskrypcję. Ten katalog jest inny niż ten, który zawiera dzierżawy usługi Azure B2C.
3. Wybierz wszystkie usługi, w lewym górnym rogu witryny Azure portal, wyszukiwanie i wybieranie **kont magazynu**. 
4. Wybierz pozycję **Dodaj**.
5. W obszarze **grupy zasobów**, wybierz opcję **Utwórz nową**, wprowadź nazwę dla nowej grupy zasobów, a następnie kliknij przycisk **OK**.
6. Wprowadź nazwę konta magazynu. Wybrana nazwa musi być unikatowa w obrębie platformy Azure, musi mieć długość od 3 do 24 znaków oraz może zawierać tylko cyfry i małe litery.
7. Wybierz lokalizację konta magazynu, lub zaakceptuj lokalizację domyślną. 
8. Zaakceptuj pozostałe wartości domyślne, wybierz **przeglądu + Utwórz**, a następnie kliknij przycisk **Utwórz**.
9. Po utworzeniu konta magazynu, wybierz **przejdź do zasobu**.

### <a name="create-a-container"></a>Tworzenie kontenera

1. Na stronie Przegląd konta usługi storage wybierz **obiektów blob**.
2. Wybierz **kontenera**, wprowadź nazwę kontenera, wybierz pozycję **obiektów Blob (anonimowy dostęp do odczytu tylko dla obiektów blob.)**, a następnie kliknij przycisk **OK**.

### <a name="enable-cors"></a>Włączanie mechanizmu CORS

 Kod platformy Azure AD B2C w przeglądarce używa nowoczesnych i standardowego podejścia można załadować niestandardowej zawartości z adresu URL, który określisz w przepływu użytkownika. Współużytkowanie zasobów między źródłami (cors) umożliwia ograniczone zasoby na stronie sieci web, aby zażądać z innych domen.

1. Wybierz z menu **CORS**.
2. Aby uzyskać **dozwolone źródła**, wprowadź `https://your-tenant-name.b2clogin.com`. Zastąp `your-tenant-name` nazwą dzierżawy usługi Azure AD B2C. Na przykład `https://fabrikam.b2clogin.com`. Należy używać małych liter, wprowadzając nazwę dzierżawy.
3. Aby uzyskać **dozwolone metody**, zaznacz zarówno pozycję `GET` i `OPTIONS`.
4. Aby uzyskać **dozwolone nagłówki**, wprowadź znak gwiazdki (*).
5. Aby uzyskać **udostępniane nagłówki**, wprowadź znak gwiazdki (*).
6. Aby uzyskać **maksymalny wiek**, wprowadź 200.

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

## <a name="update-the-user-flow"></a>Aktualizacja przepływu użytkownika

1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
2. Wybierz **przepływy użytkownika (zasady)**, a następnie wybierz pozycję *B2C_1_signupsignin1* przepływu użytkownika.
3. Wybierz **strony układów**, a następnie w obszarze **ujednolicona strona rejestracji lub logowania**, kliknij przycisk **tak** dla **niestandardową zawartość strony**.
4. W **identyfikator URI strony niestandardowe**, wprowadź identyfikator URI *ui.html niestandardowe* pliku, który zapisaną wcześniej.
5. W górnej części strony wybierz **Zapisz**.

## <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. W ramach dzierżawy usługi Azure AD B2C wybierz **przepływy użytkownika** i wybierz *B2C_1_signupsignin1* przepływu użytkownika.
2. W górnej części strony kliknij **uruchomić przepływ użytkownika**.
3. Kliknij przycisk **uruchomić przepływ użytkownika** przycisku.

    ![Uruchamianie przepływu rejestracji lub logowania użytkownika](./media/tutorial-customize-ui/run-user-flow.png)

    Powinieneś widzieć stronę podobny do poniższego przykładu z elementami, a ich tematyka na podstawie pliku CSS, który został utworzony:

    ![Wyniki przepływu użytkownika](./media/tutorial-customize-ui/run-now.png) 

## <a name="next-steps"></a>Kolejne kroki

W tym artykule zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz pliki dostosowania interfejsu użytkownika
> * Aktualizacja przepływu użytkownika do użycia plików
> * Testowanie dostosowanego interfejsu użytkownika

> [!div class="nextstepaction"]
> [Dostosowywanie języka w usłudze Azure Active Directory B2C](active-directory-b2c-reference-language-customization.md)
