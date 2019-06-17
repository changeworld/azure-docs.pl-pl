---
title: Dostosowywanie interfejsu użytkownika aplikacji za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o dostosowywaniu interfejsu użytkownika za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c3c97e786e2147f043a63b90b886e01eb5944cb4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66507672"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Dostosowywanie interfejsu użytkownika aplikacji za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Po zakończeniu pracy w tym artykule, konieczne będzie rejestracji i logowania zasad niestandardowych swoją markę i wyglądu. Usługa Azure Active Directory B2C (Azure AD B2C), możesz szybciej niemal pełną kontrolę nad zawartość HTML i CSS, które są prezentowane użytkownikom. Użycie zasad niestandardowych, należy skonfigurować dostosowywania interfejsu użytkownika w XML, zamiast korzystać z kontrolek w witrynie Azure portal. 

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md). Należy mieć zasady niestandardowe pracy, zarejestruj się i zaloguj się przy użyciu konta lokalnego.

## <a name="page-ui-customization"></a>Dostosowywanie interfejsu użytkownika strony

Za pomocą funkcji dostosowywania interfejsu użytkownika strony, można dostosować wygląd i działanie żadnych zasad niestandardowych. Można także zapewnić spójność wizerunku marki i wrażeń wizualnych między aplikacją i usługą Azure AD B2C.

