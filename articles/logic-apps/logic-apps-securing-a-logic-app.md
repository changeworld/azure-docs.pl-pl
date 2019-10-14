---
title: Bezpieczny dostęp i Azure Logic Apps danych
description: Ochrona danych wejściowych parametrów, wyzwalaczy żądań HTTP, historii uruchamiania, operacji aplikacji logiki i połączeń z innymi usługami w Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 06/28/2019
ms.openlocfilehash: b1331865224d34f731dbd388cebdaec8b7c9fe7f
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264666"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Zabezpieczanie dostępu i danych w Azure Logic Apps

Aby kontrolować dostęp i chronić dane w Azure Logic Apps, można skonfigurować zabezpieczenia w następujących obszarach:

* [Dostęp do wyzwalaczy żądań HTTP](#secure-triggers)
* [Dostęp do operacji aplikacji logiki](#secure-operations)
* [Dostęp do danych wejściowych i wyjściowych historii uruchamiania](#secure-run-history)
* [Dostęp do danych wejściowych parametrów](#secure-action-parameters)
* [Dostęp do usług i systemów wywoływanych z usługi Logic Apps](#secure-requests)

<a name="secure-triggers"></a>

## <a name="access-to-http-request-triggers"></a>Dostęp do wyzwalaczy żądań HTTP

Gdy aplikacja logiki używa wyzwalacza opartego na żądaniach HTTP, takiego jak wyzwalacz [żądania](../connectors/connectors-native-reqres.md) lub [elementu webhook](../connectors/connectors-native-webhook.md) , można ograniczyć dostęp, tak aby tylko autoryzowani klienci mogli uruchamiać aplikację logiki. Wszystkie żądania odebrane przez aplikację logiki są szyfrowane i zabezpieczone przy użyciu protokołu SSL (SSL). 

Poniżej przedstawiono sposoby zabezpieczenia dostępu do tego typu wyzwalacza:

* [Generowanie sygnatur dostępu współdzielonego](#sas)
* [Ogranicz przychodzące adresy IP](#restrict-incoming-ip-addresses)
* [Dodawanie Azure Active Directory, uwierzytelniania OAuth lub innych zabezpieczeń](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>Generowanie sygnatur dostępu współdzielonego (SAS)

Każdy punkt końcowy żądania w aplikacji logiki ma [sygnaturę dostępu współdzielonego (SAS)](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas) w adresie URL punktu końcowego, który jest następujący:

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

Każdy adres URL zawiera parametr zapytania `sp`, `sv` i `sig`, zgodnie z opisem w tej tabeli:

| Parametr zapytania | Opis |
|-----------------|-------------|
| `sp` | Określa uprawnienia dla dozwolonych metod HTTP do użycia. |
| `sv` | Określa wersję sygnatury dostępu współdzielonego, która ma być używana do generowania podpisu. |
| `sig` | Określa podpis, który ma być używany do uwierzytelniania dostępu do wyzwalacza. Ta sygnatura jest generowana przy użyciu algorytmu SHA256 z kluczowym kluczem dostępu dla wszystkich ścieżek URL i właściwości. Nie jest nigdy narażony ani publikowany, ten klucz jest przechowywany w postaci zaszyfrowanej i przechowywanej w aplikacji logiki. Aplikacja logiki autoryzuje tylko te wyzwalacze, które zawierają prawidłowy podpis utworzony przy użyciu klucza tajnego. |
|||

Aby uzyskać więcej informacji na temat zabezpieczania dostępu za pomocą sygnatury dostępu współdzielonego, zobacz następujące sekcje w tym temacie:

* [Wygeneruj ponownie klucze dostępu](#access-keys)
* [Tworzenie wygasających adresów URL wywołania zwrotnego](#expiring-urls)
* [Tworzenie adresów URL z kluczem podstawowym lub pomocniczym](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Generowanie ponowne kluczy dostępu

Aby wygenerować nowy bezpieczny klucz dostępu w dowolnym momencie, użyj interfejsu API REST lub Azure Portal platformy Azure. Wszystkie wcześniej wygenerowane adresy URL używające starego klucza są unieważnione i nie mają już autoryzacji do wyzwalania aplikacji logiki. Adresy URL, które należy pobrać po ponownym uruchomieniu, są podpisane przy użyciu nowego klucza dostępu.

1. W Azure Portal Otwórz aplikację logiki, która ma klucz, który chcesz wygenerować ponownie.

1. W menu aplikacji logiki w obszarze **Ustawienia**wybierz pozycję **klucze dostępu**.

1. Wybierz klucz, który chcesz ponownie wygenerować i zakończyć proces.

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>Tworzenie wygasających adresów URL wywołania zwrotnego

Jeśli adres URL punktu końcowego jest współużytkowany dla wyzwalacza opartego na żądaniach HTTP innym stronom, można wygenerować adresy URL wywołania zwrotnego, które używają określonych kluczy i mają daty wygaśnięcia. Dzięki temu można bezproblemowo rzutować klucze lub ograniczyć dostęp do wyzwalania aplikacji logiki na podstawie określonego przedziału czasu. Aby określić datę wygaśnięcia dla adresu URL, należy użyć [interfejsu API REST Logic Apps](https://docs.microsoft.com/rest/api/logic/workflowtriggers), na przykład:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

W treści Uwzględnij `NotAfter`property przy użyciu ciągu daty JSON. Ta właściwość zwraca adres URL wywołania zwrotnego, który jest prawidłowy tylko do `NotAfter` daty i godziny.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Tworzenie adresów URL przy użyciu podstawowego lub pomocniczego klucza tajnego

Po wygenerowaniu lub wyświetleniu listy adresów URL wywołania zwrotnego dla wyzwalaczy opartych na żądaniach HTTP można określić klucz, który ma być używany do podpisywania adresu URL. Aby wygenerować adres URL podpisany za pomocą określonego klucza, użyj [interfejsu API REST Logic Apps](https://docs.microsoft.com/rest/api/logic/workflowtriggers), na przykład:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

W treści należy uwzględnić Właściwość `KeyType` jako `Primary` lub `Secondary`. Ta właściwość zwraca adres URL, który jest podpisany przez określony klucz zabezpieczeń.

<a name="restrict-incoming-ip"></a>

### <a name="restrict-incoming-ip-addresses"></a>Ogranicz przychodzące adresy IP

Wraz z sygnaturą dostępu współdzielonego możesz chcieć ograniczyć określonych klientów, którzy mogą wywołać aplikację logiki. Na przykład Jeśli zarządzasz punktem końcowym żądania przy użyciu usługi Azure API Management, możesz ograniczyć aplikację logiki do akceptowania żądań tylko z adresu IP dla wystąpienia API Management.

#### <a name="restrict-incoming-ip-ranges-in-azure-portal"></a>Ogranicz przychodzące zakresy adresów IP w Azure Portal

1. W Azure Portal Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. W menu aplikacji logiki w obszarze **Ustawienia**wybierz pozycję **Ustawienia przepływu pracy**.

1. W obszarze **Konfiguracja kontroli dostępu** > **dozwolone przychodzące adresy IP**wybierz pozycję **określone zakresy adresów IP**.

1. W obszarze **zakresy adresów IP dla wyzwalaczy**określ zakresy adresów IP akceptowane przez wyzwalacz.

   Prawidłowy zakres adresów IP używa następujących formatów: *x. x. x. x/x* lub *x. x. x. x-x. x. x. x*

Jeśli aplikacja logiki ma być wyzwalana tylko jako zagnieżdżona aplikacja logiki, z listy **dozwolone przychodzące adresy IP** wybierz **tylko inne Logic Apps**. Ta opcja umożliwia zapisanie pustej tablicy do zasobu aplikacji logiki. Dzięki temu tylko wywołania z usługi Logic Apps (nadrzędne Aplikacje logiki) mogą wyzwolić zagnieżdżoną aplikację logiki.

> [!NOTE]
> Niezależnie od adresu IP można nadal uruchamiać aplikację logiki z wyzwalaczem opartym na żądaniach HTTP przy użyciu `/triggers/<trigger-name>/run` za pośrednictwem interfejsu API REST platformy Azure lub za pośrednictwem API Management. Jednak ten scenariusz nadal wymaga uwierzytelniania względem interfejsu API REST platformy Azure. Wszystkie zdarzenia pojawiają się w dzienniku inspekcji platformy Azure. Upewnij się, że zasady kontroli dostępu zostały odpowiednio skonfigurowane.

#### <a name="restrict-incoming-ip-ranges-in-azure-resource-manager-template"></a>Ogranicz przychodzące zakresy adresów IP w szablonie Azure Resource Manager

W przypadku automatyzowania wdrożeń aplikacji logiki przy użyciu [szablonu Azure Resource Manager](../logic-apps/logic-apps-create-deploy-template.md)można określić zakresy adresów IP za pomocą sekcji `accessControl` z sekcją `triggers` w definicji zasobu aplikacji logiki, na przykład:

``` json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {<workflow-definition>},
            "parameters": {},
            "accessControl": {
               "triggers": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     },
                     {
                        "addressRange": "2001:0db8::/64"
                     }
                  ]
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="add-authentication"></a>

### <a name="add-azure-active-directory-oauth-or-other-security"></a>Dodawanie Azure Active Directory, uwierzytelniania OAuth lub innych zabezpieczeń

Aby dodać więcej protokołów autoryzacji do aplikacji logiki, rozważ użycie [usługi Azure API Management](../api-management/api-management-key-concepts.md). Ta usługa zapewnia możliwość udostępnienia aplikacji logiki jako interfejsu API i oferuje zaawansowane monitorowanie, zabezpieczenia, zasady i dokumentację dowolnego punktu końcowego. API Management może uwidaczniać publiczny lub prywatny punkt końcowy dla aplikacji logiki, która może używać Azure Active Directory, uwierzytelniania OAuth, certyfikatu lub innych standardów zabezpieczeń. Gdy API Management odbiera żądanie, usługa wysyła żądanie do aplikacji logiki, a także przeprowadza wszelkie niezbędne przekształcenia lub ograniczenia. Aby umożliwić API Management Wyzwól aplikację logiki, możesz użyć ustawień przychodzącego zakresu adresów IP aplikacji logiki.

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>Dostęp do operacji aplikacji logiki

Można zezwolić tylko określonym użytkownikom lub grupom na uruchamianie określonych operacji, takich jak zarządzanie, edytowanie i przeglądanie aplikacji logiki. Aby kontrolować ich uprawnienia, użyj [Access Control opartej na rolach (RBAC) platformy Azure](../role-based-access-control/role-assignments-portal.md) do przypisywania dostosowanych lub wbudowanych ról do członków w ramach subskrypcji platformy Azure:

* [Współautor aplikacji logiki](../role-based-access-control/built-in-roles.md#logic-app-contributor): umożliwia zarządzanie aplikacjami logiki, ale nie pozwala na zmianę dostępu do nich.

* [Operator aplikacji logiki](../role-based-access-control/built-in-roles.md#logic-app-operator): umożliwia odczytywanie, Włączanie i wyłączanie aplikacji logiki, ale nie można ich edytować ani aktualizować.

Aby uniemożliwić innym użytkownikom zmianę lub usunięcie aplikacji logiki, możesz użyć [blokady zasobów platformy Azure](../azure-resource-manager/resource-group-lock-resources.md), która uniemożliwia innym użytkownikom zmianę lub usunięcie zasobów produkcyjnych.

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>Dostęp do danych historii uruchamiania

Podczas uruchamiania aplikacji logiki wszystkie dane są szyfrowane podczas przesyłania przy użyciu [Transport Layer Security (TLS)](https://azure.microsoft.com/updates/app-service-and-functions-hosted-apps-can-now-update-tls-versions/) i w [spoczynku](../security/fundamentals/encryption-atrest.md). Po zakończeniu działania aplikacji logiki można wyświetlić historię tego uruchomienia, w tym kroki, które zostały wykonane wraz ze stanem, czasem trwania, danymi wejściowymi i wynikami dla każdej akcji. Ta obszerna szczegółowo zawiera szczegółowe informacje na temat działania aplikacji logiki oraz miejsca, w którym można rozpocząć rozwiązywanie wszelkich powstających problemów.

Gdy uzyskujesz dostęp do historii uruchamiania aplikacji logiki, Logic Apps uwierzytelniają dostęp i udostępniają linki do wejść i wyjść z żądań i odpowiedzi w ramach uruchomienia aplikacji logiki. Jednak w przypadku akcji, które obsługują hasła, wpisy tajne, klucze lub inne poufne informacje, chcesz uniemożliwić innym użytkownikom wyświetlanie tych danych i uzyskiwanie do nich dostępu. Na przykład jeśli aplikacja logiki pobiera wpis tajny z [Azure Key Vault](../key-vault/key-vault-overview.md) , który ma być używany podczas uwierzytelniania akcji http, należy ukryć ten klucz tajny z widoku.

Aby kontrolować dostęp do wejść i wyjść w historii uruchamiania aplikacji logiki, dostępne są następujące opcje:

* [Ogranicz dostęp według zakresu adresów IP](#restrict-ip).

  Ta opcja umożliwia zabezpieczenie dostępu do historii uruchamiania na podstawie żądań z określonego zakresu adresów IP.

* [Ukryj dane z historii uruchamiania przy użyciu zaciemniania](#obfuscate).

  W wielu wyzwalaczach i akcjach można ukryć ich dane wejściowe, wyjścia lub zarówno z historii uruchamiania aplikacji logiki.

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>Ograniczanie dostępu według zakresu adresów IP

Możesz ograniczyć dostęp do danych wejściowych i wyjściowych w historii uruchamiania aplikacji logiki, tak aby tylko żądania z określonych zakresów adresów IP mogły wyświetlać te dane. Na przykład, aby zablokować wszystkim użytkownikom dostęp do danych wejściowych i wyjściowych, określ zakres adresów IP, taki jak `0.0.0.0-0.0.0.0`. Tylko osoba z uprawnieniami administratora może usunąć to ograniczenie, które zapewnia możliwość dostępu "just in Time" do danych aplikacji logiki. Możesz określić zakresy adresów IP, które mają być ograniczone przy użyciu Azure Portal lub szablonu Azure Resource Manager, który jest używany do wdrażania aplikacji logiki.

#### <a name="restrict-ip-ranges-in-azure-portal"></a>Ograniczanie zakresów adresów IP w Azure Portal

1. W Azure Portal Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. W menu aplikacji logiki w obszarze **Ustawienia**wybierz pozycję **Ustawienia przepływu pracy**.

1. W obszarze **Konfiguracja kontroli dostępu** > **dozwolone przychodzące adresy IP**wybierz pozycję **określone zakresy adresów IP**.

1. W obszarze **zakresy adresów IP dla pozycji zawartość**określ zakresy adresów IP, które mogą uzyskiwać dostęp do zawartości z danych wejściowych i wyjściowych. 

   Prawidłowy zakres adresów IP używa następujących formatów: *x. x. x. x/x* lub *x. x. x. x-x. x. x. x*

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>Ogranicz zakresy adresów IP w szablonie Azure Resource Manager

W przypadku automatyzowania wdrożeń aplikacji logiki przy użyciu [szablonu Azure Resource Manager](../logic-apps/logic-apps-create-deploy-template.md)można określić zakresy adresów IP za pomocą sekcji `accessControl` z sekcją `contents` w definicji zasobu aplikacji logiki, na przykład:

``` json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {<workflow-definition>},
            "parameters": {},
            "accessControl": {
               "contents": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     },
                     {
                        "addressRange": "2001:0db8::/64"
                     }
                  ]
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="obfuscate"></a>

### <a name="hide-data-from-run-history-by-using-obfuscation"></a>Ukryj dane z historii uruchamiania przy użyciu zaciemniania

Wiele wyzwalaczy i akcji ma ustawienia umożliwiające ukrycie danych wejściowych, wyjściowych lub zarówno z historii uruchamiania aplikacji logiki. Poniżej przedstawiono kilka [kwestii, które](#obfuscation-considerations) należy wykonać w celu zapoznania się z tymi ustawieniami w celu zabezpieczenia tych danych.

#### <a name="secure-inputs-and-outputs-in-the-designer"></a>Zabezpieczanie wejść i wyjść w projektancie

1. Jeśli aplikacja logiki nie jest jeszcze otwarta w [Azure Portal](https://portal.azure.com), Otwórz aplikację logiki w Projektancie aplikacji logiki.

   ![Otwieranie przykładowej aplikacji logiki](media/logic-apps-securing-a-logic-app/sample-logic-app.png)

1. Na wyzwalaczu lub akcji, w której chcesz zabezpieczyć dane, wybierz przycisk wielokropka ( **...** ), a następnie wybierz pozycję **Ustawienia**.

   ![Otwórz "Ustawienia"](media/logic-apps-securing-a-logic-app/open-settings.png)

1. Włącz opcję **Zabezpiecz dane wejściowe**, **bezpieczne wyjście**lub oba te elementy. Po zakończeniu wybierz pozycję **Gotowe**.

   ![Włącz bezpieczne dane wejściowe lub wyjściowe](media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   Akcja lub wyzwalacz zawiera teraz ikonę kłódki na pasku tytułu.

   ![Ikona kłódki na pasku tytułu](media/logic-apps-securing-a-logic-app/title-bar-lock-icon.png)

   Tokeny, które reprezentują zabezpieczone dane wyjściowe z poprzednich akcji, również pokazują ikony blokady. Na przykład po wybraniu takich danych wyjściowych z listy zawartości dynamicznej do użycia w akcji token pokazuje ikonę kłódki.

   ![Wybierz dane wyjściowe](media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. Po uruchomieniu aplikacji logiki można wyświetlić historię tego uruchomienia.

   1. W okienku **Przegląd** aplikacji logiki wybierz przebieg, który chcesz wyświetlić.

   1. W okienku **uruchamiania aplikacji logiki** rozwiń akcje, które chcesz przejrzeć.

      W przypadku wybrania opcji zabezpieczenia danych wejściowych i wyjściowych te wartości zostaną wyświetlone jako ukryte.

      ![Dane ukryte w historii uruchamiania](media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="secure-inputs-and-outputs-in-code-view"></a>Zabezpiecz dane wejściowe i wyjściowe w widoku kodu

W podstawowym wyzwalaczu lub definicji akcji Dodaj lub zaktualizuj tablicę `runtimeConfiguration.secureData.properties` z co najmniej jedną z następujących wartości:

* `"inputs"`: zabezpiecza dane wejściowe w historii uruchamiania.
* `"outputs"`: zabezpiecza dane wyjściowe w historii uruchamiania.

Poniżej przedstawiono kilka [kwestii, które](#obfuscation-considerations) należy wykonać w celu zapoznania się z tymi ustawieniami w celu zabezpieczenia tych danych.

```json
"<trigger-or-action-name>": {
   "type": "<trigger-or-action-type>",
   "inputs": {
      <trigger-or-action-inputs>
   },
   "runtimeConfiguration": {
      "secureData": {
         "properties": [
            "inputs",
            "outputs"
         ]
      }
   },
   <other-attributes>
}
```

<a name="obfuscation-considerations"></a>

#### <a name="considerations-when-hiding-inputs-and-outputs"></a>Zagadnienia dotyczące ukrywania danych wejściowych i wyjść

* W przypadku zabezpieczania danych wejściowych lub danych wyjściowych wyzwalacza lub akcji, Logic Apps nie wysyła zabezpieczone dane do usługi Azure Log Analytics. Nie można również dodać [śledzonych właściwości](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details) do tego wyzwalacza lub akcji do monitorowania.

* [Interfejs API Logic Apps obsługujący historię przepływu pracy](https://docs.microsoft.com/rest/api/logic/) nie zwraca zabezpieczonych danych wyjściowych.

* W akcji, w której zabezpieczane są dane wejściowe lub jawnie używające zabezpieczonych danych wyjściowych, a ta akcja zwraca odpowiedź z danymi wyjściowymi zawierającymi te zabezpieczone dane, trzeba ręcznie włączyć **bezpieczne wyjścia** w tej akcji, aby zabezpieczyć te wyniki.

* Upewnij się, że włączasz **bezpieczne dane wejściowe** lub **zabezpieczone wyjścia** w akcjach podrzędnych, w których oczekuje się, że historia uruchamiania ma zabezpieczyć te dane.

  **Ustawienia bezpiecznego wyjścia**

  Po ręcznym włączeniu **bezpiecznych danych wyjściowych** w wyzwalaczu lub akcji Logic Apps zabezpiecza te dane wyjściowe w historii uruchamiania. Jeśli akcja w trybie podrzędnym jawnie używa tych zabezpieczonych danych wyjściowych jako dane wejściowe, Logic Apps ukrywa dane wejściowe tej akcji w historii uruchamiania, ale *nie włącza* ustawienia **zabezpieczonych danych wejściowych** akcji.

  ![Zabezpieczone wyjście jako dane wejściowe i wpływ na efekt podrzędny w większości akcji](media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  Akcje redagowania, przeanalizowania JSON i odpowiedzi mają tylko ustawienia **zabezpieczeń danych wejściowych** . Po włączeniu tego ustawienia ukrywa także te wyniki. Jeśli te akcje jawnie wykorzystują dane wyjściowe przesyłane strumieniowo do strumienia, Logic Apps ukrywa **dane wejściowe i** wyjściowe akcji, ale *nie włączają* tych akcji. Jeśli akcja w trybie podrzędnym jawnie używa ukrytych danych wyjściowych z akcji Redaguj, Analizuj dane JSON lub odpowiedzi jako dane wejściowe, Logic Apps *nie ukrywa danych wejściowych lub wyjściowych akcji podrzędnej*.

  ![Zabezpieczone wyjścia jako dane wejściowe z wpływem podrzędnym na określone akcje](media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **Bezpieczne ustawienie danych wejściowych**

  Po ręcznym włączeniu **zabezpieczeń danych wejściowych** w wyzwalaczu lub akcji Logic Apps zabezpiecza te dane wejściowe w historii uruchamiania. Jeśli akcja w trybie podrzędnym jawnie używa widocznego wyjścia z tego wyzwalacza lub akcji jako danych wejściowych, Logic Apps ukrywa dane wejściowe akcji podrzędnej w historii uruchamiania, ale *nie włącza* **zabezpieczeń danych wejściowych** w tej akcji i nie ukrywa tej akcji. wydajności.

  ![Zabezpieczanie danych wejściowych i wpływu na wpływ na większość akcji](media/logic-apps-securing-a-logic-app/secure-inputs-flow.png)

  Jeśli akcje redagowania, analizy JSON i odpowiedzi jawnie używają widocznych danych wyjściowych wyzwalacza lub akcji, które mają zabezpieczone dane wejściowe, Logic Apps ukrywają dane wejściowe i wyjściowe akcji, ale *nie włączą* **zabezpieczonych danych wejściowych** tej akcji konfigurowania. Jeśli akcja w trybie podrzędnym jawnie używa ukrytych danych wyjściowych z akcji Redaguj, Analizuj dane JSON lub odpowiedzi jako dane wejściowe, Logic Apps *nie ukrywa danych wejściowych lub wyjściowych akcji podrzędnej*.

  ![Zabezpieczone wejścia i wpływ na określone akcje](media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>Dostęp do danych wejściowych parametrów

W przypadku wdrażania w różnych środowiskach należy rozważyć parametryzacja wartości w definicji przepływu pracy, które różnią się w zależności od tych środowisk. Dzięki temu można użyć [szablonu Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md#parameters) do wdrożenia aplikacji logiki, ochrony poufnych informacji przez zdefiniowanie zabezpieczonych parametrów i udostępnienia tych parametrów danych wejściowych oddzielnie za pośrednictwem parametrów szablonu przy użyciu [parametru plik](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values).

Na przykład jeśli uwierzytelniasz akcje HTTP za pomocą [Azure Active Directory](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication), możesz zdefiniować i zabezpieczyć parametry, które akceptują identyfikator klienta i klucz tajny klienta używane do uwierzytelniania. Aby zdefiniować te parametry dla aplikacji logiki, użyj sekcji `parameters` w definicji przepływu pracy aplikacji logiki. Aby chronić wartości parametrów, które nie mają być wyświetlane podczas edytowania aplikacji logiki lub wyświetlania historii uruchamiania, zdefiniuj parametry za pomocą typu `securestring` lub `secureobject` i użyj kodowania w razie potrzeby. Parametry, które mają ten typ nie są zwracane z definicją zasobu i nie są dostępne podczas wyświetlania zasobu po wdrożeniu. Aby uzyskać dostęp do tych wartości parametrów podczas wykonywania, należy użyć wyrażenia `@parameters('<parameter-name>')` w definicji przepływu pracy. To wyrażenie jest oceniane tylko w czasie wykonywania i jest opisane przez [Język definicji przepływu pracy](../logic-apps/logic-apps-workflow-definition-language.md).

> [!NOTE]
> Jeśli używasz parametru w nagłówkach lub treści żądania HTTP, ten parametr może być widoczny podczas wyświetlania historii uruchamiania aplikacji logiki i wychodzącego żądania HTTP. Upewnij się, że ustawiono również zasady dostępu do zawartości. Nagłówki autoryzacji nigdy nie są widoczne za poorednictwem danych wejściowych lub wyjściowych. Dlatego jeśli w tym miejscu jest używany wpis tajny, ten klucz tajny nie jest możliwy do pobierania.

Aby uzyskać więcej informacji, zobacz [Zabezpieczanie parametrów w definicjach przepływu pracy](#secure-parameters-workflow) w dalszej części tego tematu.

W przypadku automatyzowania wdrożeń przy użyciu [szablonów Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md#parameters)można definiować zabezpieczone parametry szablonu, które są oceniane we wdrożeniu, przy użyciu typów `securestring` i `secureobject`. Aby zdefiniować parametry szablonu, użyj sekcji najwyższego poziomu szablonu `parameters`, która jest oddzielona i inna niż sekcja `parameters` definicji przepływu pracy. Aby podać wartości parametrów szablonu, użyj oddzielnego [pliku parametrów](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values).

Na przykład, jeśli używasz wpisów tajnych, możesz definiować i używać zabezpieczonych parametrów szablonu, które pobierają te wpisy tajne z [Azure Key Vault](../key-vault/key-vault-overview.md) we wdrożeniu. Następnie można odwołać się do magazynu kluczy i wpisu tajnego w pliku parametrów. Aby uzyskać więcej informacji, zobacz następujące tematy:

* [Użyj Azure Key Vault, aby przekazać bezpieczne wartości parametrów podczas wdrażania](../azure-resource-manager/resource-manager-keyvault-parameter.md)
* [Zabezpieczanie parametrów w szablonach Azure Resource Manager](#secure-parameters-deployment-template) w dalszej części tego tematu

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>Zabezpieczanie parametrów w definicjach przepływu pracy

Aby chronić poufne informacje w definicji przepływu pracy aplikacji logiki, użyj zabezpieczonych parametrów, aby te informacje nie były widoczne po zapisaniu aplikacji logiki. Załóżmy na przykład, że akcja HTTP wymaga uwierzytelniania podstawowego, które używa nazwy użytkownika i hasła. W definicji przepływu pracy sekcja `parameters` definiuje `basicAuthPasswordParam` i `basicAuthUsernameParam` parametrów przy użyciu typu `securestring`. Definicja akcji odwołuje się do tych parametrów w sekcji `authentication`.

```json
"definition": {
   "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
   "actions": {
      "HTTP": {
         "type": "Http",
         "inputs": {
            "method": "GET",
            "uri": "https://www.microsoft.com",
            "authentication": {
               "type": "Basic",
               "username": "@parameters('basicAuthUsernameParam')",
               "password": "@parameters('basicAuthPasswordParam')"
            }
         },
         "runAfter": {}
      }
   },
   "parameters": {
      "basicAuthPasswordParam": {
         "type": "securestring"
      },
      "basicAuthUsernameParam": {
         "type": "securestring"
      }
   },
   "triggers": {
      "manual": {
         "type": "Request",
         "kind": "Http",
         "inputs": {
            "schema": {}
         }
      }
   },
   "contentVersion": "1.0.0.0",
   "outputs": {}
}
```

<a name="secure-parameters-deployment-template"></a>

### <a name="secure-parameters-in-azure-resource-manager-templates"></a>Zabezpieczanie parametrów w szablonach Azure Resource Manager

Szablon Menedżer zasobów dla aplikacji logiki zawiera wiele sekcji `parameters`. Aby chronić hasła, klucze, wpisy tajne i inne poufne informacje, zdefiniuj zabezpieczone parametry na poziomie szablonu i na poziomie definicji przepływu pracy przy użyciu typu `securestring` lub `secureobject`. Następnie można przechowywać te wartości w [Azure Key Vault](../key-vault/key-vault-overview.md) i używać [pliku parametrów](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values) do odwoływania się do magazynu kluczy i wpisu tajnego. Następnie szablon pobiera te informacje we wdrożeniu. Aby uzyskać więcej informacji, zobacz [używanie Azure Key Vault do przekazywania bezpiecznych wartości parametrów we wdrożeniu](../azure-resource-manager/resource-manager-keyvault-parameter.md).

Poniżej znajduje się więcej informacji na temat tych `parameters` sekcji:

* Na najwyższego poziomu szablonu sekcja `parameters` definiuje parametry dla wartości używanych przez szablon podczas *wdrażania*. Na przykład te wartości mogą zawierać parametry połączenia dla określonego środowiska wdrożenia. Następnie można przechowywać te wartości w osobnym [pliku parametrów](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values), co ułatwia zmianę tych wartości.

* W definicji zasobu aplikacji logiki, ale poza definicją przepływu pracy, sekcja `parameters` określa wartości parametrów definicji przepływu pracy. W tej sekcji można przypisać te wartości przy użyciu wyrażeń szablonu, które odwołują się do parametrów szablonu. Wyrażenia te są oceniane podczas wdrażania.

* W definicji przepływu pracy sekcja `parameters` definiuje parametry używane przez aplikację logiki w czasie wykonywania. Następnie można odwoływać się do tych parametrów w przepływie pracy aplikacji logiki przy użyciu wyrażeń definicji przepływu pracy, które są oceniane w czasie wykonywania.

Ten przykładowy szablon, który ma wiele zabezpieczonych definicji parametrów używających typu `securestring`:

| Nazwa parametru | Opis |
|----------------|-------------|
| `TemplatePasswordParam` | Parametr szablonu, który akceptuje hasło, które jest następnie przekazanie do parametru `basicAuthPasswordParam` definicji przepływu pracy |
| `TemplateUsernameParam` | Parametr szablonu, który akceptuje nazwę użytkownika, która jest następnie przenoszona do parametru `basicAuthUserNameParam` definicji przepływu pracy |
| `basicAuthPasswordParam` | Parametr definicji przepływu pracy, który akceptuje hasło uwierzytelniania podstawowego w akcji HTTP |
| `basicAuthUserNameParam` | Parametr definicji przepływu pracy, który akceptuje nazwę użytkownika uwierzytelniania podstawowego w akcji HTTP |
|||

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "metadata": {
            "description": "Name of the Logic App."
         }
      },
      "TemplatePasswordParam": {
         "type": "securestring"
      },
      "TemplateUsernameParam": {
         "type": "securestring"
      },
      "LogicAppLocation": {
         "type": "string",
         "defaultValue": "[resourceGroup().location]",
         "allowedValues": [
            "[resourceGroup().location]",
            "eastasia",
            "southeastasia",
            "centralus",
            "eastus",
            "eastus2",
            "westus",
            "northcentralus",
            "southcentralus",
            "northeurope",
            "westeurope",
            "japanwest",
            "japaneast",
            "brazilsouth",
            "australiaeast",
            "australiasoutheast",
            "southindia",
            "centralindia",
            "westindia",
            "canadacentral",
            "canadaeast",
            "uksouth",
            "ukwest",
            "westcentralus",
            "westus2"
         ],
         "metadata": {
            "description": "Location of the Logic App."
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-0601/workflowdefinition.json#",
               "actions": {
                  "HTTP": {
                     "type": "Http",
                     "inputs": {
                        "method": "GET",
                        "uri": "https://www.microsoft.com",
                        "authentication": {
                           "type": "Basic",
                           "username": "@parameters('basicAuthUsernameParam')",
                           "password": "@parameters('basicAuthPasswordParam')"
                        }
                     },
                  "runAfter": {}
                  }
               },
               "parameters": {
                  "basicAuthPasswordParam": {
                     "type": "securestring"
                  },
                  "basicAuthUsernameParam": {
                     "type": "securestring"
                  }
               },
               "triggers": {
                  "manual": {
                     "type": "Request",
                     "kind": "Http",
                     "inputs": {
                        "schema": {}
                     }
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "basicAuthPasswordParam": {
                  "value": "[parameters('TemplatePasswordParam')]"
               },
               "basicAuthUsernameParam": {
                  "value": "[parameters('TemplateUsernameParam')]"
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-requests"></a>

## <a name="access-to-services-and-systems-called-from-logic-apps"></a>Dostęp do usług i systemów wywoływanych z usługi Logic Apps

Oto kilka sposobów zabezpieczania punktów końcowych, w których aplikacja logiki potrzebuje dostępu do żądań wysłania:

* Dodaj uwierzytelnianie dla żądań wychodzących.

  Podczas pracy z akcją HTTP, HTTP + Swagger (Open API) lub webhook można dodać uwierzytelnianie do żądania wysyłanego przez aplikację logiki. Można na przykład użyć uwierzytelniania podstawowego, uwierzytelniania certyfikatu lub Azure Active Directory uwierzytelniania. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie wyzwalaczy lub akcji](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

* Ogranicz dostęp z adresów IP aplikacji logiki.

  Wszystkie wywołania punktów końcowych z usługi Logic Apps pochodzą z określonych wyznaczone adresy IP, które są oparte na regionach aplikacji logiki. Można dodać filtrowanie akceptujące żądania tylko z tych adresów IP. Aby uzyskać te adresy IP, zobacz [limity i konfiguracja dla Azure Logic Apps](logic-apps-limits-and-config.md#configuration).

* Uwierzytelniaj i uzyskuj dostęp do zasobów jako tożsamości zarządzane.

  Aby uzyskać dostęp do zasobów w innych dzierżawach usługi Azure Active Directory (Azure AD), aplikacja logiki może uwierzytelnić swoją tożsamość bez logowania się przy użyciu tożsamości zarządzanej (znanej wcześniej jako tożsamość usługi zarządzanej lub MSI), a nie poświadczeń lub wpisów tajnych. Platforma Azure zarządza tą tożsamością i pomaga zabezpieczyć Twoje poświadczenia, ponieważ nie trzeba podawać ani obrócić wpisów tajnych. Aby uzyskać więcej informacji na temat konfigurowania i używania tożsamości zarządzanej przypisanej do systemu dla aplikacji logiki, zobacz temat [uwierzytelnianie i dostęp do zasobów za pomocą tożsamości zarządzanych w Azure Logic Apps](../logic-apps/create-managed-service-identity.md).

* Bezpieczne połączenia z systemami lokalnymi.

  Azure Logic Apps zapewnia integrację z tymi usługami w celu bezpiecznej i niezawodnej komunikacji lokalnej.

  * Brama danych lokalnych

    Wiele zarządzanych łączników w Azure Logic Apps zapewnia bezpieczne połączenia z systemami lokalnymi, takimi jak system plików, SQL, SharePoint i DB2. Brama wysyła dane ze źródeł lokalnych w zaszyfrowanej kanale za pośrednictwem Azure Service Bus. Cały ruch pochodzący z agenta bramy jest zabezpieczonym ruchem wychodzącym. Dowiedz się [, jak działa lokalna Brama danych](logic-apps-gateway-install.md#gateway-cloud-service).

  * Nawiązywanie połączenia za pomocą usługi Azure API Management

    [Usługa Azure API Management](../api-management/api-management-key-concepts.md) udostępnia opcje połączenia lokalnego, takie jak wirtualna sieć prywatna typu lokacja-lokacja i integracja ExpressRoute dla zabezpieczonego serwera proxy i komunikacji z systemami lokalnymi. W przepływie pracy aplikacji logiki w Projektancie aplikacji logiki można wybrać interfejs API, który jest udostępniany przez API Management, który zapewnia szybki dostęp do systemów lokalnych.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie szablonów wdrożenia](logic-apps-create-deploy-template.md)  
* [Monitorowanie aplikacji logiki](logic-apps-monitor-your-logic-apps.md)  
* [Diagnozowanie błędów i problemów aplikacji logiki](logic-apps-diagnosing-failures.md)  
* [Automatyzowanie wdrożenia aplikacji logiki](logic-apps-azure-resource-manager-templates-overview.md)
