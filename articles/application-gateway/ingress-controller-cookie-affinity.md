---
title: Włącz koligację opartą na plikach cookie przy użyciu Application Gateway
description: Ten artykuł zawiera informacje na temat włączania koligacji opartej na plikach cookie przy użyciu Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 10/23/2019
ms.author: caya
ms.openlocfilehash: cb1a403888f8dca78d1af7395095aa2bac39935e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513577"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>Włącz koligację opartą na plikach cookie przy użyciu Application Gateway
Zgodnie z opisem w [dokumentacji usługi Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#http-settings)Application Gateway obsługuje koligację opartą na plikach cookie, co oznacza, że może kierować kolejny ruch z sesji użytkownika do tego samego serwera w celu przetworzenia.

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
