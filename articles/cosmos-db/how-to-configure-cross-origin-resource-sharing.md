---
title: Udostępnianie zasobów między źródłami (CORS) w usłudze Azure Cosmos DB
description: W tym artykule opisano sposób konfigurowania udostępniania zasobów między źródłami (CORS) w usłudze Azure Cosmos DB przy użyciu szablonów witryny Azure portal i usługi Azure Resource Manager.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: dech
ms.openlocfilehash: 7a487cb10965a379a0a418efaa061be88c5d10dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77082976"
---
# <a name="configure-cross-origin-resource-sharing-cors"></a>Konfigurowanie udostępniania zasobów między źródłami (CORS)

Udostępnianie zasobów między źródłami (CORS) to funkcja HTTP, która umożliwia aplikacji sieci web działającej w jednej domenie dostęp do zasobów w innej domenie. Przeglądarki sieci Web implementują ograniczenie zabezpieczeń znane jako zasady tego samego źródła, które uniemożliwia stronie sieci Web wywoływanie interfejsów API w innej domenie. Jednak cors zapewnia bezpieczny sposób, aby umożliwić domeny pochodzenia do wywoływania interfejsów API w innej domenie. Interfejs API core (SQL) w usłudze Azure Cosmos DB obsługuje teraz udostępnianie zasobów między źródłami (CORS) przy użyciu nagłówka "allowedOrigins". Po włączeniu obsługi CORS dla konta usługi Azure Cosmos tylko uwierzytelnione żądania są oceniane w celu ustalenia, czy są one dozwolone zgodnie z określonymi regułami.

Ustawienie udostępniania zasobów między źródłami (CORS) można skonfigurować z witryny Azure portal lub z szablonu usługi Azure Resource Manager. W przypadku kont usługi Cosmos przy użyciu interfejsu API Core (SQL) usługa Azure Cosmos DB obsługuje bibliotekę JavaScript, która działa zarówno w systemie Node.js, jak i w środowiskach opartych na przeglądarce. Ta biblioteka może teraz korzystać z obsługi CORS podczas korzystania z trybu bramy. Do korzystania z tej funkcji nie jest wymagana żadna konfiguracja po stronie klienta. Dzięki obsłudze cors zasoby z przeglądarki mogą bezpośrednio uzyskiwać dostęp do usługi Azure Cosmos DB za pośrednictwem [biblioteki JavaScript](https://www.npmjs.com/package/@azure/cosmos) lub bezpośrednio z [interfejsu API REST](https://docs.microsoft.com/rest/api/cosmos-db/) w celu prostych operacji.

> [!NOTE]
> Obsługa usługi CORS ma zastosowanie i jest obsługiwana tylko dla interfejsu API programu Azure Cosmos DB Core (SQL). Nie ma zastosowania do interfejsów API bazy danych usługi Azure Cosmos dla cassandra, Gremlin lub MongoDB, ponieważ te protokoły nie używają protokołu HTTP do komunikacji klient-serwer.

## <a name="enable-cors-support-from-azure-portal"></a>Włącz obsługę CORS w witrynie Azure portal

Aby włączyć udostępnianie zasobów między źródłami przy użyciu witryny Azure portal, należy wykonać następujące czynności:

1. Przejdź do konta usługi Azure cosmos DB. Otwórz ostrze **CORS.**

2. Określ oddzieloną przecinkami listę źródeł, które mogą wykonywać wywołania między źródłami na koncie usługi Azure Cosmos DB. Na przykład `https://www.mydomain.com` `https://mydomain.com`, `https://api.mydomain.com`, . Można również użyć symbolu\*wieloznacznego " ", aby zezwolić na wszystkie źródła i wybrać **Prześlij**. 

   > [!NOTE]
   > Obecnie nie można używać symboli wieloznacznych jako części nazwy domeny. Na `https://*.mydomain.net` przykład format nie jest jeszcze obsługiwany. 

   ![Włączanie udostępniania zasobów między źródłami przy użyciu witryny Azure portal](./media/how-to-configure-cross-origin-resource-sharing/enable-cross-origin-resource-sharing-using-azure-portal.png)

## <a name="enable-cors-support-from-resource-manager-template"></a>Włącz obsługę CORS z szablonu Menedżera zasobów

Aby włączyć cors przy użyciu szablonu Menedżera zasobów, dodaj sekcję "cors" z właściwością "allowedOrigins" do dowolnego istniejącego szablonu. Poniższy JSON jest przykładem szablonu, który tworzy nowe konto usługi Azure Cosmos z włączoną usługą CORS.

```json
{
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "name": "[variables('accountName')]",
  "apiVersion": "2019-08-01",
  "location": "[parameters('location')]",
  "kind": "GlobalDocumentDB",
  "properties": {
    "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
    "locations": "[variables('locations')]",
    "databaseAccountOfferType": "Standard",
    "cors": [
      {
        "allowedOrigins": "*"
      }
    ]
  }
}
```

## <a name="using-the-azure-cosmos-db-javascript-library-from-a-browser"></a>Korzystanie z biblioteki JavaScript usługi Azure Cosmos DB z przeglądarki

Obecnie biblioteka JavaScript usługi Azure Cosmos DB ma tylko wersję CommonJS biblioteki dostarczanej z pakietem. Aby użyć tej biblioteki z przeglądarki, należy użyć narzędzia, takiego jak pakiet zbiorczy lub pakiet internetowy, aby utworzyć bibliotekę zgodną z przeglądarką. Niektóre biblioteki Node.js powinny mieć makiety przeglądarki dla nich. Poniżej przedstawiono przykład pliku konfiguracyjnego webpack, który ma niezbędne ustawienia makiety.

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
 
Oto [przykład kodu,](https://github.com/christopheranderson/cosmos-browser-sample) który używa TypeScript i Webpack z biblioteką Azure Cosmos DB JavaScript SDK do tworzenia aplikacji Todo, która wysyła aktualizacje w czasie rzeczywistym podczas tworzenia nowych elementów.
Najlepszym rozwiązaniem nie należy używać klucza podstawowego do komunikowania się z usługą Azure Cosmos DB z przeglądarki. Zamiast tego użyj tokenów zasobów do komunikowania się. Aby uzyskać więcej informacji na temat tokenów zasobów, zobacz [zabezpieczanie dostępu do usługi Azure Cosmos DB](secure-access-to-data.md#resource-tokens) artykuł.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o innych sposobach zabezpieczenia konta usługi Azure Cosmos, zobacz następujące artykuły:

* [Konfigurowanie zapory dla usługi Azure Cosmos DB](how-to-configure-firewall.md) artykułu.

* [Konfigurowanie dostępu opartego na sieci wirtualnej i podsieci dla konta usługi Azure Cosmos DB](how-to-configure-vnet-service-endpoint.md)
