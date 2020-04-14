---
title: Konfigurowanie usługi zarządzania interfejsami API przy użyciu platformy Git — Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak zapisać i skonfigurować konfigurację usługi api Management za pomocą git.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: mattfarm
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/12/2019
ms.author: apimpm
ms.openlocfilehash: f948d813ddb4d493b455a4922818e38ac3fd6eaa
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259174"
---
# <a name="how-to-save-and-configure-your-api-management-service-configuration-using-git"></a>Jak konfigurować i zapisywać konfigurację usługi API Management za pomocą narzędzia Git

Każde wystąpienie usługi zarządzania interfejsami API przechowuje bazę danych konfiguracji zawierającą informacje o konfiguracji i metadanych wystąpienia usługi. Zmiany można ww ciągu wystąpienia usługi, zmieniając ustawienie w witrynie Azure portal, przy użyciu polecenia cmdlet programu PowerShell lub wywołanie interfejsu API REST. Oprócz tych metod można również zarządzać konfiguracją wystąpienia usługi przy użyciu git, włączając scenariusze zarządzania usługami, takie jak:

* Wersja konfiguracjna — pobieranie i przechowywanie różnych wersji konfiguracji usługi
* Zbiorcze zmiany konfiguracji — wprowadzanie zmian w wielu częściach konfiguracji usługi w lokalnym repozytorium i integrowanie zmian z powrotem na serwerze za pomocą jednej operacji
* Znany pęk narzędzi Git i przepływ pracy — użyj narzędzi Git i obiegów pracy, które już znasz

Na poniższym diagramie przedstawiono omówienie różnych sposobów konfigurowania wystąpienia usługi zarządzania interfejsami API.

![Konfiguracja git][api-management-git-configure]

Po wprowadzeniu zmian w usłudze przy użyciu portalu Azure portal, polecenia cmdlet programu PowerShell `https://{name}.management.azure-api.net` lub interfejsu API REST, zarządzasz bazą danych konfiguracji usługi przy użyciu punktu końcowego, jak pokazano po prawej stronie diagramu. Po lewej stronie diagramu pokazano, jak można zarządzać konfiguracją usługi za pomocą repozytorium Git i Git dla usługi znajdującej się w `https://{name}.scm.azure-api.net`.

Poniższe kroki zawierają omówienie zarządzania wystąpieniem usługi zarządzania interfejsami API za pomocą git.

1. Dostęp do konfiguracji Git w usłudze
2. Zapisywanie bazy danych konfiguracji usługi w repozytorium Git
3. Klonuj repozytorium Git do lokalnej maszyny
4. Pociągnij najnowsze repozytorium do lokalnego komputera i zaobciągnij zmiany i wypchnij zmiany z powrotem do repozytorium
5. Wdrażanie zmian z repozytorium w bazie danych konfiguracji usługi

W tym artykule opisano sposób włączania i używania git do zarządzania konfiguracją usługi i zawiera odwołanie do plików i folderów w repozytorium Git.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-git-configuration-in-your-service"></a>Dostęp do konfiguracji Git w usłudze

Aby wyświetlić i skonfigurować ustawienia konfiguracji Git, możesz kliknąć menu **Zabezpieczenia** i przejść do karty **Repozytorium konfiguracji.**

![Włącz GIT][api-management-enable-git]

> [!IMPORTANT]
> Wszelkie wpisy tajne, które nie są zdefiniowane jako nazwane wartości będą przechowywane w repozytorium i pozostaną w jego historii, dopóki nie wyłączysz i ponownie włączysz dostęp Git. Nazwane wartości zapewniają bezpieczne miejsce do zarządzania wartościami ciągów stałych, w tym wpisami tajnymi, we wszystkich konfiguracjach interfejsu API i zasadach, dzięki czemu nie trzeba przechowywać ich bezpośrednio w instrukcjach zasad. Aby uzyskać więcej informacji, zobacz [Jak używać nazwanych wartości w zasadach usługi Azure API Management](api-management-howto-properties.md).
>
>

Aby uzyskać informacje na temat włączania lub wyłączania dostępu Git za pomocą interfejsu API REST, zobacz [Włączanie lub wyłączanie dostępu Git za pomocą interfejsu API REST](/rest/api/apimanagement/2019-12-01/tenantaccess?EnableGit).

## <a name="to-save-the-service-configuration-to-the-git-repository"></a>Aby zapisać konfigurację usługi w repozytorium Git

Pierwszym krokiem przed sklonowaniem repozytorium jest zapisanie bieżącego stanu konfiguracji usługi w repozytorium. Kliknij **przycisk Zapisz do repozytorium**.

Wykonuj żądane zmiany na ekranie potwierdzenia i kliknij przycisk **Ok,** aby zapisać.

