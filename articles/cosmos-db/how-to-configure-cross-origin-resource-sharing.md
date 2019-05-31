---
title: Cross-Origin Resource Sharing (CORS) w usłudze Azure Cosmos DB
description: W tym artykule opisano sposób konfigurowania udostępniania zasobów między źródłami (CORS) w usłudze Azure Cosmos DB przy użyciu witryny Azure portal i szablonów usługi Azure Resource Manager.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dech
ms.openlocfilehash: 1269c4c2405e9b906b63c8a29c0de1ac217da1d7
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241893"
---
# <a name="configure-cross-origin-resource-sharing-cors"></a>Skonfiguruj współużytkowanie zasobów między źródłami (Cors) 

Udostępnianie zasobów między źródłami (CORS) to funkcja protokołu HTTP, który umożliwia aplikacji sieci web uruchomionej w jednej domenie dostęp do zasobów w innej domenie. Przeglądarki sieci Web wdrażają ograniczenie bezpieczeństwa nazywane zasadami jednego źródła, który uniemożliwia strony sieci web wywoływaniu interfejsów API w innej domenie. Jednak mechanizm CORS zapewnia bezpieczną metodę umożliwiania domenę pochodzenia w celu wywoływania interfejsów API w innej domenie. Interfejs API SQL Core w usłudze Azure Cosmos DB obsługuje teraz udostępniania zasobów między źródłami (CORS) przy użyciu nagłówka "allowedOrigins". Po włączeniu obsługi mechanizmu CORS dla konta usługi Azure Cosmos tylko uwierzytelnionego żądania są obliczane, aby określić, czy są dozwolone zgodnie z regułami, które określono.

Można skonfigurować współużytkowanie zasobów, udostępnianie ustawień (między źródłami CORS) w witrynie Azure portal lub przy użyciu szablonu usługi Azure Resource Manager. Interfejs API SQL Core w usłudze Azure Cosmos DB obsługuje biblioteki JavaScript, która działa zarówno w technologii Node.js, jak i oparte na przeglądarce środowiska. Ta biblioteka teraz korzystać z zalet obsługi mechanizmu CORS w trybie bramy. Brak konfiguracji po stronie klienta potrzebnych do korzystania z tej funkcji. Z obsługą mechanizmu CORS zasobów z poziomu przeglądarki można uzyskać dostęp do usługi Azure Cosmos DB za pomocą [biblioteki JavaScript](https://www.npmjs.com/package/@azure/cosmos) lub bezpośrednio z [interfejsu API REST](https://docs.microsoft.com/rest/api/cosmos-db/) dla prostych operacji. 

## <a name="enable-cors-support-from-azure-portal"></a>Włączanie obsługi mechanizmu CORS w witrynie Azure portal

Aby włączyć udostępnianie zasobów między źródłami za pomocą witryny Azure portal, należy użyć następujące czynności:

1. Przejdź do swojego konta usługi Azure cosmos DB. Otwórz **CORS** bloku.

2. Określ listę rozdzielanych przecinkami, źródeł, które mogą ułatwić wywołań między źródłami na koncie usługi Azure Cosmos DB. Na przykład `https://www.mydomain.com`, `https://mydomain.com`, `https://api.mydomain.com`. Można również użyć symbolu wieloznacznego "\*" Aby zezwalać na wszystkie pochodzenia i wybrać **przesyłania**. 

   > [!NOTE]
   > Obecnie nie można używać symboli wieloznacznych jako część nazwy domeny. Na przykład `https://*.mydomain.net` format nie jest jeszcze obsługiwany. 
   
   ![Włącz udostępnianie zasobów między źródłami przy użyciu witryny Azure portal](./media/how-to-configure-cross-origin-resource-sharing/enable-cross-origin-resource-sharing-using-azure-portal.png)
 
## <a name="enable-cors-support-from-resource-manager-template"></a>Włączanie obsługi mechanizmu CORS za pomocą szablonu usługi Resource Manager

Aby włączyć mechanizm CORS za pomocą szablonu usługi Resource Manager, należy dodać sekcję "cors" z właściwością "allowedOrigins" do dowolnego istniejącego szablonu. Następujące dane JSON znajduje się przykład szablonu, który tworzy nowe konto usługi Azure Cosmos z włączonym mechanizmem CORS.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "name": "test",
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "apiVersion": "2015-04-08",
            "location": "East US 2",
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": {
                    "defaultConsistencyLevel": "Session",
                    "maxIntervalInSeconds": 5,
                    "maxStalenessPrefix": 100
                },
                "locations": [
                    {
                        "id": "test-eastus2",
                        "failoverPriority": 0,
                        "locationName": "East US 2"
                    }
                ],
                "cors": [
                    {
                        "allowedOrigins": "*"
                    }
                ]
            },
            "dependsOn": [
            ]
        }
    ]
}
```

## <a name="using-the-azure-cosmos-db-javascript-library-from-a-browser"></a>Przy użyciu biblioteki usługi Azure Cosmos DB JavaScript w przeglądarce

Obecnie biblioteki Azure Cosmos DB JavaScript ma CommonJS wersję biblioteki dostarczane z pakietu. Aby użyć tej biblioteki z przeglądarki, musisz utworzyć przeglądarki zgodnej biblioteki za pomocą narzędzia pakietu zbiorczego aktualizacji lub Webpack. Niektóre Node.js biblioteki powinien mieć mocks przeglądarki dla nich. Oto przykład webpack pliku konfiguracji, który zawiera niezbędne ustawienia makiety.

```javascript
const path = require("path");

module.exports = {
  entry: "./src/index.ts",
  devtool: "inline-source-map",
  node: {
    net: "mock",
    tls: "mock"
  },
  output: {
    filename: "bundle.js",
    path: path.resolve(__dirname, "dist")
  }
};
```
 
Oto [przykładowy kod](https://github.com/christopheranderson/cosmos-browser-sample) używający TypeScript i Webpack z biblioteką zestaw JavaScript SDK usługi Azure Cosmos DB do tworzenia aplikacji zadań do wykonania, która wysyła aktualizacje w czasie rzeczywistym, gdy są tworzone nowe elementy.
Najlepszym rozwiązaniem jest nieużywanie klucz podstawowy do komunikowania się z usługą Azure Cosmos DB za pomocą przeglądarki. Zamiast tego należy użyć tokenów zasobów do komunikowania się. Aby uzyskać więcej informacji na temat tokenów zasobów, zobacz [zabezpieczanie dostępu do usługi Azure Cosmos DB](secure-access-to-data.md#resource-tokens) artykułu.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat innych sposobów, aby zabezpieczyć swoje konto usługi Azure Cosmos, zobacz następujące artykuły:

* [Konfigurowanie zapory dla usługi Azure Cosmos DB](how-to-configure-firewall.md) artykułu.

* [Konfigurowanie sieci wirtualnej i podsieci na podstawie dostępu dla konta usługi Azure Cosmos DB](how-to-configure-vnet-service-endpoint.md)
    

