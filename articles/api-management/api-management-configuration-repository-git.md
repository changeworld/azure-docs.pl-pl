---
title: Konfigurowanie usługi API Management przy użyciu narzędzia Git — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zapisywanie i konfigurowanie konfiguracji usługi API Management przy użyciu narzędzia Git.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: mattfarm
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2019
ms.author: apimpm
ms.openlocfilehash: adf4d8d5cfcef2dde8193ce1b7f2805a44e2d93d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60657926"
---
# <a name="how-to-save-and-configure-your-api-management-service-configuration-using-git"></a>Zapisywanie i konfigurowanie konfiguracji usługi API Management przy użyciu narzędzia Git

Każde wystąpienie usługi API Management obsługuje bazę danych konfiguracji, który zawiera informacje o konfiguracji i metadanych dla wystąpienia usługi. Można zmodyfikować wystąpienie usługi, zmieniając ustawienia w witrynie Azure portal, za pomocą polecenia cmdlet programu PowerShell lub wywołania interfejsu API REST. Oprócz tych metod można również zarządzać przy użyciu narzędzia Git, takich jak włączenie scenariuszy zarządzania usługi konfiguracji wystąpienia usługi:

* Przechowywanie wersji konfiguracji — Pobierz i Zapisz różne wersje konfiguracji usługi
* Zbiorcze zmiany w konfiguracji — wprowadzić zmiany do wielu fragmentów konfiguracji usługi w lokalnym repozytorium i integracji zmian do serwera przy użyciu jednej operacji
* Dobrze znanych łańcuch narzędzi programu Git i przepływu pracy — skorzystaj z narzędzia Git i przepływów pracy, które już znasz z

Na poniższym diagramie przedstawiono omówienie różnych sposobów, aby skonfigurować swoje wystąpienie usługi API Management.

![Konfigurowanie usługi Git][api-management-git-configure]

Po wprowadzeniu zmian do usługi za pomocą witryny Azure portal, poleceń cmdlet programu PowerShell lub interfejsu API REST zarządzania swojej usługi konfiguracji bazy danych przy użyciu `https://{name}.management.azure-api.net` punktu końcowego, jak pokazano po prawej stronie diagramu. Po lewej stronie diagramu ilustruje, jak można zarządzać konfiguracji usługi za pomocą narzędzia Git i repozytorium Git na potrzeby usługi znajdujące się na `https://{name}.scm.azure-api.net`.

W poniższych krokach przedstawiono omówienie zarządzania wystąpienia usługi API Management przy użyciu narzędzia Git.

1. Dostęp do konfiguracji usługi Git w usłudze
2. Zapisywanie bazy danych konfiguracji usług do repozytorium Git
3. Sklonuj repozytorium Git na komputerze lokalnym
4. Ściągnij najnowsze repozytorium do komputera lokalnego i zatwierdzać i wypychać zmiany z powrotem do repozytorium
5. Wdrażanie zmiany z repozytorium w bazie danych konfiguracji usługi

W tym artykule opisano, jak włączyć i zarządzać konfiguracją usługi za pomocą narzędzia Git i zapewniający odniesienie dla plików i folderów w repozytorium Git.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-git-configuration-in-your-service"></a>Dostęp do konfiguracji usługi Git w usłudze

Aby wyświetlić i skonfigurować ustawienia konfiguracji usługi Git, możesz kliknąć **zabezpieczeń** menu i przejdź do **repozytorium konfiguracji** kartę.

![Włącz system GIT][api-management-enable-git]

> [!IMPORTANT]
> Jakichkolwiek kluczy tajnych, które nie są zdefiniowane jako o nazwie wartości będą przechowywane w repozytorium i pozostanie w jego historię aż Wyłącz i ponownie włączyć dostęp usługi Git. Nazwane wartości zapewniają bezpiecznym miejscu, aby zarządzać stałe wartości ciągów, w tym wpisów tajnych oraz ich we wszystkich Konfiguracja interfejsu API i zasady, dzięki czemu nie trzeba przechowywać je bezpośrednio w zasadach zestawienia. Aby uzyskać więcej informacji, zobacz [sposób użycia wartości o nazwie w zasadach usługi Azure API Management](api-management-howto-properties.md).
>
>

