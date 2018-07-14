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
ms.openlocfilehash: 5169432af65a046465c9d1ced349687d6fdc8bb7
ms.sourcegitcommit: caebf2bb2fc6574aeee1b46d694a61f8b9243198
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/12/2018
ms.locfileid: "35394030"
---
Python napotka błąd podczas uruchamiania aplikacji, tylko prosty błąd strony zostaną zwrócone (np. "Nie można wyświetlić strony, ponieważ wystąpił błąd wewnętrzny serwera.").

Do przechwytywania błędów aplikacji w języku Python:

1. W witrynie Azure portal w aplikacji sieci web wybierz **ustawienia**.
2. Na **ustawienia** zaznacz **ustawienia aplikacji**.
3. W obszarze **ustawienia aplikacji**, wprowadź następujące pary klucz/wartość:
    * Klucz: WSGI_LOG
    * Wartość: D:\home\site\wwwroot\logs.txt (wprowadź wybraną nazwę pliku)

Zostaną wyświetlone błędy w pliku logs.txt w folderze wwwroot.
