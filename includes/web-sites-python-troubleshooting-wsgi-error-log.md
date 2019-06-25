---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/11/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 020e59f029b09f3c7656f67039731e4141e68d31
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183835"
---
Python napotka błąd podczas uruchamiania aplikacji, tylko prosty błąd strony zostaną zwrócone (np. "Nie można wyświetlić strony, ponieważ wystąpił błąd wewnętrzny serwera.").

Do przechwytywania błędów aplikacji w języku Python:

1. W witrynie Azure portal w aplikacji sieci web wybierz **ustawienia**.
2. Na **ustawienia** zaznacz **ustawienia aplikacji**.
3. W obszarze **ustawienia aplikacji**, wprowadź następujące pary klucz/wartość:
    * Klucz: WSGI_LOG
    * Wartość: D:\home\site\wwwroot\logs.txt (wprowadź wybraną nazwę pliku)

Zostaną wyświetlone błędy w pliku logs.txt w folderze wwwroot.
