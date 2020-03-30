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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183835"
---
Jeśli Python napotka błąd podczas uruchamiania aplikacji, zostanie zwrócona tylko prosta strona błędu (np. "Strona nie może być wyświetlana, ponieważ wystąpił wewnętrzny błąd serwera.").

Aby przechwycić błędy aplikacji języka Python:

1. W witrynie Azure portal w aplikacji sieci Web wybierz pozycję **Ustawienia**.
2. Na karcie **Ustawienia** wybierz pozycję **Ustawienia aplikacji**.
3. W obszarze **Ustawienia aplikacji**wprowadź następującą parę klucz/wartość:
    * Klawisz : WSGI_LOG
    * Wartość : D:\home\site\wwwroot\logs.txt (wprowadź wybór nazwy pliku)

Błędy powinny być teraz widoczne w pliku logs.txt w folderze wwwroot.
