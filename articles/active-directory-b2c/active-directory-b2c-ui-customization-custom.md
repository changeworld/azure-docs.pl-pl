---
title: Dostosowywanie interfejsu użytkownika za pomocą niestandardowych zasad w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Więcej informacji na temat dostosowywania interfejsu użytkownika (UI), gdy użycie zasad niestandardowych w usłudze Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 3a8d797793b0e54df112ec44cdb37cbb1b40ce86
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "34710145"
---
# <a name="azure-active-directory-b2c-configure-ui-customization-in-a-custom-policy"></a>Usługa Azure Active Directory B2C: Konfigurowanie dostosowywania interfejsu użytkownika w zasadach niestandardowych

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Po zakończeniu pracy w tym artykule, konieczne będzie rejestracji i logowania zasady niestandardowe marki i wyglądu. Z usługi Azure Active Directory B2C (Azure AD B2C), możesz uzyskać prawie pełną kontrolę nad zawartość HTML i CSS, które są prezentowane użytkownikom. Użycie zasad niestandardowych, należy skonfigurować dostosowywania interfejsu użytkownika w XML, zamiast za pomocą formantów w portalu Azure. 

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy wykonać [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md). Powinien mieć pracy niestandardowych zasad rejestracji i logowania z kontami lokalnymi.

## <a name="page-ui-customization"></a>Dostosowywanie interfejsu użytkownika strony

Za pomocą funkcji dostosowywania interfejsu użytkownika strony, można dostosować wygląd i działanie dowolne zasady niestandardowe. Można również utrzymać marki i wizualne spójności między aplikacji i usługi Azure AD B2C.

