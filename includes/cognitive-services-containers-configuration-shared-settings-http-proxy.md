---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: fe1b4699a300831294c26b103d322fb83ad87d3b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66116766"
---
Aby skonfigurować serwer proxy HTTP dla żądania wychodzącego, należy użyć tych dwóch argumentów:

| Name (Nazwa) | Typ danych | Opis |
|--|--|--|
|HTTP_PROXY|string|Serwer proxy, aby użyć, na przykład `http://proxy:8888`<br><proxy-url>|
|HTTP_PROXY_CREDS|string|wszystkie poświadczenia wymagane do uwierzytelniania serwera proxy, na przykład nazwa_użytkownika: hasło.|
|`<proxy-user>`|string|Użytkownik, dla serwera proxy.|
|`proxy-password`|string|Hasło skojarzone z `<proxy-user>` dla serwera proxy.|
||||


```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=<proxy-url> \
HTTP_PROXY_CREDS=<proxy-user>:<proxy-password> \
```