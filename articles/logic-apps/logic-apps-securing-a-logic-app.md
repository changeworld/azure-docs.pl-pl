---
title: Bezpieczny dostęp do usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Ochrona dostępu do wyzwalaczy, danych wejściowych i danych wyjściowych, parametrów akcji i usług w przepływach pracy dla usługi Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.topic: article
ms.date: 11/22/2016
ms.openlocfilehash: 1307b6df22c51af9710d44abb23178d65e3507aa
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44377398"
---
# <a name="secure-access-in-azure-logic-apps"></a>Bezpieczny dostęp w usłudze Azure Logic Apps

Poniżej przedstawiono sposoby zabezpieczenie dostępu do różnych składników aplikacji logiki:

* Zabezpieczanie dostępu do wyzwalania przepływu pracy aplikacji logiki z wyzwalaczem żądania HTTP.
* Zabezpieczanie dostępu do zarządzania, edytowanie lub odczytywanie aplikacji logiki.
* Bezpieczny dostęp do zawartości wewnątrz dane wejściowe i wyjściowe dla przebiegu aplikacji logiki.
* Zabezpiecz dane wejściowe dla działania w przepływie pracy aplikacji logiki lub parametrów.
* Bezpieczny dostęp do usług, które odbierać żądania z przepływu pracy aplikacji logiki.

## <a name="secure-access-to-trigger"></a>Bezpieczny dostęp do wyzwolenia

Podczas pracy z aplikacją logiki, który jest uruchamiany na żądania HTTP ([żądania](../connectors/connectors-native-reqres.md) lub [elementu Webhook](../connectors/connectors-native-webhook.md)), możesz ograniczyć dostęp, tak aby tylko autoryzowani klienci mogą wyzwalać aplikacji logiki. Wszystkie żądania do aplikacji logiki są szyfrowane i chronione za pomocą protokołu SSL.

### <a name="shared-access-signature"></a>Sygnatura dostępu współdzielonego

