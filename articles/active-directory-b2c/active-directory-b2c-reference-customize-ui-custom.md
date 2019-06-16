---
title: Dostosowywanie interfejsu użytkownika dla podróży użytkownika za pomocą zasad niestandardowych | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o zasadach niestandardowych usługi Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7053f5b0211878d2f0b9d810fc3f4c0b9361e6f7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66509606"
---
# <a name="customize-the-ui-of-a-user-journey-with-custom-policies"></a>Dostosowywanie interfejsu użytkownika dla podróży użytkownika za pomocą zasad niestandardowych

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

> [!NOTE]
> Ten artykuł stanowi zaawansowane opis sposobu działania dostosowywania interfejsu użytkownika i jak włączyć za pomocą niestandardowych zasad usługi Azure AD B2C, przy użyciu platformy środowiska tożsamości.


Nie zakłóca pracy użytkowników jest klucz dla dowolnego rozwiązania firma klient. Nie zakłóca pracy użytkowników to środowisko, na urządzeniu lub w przeglądarce, gdzie podróży użytkownika za pośrednictwem usługi jest odróżnienia od obsługi klienta, z których korzystają.

## <a name="understand-the-cors-way-for-ui-customization"></a>Zrozumieć sposób CORS do dostosowania interfejsu użytkownika

Usługa Azure AD B2C umożliwia dostosowanie wyglądu i działania programu użytkownika środowiska na różnych stronach, które są obsługiwane i wyświetlane przez usługę Azure AD B2C za pomocą niestandardowych zasad.

