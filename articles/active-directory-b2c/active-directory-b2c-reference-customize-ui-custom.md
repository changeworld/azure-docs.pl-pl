---
title: Dostosowywanie interfejsu użytkownika w podróży użytkownika z niestandardowych zasad | Dokumentacja firmy Microsoft
description: Informacje o zasadach niestandardowych usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/25/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 0980c79ccd9ebd170e747514bba712c498e1387c
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34711913"
---
# <a name="customize-the-ui-of-a-user-journey-with-custom-policies"></a>Dostosowywanie interfejsu użytkownika w podróży użytkownika przy użyciu zasad niestandardowych

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

> [!NOTE]
> W tym artykule jest zaawansowane opis działania dostosowywania interfejsu użytkownika i jak włączyć za pomocą niestandardowych zasad usługi Azure AD B2C, przy użyciu platformy obsługi tożsamości.


Nie zakłóca pracy użytkowników jest kluczem dla dowolnego rozwiązania biznesowe z klientem. Nie zakłóca pracy użytkowników to środowisko, na urządzeniu lub w przeglądarce, gdzie jest odróżnienia od obsługi klienta, które używają przebieg użytkownika za pośrednictwem usługi.

## <a name="understand-the-cors-way-for-ui-customization"></a>Zrozumienie sposobu CORS do dostosowania interfejsu użytkownika

Usługa Azure AD B2C umożliwia dostosowanie wyglądu i działania środowisko użytkownika (UX) na różnych stronach, które są obsługiwane i wyświetlane przez usługę Azure AD B2C za pomocą niestandardowych zasad.

