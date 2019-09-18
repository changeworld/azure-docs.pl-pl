---
title: Dostosuj interfejs użytkownika aplikacji przy użyciu zasad niestandardowych w Azure Active Directory B2C | Microsoft Docs
description: Dowiedz się więcej o dostosowywaniu interfejsu użytkownika przy użyciu zasad niestandardowych w programie Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 88a8258a91237c7b3eadccc32a30c3fe8149eca5
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064637"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Dostosuj interfejs użytkownika aplikacji przy użyciu zasad niestandardowych w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Po wykonaniu tego artykułu będziesz mieć zasady dotyczące rejestracji i logowania przy użyciu marki i wyglądu. Dzięki Azure Active Directory B2C (Azure AD B2C) uzyskujesz niemal pełną kontrolę nad zawartością HTML i CSS prezentowaną użytkownikom. W przypadku korzystania z zasad niestandardowych można skonfigurować Dostosowywanie interfejsu użytkownika w formacie XML zamiast korzystać z formantów w Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki opisane w temacie Wprowadzenie [do zasad niestandardowych](active-directory-b2c-get-started-custom.md). Należy mieć działającą niestandardową zasadę do rejestracji i logowania przy użyciu kont lokalnych.

## <a name="page-ui-customization"></a>Dostosowywanie interfejsu użytkownika strony

Za pomocą funkcji dostosowywania interfejsu użytkownika na stronie można dostosować wygląd i działanie wszelkich zasad niestandardowych. Można także zapewnić spójność wizerunku marki i wrażeń wizualnych między aplikacją i usługą Azure AD B2C.

