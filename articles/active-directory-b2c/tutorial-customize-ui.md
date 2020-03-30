---
title: 'Samouczek: Dostosowywanie interfejsu użytkownika'
titleSuffix: Azure AD B2C
description: Dowiedz się, jak dostosować interfejs użytkownika (UI) aplikacji w usłudze Azure Active Directory B2C przy użyciu witryny Azure portal.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b2b2bc8dd4e60348553228b8b418df252a8c426a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186254"
---
# <a name="tutorial-customize-the-interface-of-user-experiences-in-azure-active-directory-b2c"></a>Samouczek: dostosowywanie interfejsu środowiska użytkownika w usłudze Azure Active Directory B2C

Aby uzyskać bardziej typowe środowisko użytkownika, takie jak rejestracja, logowanie i edytowanie profilów, można użyć [przepływów użytkowników](user-flow-overview.md) w usłudze Azure Active Directory B2C (Azure AD B2C). Informacje zawarte w tym samouczku pomagają dowiedzieć się, jak [dostosować interfejs użytkownika (UI)](customize-ui-overview.md) tych środowisk przy użyciu własnych plików HTML i CSS.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie plików dostosowywania interfejsu użytkownika
> * Aktualizowanie przepływu użytkownika w celu korzystania z plików
> * Testowanie dostosowanego interfejsu użytkownika

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

[Utwórz przepływ użytkownika,](tutorial-create-user-flows.md) aby umożliwić użytkownikom rejestrację i zalogowanie się do aplikacji.

## <a name="create-customization-files"></a>Tworzenie plików dostosowywania

Utwórz konto i kontener magazynu platformy Azure, a następnie umieść podstawowe pliki HTML i CSS w kontenerze.

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Chociaż pliki można przechowywać na wiele sposobów, w tym samouczku można przechowywać je w [magazynie obiektów Blob platformy Azure](../storage/blobs/storage-blobs-introduction.md).

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Upewnij się, że używasz katalogu, który zawiera subskrypcję platformy Azure. Wybierz filtr **Katalog + subskrypcja** w górnym menu i wybierz katalog zawierający subskrypcję. Ten katalog różni się od tego, który zawiera dzierżawę usługi Azure B2C.
3. Wybierz pozycję Wszystkie usługi w lewym górnym rogu witryny Azure portal, wyszukaj i wybierz pozycję **Konta magazynu**.
4. Wybierz pozycję **Dodaj**.
5. W obszarze **Grupa zasobów**wybierz pozycję **Utwórz nowy**, wprowadź nazwę nowej grupy zasobów, a następnie kliknij przycisk **OK**.
6. Wprowadź nazwę konta magazynu. Wybrana nazwa musi być unikatowa w obrębie platformy Azure, musi mieć długość od 3 do 24 znaków oraz może zawierać tylko cyfry i małe litery.
7. Wybierz lokalizację konta magazynu lub zaakceptuj lokalizację domyślną.
8. Zaakceptuj wszystkie inne wartości domyślne, wybierz **pozycję Przejrzyj + utwórz**, a następnie kliknij przycisk **Utwórz**.
9. Po utworzeniu konta magazynu wybierz pozycję **Przejdź do zasobu**.

### <a name="create-a-container"></a>Tworzenie kontenera

1. Na stronie przegląd konta magazynu wybierz pozycję **Obiekty Blobs**.
2. Wybierz **opcję Kontener**, wprowadź nazwę kontenera, wybierz pozycję **Blob (anonimowy dostęp do odczytu tylko dla obiektów blob),** a następnie kliknij przycisk **OK**.

### <a name="enable-cors"></a>Włączanie mechanizmu CORS

 Kod usługi Azure AD B2C w przeglądarce używa nowoczesnego i standardowego podejścia do ładowania zawartości niestandardowej z adresu URL określonego w przepływie użytkownika. Udostępnianie zasobów między źródłami (CORS) umożliwia wymagane ograniczone zasoby na stronie sieci web z innych domen.

1. W menu wybierz **CORS**.
2. W przypadku **dozwolonych** `https://your-tenant-name.b2clogin.com`źródeł pochodzenia wprowadź . Zamień `your-tenant-name` na nazwę dzierżawy usługi Azure AD B2C. Na przykład `https://fabrikam.b2clogin.com`. Podczas wprowadzania nazwy dzierżawy należy użyć wszystkich małych liter.
3. W przypadku metod `GET` **dozwolonych** `OPTIONS`wybierz ,`PUT`i .
4. W polu **Dozwolone nagłówki**wprowadź gwiazdkę (*).
5. W polu Widoczne **nagłówki**wprowadź gwiazdkę (*).
6. Dla **maksymalnego wieku**, wprowadź 200.

    ![Strona konfiguracji usługi CORS w magazynie obiektów blob platformy Azure w witrynie Azure portal](./media/tutorial-customize-ui/enable-cors.png)

