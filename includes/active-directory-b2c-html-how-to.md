---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/19/2020
ms.author: mimart
ms.openlocfilehash: af11283f9e9dbd925ec994dcb1d96393332b90fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80117152"
---
## <a name="use-custom-page-content"></a>Korzystanie z niestandardowej zawartości strony

Korzystając z funkcji dostosowywania interfejsu użytkownika strony, można dostosować wygląd i działanie dowolnej zasady niestandardowej. Można także zapewnić spójność wizerunku marki i wrażeń wizualnych między aplikacją i usługą Azure AD B2C.

### <a name="how-it-works"></a>Jak to działa

Usługa Azure AD B2C uruchamia kod w przeglądarce klienta przy użyciu [udostępniania zasobów między źródłami (CORS)](https://www.w3.org/TR/cors/). W czasie wykonywania zawartość jest ładowana z adresu URL określonego w przepływie użytkownika lub zasadach niestandardowych. Każda strona w środowiska użytkownika ładuje swoją zawartość z adresu URL, który określisz dla tej strony. Po załadowaniu zawartości z adresu URL jest scalana z fragmentem HTML wstawionym przez usługę Azure AD B2C, a następnie strona jest wyświetlana klientowi.

![Niestandardowy margines zawartości strony](./media/active-directory-b2c-html-templates/html-content-merging.png)

## <a name="custom-html-page-content"></a>Niestandardowa zawartość strony HTML

Utwórz stronę HTML z własną marką, aby wyświetlać niestandardową zawartość strony. Ta strona może być `*.html` stroną statyczną lub stroną dynamiczną, taką jak .NET, Node.js lub PHP.

Niestandardowa zawartość strony może zawierać elementy HTML, w tym CSS i JavaScript, ale nie może zawierać niezabezpieczonych elementów, takich jak elementy iframe. Jedynym wymaganym elementem jest element `id` div z zestawem na `api`, taki jak ten `<div id="api"></div>` na stronie HTML.

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

### <a name="customize-the-default-azure-ad-b2c-pages"></a>Dostosowywanie domyślnych stron usługi Azure AD B2C

Zamiast tworzyć niestandardową zawartość strony od podstaw, można dostosować domyślną zawartość strony usługi Azure AD B2C.

W poniższej tabeli wymieniono domyślną zawartość strony dostarczoną przez usługę Azure AD B2C. Pobierz pliki i użyj ich jako punktu wyjścia do tworzenia własnych stron niestandardowych.

| Strona domyślna | Opis | Identyfikator definicji zawartości<br/>(tylko zasady niestandardowe) |
|:-----------------------|:--------|-------------|
| [exception.html](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Strona błędu**. Ta strona jest wyświetlana po napotkaniu wyjątku lub błędu. | *api.error* |
| [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) |  **Strona z własnym potwierdzeniem**. Użyj tego pliku jako niestandardowej zawartości strony dla strony rejestracji konta społecznościowego, strony rejestracji konta lokalnego, strony logowania do konta lokalnego, resetowania hasła i innych funkcji. Formularz może zawierać różne kontrolki wejściowe, takie jak: pole wprowadzania tekstu, pole wprowadzania hasła, przycisk opcji, pola rozwijane z pojedynczym zaznaczeniem i pola wyboru z wieloma zaznaczeniami. | *api.localaccountsignin*, *api.localaccountsignup*, *api.localaccountpasswordreset*, *api.selfasserted* |
| [multifactor-1.0.0.html](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Strona uwierzytelniania wieloskładnikowego**. Na tej stronie użytkownicy mogą zweryfikować swoje numery telefonów (za pomocą tekstu lub głosu) podczas rejestracji lub logowania. | *api.phonefactor* |
| [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Strona aktualizacji profilu**. Ta strona zawiera formularz, do który użytkownicy mogą uzyskać dostęp do aktualizacji swojego profilu. Ta strona jest podobna do strony rejestracji konta społecznościowego, z wyjątkiem pól wprowadzania hasła. | *api.selfasserted.profileupdate* |
| [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Ujednolicona strona rejestracji lub logowania**. Ta strona obsługuje proces rejestracji i logowania użytkownika. Użytkownicy mogą korzystać z dostawców tożsamości przedsiębiorstwa, dostawców tożsamości społecznościowych, takich jak Facebook, Google+, lub kont lokalnych. | *api.signuporsignin* |

## <a name="hosting-the-page-content"></a>Hostowanie zawartości strony

Podczas korzystania z własnych plików HTML i CSS, aby dostosować interfejs użytkownika, hostuj zawartość interfejsu użytkownika w dowolnym publicznie dostępnym punkcie końcowym HTTPS, który obsługuje cors. Na przykład [usługa Azure Blob storage](../articles/storage/blobs/storage-blobs-introduction.md), usługi Azure App [Services](/azure/app-service/), serwery sieci web, sieci CDN, AWS S3 lub systemy udostępniania plików.

## <a name="guidelines-for-using-custom-page-content"></a>Wskazówki dotyczące korzystania z niestandardowej zawartości strony

- Użyj bezwzględnego adresu URL, gdy do pliku HTML do pliku HTML dołączono zasoby zewnętrzne, takie jak multimedia, pliki CSS i JavaScript.
- Korzystając z [układu strony](../articles/active-directory-b2c/page-layout.md) w wersji 1.2.0 lub wyższej, można dodać `data-preload="true"` atrybut w tagach HTML, aby kontrolować kolejność ładowania CSS i JavaScript. Z `data-preload=true`, strona jest skonstruowana przed pokazano użytkownikowi. Ten atrybut pomaga zapobiec "migotaniu" strony przez wstępne ładowanie pliku CSS bez wyświetlanego użytkownikowi kodu HTML w niestylizowanym stylu. Poniższy fragment kodu HTML pokazuje użycie `data-preload` tagu.
  ```HTML
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- Zalecamy rozpoczęcie od domyślnej zawartości strony i tworzenie jej na jej podstawie.
- W zawartości niestandardowej można dołączyć obsługę JavaScript zarówno dla [przepływów użytkownika,](../articles/active-directory-b2c/user-flow-javascript-overview.md) jak i [zasad niestandardowych.](../articles/active-directory-b2c/javascript-samples.md)
- Obsługiwane wersje przeglądarki to:
  - Internet Explorer 11, 10 i Microsoft Edge
  - Ograniczona obsługa programu Internet Explorer 9 i 8
  - Google Chrome 42.0 lub nowsze
  - Mozilla Firefox 38.0 i powyżej
  - Safari dla iOS i macOS w wersji 12 i wyższej
- Ze względu na ograniczenia zabezpieczeń usługa Azure AD `frame` `iframe`B2C nie obsługuje elementów HTML ani `form` usług.

## <a name="custom-page-content-walkthrough"></a>Przewodnik po niestandardowej zawartości strony

Oto przegląd procesu:

1. Przygotowanie lokalizacji do hostowania niestandardowej zawartości strony (publicznie dostępnego punktu końcowego HTTPS z obsługą mechanizmu CORS).
1. Pobierz i dostosuj domyślny plik zawartości `unified.html`strony, na przykład .
1. Opublikuj niestandardową zawartość strony publicznie dostępnym punktem końcowym HTTPS.
1. Ustaw udostępnianie zasobów między źródłami (CORS) dla aplikacji sieci web.
1. Skieruj zasady na niestandardowy identyfikator URI zawartości zasad.

### <a name="1-create-your-html-content"></a>1. Tworzenie zawartości HTML

Utwórz niestandardową zawartość strony z nazwą marki produktu w tytule.

1. Skopiuj następujący fragment kodu HTML. Jest dobrze sformułowany HTML5 z pustym elementem o nazwie * \<\>\<div id="api" /div\> * znajdującym * \<\> * się w tagach treści. Ten element wskazuje, gdzie ma zostać wstawiona zawartość usługi Azure AD B2C.

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

1. Wklej skopiowany fragment kodu w edytorze tekstu, a następnie zapisz plik jako *customize-ui.html*.

> [!NOTE]
> Elementy formularza HTML zostaną usunięte z powodu ograniczeń zabezpieczeń, jeśli używasz login.microsoftonline.com. Jeśli chcesz używać elementów formularza HTML w niestandardowej zawartości HTML, [użyj b2clogin.com](../articles/active-directory-b2c/b2clogin.md).

### <a name="2-create-an-azure-blob-storage-account"></a>2. Tworzenie konta magazynu obiektów Blob platformy Azure

W tym artykule używamy usługi Azure Blob storage do hostowania naszej zawartości. Można wybrać hostowanie zawartości na serwerze www, ale należy [włączyć cors na serwerze www](https://enable-cors.org/server.html).

Aby hostować zawartość HTML w magazynie obiektów Blob, wykonaj następujące czynności:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. W menu **Centrum** wybierz pozycję **Nowe** > **konto****magazynu** > .
1. Wybierz **subskrypcję** dla swojego konta magazynu.
1. Utwórz **grupę zasobów** lub wybierz istniejącą.
1. Wprowadź unikatową **nazwę** konta magazynu.
1. Wybierz **lokalizację geograficzną** konta magazynu.
1. **Model wdrażania** może pozostać **Menedżerem zasobów**.
1. **Wydajność** może pozostać **standardowa**.
1. Zmień **rodzaj konta** na magazyn obiektów **Blob**.
1. **Replikacja** może pozostać **RA-GRS**.
1. **Warstwa dostępu** może pozostać **gorąca**.
1. Wybierz **pozycję Przejrzyj + utwórz,** aby utworzyć konto magazynu.
    Po zakończeniu wdrażania strona **konto magazynu** zostanie otwarta automatycznie.

#### <a name="21-create-a-container"></a>2.1 Tworzenie kontenera

Aby utworzyć kontener publiczny w magazynie obiektów Blob, wykonaj następujące kroki:

1. W obszarze **Usługa obiektów Blob** w menu po lewej stronie wybierz pozycję **Blobs**.
1. Wybierz **+Kontener**.
1. W **yjmij nazwę**, wprowadź *katalog główny*. Nazwa może być nazwą wybranego, na przykład *contoso*, ale używamy *katalogu głównego* w tym przykładzie dla prostoty.
1. W obszarze **Poziom dostępu publicznego**wybierz pozycję **Blob**, a następnie **OK**.
1. Wybierz **katalog główny,** aby otworzyć nowy kontener.

#### <a name="22-upload-your-custom-page-content-files"></a>2.2 Przesyłanie niestandardowych plików zawartości strony

1. Wybierz **pozycję Przekaż**.
1. Wybierz ikonę folderu obok **pozycji Wybierz plik**.
1. Przejdź do i wybierz **opcję dostosuj interfejs użytkownika,,który**został utworzony wcześniej w sekcji dostosowywania interfejsu użytkownika strony.
1. Jeśli chcesz przesłać do podfolderu, rozwiń pozycję **Zaawansowane** i wprowadź nazwę folderu w **folderze Przekaż do folderu**.
1. Wybierz **pozycję Przekaż**.
1. Wybierz obiekt blob **customize-ui.html,** który został przekazany.
1. Po prawej stronie pola tekstowego **adresu URL** wybierz ikonę **Kopiuj do schowka,** aby skopiować adres URL do schowka.
1. W przeglądarce sieci Web przejdź do skopiowanego adresu URL, aby sprawdzić, czy przekazany obiekt blob jest dostępny. Jeśli jest niedostępny, na przykład jeśli wystąpi `ResourceNotFound` błąd, upewnij się, że typ dostępu do kontenera jest ustawiony na obiekt **blob**.

### <a name="3-configure-cors"></a>3. Konfigurowanie cors

Skonfiguruj magazyn obiektów Blob dla udostępniania zasobów między źródłami, wykonując następujące kroki:

1. W menu wybierz **CORS**.
1. W przypadku **dozwolonych** `https://your-tenant-name.b2clogin.com`źródeł pochodzenia wprowadź . Zamień `your-tenant-name` na nazwę dzierżawy usługi Azure AD B2C. Na przykład `https://fabrikam.b2clogin.com`. Podczas wprowadzania nazwy dzierżawy należy używać wszystkich małych liter.
1. W przypadku **metod dozwolonych**wybierz opcję "Obie `GET` i . `OPTIONS`"
1. W polu **Dozwolone nagłówki**wprowadź gwiazdkę (*).
1. W polu Widoczne **nagłówki**wprowadź gwiazdkę (*).
1. Dla **maksymalnego wieku**, wprowadź 200.
1. Wybierz **pozycję Zapisz**.

#### <a name="31-test-cors"></a>3.1 Test CORS

Sprawdź, czy jesteś gotowy, wykonując następujące kroki:

1. Powtórz krok konfigurowania CORS. W przypadku **dozwolonych źródeł pochodzenia**wprowadź`https://www.test-cors.org`
1. Przejdź do [www.test-cors.org](https://www.test-cors.org/) 
1. W polu **Zdalny adres URL** wklej adres URL pliku HTML. Na przykład: `https://your-account.blob.core.windows.net/azure-ad-b2c/unified.html`
1. Wybierz **pozycję Wyślij żądanie**.
    Wynik powinien `XHR status: 200`być . 
    Jeśli zostanie wyświetlony błąd, upewnij się, że ustawienia CORS są poprawne. Może być również konieczne wyczyszczenie pamięci podręcznej przeglądarki lub otwarcie prywatnej sesji przeglądania, naciskając klawisze Ctrl+Shift+P.