Aby uzyskać informacje dotyczące włączania lub wyłączania dostępu do usługi Git przy użyciu interfejsu API REST, zobacz [Włącz lub wyłącz dostęp do usługi Git przy użyciu interfejsu API REST](/rest/api/apimanagement/tenantaccess?EnableGit).

## <a name="to-save-the-service-configuration-to-the-git-repository"></a>Aby zapisać konfigurację usługi do repozytorium Git

Pierwszym krokiem przed klonowaniem repozytorium jest zapisanie bieżącego stanu konfiguracji usługi do repozytorium. Kliknij przycisk **zapisać repozytorium**.

Wprowadź żądane zmiany na ekranie potwierdzenia, a następnie kliknij przycisk **Ok** do zapisania.

Po kilku chwilach konfiguracja jest zapisywana i wyświetlany jest stan konfiguracji repozytorium, w tym datę i godzinę ostatniej zmiany konfiguracji i ostatniej synchronizacji między konfigurację usługi i repozytorium.

Po zapisaniu do repozytorium konfiguracji mogą być klonowane.

Aby uzyskać informacje na wykonanie tej operacji za pomocą interfejsu API REST, zobacz [konfiguracji zatwierdzania migawki za pomocą interfejsu API REST](/rest/api/apimanagement/tenantaccess?CommitSnapshot).

## <a name="to-clone-the-repository-to-your-local-machine"></a>Aby sklonować repozytorium na komputerze lokalnym

Klonowanie repozytorium, należy adres URL repozytorium, nazwę użytkownika i hasło. Aby uzyskać nazwę użytkownika i inne poświadczenia, kliknij polecenie **poświadczenia dostępu** w górnej części strony.

Aby wygenerować hasła, najpierw upewnij się, że **wygaśnięcia** jest ustawiona na odpowiednią datę i godzinę wygaśnięcia, a następnie kliknij przycisk **Generuj**.

> [!IMPORTANT]
> Zanotuj hasło. Gdy opuścisz tę stronę hasło nie zostanie ponownie wyświetlone.
>