W tym celu usługi Azure AD B2C kodu w przeglądarce usługi konsumenta, korzysta z nowoczesnego i standardowego podejścia [udostępniania zasobów między źródłami (CORS)](https://www.w3.org/TR/cors/) załadować niestandardowej zawartości z określonych adresów URL, określonej w zasadach niestandardowych, aby wskazywał Szablony HTML5/CSS. CORS to mechanizm, który umożliwia ograniczone zasoby, takie jak czcionki, na stronie sieci web wymagane z innej domeny spoza domeny, z której pochodzi zasobu.

W porównaniu do tradycyjnych stary sposób, gdzie strony szablonów są własnością rozwiązania, w miejscu podania ograniczone tekstu i obrazów, w przypadku, gdy ograniczoną kontrolę nad układu i sposobu działania zaoferował, co prowadzi do więcej niż trudności do osiągnięcia nie zakłóca pracy sposób mechanizmu CORS obsługuje HTML5 i CSS i pozwalają na:

- Hostowanie zawartości i rozwiązanie wprowadza jego formantów, za pomocą skryptu po stronie klienta.
- Mają pełną kontrolę nad każdego piksela układu i sposobu działania.

Możesz podać dowolną liczbę stron zawartości, jak chcesz przy tworzeniu plików HTML5/CSS, zgodnie z potrzebami.

> [!NOTE]
> Ze względów bezpieczeństwa korzystanie z języka JavaScript jest obecnie zablokowana do dostosowania. 

W każdym z szablonów HTML5/CSS, podaj *zakotwiczenia* element, który odnosi się do wymaganych `<div id=”api”>` elementu w kodzie HTML lub strony zawartości, tak jak pokazują poniżej. Usługa Azure AD B2C wymaga wszystkich stron zawartości tego określonego div.

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

Zawartość usługi Azure AD B2C, związane z strony wstrzykuje się ten element div, podczas gdy pozostałej części strony jest Twój formant. Kod JavaScript w usłudze Azure AD B2C ściąga we własnych treściach i wprowadza HTML do tego elementu div określone. Usługa Azure AD B2C wprowadza następujące elementy sterujące zgodnie z potrzebami: Formant selektora konta, zalogować się kontrolki, Multi-Factor Authentication (obecnie telefoniczny) kontrolek i kontrolki kolekcji atrybutów. Usługa Azure AD B2C zapewnia, że wszystkie formanty są HTML5, zgodne i jest dostępny, można w pełni różne wszystkich kontrolek, a wersja kontrolki nie zbadanie.

Scalone zawartości po pewnym czasie jest wyświetlana jako dynamiczny dokumentu do konsumentów.

Aby upewnić się, że wszystko działa zgodnie z oczekiwaniami, musisz mieć:

- Upewnij się, że Twoja zawartość jest HTML5, zgodne i jest dostępny
- Upewnij się, że serwer zawartości jest włączona dla mechanizmu CORS.
- Udostępniać zawartość przy użyciu protokołu HTTPS.
- Używać bezwzględnych adresów URL, takich jak `https://yourdomain/content` dla wszystkich łączy i zawartość arkusza CSS.

> [!TIP]
> Aby sprawdzić, czy włączono mechanizm CORS lokacji zawartości są hosting w systemie i przetestować żądań CORPS, można skorzystać z witryny https://test-cors.org/. Dzięki rozłożeniu w tej witrynie możesz wysyłać żądania CORS na serwerze zdalnym (tak, aby sprawdzić, czy CORS jest obsługiwany) lub wysyłać żądania CORS serwer testowy (zapoznaj się z pewnych funkcji CORS).

> [!TIP]
> Witryna https://enable-cors.org/ stanowi również bardziej niż przydatne zasoby na mechanizmu CORS.

Dzięki rozłożeniu w tym podejściu z mechanizmu CORS użytkownicy końcowi mają spójnego środowiska między aplikacją i stron, obsługiwane przez usługę Azure AD B2C.

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Jako warunek wstępny musisz utworzyć konto magazynu. Musisz mieć subskrypcję platformy Azure do utworzenia konta usługi Azure Blob Storage. Możesz utworzyć konto bezpłatnej wersji próbnej w [witryny sieci Web Azure](https://azure.microsoft.com/pricing/free-trial/).

1. Otwórz sesję przeglądania i przejdź do [witryny Azure portal](https://portal.azure.com).
2. Zaloguj się przy użyciu poświadczeń administracyjnych.
3. Kliknij przycisk **Utwórz zasób** > **magazynu** > **konta magazynu**.  A **Tworzenie konta magazynu** otwarcie okienka.
4. W **nazwa**, podaj nazwę konta magazynu, na przykład *contoso369b2c*. Ta wartość jest później określany jako *storageAccountName*.
5. Wybierz odpowiednie opcje dla grupy zasobów, subskrypcji i warstwy cenowej. Upewnij się, że masz **Przypnij do tablicy startowej** zaznaczoną opcją. Kliknij pozycję **Utwórz**.
6. Wróć do tablicy startowej, a następnie kliknij konto magazynu, który został utworzony.
7. W **usług** kliknij **obiektów blob**. A **okienko usługi obiektów Blob** zostanie otwarty.
8. Kliknij przycisk **+ kontener**.
9. W **nazwa**, podaj nazwę kontenera, na przykład *b2c*. Ta wartość jest później nazywany *containerName*.
9. Wybierz **Blob** jako **dostęp typu**. Kliknij pozycję **Utwórz**.
10. Kontener, który został utworzony, pojawia się na liście na **okienko usługi obiektów Blob**.
11. Zamknij **obiektów blob** okienka.
12. Na **okienko konta magazynu**, kliknij przycisk **klucz** ikony. **Okienko klucze dostępu** zostanie otwarty.  
13. Zanotuj wartość **klucz1**. Ta wartość jest nazywana później *klucz1*.

## <a name="downloading-the-helper-tool"></a>Pobranie narzędzia pomocy

1.  Pobierz narzędzie Pomocnik z [GitHub](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip).
2.  Zapisz *B2C-usłudze Azure blob Storage-klient master.zip* pliku na komputerze lokalnym.
3.  Wyodrębnij zawartość pliku B2C-usłudze Azure blob Storage-klient master.zip na dysku lokalnym, na przykład w obszarze **pakiet w przypadku dostosowania interfejsu użytkownika** folderu, który tworzy *B2C-usłudze Azure blob Storage — klient master*folderu poniżej.
4.  Otwórz ten folder i Wyodrębnij zawartość pliku archiwum *B2CAzureStorageClient.zip* znajdujący się w nim.

## <a name="upload-the-ui-customization-pack-sample-files"></a>Przekazywanie plików przykładowych pakiet w przypadku dostosowania interfejsu użytkownika

1.  Za pomocą Eksploratora Windows przejdź do folderu *B2C-usłudze Azure blob Storage — klient master* znajdujący się w folderze *pakiet w przypadku dostosowania interfejsu użytkownika* folderu utworzonego w poprzedniej sekcji.
2.  Uruchom *B2CAzureStorageClient.exe* pliku. Ten program służy do przekazywania wszystkich plików w katalogu, określić, czy konto magazynu i włączyć dostęp CORS dla tych plików.
3.  Po wyświetleniu monitu podaj:.  Nazwa konta magazynu *storageAccountName*, na przykład *contoso369b2c*.
    b.  Podstawowy klucz dostępu usługi azure blob Storage, *klucz1*, na przykład *contoso369b2c*.
    c.  Nazwa kontenera magazynu obiektów blob usługi storage *containerName*, na przykład *b2c*.
    d.  Ścieżka *pakiet startowy* przykładowe pliki, na przykład *... \B2CTemplates\wingtiptoys*.

Po wykonaniu powyższych kroków, HTML5 i CSS pliki *pakiet w przypadku dostosowania interfejsu użytkownika* w fikcyjnej firmie **wingtiptoys** jest teraz skierowana do swojego konta magazynu.  Aby sprawdzić, czy zawartość został przekazany poprawnie, otwierając okienko powiązane kontenera w witrynie Azure portal. Można też sprawdzić, czy zawartość został przekazany poprawnie, uzyskując dostęp do strony w przeglądarce. Aby uzyskać więcej informacji, zobacz [usługi Azure Active Directory B2C: Narzędzie pomocnika używany do przedstawiania funkcji dostosowywania interfejsu użytkownika strony](active-directory-b2c-reference-ui-customization-helper-tool.md).

## <a name="ensure-the-storage-account-has-cors-enabled"></a>Upewnij się, że włączono mechanizm CORS konta magazynu

Mechanizm CORS (Cross-Origin Resource Sharing) musi być włączona w punkcie końcowym usługi Azure AD B2C do załadowania zawartości. Jest to spowodowane zawartości znajduje się w innej domenie niż domena, do którego usługa Azure AD B2C będzie obsługująca stronę z.

Aby sprawdzić, czy magazyn, które hostują zawartości na ma włączono mechanizm CORS, wykonaj następujące czynności:

1. Otwieranie sesji przeglądania i przejdź do strony *unified.html* przy użyciu pełny adres URL lokalizacji na koncie magazynu `https://<storageAccountName>.blob.core.windows.net/<containerName>/unified.html`. Na przykład https://contoso369b2c.blob.core.windows.net/b2c/unified.html.
2. Przejdź do adresu https://test-cors.org. Ta witryna pozwala sprawdzić, czy strony, którego używasz, ma włączono mechanizm CORS.  
   <!--
   ![test-cors.org](../../media/active-directory-b2c-customize-ui-of-a-user-journey/test-cors.png)
   -->

3. W **zdalnego adresu URL**, wprowadź pełny adres URL zawartości unified.html i kliknij przycisk **Wyślij żądanie**.
4. Upewnij się, że dane wyjściowe w **wyniki** sekcja zawiera *XHR stanu: 200*, co oznacza, że włączono mechanizm CORS.
   <!--
   ![CORS enabled](../../media/active-directory-b2c-customize-ui-of-a-user-journey/cors-enabled.png)
   -->
   Konto magazynu nie powinien zawierać kontenera obiektów blob o nazwie *b2c* na ilustracji, który zawiera następujące szablony wingtiptoys z *pakiet startowy*.

<!--
![Correctly configured storage account](../../articles/active-directory-b2c/media/active-directory-b2c-reference-customize-ui-custom/storage-account-final.png)
-->

Poniższa tabela opisuje przeznaczenie stron HTML5.

| Szablon języka HTML5 | Opis |
|----------------|-------------|
| *phonefactor.html* | Na tej stronie może służyć jako szablon dla strony uwierzytelniania wieloskładnikowego. |
| *resetpassword.html* | Na tej stronie mogą być używane jako szablon strona obsługi zapomnianego hasła. |
| *selfasserted.html* | Na tej stronie mogą służyć jako szablonu dla konta społecznościowego Zarejestruj stronę, konta lokalnego stronę w górę lub strony logowania konta lokalnego. |
| *unified.html* | Na tej stronie może służyć jako szablon ujednoliconego rejestracji lub logowania strony. |
| *updateprofile.html* | Na tej stronie może służyć jako szablon dla strony aktualizacji profilu. |

## <a name="add-a-link-to-your-html5css-templates-to-your-user-journey"></a>Dodaj link do szablonów HTML5/CSS do Twojej podróży użytkownika

Można dodać łącze do szablonów HTML5/CSS do swoją podróż po użytkownik bezpośrednio edytując zasad niestandardowych.

Niestandardowe szablony HTML5/CSS w podróż użytkownika, musisz określić listę definicji zawartości, które mogą być używane w tych podróży użytkownika. W tym celu, opcjonalny  *\<ContentDefinitions >* — element XML musi być zadeklarowana w obszarze  *\<BuildingBlocks >* sekcji w pliku XML zasady niestandardowe.

W poniższej tabeli opisano zestaw identyfikatorów definicji rozpoznał tożsamości usługi Azure AD B2C środowiska aparatu i na typ stron, które odnoszą się do nich zawartości.

| Identyfikator definicji zawartości | Opis |
|-----------------------|-------------|
| *api.error* | **Strona błędu**. Ta strona jest wyświetlana, gdy występuje wyjątek lub komunikat o błędzie. |
| *api.idpselections* | **Strona wyboru dostawcy tożsamości**. Ta strona zawiera listę dostawców tożsamości, które użytkownik może wybrać z podczas logowania. Ci dostawcy są enterprise dostawców tożsamości, dostawców tożsamości społecznościowych, takich jak Facebook i Google + lub kont lokalnych (oparte na nazwę użytkownika lub adres e-mail). |
| *api.idpselections.signup* | **Wybór dostawcy tożsamości dla rejestracji**. Ta strona zawiera listę dostawców tożsamości, które użytkownik może wybierać podczas rejestracji. Ci dostawcy są enterprise dostawców tożsamości, dostawców tożsamości społecznościowych, takich jak Facebook i Google + lub kont lokalnych (oparte na nazwę użytkownika lub adres e-mail). |
| *api.localaccountpasswordreset* | **Strona obsługi zapomnianego hasła**. Ta strona zawiera formularz, który użytkownik będzie musiał wypełnienia inicjować ich resetowania hasła.  |
| *api.localaccountsignin* | **Strona logowania dla kont lokalnych**. Ta strona zawiera formularz logowania, które użytkownik będzie musiał podać podczas logowania się za pomocą konta lokalnego, który jest oparty na adres e-mail lub nazwę użytkownika. Formularz może zawierać pola wprowadzania tekstu, a pole wprowadzania hasła. |
| *api.localaccountsignup* | **Strona rejestracji dla kont lokalnych**. Ta strona zawiera formularz rejestracji, który użytkownik będzie musiał podać podczas tworzenia konta lokalnego, który jest oparty na adres e-mail lub nazwę użytkownika. Formularz może zawierać innej kontrolki wejściowe, takie jak pola wprowadzania tekstu, pole wprowadzania hasła, przycisk radiowy, wybieranych list rozwijanych i pól wyboru wielokrotnego wyboru. |
| *api.phonefactor* | **Strona uwierzytelniania wieloskładnikowego**. Na tej stronie użytkowników można sprawdzić swoje numery telefonów (przy użyciu tekstowych lub głosowych) podczas tworzenia konta lub logowania. |
| *api.selfasserted* | **Strona rejestracji dla kont społecznościowych**. Ta strona zawiera formularz rejestracji, który użytkownik będzie musiał podać podczas logowania się przy użyciu istniejącego konta z dostawcy tożsamości społecznościowych, takich jak Facebook lub Google +. Ta strona jest podobna do poprzedniej strony rejestracji konta społecznościowego, z wyjątkiem pól wprowadzania hasła. |
| *api.selfasserted.profileupdate* | **Strona aktualizacji profilu**. Ta strona zawiera formularz, który użytkownik może użyć, aby zaktualizować swój profil. Ta strona jest podobna do poprzedniej strony rejestracji konta społecznościowego, z wyjątkiem pól wprowadzania hasła. |
| *api.signuporsignin* | **Ujednolicona strona rejestracji lub logowania**.  Ta strona obsługuje zarówno rejestracji i logowania użytkowników, którzy mogą korzystać z dostawców tożsamości organizacji, dostawców tożsamości społecznościowych, takich jak Facebook lub Google + lub kont lokalnych.

## <a name="next-steps"></a>Kolejne kroki
[Odwołanie: Omówienie zasad niestandardowych działają z platformy środowiska tożsamości w B2C](active-directory-b2c-reference-custom-policies-understanding-contents.md)
