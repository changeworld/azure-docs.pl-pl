---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f860a0cebefaebe16c40abf008bd24e333eb80d5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30197979"
---
Jeśli chcesz utworzyć połączenie punkt-lokacja z komputerem klienckim innym niż użyty do wygenerowania certyfikatów klienta, należy zainstalować certyfikat klienta. Podczas instalowania certyfikatu klienta potrzebne jest hasło, które zostało utworzone w trakcie eksportowania certyfikatu klienta.

1. Znajdź plik *pfx* i skopiuj go na komputer kliencki. Na komputerze klienckim kliknij dwukrotnie plik *pfx*, aby go zainstalować. W polu **Lokalizacja magazynu** pozostaw wartość **Bieżący użytkownik**, a następnie kliknij pozycję **Dalej**.
2. Na stronie importowania **Plik** nie wprowadzaj żadnych zmian. Kliknij przycisk **Dalej**.
3. Na **ochrony klucza prywatnego** strony, wprowadź hasło dla certyfikatu, lub sprawdź, czy podmiot zabezpieczeń jest poprawna, a następnie kliknij przycisk **dalej**.
4. Na stronie **Magazyn certyfikatów** pozostaw lokalizację domyślną, a następnie kliknij pozycję **Dalej**.
5. Kliknij przycisk **Zakończ**. Na stronie **Ostrzeżenie o zabezpieczeniach** dla instalacji certyfikatu kliknij pozycję **Tak**. Możesz bez obaw kliknąć pozycję „Tak”, ponieważ wygenerowano certyfikat. Certyfikat został pomyślnie zaimportowany.