Oto jak to działa: Azure AD B2C uruchamia kod w przeglądarce klienta i korzysta z nowoczesnego podejścia zwanego [współużytkowaniem zasobów między źródłami (CORS)](https://www.w3.org/TR/cors/). Najpierw należy określić adres URL w zasadach niestandardowych przy użyciu dostosowanej zawartości HTML. Usługa Azure AD B2C scala elementy interfejsu użytkownika z zawartością HTML ładowaną z adresu URL, a następnie wyświetla stronę klientowi.

## <a name="create-your-html5-content"></a>Utwórz zawartość HTML5

Utwórz zawartość HTML z nazwą marki produktu w tytule.

1. Skopiuj poniższy fragment kodu HTML. Jest to poprawnie sformułowany plik HTML5 z pustym elementem o nazwie  *\<DIV ID = "\>API\> "\</DIV* znajdującym się w *\<tagach treści\>* . Ten element wskazuje, gdzie Azure AD B2C zawartość ma zostać wstawiona.

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

1. Wklej skopiowany fragment w edytorze tekstów, a następnie Zapisz plik jako *Customize-UI. html*.

> [!NOTE]
> Elementy formularza HTML zostaną usunięte ze względu na ograniczenia zabezpieczeń, jeśli używasz login.microsoftonline.com. Użyj b2clogin.com, jeśli chcesz użyć elementów formularza HTML w niestandardowej zawartości HTML. Zobacz [Korzystanie z b2clogin.com](b2clogin.md) , aby uzyskać inne korzyści.

## <a name="create-an-azure-blob-storage-account"></a>Tworzenie konta magazynu obiektów Blob platformy Azure

>[!NOTE]
> W tym artykule korzystamy z usługi Azure Blob Storage do obsługi naszej zawartości. Możesz wybrać hostowanie zawartości na serwerze sieci Web, ale należy [włączyć funkcję CORS na serwerze sieci Web](https://enable-cors.org/server.html).

Aby hostować tę zawartość HTML w usłudze BLOB Storage, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W menu **centrum** wybierz > kolejno pozycje **Nowy** > magazyn**konto magazynu**.
1. Wybierz **subskrypcję** dla konta magazynu.
1. Utwórz **grupę zasobów** lub wybierz istniejącą.
1. Wprowadź unikatową **nazwę** konta magazynu.
1. Wybierz **lokalizację geograficzną** dla swojego konta magazynu.
1. **Model wdrażania** może pozostawać **Menedżer zasobów**.
1. **Wydajność** może być **standardowa**.
1. Zmień **rodzaj konta** na **Magazyn obiektów BLOB**.
1. **Replikacja** może pozostawać **RA-GRS**.
1. **Warstwa dostępu** może pozostać **gorąca**.
1. Kliknij przycisk **Przegląd + Utwórz** , aby utworzyć konto magazynu.
    Po zakończeniu wdrażania zostanie automatycznie otwarta strona **konto magazynu** .

## <a name="create-a-container"></a>Tworzenie kontenera

Aby utworzyć kontener publiczny w usłudze BLOB Storage, wykonaj następujące czynności:

1. W obszarze **BLOB Service** w menu po lewej stronie wybierz pozycję **obiekty blob**.
1. Kliknij pozycję **+ kontener**.
1. W obszarze **Nazwa wprowadź nazwę** *root*. Może to być wybrana nazwa, na przykład *wingtiptoys*, ale w tym przykładzie używamy *elementu root* .
1. W obszarze **poziom dostępu publicznego**wybierz pozycję **obiekt BLOB**, a następnie kliknij **przycisk OK**.
1. Kliknij pozycję **root** , aby otworzyć nowy kontener.
1. Kliknij pozycję **Przekaż**.
1. Kliknij ikonę folderu obok pozycji **Wybierz plik**.
1. Przejdź do i wybierz pozycję **Customize-UI. html** utworzoną wcześniej w sekcji Dostosowywanie interfejsu użytkownika strony.
1. Jeśli chcesz przekazać do podfolderu, rozwiń pozycję **Zaawansowane** i wprowadź nazwę folderu w polu **Przekaż do folderu**.
1. Wybierz pozycję **Przekaż**.
1. Wybierz obiekt BLOB **Customize-UI. html** , który został przekazany.
1. Z prawej strony pola tekstowego **adres URL** wybierz ikonę **Kopiuj do schowka** , aby skopiować adres URL do Schowka.
1. W przeglądarce internetowej przejdź do adresu URL skopiowanego w celu zweryfikowania dostępności przekazanego obiektu BLOB. Jeśli jest niedostępny, na przykład jeśli `ResourceNotFound` wystąpi błąd, upewnij się, że typ dostępu do kontenera jest ustawiony na **obiekt BLOB**.

## <a name="configure-cors"></a>Konfigurowanie mechanizmu CORS

Skonfiguruj magazyn obiektów BLOB dla udostępniania zasobów między źródłami, wykonując następujące czynności:

1. Z menu wybierz pozycję **CORS**.
1. Dla **dozwolonych źródeł**wprowadź `https://your-tenant-name.b2clogin.com`. Zastąp `your-tenant-name` nazwą dzierżawy usługi Azure AD B2C. Na przykład `https://fabrikam.b2clogin.com`. Podczas wprowadzania nazwy dzierżawy należy używać wszystkich małych liter.
1. W przypadku **dozwolonych metod**zaznacz `GET` opcję `OPTIONS`oba i.
1. Dla **dozwolonych nagłówków**Wprowadź gwiazdkę (*).
1. W przypadku **widocznych nagłówków**Wprowadź gwiazdkę (*).
1. W obszarze **Maksymalny wiek**wprowadź 200.
1. Kliknij polecenie **Zapisz**.

## <a name="test-cors"></a>Testowanie CORS

Sprawdź, czy wszystko jest gotowe, wykonując następujące czynności:

1. Przejdź do witryny sieci Web [www.test-CORS.org](https://www.test-cors.org/) , a następnie wklej adres URL w polu **zdalny adres URL** .
1. Kliknij pozycję **Wyślij żądanie**.
    Jeśli wystąpi błąd, upewnij się, że ustawienia mechanizmu [CORS](#configure-cors) są poprawne. Może być również konieczne wyczyszczenie pamięci podręcznej przeglądarki lub otwarcie sesji przeglądania w trybie prywatnym przez naciśnięcie klawiszy Ctrl + Shift + P.

## <a name="modify-the-extensions-file"></a>Modyfikuj plik rozszerzeń

Aby skonfigurować dostosowanie interfejsu użytkownika, skopiuj **ContentDefinition** i jego elementy podrzędne z pliku podstawowego do pliku rozszerzeń.

1. Otwórz podstawowy plik zasad. Na przykład *`SocialAndLocalAccounts/`*****`TrustFrameworkBase.xml`. Jest to jeden z plików zasad uwzględnionych w pakiecie startowym zasad niestandardowych, który powinien zostać uzyskany w wymaganiu wstępnym, [Rozpocznij od zasad niestandardowych](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom).
1. Wyszukaj i Skopiuj całą zawartość elementu **ContentDefinitions** .
1. Otwórz plik rozszerzenia. Na przykład *TrustFrameworkExtensions. XML*. Wyszukaj element **BuildingBlocks** . Jeśli element nie istnieje, Dodaj go.
1. Wklej całą zawartość elementu **ContentDefinitions** , który został skopiowany jako element podrzędny elementu **BuildingBlocks** .
1. Wyszukaj element **ContentDefinition** , który zawiera `Id="api.signuporsignin"` kod XML, który został skopiowany.
1. Zmień wartość **LoadUri** na adres URL pliku HTML, który został przekazany do magazynu. Na przykład `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.

    Zasady niestandardowe powinny wyglądać następująco:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

1. Zapisz plik rozszerzeń.

## <a name="upload-your-updated-custom-policy"></a>Przekazywanie zaktualizowanych zasad niestandardowych

1. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **platforma obsługi tożsamości**.
1. Kliknij pozycję **wszystkie zasady**.
1. Kliknij pozycję **Przekaż zasady**.
1. Przekaż wcześniej zmieniony plik rozszerzeń.

## <a name="test-the-custom-policy-by-using-run-now"></a>Testowanie zasad niestandardowych za pomocą polecenia **Uruchom teraz**

1. Na stronie **Azure AD B2C** przejdź do pozycji **wszystkie zasady**.
1. Wybierz przekazane zasady niestandardowe i kliknij przycisk **Uruchom teraz** .
1. Należy mieć możliwość rejestrowania się przy użyciu adresu e-mail.

## <a name="reference"></a>Tematy pomocy

### <a name="sample-templates"></a>Przykładowe szablony
Przykładowe szablony do dostosowywania interfejsu użytkownika można znaleźć tutaj:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

Folder sample_templates/Wingtip zawiera następujące pliki HTML:

| Szablon HTML5 | Opis |
|----------------|-------------|
| *phonefactor.html* | Użyj tego pliku jako szablonu strony usługi uwierzytelniania wieloskładnikowego. |
| *resetpassword.html* | Użyj tego pliku jako szablonu na stronie zapomnianego hasła. |
| *selfasserted.html* | Użyj tego pliku jako szablonu dla strony rejestracji konta społecznościowego, strony rejestracji konta lokalnego lub strony logowania do konta lokalnego. |
| *unified.html* | Użyj tego pliku jako szablonu ujednoliconej strony rejestracji lub logowania. |
| *updateprofile.html* | Użyj tego pliku jako szablonu strony aktualizacji profilu. |

Poniżej przedstawiono procedurę korzystania z przykładu:

1. Sklonuj repozytorium na komputerze lokalnym. Wybierz folder szablonu w obszarze sample_templates. Można użyć `wingtip` lub `contoso`.
1. Przekaż wszystkie pliki w `css`folderach, i `fonts` `images` do magazynu obiektów blob, zgodnie z opisem w poprzednich sekcjach.
1. Następnie otwórz każdy \*plik HTML w katalogu głównym `wingtip` lub `contoso` (w zależności od tego, który został wybrany w pierwszym kroku) i Zastąp wszystkie wystąpienia "http://localhost" adresami URL plików CSS, obrazów i czcionek przekazanych w kroku 2.
1. Zapisz pliki \*. html i przekaż je do magazynu obiektów BLOB.
1. Teraz zmodyfikuj plik rozszerzeń, jak wspomniano wcześniej w [Modyfikuj plik rozszerzeń](#modify-the-extensions-file).
1. Jeśli widzisz brakujące czcionki, obrazy lub CSS, Sprawdź odwołania w zasadach rozszerzeń i \*plikach. html.

### <a name="content-definition-ids"></a>Identyfikatory definicji zawartości

W sekcji Modyfikuj swoją rejestrację lub logowanie w ramach zasad niestandardowych skonfigurowano definicję zawartości dla programu `api.idpselections`. Pełny zestaw identyfikatorów definicji zawartości, które są rozpoznawane przez strukturę programu Azure AD B2C Identity Experience i ich opisy, znajdują się w poniższej tabeli:

| Identyfikator definicji zawartości | Opis |
|-----------------------|-------------|
| *api.error* | **Strona błędu**. Ta strona jest wyświetlana po napotkaniu wyjątku lub błędu. |
| *API. idpselections* | **Strona wyboru dostawcy tożsamości**. Ta strona zawiera listę dostawców tożsamości, z których użytkownik może wybrać podczas logowania. Te opcje są dostawcami tożsamości przedsiębiorstwa, dostawcami tożsamości społecznościowych, takimi jak Facebook, Google + lub kontami lokalnymi. |
| *API. idpselections. signup* | **Wybór dostawcy tożsamości na potrzeby rejestracji**. Ta strona zawiera listę dostawców tożsamości, z których użytkownik może wybierać podczas rejestracji. Te opcje są dostawcami tożsamości przedsiębiorstwa, dostawcami tożsamości społecznościowych, takimi jak Facebook, Google + lub kontami lokalnymi. |
| *api.localaccountpasswordreset* | **Zapomniane hasło strony**. Ta strona zawiera formularz, który użytkownik musi wykonać, aby zainicjować Resetowanie hasła.  |
| *api.localaccountsignin* | **Strona logowania do konta lokalnego**. Ta strona zawiera formularz logowania służący do logowania się przy użyciu konta lokalnego na podstawie adresu e-mail lub nazwy użytkownika. Formularz może zawierać pole wprowadzania tekstu i pole wprowadzania hasła. |
| *api.localaccountsignup* | **Strona rejestracji w ramach konta lokalnego**. Ta strona zawiera formularz rejestracji na potrzeby rejestracji w celu utworzenia konta lokalnego na podstawie adresu e-mail lub nazwy użytkownika. Formularz może zawierać różne kontrolki wprowadzania, takie jak pole wprowadzania tekstu, pole wprowadzania hasła, przycisk radiowy, pola rozwijane z pojedynczym wybieraniem i pola wyboru z wieloma zaznaczeniami. |
| *api.phonefactor* | **Strona uwierzytelniania**wieloskładnikowego. Na tej stronie użytkownicy mogą weryfikować numery telefonów (za pomocą tekstu lub głosu) podczas rejestracji lub logowania. |
| *api.selfasserted* | **Strona rejestracji konta społecznościowego**. Ta strona zawiera formularz rejestracji, który użytkownicy muszą ukończyć podczas rejestrowania się przy użyciu istniejącego konta od dostawcy tożsamości społecznościowej, takiego jak Facebook lub Google +. Ta strona jest podobna do poprzedniej strony rejestracji konta społecznościowego, z wyjątkiem pól wprowadzania hasła. |
| *api.selfasserted.profileupdate* | **Strona aktualizacji profilu**. Ta strona zawiera formularz, za pomocą którego użytkownicy mogą aktualizować swój profil. Ta strona jest podobna do strony rejestracji konta społecznościowego, z wyjątkiem pól wprowadzania hasła. |
| *api.signuporsignin* | **Ujednolicona Strona rejestracji lub logowania**. Ta strona obsługuje zarówno rejestrację, jak i Logowanie użytkowników, którzy mogą korzystać z dostawców tożsamości przedsiębiorstwa, dostawców tożsamości społecznościowych, takich jak Facebook, Google + lub kont lokalnych.  |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat elementów interfejsu użytkownika, które można dostosować, zobacz [Przewodnik referencyjny dotyczący dostosowywania interfejsu użytkownika dla zasad wbudowanych](active-directory-b2c-reference-ui-customization.md).
