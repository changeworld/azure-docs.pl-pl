---
title: Konfigurowanie usługi Zarządzanie interfejsami API przy użyciu narzędzia Git - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zapisać i skonfigurować konfiguracji usługi Zarządzanie interfejsami API przy użyciu narzędzia Git.
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
ms.date: 02/02/2018
ms.author: apimpm
ms.openlocfilehash: 0165de82850c0c80052564c5f31a5e5cf5effb11
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938312"
---
# <a name="how-to-save-and-configure-your-api-management-service-configuration-using-git"></a>Zapisz i konfigurowaniu konfiguracji usługi Zarządzanie interfejsami API przy użyciu narzędzia Git

Każde wystąpienie usługi Zarządzanie interfejsami API utrzymuje bazę danych konfiguracji, który zawiera informacje o konfiguracji i metadanych dla wystąpienia usługi. Zmiany mogą być wprowadzane do wystąpienia usługi zmiana ustawienia w portalu Azure, korzystając z polecenia cmdlet programu PowerShell lub wywołania interfejsu API REST. Oprócz tych metod można również zarządzać konfiguracji wystąpienia usługi przy użyciu narzędzia Git, takie jak włączanie scenariusze zarządzania usługi:

* Przechowywanie wersji konfiguracji — Pobierz i przechowywać różne wersje konfiguracji usługi
* Zbiorcze zmiany konfiguracji — wprowadzić zmiany w wielu części konfiguracji usługi w lokalnym repozytorium i integracji zmian z powrotem na serwer z jednej operacji
* Znane Git łańcuch narzędzi i przepływ pracy - Użyj narzędzia Git i przepływy pracy, które znasz już

Na poniższym diagramie przedstawiono przegląd różnych sposobów, aby skonfigurować wystąpienie usługi Zarządzanie interfejsami API.

![Skonfiguruj Git][api-management-git-configure]

Po wprowadzeniu zmian w usłudze przy użyciu portalu Azure, poleceń cmdlet programu PowerShell lub interfejsu API REST zarządzania z usługi konfiguracji bazy danych przy użyciu `https://{name}.management.azure-api.net` punktu końcowego, jak pokazano po prawej stronie diagramu. W lewej części diagram ilustruje, jak można zarządzać przy użyciu narzędzia Git konfiguracji usługi i repozytorium Git dla usługi znajduje się w `https://{name}.scm.azure-api.net`.

Poniższe kroki zawierają omówienie zarządzania wystąpienia usługi Zarządzanie interfejsami API przy użyciu narzędzia Git.

1. Konfiguracja Git dostępu w usłudze
2. Zapisz bazę danych konfiguracji usługi do repozytorium Git
3. Klonowanie repozytorium Git na komputerze lokalnym
4. Pobierać najnowsze repozytorium do komputera lokalnego i zatwierdzania i wypychania zmian z powrotem do Twojego repozytorium
5. Wdrażanie zmiany z Twojego repozytorium do bazy danych konfiguracji usługi

W tym artykule opisano, jak włączyć i umożliwia zarządzanie konfiguracji usługi Git i zawiera odwołanie do plików i folderów w repozytorium Git.

## <a name="access-git-configuration-in-your-service"></a>Konfiguracja Git dostępu w usłudze

Aby wyświetlić i skonfigurować ustawienia konfiguracji Git, możesz kliknąć **zabezpieczeń** menu i przejdź do **repozytorium konfiguracji** kartę.

![Włącz GIT][api-management-enable-git]

> [!IMPORTANT]
> Wszystkie klucze tajne, które nie są zdefiniowane jako właściwości będą przechowywane w repozytorium i pozostanie w jego historii dopóki Wyłącz i ponownie włączyć dostęp Git. Właściwości zapewniają bezpiecznym miejscu, aby zarządzać stałych wartości ciągów, tym klucze tajne, we wszystkich Konfiguracja interfejsu API i zasad, więc nie trzeba przechowywać je bezpośrednio w instrukcjach użytkownika zasady. Aby uzyskać więcej informacji, zobacz [sposób użycia właściwości w ramach zasad usługi Azure API Management](api-management-howto-properties.md).
> 
> 

