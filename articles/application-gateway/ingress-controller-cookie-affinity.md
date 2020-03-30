---
title: Włącz koligacji opartej na plikach cookie z bramą aplikacji
description: Ten artykuł zawiera informacje dotyczące włączania koligacji opartej na plikach cookie za pomocą bramy aplikacji.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: adacd805e736524fd7956c4bbc0ad402980b4cd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795991"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>Włącz koligacji opartej na plikach cookie z bramą aplikacji
Jak opisano w [dokumentacji bramy aplikacji platformy Azure, brama](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#http-settings)aplikacji obsługuje koligacji opartej na plikach cookie, co oznacza, że może kierować kolejny ruch z sesji użytkownika do tego samego serwera do przetwarzania.

## <a name="example"></a>Przykład
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```
