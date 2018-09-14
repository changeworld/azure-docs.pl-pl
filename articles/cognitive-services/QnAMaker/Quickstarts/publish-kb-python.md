---
title: Szybki start dla języka Python — publikowanie bazy wiedzy — Azure Cognitive Services | Microsoft Docs
description: W jaki sposób opublikować bazę wiedzy za pomocą języka Python w usłudze QnA Maker.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.technology: qna-maker
ms.topic: quickstart
ms.date: 06/18/2018
ms.author: nolachar
ms.openlocfilehash: 0b4c08f2c0f5921275ac191f684ce0c4cbdf1eaf
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "43771019"
---
# <a name="publish-a-knowledge-base-in-python"></a>Publikowanie bazy wiedzy za pomocą języka Python

Poniższy kod publikuje istniejącą bazę wiedzy za pomocą metody [Publish](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe).

1. Utwórz nowy projekt języka Python w Twoim ulubionym środowisku IDE.
2. Dodaj kod przedstawiony poniżej.
3. Zastąp wartość `key` kluczem dostępu właściwym dla Twojej subskrypcji.
4. Uruchom program.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE';

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def publish_kb (path, content):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len (content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("POST", path, content, headers)
    response = conn.getresponse ()

    if response.status == 204:
        return json.dumps({'result' : 'Success.'})
    else:
        return response.read ()

path = service + method + kb
result = publish_kb (path, '')
print (pretty_print(result))
```

## <a name="the-publish-a-knowledge-base-response"></a>Tworzenie i publikowanie odpowiedzi bazy wiedzy

Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź w formacie JSON, jak pokazano w następującym przykładzie:

```json
{
  "result": "Success."
}
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference (Dokumentacja interfejsu API REST usługi QnA Maker w wersji 4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)

[Omówienie usługi QnA Maker](../Overview/overview.md)