W tym celu usługi Azure AD B2C kod w przeglądarce sieci klienta, korzysta z podejścia nowoczesnych i standard [udostępniania zasobów między źródłami (CORS)](http://www.w3.org/TR/cors/) załadować niestandardowej zawartości z określonym adresem URL określonym w zasadach niestandardowych, aby wskazywał szablonów HTML5/CSS. CORS jest mechanizm umożliwiający ograniczone zasoby, takie jak czcionki, na stronie sieci web wymagane z innej domeny spoza domeny, z którego pochodzi zasobu.

W porównaniu do starego tradycyjny sposób, gdzie strony szablonu należą do firmy przy użyciu rozwiązania, gdzie podane ograniczone tekstu i obrazów, w którym została przyjęta ograniczone kontrolę nad układ i sposób działania prowadzące do więcej niż trudności do osiągnięcia bezproblemowej występują, sposób CORS obsługuje HTML5 i CSS i umożliwiają:

- Hostowanie zawartości i rozwiązanie injects jego formantów za pomocą skryptu po stronie klienta.
- Mają pełną kontrolę nad każdego piksela układ i działania.

Możesz podać przez obsługuje tworzenie pliki HTML5/CSS, takich jak dowolną liczbę stron zawartości.

> [!NOTE]
> Ze względów bezpieczeństwa użycie JavaScript jest obecnie zablokowany do dostosowania. Aby odblokować JavaScript, nazwy domeny niestandardowej dla dzierżawy usługi Azure AD B2C są potrzebne.

W każdej z szablonów HTML5/CSS, można zapewnić *zakotwiczenia* element, który odpowiada wymaganych `<div id=”api”>` element w kodzie HTML lub strony zawartości, jak ilustrują poniżej. Usługa Azure AD B2C musi mieć wszystkie strony z zawartością tego określonego div.

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

Azure AD B2C związane z zawartości dla strony wstrzykuje się ten element div, podczas pozostałej części strony jest Twój do formantu. Kod JavaScript usługi Azure AD B2C ściąga zawartości i injects HTML do tego elementu div określone. Usługa Azure AD B2C injects następujące formanty odpowiednio: wybór Kontrola konta, zaloguj się w formantach, wieloskładnikowego formantów (obecnie telefoniczny) i atrybutu kolekcji formantów. Usługa Azure AD B2C zapewnia, że wszystkie formanty są HTML5 zgodne i jest dostępny, wszystkie formanty można całkowicie stylem i czy nie zbadanie kontroli wersji.

Scalone zawartości ostatecznie jest wyświetlana jako dynamiczny dokumentu z klientem.

Aby upewnić się, że wszystko działa zgodnie z oczekiwaniami, należy:

- Upewnij się, że zawartość jest HTML5 zgodne i jest dostępny
- Upewnij się, że serwer zawartości jest włączona dla CORS.
- Udostępniać zawartość za pośrednictwem protokołu HTTPS.
- Takie jak używać bezwzględnych adresów URL https://yourdomain/content dla wszystkich łączy i zawartość arkusza CSS.

> [!TIP]
> Aby sprawdzić, czy są hostingu zawartości na lokacji ma włączonego mechanizmu CORS i żądań CORS testu, można skorzystać z witryny http://test-cors.org/. Dzięki tej lokacji możesz wysłać żądanie CORS do serwera zdalnego (do testowania, jeśli jest obsługiwana przez CORS) lub Wyślij żądanie CORS na serwerze testowym (aby eksplorować niektórych funkcji CORS).

> [!TIP]
> Witryny http://enable-cors.org/ stanowi również ponad przydatne zasoby na CORS.

Dzięki użyciu tej metody na podstawie specyfikacji CORS użytkownicy końcowi mają spójne działanie pomiędzy aplikacją i stron obsługiwanych przez usługę Azure AD B2C.

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Warunkiem wstępnym musisz utworzyć konto magazynu. Potrzebujesz subskrypcji platformy Azure, aby utworzyć konto magazynu obiektów Blob Azure. Możesz utworzyć konto bezpłatnej wersji próbnej na [witryny sieci Web Azure](https://azure.microsoft.com/pricing/free-trial/).

1. Otwórz sesję przeglądania i przejdź do [portalu Azure](https://portal.azure.com).
2. Zaloguj się przy użyciu poświadczeń administracyjnych.
3. Kliknij przycisk **Utwórz zasób** > **magazynu** > **konta magazynu**.  A **utworzyć konto magazynu** otwiera okienka.
4. W **nazwa**, podaj nazwę konta magazynu, na przykład *contoso369b2c*. Ta wartość jest później określone jako *storageAccountName*.
5. Wybierz odpowiednie opcje dla warstwy cenowej, grupy zasobów i subskrypcji. Upewnij się, że masz **Przypnij do tablicy startowej** zaznaczoną opcją. Kliknij przycisk **Utwórz**.
6. Wróć do tablicy startowej, a następnie kliknij przycisk utworzone konto magazynu.
7. W **usług** kliknij **obiekty BLOB**. A **obiektu Blob usługa okienku** otwiera.
8. Kliknij przycisk **+ kontener**.
9. W **nazwa**, podaj nazwę kontenera, na przykład *b2c*. Ta wartość jest później nazywany *containerName*.
9. Wybierz **obiektu Blob** jako **dostęp typu**. Kliknij przycisk **Utwórz**.
10. Kontener, który został utworzony zostanie wyświetlony na liście w **obiektu Blob usługa okienku**.
11. Zamknij **obiekty BLOB** okienka.
12. Na **okienko konta magazynu**, kliknij przycisk **klucza** ikony. **Okienko klucze dostępu** otwiera.  
13. Zanotuj wartość **klucz1**. Ta wartość jest później określane jako *klucz1*.

## <a name="downloading-the-helper-tool"></a>Pobranie narzędzia pomocy

1.  Pobierz narzędzie Pomocnik z [GitHub](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip).
2.  Zapisz *B2C-AzureBlobStorage-klienta master.zip* plik na komputerze lokalnym.
3.  Wyodrębnij zawartość pliku B2C-AzureBlobStorage-klienta master.zip na lokalnym dysku, na przykład w obszarze **pakietu w przypadku dostosowania interfejsu użytkownika** folder, który tworzy *B2C-AzureBlobStorage-klienta master*folderu poniżej.
4.  Otwórz ten folder i Wyodrębnij zawartość pliku archiwum *B2CAzureStorageClient.zip* w nim.

## <a name="upload-the-ui-customization-pack-sample-files"></a>Przekaż przykładowych plików pakietu w przypadku dostosowania interfejsu użytkownika

1.  Przy użyciu Eksploratora Windows przejdź do folderu *B2C-AzureBlobStorage-klienta master* znajduje się w obszarze *pakietu w przypadku dostosowania interfejsu użytkownika* folder utworzony w poprzedniej sekcji.
2.  Uruchom *B2CAzureStorageClient.exe* pliku. Ten program będzie przekazywać wszystkie pliki w katalogu, określić, czy Twoje konto magazynu i włączyć CORS dostęp do tych plików.
3.  Po wyświetleniu monitu podaj:.  Nazwa konta magazynu *storageAccountName*, na przykład *contoso369b2c*.
    b.  Podstawowy klucz dostępu magazynu obiektów blob platformy azure, *klucz1*, na przykład *contoso369b2c*.
    c.  Nazwa kontenera magazynu obiektów blob z magazynu *containerName*, na przykład *b2c*.
    d.  Ścieżka *początkowego pakietu* próbki plików, na przykład *... \B2CTemplates\wingtiptoys*.

Po wykonaniu powyższych kroków pliki CSS i HTML5 *pakietu w przypadku dostosowania interfejsu użytkownika* w fikcyjnej firmie **wingtiptoys** teraz wskazuje konta magazynu.  Aby sprawdzić, czy zawartość, został przesłany poprawnie, otwierając okienko pokrewne kontenera w portalu Azure. Alternatywnie można sprawdzić, czy zawartość, został przesłany prawidłowo po zalogowaniu się do strony w przeglądarce. Aby uzyskać więcej informacji, zobacz [usługi Azure Active Directory B2C: Narzędzie Pomocnik, używany do przedstawiania funkcji dostosowywania interfejsu użytkownika strony](active-directory-b2c-reference-ui-customization-helper-tool.md).

## <a name="ensure-the-storage-account-has-cors-enabled"></a>Upewnij się, że konto magazynu ma włączonego mechanizmu CORS

CORS (Cross-Origin Resource Sharing) musi być włączona punktu końcowego dla usługi Azure AD B2C do załadowania zawartości. Jest to spowodowane zawartości znajduje się na innej domeny niż domena usługi Azure AD B2C będzie obsługująca stronę z.

Aby sprawdzić, czy magazyn, które prowadzą hosting zawartości na ma włączonego mechanizmu CORS, kontynuować następujące czynności:

1. Otwórz sesję przeglądania i przejdź do strony *unified.html* przy użyciu pełny adres URL lokalizacji na koncie magazynu `https://<storageAccountName>.blob.core.windows.net/<containerName>/unified.html`. Na przykład https://contoso369b2c.blob.core.windows.net/b2c/unified.html.
2. Przejdź do adresu http://test-cors.org. Tej lokacji pozwala sprawdzić, czy strona, której używasz ma włączonego mechanizmu CORS.  
<!--
![test-cors.org](../../media/active-directory-b2c-customize-ui-of-a-user-journey/test-cors.png)
-->

3. W **zdalnego adresu URL**, wprowadź pełny adres URL dla zawartości unified.html i kliknij przycisk **Wyślij żądanie**.
4. Sprawdź, czy dane wyjściowe w **wyniki** sekcja zawiera *stan XHR: 200*, co oznacza, że włączono CORS.
<!--
![CORS enabled](../../media/active-directory-b2c-customize-ui-of-a-user-journey/cors-enabled.png)
-->
Konto magazynu powinien zawierać teraz kontenera obiektów blob o nazwie *b2c* na ilustracji, który zawiera następujące szablony wingtiptoys z *początkowego pakietu*.

<!--
![Correctly configured storage account](../../articles/active-directory-b2c/media/active-directory-b2c-reference-customize-ui-custom/storage-account-final.png)
-->

W poniższej tabeli opisano przeznaczenie stron HTML5.

| Szablon HTML5 | Opis |
|----------------|-------------|
| *phonefactor.html* | Ta strona może służyć jako szablon dla strony uwierzytelniania wieloskładnikowego. |
| *resetpassword.html* | Ta strona może służyć jako szablon dla nie pamiętasz hasła strony. |
| *selfasserted.html* | Ta strona służy jako szablon dla kont społecznościowych, zarejestruj się strony, strony logowania konta lokalnego lub stronę logowania konta lokalnego. |
| *unified.html* | Ta strona może służyć jako szablon ujednoliconego rejestracji lub strony logowania. |
| *updateprofile.html* | Ta strona może służyć jako szablon strony aktualizacji profilu. |

## <a name="add-a-link-to-your-html5css-templates-to-your-user-journey"></a>Dodaj łącze do szablonów HTML5/CSS do podróży użytkownika

Można dodać łącza do szablonów HTML5/CSS do podróży użytkownika bezpośrednio edytując zasadę niestandardową.

Szablony niestandardowe HTML5/CSS do użycia w podróży użytkownika ma określonych na liście zawartości definicje, które mogą być używane w podróży tych użytkowników. W tym celu opcjonalny *<ContentDefinitions>* — element XML musi być zadeklarowana w obszarze *<BuildingBlocks>* sekcji w pliku XML zasady niestandardowe.

W poniższej tabeli opisano zestaw identyfikatorów definicji rozpoznał tożsamości usługi Azure AD B2C może wystąpić aparatu i typ strony, które odnoszą się do nich zawartości.

| Identyfikator definicji zawartości | Opis |
|-----------------------|-------------|
| *api.error* | **Strona błędu**. Ta strona jest wyświetlana po napotkaniu wyjątku lub wystąpił błąd. |
| *API.idpselections* | **Strona wyboru dostawcy tożsamości**. Ta strona zawiera listę dostawców tożsamości, które użytkownik może wybrać podczas logowania. Ci dostawcy są enterprise dostawców tożsamości, dostawców tożsamości społecznościowych, takich jak Facebook i Google + lub kont lokalnych (w oparciu nazwa użytkownika lub adres e-mail). |
| *API.idpselections.Signup* | **Wybór dostawcy tożsamości dla rejestracji**. Ta strona zawiera listę dostawców tożsamości, które użytkownik może wybrać podczas tworzenia konta. Ci dostawcy są enterprise dostawców tożsamości, dostawców tożsamości społecznościowych, takich jak Facebook i Google + lub kont lokalnych (w oparciu nazwa użytkownika lub adres e-mail). |
| *api.localaccountpasswordreset* | **Nie pamiętasz hasła strony**. Ta strona zawiera formularz, który użytkownik ma do wypełnienia zainicjować ich resetowania hasła.  |
| *api.localaccountsignin* | **Strona logowania konta lokalnego**. Ta strona zawiera formularz logowania, który użytkownik musi podać podczas logowania się za pomocą konta lokalnego, która jest oparta na adres e-mail lub nazwę użytkownika. Formularz może zawierać pola do wprowadzania tekstu, a w polu wprowadzania hasła. |
| *API.localaccountsignup* | **Stronę tworzenia konta lokalnego konta**. Ta strona zawiera wypełnieniu formularza, który użytkownik musi podać podczas zapisywania się do konta lokalnego, która jest oparta na adres e-mail lub nazwę użytkownika. Formularz może zawierać różne kontrolki wejściowe, takich jak pola do wprowadzania tekstu, pole wprowadzania hasła przycisk radiowy, jednokrotnym zaznaczeniem pola listy rozwijanej i pól wyboru wielokrotnego wyboru. |
| *api.phonefactor* | **Strona uwierzytelniania wieloskładnikowego**. Na tej stronie użytkowników można sprawdzić ich numery telefonów (przy użyciu tekstowych lub głosowych) podczas tworzenia konta lub logowania. |
| *api.selfasserted* | **Strony rejestracji społecznościowych konta**. Ta strona zawiera wypełnieniu formularza, który użytkownik musi podać podczas logowania przy użyciu istniejącego konta od dostawcy tożsamości społecznościowych, takich jak Facebook lub Google +. Ta strona jest podobny do poprzedniej strony rejestracji społecznościowych konta, z wyjątkiem pól wprowadzania hasła. |
| *api.selfasserted.profileupdate* | **Strona aktualizacji profilu**. Ta strona zawiera formularz, który użytkownik może użyć do zaktualizowania swój profil. Ta strona jest podobny do poprzedniej strony rejestracji społecznościowych konta, z wyjątkiem pól wprowadzania hasła. |
| *api.signuporsignin* | **Ujednolicone stronę tworzenia konta lub logowania**.  Ta strona obsługuje zarówno rejestracji i logowania użytkowników można używać w organizacji dostawcy tożsamości, dostawców tożsamości społecznościowych, takich jak Facebook lub Google + lub kont lokalnych.

## <a name="next-steps"></a>Kolejne kroki
[Odwołanie: Zrozumieć, jak niestandardowe zasady współpracują w ramach obsługi tożsamości w B2C](active-directory-b2c-reference-custom-policies-understanding-contents.md)
