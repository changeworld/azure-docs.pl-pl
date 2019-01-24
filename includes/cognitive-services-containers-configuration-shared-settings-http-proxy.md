---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2019
ms.openlocfilehash: fd5354491254a216e720dac6487b331f047bd5cb
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54479253"
---
Aby skonfigurować serwer proxy HTTP dla żądania wychodzącego, należy użyć tych dwóch argumentów:

| Name (Nazwa) | Typ danych | Opis |
|--|--|--|
|HTTP_PROXY|ciąg|Serwer proxy, aby użyć, na przykład http://proxy:8888|
|HTTP_PROXY_CREDS|ciąg|wszystkie poświadczenia wymagane do uwierzytelniania serwera proxy, na przykład nazwa_użytkownika: hasło.|

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
mcr.microsoft.com/azure-cognitive-services/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=http://190.169.1.6:3128 \
HTTP_PROXY_CREDS=jerry:123456 \
Logging:Disk:LogLevel=Debug Logging:Disk:Format=json
```