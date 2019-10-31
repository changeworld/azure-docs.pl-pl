---
title: Dostosowywanie interfejsu użytkownika podróży użytkowników przy użyciu zasad niestandardowych | Microsoft Docs
description: Dowiedz się więcej na temat Azure Active Directory B2C zasad niestandardowych.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7e4714de9868dbd540e2e662b22a22da6df6514b
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73147533"
---
# <a name="customize-the-ui-of-a-user-journey-with-custom-policies"></a>Dostosowywanie interfejsu użytkownika podróży użytkowników przy użyciu zasad niestandardowych

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

> [!NOTE]
> Ten artykuł zawiera zaawansowany opis sposobu działania dostosowywania interfejsu użytkownika oraz sposobu włączania zasad niestandardowych Azure AD B2C przy użyciu struktury obsługi tożsamości.


Bezproblemowe środowisko użytkownika jest kluczem do dowolnych rozwiązań między firmami. Bezproblemowe środowisko użytkownika to środowisko pracy, niezależnie od tego, czy urządzenie lub przeglądarka, gdzie przechodzenie przez usługę jest odróżnienie od usługi klienta, z której korzystają.

## <a name="understand-the-cors-way-for-ui-customization"></a>Informacje na temat sposobu dostosowywania interfejsu użytkownika

Azure AD B2C umożliwia dostosowanie wyglądu i działania środowiska użytkownika na różnych stronach, które są obsługiwane i wyświetlane przez Azure AD B2C przy użyciu zasad niestandardowych.