Oto jak to działa: usługi Azure AD B2C kod w przeglądarce klienta, korzysta z podejścia nowoczesnych o nazwie [udostępniania zasobów między źródłami (CORS)](http://www.w3.org/TR/cors/). Najpierw należy określić adres URL w zasadach niestandardowych z dostosowana zawartość HTML. Usługa Azure AD B2C scala elementy interfejsu użytkownika z zawartość HTML, który jest ładowany z danego adresu URL, a następnie wyświetla strony do klienta.

## <a name="create-your-html5-content"></a>Tworzenie sieci HTML5 zawartości

Tworzenie zawartości o nazwie markę produktu HTML w tytule.

1. Skopiuj poniższy fragment kodu HTML. Jest poprawnie sformułowanym HTML5 z pustego elementu o nazwie *\<div id = "interfejsu api"\>\</DIV\>* znajdujących się w granicach *\<treści\>* tagów. Ten element wskazuje, gdzie jest zawartość usługi Azure AD B2C do wstawienia.

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

   >[!NOTE]
   >Ze względów bezpieczeństwa użycie JavaScript jest obecnie zablokowany do dostosowania.

2. Wklej skopiowane fragment kodu w edytorze tekstu, a następnie zapisz plik jako *dostosować ui.html*.

## <a name="create-an-azure-blob-storage-account"></a>Tworzenie konta magazynu obiektów Blob platformy Azure

>[!NOTE]
> W tym artykule używamy magazynu obiektów Blob platformy Azure do hostowania zawartość. Użytkownik może udostępnić zawartość na serwerze sieci web, ale należy [włączenia CORS na serwerze sieci web](https://enable-cors.org/server.html).

Aby hostować tę zawartość HTML w magazynie obiektów Blob, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Na **Centrum** menu, wybierz opcję **nowy** > **magazynu** > **konta magazynu**.
3. Wprowadź unikatową **nazwa** dla konta magazynu.
4. **Model wdrażania** może pozostawać **Resource Manager**.
5. Zmień **rodzaj konta** do **magazynu obiektów Blob**.
6. **Wydajność** może pozostawać **standardowe**.
7. **Replikacja** może pozostawać **RA-GRS**.
8. **Warstwa dostępu** może pozostawać **gorąca**.
9. **Szyfrowanie usługi Magazyn** może pozostawać **wyłączone**.
10. Wybierz **subskrypcji** dla konta magazynu.
11. Utwórz **grupy zasobów** lub wybierz istniejący.
12. Wybierz **lokalizacji geograficznej** dla konta magazynu.
13. Kliknij pozycję **Utwórz**, aby utworzyć konto magazynu.  
    Po zakończeniu wdrożenia **konta magazynu** automatycznie zostanie otwarty blok.

## <a name="create-a-container"></a>Tworzenie kontenera

Aby utworzyć publicznego kontenera w magazynie obiektów Blob, wykonaj następujące czynności:

1. Kliknij przycisk **omówienie** kartę.
2. Kliknij przycisk **kontenera**.
3. Aby uzyskać **nazwa**, typ **$root**.
4. Ustaw **dostęp typu** do **obiektu Blob**.
5. Kliknij przycisk **$root** można otworzyć nowego kontenera.
6. Kliknij pozycję **Przekaż**.
7. Kliknij ikonę folderu **wybierz plik**.
8. Przejdź do **dostosować ui.html**, który został wcześniej utworzony w [dostosowywania interfejsu użytkownika strony](#the-page-ui-customization-feature) sekcji.
9. Kliknij pozycję **Przekaż**.
10. Wybierz ui.html Dostosowywanie obiektów blob, który został przekazany.
11. Obok pozycji **adres URL**, kliknij przycisk **kopiowania**.
12. W przeglądarce Wklej skopiowany adres URL, a następnie przejdź do witryny. Jeśli witryna jest niedostępny, upewnij się, że typ dostępu do kontenera ustawiono **obiektu blob**.

## <a name="configure-cors"></a>Konfigurowanie mechanizmu CORS

Konfigurowanie magazynu obiektów Blob do udostępniania zasobów między źródłami w następujący sposób:

>[!NOTE]
>Czy chcesz wypróbować dostosowywanie funkcji interfejsu użytkownika za pomocą naszej próbki kodu HTML i CSS zawartości? Przygotowaliśmy [Narzędzie Pomocnik proste](active-directory-b2c-reference-ui-customization-helper-tool.md) który przekazuje i konfiguruje zawartość przykładowej na koncie magazynu obiektów Blob. Jeśli używasz narzędzia, przejdź do [zmodyfikować zasady niestandardowe rejestracji i logowania](#modify-your-sign-up-or-sign-in-custom-policy).

1. Na **magazynu** bloku, w obszarze **ustawienia**, otwórz **CORS**.
2. Kliknij pozycję **Add** (Dodaj).
3. Aby uzyskać **dozwolone źródła**, wpisz znak gwiazdki (\*).
4. W **dozwolonych zleceń** listy rozwijanej, wybierz **UZYSKAĆ** i **opcje**.
5. Aby uzyskać **dozwolone nagłówki**, wpisz znak gwiazdki (\*).
6. Aby uzyskać **widoczne nagłówki**, wpisz znak gwiazdki (\*).
7. Aby uzyskać **maksymalny wiek (w sekundach)**, typ **200**.
8. Kliknij pozycję **Add** (Dodaj).

## <a name="test-cors"></a>Test CORS

Sprawdź, czy wszystko jest gotowe, wykonując następujące czynności:

1. Przejdź do [www.test-cors.org](http://www.test-cors.org/) witryny sieci Web, a następnie wklej adres URL w **zdalnego adresu URL** pole.
2. Kliknij przycisk **wysłać żądania**.  
    Jeśli wystąpi błąd, upewnij się, że Twoje [ustawień specyfikacji CORS](#configure-cors) są poprawne. Może być również konieczne Wyczyść pamięć podręczną przeglądarki lub otworzyć sesji przeglądania w trybie prywatnym, naciskając klawisze Ctrl + Shift + P.

## <a name="modify-your-sign-up-or-sign-in-custom-policy"></a>Modyfikowanie zasad niestandardowych rejestracji i logowania

Najwyższego poziomu *\<TrustFrameworkPolicy\>* tagu, należy odnaleźć *\<BuildingBlocks\>* tagu. W ramach *\<BuildingBlocks\>* Dodaj tagi, *\<ContentDefinitions\>* tag przez skopiowanie w poniższym przykładzie. Zastąp *your_storage_account* z nazwą konta magazynu.

  ```xml
  <BuildingBlocks>
    <ContentDefinitions>
      <ContentDefinition Id="api.idpselections">
        <LoadUri>https://{your_storage_account}.blob.core.windows.net/customize-ui.html</LoadUri>
        <DataUri>urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0</DataUri>
      </ContentDefinition>
    </ContentDefinitions>
  </BuildingBlocks>
  ```

## <a name="upload-your-updated-custom-policy"></a>Przekaż zaktualizowany zasad niestandardowych

1. W [portalu Azure](https://portal.azure.com), [przełącznika w kontekście dzierżawy usługi Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md), a następnie otwórz **usługi Azure AD B2C** bloku.
2. Kliknij przycisk **wszystkich zasad**.
3. Kliknij przycisk **przekazywać zasady**.
4. Przekaż `SignUpOrSignin.xml` z *\<ContentDefinitions\>* tag, który wcześniej został dodany.

## <a name="test-the-custom-policy-by-using-run-now"></a>Testowanie zasad niestandardowych przy użyciu **Uruchom teraz**

1. Na **usługi Azure AD B2C** bloku, przejdź do **wszystkich zasad**.
2. Wybierz zasady niestandardowe przekazywane i kliknij przycisk **Uruchom teraz** przycisku.
3. Należy zalogowanie przy użyciu adresu e-mail.

## <a name="reference"></a>Informacje ogólne

Przykładowe szablonów można znaleźć do dostosowania interfejsu użytkownika w tym miejscu:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

Folder sample_templates/wingtip zawiera następujące pliki HTML:

| Szablon HTML5 | Opis |
|----------------|-------------|
| *phonefactor.html* | Użyj tego pliku jako szablonu dla strony uwierzytelniania wieloskładnikowego. |
| *resetpassword.html* | Użyj tego pliku jako szablonu dla nie pamiętasz hasła strony. |
| *selfasserted.html* | Użyj tego pliku jako szablonu dla kont społecznościowych stronę tworzenia konta, stronę tworzenia konta lokalnego konta lub stronę logowania konta lokalnego. |
| *unified.html* | Użyj tego pliku jako szablonu ujednoliconego strony rejestracji lub logowania. |
| *updateprofile.html* | Użyj tego pliku jako szablonu strony aktualizacji profilu. |

W [zmodyfikować sekcji rejestracji i logowania zasady niestandardowe](#modify-your-sign-up-or-sign-in-custom-policy), skonfigurowany zawartości definicji `api.idpselections`. Pełny zestaw zawartości identyfikatorów definicji, które są rozpoznawane w ramach obsługi tożsamości usługi Azure AD B2C i ich opisy znajdują się w poniższej tabeli:

| Identyfikator definicji zawartości | Opis | 
|-----------------------|-------------|
| *api.error* | **Strona błędu**. Ta strona jest wyświetlana po napotkaniu wyjątku lub wystąpił błąd. |
| *API.idpselections* | **Strona wyboru dostawcy tożsamości**. Ta strona zawiera listę dostawców tożsamości, które użytkownik może wybrać podczas logowania. Te opcje są enterprise dostawców tożsamości, dostawców tożsamości społecznościowych, takich jak Facebook i Google + lub kont lokalnych. |
| *API.idpselections.Signup* | **Wybór dostawcy tożsamości dla rejestracji**. Ta strona zawiera listę dostawców tożsamości, które użytkownik może wybrać podczas tworzenia konta. Te opcje są enterprise dostawców tożsamości, dostawców tożsamości społecznościowych, takich jak Facebook i Google + lub kont lokalnych. |
| *api.localaccountpasswordreset* | **Nie pamiętasz hasła strony**. Ta strona zawiera formularz, który użytkownik należy wykonać, aby zainicjować resetowania hasła.  |
| *api.localaccountsignin* | **Strona logowania konta lokalnego**. Ta strona zawiera formularz logowania dla logowania przy użyciu konta lokalnego, która jest oparta na adres e-mail lub nazwę użytkownika. Formularz może zawierać pola do wprowadzania tekstu, a w polu wprowadzania hasła. |
| *API.localaccountsignup* | **Stronę tworzenia konta lokalnego konta**. Ta strona zawiera formularz zapisów do skorzystania z konta lokalnego, która jest oparta na adres e-mail lub nazwę użytkownika. Formularz może zawierać różne kontrolki wejściowe, takich jak pola do wprowadzania tekstu, pole wprowadzania hasła przycisk radiowy, jednokrotnym zaznaczeniem pola listy rozwijanej i pól wyboru wielokrotnego wyboru. |
| *api.phonefactor* | **Strona uwierzytelniania wieloskładnikowego**. Na tej stronie użytkownicy mogą sprawdzić swoje numery telefonów (przy użyciu tekstowych lub głosowych) podczas tworzenia konta lub logowania. |
| *api.selfasserted* | **Strony rejestracji społecznościowych konta**. Ta strona zawiera wypełnieniu formularza, który użytkownicy muszą wykonać podczas logowania przy użyciu istniejącego konta od dostawcy tożsamości społecznościowych, takich jak Facebook lub Google +. Ta strona jest podobny do poprzedniego konta społecznościowych stronę tworzenia konta, z wyjątkiem pól wprowadzania hasła. |
| *api.selfasserted.profileupdate* | **Strona aktualizacji profilu**. Ta strona zawiera formularz, który użytkownicy mogą używać do aktualizacji profilu. Ta strona jest podobna do strony rejestracji społecznościowych konto, z wyjątkiem pól wprowadzania hasła. |
| *api.signuporsignin* | **Ujednolicone stronę tworzenia konta lub logowania**. Ta strona obsługuje zarówno rejestracji i logowania użytkowników, którzy można używać w organizacji dostawcy tożsamości, dostawców tożsamości społecznościowych, takich jak Facebook lub Google + lub kont lokalnych.  |

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać dodatkowe informacje na temat elementy interfejsu użytkownika, które można dostosowywać, zobacz [Podręcznik do dostosowania interfejsu użytkownika dla zasad wbudowany](active-directory-b2c-reference-ui-customization.md).
