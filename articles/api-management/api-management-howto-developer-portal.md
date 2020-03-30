---
title: Omówienie portalu dla deweloperów usługi Azure API Management
titleSuffix: Azure API Management
description: Dowiedz się więcej o portalu dla deweloperów w usłudze API Management.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/15/2020
ms.author: apimpm
ms.openlocfilehash: fefa5ff5d112b479110d484ee0ea4c358b5c88a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335913"
---
# <a name="azure-api-management-developer-portal-overview"></a>Omówienie portalu dla deweloperów usługi Azure API Management

Portal dla deweloperów to automatycznie generowana, w pełni konfigurowalna witryna sieci Web z dokumentacją interfejsów API. Jest to miejsce, gdzie konsumenci interfejsu API można odnajdywać interfejsy API, dowiedzieć się, jak z nich korzystać, zażądać dostępu i wypróbować je.

W tym artykule opisano różnice między samodzielnie hostowanymi i zarządzanymi wersjami portalu dewelopera w usłudze API Management. Wyjaśnia również jego architekturę i dostarcza odpowiedzi na często zadawane pytania.

![Portal deweloperski api management](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="managed-and-self-hosted-versions"></a><a name="managed-vs-self-hosted"></a>Wersje zarządzane i hostowane samodzielnie

Portal dla deweloperów można utworzyć na dwa sposoby:

- **Wersja zarządzana** — poprzez edytowanie i dostosowywanie portalu, który jest wbudowany `<your-api-management-instance-name>.developer.azure-api.net`w wystąpienie usługi API Management i jest dostępny za pośrednictwem adresu URL . Zapoznaj się z [tym artykułem dokumentacji,](api-management-howto-developer-portal-customize.md) aby dowiedzieć się, jak uzyskać dostęp do zarządzanego portalu i dostosować go.
- **Wersja hostowana samodzielnie** — wdrażając i samodzielnie hostując portal poza wystąpieniem usługi API Management. Takie podejście umożliwia edytowanie bazy kodu portalu i rozszerzanie podanych podstawowych funkcji — na przykład implementowanie niestandardowych widżetów do integracji z systemami innych firm. W tym scenariuszu jesteś opiekunem portalu i jesteś odpowiedzialny za uaktualnienie portalu do najnowszej wersji. Szczegółowe informacje i instrukcje można znaleźć w [repozytorium GitHub z kodem źródłowym portalu][1] i [samouczkiem na temat implementowania widżetu][3]. [Samouczek dla wersji zarządzanej](api-management-howto-developer-portal-customize.md) przechodzi przez panel administracyjny portalu, który jest wspólny dla wersji zarządzanych i hostowanych samodzielnie.

## <a name="portal-architectural-concepts"></a>Koncepcje architektoniczne portalu

Składniki portalu można logicznie podzielić na dwie kategorie: *kod* i *zawartość*.

*Kod* jest obsługiwany w [repozytorium GitHub][1] i zawiera:

- Widżety - które reprezentują elementy wizualne i łączą HTML, JavaScript, zdolność stylizacji, ustawienia i mapowanie zawartości. Przykładami są obraz, akapit tekstowy, formularz, lista interfejsów API itp.
- Definicje stylizowania — określające sposób stylizowania widżetów
- Engine - który generuje statyczne strony internetowe z zawartości portalu i jest napisany w JavaScript
- Edytor wizualny - który pozwala na dostosowanie przeglądarki i tworzenie doświadczenia

*Zawartość* jest podzielona na dwie podkategorie: *zawartość portalu* i zawartość *api management*.

*Zawartość portalu* jest specyficzna dla portalu i obejmuje:

- Strony - na przykład strona docelowa, samouczki API, wpisy na blogu
- Multimedia — obrazy, animacje i inna zawartość oparta na plikach
- Układy - szablony, które są dopasowywały się do adresu URL i definiują sposób wyświetlania stron
- Style - wartości definicji stylów, np.
- Ustawienia - konfiguracja, np.

*Zawartość portalu*, z wyjątkiem nośników, jest wyrażona jako dokumenty JSON.

*Zawartość usługi API Management* obejmuje jednostki, takie jak interfejsy API, operacje, produkty, subskrypcje.

Portal opiera się na dostosowanym widelcu [paperbits ram](https://paperbits.io/). Oryginalna funkcja Paperbits została rozszerzona, aby zapewnić widżety specyficzne dla api Management (na przykład listę interfejsów API, listę produktów) i łącznik do usługi API Management do zapisywania i pobierania zawartości.

## <a name="frequently-asked-questions"></a><a name="faq"></a>Często zadawane pytania

W tej sekcji odpowiadamy na często zadawane pytania dotyczące portalu dla deweloperów, które mają charakter ogólny. Pytania dotyczące wersji hostowane samodzielnie można znaleźć [w sekcji wiki repozytorium GitHub](https://github.com/Azure/api-management-developer-portal/wiki).

### <a name="how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"/>Jak przeprowadzić migrację z wersji zapoznawczej portalu?

Korzystając z wersji zapoznawczej portalu dewelopera, udostępnino zawartość w wersji zapoznawczej w usłudze zarządzania interfejsami API. Zawartość domyślna została znacząco zmodyfikowana w ogólnie dostępnej wersji, aby uzyskać lepsze wrażenia użytkownika. Zawiera również nowe widżety.

Jeśli używasz wersji zarządzanej, zresetuj zawartość portalu, klikając **pozycję Resetuj zawartość** w sekcji **Menu Operacje.** Potwierdzenie tej operacji spowoduje usunięcie całej zawartości portalu i udostępnienie nowej zawartości domyślnej. Aparat portalu został automatycznie zaktualizowany w usłudze zarządzania interfejsami API.

![Resetowanie zawartości portalu](media/api-management-howto-developer-portal/reset-content.png)

Jeśli używasz wersji hostowane samodzielnie, użyj `scripts/cleanup.bat` `scripts/generate.bat` i z repozytorium GitHub, aby usunąć istniejącą zawartość i aprowizować nową zawartość. Upewnij się, że kod portalu jest uaktualniony do najnowszej wersji z repozytorium GitHub wcześniej.

Jeśli nie chcesz resetować zawartości portalu, możesz użyć nowo dostępnych widżetów na swoich stronach. Istniejące widżety zostały automatycznie zaktualizowane do najnowszych wersji.

Jeśli portal został aprowidyfikowany po ogłoszeniu o ogólnej dostępności, powinien już zawierać nową zawartość domyślną. Nie jest wymagane żadne działanie z Twojej strony.

### <a name="how-can-i-migrate-from-the-old-developer-portal-to-the-developer-portal"></a>Jak mogę przeprowadzić migrację ze starego portalu dla deweloperów do portalu dla deweloperów?

Portale są niezgodne i należy przeprowadzić ręczną migrację zawartości.

### <a name="does-the-portal-have-all-the-features-of-the-old-portal"></a>Czy portal ma wszystkie funkcje starego portalu?

Portal dla deweloperów nie obsługuje już *aplikacji* i *problemów.*

Uwierzytelnianie za pomocą OAuth w interaktywnej konsoli dewelopera nie jest jeszcze obsługiwane. Postęp można śledzić za pomocą [problemu z githubem](https://github.com/Azure/api-management-developer-portal/issues/208).

### <a name="has-the-old-portal-been-deprecated"></a>Czy stary portal został przestarzały?

Stare portale dla deweloperów i wydawców są teraz *starszymi* funkcjami — będą otrzymywać tylko aktualizacje zabezpieczeń. Nowe funkcje zostaną zaimplementowane tylko w nowym portalu dla deweloperów.

Wycofanie starszych portali zostanie ogłoszone oddzielnie. Jeśli masz pytania, wątpliwości lub komentarze, zgłoś je [w dedykowanym numerze GitHub](https://github.com/Azure/api-management-developer-portal/issues/121).

### <a name="functionality-i-need-isnt-supported-in-the-portal"></a>Potrzebna funkcjonalność nie jest obsługiwana w portalu

Możesz samodzielnie otworzyć [żądanie funkcji](https://aka.ms/apimwish) lub [zaimplementować brakujące funkcje.][3] Jeśli zaimplementujesz funkcje samodzielnie, możesz samodzielnie hostować portal deweloperów lub otworzyć żądanie ściągnięcia w usłudze GitHub, aby uwzględnić zmiany w wersji zarządzanej.

### <a name="how-can-i-automate-portal-deployments"></a>Jak zautomatyzować wdrożenia portalu?

Możesz programowo uzyskiwać dostęp do zawartości portalu dewelopera i zarządzać nią za pośrednictwem interfejsu API REST, niezależnie od tego, czy używasz wersji zarządzanej, czy samodzielnej.

Interfejs API jest udokumentowany w [sekcji wiki repozytorium GitHub][2]. Może służyć do automatyzacji migracji zawartości portalu między środowiskami — na przykład ze środowiska testowego do środowiska produkcyjnego. Więcej informacji na temat tego procesu można znaleźć [w tym artykule dokumentacji](https://aka.ms/apimdocs/migrateportal) w usłudze GitHub.

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>Czy portal obsługuje szablony usługi Azure Resource Manager i/lub jest zgodny z zestawem devops zasób usługi API Management?

Nie.

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>Czy muszę włączyć dodatkową łączność sieci wirtualnej dla zależności zarządzanego portalu?

W większości przypadków - nie.

Jeśli usługa zarządzania interfejsami API znajduje się w wewnętrznej sieci wirtualnej, portal dla deweloperów jest dostępny tylko z poziomu sieci. Nazwa hosta punktu końcowego zarządzania musi zostać rozpoznana do wewnętrznego adresu VIP usługi z komputera używanego do uzyskiwania dostępu do interfejsu administracyjnego portalu. Upewnij się, że punkt końcowy zarządzania jest zarejestrowany w systemie DNS. W przypadku błędnej konfiguracji zostanie wyświetlony błąd: `Unable to start the portal. See if settings are specified correctly in the configuration (...)`.

Jeśli usługa zarządzania interfejsami API znajduje się w wewnętrznej sieci wirtualnej i uzyskujesz do niej dostęp za pośrednictwem bramy aplikacji z Internetu, upewnij się, że włączysz łączność z portalem dewelopera i punktami końcowymi zarządzania usługi API Management.

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>Przypisano niestandardową domenę usługi API Management, a opublikowany portal nie działa

Po zaktualizowaniu domeny należy [ponownie opublikować portal,](api-management-howto-developer-portal-customize.md#publish) aby zmiany zostały wprowadzone.

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>Dodałem dostawcę tożsamości i nie widzę go w portalu

Po skonfigurowaniu dostawcy tożsamości (na przykład AAD, AAD B2C), należy [ponownie opublikować portal,](api-management-howto-developer-portal-customize.md#publish) aby zmiany zostały wprowadzone.

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>Mam skonfigurowany delegowanie i portal nie używa go

Po skonfigurowaniu delegowania należy [ponownie opublikować portal,](api-management-howto-developer-portal-customize.md#publish) aby zmiany zostały wprowadzone.

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>Inne zmiany konfiguracji usługi API Management nie zostały propagowane w portalu dla deweloperów

Większość zmian konfiguracji (na przykład sieci wirtualnej, logowania i warunków produktu) wymaga [ponownego opublikowania portalu](api-management-howto-developer-portal-customize.md#publish).

### <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a>Podczas korzystania z konsoli interaktywnej pojawia się błąd CORS

Interaktywna konsola sprawia, że żądanie interfejsu API po stronie klienta z przeglądarki. Rozwiąż problem cors, dodając [zasady CORS](api-management-cross-domain-policies.md#CORS) w interfejsach API.

Możesz sprawdzić stan zasad CORS w sekcji **Omówienie portalu** usługi zarządzania interfejsami API w witrynie Azure portal. Pole ostrzegawcze wskazuje brak lub nieprawidłowo skonfigurowane zasady.

![Portal deweloperski api management](media/api-management-howto-developer-portal/cors-azure-portal.png)

Automatyczne stosowanie zasad CORS przez kliknięcie przycisku **Włącz CORS.**

Można również włączyć CORS ręcznie.

1. Kliknij **ręcznie zastosować go na poziomie globalnym** link, aby wyświetlić wygenerowany kod zasad.
2. Przejdź do **wszystkich interfejsów API** w sekcji **interfejsów API** usługi zarządzania interfejsami API w witrynie Azure portal.
3. Kliknij ikonę **</>** w sekcji **Przetwarzanie przychodzące.**
4. Wstaw zasady **<inbound>** w sekcji pliku XML. Upewnij się, **<origin>** że wartość jest zgodna z domeną portalu deweloperów.

> [!NOTE]
> 
> Jeśli zastosujesz zasady CORS w zakresie produktu, zamiast zakresów interfejsów API, a interfejs API użyje uwierzytelniania klucza subskrypcji za pośrednictwem nagłówka, konsola nie będzie działać.
>
> Przeglądarka automatycznie wystawia żądanie HTTP opcje, które nie zawiera nagłówka z kluczem subskrypcji. Z powodu brakującego klucza subskrypcji usługa API Management nie może skojarzyć wywołania OPTIONS z produktem, więc nie może zastosować zasad CORS.
>
> Jako obejście można przekazać klucz subskrypcji w parametrze zapytania.

### <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>Jakich uprawnień potrzebuję do edytowania portalu dla deweloperów?

Jeśli `Oops. Something went wrong. Please try again later.` widzisz błąd podczas otwierania portalu w trybie administracyjnym, może brakować wymaganych uprawnień (RBAC).

Starsze portale wymagały `Microsoft.ApiManagement/service/getssotoken/action` uprawnień w`/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>`zakresie usługi ( ), aby umożliwić administratorowi użytkownika dostęp do portali. Nowy portal wymaga `Microsoft.ApiManagement/service/users/token/action` uprawnienia w `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1`zakresie .

Do utworzenia roli z wymaganym uprawnieniem można użyć następującego skryptu programu PowerShell. Pamiętaj, aby `<subscription-id>` zmienić parametr. 

```PowerShell
#New Portals Admin Role 
Import-Module Az 
Connect-AzAccount 
$contributorRole = Get-AzRoleDefinition "API Management Service Contributor" 
$customRole = $contributorRole 
$customRole.Id = $null
$customRole.Name = "APIM New Portal Admin" 
$customRole.Description = "This role gives the user ability to log in to the new Developer portal as administrator" 
$customRole.Actions = "Microsoft.ApiManagement/service/users/token/action" 
$customRole.IsCustom = $true 
$customRole.AssignableScopes.Clear() 
$customRole.AssignableScopes.Add('/subscriptions/<subscription-id>') 
New-AzRoleDefinition -Role $customRole 
```
 
Po utworzeniu roli można ją przyznać każdemu użytkownikowi z sekcji **Kontrola dostępu (IAM)** w witrynie Azure portal. Przypisanie tej roli do użytkownika spowoduje przypisanie uprawnień w zakresie usługi. Użytkownik będzie mógł generować tokeny sygnatury dostępu Współdzielonego w imieniu *dowolnego* użytkownika w usłudze. Co najmniej ta rola musi być przypisana do administratora usługi. Następujące polecenie programu PowerShell pokazuje, jak przypisać `user1` rolę do użytkownika w najniższym zakresie, aby uniknąć udzielania niepotrzebnych uprawnień użytkownikowi: 

```PowerShell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

Po przyznaniu uprawnień użytkownikowi użytkownik musi się wylogować i zalogować się ponownie w witrynie Azure portal, aby nowe uprawnienia zaczęły obowiązywać.

### <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>Widzę `Unable to start the portal. See if settings are specified correctly (...)` błąd

Ten błąd jest `GET` wyświetlany, `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` gdy wywołanie nie powiedzie się. Wywołanie jest wydawane z przeglądarki przez interfejs administracyjny portalu.

Jeśli usługa zarządzania interfejsami API znajduje się w sieci wirtualnej — zapoznaj się z pytaniem o łączność sieci wirtualnej powyżej.

Niepowodzenie połączenia może być również spowodowane przez certyfikat TLS/SSL, który jest przypisany do domeny niestandardowej i nie jest zaufany przez przeglądarkę. Jako ograniczenie, można usunąć domeny niestandardowej punktu końcowego zarządzania — zarządzanie interfejsem API powróci do domyślnego punktu końcowego z zaufanym certyfikatem.

### <a name="whats-the-browser-support-for-the-portal"></a>Jaka jest obsługa przeglądarki dla portalu?

| Przeglądarka                     | Obsługiwane       |
|-----------------------------|-----------------|
| Apple Safari                | Tak<sup>1</sup> |
| Google Chrome               | Tak<sup>1</sup> |
| Microsoft Edge              | Tak<sup>1</sup> |
| Microsoft Internet Explorer | Nie              |
| Mozilla Firefox             | Tak<sup>1</sup> |

 <small><sup>1</sup> Obsługiwane w dwóch najnowszych wersjach produkcyjnych.</small>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o nowym portalu dla deweloperów:

- [Uzyskiwanie dostępu do zarządzanego portalu deweloperskiego i dostosowywanie ich](api-management-howto-developer-portal-customize.md)
- [Konfigurowanie samodzielnej wersji portalu][2]
- [Zaimplementuj własny widżet][3]

Przeglądaj inne zasoby:

- [Repozytorium GitHub z kodem źródłowym][1]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://aka.ms/apimdevportal/extend