---
title: Tworzenie strony błędów niestandardowych usługi Azure Application Gateway
description: W tym artykule pokazano, jak utworzyć bramę aplikacji strony błędów niestandardowych.
services: application-gateway
author: amitsriva
ms.service: application-gateway
ms.topic: article
ms.date: 10/11/2018
ms.author: victorh
ms.openlocfilehash: 2f76347105743538e9fc1d7588ecb949f2675696
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "49071225"
---
# <a name="create-application-gateway-custom-error-pages"></a>Tworzenie bramy Application Gateway strony błędów niestandardowych

Usługa Application Gateway umożliwia tworzenie strony błędów niestandardowych zamiast domyślnej strony błędów. Można użyć własnymi znakowania i układu za pomocą niestandardowej strony błędu.

Na przykład można zdefiniować własną stronę konserwacji, jeśli aplikacja sieci web nie jest dostępny. Lub możesz utworzyć stronę przed nieautoryzowanym dostępem, jeśli złośliwe żądanie jest wysyłane do aplikacji sieci web.

Strony błędów niestandardowych są obsługiwane w następujących dwóch scenariuszach:

- **Strona obsługi** — ta strona błędu niestandardowego jest wysyłany zamiast strony 502 — Zła brama. Jest on wyświetlany, gdy brama aplikacji ma bez zaplecza ma kierować ruch. Na przykład, gdy zaplanowano konserwacji lub gdy nieprzewidziany problem ma wpływ do puli zaplecza.
- **Strona przed nieautoryzowanym dostępem** — ta strona błędu niestandardowego jest wysyłany zamiast strony 403 przed nieautoryzowanym dostępem. Go jest wyświetlany, gdy brama aplikacji zapory aplikacji internetowych wykrywa złośliwy ruch i blokuje ją.

Jeśli błąd pochodzi z serwerów wewnętrznej bazy danych, następnie go jest przekazywany niezmodyfikowanego wstecz do obiektu wywołującego. Nie jest wyświetlana strona błędu niestandardowego. Usługa Application gateway mogą wyświetlać niestandardowej strony błędu, gdy żądanie nie może nawiązać połączenia wewnętrznej bazy danych.

Strony błędów niestandardowych można zdefiniować na poziomie globalnym i poziomie odbiornika:

- **Poziom globalny** — strona błędu ma zastosowanie do ruchu dla aplikacji sieci web wdrażane w tej bramie aplikacji.
- **Poziom odbiornika** — strona błędu jest stosowany do ruch odebrany przez ten odbiornik.
- **Zarówno** — strona błędu niestandardowego, zdefiniowany na poziomie odbiornika zastępuje jednego zestawu na poziomie globalnym.

Aby utworzyć stronę błędu niestandardowego, musisz mieć:
- Kod stanu odpowiedzi HTTP.
- odpowiedniej lokalizacji strony błędu. 
- obiektu blob magazynu Azure ogólnie dostępny dla lokalizacji.
- *.htm lub *.html typ rozszerzenia. 

Rozmiar strony błędu musi być mniejszy niż 1 MB. W przypadku obrazów w stronę błędu musi być dostępny publicznie bezwzględne adresy URL lub zakodowane w formacie base64 obrazu bezpośrednio w stronę błędu niestandardowego. Linki względne z obrazów w tej samej lokalizacji obiektu blob nie są obecnie obsługiwane. 

Po określeniu stronę błędu, bramy aplikacji powoduje jego pobranie z lokalizacji magazynu obiektów blob i zapisuje go do aplikacji lokalnej pamięci podręcznej bramy. Następnie stronę błędu są dostarczane bezpośrednio z bramy aplikacji. Aby zmodyfikować istniejącą stronę błędu niestandardowego, musi wskazywać lokalizację różnych obiektów blob w konfiguracji bramy aplikacji. Application gateway nie okresowo sprawdza lokalizacji obiektu blob do pobrania nowych wersji.

## <a name="portal-configuration"></a>Konfiguracja portalu

1. Przejdź do usługi Application Gateway w portalu i wybierz bramę aplikacji.

    ![Grupa dostępności — omówienie](media/custom-error/ag-overview.png)
2. Kliknij przycisk **odbiorników** i przejdź do określonego odbiornika, które chcesz określić stronę błędu.

    ![Odbiorniki bramy aplikacji](media/custom-error/ag-listener.png)
3. Konfigurowanie niestandardowej strony błędu 403 błędu zapory aplikacji sieci Web lub strony 502 konserwacji na poziomie odbiornika.

    > [!NOTE]
    > Tworzenie globalnego poziomu błędów niestandardowych stron w witrynie Azure portal nie jest obecnie obsługiwane.

4. Określ adres URL dostępny publicznie obiektu blob dla kodu stanu danego błędu, a następnie kliknij przycisk **Zapisz**. Application Gateway jest skonfigurowany za pomocą niestandardowej strony błędu.

   ![Kody błędów bramy aplikacji](media/custom-error/ag-error-codes.png)
## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać informacji na temat diagnostyki usługi Application Gateway, zobacz [kondycja zaplecza, dzienniki diagnostyczne i metryki dla usługi Application Gateway](application-gateway-diagnostics.md).