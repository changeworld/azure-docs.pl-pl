---
title: Plik dyrektywy include
description: Plik dyrektywy include
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: f77a036d41ce551d9eab0250eaf4dc16444b24da
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
## <a name="build-and-run-code-in-kubernetes"></a>Tworzenie i uruchamianie kodu w Kubernetes
Teraz uruchom naszego kodu! W oknie terminalu, uruchom to polecenie z **głównego katalogu z kodem**, webfrontend:

```cmd
azds up
```

Śledzą danych wyjściowych polecenia, można zauważyć kilka rzeczy, zgodnie z jego postępów:
- Kod źródłowy jest synchronizowany do środowiska projektowego na platformie Azure.
- Obraz kontenera jest oparty na platformie Azure, określony przez zasoby Docker w katalogu z kodem.
- Kubernetes obiekty są tworzone wykorzystujące obrazu kontener określony przez wykres Helm w katalogu z kodem.
- Zostaną wyświetlone informacje o kontenera punktów końcowych. W tym przypadku firma Microsoft jest oczekiwany publiczny adres URL protokołu HTTP.
- Zakładając, że powyższe etapy zakończona pomyślnie, powinien rozpocząć zobacz `stdout` (i `stderr`) dane wyjściowe jako kontenera jest uruchamiany.

> [!Note]
> Te kroki będzie trwać dłużej po raz pierwszy `up` polecenie jest uruchamiane, ale kolejnych uruchomieniach powinny trwać krócej.

## <a name="test-the-web-app"></a>Testowanie aplikacji internetowej
Dane wyjściowe konsoli informacji o publiczny adres URL, który został utworzony przez skanowanie `up` polecenia. Będzie on mieć postać: 

`Running at public URL: http://<servicename>-<environmentname>.<guid>.<region>.aksapp.io` 

Otwórz ten adres URL w przeglądarce i powinna zostać wyświetlona aplikacja sieci web, obciążenia. Gdy jest wykonywana kontenera, `stdout` i `stderr` danych wyjściowych jest przesyłane strumieniowo do okno terminalu.
