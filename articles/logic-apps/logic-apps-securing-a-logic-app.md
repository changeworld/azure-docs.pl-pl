---
title: Bezpieczny dostęp do usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Dodawanie zabezpieczeń dla ochrony dostępu do wyzwalaczy, danych wejściowych i wyjściowych parametry akcji i usług używanych wraz z przepływów pracy w programie Azure Logic Apps.
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: ''
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/22/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 2042fdaa037fe1928fdb81727968a532ddfae0a6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="secure-access-to-your-logic-apps"></a>Bezpieczny dostęp do aplikacji logiki

Istnieje wiele narzędzi ułatwiających zabezpieczanie aplikacji logiki.

* Zabezpieczanie dostępu do wyzwolenia aplikacja logiki (wyzwalacza żądania HTTP)
* Zabezpieczanie dostępu do zarządzania, edytować lub odczytać aplikacji logiki
* Zabezpieczanie dostępu do zawartości z wejściami i wyjściami dla przebiegu
* Zabezpieczanie parametrów lub danych wejściowych w ramach działań w przepływie pracy
* Zabezpieczanie dostępu do usług, które odbierania żądań z przepływu pracy

## <a name="secure-access-to-trigger"></a>Bezpieczny dostęp do wyzwolenia

Podczas pracy z aplikacji logiki, uruchamiane na żądanie HTTP ([żądania](../connectors/connectors-native-reqres.md) lub [Webhook](../connectors/connectors-native-webhook.md)), można ograniczyć dostęp, tak aby tylko autoryzowani klienci mogą wyzwalać aplikacji logiki. Wszystkie żądania do aplikacji logiki są szyfrowane i chronione za pomocą protokołu SSL.

### <a name="shared-access-signature"></a>Sygnatury dostępu współdzielonego

Każdy punkt końcowy żądania dla aplikacji logiki obejmuje [dostępu sygnatury dostępu Współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md) jako część adresu URL. Każdy adres URL zawiera `sp`, `sv`, i `sig` parametr zapytania. Uprawnienia są określane przez `sp`i odpowiadają metod HTTP dozwolonych, `sv` jest używany do generowania, wersja i `sig` służy do uwierzytelniania dostępu do wyzwalania. Podpis jest generowany przy użyciu algorytmu skrótu SHA256 o klucz tajny dla wszystkich ścieżek URL i właściwości. Klucz tajny nigdy nie jest widoczne i opublikowana i są przechowywane w zaszyfrowanej i przechowywane aplikacji logiki. Aplikację logiki autoryzuje tylko wyzwalaczy, które zawiera prawidłowy podpis utworzone za pomocą klucza tajnego.

#### <a name="regenerate-access-keys"></a>Ponowne generowanie kluczy dostępu

Można ponownie wygenerować nowy klucz bezpiecznego w dowolnym momencie za pośrednictwem portalu interfejsu API REST lub Azure. Wszystkie bieżącego adresów URL, które zostały wygenerowane wcześniej przy użyciu starego klucza są unieważniona i traci prawo, aby uruchomić aplikację logiki.

1. W portalu Azure Otwórz aplikację logiki, które chcesz ponownie wygenerować klucz
1. Kliknij przycisk **klucze dostępu** elementu menu pod **ustawienia**
1. Wybierz klawisz, aby ponownie wygenerować i ukończyć proces

Adresy URL, który można pobrać po ponowne generowanie zalogowano się za pomocą nowego klucza dostępu.

#### <a name="creating-callback-urls-with-an-expiration-date"></a>Tworzenie adresów URL wywołania zwrotnego z datą wygaśnięcia