Po kilku chwilach konfiguracja jest zapisywana i wyświetlany jest stan konfiguracji repozytorium, w tym data i godzina ostatniej zmiany konfiguracji oraz ostatnia synchronizacja między konfiguracją usługi a repozytorium.

Po zapisaniu konfiguracji w repozytorium można ją sklonować.

Aby uzyskać informacje na temat wykonywania tej operacji przy użyciu interfejsu API REST, zobacz [Zatwierdzanie migawki konfiguracji przy użyciu interfejsu API REST](/rest/api/apimanagement/2019-12-01/tenantaccess?CommitSnapshot).

## <a name="to-clone-the-repository-to-your-local-machine"></a>Aby sklonować repozytorium na komputerze lokalnym

Aby sklonować repozytorium, potrzebujesz adresu URL do repozytorium, nazwy użytkownika i hasła. Aby uzyskać nazwę użytkownika i inne poświadczenia, kliknij **poświadczenia programu Access** u góry strony.

Aby **wygenerować** hasło, najpierw upewnij się, że data i godzina wygaśnięcia jest ustawiona na żądaną datę i godzinę wygaśnięcia, a następnie kliknij przycisk **Generuj**.

> [!IMPORTANT]
> Zanotuj to hasło. Po opuszczeniu tej strony hasło nie zostanie ponownie wyświetlone.
>