Poniżej przedstawiono, jak to działa: Usługa Azure AD B2C kodu w przeglądarce klienta, korzysta z nowoczesnego podejścia o nazwie [udostępniania zasobów między źródłami (CORS)](https://www.w3.org/TR/cors/). Najpierw należy określić adres URL w zasadach niestandardowych z dostosowaną zawartość HTML. Usługa Azure AD B2C scala elementy interfejsu użytkownika z zawartością HTML ładowaną z adresu URL, a następnie wyświetla stronę klientowi.

## <a name="create-your-html5-content"></a>Utwórz swoje HTML5 zawartości

Utwórz HTML zawartość Nazwa marki produktu w tytule.

1. Skopiuj poniższy fragment kodu HTML. Jest poprawnie sformułowanym HTML5 za pomocą pustego elementu o nazwie *\<identyfikatora DZIEL = "interfejs api"\>\</DIV\>* znajdującymi się w *\<treści\>* tagów. Ten element wskazuje, gdzie ma zostać wstawiony zawartości usługi Azure AD B2C.

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

2. Wklej skopiowany fragment kodu w edytorze tekstów, a następnie zapisz plik jako *dostosować ui.html*.

## <a name="create-an-azure-blob-storage-account"></a>Tworzenie konta magazynu obiektów Blob platformy Azure

>[!NOTE]
> W tym artykule używamy usługi Azure Blob storage do hostowania naszej zawartości. Można wybrać do hostowania zawartości na serwerze sieci web, ale należy [Włączanie mechanizmu CORS na serwerze sieci web](https://enable-cors.org/server.html).

Aby hostować tę zawartość HTML w usłudze Blob storage, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Na **Centrum** menu, wybierz opcję **New** > **magazynu** > **konta magazynu**.
3. Wprowadź unikatową **nazwa** konta magazynu.
4. **Model wdrażania** może pozostać **usługi Resource Manager**.
5. Zmiana **rodzaju konta** do **magazynu obiektów Blob**.
6. **Wydajność** może pozostać **standardowa**.
7. **Replikacja** może pozostać **RA-GRS**.
8. **Warstwa dostępu** może pozostać **gorąca**.
9. **Szyfrowanie usługi Storage** może pozostać **wyłączone**.
10. Wybierz **subskrypcji** konta magazynu.
11. Tworzenie **grupy zasobów** lub wybierz istniejącą grupę.
12. Wybierz **lokalizacji geograficznej** konta magazynu.
13. Kliknij pozycję **Utwórz**, aby utworzyć konto magazynu.  
    Po zakończeniu wdrożenia **konta magazynu** automatycznie zostanie otwarty blok.

## <a name="create-a-container"></a>Tworzenie kontenera

Aby utworzyć publicznego kontenera w usłudze Blob storage, wykonaj następujące czynności:

1. Kliknij przycisk **Przegląd** kartę.
2. Kliknij przycisk **kontenera**.
3. Aby uzyskać **nazwa**, typ **$root**.
4. Ustaw **dostęp typu** do **Blob**.
5. Kliknij przycisk **$root** można otworzyć nowego kontenera.
6. Kliknij pozycję **Przekaż**.
7. Kliknij ikonę folderu **wybierz plik**.
8. Przejdź do **dostosować ui.html**, której wcześniej utworzoną w sekcji dotyczącej dostosowywania interfejsu użytkownika strony.
9. Kliknij pozycję **Przekaż**.
10. Wybierz obiekt blob Dostosuj ui.html, który został przekazany.
11. Obok pozycji **adresu URL**, kliknij przycisk **kopiowania**.
12. W przeglądarce Wklej skopiowany adres URL, a następnie przejdź do witryny. Jeśli witryna jest niedostępny, upewnij się, że typ dostępu do kontenera, jest ustawiony na **blob**.

## <a name="configure-cors"></a>Konfigurowanie mechanizmu CORS

Konfigurowanie magazynu obiektów Blob na potrzeby udostępniania zasobów między źródłami, wykonując następujące czynności:

1. Wybierz z menu **CORS**.
2. Aby uzyskać **dozwolone źródła**, wprowadź `https://your-tenant-name.b2clogin.com`. Zastąp `your-tenant-name` nazwą dzierżawy usługi Azure AD B2C. Na przykład `https://fabrikam.b2clogin.com`. Należy używać małych liter, wprowadzając nazwę dzierżawy.
3. Aby uzyskać **dozwolone metody**, zaznacz zarówno pozycję `GET` i `OPTIONS`.
4. Aby uzyskać **dozwolone nagłówki**, wprowadź znak gwiazdki (*).
5. Aby uzyskać **udostępniane nagłówki**, wprowadź znak gwiazdki (*).
6. Aby uzyskać **maksymalny wiek**, wprowadź 200.
7. Kliknij pozycję **Zapisz**.

## <a name="test-cors"></a>Test CORS

Sprawdź, czy wszystko jest gotowe, wykonując następujące czynności:

1. Przejdź do [www.test-cors.org](https://www.test-cors.org/) witryny sieci Web, a następnie wklej adres URL w **zdalnego adresu URL** pole.
2. Kliknij przycisk **Wyślij żądanie**.  
    Jeśli otrzymasz komunikat o błędzie, upewnij się, że Twoje [ustawienia specyfikacji CORS](#configure-cors) są poprawne. Może być również konieczne wyczyszczenie pamięci podręcznej przeglądarki lub otwórz sesję przeglądania w trybie prywatnym, naciskając klawisze Ctrl + Shift + P.

## <a name="modify-the-extensions-file"></a>Zmodyfikuj plik rozszerzenia

Aby skonfigurować dostosowywania interfejsu użytkownika, należy skopiować **ContentDefinition** i jego elementy podrzędne z pliku podstawowego pliku rozszerzenia.

1. Otwórz plik podstawowy zasad. Na przykład *TrustFrameworkBase.xml*.
2. Wyszukaj, a następnie skopiować całą zawartość **ContentDefinitions** elementu.
3. Otwórz plik rozszerzenia. Na przykład *TrustFrameworkExtensions.xml*. Wyszukaj **BuildingBlocks** elementu. Jeśli element nie istnieje, należy go dodać.
4. Wklej całą zawartość **ContentDefinitions** element, który został skopiowany jako element podrzędny elementu **BuildingBlocks** elementu. 
5. Wyszukaj **ContentDefinition** element, który zawiera `Id="api.signuporsignin"` w formacie XML, który został skopiowany.
6. Zmień wartość właściwości **parametr LoadUri** do adresu URL pliku HTML, który został przekazany do magazynu. Na przykład `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.
    
    Niestandardowe zasady powinny wyglądać następująco:

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

7. Zapisz plik rozszerzenia.

## <a name="upload-your-updated-custom-policy"></a>Przekaż zaktualizowany zasad niestandardowych

1. Upewnij się, że używasz katalogu zawierającego Twoją dzierżawę usługi Azure AD B2C, klikając pozycję **Filtr katalogu i subskrypcji** w górnym menu i wybierając katalog zawierający Twoją dzierżawę.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **struktura środowiska tożsamości**.
2. Kliknij przycisk **wszystkie zasady**.
3. Kliknij przycisk **przekazywać zasady**.
4. Przekaż plik rozszerzenia, który wcześniej został zmodyfikowany.

## <a name="test-the-custom-policy-by-using-run-now"></a>Testowanie zasad niestandardowych przy użyciu **Uruchom teraz**

1. Na **usługi Azure AD B2C** przejdź do bloku **wszystkie zasady**.
2. Wybierz zasady niestandardowe, które przekazane i kliknij przycisk **Uruchom teraz** przycisku.
3. Powinien móc zarejestrować się przy użyciu adresu e-mail.

## <a name="reference"></a>Tematy pomocy

Dostosowywanie interfejsu użytkownika w tym miejscu można znaleźć przykładowe szablony:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

Folder sample_templates/wingtip zawiera następujące pliki HTML:

| Szablon języka HTML5 | Opis |
|----------------|-------------|
| *phonefactor.html* | Użyj tego pliku jako szablon dla strony uwierzytelniania wieloskładnikowego. |
| *resetpassword.html* | Użyj tego pliku jako szablon dla strona obsługi zapomnianego hasła. |
| *selfasserted.html* | Użyj tego pliku jako szablon dla strony rejestracji konta w sieci społecznościowej, strona rejestracji dla kont lokalnych lub strony logowania konta lokalnego. |
| *unified.html* | Użyj tego pliku jako szablon dla ujednoliconego strony rejestracji lub logowania. |
| *updateprofile.html* | Użyj tego pliku jako szablon dla strony aktualizacji profilu. |

W Modyfikuj sekcji niestandardowe zasady rejestracji lub logowania jest konfigurowany definicji zawartości dla `api.idpselections`. Pełny zestaw zawartości identyfikatorów definicji, które są rozpoznawane przez platformy środowiska tożsamości usługi Azure AD B2C i ich opisy znajdują się w poniższej tabeli:

| Identyfikator definicji zawartości | Opis | 
|-----------------------|-------------|
| *api.error* | **Strona błędu**. Ta strona jest wyświetlana, gdy występuje wyjątek lub komunikat o błędzie. |
| *api.idpselections* | **Strona wyboru dostawcy tożsamości**. Ta strona zawiera listę dostawców tożsamości, które użytkownik może wybrać z podczas logowania. Opcje te są dostawców tożsamości w organizacji, dostawców tożsamości społecznościowych, takich jak Facebook i Google + lub kont lokalnych. |
| *api.idpselections.signup* | **Wybór dostawcy tożsamości dla rejestracji**. Ta strona zawiera listę dostawców tożsamości, które użytkownik może wybierać podczas rejestracji. Opcje te są dostawców tożsamości w organizacji, dostawców tożsamości społecznościowych, takich jak Facebook i Google + lub kont lokalnych. |
| *api.localaccountpasswordreset* | **Strona obsługi zapomnianego hasła**. Ta strona zawiera formularz, który użytkownik musi wykonać, aby zainicjować resetowania hasła.  |
| *api.localaccountsignin* | **Strona logowania dla kont lokalnych**. Ta strona zawiera formularz logowania logujesz się przy użyciu konta lokalnego, który jest oparty na adres e-mail lub nazwę użytkownika. Formularz może zawierać pola wprowadzania tekstu, a pole wprowadzania hasła. |
| *api.localaccountsignup* | **Strona rejestracji dla kont lokalnych**. Ta strona zawiera formularz zapisów za utworzenie konta lokalnego, który jest oparty na adres e-mail lub nazwę użytkownika. Formularz może zawierać różne kontrolki wejściowe, takie jak pola wprowadzania tekstu, pole wprowadzania hasła, przycisk radiowy, wybieranych list rozwijanych i pól wyboru wielokrotnego wyboru. |
| *api.phonefactor* | **Strona uwierzytelniania wieloskładnikowego**. Na tej stronie użytkownicy mogą sprawdzić swoje numery telefonów (przy użyciu tekstowych lub głosowych) podczas tworzenia konta lub logowania. |
| *api.selfasserted* | **Strona rejestracji dla kont społecznościowych**. Ta strona zawiera formularz rejestracji, które użytkownicy muszą wykonać po utworzeniu konta przy użyciu istniejącego konta z dostawcy tożsamości społecznościowych, takich jak Facebook lub Google +. Ta strona jest podobny do poprzedniego konta społecznościowego zapisywania strony, z wyjątkiem pól wprowadzania hasła. |
| *api.selfasserted.profileupdate* | **Strona aktualizacji profilu**. Ta strona zawiera formularza, którego użytkownicy mogą zaktualizować swój profil. Ta strona jest podobna do strony rejestracji konta w sieci społecznościowej, z wyjątkiem pól wprowadzania hasła. |
| *api.signuporsignin* | **Ujednolicona strona rejestracji lub logowania**. Ta strona obsługuje zarówno rejestracji i logowania użytkowników, którzy mogą korzystać z dostawców tożsamości organizacji, dostawców tożsamości społecznościowych, takich jak Facebook lub Google + lub kont lokalnych.  |

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać dodatkowe informacje na temat elementów interfejsu użytkownika, które można dostosować, zobacz [Przewodnik dostosowywania interfejsu użytkownika dla wbudowanych zasad](active-directory-b2c-reference-ui-customization.md).
