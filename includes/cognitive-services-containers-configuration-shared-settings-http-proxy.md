---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 84cd8ed79281b005407b5a857398b5669635c072
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68320537"
---
Jeśli chcesz skonfigurować serwer proxy HTTP do wysyłania żądań wychodzących, użyj tych dwóch argumentów:

| Nazwa | Typ danych | Opis |
|--|--|--|
|HTTP_PROXY|ciąg|Serwer proxy do wykorzystania, na przykład,`http://proxy:8888`<br>`<proxy-url>`|
|HTTP_PROXY_CREDS|ciąg|Wszelkie poświadczenia potrzebne do uwierzytelnienia względem serwera proxy, na przykład nazwa_użytkownika:hasło.|
|`<proxy-user>`|ciąg|Użytkownik serwera proxy.|
|`<proxy-password>`|ciąg|Hasło skojarzone `<proxy-user>` z serwerem proxy.|
||||


```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=<proxy-url> \
HTTP_PROXY_CREDS=<proxy-user>:<proxy-password> \
```
