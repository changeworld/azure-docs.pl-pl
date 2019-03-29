---
title: Bezpieczny dostęp do usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Dodawanie zabezpieczeń w usłudze Azure Logic Apps, w tym wyzwalaczy, danych wejściowych i danych wyjściowych, parametrów i innych usług
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.topic: article
ms.date: 02/05/2019
ms.openlocfilehash: 6baeb27855381ca03862f2632d31c628a088af39
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58620618"
---
# <a name="secure-access-in-azure-logic-apps"></a>Bezpieczny dostęp w usłudze Azure Logic Apps

Poniżej przedstawiono elementów w aplikacji logiki gdzie można zabezpieczyć dostęp do:

* [Wyzwalacze żądania lub elementu Webhook](#secure-triggers)
* [Operacje, takie jak zarządzanie, edytowanie lub przeglądanie](#secure-operations) aplikacji logiki
* [Dane wejściowe i wyjściowe](#secure-run-history) z aplikacji logiki w historii uruchamiania
* [Parametry akcji i dane wejściowe](#secure-action-parameters)
* [Usługi, które żądania get](#secure-requests) z aplikacji logiki

<a name="secure-triggers"></a>

## <a name="secure-access-to-request-triggers"></a>Bezpieczny dostęp do żądania wyzwalaczy

Gdy Twoja aplikacja logiki używa wyzwalacz na podstawie żądań HTTP, takich jak [żądania](../connectors/connectors-native-reqres.md) lub [elementu Webhook](../connectors/connectors-native-webhook.md) wyzwalacz, możesz ograniczyć dostęp, tylko autoryzowani klienci mogą uruchomić swoją aplikację logiki. Wszystkich żądań odebranych przez aplikację logiki są szyfrowane i chronione przy użyciu protokołu Secure Sockets Layer (SSL). Poniżej przedstawiono różne sposoby, można zabezpieczyć dostęp do tego typu wyzwalacza:

* [Generowanie sygnatury dostępu współdzielonego](#sas)
* [Ogranicz przychodzące adresy IP](#restrict-incoming-ip-addresses)
* [Dodawanie usługi Azure Active Directory, OAuth lub innych zabezpieczeń](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures"></a>Generowanie sygnatury dostępu współdzielonego

Każdy punkt końcowy żądania, w aplikacji logiki obejmuje [sygnatury dostępu współdzielonego (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md) w adresie URL punktu końcowego. Każdy adres URL zawiera `sp`, `sv`, i `sig` parametru zapytania:

* `sp` Określa uprawnienia, które mapują do metod HTTP dozwolonych do użycia.
* `sv` Określa wersję używaną do generowania podpisu.
* `sig` jest używany do uwierzytelniania dostępu do wyzwalacza.

Podpis jest generowana z użyciem algorytm SHA256 za pomocą klucza dostępu do kluczy tajnych na wszystkich ścieżkach URL i właściwości. Klucz tajny nigdy nie jest widoczne ani opublikowane i są przechowywane zaszyfrowane i przechowywane za pomocą aplikacji logiki. Twoja aplikacja logiki autoryzuje tylko wyzwalaczy, zawierające prawidłowy podpis utworzony za pomocą klucza tajnego. 

Poniżej przedstawiono więcej informacji na temat zabezpieczania dostępu za pomocą sygnatura dostępu współdzielonego:

* [Wygeneruj ponownie klucze dostępu](#access-keys)
* [Utwórz wygasające adresów URL wywołania zwrotnego](#expiring-urls)
* [Tworzyć adresy URL z kluczem podstawowym lub pomocniczym](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Generowanie ponowne kluczy dostępu

Aby ponownie wygenerować klucza bezpiecznego dostępu w dowolnym momencie, należy użyć interfejsu API REST platformy Azure lub w witrynie Azure portal. Wszystkie wcześniej wygenerowany adresów URL, które są unieważniane stary klucz, a nie są już autoryzacji do wyzwalacza aplikacji logiki. Adresy URL, który można pobrać po ponownego wygenerowania są podpisane za pomocą nowego klucza dostępu.

1. Otwórz aplikację logiki, która ma klucz, który chcesz ponownie wygenerować w witrynie Azure portal.

1. W menu aplikacji logiki w ramach **ustawienia**, wybierz opcję **klucze dostępu**.

1. Wybierz klucz, który chcesz ponownie wygenerować i ukończyć proces.

<a name="expiring-urls"></a>

#### <a name="create-callback-urls-with-expiration-dates"></a>Tworzenie adresów URL wywołania zwrotnego znajdują się daty wygaśnięcia

Jeśli adres URL punktu końcowego wyzwalacza opartego na żądaniach udostępniane inne strony, można wygenerować adresy URL wywołania zwrotnego określonych kluczy i daty wygaśnięcia, zgodnie z potrzebami. Można następnie bezproblemowo wdrażać kluczy, lub ograniczyć dostęp do wyzwalania aplikacji logiki do określonego przedziału czasu. Należy określić datę wygaśnięcia dla danego adresu URL, za pomocą [interfejsu API REST aplikacji logiki](https://docs.microsoft.com/rest/api/logic/workflowtriggers), na przykład:

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

W treści zawierać `NotAfter`właściwości przy użyciu JSON daty ciąg. Ta właściwość zwraca adres URL wywołania zwrotnego, który jest prawidłowy tylko do `NotAfter` daty i godziny.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Tworzenie adresów URL przy użyciu podstawowy lub pomocniczy klucz tajny

Po wygenerowaniu lub listy adresów URL wywołania zwrotnego dla Wyzwalacze oparte na żądanie, można również określić klucz do użycia podczas podpisywania adresu URL. Można wygenerować adresu URL, który jest podpisany przy użyciu określonego klucza przy użyciu [interfejsu API REST aplikacji logiki](https://docs.microsoft.com/rest/api/logic/workflowtriggers), na przykład:

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

W treści zawierać `KeyType` właściwości jako `Primary` lub `Secondary`. Ta właściwość zwraca adres URL, który jest podpisany przy użyciu określonego klucza bezpieczne.

<a name="restrict-incoming-ip"></a>

### <a name="restrict-incoming-ip-addresses"></a>Ogranicz przychodzące adresy IP

Wraz z Shared Access Signature możesz chcieć ograniczyć określonych klientów wywołujących aplikację logiki.  
Na przykład jeśli zarządzasz żądania punktu końcowego usługi Azure API Management, można ograniczyć aplikację logiki w celu umożliwienia akceptowania żądań tylko z adresu IP wystąpienia usługi API Management. 

#### <a name="set-ip-ranges---azure-portal"></a>Ustawianie zakresów adresów IP — witryna Azure portal

Aby skonfigurować to ograniczenie w witrynie Azure portal, przejdź do ustawień aplikacji logiki: 

1. W witrynie Azure portal Otwórz aplikację logiki w Projektancie aplikacji logiki. 

1. W menu aplikacji logiki w ramach **ustawienia**, wybierz opcję **ustawienia przepływu pracy**.

1. W obszarze **konfiguracji kontroli dostępu** > 
**dozwolone przychodzące adresy IP**, wybierz opcję **konkretnych zakresów adresów IP**.

1. W obszarze **zakresy adresów IP dla wyzwalaczy**, określ zakresy adresów IP, które akceptuje wyzwalacza. Prawidłowy zakres adresów IP korzysta z tych formatów: *x.x.x.x/x* lub *x.x.x.x-x.x.x.x* 

Jeśli chcesz, aby aplikacja logiki ogień tylko jako zagnieżdżoną aplikację logiki, **dozwolone przychodzące adresy IP** listy wybierz **tylko inne aplikacje Logic Apps**. Ta opcja zapisuje pusta tablica zasobu aplikacji logiki, więc tylko wywołania z usługi Logic Apps (aplikacje logiki nadrzędnego) może wyzwalać zagnieżdżoną aplikację logiki.

> [!NOTE]
> Niezależnie od tego, adres IP, nadal można uruchomić aplikację logiki, która zawiera wyzwalacz na podstawie żądań przy użyciu `/triggers/{triggerName}/run` za pośrednictwem interfejsu API REST platformy Azure lub usługi API Management. Jednak w tym scenariuszu nadal wymaga uwierzytelnienia przy użyciu interfejsu API REST platformy Azure, a wszystkie zdarzenia są wyświetlane w dzienniku inspekcji usługi Azure. Upewnij się, że odpowiednio ustawić zasady kontroli dostępu.

#### <a name="set-ip-ranges---logic-app-deployment-template"></a>Ustawianie zakresów adresów IP — Szablon wdrożenia aplikacji logiki

Jeśli automatyzujesz wdrożenia aplikacji logiki przy użyciu [Szablon wdrożenia usługi Azure Resource Manager](../logic-apps/logic-apps-create-deploy-template.md), można ustawić zakresy adresów IP w tym szablonie, na przykład:

``` json
{
   "properties": {
      "definition": {},
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
   },
   "type": "Microsoft.Logic/workflows",
}
```

<a name="add-authentication"></a>

### <a name="add-azure-active-directory-oauth-or-other-security"></a>Dodawanie usługi Azure Active Directory, OAuth lub innych zabezpieczeń

Aby dodać więcej protokoły autoryzacji do aplikacji logiki, należy rozważyć użycie [usługi Azure API Management](https://azure.microsoft.com/services/api-management/). Ta usługa oferuje rozbudowane monitorowanie, zabezpieczenia, zasad i dokumentację dla dowolnego punktu końcowego i daje możliwość udostępnienia aplikacji logiki jako interfejsu API. Usługa API Management może narazić publicznych lub prywatnych punktu końcowego dla aplikacji logiki, które można następnie użyć usługi Azure Active Directory, OAuth, certyfikat lub inne standardy zabezpieczeń. Gdy usługa API Management odbiera żądanie, usługa wysyła żądanie do aplikacji logiki, umożliwiając wszelkie niezbędne przekształcenia lub ograniczenia po drodze. Aby umożliwić tylko usługi API Management wyzwolenie aplikacji logiki, można użyć ustawień zakresu adresów IP przychodzących aplikację logiki. 

<a name="secure-operations"></a>

## <a name="secure-access-to-logic-app-operations"></a>Bezpieczny dostęp do operacji w aplikacji logiki

Aby umożliwić tylko określonych użytkowników lub grup uruchamianie operacji na aplikację logiki, można ograniczyć dostęp do zadań, takich jak zarządzanie, edytowania i wyświetlania. Usługa Logic Apps obsługuje [based kontroli dostępu (RBAC)](../role-based-access-control/role-assignments-portal.md), które można dostosować lub przypisać role wbudowane do elementów członkowskich w ramach subskrypcji, na przykład:

* **Współautor aplikacji logiki**: Użytkownicy mogą przeglądać, edytować i zaktualizuj swoją aplikację logiki. Tej roli nie można usunąć aplikacji logiki lub uruchamiania operacji administratora.
* **Operator aplikacji logiki**: Użytkownicy mogą wyświetlić swoją aplikację logiki i historii uruchamiania i włączyć lub wyłączyć aplikację logiki. Tej roli nie można edytować ani zaktualizować aplikacji logiki.

Aby uniemożliwić innym użytkownikom zmienianie lub usuwanie aplikacji logiki, można użyć [blokady zasobu platformy Azure](../azure-resource-manager/resource-group-lock-resources.md). Ta funkcja pomaga uniemożliwić innym użytkownikom zmienianie lub usuwanie zasobów w środowisku produkcyjnym.

<a name="secure-run-history"></a>

## <a name="secure-access-to-logic-app-run-history"></a>Bezpieczny dostęp do historii przebiegu aplikacji logiki

Aby chronić zawartości przekazywanej w postaci danych wejściowych ani danych wyjściowych z poprzedniego uruchomienia aplikacji logiki, można ograniczyć dostęp do określonych zakresów adresów IP. Ta funkcja oferuje większą kontrolę dostępu. Wszystkie dane w przebiegu aplikacji logiki są szyfrowane podczas przesyłania i przechowywanych. W przypadku żądania historii przebiegu aplikacji logiki, Logic Apps uwierzytelnia żądanie zawiera łącza do dane wejściowe i dane wyjściowe z żądania i odpowiedzi w przepływie pracy aplikacji logiki. Można chronić te linki, dlatego żądania tylko z określonego adresu IP zwrócona tej zawartości. Na przykład użytkownik może nawet określić adres IP takich jak `0.0.0.0-0.0.0.0` , dzięki czemu nie będą mogli dane wejściowe i wyjściowe. Tylko osoby z uprawnieniami administratora można usunąć tego ograniczenia, zapewniając możliwość "just-in-time" dostęp do zawartości aplikacji logiki.

### <a name="set-ip-ranges---azure-portal"></a>Ustawianie zakresów adresów IP — witryna Azure portal

Aby skonfigurować to ograniczenie w witrynie Azure portal, przejdź do ustawień aplikacji logiki:

1. W witrynie Azure portal Otwórz aplikację logiki w Projektancie aplikacji logiki. 

1. W menu aplikacji logiki w ramach **ustawienia**, wybierz opcję **ustawienia przepływu pracy**.

1. W obszarze **konfiguracji kontroli dostępu** > 
    **dozwolone przychodzące adresy IP**, wybierz opcję **konkretnych zakresów adresów IP**.

1. W obszarze **zakresy adresów IP dla zawartości**, określ zakresy adresów IP, które mogą uzyskać dostęp do zawartości w danych wejściowych i wyjściowych. 
   Prawidłowy zakres adresów IP korzysta z tych formatów: *x.x.x.x/x* lub *x.x.x.x-x.x.x.x* 

### <a name="set-ip-ranges---logic-app-deployment-template"></a>Ustawianie zakresów adresów IP — Szablon wdrożenia aplikacji logiki

Jeśli automatyzujesz wdrożenia aplikacji logiki przy użyciu [Szablon wdrożenia usługi Azure Resource Manager](../logic-apps/logic-apps-create-deploy-template.md), można ustawić zakresy adresów IP w tym szablonie, na przykład:

``` json
{
   "properties": {
      "definition": {},
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
   },
   "type": "Microsoft.Logic/workflows",
}
```

<a name="secure-action-parameters"></a>

## <a name="secure-action-parameters-and-inputs"></a>Zabezpieczanie parametry akcji i dane wejściowe

W przypadku wdrażania w różnych środowiskach, można zdefiniować parametry określone elementy w definicji przepływu pracy aplikacji logiki. W ten sposób możesz podać dane wejściowe, oparte na środowiska używasz i chronić poufne informacje. Na przykład, jeśli w przypadku uwierzytelniania HTTP akcji przy użyciu [usługi Azure Active Directory](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication), zdefiniuj i zabezpieczanie parametry, które akceptuje identyfikator klienta i klucz tajny klienta używany do uwierzytelniania. Dla tych parametrów definicji aplikacji logiki ma swój własny `parameters` sekcji.
Aby uzyskać dostęp do wartości parametrów w czasie wykonywania, można użyć `@parameters('parameterName')` wyrażenie, które są dostarczane przez [język definicji przepływów pracy](https://aka.ms/logicappsdocs). 

Aby chronić parametry i wartości, które nie mają być pokazywane podczas edytowania aplikacji logiki lub uruchom wyświetlanie historii, można zdefiniować parametry, z `securestring` wpisz i użyj kodowania zgodnie z potrzebami. Parametry, które mają tego typu nie są zwracane z definicji zasobu i nie są dostępne podczas wyświetlania zasobu po wdrożeniu.

> [!NOTE]
> Jeśli parametr w nagłówkach żądania lub treść tego parametru może być widoczna podczas uzyskiwania dostępu do aplikacji logiki, historii uruchamiania i wychodzące żądania HTTP. Upewnij się, że można również ustawić zasady dostępu do zawartości w związku z tym.
> Nagłówki autoryzacji nie są widoczne za pośrednictwem danych wejściowych lub wyjściowych. Dlatego jeśli klucz tajny jest używany istnieje, ten wpis tajny nie jest możliwe do pobierania.

Aby uzyskać więcej informacji na temat zabezpieczania parametrów w logice definicji aplikacji, zobacz [Secure parametrów w logice definicji aplikacji](#secure-parameters-workflow) dalej na tej stronie.

Jeśli automatyzujesz wdrożenia za pomocą [szablony wdrażania usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md#parameters), umożliwia również zabezpieczonej parametrów w tych szablonów. Na przykład parametry służy do pobierania wpisów tajnych usługi KeyVault, podczas tworzenia aplikacji logiki. Twoja definicja szablonu wdrożenia ma swój własny `parameters` sekcji, niezależnie od aplikacji logiki `parameters` sekcji. Aby uzyskać więcej informacji na temat zabezpieczania parametrów w szablonach wdrażania, zobacz [Secure parametrów w szablonach wdrożenia](#secure-parameters-deployment-template) dalej na tej stronie.

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-logic-app-definitions"></a>Zabezpieczanie parametrów w logice definicji aplikacji

Do ochrony informacji poufnych w definicji przepływu pracy aplikacji logiki, należy użyć parametrów zabezpieczonej, więc te informacje nie są widoczne po zapisaniu aplikacji logiki. Załóżmy, że używasz `Basic` uwierzytelniania w definicji działania protokołu HTTP. Ten przykład obejmuje `parameters` sekcja, która definiuje parametry dla definicji akcji oraz `authentication` sekcji, która akceptuje `username` i `password` wartości parametrów. Podanie wartości tych parametrów, można użyć pliku oddzielne parametry, na przykład:

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
               "username": "@parameters('usernameParam')",
               "password": "@parameters('passwordParam')"
            }
         },
         "runAfter": {}
      }
   },
   "parameters": {
      "passwordParam": {
         "type": "securestring"
      },
      "userNameParam": {
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

Korzystasz z wpisy tajne, można uzyskać tych kluczy tajnych w czasie wdrażania, za pomocą [usługi Azure Resource Manager KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md).

<a name="secure-parameters-deployment-template"></a>

### <a name="secure-parameters-in-azure-resource-manager-deployment-templates"></a>Zabezpieczanie parametrów w szablonach wdrożenia usługi Azure Resource Manager

Ten przykład przedstawia szablon wdrożenia usługi Resource Manager, który korzysta z więcej niż jeden parametr środowiska uruchomieniowego za pomocą `securestring` typu:

* `armTemplatePasswordParam`, które to dane wejściowe na potrzeby definicji aplikacji logiki `logicAppWfParam` parametru

* `logicAppWfParam`, które to dane wejściowe dla akcji HTTP przy użyciu uwierzytelniania podstawowego

Ten przykład obejmuje wewnętrznego `parameters` sekcji, która należy do definicji przepływu pracy aplikacji logiki i zewnętrznym `parameters` sekcji, która należy do wdrożenia szablonu. Aby określić wartości parametrów środowiska, można użyć pliku oddzielne parametry. 

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "logicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "metadata": {
            "description": "Name of the Logic App."
         }
      },
      "armTemplatePasswordParam": {
         "type": "securestring"
      },
      "logicAppLocation": {
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
         "name": "[parameters('logicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('logicAppLocation')]",
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
                           "username": "@parameters('usernameParam')",
                           "password": "@parameters('logicAppWfParam')"
                        }
                     },
                  "runAfter": {}
                  }
               },
               "parameters": {
                  "logicAppWfParam": {
                     "type": "securestring"
                  },
                  "userNameParam": {
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
               "logicAppWfParam": {
                  "value": "[parameters('armTemplatePasswordParam')]"
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

Korzystasz z wpisy tajne, można uzyskać tych kluczy tajnych w czasie wdrażania, za pomocą [usługi Azure Resource Manager KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md).

<a name="secure-requests"></a>

## <a name="secure-access-to-services-receiving-requests"></a>Bezpieczny dostęp do odbierania żądań usług

Oto kilka sposobów, można zabezpieczyć dowolnego punktu końcowego, gdy aplikacja logiki musi mieć dostęp i wysyła żądania.

### <a name="add-authentication-on-outbound-requests"></a>Dodawanie uwierzytelniania dla wychodzących żądań

Podczas pracy z protokołu HTTP, HTTP + Swagger (interfejsu Open API) lub Akcja elementu Webhook, możesz dodać do żądania wysłanego przez aplikację logiki uwierzytelniania. Na przykład można użyć uwierzytelniania podstawowego, uwierzytelnianie certyfikatu lub uwierzytelniania usługi Azure Active Directory. Aby uzyskać więcej informacji, zobacz [uwierzytelniania wyzwalacze i akcje](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

### <a name="restrict-access-to-logic-app-ip-addresses"></a>Ograniczanie dostępu do adresów IP aplikacji logiki

Wywołania z aplikacji logiki pochodzą z określonych adresów IP wyznaczone na podstawie tego. Aby dodać, filtrowania, który akceptuje żądania tylko z adresów IP. Dla tych adresów IP, zobacz [limity i konfiguracja dla usługi Azure Logic Apps](logic-apps-limits-and-config.md#configuration).

### <a name="secure-on-premises-connectivity"></a>Bezpieczne połączenia lokalnego

Usługa Azure Logic Apps zapewnia integrację z tych usług w bezpieczną i niezawodną komunikację lokalne.

#### <a name="on-premises-data-gateway"></a>Brama danych lokalnych

Wiele łączników zarządzanych w usłudze Azure Logic Apps zapewniają bezpieczne połączenia z systemów lokalnych, takich jak System plików, SQL, SharePoint, bazy danych DB2 i inne. Brama wysyła dane ze źródeł lokalnych w kanałach zaszyfrowanych za pomocą usługi Azure Service Bus. Cały ruch pochodzący jako bezpieczny ruch wychodzący z agenta bramy. Dowiedz się, [jak lokalna brama danych działa](logic-apps-gateway-install.md#gateway-cloud-service).

#### <a name="azure-api-management"></a>Usługa Azure API Management

[Usługa Azure API Management](https://azure.microsoft.com/services/api-management/) udostępnia opcje połączenie lokalne, takie jak lokacja lokacja wirtualnej sieci prywatnej, jak i integracji usługi ExpressRoute na potrzeby zabezpieczonych serwera proxy i komunikacji z systemami lokalnymi. W Projektancie aplikacji logiki można wybrać interfejs API udostępnianych przez usługę API Management z przepływu pracy aplikacji logiki, zapewniając szybki dostęp do systemów lokalnych.

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie szablonu wdrożenia](logic-apps-create-deploy-template.md)  
* [Obsługa wyjątków](logic-apps-exception-handling.md)  
* [Monitorowanie aplikacji logiki](logic-apps-monitor-your-logic-apps.md)  
* [Diagnozowanie błędów aplikacji logiki i problemów z](logic-apps-diagnosing-failures.md)  
