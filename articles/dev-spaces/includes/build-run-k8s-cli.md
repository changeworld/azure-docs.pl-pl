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
ms.openlocfilehash: 1cf301ab3b4430c7bb79f6fdd72243f9f2ad1d9b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
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
- Zostaną wyświetlone informacje o kontenera punktów końcowych. W tym przypadku firma Microsoft jest oczekiwany publiczny adres URL HTTPS.
- Zakładając, że powyższe etapy zakończona pomyślnie, powinien rozpocząć zobacz `stdout` (i `stderr`) dane wyjściowe jako kontenera jest uruchamiany.

> [!Note]
> Te kroki będzie trwać dłużej po raz pierwszy `up` polecenie jest uruchamiane, ale kolejnych uruchomieniach powinny trwać krócej.

## <a name="test-the-web-app"></a>Testowanie aplikacji internetowej
Dane wyjściowe konsoli informacji o publiczny adres URL, który został utworzony przez skanowanie `up` polecenia. Będzie on mieć postać: 

`Running at public URL: https://<servicename>-<environmentname>.<guid>.<region>.aksapp.io` 

Otwórz ten adres URL w przeglądarce i powinna zostać wyświetlona aplikacja sieci web, obciążenia. Gdy jest wykonywana kontenera, `stdout` i `stderr` danych wyjściowych jest przesyłane strumieniowo do okno terminalu.