W poniższych przykładach używane narzędzie powłoki Git Bash z [Git dla Windows](https://www.git-scm.com/downloads) , ale można użyć dowolnego narzędzia Git, które znasz.

Otwórz narzędzie Git w żądany folder, a następnie uruchom następujące polecenie, aby sklonować repozytorium git na komputerze lokalnym przy użyciu polecenia udostępnionych przez portal systemu Azure.

```
git clone https://{name}.scm.azure-api.net/
```

Podaj nazwę użytkownika i hasło po wyświetleniu monitu.

Jeśli otrzymujesz błędy, spróbuj zmodyfikować swoje `git clone` polecenie, aby uwzględnić nazwę użytkownika i hasło, jak pokazano w poniższym przykładzie.

```
git clone https://username:password@{name}.scm.azure-api.net/
```

Jeśli zapewnia to błąd, spróbuj hasła będącego częścią polecenie kodowania adresu URL. Jednym ze sposobów szybkiego, w tym celu jest Otwórz program Visual Studio i wydać następujące polecenie w **bezpośrednim**. Aby otworzyć **bezpośrednim**, otwórz dowolnego rozwiązania lub projektu w programie Visual Studio (lub Utwórz nową aplikację konsoli pusty) i wybierz polecenie **Windows**, **bezpośrednie** z **Debugowania** menu.

```
?System.NetWebUtility.UrlEncode("password from the Azure portal")
```

Użyj zakodowany hasła wraz z lokalizacji nazwy i repozytorium użytkownika do konstruowania polecenia git.

```
git clone https://username:url encoded password@{name}.scm.azure-api.net/
```

Po repozytorium zostanie sklonowany, można przeglądać i pracować w lokalnym systemie plików. Aby uzyskać więcej informacji, zobacz [plików i folderów struktury odwołania do lokalnego repozytorium Git](#file-and-folder-structure-reference-of-local-git-repository).

## <a name="to-update-your-local-repository-with-the-most-current-service-instance-configuration"></a>Aby zaktualizować repozytorium lokalne przy użyciu najnowszej konfiguracji wystąpienia usługi

Po wprowadzeniu zmian do wystąpienia usługi API Management w witrynie Azure portal lub za pomocą interfejsu API REST, aby można było zaktualizować Twojego lokalnego repozytorium z najnowszymi zmianami należy zapisać te zmiany do repozytorium. Aby to zrobić, kliknij przycisk **Zapisz konfigurację do repozytorium** na **repozytorium konfiguracji** karcie w witrynie Azure portal, a następnie należy wydać następujące polecenie w repozytorium lokalnym.

```
git pull
```

Przed uruchomieniem `git pull` upewnij się, że jesteś w folderze lokalnego repozytorium. Jeśli właśnie został ukończony `git clone` polecenia, a następnie należy zmienić katalog do repozytorium, uruchamiając polecenia podobnego do poniższego.

```
cd {name}.scm.azure-api.net/
```

## <a name="to-push-changes-from-your-local-repo-to-the-server-repo"></a>Aby wypchnąć zmiany z repozytorium lokalnego do repozytorium serwera
Wypychania zmian z lokalnego repozytorium do repozytorium serwera, należy zatwierdzić zmiany, a następnie Wypychaj je do repozytorium serwera. Aby zatwierdzić zmiany, Otwórz narzędzie polecenia Git, przejdź do katalogu lokalnego repozytorium, a następnie uruchom następujące polecenia.

```
git add --all
git commit -m "Description of your changes"
```

Aby wypchnąć wszystkie zatwierdzenia na serwerze, uruchom następujące polecenie.

```
git push
```

## <a name="to-deploy-any-service-configuration-changes-to-the-api-management-service-instance"></a>Aby wdrożyć zmiany konfiguracji usługi wystąpienia usługi API Management

Po lokalne zmiany są zatwierdzeniu i wypchnięciu do repozytorium serwera, możesz je wdrożyć do wystąpienia usługi API Management.

Aby uzyskać informacje na wykonanie tej operacji za pomocą interfejsu API REST, zobacz [wdrożenia Git zmiany konfiguracji bazy danych przy użyciu interfejsu API REST](https://docs.microsoft.com/rest/api/apimanagement/tenantconfiguration).

## <a name="file-and-folder-structure-reference-of-local-git-repository"></a>Odwołanie struktury pliku i folderu lokalnego repozytorium Git

Pliki i foldery w lokalnym repozytorium git zawiera informacje o konfiguracji dotyczące wystąpienia usługi.

| Element | Opis |
| --- | --- |
| głównego folderu usługi api management |Zawiera konfigurację najwyższego poziomu dla wystąpienia usługi |
| folder interfejsów API |Zawiera konfigurację dla interfejsów API w wystąpieniu usługi |
| folder grupy |Zawiera konfigurację dla grup w wystąpieniu usługi |
| folder zasady |Zawiera zasady w wystąpieniu usługi |
| portalStyles folder |Zawiera konfigurację Dostosowywanie portalu dla deweloperów w wystąpieniu usługi |
| folder produktów |Zawiera konfigurację dla produktów w wystąpieniu usługi |
| folder szablonów |Zawiera konfigurację szablony wiadomości e-mail w wystąpieniu usługi |

Każdy folder może zawierać jeden lub więcej plików, a w niektórych przypadkach jeden lub więcej folderów, na przykład folder dla każdego interfejsu API, produktów lub grupy. Pliki znajdujące się w każdym folderze są specyficzne dla typu jednostki opisane według nazwy folderu.

| Typ pliku | Przeznaczenie |
| --- | --- |
| json |Informacje o konfiguracji dotyczące odpowiedniej jednostki |
| html |Opisy o tej jednostce często wyświetlany w portalu dla deweloperów |
| xml |Instrukcje zasad |
| CSS |Arkusze stylów dla Dostosowywanie portalu dla deweloperów |

Te pliki można utworzyć, usunięte, edytować i zarządzane w lokalnym systemie plików, a zmiany wdrożone wystąpienia usługi API Management.

> [!NOTE]
> Następujące jednostki nie znajdują się w repozytorium Git i nie można skonfigurować przy użyciu narzędzia Git.
>
> * [Użytkownicy](https://docs.microsoft.com/en-us/rest/api/apimanagement/user)
> * [Subskrypcje](https://docs.microsoft.com/en-us/rest/api/apimanagement/subscription)
> * [Nazwane wartości](https://docs.microsoft.com/en-us/rest/api/apimanagement/property)
> * Jednostki portalu dla deweloperów, inne niż style
>

### <a name="root-api-management-folder"></a>głównego folderu usługi api management
Katalog główny `api-management` folder zawiera `configuration.json` pliku, który zawiera najwyższego poziomu informacji o wystąpieniu usługi, w następującym formacie.

```json
{
  "settings": {
    "RegistrationEnabled": "True",
    "UserRegistrationTerms": null,
    "UserRegistrationTermsEnabled": "False",
    "UserRegistrationTermsConsentRequired": "False",
    "DelegationEnabled": "False",
    "DelegationUrl": "",
    "DelegatedSubscriptionEnabled": "False",
    "DelegationValidationKey": "",
    "RequireUserSigninEnabled": "false"
  },
  "$ref-policy": "api-management/policies/global.xml"
}
```

Pierwsze cztery ustawienia (`RegistrationEnabled`, `UserRegistrationTerms`, `UserRegistrationTermsEnabled`, i `UserRegistrationTermsConsentRequired`) mapy w następujących ustawieniach na **tożsamości** karcie **zabezpieczeń** sekcji.

| Ustawienia tożsamości | Mapuje |
| --- | --- |
| RegistrationEnabled |Obecność **nazwy użytkownika i hasło** dostawcy tożsamości |
| UserRegistrationTerms |**Warunki użytkowania podczas tworzenia konta użytkownika** textbox |
| UserRegistrationTermsEnabled |**Wyświetlanie warunków użytkowania na stronie tworzenia konta** pola wyboru |
| UserRegistrationTermsConsentRequired |**Wymagaj zgody** pola wyboru |
| RequireUserSigninEnabled |**Przekieruj użytkowników anonimowych do strony logowania** pola wyboru |

Następne cztery ustawienia (`DelegationEnabled`, `DelegationUrl`, `DelegatedSubscriptionEnabled`, i `DelegationValidationKey`) mapy w następujących ustawieniach na **delegowania** karcie **zabezpieczeń** sekcji.

| Ustawienia delegowania | Mapuje |
| --- | --- |
| DelegationEnabled |**Delegat, zaloguj się i rejestrowanie** pola wyboru |
| DelegationUrl |**Adres URL punktu końcowego delegowania** textbox |
| DelegatedSubscriptionEnabled |**Deleguj subskrypcję produktu** pola wyboru |
| DelegationValidationKey |**Delegowanie klucz sprawdzania poprawności** textbox |

Ustawienie ostatecznego `$ref-policy`, mapy do pliku instrukcje zasad globalnych dla wystąpienia usługi.

### <a name="apis-folder"></a>folder interfejsów API
`apis` Folder zawiera folder dla każdego interfejsu API w wystąpieniu usługi, która zawiera następujące elementy.

* `apis\<api name>\configuration.json` — to jest Konfiguracja interfejsu API i zawiera informacje dotyczące adresu URL usługi wewnętrznej bazy danych i operacje. Jest to te same informacje, które zostaną zwrócone, jeśli nastąpi wywołanie [uzyskać konkretnego interfejsu API](https://docs.microsoft.com/rest/api/apimanagement/apis/get) z `export=true` w `application/json` formatu.
* `apis\<api name>\api.description.html` — to jest opis interfejsu API i odpowiada `description` właściwość [jednostki interfejsu API](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table._entity_property).
* `apis\<api name>\operations\` — Ten folder zawiera `<operation name>.description.html` pliki, które są mapowane na operacje w interfejsie API. Każdy plik zawiera opis jednej operacji w interfejsie API, który mapuje `description` właściwość [jednostki operacji](https://docs.microsoft.com/rest/api/visualstudio/operations/list#operationproperties) w interfejsie API REST.

### <a name="groups-folder"></a>folder grupy
`groups` Zawiera folder dla każdej grupy zdefiniowane w wystąpieniu usługi.

* `groups\<group name>\configuration.json` — jest to konfiguracja dla grupy. Jest to te same informacje, które zostaną zwrócone, jeśli nastąpi wywołanie [Pobierz określoną grupę](https://docs.microsoft.com/rest/api/apimanagement/group/get) operacji.
* `groups\<group name>\description.html` — to jest opis grupy i odpowiada `description` właściwość [grupy jednostek](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity).

### <a name="policies-folder"></a>folder zasady
`policies` Folder zawiera instrukcje zasad dla swojego wystąpienia usługi.

* `policies\global.xml` -zawiera zasady zdefiniowany w globalnym zakresie danego wystąpienia usługi.
* `policies\apis\<api name>\` -Jeśli masz jakiekolwiek zasady zdefiniowany w zakresie interfejsu API, znajdują się w tym folderze.
* `policies\apis\<api name>\<operation name>\` folder — Jeśli masz jakiekolwiek zasady zdefiniowany w zakresie operacji, znajdują się w tym folderze `<operation name>.xml` pliki, które mapują do deklaracji zasad dla każdej operacji.
* `policies\products\` -Jeśli masz jakiekolwiek zasady zdefiniowany w zakresie produktu, znajdują się w tym folderze, który zawiera `<product name>.xml` pliki, które mapują do deklaracji zasad dla każdego produktu.

### <a name="portalstyles-folder"></a>portalStyles folder
`portalStyles` Folder zawiera arkusze konfiguracji i stylu dla Dostosowywanie portalu dla deweloperów wystąpienia usługi.

* `portalStyles\configuration.json` -zawiera nazwy arkuszy stylów, używanym w portalu dla deweloperów
* `portalStyles\<style name>.css` -każdego `<style name>.css` plik zawiera style portalu dla deweloperów (`Preview.css` i `Production.css` domyślnie).

### <a name="products-folder"></a>folder produktów
`products` Zawiera folder dla każdego produktu z definicją w wystąpieniu usługi.

* `products\<product name>\configuration.json` — jest to konfiguracja dla produktu. Jest to te same informacje, które zostaną zwrócone, jeśli nastąpi wywołanie [pobrać określonego produktu](https://docs.microsoft.com/rest/api/apimanagement/product/get) operacji.
* `products\<product name>\product.description.html` — to jest opis produktu i odpowiada `description` właściwość [jednostkę produktu](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-product-entity) w interfejsie API REST.

### <a name="templates"></a>templates
`templates` Folder zawiera konfigurację [szablonów wiadomości e-mail](api-management-howto-configure-notifications.md) wystąpienia usługi.

* `<template name>\configuration.json` — jest to konfiguracja szablonu wiadomości e-mail.
* `<template name>\body.html` — to jest treść szablonu wiadomości e-mail.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać informacji na temat innych sposobów zarządzania wystąpienia usługi zobacz:

* Zarządzanie wystąpienia usługi przy użyciu następujących poleceń cmdlet programu PowerShell
  * [Wdrożenie usługi w dokumentacji dotyczącej poleceń cmdlet programu PowerShell](https://docs.microsoft.com/powershell/module/wds)
  * [Zarządzanie usługą Dokumentacja poleceń cmdlet programu PowerShell](https://docs.microsoft.com/powershell/azure/servicemanagement/overview)
* Zarządzanie przy użyciu interfejsu API REST wystąpienia usługi
  * [Dokumentacja interfejsu API REST zarządzania interfejsu API](/rest/api/apimanagement/)


[api-management-enable-git]: ./media/api-management-configuration-repository-git/api-management-enable-git.png
[api-management-git-enabled]: ./media/api-management-configuration-repository-git/api-management-git-enabled.png
[api-management-save-configuration]: ./media/api-management-configuration-repository-git/api-management-save-configuration.png
[api-management-save-configuration-confirm]: ./media/api-management-configuration-repository-git/api-management-save-configuration-confirm.png
[api-management-configuration-status]: ./media/api-management-configuration-repository-git/api-management-configuration-status.png
[api-management-configuration-git-clone]: ./media/api-management-configuration-repository-git/api-management-configuration-git-clone.png
[api-management-generate-password]: ./media/api-management-configuration-repository-git/api-management-generate-password.png
[api-management-password]: ./media/api-management-configuration-repository-git/api-management-password.png
[api-management-git-configure]: ./media/api-management-configuration-repository-git/api-management-git-configure.png
[api-management-configuration-deploy]: ./media/api-management-configuration-repository-git/api-management-configuration-deploy.png
[api-management-identity-settings]: ./media/api-management-configuration-repository-git/api-management-identity-settings.png
[api-management-delegation-settings]: ./media/api-management-configuration-repository-git/api-management-delegation-settings.png
[api-management-git-icon-enable]: ./media/api-management-configuration-repository-git/api-management-git-icon-enable.png