Każdy punkt końcowy żądania, dla aplikacji logiki obejmuje [sygnatury dostępu współdzielonego (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md) jako część adresu URL. Każdy adres URL zawiera `sp`, `sv`, i `sig` parametr zapytania. Uprawnienia są określane przez `sp`, a odpowiadają metod HTTP dozwolonych, `sv` jest wersją używaną do generowania, i `sig` jest używany do uwierzytelniania dostępu do wyzwalania. Podpis jest generowana z użyciem algorytm SHA256 za pomocą klucza tajnego dla wszystkich ścieżek URL i właściwości. Klucz tajny nigdy nie jest widoczne i opublikowany i są przechowywane zaszyfrowane i przechowywane jako część aplikacji logiki. Twoja aplikacja logiki autoryzuje tylko wyzwalaczy, które zawierają prawidłowego podpisu utworzonych za pomocą klucza tajnego.

#### <a name="regenerate-access-keys"></a>Wygeneruj ponownie klucze dostępu

Można ponownie wygenerować nowy klucz bezpieczne, w dowolnym momencie za pośrednictwem portalu interfejsu API REST lub na platformie Azure. Wszystkie bieżące adresy URL, które zostały wygenerowane, wcześniej przy użyciu starego klucza są unieważniane i traci wyzwolenie aplikacji logiki.

1. W witrynie Azure portal Otwórz aplikację logiki, które chcesz ponownie wygenerować klucz
1. Kliknij przycisk **klucze dostępu** elementu menu, w obszarze **ustawienia**
1. Wciśnij klawisz, aby ponownie wygenerować i ukończyć proces

Adresy URL, który można pobrać po ponownego wygenerowania są podpisane za pomocą nowego klucza dostępu.

#### <a name="creating-callback-urls-with-an-expiration-date"></a>Tworzenie adresów URL wywołania zwrotnego z datą wygaśnięcia

Jeśli udostępniasz adres URL z innych stron, można wygenerować adresy URL przy użyciu określonych kluczy i daty wygaśnięcia, zgodnie z potrzebami. Można następnie bezproblemowo wdrażać kluczy, lub upewnij się, że dostęp, uruchomienie aplikacji jest ograniczony do niektórych przedziału czasu. Można określić datę wygaśnięcia dla danego adresu URL za pośrednictwem [logiki aplikacji interfejsu API REST](https://docs.microsoft.com/rest/api/logic/workflowtriggers):

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

W treści zawierać właściwości `NotAfter` jako ciąg daty JSON, która zwraca adres URL wywołania zwrotnego, która obowiązuje tylko `NotAfter` daty i godziny.

#### <a name="creating-urls-with-primary-or-secondary-secret-key"></a>Tworzenie adresów URL za pomocą podstawowy lub pomocniczy klucz tajny

Podczas generowania lub listy adresy URL wywołania zwrotnego Wyzwalacze oparte na żądanie, można również określić który klucz używany do podpisywania adresu URL.  Można wygenerować adresu URL podpisany przy użyciu określonego klucza przy użyciu [logiki aplikacji interfejsu API REST](https://docs.microsoft.com/rest/api/logic/workflowtriggers) w następujący sposób:

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

W treści zawierać właściwości `KeyType` jako `Primary` lub `Secondary`.  Spowoduje to zwrócenie podpisane przez bezpieczny klucz określony adres URL.

### <a name="restrict-incoming-ip-addresses"></a>Ogranicz przychodzące adresy IP

Oprócz sygnatura dostępu współdzielonego możesz ograniczyć, wywoływanie aplikacji logiki tylko z określonych klientów.  Na przykład jeśli zarządzasz punktu końcowego usługi za pomocą usługi Azure API Management, można ograniczyć aplikację logiki, aby akceptowała żądania, jeśli żądanie pochodzi z adresu IP wystąpienia usługi API Management.

To ustawienie można skonfigurować w ustawieniach aplikacji logiki:

1. W witrynie Azure portal Otwórz aplikację logiki, które chcesz dodać ograniczenia adresów IP
1. Kliknij przycisk **ustawienia przepływu pracy** elementu menu, w obszarze **ustawienia**
1. Określ listę zakresów adresów IP do zaakceptowania przez wyzwalacz

Prawidłowy zakres adresów IP przybiera format `192.168.1.1/32`. Jeśli chcesz, aby aplikację logiki, aby były uruchamiane tylko wtedy jako zagnieżdżoną aplikację logiki, wybierz opcję **tylko inne aplikacje logic apps** opcji. Ta opcja powoduje zapisanie pustą tablicę do zasobu, znaczenie tylko wywołania z usługi (aplikacje logiki nadrzędnego) uruchomiony pomyślnie.

> [!NOTE]
> Nadal można uruchomić aplikację logiki z wyzwalaczem żądania za pośrednictwem interfejsu API REST / zarządzania `/triggers/{triggerName}/run` niezależnie od adresów IP. Ten scenariusz wymaga uwierzytelniania przy użyciu interfejsu API REST platformy Azure, a wszystkie zdarzenia pojawią się w dzienniku inspekcji usługi Azure. Zestaw kontroli dostępu w zasad odpowiednio.

#### <a name="setting-ip-ranges-on-the-resource-definition"></a>Ustawianie zakresów adresów IP w definicji zasobu

Jeśli używasz [Szablon wdrożenia](logic-apps-create-deploy-template.md) do automatyzowania wdrożeń, ustawienia zakresu adresów IP można skonfigurować w szablonie zasobu.  

``` json
{
    "properties": {
        "definition": {
        },
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
    "type": "Microsoft.Logic/workflows"
}

```

### <a name="adding-azure-active-directory-oauth-or-other-security"></a>Dodawanie usługi Azure Active Directory, OAuth lub innych zabezpieczeń

Aby dodać więcej protokoły autoryzacji na podstawie aplikacji logiki [usługi Azure API Management](https://azure.microsoft.com/services/api-management/) oferty zaawansowana funkcja monitorowania, zabezpieczeń, zasad i dokumentację dla dowolnego punktu końcowego za pomocą funkcji, aby udostępnić aplikację logiki jako interfejsu API. Usługa Azure API Management może narazić publicznych lub prywatnych punktu końcowego dla aplikacji logiki, który może używać usługi Azure Active Directory, certyfikat, OAuth lub inne standardy zabezpieczeń. Po odebraniu żądania usługi Azure API Management przekazuje żądania do aplikacji logiki (wykonanie wszelkie niezbędne przekształcenia lub ograniczenia śledząc). Przychodzące ustawień zakresu adresów IP w aplikacji logiki służy do Zezwalaj tylko na aplikację logiki, aby zostać wywołany z usługi API Management.

## <a name="secure-access-to-manage-or-edit-logic-apps"></a>Bezpieczny dostęp do zarządzania lub edytowanie aplikacji logiki

Można ograniczyć dostęp do operacji zarządzania w aplikacji logiki, aby były tylko do określonych użytkowników lub grupy mogą wykonywać operacje na zasobie. Aplikacje logiki, skorzystaj z Kalkulatora [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/role-assignments-portal.md) funkcji i można dostosować, używając tych samych narzędzi.  Istnieje kilka wbudowanych ról, które można przypisać członków subskrypcją również:

* **Współautor aplikacji logiki** — zapewnia dostęp do wyświetlania, edytowania i aktualizowanie aplikacji logiki.  Nie można usunąć zasobu lub wykonywać operacje administracyjne.
* **Operator aplikacji logiki** — można wyświetlić aplikacji logiki i historii uruchamiania i włączanie/wyłączanie.  Nie można edytować ani aktualizacji definicji.

Można również użyć [blokady zasobu platformy Azure](../azure-resource-manager/resource-group-lock-resources.md) zmienianie lub usuwanie aplikacji logiki. Ta możliwość jest przydatne, aby zapobiec zasoby produkcyjne z zmiany lub usunięcia.

## <a name="secure-access-to-contents-of-the-run-history"></a>Bezpieczny dostęp do zawartości historii uruchamiania

Możesz ograniczyć dostęp do zawartości danych wejściowych ani danych wyjściowych z poprzednich przebiegów do określonych zakresów adresów IP.  

Wszystkie dane w ramach przebieg przepływu pracy są szyfrowane, przesyłanych i magazynowanych. Gdy wykonywane jest wywołanie do historii przebiegów, usługa uwierzytelnia żądanie i zawiera łącza do żądania i odpowiedzi dane wejściowe i wyjściowe. Ten link, mogą być chronione, dlatego żądania tylko do wyświetlania zawartości z wyznaczonym zakresu adresów IP zwrócona zawartość. Ta funkcja służy do dodatkową kontrolę dostępu. Można nawet określić adresu IP, takich jak `0.0.0.0` więc nie można uzyskać dostępu do danych wejściowych lub wyjściowych. Tylko osoba posiadająca uprawnienia administratora można usunąć tego ograniczenia, zapewniając możliwość "just-in-time" dostęp do zawartości przepływu pracy.

To ustawienie można skonfigurować w ustawieniach zasobów w witrynie Azure Portal:

1. W witrynie Azure portal Otwórz aplikację logiki, które chcesz dodać ograniczenia adresów IP
2. Kliknij przycisk **konfiguracji kontroli dostępu** elementu menu, w obszarze **ustawienia**
3. Określ listę zakresów adresów IP w celu uzyskania dostępu do zawartości

#### <a name="setting-ip-ranges-on-the-resource-definition"></a>Ustawianie zakresów adresów IP w definicji zasobu

Jeśli używasz [Szablon wdrożenia](logic-apps-create-deploy-template.md) do automatyzowania wdrożeń, ustawienia zakresu adresów IP można skonfigurować w szablonie zasobu.  

``` json
{
    "properties": {
        "definition": {
        },
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
    "type": "Microsoft.Logic/workflows"
}
```

## <a name="secure-parameters-and-inputs-within-a-workflow"></a>Zabezpieczanie parametrów i danych wejściowych w przepływie pracy

Można zdefiniować parametry niektóre aspekty definicji przepływu pracy dla wdrażania w środowiskach. Ponadto niektóre parametry może być bezpieczne parametry, które nie mają być wyświetlane podczas edytowania przepływu pracy, takie jak identyfikator klienta i klucz tajny klienta dla [uwierzytelniania usługi Azure Active Directory](../connectors/connectors-native-http.md#authentication) akcji HTTP.

### <a name="using-parameters-and-secure-parameters"></a>Za pomocą parametrów i bezpieczne

Do uzyskania dostępu do wartości parametru zasobu w czasie wykonywania, [język definicji przepływów pracy](http://aka.ms/logicappsdocs) zapewnia `@parameters()` operacji. Można również [Określ parametry w szablonie wdrożenia zasobów](../azure-resource-manager/resource-group-authoring-templates.md#parameters). Ale jeśli określisz typ parametru jako `securestring`, parametr nie będą zwracane z pozostałą częścią definicji zasobu i nie będą dostępne, wyświetlając zasobu po wdrożeniu.

> [!NOTE]
> Jeśli Twój parametr jest używany w nagłówkach ani treści żądania, parametr może być widoczna, uzyskując dostęp do historii uruchamiania i wychodzące żądania HTTP. Upewnij się, że odpowiednio ustawić zasady dostępu do zawartości.
> Nagłówki autoryzacji nie są widoczne za pośrednictwem danych wejściowych lub wyjściowych. Wpis tajny jest używane, wpis tajny nie jest możliwe do pobierania.

#### <a name="resource-deployment-template-with-secrets"></a>Szablon wdrożenia zasobów przy użyciu kluczy tajnych

W poniższym przykładzie pokazano wdrożenia, który odwołuje się do parametru secure `secret` w czasie wykonywania. W pliku oddzielne parametry można określić wartość środowiska `secret`, lub użyj [usługi Azure Resource Manager KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md) do pobierania wpisów tajnych na wdrażanie czasu.

``` json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "secretDeploymentParam": {
         "type": "securestring"
      }
   },
   "variables": {},
   "resources": [ {
      "name": "secret-deploy",
      "type": "Microsoft.Logic/workflows",
      "location": "westus",
      "tags": {
         "displayName": "LogicApp"
      },
      "apiVersion": "2016-06-01",
      "properties": {
         "definition": {
            "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
            "actions": {
               "Call_External_API": {
                  "type": "Http",
                  "inputs": {
                     "headers": {
                        "Authorization": "@parameters('secret')"
                     },
                     "body": "This is the request"
                  },
                  "runAfter": {}
               }
            },
            "parameters": {
               "secret": {
                  "type": "SecureString"
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
            "secret": {
               "value": "[parameters('secretDeploymentParam')]"
            }
         }
      }
   } ],
   "outputs": {}
}
```

## <a name="secure-access-to-services-receiving-requests-from-a-workflow"></a>Bezpieczny dostęp do usług odbierania żądań z przepływu pracy

Istnieje wiele sposobów, aby ułatwić zabezpieczanie dowolnego punktu końcowego, aplikacja logiki musi uzyskać dostęp.

### <a name="using-authentication-on-outbound-requests"></a>Przy użyciu uwierzytelniania w odpowiedzi na żądania wychodzące

Podczas pracy z protokołu HTTP, HTTP + Swagger (interfejsu Open API) lub Akcja elementu Webhook, możesz dodać uwierzytelnianie do żądania są wysyłane. Może obejmować uwierzytelnianie podstawowe, uwierzytelnianie certyfikatu lub uwierzytelniania usługi Azure Active Directory. Można znaleźć szczegółowe informacje na temat sposobu konfigurowania tego uwierzytelnienia [w tym artykule](../connectors/connectors-native-http.md#authentication).

### <a name="restricting-access-to-logic-app-ip-addresses"></a>Ograniczanie dostępu do adresów IP aplikacji logiki

Wywołania z aplikacji logiki pochodzą określony zbiór adresów IP dla regionu. Możesz dodać dodatkowe, filtrowanie, aby akceptowała żądania wyznaczonej adresów IP. Aby uzyskać listę adresów IP, zobacz [logiki limity i Konfiguracja aplikacji](logic-apps-limits-and-config.md#configuration).

### <a name="on-premises-connectivity"></a>Łączność lokalna

Aplikacje logiki udostępniają integrację z kilku usług, aby zapewnić bezpieczne i niezawodne w środowisku lokalnym komunikacji.

#### <a name="on-premises-data-gateway"></a>Brama danych lokalnych

Wiele zarządzanych łączników dla usługi logic apps zapewnia bezpieczne połączenie systemów lokalnych, w tym System plików, SQL, SharePoint i bazy danych DB2. Brama przekazuje dane ze źródeł lokalnych w kanałach zaszyfrowanych za pomocą usługi Azure Service Bus. Cały ruch pochodzący jako bezpieczny ruch wychodzący z agenta bramy. Dowiedz się więcej o [sposobu działania bramy danych](logic-apps-gateway-install.md#gateway-cloud-service).

#### <a name="azure-api-management"></a>Usługa Azure API Management

[Usługa Azure API Management](https://azure.microsoft.com/services/api-management/) ma opcjom łączności w środowisku lokalnym, w tym site-to-site VPN i ExpressRoute integracji dla zabezpieczonych serwera proxy i komunikacji z systemami lokalnymi. W Projektancie aplikacji logiki można szybko wybrać interfejsu API dostępne w usłudze Azure API Management w ramach przepływu pracy, zapewniając szybki dostęp do systemów lokalnych.

## <a name="next-steps"></a>Kolejne kroki
[Tworzenie szablonu wdrożenia](logic-apps-create-deploy-template.md)  
[Obsługa wyjątków](logic-apps-exception-handling.md)  
[Monitorowanie aplikacji logiki](logic-apps-monitor-your-logic-apps.md)  
[Diagnozowanie błędów aplikacji logiki i problemów](logic-apps-diagnosing-failures.md)  