Aby uzyskać informacje na włączanie lub wyłączanie dostępu Git przy użyciu interfejsu API REST, zobacz [Włączanie lub wyłączanie dostępu Git przy użyciu interfejsu API REST](https://msdn.microsoft.com/library/dn781420.aspx#EnableGit).

## <a name="to-save-the-service-configuration-to-the-git-repository"></a>Aby zapisać konfigurację usługi do repozytorium Git

Pierwszym krokiem przed klonowanie repozytorium jest zapisanie bieżącego stanu konfiguracji usługi do repozytorium. Kliknij przycisk **zapisać repozytorium**.

Wprowadź żądane zmiany na ekranie potwierdzenia i kliknij przycisk **Ok** do zapisania.

Po kilku chwilach konfiguracji jest zapisywana i wyświetlany jest stan konfiguracji repozytorium, w tym datę i godzinę ostatniej zmiany konfiguracji i ostatniej synchronizacji konfiguracji usługi i repozytorium.

Po zapisaniu konfiguracji do repozytorium można sklonować.

Aby uzyskać informacje na wykonanie tej operacji przy użyciu interfejsu API REST, zobacz [migawki za pomocą interfejsu API REST konfiguracji zatwierdzania](https://msdn.microsoft.com/library/dn781420.aspx#CommitSnapshot).

## <a name="to-clone-the-repository-to-your-local-machine"></a>Klonowanie repozytorium na komputerze lokalnym

Klonowanie repozytorium, musisz adres URL repozytorium, nazwę użytkownika i hasła. Aby uzyskać nazwę użytkownika i inne poświadczenia, kliknij polecenie **dostęp do poświadczeń** górnej części strony.  
 
Aby wygenerować hasła, najpierw upewnij się, że **wygaśnięcia** jest ustawiona na odpowiednią datę i godzinę wygaśnięcia, a następnie kliknij przycisk **Generuj**.

> [!IMPORTANT]
> Zanotuj to hasło. Gdy opuścisz tę stronę hasło nie zostanie ponownie wyświetlone.
> 

W poniższych przykładach użyto narzędzia Git Bash z [Git dla systemu Windows](http://www.git-scm.com/downloads) , ale można użyć dowolnego narzędzia Git, które znasz.

Otwórz narzędzie Git w odpowiedni folder i uruchom następujące polecenie, aby Klonuj repozytorium git na komputerze lokalnym za pomocą polecenia udostępnionych przez Azure portal.

```
git clone https://bugbashdev4.scm.azure-api.net/
```

Podaj nazwę użytkownika i hasło po wyświetleniu monitu.

Jeśli wystąpią błędy, spróbuj zmodyfikować Twoje `git clone` polecenie, aby uwzględnić nazwę użytkownika i hasło, jak pokazano w poniższym przykładzie.

```
git clone https://username:password@bugbashdev4.scm.azure-api.net/
```

Jeśli to zawiera błąd, spróbuj kodowania hasła część polecenia URL. To jeden szybko, w tym celu otwórz program Visual Studio i wydać następujące polecenie w **oknie bezpośrednim**. Aby otworzyć **oknie bezpośrednim**, otwórz dowolnego rozwiązania lub projektu w programie Visual Studio (lub Utwórz nową aplikację konsoli puste) i wybierz polecenie **Windows**, **Immediate** z **Debugowania** menu.

```
?System.NetWebUtility.UrlEncode("password from the Azure portal")
```

Użyj zaszyfrowanych haseł wraz z lokalizacji nazwy i repozytorium użytkownika do skonstruowania polecenia git.

```
git clone https://username:url encoded password@bugbashdev4.scm.azure-api.net/
```

Gdy jest sklonować repozytorium można przeglądać i pracę z nią w lokalnym systemie plików. Aby uzyskać więcej informacji, zobacz [plików i folderów odniesienia lokalnego repozytorium Git struktury](#file-and-folder-structure-reference-of-local-git-repository).

## <a name="to-update-your-local-repository-with-the-most-current-service-instance-configuration"></a>Aby zaktualizować lokalnym repozytorium z najnowszą konfiguracją wystąpienia usługi

Wprowadzenie zmian do Twojego wystąpienia usługi Zarządzanie interfejsami API w portalu Azure lub przy użyciu interfejsu API REST, należy zapisać te zmiany do repozytorium przed zaktualizowaniem lokalnym repozytorium o najnowsze zmiany. Aby to zrobić, kliknij przycisk **Zapisz konfigurację do repozytorium** na **repozytorium konfiguracji** karcie w portalu Azure, a następnie należy wydać następujące polecenie w lokalnym repozytorium.

```
git pull
```

Przed uruchomieniem `git pull` upewnij się, że znajdujesz się w folderze lokalnym repozytorium. Jeśli właśnie został ukończony `git clone` polecenia, a następnie należy zmienić katalogu do Twojego repozytorium, uruchamiając polecenie podobnie do poniższego.

```
cd bugbashdev4.scm.azure-api.net/
```

## <a name="to-push-changes-from-your-local-repo-to-the-server-repo"></a>Aby wypchnąć zmiany z z lokalnego repozytorium do repozytorium serwera
Aby wypchnąć zmiany z lokalnym repozytorium do repozytorium serwera, należy zatwierdzić zmiany i wypychanie ich do repozytorium serwera. Aby zatwierdzić zmiany, Otwórz narzędzie polecenia Git, przełącz się do katalogu lokalnego repozytorium i wydawać następujące polecenia.

```
git add --all
git commit -m "Description of your changes"
```

Aby wypchnąć wszystkie zatwierdzenia na serwerze, uruchom następujące polecenie.

```
git push
```

## <a name="to-deploy-any-service-configuration-changes-to-the-api-management-service-instance"></a>Aby wdrożyć zmian konfiguracji usługi do wystąpienia usługi Zarządzanie interfejsami API

Po lokalne zmiany są zatwierdzone i do repozytorium serwera, można je wdrożyć do Twojego wystąpienia usługi Zarządzanie interfejsami API.

Aby uzyskać informacje na wykonanie tej operacji przy użyciu interfejsu API REST, zobacz [wdrażanie Git zmiany w konfiguracji bazy danych przy użyciu interfejsu API REST](https://docs.microsoft.com/rest/api/apimanagement/tenantconfiguration).

## <a name="file-and-folder-structure-reference-of-local-git-repository"></a>Odwołanie struktury plików i folderów z lokalnego repozytorium Git

Pliki i foldery w repozytorium git lokalne zawierają informacje o konfiguracji dotyczące wystąpienia usługi.

| Element | Opis |
| --- | --- |
| folder główny zarządzanie interfejsami api |Zawiera konfigurację najwyższego poziomu dla wystąpienia usługi |
| interfejsy API folderu |Zawiera konfigurację interfejsów API w wystąpieniu usługi |
| folder grupy |Zawiera konfigurację grup w wystąpieniu usługi |
| folder zasady |Zawiera zasady w wystąpieniu usługi |
| portalStyles folder |Zawiera konfigurację Dostosowywanie portalu deweloperów w wystąpieniu usługi |
| folder produktów |Zawiera konfigurację produktów w wystąpieniu usługi |
| folder szablonów |Zawiera konfigurację szablony wiadomości e-mail w wystąpieniu usługi |

Każdego folderu może zawierać jeden lub więcej plików, a w niektórych przypadkach jeden lub więcej folderów, na przykład folderu dla każdego interfejsu API, produktu lub grupy. Pliki w tych folderach są specyficzne dla typu jednostki opisanego przez nazwę folderu.

| Typ pliku | Przeznaczenie |
| --- | --- |
| json |Informacje o konfiguracji dotyczące odpowiednich jednostek |
| html |Opisy jednostek, często są wyświetlane w portalu dla deweloperów |
| xml |Instrukcje zasad |
| CSS |Arkusze stylów do dostosowania portalu dla deweloperów |

Te pliki można tworzyć, usunąć, edytować i zarządzane w lokalnym systemie plików i wdrożeniu zmian z powrotem do wystąpienia usługi Zarządzanie interfejsami API.

> [!NOTE]
> Następujące jednostek nie są zawarte w repozytorium Git i nie można skonfigurować przy użyciu narzędzia Git.
> 
> * Użytkownicy
> * Subskrypcje
> * Właściwości
> * Jednostek portalu deweloperów innych niż style
> 

### <a name="root-api-management-folder"></a>Folder główny zarządzanie interfejsami api
Katalog główny `api-management` folder zawiera `configuration.json` plik zawierający informacje najwyższego poziomu o wystąpienie usługi o następującym formacie.

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
    "DelegationValidationKey": ""
  },
  "$ref-policy": "api-management/policies/global.xml"
}
```

Pierwsze cztery ustawienia (`RegistrationEnabled`, `UserRegistrationTerms`, `UserRegistrationTermsEnabled`, i `UserRegistrationTermsConsentRequired`) mapowania na następujące ustawienia na **tożsamości** karcie **zabezpieczeń** sekcji.

| Ustawienia tożsamości | Mapuje |
| --- | --- |
| RegistrationEnabled |**Przekieruj do strony logowania użytkowników anonimowych** wyboru |
| UserRegistrationTerms |**Warunki użytkowania w rejestracji użytkownika** pole tekstowe |
| UserRegistrationTermsEnabled |**Pokaż warunki użytkowania na stronie rejestracja** wyboru |
| UserRegistrationTermsConsentRequired |**Wymagaj zgody** wyboru |

Następnie czterech ustawień (`DelegationEnabled`, `DelegationUrl`, `DelegatedSubscriptionEnabled`, i `DelegationValidationKey`) mapowania na następujące ustawienia na **delegowania** karcie **zabezpieczeń** sekcji.

| Ustawienie delegowania | Mapuje |
| --- | --- |
| DelegationEnabled |**Delegat logowania & zapisywania** wyboru |
| DelegationUrl |**Adres URL punktu końcowego delegowania** pole tekstowe |
| DelegatedSubscriptionEnabled |**Delegowanie subskrypcji produktu** wyboru |
| DelegationValidationKey |**Delegowanie klucz sprawdzania poprawności** pole tekstowe |

Ustawienie ostatecznego `$ref-policy`, mapy do pliku instrukcje globalne zasady dla wystąpienia usługi.

### <a name="apis-folder"></a>interfejsy API folderu
`apis` Folder zawiera folder dla każdego interfejsu API w wystąpieniu usługi, który zawiera następujące elementy.

* `apis\<api name>\configuration.json` -to jest Konfiguracja interfejsu API i zawiera informacje dotyczące operacji i adres URL usługi wewnętrznej bazy danych. Jest to te same informacje, który będzie zwracany w przypadku wywołania [pobrania określonego interfejsu API](https://docs.microsoft.com/en-us/rest/api/apimanagement/api/get) z `export=true` w `application/json` format.
* `apis\<api name>\api.description.html` -to jest opis interfejsu API i odpowiada `description` właściwość [jednostki interfejsu API](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.table._entity_property).
* `apis\<api name>\operations\` — Ten folder zawiera `<operation name>.description.html` pliki, które są mapowane na operacje w interfejsie API. Każdy plik zawiera opis jednej operacji w interfejsie API, który mapuje na `description` właściwość [operacji jednostki](https://docs.microsoft.com/en-us/rest/api/visualstudio/operations/list#operationproperties) w interfejsie API REST.

### <a name="groups-folder"></a>folder grupy
`groups` Folder zawiera folder dla każdej grupy zdefiniowane w wystąpieniu usługi.

* `groups\<group name>\configuration.json` — jest to konfiguracja dla grupy. Jest to te same informacje, który będzie zwracany w przypadku wywołania [Pobierz określoną grupę](https://msdn.microsoft.com/library/azure/dn776329.aspx#GetGroup) operacji.
* `groups\<group name>\description.html` -to jest opis grupy i odpowiada `description` właściwość [grupy jednostki](https://msdn.microsoft.com/library/azure/dn776329.aspx#EntityProperties).

### <a name="policies-folder"></a>folder zasady
`policies` Folder zawiera deklaracji zasad dla swojego wystąpienia usługi.

* `policies\global.xml` -zawiera zasady zdefiniowane w zakresie globalnym wystąpienia usługi.
* `policies\apis\<api name>\` — Jeśli masz wszystkie zasady zdefiniowane w zakresie interfejsu API są zawarte w tym folderze.
* `policies\apis\<api name>\<operation name>\` folder — Jeśli masz wszystkie zasady zdefiniowane w zakresie operacji są zawarte w tym folderze `<operation name>.xml` plików, które mapują do deklaracji zasad dla każdej operacji.
* `policies\products\` — Jeśli masz wszystkie zasady zdefiniowane w zakresie produktu są zawarte w tym folderze, który zawiera `<product name>.xml` plików, które mapują do deklaracji zasad dla każdego produktu.

### <a name="portalstyles-folder"></a>portalStyles folder
`portalStyles` Folder zawiera konfigurację i styl arkusze Dostosowywanie portalu deweloperów dla wystąpienia usługi.

* `portalStyles\configuration.json` -zawiera nazwy arkuszy stylów używany przez portalu dla deweloperów
* `portalStyles\<style name>.css` -każdego `<style name>.css` plik zawiera style portalu dla deweloperów (`Preview.css` i `Production.css` domyślnie).

### <a name="products-folder"></a>folder produktów
`products` Folder zawiera folder dla każdego produktu z definicją w wystąpieniu usługi.

* `products\<product name>\configuration.json` — jest to konfiguracja produktu. Jest to te same informacje, który będzie zwracany w przypadku wywołania [pobrania określonego produktu](https://msdn.microsoft.com/library/azure/dn776336.aspx#GetProduct) operacji.
* `products\<product name>\product.description.html` -to jest opis produktu i odpowiada `description` właściwość [jednostki produktu](https://msdn.microsoft.com/library/azure/dn776336.aspx#Product) w interfejsie API REST.

### <a name="templates"></a>templates
`templates` Folder zawiera konfigurację [szablonów wiadomości e-mail](api-management-howto-configure-notifications.md) wystąpienia usługi.

* `<template name>\configuration.json` -to jest konfiguracja dla szablonu wiadomości e-mail.
* `<template name>\body.html` -to jest treść szablon wiadomości e-mail.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać informacje na inne sposoby zarządzania wystąpienia usługi zobacz:

* Zarządzanie wystąpienia usługi za pomocą następujących poleceń cmdlet programu PowerShell
  * [Wdrożenie usługi dokumentacji poleceń cmdlet programu PowerShell](https://msdn.microsoft.com/library/azure/mt619282.aspx)
  * [Zarządzanie usługami dokumentacji poleceń cmdlet programu PowerShell](https://msdn.microsoft.com/library/azure/mt613507.aspx)
* Zarządzanie za pomocą interfejsu API REST wystąpienia usługi
  * [Dokumentacja interfejsu API REST zarządzania interfejsu API](https://msdn.microsoft.com/library/azure/dn776326.aspx)


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




