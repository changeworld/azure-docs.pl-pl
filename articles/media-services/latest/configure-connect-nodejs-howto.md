---
title: Łączenie usługi Azure Media Services v3 API — Node.js
description: Dowiedz się, jak nawiązać połączenie usługi Media Services v3 z interfejsem API w środowisku Node.js.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: juliako
ms.openlocfilehash: 40880a2c28ce28a671930ef8837082247e61e24b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733134"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Łączenie z Media Services v3 API — Node.js

W tym artykule pokazano, jak połączyć się z usługi Azure Media Services v3 node.js w zestawie SDK w metodzie przy użyciu znaku nazwy głównej usługi.

## <a name="prerequisites"></a>Wymagania wstępne

- Zainstaluj [Node.js](https://nodejs.org/en/download/).
- [Utwórz konto usługi Media Services](create-account-cli-how-to.md). Należy pamiętać, nazwę grupy zasobów i nazwę konta usługi Media Services.

## <a name="create-packagejson"></a>Create package.json

1. Utwórz plik package.json przy użyciu ulubionego edytora.
1. Otwórz plik i wklej następujący kod:

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.js",
  "dependencies": {
    "azure-arm-mediaservices": "^4.1.0",
    "azure-storage": "^2.8.0",
    "ms-rest": "^2.3.3",
    "ms-rest-azure": "^2.5.5"
  }
}
```

Należy określić następujące pakiety:

|Pakiet|Opis|
|---|---|
|`azure-arm-mediaservices`|Usługi Azure Media Services SDK. <br/>Aby upewnić się, w przypadku korzystania z najnowszego pakietu usługi Azure Media Services, zapoznaj się z [NPM install azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/).|
|`azure-storage`|Zestaw SDK usługi Storage. Używany podczas przekazywania plików na zasoby.|
|`ms-rest-azure`| Użyte do zalogowania.|

Można uruchomić następujące polecenie, aby upewnić się, że używasz najnowszego pakietu:

```
npm install azure-arm-mediaservices
```

## <a name="connect-to-nodejs-client"></a>Nawiązywać połączenia z klientem platformy Node.js

1. Utwórz plik js, za pomocą ulubionego edytora.
1. Otwórz plik i wklej następujący kod.
1. Ustaw wartości w sekcji "Konfiguracja punktu końcowego" do wartości uzyskana z [dostęp do interfejsów API](access-api-cli-how-to.md).

```js
'use strict';

const MediaServices = require('azure-arm-mediaservices');
const msRestAzure = require('ms-rest-azure');
const msRest = require('ms-rest');
const azureStorage = require('azure-storage');

// endpoint config
// make sure your URL values end with '/'
const armAadAudience = "";
const aadEndpoint = "";
const armEndpoint = "";
const subscriptionId = "";
const accountName = "";
const region = "";
const aadClientId = "";
const aadSecret = "";
const aadTenantId = "";
const resourceGroup = "";

let azureMediaServicesClient;

///////////////////////////////////////////
//     Entrypoint for sample script      //
///////////////////////////////////////////

msRestAzure.loginWithServicePrincipalSecret(aadClientId, aadSecret, aadTenantId, {
  environment: {
    activeDirectoryResourceId: armAadAudience,
    resourceManagerEndpointUrl: armEndpoint,
    activeDirectoryEndpointUrl: aadEndpoint
  }
}, async function(err, credentials, subscriptions) {
    if (err) return console.log(err);
    azureMediaServicesClient = new MediaServices(credentials, subscriptionId, armEndpoint, { noRetryPolicy: true });
    
    console.log("connected");

});
```

## <a name="run-your-app"></a>Uruchamianie aplikacji

Otwórz wiersz polecenia. Przejdź do katalogu próbki, a następnie wykonaj następujące polecenia:

```
npm install 
node index.js
```

## <a name="see-also"></a>Zobacz także

- [Pojęcia dotyczące usługi Media Services](concepts-overview.md)
- [Instalacja menedżera NPM — azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/)

## <a name="next-steps"></a>Kolejne kroki

Przeanalizuj [dokumentację oprogramowania Node.js](https://aka.ms/ams-v3-nodejs-ref) usługi Media Services i zapoznaj się z [przykładami](https://github.com/Azure-Samples/media-services-v3-node-tutorials) pokazującymi, jak używać interfejsu API usługi Media Services przy użyciu środowiska node.js.