Adres URL w przypadku udostępniania innym osobom, można wygenerować adresy URL określone klucze i daty ważności, zgodnie z potrzebami. Możesz bezproblemowo Przywróć klucze, lub upewnij się, że jest ograniczone do niektórych zakres czasu do uruchomienie aplikacji. Można określić datę wygaśnięcia dla danego adresu URL za pośrednictwem [logiki aplikacji interfejsu API REST](https://docs.microsoft.com/rest/api/logic/workflowtriggers):

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

W treści, będą zawierać właściwości `NotAfter` jako ciąg daty JSON, który zwraca wywołania zwrotnego adresu URL, które obowiązuje tylko `NotAfter` daty i godziny.

#### <a name="creating-urls-with-primary-or-secondary-secret-key"></a>Tworzenie adresów URL z podstawowym lub pomocniczym klucz tajny

Podczas generowania lub umieszczanie na liście adresów URL wywołania zwrotnego na podstawie żądań wyzwalaczy, można także określić klucz, do którego można używać w celu zarejestrowania adresu URL.  Możesz wygenerować podpisane przez określonego klucza przy użyciu adresu URL [logiki aplikacji interfejsu API REST](https://docs.microsoft.com/rest/api/logic/workflowtriggers) w następujący sposób:

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

W treści, będą zawierać właściwości `KeyType` jako `Primary` lub `Secondary`.  Zwróci ono podpisane przez klucz zabezpieczeń określony adres URL.

### <a name="restrict-incoming-ip-addresses"></a>Ogranicz przychodzących adresów IP

Oprócz sygnatura dostępu współdzielonego można ograniczyć wywoływanie aplikacji logiki tylko z określonym klientem.  Na przykład jeśli zarządzasz punktu końcowego za pośrednictwem usługi Azure API Management, można ograniczyć aplikację logiki, aby akceptowała żądania, jeśli żądanie pochodzi z adresu IP wystąpienia interfejsu API zarządzania.

To ustawienie można skonfigurować w ustawieniach aplikacji logiki:

1. W portalu Azure Otwórz aplikację logiki, aby dodać ograniczenia adresu IP
1. Kliknij przycisk **ustawienia przepływu pracy** elementu menu pod **ustawienia**
1. Określ listę zakresów adresów IP na zatwierdzenie przez wyzwalacz

Prawidłowy zakres IP przybiera format `192.168.1.1/255`. Tylko uruchomienie jako aplikacji logiki zagnieżdżonych aplikację logiki, wybierz opcję **tylko innych aplikacji logiki** opcji. Ta opcja powoduje zapisanie pustą tablicę do zasobu, znaczenie tylko wywołania z samej (aplikacje logiki nadrzędnego) usługi wyzwalać pomyślnie.

> [!NOTE]
> Nadal można uruchomić aplikacji logiki z wyzwalaczem żądanie za pośrednictwem interfejsu API REST / zarządzania `/triggers/{triggerName}/run` niezależnie od adresu IP. Ten scenariusz wymaga uwierzytelniania interfejsu API REST Azure, a wszystkie zdarzenia pojawią się w dzienniku inspekcji Azure. Ustaw dostęp do kontrolowania zasad odpowiednio.

#### <a name="setting-ip-ranges-on-the-resource-definition"></a>Ustawienie zakresy adresów IP w definicji zasobu

Jeśli używasz [Szablon wdrożenia](logic-apps-create-deploy-template.md) do automatyzowania wdrożeń, ustawienia zakres IP można skonfigurować przy użyciu szablonu zasobów.  

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

Aby dodać więcej protokołów autoryzacji na podstawie aplikacji logiki, [Azure API Management](https://azure.microsoft.com/services/api-management/) oferuje rozbudowane monitorowanie, zabezpieczenia, zasad i dokumentację dla dowolnego punktu końcowego z możliwością do udostępnienia aplikacji logiki jako interfejs API. Zarządzanie interfejsami API Azure mogą uwidaczniać publicznych lub prywatnych punktu końcowego dla aplikacji logiki, który może używać usługi Azure Active Directory, certyfikat, OAuth lub innych standardów zabezpieczeń. Po odebraniu żądania usługi Azure API Management przekazuje żądanie do aplikacji logiki (wykonanie żadnych ograniczeń locie lub przekształcenia wymagane). Ustawienia przychodzących zakres IP na aplikację logiki umożliwia Zezwalaj tylko na aplikację logiki, aby być wywołany z interfejsu API zarządzania.

## <a name="secure-access-to-manage-or-edit-logic-apps"></a>Bezpieczny dostęp do zarządzania i edytowania aplikacji logiki

Można ograniczyć dostęp do operacji zarządzania na aplikację logiki, tak aby były tylko przez określonych użytkowników lub grupy mogą wykonywać operacje na zasobie. Aplikacje logiki korzystać z platformy Azure [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/role-assignments-portal.md) funkcji i można dostosować za pomocą tych samych narzędzi.  Istnieje kilka wbudowanych ról, które można przypisać członkami subskrypcją również:

* **Współautor aplikacji logiki** — zapewnia dostęp do wyświetlania, edytowania i aktualizowanie aplikacji logiki.  Nie można usunąć zasobu lub wykonywać operacje administracyjne.
* **Operator aplikacji logiki** — można wyświetlić aplikację logiki Historia uruchomień i włączanie/wyłączanie.  Nie można edytować ani aktualizacji definicji.

Można również użyć [Blokada zasobu Azure](../azure-resource-manager/resource-group-lock-resources.md) zmienianie lub usuwanie aplikacji logiki. Ta funkcja jest przydatna, aby zapobiec zasobów produkcyjnych z zmiany i usunięcia.

## <a name="secure-access-to-contents-of-the-run-history"></a>Bezpieczny dostęp do zawartości Historia uruchomień

Z poprzednich używa do określonych zakresów adresów IP, można ograniczyć dostęp do zawartości danych wejściowych lub wyjściowych.  

Wszystkie dane w ramach wykonywania przepływu pracy są szyfrowane przesyłanych i przechowywanych. Po wywołaniu Historia uruchomień, usługa uwierzytelnia żądanie i łącza do żądania i odpowiedzi danych wejściowych i wyjściowych. To łącze mogą być chronione, dlatego tylko żądania do wyświetlenia zawartości z wyznaczonych zakresu adresów IP zwrócona zawartość. Ta funkcja służy do kontroli dostępu dodatkowe. Można nawet określić adres IP, takich jak `0.0.0.0` , nikt nie mógł uzyskać dostępu do danych wejściowych/wyjściowych. Tylko osoby z uprawnieniami administratora może usunąć to ograniczenie, zapewniając możliwość "just-in-time" dostęp do zawartości przepływu pracy.

To ustawienie można skonfigurować w ramach ustawienia zasobów w portalu Azure:

1. W portalu Azure Otwórz aplikację logiki, aby dodać ograniczenia adresu IP
1. Kliknij przycisk **konfiguracji kontroli dostępu** elementu menu pod **ustawienia**
1. Określ listę zakresów adresów IP w celu uzyskania dostępu do zawartości

#### <a name="setting-ip-ranges-on-the-resource-definition"></a>Ustawienie zakresy adresów IP w definicji zasobu

Jeśli używasz [Szablon wdrożenia](logic-apps-create-deploy-template.md) do automatyzowania wdrożeń, ustawienia zakres IP można skonfigurować przy użyciu szablonu zasobów.  

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

## <a name="secure-parameters-and-inputs-within-a-workflow"></a>Zabezpieczanie parametry i dane wejściowe w przepływie pracy

Możesz chcieć parametryzacja niektórych aspektów definicję przepływu pracy dla wdrażania w środowiskach. Ponadto niektóre parametry mogą być bezpieczne parametrów, które nie mają być wyświetlane podczas edytowania przepływu pracy, takich jak identyfikator klienta i klucz tajny klienta dla [uwierzytelniania usługi Azure Active Directory](../connectors/connectors-native-http.md#authentication) akcji HTTP.

### <a name="using-parameters-and-secure-parameters"></a>Przy użyciu parametrów i bezpieczne

Aby uzyskać dostęp do wartości parametru zasobów w czasie wykonywania, [język definicji przepływu pracy](http://aka.ms/logicappsdocs) zapewnia `@parameters()` operacji. Można również [określić parametry w szablonie wdrożenia zasobu](../azure-resource-manager/resource-group-authoring-templates.md#parameters). Ale jeśli określony typ parametru jako `securestring`, parametr nie będą zwracane z pozostałą częścią definicji zasobu i nie będzie dostępny, wyświetlając zasobu po wdrożeniu.

> [!NOTE]
> Jeśli parametr jest używany w nagłówkach i treści żądania, parametr może być widoczna uzyskując dostęp do historii wykonywania i wychodzących żądania HTTP. Upewnij się, że odpowiednio ustawić zasad dostępu do zawartości.
> Nagłówki autoryzacji nie są widoczne za pośrednictwem wejściowych i wyjściowych. Dlatego jeśli klucz tajny jest używany istnieje, nie jest pobieranie klucz tajny.

#### <a name="resource-deployment-template-with-secrets"></a>Szablon wdrożenia zasobów z kluczy tajnych

W poniższym przykładzie przedstawiono wdrożenia, który odwołuje się do parametru secure `secret` w czasie wykonywania. W pliku parametrów oddzielne można określić wartość środowiska `secret`, lub użyj [KeyVault Menedżera zasobów Azure](../azure-resource-manager/resource-manager-keyvault-parameter.md) można pobrać kluczy tajnych na wdrażanie czasu.

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
  "resources": [
    {
      "name": "secret-deploy",
      "type": "Microsoft.Logic/workflows",
      "location": "westus",
      "tags": {
        "displayName": "LogicApp"
      },
      "apiVersion": "2016-06-01",
      "properties": {
        "definition": {
          "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
          "actions": {
            "Call_External_API": {
              "type": "http",
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
    }
  ],
  "outputs": {}
}
```

## <a name="secure-access-to-services-receiving-requests-from-a-workflow"></a>Bezpieczny dostęp do usług odbierania żądań z przepływu pracy

Istnieje wiele sposobów, aby ułatwić zabezpieczanie dowolnego punktu końcowego potrzebuje dostępu do aplikacji logiki.

### <a name="using-authentication-on-outbound-requests"></a>Przy użyciu uwierzytelniania w odpowiedzi na żądania wychodzącego

Podczas pracy z HTTP, HTTP + Swagger (otwarty interfejs API) lub elementu Webhook akcji, można dodać do żądania wysyłane uwierzytelniania. Mogą obejmować uwierzytelnianie podstawowe, uwierzytelnianie certyfikatu lub uwierzytelniania usługi Azure Active Directory. Szczegóły dotyczące sposobu konfigurowania tego uwierzytelniania można znaleźć [w tym artykule](../connectors/connectors-native-http.md#authentication).

### <a name="restricting-access-to-logic-app-ip-addresses"></a>Ograniczanie dostępu do adresów IP aplikacji logiki

Wywołania z aplikacji logiki pochodzą z określonego zestawu adresów IP dla regionu. Można dodać dodatkowe, filtrowanie, aby akceptowała żądania wyznaczonych adresów IP. Aby uzyskać listę adresów IP, zobacz [limity aplikacji logiki i konfiguracji](logic-apps-limits-and-config.md#configuration).

### <a name="on-premises-connectivity"></a>Łączność lokalna

Aplikacje logiki mają integracji z kilku usług, aby zapewnić bezpieczny i niezawodny lokalnej komunikacji.

#### <a name="on-premises-data-gateway"></a>Brama danych lokalnych

Wiele łączników zarządzanych dla usługi logic apps udostępnia bezpieczne połączenie z systemami lokalnymi, w tym systemie plików, SQL, SharePoint i bazy danych DB2. Brama przekazuje dane z lokalnych źródeł w kanałach zaszyfrowane za pomocą usługi Azure Service Bus. Cały ruch pochodzi jako bezpieczny ruch wychodzący z agentem bramy. Dowiedz się więcej o [działanie bramy danych](logic-apps-gateway-install.md#gateway-cloud-service).

#### <a name="azure-api-management"></a>Usługa Azure API Management

[Zarządzanie interfejsami API Azure](https://azure.microsoft.com/services/api-management/) ma opcji łączności lokalnych, włącznie z integracją sieci VPN i ExpressRoute lokacja lokacja dla zabezpieczonych serwera proxy oraz komunikacji z systemami lokalnymi. W Projektancie aplikacji logiki możesz szybko zaznaczyć interfejs API widoczne z usługi Azure API Management w przepływie pracy, zapewniając szybki dostęp do systemów lokalnych.

## <a name="next-steps"></a>Kolejne kroki
[Tworzenie szablonu wdrożenia](logic-apps-create-deploy-template.md)  
[Obsługa wyjątków](logic-apps-exception-handling.md)  
[Monitorowanie aplikacji logiki](logic-apps-monitor-your-logic-apps.md)  
[Diagnozowanie błędów aplikacji logiki i problemy](logic-apps-diagnosing-failures.md)  
