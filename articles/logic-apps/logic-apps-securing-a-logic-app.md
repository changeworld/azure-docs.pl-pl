---
title: Zabezpieczanie dostępu i danych
description: Bezpieczny dostęp do wejść, danych wyjściowych, wyzwalaczy opartych na żądaniach, historii uruchamiania, zadań zarządzania i dostępu do innych zasobów w Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/04/2020
ms.openlocfilehash: 47b9c0f89cb3db1610b8e3d98f408283c6ff9980
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77116932"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Zabezpieczanie dostępu i danych w Azure Logic Apps

Aby kontrolować dostęp i chronić dane w Azure Logic Apps, można skonfigurować zabezpieczenia w następujących obszarach:

* [Dostęp do wyzwalaczy opartych na żądaniach](#secure-triggers)
* [Dostęp do operacji aplikacji logiki](#secure-operations)
* [Dostęp do danych wejściowych i wyjściowych historii uruchamiania](#secure-run-history)
* [Dostęp do danych wejściowych parametrów](#secure-action-parameters)
* [Dostęp do usług i systemów wywoływanych z usługi Logic Apps](#secure-requests)

<a name="secure-triggers"></a>

## <a name="access-to-request-based-triggers"></a>Dostęp do wyzwalaczy opartych na żądaniach

Jeśli aplikacja logiki korzysta z wyzwalacza opartego na żądaniach, który odbiera przychodzące wywołania lub żądania, takie jak wyzwalacz [żądania](../connectors/connectors-native-reqres.md) lub [elementu webhook](../connectors/connectors-native-webhook.md) , można ograniczyć dostęp, tak aby tylko autoryzowani klienci mogli wywołać aplikację logiki. Wszystkie żądania odebrane przez aplikację logiki są szyfrowane i zabezpieczone przy użyciu protokołu SSL (SSL).

Poniżej przedstawiono sposoby zabezpieczenia dostępu do tego typu wyzwalacza:

* [Generowanie sygnatur dostępu współdzielonego](#sas)
* [Ogranicz przychodzące adresy IP](#restrict-inbound-ip-addresses)
* [Dodawanie Azure Active Directory uwierzytelniania OAuth lub innych zabezpieczeń](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>Generowanie sygnatur dostępu współdzielonego (SAS)

Każdy punkt końcowy żądania w aplikacji logiki ma [sygnaturę dostępu współdzielonego (SAS)](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas) w adresie URL punktu końcowego, który jest następujący:

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

Każdy adres URL zawiera parametr zapytania `sp`, `sv`i `sig`, zgodnie z opisem w tej tabeli:

| Parametr zapytania | Opis |
|-----------------|-------------|
| `sp` | Określa uprawnienia dla dozwolonych metod HTTP do użycia. |
| `sv` | Określa wersję sygnatury dostępu współdzielonego, która ma być używana do generowania podpisu. |
| `sig` | Określa podpis, który ma być używany do uwierzytelniania dostępu do wyzwalacza. Ta sygnatura jest generowana przy użyciu algorytmu SHA256 z kluczowym kluczem dostępu dla wszystkich ścieżek URL i właściwości. Nie jest nigdy narażony ani publikowany, ten klucz jest przechowywany w postaci zaszyfrowanej i przechowywanej w aplikacji logiki. Aplikacja logiki autoryzuje tylko te wyzwalacze, które zawierają prawidłowy podpis utworzony przy użyciu klucza tajnego. |
|||

Aby uzyskać więcej informacji na temat zabezpieczania dostępu za pomocą SAS, zobacz następujące sekcje w tym temacie:

* [Wygeneruj ponownie klucze dostępu](#access-keys)
* [Tworzenie wygasających adresów URL wywołania zwrotnego](#expiring-urls)
* [Tworzenie adresów URL z kluczem podstawowym lub pomocniczym](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Generowanie ponowne kluczy dostępu

Aby wygenerować nowy bezpieczny klucz dostępu w dowolnym momencie, użyj interfejsu API REST lub Azure Portal platformy Azure. Wszystkie wcześniej wygenerowane adresy URL używające starego klucza są unieważnione i nie mają już autoryzacji do wyzwalania aplikacji logiki. Adresy URL, które należy pobrać po ponownym uruchomieniu, są podpisane przy użyciu nowego klucza dostępu.

1. W [Azure Portal](https://portal.azure.com)Otwórz aplikację logiki, która ma klucz, który chcesz wygenerować ponownie.

1. W menu aplikacji logiki w obszarze **Ustawienia**wybierz pozycję **klucze dostępu**.

1. Wybierz klucz, który chcesz ponownie wygenerować i zakończyć proces.

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>Tworzenie wygasających adresów URL wywołania zwrotnego

Jeśli adres URL punktu końcowego jest współużytkowany dla wyzwalacza opartego na żądaniach innych stron, można wygenerować adresy URL wywołania zwrotnego, które używają określonych kluczy i mają daty wygaśnięcia. Dzięki temu można bezproblemowo rzutować klucze lub ograniczyć dostęp do wyzwalania aplikacji logiki na podstawie określonego przedziału czasu. Aby określić datę wygaśnięcia dla adresu URL, należy użyć [interfejsu API REST Logic Apps](https://docs.microsoft.com/rest/api/logic/workflowtriggers), na przykład:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

W treści Uwzględnij Właściwość `NotAfter`przy użyciu ciągu daty JSON. Ta właściwość zwraca adres URL wywołania zwrotnego, który jest prawidłowy tylko do `NotAfter` datę i godzinę.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Tworzenie adresów URL przy użyciu podstawowego lub pomocniczego klucza tajnego

Podczas generowania lub wyświetlania adresów URL wywołania zwrotnego dla wyzwalacza opartego na żądaniach można określić klucz używany do podpisywania adresu URL. Aby wygenerować adres URL podpisany za pomocą określonego klucza, użyj [interfejsu API REST Logic Apps](https://docs.microsoft.com/rest/api/logic/workflowtriggers), na przykład:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

W treści Uwzględnij Właściwość `KeyType` jako `Primary` lub `Secondary`. Ta właściwość zwraca adres URL, który jest podpisany przez określony klucz zabezpieczeń.

<a name="restrict-inbound-ip"></a>

### <a name="restrict-inbound-ip-addresses"></a>Ogranicz przychodzące adresy IP

Wraz z sygnaturą dostępu współdzielonego można jawnie ograniczyć liczbę klientów, którzy mogą wywołać aplikację logiki. Na przykład Jeśli zarządzasz punktem końcowym żądania przy użyciu usługi Azure API Management, możesz ograniczyć aplikację logiki do akceptowania żądań tylko z adresu IP dla wystąpienia API Management.

#### <a name="restrict-inbound-ip-ranges-in-azure-portal"></a>Ogranicz zakresy adresów IP dla ruchu przychodzącego w Azure Portal

1. W [Azure Portal](https://portal.azure.com)Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. W menu aplikacji logiki w obszarze **Ustawienia**wybierz pozycję **Ustawienia przepływu pracy**.

1. W obszarze **Konfiguracja kontroli dostępu** > **dozwolone przychodzące adresy IP**wybierz pozycję **określone zakresy adresów IP**.

1. W obszarze **zakresy adresów IP dla wyzwalaczy**określ zakresy adresów IP akceptowane przez wyzwalacz.

   Prawidłowy zakres adresów IP używa następujących formatów: *x. x. x. x/x* lub *x. x. x. x-x. x. x. x*

Jeśli aplikacja logiki ma być wyzwalana tylko jako zagnieżdżona aplikacja logiki, z listy **dozwolone przychodzące adresy IP** wybierz **tylko inne Logic Apps**. Ta opcja umożliwia zapisanie pustej tablicy do zasobu aplikacji logiki. Dzięki temu tylko wywołania z usługi Logic Apps (nadrzędne Aplikacje logiki) mogą wyzwolić zagnieżdżoną aplikację logiki.

> [!NOTE]
> Niezależnie od adresu IP można nadal uruchamiać aplikację logiki, która ma wyzwalacz oparty na żądaniach, za pomocą `/triggers/<trigger-name>/run` za pośrednictwem interfejsu API REST platformy Azure lub za pośrednictwem API Management. Jednak ten scenariusz nadal wymaga uwierzytelniania względem interfejsu API REST platformy Azure. Wszystkie zdarzenia pojawiają się w dzienniku inspekcji platformy Azure. Upewnij się, że zasady kontroli dostępu zostały odpowiednio skonfigurowane.

#### <a name="restrict-inbound-ip-ranges-in-azure-resource-manager-template"></a>Ogranicz zakresy adresów IP dla ruchu przychodzącego w szablonie Azure Resource Manager

W przypadku [automatyzowania wdrażania aplikacji logiki przy użyciu szablonów Menedżer zasobów](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)można określić zakresy adresów IP za pomocą sekcji `accessControl` z sekcją `triggers` w definicji zasobu aplikacji logiki, na przykład:

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

### <a name="add-azure-active-directory-oauth-or-other-security"></a>Dodawanie Azure Active Directory uwierzytelniania OAuth lub innych zabezpieczeń

Aby dodać więcej protokołów autoryzacji do aplikacji logiki, rozważ użycie usługi [Azure API Management](../api-management/api-management-key-concepts.md) . Ta usługa ułatwia uwidocznienie aplikacji logiki jako interfejsu API i oferuje zaawansowane monitorowanie, zabezpieczenia, zasady i dokumentację dla dowolnego punktu końcowego. API Management może uwidaczniać publiczny lub prywatny punkt końcowy dla aplikacji logiki. Aby autoryzować dostęp do tego punktu końcowego, można użyć [Azure Active Directory uwierzytelniania OAuth](#azure-active-directory-oauth-authentication), [certyfikatu klienta](#client-certificate-authentication)lub innych standardów zabezpieczeń w celu autoryzowania dostępu do tego punktu końcowego. Gdy API Management odbiera żądanie, usługa wysyła żądanie do aplikacji logiki, a także przeprowadza wszelkie niezbędne przekształcenia lub ograniczenia. Aby umożliwić API Management Wyzwól aplikację logiki, możesz użyć ustawień zakresu przychodzącego adresu IP aplikacji logiki.

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>Dostęp do operacji aplikacji logiki

Można zezwolić tylko określonym użytkownikom lub grupom na uruchamianie określonych zadań, takich jak zarządzanie, edytowanie i przeglądanie aplikacji logiki. Aby kontrolować ich uprawnienia, użyj [Access Control opartej na rolach (RBAC) platformy Azure](../role-based-access-control/role-assignments-portal.md) , aby można było przypisywać niestandardowe lub wbudowane role do członków w ramach subskrypcji platformy Azure:

* [Współautor aplikacji logiki](../role-based-access-control/built-in-roles.md#logic-app-contributor): umożliwia zarządzanie aplikacjami logiki, ale nie pozwala na zmianę dostępu do nich.

* [Operator aplikacji logiki](../role-based-access-control/built-in-roles.md#logic-app-operator): umożliwia odczytywanie, Włączanie i wyłączanie aplikacji logiki, ale nie można ich edytować ani aktualizować.

Aby uniemożliwić innym użytkownikom zmianę lub usunięcie aplikacji logiki, możesz użyć [blokady zasobów platformy Azure](../azure-resource-manager/management/lock-resources.md). Ta funkcja uniemożliwia innym osobom zmianę lub usunięcie zasobów produkcyjnych.

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>Dostęp do danych historii uruchamiania

Podczas uruchamiania aplikacji logiki wszystkie dane są [szyfrowane podczas przesyłania](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) przy użyciu Transport Layer Security (TLS) i [w spoczynku](../security/fundamentals/encryption-atrest.md). Po zakończeniu działania aplikacji logiki można wyświetlić historię tego uruchomienia, w tym kroki, które zostały wykonane wraz ze stanem, czasem trwania, danymi wejściowymi i wynikami dla każdej akcji. Ta obszerna szczegółowo zawiera szczegółowe informacje na temat działania aplikacji logiki oraz miejsca, w którym można rozpocząć rozwiązywanie wszelkich powstających problemów.

Podczas wyświetlania historii uruchamiania aplikacji logiki usługa Logic Apps uwierzytelnia dostęp, a następnie dostarcza linki do wejść i wyjść dla żądań i odpowiedzi dla każdego przebiegu. Jednak w przypadku akcji, które obsługują hasła, wpisy tajne, klucze lub inne poufne informacje, chcesz uniemożliwić innym użytkownikom wyświetlanie tych danych i uzyskiwanie do nich dostępu. Na przykład jeśli aplikacja logiki pobiera wpis tajny z [Azure Key Vault](../key-vault/key-vault-overview.md) , który ma być używany podczas uwierzytelniania akcji http, należy ukryć ten klucz tajny z widoku.

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

W przypadku [automatyzowania wdrażania aplikacji logiki przy użyciu szablonów Menedżer zasobów](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)można określić zakresy adresów IP za pomocą sekcji `accessControl` z sekcją `contents` w definicji zasobu aplikacji logiki, na przykład:

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

1. W [Azure Portal](https://portal.azure.com)Otwórz aplikację logiki w Projektancie aplikacji logiki.

   ![Otwórz aplikację logiki w Projektancie aplikacji logiki](./media/logic-apps-securing-a-logic-app/open-sample-logic-app-in-designer.png)

1. Na wyzwalaczu lub akcji, w której chcesz zabezpieczyć dane, wybierz przycisk wielokropka ( **...** ), a następnie wybierz pozycję **Ustawienia**.

   ![Otwórz wyzwalacz lub ustawienia akcji](./media/logic-apps-securing-a-logic-app/open-action-trigger-settings.png)

1. Włącz opcję **Zabezpiecz dane wejściowe**, **bezpieczne wyjście**lub oba te elementy. Po zakończeniu wybierz pozycję **Gotowe**.

   ![Włącz bezpieczne dane wejściowe lub wyjściowe](./media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   Akcja lub wyzwalacz zawiera teraz ikonę kłódki na pasku tytułu.

   ![Akcja lub pasek tytułu wyzwalacza pokazuje ikonę kłódki](./media/logic-apps-securing-a-logic-app/lock-icon-action-trigger-title-bar.png)

   Tokeny, które reprezentują zabezpieczone dane wyjściowe z poprzednich akcji, również pokazują ikony blokady. Na przykład po wybraniu takich danych wyjściowych z listy zawartości dynamicznej do użycia w akcji token pokazuje ikonę kłódki.

   ![Wybierz token dla zabezpieczonych danych wyjściowych](./media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. Po uruchomieniu aplikacji logiki można wyświetlić historię tego uruchomienia.

   1. W okienku **Przegląd** aplikacji logiki wybierz przebieg, który chcesz wyświetlić.

   1. W okienku **uruchamiania aplikacji logiki** rozwiń akcje, które chcesz przejrzeć.

      W przypadku wybrania opcji zabezpieczenia danych wejściowych i wyjściowych te wartości zostaną wyświetlone jako ukryte.

      ![Ukryte dane wejściowe i wyjściowe w historii uruchamiania](./media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="secure-inputs-and-outputs-in-code-view"></a>Zabezpiecz dane wejściowe i wyjściowe w widoku kodu

W podstawowym wyzwalaczu lub definicji akcji Dodaj lub zaktualizuj tablicę `runtimeConfiguration.secureData.properties` przy użyciu jednej lub obu tych wartości:

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

* W przypadku zabezpieczania danych wejściowych lub danych wyjściowych wyzwalacza lub akcji, Logic Apps nie wysyła zabezpieczone dane do usługi Azure Log Analytics. Nie można również dodać [śledzonych właściwości](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data) do tego wyzwalacza lub akcji do monitorowania.

* [Interfejs API Logic Apps obsługujący historię przepływu pracy](https://docs.microsoft.com/rest/api/logic/) nie zwraca zabezpieczonych danych wyjściowych.

* Aby zabezpieczyć wyjście z akcji, która zabezpiecza dane wejściowe lub jawnie używa zabezpieczonych danych wyjściowych, należy ręcznie włączyć **bezpieczne wyjście** w ramach tej akcji.

* Upewnij się, że włączasz **bezpieczne dane wejściowe** lub **zabezpieczone wyjścia** w akcjach podrzędnych, w których oczekuje się, że historia uruchamiania ma zabezpieczyć te dane.

  **Ustawienia bezpiecznego wyjścia**

  Po ręcznym włączeniu **bezpiecznych danych wyjściowych** w wyzwalaczu lub akcji Logic Apps zabezpiecza te dane wyjściowe w historii uruchamiania. Jeśli akcja w trybie podrzędnym jawnie używa tych zabezpieczonych danych wyjściowych jako dane wejściowe, Logic Apps ukrywa dane wejściowe tej akcji w historii uruchamiania, ale *nie włącza* ustawienia **zabezpieczonych danych wejściowych** akcji.

  ![Zabezpieczone wyjście jako dane wejściowe i wpływ na efekt podrzędny w większości akcji](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  Akcje redagowania, przeanalizowania JSON i odpowiedzi mają tylko ustawienia **zabezpieczeń danych wejściowych** . Po włączeniu tego ustawienia ukrywa także te wyniki. Jeśli te akcje jawnie wykorzystują dane wyjściowe przesyłane strumieniowo do strumienia, Logic Apps ukrywa **dane wejściowe i** wyjściowe akcji, ale *nie włączają* tych akcji. Jeśli akcja w trybie podrzędnym jawnie używa ukrytych danych wyjściowych z akcji Redaguj, Analizuj dane JSON lub odpowiedzi jako dane wejściowe, Logic Apps *nie ukrywa danych wejściowych lub wyjściowych akcji podrzędnej*.

  ![Zabezpieczone wyjścia jako dane wejściowe z wpływem podrzędnym na określone akcje](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **Bezpieczne ustawienie danych wejściowych**

  Po ręcznym włączeniu **zabezpieczeń danych wejściowych** w wyzwalaczu lub akcji Logic Apps zabezpiecza te dane wejściowe w historii uruchamiania. Jeśli akcja przekroczenia jawnie używa widocznego wyjścia z tego wyzwalacza lub akcji jako danych wejściowych, Logic Apps ukrywa dane wejściowe akcji podrzędnej w historii uruchamiania, ale *nie włącza* **bezpiecznego wejścia** w tej akcji i nie ukrywa danych wyjściowych tej akcji.

  ![Zabezpieczanie danych wejściowych i wpływu na wpływ na większość akcji](./media/logic-apps-securing-a-logic-app/secure-inputs-impact-on-downstream.png)

  Jeśli akcje redagowania, analizy JSON i odpowiedzi jawnie używają widocznych danych wyjściowych wyzwalacza lub akcji, które mają zabezpieczone dane wejściowe, Logic Apps ukrywają dane wejściowe i wyjściowe akcji, ale *nie włączają* ustawienia **zabezpieczonych danych wejściowych** tej akcji. Jeśli akcja w trybie podrzędnym jawnie używa ukrytych danych wyjściowych z akcji Redaguj, Analizuj dane JSON lub odpowiedzi jako dane wejściowe, Logic Apps *nie ukrywa danych wejściowych lub wyjściowych akcji podrzędnej*.

  ![Zabezpieczone wejścia i wpływ na określone akcje](./media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>Dostęp do danych wejściowych parametrów

W przypadku wdrażania w różnych środowiskach należy rozważyć parametryzacja wartości w definicji przepływu pracy, które różnią się w zależności od tych środowisk. Dzięki temu można uniknąć zakodowanych danych przy użyciu [szablonu Azure Resource Manager](../azure-resource-manager/templates/overview.md) , aby wdrożyć aplikację logiki, chronić poufne dane przez zdefiniowanie zabezpieczonych parametrów i przekazać te dane jako osobny dane wejściowe za pomocą [parametrów szablonu](../azure-resource-manager/templates/template-parameters.md) przy użyciu [pliku parametrów](../azure-resource-manager/templates/parameter-files.md).

Na przykład jeśli uwierzytelniasz akcje HTTP za pomocą [Azure Active Directory OAuth](#azure-active-directory-oauth-authentication), możesz zdefiniować i zabezpieczyć parametry akceptujące identyfikator klienta i klucz tajny klienta, które są używane do uwierzytelniania. Aby zdefiniować te parametry w aplikacji logiki, użyj sekcji `parameters` w definicji przepływu pracy aplikacji logiki i szablonu Menedżer zasobów do wdrożenia. Aby ukryć wartości parametrów, które nie mają być wyświetlane podczas edytowania aplikacji logiki lub wyświetlania historii uruchamiania, zdefiniuj parametry przy użyciu `securestring` lub `secureobject` typu i użyj kodowania w razie potrzeby. Parametry, które mają ten typ nie są zwracane z definicją zasobu i nie są dostępne podczas wyświetlania zasobu po wdrożeniu. Aby uzyskać dostęp do tych wartości parametrów podczas wykonywania, użyj wyrażenia `@parameters('<parameter-name>')` wewnątrz definicji przepływu pracy. To wyrażenie jest oceniane tylko w czasie wykonywania i jest opisane przez [Język definicji przepływu pracy](../logic-apps/logic-apps-workflow-definition-language.md).

> [!NOTE]
> Jeśli używasz parametru w nagłówku żądania lub treści, ten parametr może być widoczny podczas wyświetlania historii uruchamiania aplikacji logiki i wychodzącego żądania HTTP. Upewnij się, że ustawiono również zasady dostępu do zawartości. Można również użyć [zaciemniania](#obfuscate) , aby ukryć wejścia i wyjścia w historii uruchamiania. Nagłówki autoryzacji nigdy nie są widoczne za poorednictwem danych wejściowych lub wyjściowych. Dlatego jeśli w tym miejscu jest używany wpis tajny, ten klucz tajny nie jest możliwy do pobierania.

Aby uzyskać więcej informacji, zobacz następujące sekcje w tym temacie:

* [Zabezpieczanie parametrów w definicjach przepływu pracy](#secure-parameters-workflow)
* [Ukryj dane z historii uruchamiania przy użyciu zaciemniania](#obfuscate)

W przypadku [automatyzowania wdrażania aplikacji logiki za pomocą szablonów Menedżer zasobów](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)można definiować zabezpieczone [Parametry szablonu](../azure-resource-manager/templates/template-parameters.md), które są oceniane we wdrożeniu, przy użyciu typów `securestring` i `secureobject`. Aby zdefiniować parametry szablonu, użyj sekcji najwyższego poziomu szablonu `parameters`, która jest odrębna i inna od sekcji `parameters` definicji przepływu pracy. Aby podać wartości parametrów szablonu, użyj oddzielnego [pliku parametrów](../azure-resource-manager/templates/parameter-files.md).

Na przykład, jeśli używasz wpisów tajnych, możesz definiować i używać zabezpieczonych parametrów szablonu, które pobierają te wpisy tajne z [Azure Key Vault](../key-vault/key-vault-overview.md) we wdrożeniu. Następnie można odwołać się do magazynu kluczy i wpisu tajnego w pliku parametrów. Aby uzyskać więcej informacji zobacz następujące tematy:

* [Przekaż wartości poufne podczas wdrażania przy użyciu Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)
* [Zabezpieczanie parametrów w szablonach Azure Resource Manager](#secure-parameters-deployment-template) w dalszej części tego tematu

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>Zabezpieczanie parametrów w definicjach przepływu pracy

Aby chronić poufne informacje w definicji przepływu pracy aplikacji logiki, użyj zabezpieczonych parametrów, aby te informacje nie były widoczne po zapisaniu aplikacji logiki. Załóżmy na przykład, że akcja HTTP wymaga uwierzytelniania podstawowego, które używa nazwy użytkownika i hasła. W definicji przepływu pracy sekcja `parameters` definiuje parametry `basicAuthPasswordParam` i `basicAuthUsernameParam` przy użyciu typu `securestring`. Definicja akcji odwołuje się do tych parametrów w sekcji `authentication`.

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

[Szablon Menedżer zasobów](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) dla aplikacji logiki zawiera wiele sekcji `parameters`. Aby chronić hasła, klucze, wpisy tajne i inne informacje poufne, zdefiniuj zabezpieczone parametry na poziomie szablonu i na poziomie definicji przepływu pracy przy użyciu typu `securestring` lub `secureobject`. Następnie można przechowywać te wartości w [Azure Key Vault](../key-vault/key-vault-overview.md) i używać [pliku parametrów](../azure-resource-manager/templates/parameter-files.md) do odwoływania się do magazynu kluczy i wpisu tajnego. Następnie szablon pobiera te informacje we wdrożeniu. Aby uzyskać więcej informacji, zobacz [przekazywanie poufnych wartości we wdrożeniu przy użyciu Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md).

Poniżej znajduje się więcej informacji o tych `parameters` sekcjach:

* Na najwyższego poziomu szablonu sekcja `parameters` definiuje parametry dla wartości używanych przez szablon podczas *wdrażania*. Na przykład te wartości mogą zawierać parametry połączenia dla określonego środowiska wdrożenia. Następnie można przechowywać te wartości w osobnym [pliku parametrów](../azure-resource-manager/templates/parameter-files.md), co ułatwia zmianę tych wartości.

* W definicji zasobu aplikacji logiki, ale poza definicją przepływu pracy, sekcja `parameters` określa wartości parametrów definicji przepływu pracy. W tej sekcji można przypisać te wartości przy użyciu wyrażeń szablonu, które odwołują się do parametrów szablonu. Wyrażenia te są oceniane podczas wdrażania.

* W definicji przepływu pracy sekcja `parameters` definiuje parametry używane przez aplikację logiki w czasie wykonywania. Następnie można odwoływać się do tych parametrów w przepływie pracy aplikacji logiki przy użyciu wyrażeń definicji przepływu pracy, które są oceniane w czasie wykonywania.

Ten przykładowy szablon, który ma wiele zabezpieczonych definicji parametrów, które używają `securestring` typu:

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

Oto kilka sposobów zabezpieczania punktów końcowych, które odbierają wywołania lub żądania z aplikacji logiki:

* Dodawanie uwierzytelniania do żądań wychodzących.

  Podczas pracy z wyzwalaczem lub akcją opartą na protokole HTTP, która wykonuje wywołania wychodzące, takie jak HTTP, HTTP + Swagger lub element webhook, można dodać uwierzytelnianie do żądania wysyłanego przez aplikację logiki. Można na przykład użyć następujących typów uwierzytelniania:

  * [Uwierzytelnianie podstawowe](#basic-authentication)

  * [Uwierzytelnianie certyfikatu klienta](#client-certificate-authentication)

  * [Active Directory uwierzytelniania OAuth](#azure-active-directory-oauth-authentication)

  * [Uwierzytelnianie tożsamości zarządzanej](#managed-identity-authentication)
  
  Aby uzyskać więcej informacji, zobacz [Dodawanie uwierzytelniania do wywołań wychodzących](#add-authentication-outbound) w dalszej części tego tematu.

* Ogranicz dostęp z adresów IP aplikacji logiki.

  Wszystkie wywołania punktów końcowych z usługi Logic Apps pochodzą z określonych wyznaczone adresy IP, które są oparte na regionach aplikacji logiki. Można dodać filtrowanie akceptujące żądania tylko z tych adresów IP. Aby uzyskać te adresy IP, zobacz [limity i konfiguracja dla Azure Logic Apps](logic-apps-limits-and-config.md#configuration).

* Bezpieczne połączenia z systemami lokalnymi.

  Azure Logic Apps zapewnia integrację z tymi usługami w celu bezpiecznej i niezawodnej komunikacji lokalnej.

  * Lokalna Brama danych

    Wiele zarządzanych łączników w Azure Logic Apps zapewnia bezpieczne połączenia z systemami lokalnymi, takimi jak system plików, SQL, SharePoint i DB2. Brama wysyła dane ze źródeł lokalnych w zaszyfrowanej kanale za pośrednictwem Azure Service Bus. Cały ruch pochodzący z agenta bramy jest zabezpieczonym ruchem wychodzącym. Dowiedz się [, jak działa lokalna Brama danych](logic-apps-gateway-install.md#gateway-cloud-service).

  * Nawiązywanie połączenia za pomocą usługi Azure API Management

    [Usługa Azure API Management](../api-management/api-management-key-concepts.md) udostępnia opcje połączenia lokalnego, takie jak wirtualna sieć prywatna typu lokacja-lokacja i integracja ExpressRoute dla zabezpieczonego serwera proxy i komunikacji z systemami lokalnymi. W przepływie pracy aplikacji logiki w Projektancie aplikacji logiki można wybrać interfejs API, który jest udostępniany przez API Management, który zapewnia szybki dostęp do systemów lokalnych.

<a name="add-authentication-outbound"></a>

## <a name="add-authentication-to-outbound-calls"></a>Dodawanie uwierzytelniania do wywołań wychodzących

Punkty końcowe HTTP i HTTPS obsługują różne rodzaje uwierzytelniania. Na podstawie wyzwalacza lub akcji, która służy do wykonywania wywołań wychodzących lub żądań, które uzyskują dostęp do tych punktów końcowych, można wybierać spośród różnych zakresów typów uwierzytelniania. Aby zapewnić ochronę poufnych informacji, które obsługuje aplikacja logiki, należy użyć zabezpieczonych parametrów i zakodować dane w razie potrzeby. Aby uzyskać więcej informacji o używaniu i zabezpieczaniu parametrów, zobacz [dostęp do danych wejściowych parametrów](#secure-action-parameters).

> [!NOTE]
> W Projektancie aplikacji logiki Właściwość **Authentication** może być ukryta w niektórych wyzwalaczach i akcjach, w których można określić typ uwierzytelniania. Aby właściwość była wyświetlana w takich przypadkach, na wyzwalaczu lub akcji Otwórz listę **Dodaj nowy parametr** , a następnie wybierz pozycję **uwierzytelnianie**. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie dostępu przy użyciu tożsamości zarządzanej](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

| Typ uwierzytelniania | Obsługiwane przez program |
|---------------------|--------------|
| [Podstawowa](#basic-authentication) | Azure API Management, Azure App Services, HTTP, HTTP + Swagger, element webhook protokołu HTTP |
| [Certyfikat klienta](#client-certificate-authentication) | Azure API Management, Azure App Services, HTTP, HTTP + Swagger, element webhook protokołu HTTP |
| [Active Directory OAuth](#azure-active-directory-oauth-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, element webhook protokołu HTTP |
| [Surowców](#raw-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, element webhook protokołu HTTP |
| [Tożsamość zarządzana](#managed-identity-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, element webhook protokołu HTTP |
|||

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>Uwierzytelnianie podstawowe

Jeśli opcja [podstawowa](../active-directory-b2c/secure-rest-api-dotnet-basic-auth.md) jest dostępna, określ następujące wartości właściwości:

| Właściwość (Projektant) | Właściwość (JSON) | Wymagany | Wartość | Opis |
|---------------------|-----------------|----------|-------|-------------|
| **Uwierzytelnianie** | `type` | Yes | Podstawowa | Typ uwierzytelniania do użycia |
| **Nazwa użytkownika** | `username` | Yes | <*nazwy użytkownika*>| Nazwa użytkownika służąca do uwierzytelniania dostępu do docelowego punktu końcowego usługi |
| **Hasło** | `password` | Yes | <*hasło*> | Hasło do uwierzytelniania dostępu do docelowego punktu końcowego usługi |
||||||

W przypadku używania [zabezpieczonych parametrów](#secure-action-parameters) do obsługi i ochrony poufnych informacji, na przykład w [szablonie Azure Resource Manager do automatyzowania wdrożenia](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), można użyć wyrażeń, aby uzyskać dostęp do tych wartości parametrów w czasie wykonywania. Ta przykładowa definicja akcji HTTP określa `type` uwierzytelniania jako `Basic` i używa [funkcji Parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) w celu uzyskania wartości parametrów:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Basic",
         "username": "@parameters('userNameParam')",
         "password": "@parameters('passwordParam')"
      }
  },
  "runAfter": {}
}
```

<a name="client-certificate-authentication"></a>

### <a name="client-certificate-authentication"></a>Uwierzytelnianie certyfikatu klienta

Jeśli opcja [certyfikat klienta](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) jest dostępna, określ następujące wartości właściwości:

| Właściwość (Projektant) | Właściwość (JSON) | Wymagany | Wartość | Opis |
|---------------------|-----------------|----------|-------|-------------|
| **Uwierzytelnianie** | `type` | Yes | **Certyfikat klienta** <br>lub <br>`ClientCertificate` | Typ uwierzytelniania, który ma być używany dla certyfikatów klienta SSL (SSL). Certyfikaty z podpisem własnym są obsługiwane, ale certyfikaty z podpisem własnym dla protokołu SSL nie są obsługiwane. |
| **PFX** | `pfx` | Yes | <*zakodowany plik PFX* —> zawartości | Zawartość zakodowana algorytmem Base64 z pliku wymiany informacji osobistych (PFX) <p><p>Aby przekonwertować plik PFX na format szyfrowany algorytmem Base64, można użyć programu PowerShell, wykonując następujące czynności: <p>1. Zapisz zawartość certyfikatu w zmiennej: <p>   `$pfx_cert = get-content 'c:\certificate.pfx' -Encoding Byte` <p>2. Przekonwertuj zawartość certyfikatu przy użyciu funkcji `ToBase64String()` i Zapisz tę zawartość do pliku tekstowego: <p>   `[System.Convert]::ToBase64String($pfx_cert) | Out-File 'pfx-encoded-bytes.txt'` |
| **Hasło** | `password`| Nie | <*hasło — dla pliku pfx*> | Hasło do uzyskiwania dostępu do pliku PFX |
|||||

W przypadku używania [zabezpieczonych parametrów](#secure-action-parameters) do obsługi i ochrony poufnych informacji, na przykład w [szablonie Azure Resource Manager do automatyzowania wdrożenia](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), można użyć wyrażeń, aby uzyskać dostęp do tych wartości parametrów w czasie wykonywania. Ta przykładowa definicja akcji HTTP określa `type` uwierzytelniania jako `ClientCertificate` i używa [funkcji Parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) w celu uzyskania wartości parametrów:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ClientCertificate",
         "pfx": "@parameters('pfxParam')",
         "password": "@parameters('passwordParam')"
      }
   },
   "runAfter": {}
}
```

Aby uzyskać więcej informacji na temat zabezpieczania usług przy użyciu uwierzytelniania certyfikatów klientów, zobacz następujące tematy:

* [Zabezpieczanie interfejsów API przy użyciu uwierzytelniania certyfikatów klientów na platformie Azure API Management](../api-management/api-management-howto-mutual-certificates-for-clients.md)
* [Zabezpieczanie usług zaplecza przy użyciu uwierzytelniania certyfikatów klientów na platformie Azure API Management](../api-management/api-management-howto-mutual-certificates.md)
* [Zabezpieczanie usługi RESTfuL przy użyciu certyfikatów klienta](../active-directory-b2c/secure-rest-api-dotnet-certificate-auth.md)
* [Poświadczenia certyfikatu na potrzeby uwierzytelniania aplikacji](../active-directory/develop/active-directory-certificate-credentials.md)
* [Użyj certyfikatu protokołu SSL w kodzie aplikacji w Azure App Service](../app-service/configure-ssl-certificate-in-code.md)

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-oauth-authentication"></a>Azure Active Directory uwierzytelniania OAuth

Jeśli dostępna jest opcja [Active Directory OAuth](../active-directory/develop/about-microsoft-identity-platform.md) , określ następujące wartości właściwości:

| Właściwość (Projektant) | Właściwość (JSON) | Wymagany | Wartość | Opis |
|---------------------|-----------------|----------|-------|-------------|
| **Uwierzytelnianie** | `type` | Yes | **Active Directory OAuth** <br>lub <br>`ActiveDirectoryOAuth` | Typ uwierzytelniania do użycia. Logic Apps jest obecnie zgodny z [protokołem OAuth 2,0](../active-directory/develop/v2-overview.md). |
| **Uwierzytelniania** | `authority` | Nie | <*adres URL-urząd-wystawca tokenów*> | Adres URL urzędu dostarczającego token uwierzytelniania. Domyślnie ta wartość jest `https://login.windows.net`. |
| **Dzierżaw** | `tenant` | Yes | <*Identyfikator dzierżawy*> | Identyfikator dzierżawy dla dzierżawy usługi Azure AD |
| **Publiczn** | `audience` | Yes | <> *zasobów do autoryzacji* | Zasób, który ma być używany na potrzeby autoryzacji, na przykład `https://management.core.windows.net/` |
| **Client ID (Identyfikator klienta)** | `clientId` | Yes | *Identyfikator klienta* <> | Identyfikator klienta aplikacji żądającej autoryzacji |
| **Typ poświadczeń** | `credentialType` | Yes | Certyfikat <br>lub <br>Wpis tajny | Typ poświadczeń, którego klient używa do żądania autoryzacji. Ta właściwość i wartość nie pojawiają się w podstawowej definicji aplikacji logiki, ale określają właściwości, które są wyświetlane dla wybranego typu poświadczenia. |
| **Wpis tajny** | `secret` | Tak, ale tylko dla typu poświadczeń "wpis tajny" | <> *klucza tajnego klienta* | Wpis tajny klienta na potrzeby żądania autoryzacji |
| **PFX** | `pfx` | Tak, ale tylko dla typu poświadczeń "certyfikat" | <*zakodowany plik PFX* —> zawartości | Zawartość zakodowana algorytmem Base64 z pliku wymiany informacji osobistych (PFX) |
| **Hasło** | `password` | Tak, ale tylko dla typu poświadczeń "certyfikat" | <*hasło — dla pliku pfx*> | Hasło do uzyskiwania dostępu do pliku PFX |
|||||

W przypadku używania [zabezpieczonych parametrów](#secure-action-parameters) do obsługi i ochrony poufnych informacji, na przykład w [szablonie Azure Resource Manager do automatyzowania wdrożenia](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), można użyć wyrażeń, aby uzyskać dostęp do tych wartości parametrów w czasie wykonywania. Ta przykładowa definicja akcji HTTP określa `type` uwierzytelniania jako `ActiveDirectoryOAuth`, typ poświadczenia jako `Secret`i używa [funkcji Parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) w celu uzyskania wartości parametrów:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ActiveDirectoryOAuth",
         "tenant": "@parameters('tenantIdParam')",
         "audience": "https://management.core.windows.net/",
         "clientId": "@parameters('clientIdParam')",
         "credentialType": "Secret",
         "secret": "@parameters('secretParam')"
     }
   },
   "runAfter": {}
}
```

<a name="raw-authentication"></a>

### <a name="raw-authentication"></a>Uwierzytelnianie surowe

Jeśli opcja **RAW** jest dostępna, można użyć tego typu uwierzytelniania w przypadku korzystania ze [schematów uwierzytelniania](https://iana.org/assignments/http-authschemes/http-authschemes.xhtml) , które nie są zgodne z [protokołem OAuth 2,0](https://oauth.net/2/). Ten typ polega na ręcznym utworzeniu wartości nagłówka autoryzacji wysyłanej przy użyciu żądania wychodzącego i określeniu tej wartości nagłówka w wyzwalaczu lub akcji.

Oto przykład nagłówka żądania HTTPS zgodnego z [protokołem OAuth 1,0](https://tools.ietf.org/html/rfc5849):

```text
Authorization: OAuth realm="Photos",
   oauth_consumer_key="dpf43f3p2l4k3l03",
   oauth_signature_method="HMAC-SHA1",
   oauth_timestamp="137131200",
   oauth_nonce="wIjqoS",
   oauth_callback="http%3A%2F%2Fprinter.example.com%2Fready",
   oauth_signature="74KNZJeDHnMBp0EMJ9ZHt%2FXKycU%3D"
```

W wyzwalaczu lub akcji, która obsługuje uwierzytelnianie surowe, określ następujące wartości właściwości:

| Właściwość (Projektant) | Właściwość (JSON) | Wymagany | Wartość | Opis |
|---------------------|-----------------|----------|-------|-------------|
| **Uwierzytelnianie** | `type` | Yes | Surowców | Typ uwierzytelniania do użycia |
| **Wartość** | `value` | Yes | <> *wartości nagłówka autoryzacji* | Wartość nagłówka autoryzacji do użycia na potrzeby uwierzytelniania |
||||||

W przypadku używania [zabezpieczonych parametrów](#secure-action-parameters) do obsługi i ochrony poufnych informacji, na przykład w [szablonie Azure Resource Manager do automatyzowania wdrożenia](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), można użyć wyrażeń, aby uzyskać dostęp do tych wartości parametrów w czasie wykonywania. Ta przykładowa definicja akcji HTTP określa `type` uwierzytelniania jako `Raw`i używa [funkcji Parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) w celu uzyskania wartości parametrów:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Raw",
         "value": "@parameters('authHeaderParam')"
      }
   },
   "runAfter": {}
}
```

<a name="managed-identity-authentication"></a>

### <a name="managed-identity-authentication"></a>Uwierzytelnianie tożsamości zarządzanej

Jeśli dostępna jest opcja [tożsamość zarządzana](../active-directory/managed-identities-azure-resources/overview.md) , aplikacja logiki może użyć tożsamości przypisanej do systemu lub *pojedynczej* ręcznie utworzonej tożsamości przypisanej przez użytkownika do uwierzytelniania dostępu do zasobów w innych dzierżawach Azure Active Directory (Azure AD) bez logowania. Platforma Azure zarządza tą tożsamością i pomaga zabezpieczyć Twoje poświadczenia, ponieważ nie trzeba podawać ani obrócić wpisów tajnych. Dowiedz się więcej [na temat usług platformy Azure, które obsługują tożsamości zarządzane na potrzeby uwierzytelniania w usłudze Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

1. Aby aplikacja logiki mogła korzystać z tożsamości zarządzanej, wykonaj kroki opisane w temacie [uwierzytelnianie dostępu do zasobów platformy Azure przy użyciu tożsamości zarządzanych w Azure Logic Apps](../logic-apps/create-managed-service-identity.md). Te kroki umożliwiają włączenie zarządzanej tożsamości w aplikacji logiki oraz skonfigurowanie dostępu tej tożsamości do docelowego zasobu platformy Azure.

1. Zanim funkcja platformy Azure będzie mogła korzystać z tożsamości zarządzanej, należy najpierw [włączyć uwierzytelnianie dla usługi Azure Functions](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

1. W wyzwalaczu lub akcji, w której ma być używana tożsamość zarządzana, określ następujące wartości właściwości:

   | Właściwość (Projektant) | Właściwość (JSON) | Wymagany | Wartość | Opis |
   |---------------------|-----------------|----------|-------|-------------|
   | **Uwierzytelnianie** | `type` | Yes | **Tożsamość zarządzana** <br>lub <br>`ManagedServiceIdentity` | Typ uwierzytelniania do użycia |
   | **Tożsamość zarządzana** | `identity` | Yes | **tożsamość zarządzana przypisana przez System** *  <br>lub <br>`SystemAssigned` <p><p>* <*przypisanej do użytkownika-Identity-name*> | Zarządzana tożsamość do użycia |
   | **Publiczn** | `audience` | Yes | <*docelowy identyfikator zasobu*> | Identyfikator zasobu dla zasobu docelowego, do którego chcesz uzyskać dostęp. <p>Na przykład `https://storage.azure.com/` powoduje, że tokeny dostępu są prawidłowe dla wszystkich kont magazynu. Można jednak określić adres URL usługi głównej, taki jak `https://fabrikamstorageaccount.blob.core.windows.net` dla określonego konta magazynu. <p>**Uwaga**: Właściwość **odbiorców** może być ukryta w niektórych wyzwalaczach lub akcjach. Aby ta właściwość była widoczna, w wyzwalaczu lub akcji Otwórz listę **Dodaj nowy parametr** , a następnie wybierz pozycję **odbiorcy**. <p><p>**Ważne**: Upewnij się, że identyfikator zasobu docelowego *dokładnie pasuje* do wartości oczekiwanej przez usługę Azure AD, w tym wszystkich wymaganych końcowych ukośników. W związku z tym `https://storage.azure.com/` identyfikator zasobu dla wszystkich kont usługi Azure Blob Storage wymaga końcowej kreski ułamkowej. Jednak identyfikator zasobu dla określonego konta magazynu nie wymaga końcowej kreski ułamkowej. Aby znaleźć te identyfikatory zasobów, zobacz [usługi platformy Azure, które obsługują usługę Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). |
   |||||

   W przypadku używania [zabezpieczonych parametrów](#secure-action-parameters) do obsługi i ochrony poufnych informacji, na przykład w [szablonie Azure Resource Manager do automatyzowania wdrożenia](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), można użyć wyrażeń, aby uzyskać dostęp do tych wartości parametrów w czasie wykonywania. Ta przykładowa definicja akcji HTTP określa `type` uwierzytelniania jako `ManagedServiceIdentity` i używa [funkcji Parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) w celu uzyskania wartości parametrów:

   ```json
   "HTTP": {
      "type": "Http",
      "inputs": {
         "method": "GET",
         "uri": "@parameters('endpointUrlParam')",
         "authentication": {
            "type": "ManagedServiceIdentity",
            "identity": "SystemAssigned",
            "audience": "https://management.azure.com/"
         },
      },
      "runAfter": {}
   }
   ```

## <a name="next-steps"></a>Następne kroki

* [Automatyzowanie wdrażania Azure Logic Apps](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)  
* [Monitorowanie aplikacji logiki](../logic-apps/monitor-logic-apps-log-analytics.md)  
* [Diagnozowanie błędów i problemów aplikacji logiki](../logic-apps/logic-apps-diagnosing-failures.md)  
* [Automatyzowanie wdrożenia aplikacji logiki](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
