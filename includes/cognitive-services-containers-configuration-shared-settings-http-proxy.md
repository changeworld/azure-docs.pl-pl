---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2019
ms.openlocfilehash: 2d3d7b37721ca1b19f5d73133352cabdbffe6d68
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58886852"
---
Aby skonfigurować serwer proxy HTTP dla żądania wychodzącego, należy użyć tych dwóch argumentów:

| Name (Nazwa) | Typ danych | Opis |
|--|--|--|
|HTTP_PROXY|string|Serwer proxy, aby użyć, na przykład `http://proxy:8888`|
|HTTP_PROXY_CREDS|string|wszystkie poświadczenia wymagane do uwierzytelniania serwera proxy, na przykład nazwa_użytkownika: hasło.|

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=http://190.169.1.6:3128 \
HTTP_PROXY_CREDS=jerry:123456 \
```