W tym celu Azure AD B2C uruchamia kod w przeglądarce klienta i korzysta z nowoczesnego i standardowego podejścia do [udostępniania zasobów między źródłami (CORS)](https://www.w3.org/TR/cors/) w celu załadowania niestandardowej zawartości z określonego adresu URL, który określono w zasadach niestandardowych, aby wskazywała na język HTML5/CSS przystawki. CORS to mechanizm, który umożliwia zażądanie zasobów z ograniczeniami, takich jak czcionki, na stronie sieci Web z innej domeny poza domeną, z której pochodzi zasób.

W porównaniu do starego tradycyjnego sposobu, w którym strony szablonu są własnością rozwiązania, w którym zamieszczono ograniczony tekst i obrazy, w przypadku których zapewniono ograniczoną kontrolę nad układem i działaniem, co prowadzi do większej liczby problemów w celu zapewnienia bezproblemowego środowiska, mechanizmu CORS obsługuje HTML5 i CSS oraz umożliwia:

- Hostowanie zawartości i rozwiązanie wprowadza jego kontrolki za pomocą skryptu po stronie klienta.
- Mają pełną kontrolę nad każdym pikselem układu i działania.

Możesz podać dowolną liczbę stron zawartości, odpowiednio wpisując pliki HTML5/CSS.

> [!NOTE]
> Ze względów bezpieczeństwa użycie języka JavaScript jest obecnie blokowane na potrzeby dostosowywania. 

W każdym z szablonów HTML5/CSS podajesz element *zakotwiczony* , który odpowiada wymaganemu elementowi `<div id="api">` w kodzie HTML lub stronie zawartości, jak pokazano poniżej. Azure AD B2C wymaga, aby wszystkie strony zawartości miały ten określony blok DIV.

```
<!DOCTYPE html>
<html>
  <head>
    <title>Your page content’s tile!</title>
  </head>
  <body>
    <div id="api"></div>
  </body>
</html>
```

Zawartość związana z Azure AD B2Cami dla strony jest wprowadzana do tego elementu DIV, podczas gdy pozostała część strony jest do kontroli. Azure AD B2C kod JavaScript jest ściągany do zawartości i wprowadza kod HTML do tego określonego elementu DIV. Azure AD B2C dodaje odpowiednio następujące kontrolki: Sterowanie wybieraniem konta, kontrolki logowania, wieloskładnikowe i kontrolki kolekcji atrybutów. Azure AD B2C zapewnia, że wszystkie kontrolki są zgodne z językiem HTML5 i dostępne, wszystkie formanty mogą być w pełni wzorowane i że wersja kontrolna nie jest przychodząca.

Scalona zawartość jest ostatecznie wyświetlana jako dokument dynamiczny dla konsumenta.

Aby upewnić się, że wszystko działa zgodnie z oczekiwaniami, musisz:

- Upewnij się, że zawartość jest zgodna z językiem HTML5 i jest dostępna
- Upewnij się, że na serwerze zawartości włączono funkcję CORS.
- Obsługuj zawartość za pośrednictwem protokołu HTTPS.
- Używaj bezwzględnych adresów URL, takich jak `https://yourdomain/content`, dla wszystkich linków i zawartości CSS.

> [!TIP]
> Aby sprawdzić, czy w lokacji, w której znajduje się Twoja zawartość, włączono funkcję CORS i przetestować żądania CORS, można użyć https://test-cors.org/ lokacji. Dziękujemy za tę lokację, można wysłać żądanie CORS do serwera zdalnego (w celu przetestowania, czy funkcja CORS jest obsługiwana), lub wysłać żądanie CORS do serwera testowego (aby poznać niektóre funkcje CORS).

> [!TIP]
> Lokacja https://enable-cors.org/ również stanowi więcej niż przydatne zasoby dotyczące mechanizmu CORS.

Dzięki zastosowaniu tego podejścia opartego na mechanizmie CORS użytkownicy końcowi mają spójne środowiska między aplikacją i stronami obsługiwanymi przez Azure AD B2C.

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Jako warunek wstępny należy utworzyć konto magazynu. Do utworzenia konta usługi Azure Blob Storage potrzebna jest subskrypcja platformy Azure. Możesz zarejestrować bezpłatną wersję próbną w [witrynie sieci Web systemu Azure](https://azure.microsoft.com/pricing/free-trial/).

1. Otwórz sesję przeglądania i przejdź do [Azure Portal](https://portal.azure.com).
2. Zaloguj się przy użyciu poświadczeń administracyjnych.
3. Kliknij pozycję **Utwórz zasób** > **magazynu** > **konto magazynu**.  Zostanie otwarte okienko **Utwórz konto magazynu** .
4. W polu **Nazwa**Podaj nazwę konta magazynu, na przykład *contoso369b2c*. Ta wartość jest późniejsza, nazywana *storageAccountName*.
5. Wybierz odpowiednie opcje dla warstwy cenowej, grupy zasobów i subskrypcji. Upewnij się, że jest zaznaczone pole wyboru **Przypnij do tablicy startowej** . Kliknij przycisk **Utwórz**.
6. Wróć do tablicy startowej i kliknij utworzone konto magazynu.
7. W sekcji **usługi** kliknij pozycję **obiekty blob**. Zostanie otwarte **okienko BLOB Service** .
8. Kliknij pozycję **+ kontener**.
9. W polu **Nazwa**Podaj nazwę kontenera, na przykład *B2C*. Ta wartość jest późniejsza nazywana *ContainerName*.
9. Wybierz **obiekt BLOB** jako **Typ dostępu**. Kliknij przycisk **Utwórz**.
10. Utworzony kontener zostanie wyświetlony na liście w **okienku BLOB Service**.
11. Zamknij okienko **obiekty blob** .
12. W **okienku konto magazynu**kliknij ikonę **klucza** . Zostanie otwarte **okienko klucze dostępu** .  
13. Zapisz wartość **Klucz1**. Ta wartość jest późniejsza, nazywana *Klucz1*.

## <a name="downloading-the-helper-tool"></a>Pobieranie narzędzia pomocnika

1.  Pobierz narzędzie pomocnika z usługi [GitHub](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip).
2.  Zapisz plik *B2C-AzureBlobStorage-Client-Master. zip* na komputerze lokalnym.
3.  Wyodrębnij zawartość pliku B2C-AzureBlobStorage-Client-master. zip na dysku lokalnym, na przykład w folderze **UI-Customization-Pack** , który tworzy folder *B2C-AzureBlobStorage-Client-Master* poniżej.
4.  Otwórz ten folder i Wyodrębnij zawartość pliku archiwum *B2CAzureStorageClient. zip* .

## <a name="upload-the-ui-customization-pack-sample-files"></a>Przekazywanie przykładowych plików z pakietem dostosowywania interfejsu użytkownika

1.  Korzystając z Eksploratora Windows, przejdź do folderu *B2C-AzureBlobStorage-Client-Master* znajdującego się w folderze *UI-Customization-Pack* utworzonym w poprzedniej sekcji.
2.  Uruchom plik *B2CAzureStorageClient. exe* . Ten program przekazuje wszystkie pliki w katalogu określonym dla konta magazynu i włączy dostęp do mechanizmu CORS dla tych plików.
3.  Po wyświetleniu monitu podaj: a.  Nazwa konta magazynu, *storageAccountName*, na przykład *contoso369b2c*.
    b.  Podstawowy klucz dostępu do magazynu obiektów blob platformy Azure, *Klucz1*, na przykład *contoso369b2c*.
    d.  Nazwa kontenera magazynu obiektów blob magazynu, *ContainerName*, na przykład *B2C*.
    d.  Ścieżka przykładowych plików programu *Starter Pack* , na przykład *. \B2CTemplates\wingtiptoys*.

Jeśli wykonano powyższe kroki, pliki HTML5 i CSS *interfejsu UI* dla fikcyjnej firmy **wingtiptoys** teraz wskazują Twoje konto magazynu.  Możesz sprawdzić, czy zawartość została pomyślnie przekazana, otwierając odpowiednie okienko kontenera w Azure Portal. Możesz również sprawdzić, czy zawartość została pomyślnie przekazana, uzyskując dostęp do strony z przeglądarki. Aby uzyskać więcej informacji, zobacz [Azure Active Directory B2C: Narzędzie pomocnika służące do zademonstrowania funkcji dostosowywania interfejsu użytkownika (UI) na stronie](active-directory-b2c-reference-ui-customization-helper-tool.md).

## <a name="ensure-the-storage-account-has-cors-enabled"></a>Upewnij się, że konto magazynu ma włączony mechanizm CORS

Aby Azure AD B2C załadować zawartość, w punkcie końcowym musi być włączona funkcja CORS (Udostępnianie zasobów między źródłami). Wynika to z faktu, że zawartość jest hostowana w innej domenie niż domena Azure AD B2C będzie obsługiwać stronę.

Aby sprawdzić, czy magazyn hostujący zawartość jest włączony przy użyciu mechanizmu CORS, wykonaj następujące czynności:

1. Otwórz sesję przeglądania i przejdź do strony *ujednolicony plik HTML* przy użyciu pełnego adresu URL jego lokalizacji na koncie magazynu, `https://<storageAccountName>.blob.core.windows.net/<containerName>/unified.html`. Na przykład https://contoso369b2c.blob.core.windows.net/b2c/unified.html.
2. Przejdź do https://test-cors.org. Ta witryna pozwala sprawdzić, czy używana strona ma włączony mechanizm CORS.  
   <!--
   ![test-cors.org](../../media/active-directory-b2c-customize-ui-of-a-user-journey/test-cors.png)
   -->

3. W polu **zdalny adres URL**wprowadź pełny adres URL dla zawartości Unified. html, a następnie kliknij pozycję **Wyślij żądanie**.
4. Sprawdź, czy dane wyjściowe w sekcji **Results** zawierają *stan XHR: 200*, który wskazuje, że funkcja CORS jest włączona.
   <!--
   ![CORS enabled](../../media/active-directory-b2c-customize-ui-of-a-user-journey/cors-enabled.png)
   -->
   Konto magazynu powinno teraz zawierać kontener obiektów BLOB o nazwie *B2C* na ilustracji, który zawiera następujące szablony wingtiptoys z *pakietu Start-Pack*.

<!--
![Correctly configured storage account](../../articles/active-directory-b2c/media/active-directory-b2c-reference-customize-ui-custom/storage-account-final.png)
-->

W poniższej tabeli opisano cel wcześniejszych stron HTML5.

| Szablon HTML5 | Opis |
|----------------|-------------|
| *PhoneFactor. html* | Ta strona może służyć jako szablon strony usługi uwierzytelniania wieloskładnikowego. |
| *ResetPassword. html* | Ta strona może być używana jako szablon dla strony z zapomnianym hasłem. |
| *selfasserted. html* | Ta strona może służyć jako szablon na stronie rejestracji konta społecznościowego, na stronie rejestracji konta lokalnego lub na stronie logowania do konta lokalnego. |
| *Unified. html* | Ta strona może służyć jako szablon ujednoliconej strony rejestracji lub logowania. |
| *updateprofile. html* | Ta strona może służyć jako szablon strony aktualizacji profilu. |

## <a name="add-a-link-to-your-html5css-templates-to-your-user-journey"></a>Dodawanie linku do szablonów HTML5/CSS do podróży użytkownika

Możesz dodać link do szablonów HTML5/CSS do podróży użytkownika, edytując zasady niestandardowe bezpośrednio.

Niestandardowe szablony HTML5/CSS do użycia w podróży użytkownika należy określić na liście definicji zawartości, które mogą być używane w tych przejazdach użytkownika. W tym celu opcjonalny element *\<ContentDefinitions >* XML musi być zadeklarowany w sekcji *\<BUILDINGBLOCKS >* pliku XML zasad niestandardowych.

W poniższej tabeli opisano zestaw identyfikatorów definicji zawartości rozpoznawanych przez aparat Azure AD B2C Identity Experience oraz typ stron, które odnoszą się do nich.

| Identyfikator definicji zawartości | Opis |
|-----------------------|-------------|
| *Interfejs API. błąd* | **Strona błędu**. Ta strona jest wyświetlana po napotkaniu wyjątku lub błędu. |
| *API. idpselections* | **Strona wyboru dostawcy tożsamości**. Ta strona zawiera listę dostawców tożsamości, z których użytkownik może wybrać podczas logowania. Ci dostawcy są dostawcami tożsamości przedsiębiorstwa, dostawcami tożsamości społecznościowych, takimi jak Facebook, Google + lub kontami lokalnymi (na podstawie adresu e-mail lub nazwy użytkownika). |
| *API. idpselections. signup* | **Wybór dostawcy tożsamości na potrzeby rejestracji**. Ta strona zawiera listę dostawców tożsamości, z których użytkownik może wybierać podczas rejestracji. Ci dostawcy są dostawcami tożsamości przedsiębiorstwa, dostawcami tożsamości społecznościowych, takimi jak Facebook, Google + lub kontami lokalnymi (na podstawie adresu e-mail lub nazwy użytkownika). |
| *API. localaccountpasswordreset* | **Zapomniane hasło strony**. Ta strona zawiera formularz, który użytkownik musi wypełnić, aby zainicjować Resetowanie hasła.  |
| *API. localaccountsignin* | **Strona logowania do konta lokalnego**. Ta strona zawiera formularz logowania, który użytkownik musi wypełnić podczas logowania się przy użyciu konta lokalnego na podstawie adresu e-mail lub nazwy użytkownika. Formularz może zawierać pole wprowadzania tekstu i pole wprowadzania hasła. |
| *API. localaccountsignup* | **Strona rejestracji w ramach konta lokalnego**. Ta strona zawiera formularz rejestracji, który użytkownik musi wypełnić podczas tworzenia konta lokalnego na podstawie adresu e-mail lub nazwy użytkownika. Formularz może zawierać różne kontrolki wejściowe, takie jak pole wprowadzania tekstu, pole wprowadzania hasła, przycisk radiowy, pola rozwijane z pojedynczym wybieraniem i pola wyboru z zaznaczeniem wielu. |
| *API. PhoneFactor* | **Strona uwierzytelniania wieloskładnikowego**. Na tej stronie użytkownicy mogą weryfikować numery telefonów (przy użyciu tekstu lub głosu) podczas rejestracji lub logowania. |
| *API. selfasserted* | **Strona rejestracji konta społecznościowego**. Ta strona zawiera formularz rejestracji, który użytkownik musi podać podczas rejestracji przy użyciu istniejącego konta od dostawcy tożsamości społecznościowej, takiego jak Facebook lub Google +. Ta strona jest podobna do poprzedniej strony rejestracji konta społecznościowego, z wyjątkiem pól wprowadzania hasła. |
| *API. selfasserted. profileupdate* | **Strona aktualizacji profilu**. Ta strona zawiera formularz, za pomocą którego użytkownik może zaktualizować swój profil. Ta strona jest podobna do poprzedniej strony rejestracji konta społecznościowego, z wyjątkiem pól wprowadzania hasła. |
| *API. signuporsignin* | **Ujednolicona Strona rejestracji lub logowania**.  Ta strona obsługuje zarówno Logowanie jednokrotne & użytkowników, którzy mogą korzystać z dostawców tożsamości przedsiębiorstwa, dostawców tożsamości społecznościowych, takich jak Facebook, Google + lub konta lokalnego.

## <a name="next-steps"></a>Następne kroki
[Dokumentacja: omówienie sposobu działania zasad niestandardowych z platformą obsługi tożsamości w programie B2C](active-directory-b2c-reference-custom-policies-understanding-contents.md)