5. Kliknij przycisk **Zapisz**.

### <a name="create-the-customization-files"></a>Tworzenie plików dostosowywania

Aby dostosować interfejs użytkownika środowiska rejestracji, należy rozpocząć od utworzenia prostego pliku HTML i CSS. Kod HTML można skonfigurować w dowolny sposób, ale musi mieć element `api` **div** z identyfikatorem . Na przykład `<div id="api"></div>`. Usługa Azure AD B2C `api` wstrzykuje elementy do kontenera, gdy strona jest wyświetlana.

1. W folderze lokalnym utwórz następujący plik i `your-storage-account` upewnij się, że `your-container` zmieniono nazwę konta magazynu i nazwę utworzonego kontenera. Na przykład `https://store1.blob.core.windows.net/b2c/style.css`.

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

    Stronę można zaprojektować w dowolny sposób, ale element **div interfejsu api** jest wymagany dla każdego tworzonego pliku dostosowywania HTML.

3. Zapisz plik jako *custom-ui.html*.
4. Utwórz następujące proste CSS, który koncentruje wszystkie elementy na stronie rejestracji lub logowania, w tym elementy, które usługi Azure AD B2C wstrzykuje.

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

### <a name="upload-the-customization-files"></a>Przekazywanie plików dostosowywania

W tym samouczku przechowujesz pliki utworzone na koncie magazynu, aby usługa Azure AD B2C mogła uzyskać do nich dostęp.

1. Wybierz **pozycję Wszystkie usługi** w lewym górnym rogu witryny Azure portal, wyszukaj i wybierz pozycję Konta **magazynu**.
2. Wybierz utworzone konto magazynu, wybierz pozycję **Obiekty blob**, a następnie wybierz utworzony kontener.
3. Wybierz **pozycję Przekaż**, przejdź do pliku *custom-ui.html* i wybierz go, a następnie kliknij przycisk **Przekaż**.

    ![Przekaż stronę obiektu blob w portalu z wyróżnionym przyciskiem Przekazywanie i plikami](./media/tutorial-customize-ui/upload-blob.png)

4. Skopiuj adres URL pliku, który został przekazany do użycia w dalszej części samouczka.
5. Powtórz krok 3 i 4 dla pliku *style.css.*

## <a name="update-the-user-flow"></a>Aktualizowanie przepływu użytkownika

1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
2. Wybierz **przepływy użytkownika (zasady),** a następnie wybierz *B2C_1_signupsignin1* przepływ użytkownika.
3. Wybierz **pozycję Układy stron**, a następnie w obszarze **Ujednolicona strona rejestracji lub logowania**kliknij pozycję **Tak,** aby **użyć niestandardowej zawartości strony**.
4. W **uri strony niestandardowej**wprowadź identyfikator URI dla pliku *custom-ui.html,* który został nagrany wcześniej.
5. U góry strony wybierz pozycję **Zapisz**.

## <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. W dzierżawie usługi Azure AD B2C wybierz **przepływy użytkownika** i wybierz *B2C_1_signupsignin1* przepływ użytkownika.
2. U góry strony kliknij pozycję **Uruchom przepływ użytkownika**.
3. Kliknij przycisk **Uruchom przepływ użytkownika.**

    ![Uruchamianie strony przepływu użytkownika dla przepływu użytkownika rejestracji lub logowania](./media/tutorial-customize-ui/run-user-flow.png)

    Powinna zostać wyświetlona strona podobna do poniższego przykładu z elementami wyśrodkowanymi na podstawie utworzonego pliku CSS:

    ![Przeglądarka internetowa z witryną rejestracji lub logowania się przy elementów niestandardowego interfejsu użytkownika](./media/tutorial-customize-ui/run-now.png)

## <a name="next-steps"></a>Następne kroki

W tym artykule zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie plików dostosowywania interfejsu użytkownika
> * Aktualizowanie przepływu użytkownika w celu korzystania z plików
> * Testowanie dostosowanego interfejsu użytkownika

> [!div class="nextstepaction"]
> [Dostosowywanie języka w usłudze Azure Active Directory B2C](user-flow-language-customization.md)