Poniższe przykłady używają narzędzia Git Bash z [Git dla Windows,](https://www.git-scm.com/downloads) ale możesz użyć dowolnego narzędzia Git, które znasz.

Otwórz narzędzie Git w żądanym folderze i uruchom następujące polecenie, aby sklonować repozytorium git na komputerze lokalnym, używając polecenia dostarczonego przez witrynę Azure portal.

```
git clone https://{name}.scm.azure-api.net/
```

Po wyświetleniu monitu podaj nazwę użytkownika i hasło.

Jeśli pojawią się błędy, spróbuj `git clone` zmodyfikować polecenie, aby uwzględnić nazwę użytkownika i hasło, jak pokazano w poniższym przykładzie.

```
git clone https://username:password@{name}.scm.azure-api.net/
```

Jeśli to zapewnia błąd, spróbuj adres URL kodowania część hasła polecenia. Jednym z szybkich sposobów, aby to zrobić, jest otwarcie programu Visual Studio i wydanie następującego polecenia w **oknie bezpośrednim.** Aby otworzyć **okno natychmiastowe,** otwórz dowolne rozwiązanie lub projekt w programie Visual Studio (lub utwórz nową pustą aplikację konsoli) i wybierz polecenie **Windows**, **Immediate** z menu **debugowania.**

```
?System.Net.WebUtility.UrlEncode("password from the Azure portal")
```

Użyj zakodowanego hasła wraz z nazwą użytkownika i lokalizacją repozytorium, aby utworzyć polecenie git.

```
git clone https://username:url encoded password@{name}.scm.azure-api.net/
```

Po sklonowaniu repozytorium można wyświetlać je i pracować z nim w lokalnym systemie plików. Aby uzyskać więcej informacji, zobacz [Odwołanie do struktury plików i folderów lokalnego repozytorium Git](#file-and-folder-structure-reference-of-local-git-repository).

## <a name="to-update-your-local-repository-with-the-most-current-service-instance-configuration"></a>Aby zaktualizować lokalne repozytorium przy obliczu najnowszej konfiguracji wystąpienia usługi

Jeśli wprowadzasz zmiany w wystąpieniu usługi zarządzania interfejsami API w witrynie Azure portal lub przy użyciu interfejsu API REST, należy zapisać te zmiany w repozytorium, zanim będzie można zaktualizować lokalne repozytorium za pomocą najnowszych zmian. Aby to zrobić, kliknij przycisk **Zapisz konfigurację do repozytorium** na karcie **Repozytorium konfiguracji** w witrynie Azure Portal, a następnie wydaj następujące polecenie w lokalnym repozytorium.

```
git pull
```

Przed `git pull` uruchomieniem upewnij się, że znajdujesz się w folderze dla lokalnego repozytorium. Jeśli `git clone` właśnie ukończyłeś polecenie, musisz zmienić katalog na repozytorium, uruchamiając polecenie podobne do poniższego.

```
cd {name}.scm.azure-api.net/
```

## <a name="to-push-changes-from-your-local-repo-to-the-server-repo"></a>Aby wypchnąć zmiany z lokalnego repozytorium do repozytorium serwera
Aby wypchnąć zmiany z repozytorium lokalnego do repozytorium serwera, należy zatwierdzić zmiany, a następnie wypchnąć je do repozytorium serwera. Aby zatwierdzić zmiany, otwórz narzędzie do poleceń Git, przełącz się do katalogu lokalnego repozytorium i wydaj następujące polecenia.

```
git add --all
git commit -m "Description of your changes"
```

Aby wypchnąć wszystkie zatwierdzenia do serwera, uruchom następujące polecenie.

```
git push
```

## <a name="to-deploy-any-service-configuration-changes-to-the-api-management-service-instance"></a>Aby wdrożyć zmiany konfiguracji usługi w wystąpieniu usługi api Management

Po zaajsnięciu i wypchnięciu lokalnych zmian do repozytorium serwera można je wdrożyć w wystąpieniu usługi zarządzania interfejsami API.

Aby uzyskać informacje na temat wykonywania tej operacji przy użyciu interfejsu API REST, zobacz [Wdrażanie zmian git w bazie danych konfiguracji przy użyciu interfejsu API REST](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/tenantconfiguration).

## <a name="file-and-folder-structure-reference-of-local-git-repository"></a>Odwołanie do struktury plików i folderów lokalnego repozytorium Git

Pliki i foldery w lokalnym repozytorium git zawierają informacje o konfiguracji wystąpienia usługi.

| Element | Opis |
| --- | --- |
| główny folder zarządzania interfejsami api |Zawiera konfigurację najwyższego poziomu dla wystąpienia usługi |
| folder apis |Zawiera konfigurację apis w wystąpieniu usługi |
| folder grupy |Zawiera konfigurację grup w wystąpieniu usługi |
| folder zasad |Zawiera zasady w wystąpieniu usługi |
| folder portalStyles |Zawiera konfigurację dostosowań portalu dewelopera w wystąpieniu usługi |
| folder produktów |Zawiera konfigurację produktów w wystąpieniu usługi |
| folder szablonów |Zawiera konfigurację szablonów wiadomości e-mail w wystąpieniu usługi |

Każdy folder może zawierać jeden lub więcej plików, a w niektórych przypadkach jeden lub więcej folderów, na przykład folder dla każdego interfejsu API, produktu lub grupy. Pliki w każdym folderze są specyficzne dla typu jednostki opisanego przez nazwę folderu.

| Typ pliku | Przeznaczenie |
| --- | --- |
| json |Informacje o konfiguracji odpowiedniej encji |
| html |Opisy dotyczące encji, często wyświetlane w portalu dla deweloperów |
| xml |Instrukcje zasad |
| css |Arkusze stylów do dostosowywania portalu dla deweloperów |

Pliki te można tworzyć, usuwać, edytować i zarządzać w lokalnym systemie plików, a zmiany wdrożone z powrotem do wystąpienia usługi zarządzania interfejsami API.

> [!NOTE]
> Następujące jednostki nie są zawarte w repozytorium Git i nie można skonfigurować przy użyciu git.
>
> * [Użytkownicy](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/user)
> * [Subskrypcje](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/subscription)
> * [Nazwane wartości](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/property)
> * Encje portalu deweloperów inne niż style
>

### <a name="root-api-management-folder"></a>Główny folder zarządzania interfejsami api
Folder `api-management` główny zawiera `configuration.json` plik zawierający informacje najwyższego poziomu o wystąpieniu usługi w następującym formacie.

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

Pierwsze cztery ustawienia`RegistrationEnabled` `UserRegistrationTerms`( `UserRegistrationTermsEnabled`, `UserRegistrationTermsConsentRequired`, , i ) są mapowane do następujących ustawień na karcie **Tożsamości** w sekcji **Zabezpieczenia.**

| Ustawienie tożsamości | Mapy do |
| --- | --- |
| RegistrationEnabled |Obecność dostawcy **tożsamości nazwy użytkownika i hasła** |
| Ustawienia rejestracji użytkowników |**Warunki użytkowania w skrzynce** tekstowej rejestracji użytkownika |
| UserRegistrationTermsEnabled |**Pole** wyboru Pokaż warunki użytkowania na stronie rejestracji |
| Wymagane ustawienia użytkownika |Pole wyboru **Wymagaj zgody** |
| Wymagajużydnikusyer |**Przekierowywanie użytkowników anonimowych do** strony logowania |

Następne cztery ustawienia`DelegationEnabled` `DelegationUrl`( `DelegatedSubscriptionEnabled`, `DelegationValidationKey`, , i ) są mapowane do następujących ustawień na karcie **Delegowanie** w sekcji **Zabezpieczenia.**

| Ustawienie delegowania | Mapy do |
| --- | --- |
| DelegowanieWnie |Pole wyboru **& rejestracji delegowania logowania** |
| DelegaturaUrl |Pole **tekstowe adresu URL punktu końcowego delegowania** |
| DelegowaneSubskrypcjaWniejszą |Pole wyboru **Delegowanie subskrypcji produktu** |
| Klucz dotacji delegowania |**Pole tekstowe Klucz sprawdzania poprawności delegowania** |

Ostateczne ustawienie `$ref-policy`, mapuje plik instrukcji zasad globalnych dla wystąpienia usługi.

### <a name="apis-folder"></a>folder apis
Folder `apis` zawiera folder dla każdego interfejsu API w wystąpieniu usługi, który zawiera następujące elementy.

* `apis\<api name>\configuration.json`- jest to konfiguracja interfejsu API i zawiera informacje o adresie URL usługi wewnętrznej bazy danych i operacji. Jest to te same informacje, które zostaną zwrócone, `export=true` jeśli `application/json` były do wywołania Pobierz określonego [interfejsu API](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/apis/get) w formacie.
* `apis\<api name>\api.description.html`- jest to opis API i odpowiada `description` właściwości [jednostki API](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table.entityproperty).
* `apis\<api name>\operations\`- ten `<operation name>.description.html` folder zawiera pliki, które mapują operacje w interfejsie API. Każdy plik zawiera opis pojedynczej operacji w interfejsie `description` API, który mapuje do właściwości [jednostki operacji](https://docs.microsoft.com/rest/api/visualstudio/operations/list#operationproperties) w interfejsie API REST.

### <a name="groups-folder"></a>folder grupy
Folder `groups` zawiera folder dla każdej grupy zdefiniowanej w wystąpieniu usługi.

* `groups\<group name>\configuration.json`- jest to konfiguracja dla grupy. Jest to te same informacje, które zostaną zwrócone, jeśli były do wywołania [Get operacji określonej grupy.](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/group/get)
* `groups\<group name>\description.html`- jest to opis grupy i odpowiada `description` właściwości [jednostki grupy](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity).

### <a name="policies-folder"></a>folder zasad
Folder `policies` zawiera instrukcje zasad dla wystąpienia usługi.

* `policies\global.xml`- zawiera zasady zdefiniowane w zakresie globalnym dla wystąpienia usługi.
* `policies\apis\<api name>\`- jeśli masz jakieś zasady zdefiniowane w zakresie INTERFEJSU API, są one zawarte w tym folderze.
* `policies\apis\<api name>\<operation name>\`folder - jeśli masz żadnych zasad zdefiniowanych w zakresie operacji, są one zawarte w tym folderze w `<operation name>.xml` plikach, które mapują do instrukcji zasad dla każdej operacji.
* `policies\products\`- jeśli masz jakieś zasady zdefiniowane w zakresie produktu, są `<product name>.xml` one zawarte w tym folderze, który zawiera pliki, które mapują do instrukcji zasad dla każdego produktu.

### <a name="portalstyles-folder"></a>folder portalStyles
Folder `portalStyles` zawiera arkusze konfiguracji i stylów dla dostosowań portalu dewelopera dla wystąpienia usługi.

* `portalStyles\configuration.json`- zawierają nazwy arkuszy stylów używanych przez portal deweloperów
* `portalStyles\<style name>.css`- `<style name>.css` każdy plik zawiera style`Preview.css` dla `Production.css` portalu dewelopera ( i domyślnie).

### <a name="products-folder"></a>folder produktów
Folder `products` zawiera folder dla każdego produktu zdefiniowanego w wystąpieniu usługi.

* `products\<product name>\configuration.json`- jest to konfiguracja produktu. Jest to te same informacje, które zostaną zwrócone, jeśli były do wywołania [Get operacji określonego produktu.](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/product/get)
* `products\<product name>\product.description.html`- jest to opis produktu i odpowiada `description` właściwości [podmiotu produktu](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-product-entity) w INTERFEJSIE REST.

### <a name="templates"></a>szablonów
Folder `templates` zawiera konfigurację [szablonów wiadomości e-mail](api-management-howto-configure-notifications.md) wystąpienia usługi.

* `<template name>\configuration.json`- jest to konfiguracja szablonu wiadomości e-mail.
* `<template name>\body.html`- jest to treść szablonu wiadomości e-mail.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać informacje na temat innych sposobów zarządzania wystąpieniem usługi, zobacz:

* Zarządzanie wystąpieniem usługi przy użyciu następujących poleceń cmdlet programu PowerShell
  * [Wdrażanie usług — dokumentacja poleceń cmdlet programu PowerShell](https://docs.microsoft.com/powershell/module/wds)
  * [Zarządzanie usługami — dokumentacja poleceń cmdlet programu PowerShell](https://docs.microsoft.com/powershell/azure/servicemanagement/overview)
* Zarządzanie wystąpieniem usługi przy użyciu interfejsu API REST
  * [Odwołanie do interfejsu API REST zarządzania interfejsem API](/rest/api/apimanagement/)


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




