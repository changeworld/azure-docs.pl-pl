---
title: Tworzenie niestandardowych stron błędów bramy aplikacji platformy Azure
description: W tym artykule pokazano, jak utworzyć niestandardowe strony błędów bramy aplikacji. W przypadku niestandardowych stron błędów możesz użyć własnych oznakowań i układu.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: victorh
ms.openlocfilehash: ff11f686287498fe12b31d15a630178bb12035ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74129865"
---
# <a name="create-application-gateway-custom-error-pages"></a>Tworzenie niestandardowych stron błędów bramy aplikacji

Usługa Application Gateway umożliwia tworzenie niestandardowych stron błędów wyświetlanych zamiast domyślnych strony błędów. W przypadku niestandardowych stron błędów możesz użyć własnych oznakowań i układu.

Na przykład można zdefiniować własną stronę konserwacji, jeśli aplikacja sieci web nie jest osiągalna. Można też utworzyć stronę nieautoryzowanego dostępu, jeśli złośliwe żądanie zostanie wysłane do aplikacji sieci web.

Niestandardowe strony błędów są obsługiwane dla następujących dwóch scenariuszy:

- **Strona konserwacji** — ta niestandardowa strona błędu jest wysyłana zamiast strony 502 złej bramy. Jest on wyświetlany, gdy brama aplikacji nie ma zaplecza do kierowania ruchu do. Na przykład, gdy istnieje zaplanowana konserwacja lub gdy nieprzewidziany problem wpływa na dostęp do puli wewnętrznej bazy danych.
- **Strona nieautoryzowanego dostępu** — ta niestandardowa strona błędu jest wysyłana zamiast strony nieautoryzowanego dostępu 403. Jest on wyświetlany, gdy usługa WAF bramy aplikacji wykrywa złośliwy ruch i blokuje go.

Jeśli błąd pochodzi z serwerów wewnętrznej bazy danych, a następnie jest przekazywana wzdłuż niezmodyfikowane z powrotem do wywołującego. Niestandardowa strona błędu nie jest wyświetlana. Brama aplikacji może wyświetlać niestandardową stronę błędu, gdy żądanie nie może dotrzeć do wewnętrznej bazy danych.

Niestandardowe strony błędów można zdefiniować na poziomie globalnym i na poziomie odbiornika:

- **Poziom globalny** — strona błędu ma zastosowanie do ruchu dla wszystkich aplikacji sieci web wdrożonych w tej bramie aplikacji.
- **Poziom odbiornika** — strona błędu jest stosowana do ruchu odebranego w tym odbiorniku.
- **Oba** — niestandardowa strona błędu zdefiniowana na poziomie odbiornika zastępuje jeden zestaw na poziomie globalnym.

Aby utworzyć niestandardową stronę błędu, musisz mieć:

- kod stanu odpowiedzi HTTP.
- odpowiednią lokalizację dla strony błędu. 
- publicznie dostępny obiekt blob magazynu platformy Azure dla tej lokalizacji.
- typu rozszerzenia *.htm lub *.html. 

Rozmiar strony błędu musi być mniejszy niż 1 MB. Jeśli na stronie błędu są połączone obrazy, muszą one być publicznie dostępne bezwzględne adresy URL lub obraz zakodowany base64 w linii na niestandardowej stronie błędu. Łącza względne z obrazami w tej samej lokalizacji obiektu blob nie są obecnie obsługiwane. 

Po określeniu strony błędu brama aplikacji pobiera ją z lokalizacji obiektu blob magazynu i zapisuje w lokalnej pamięci podręcznej bramy aplikacji. Następnie strona błędu jest obsługiwana bezpośrednio z bramy aplikacji. Aby zmodyfikować istniejącą stronę błędu niestandardowego, należy wskazać inną lokalizację obiektu blob w konfiguracji bramy aplikacji. Brama aplikacji nie okresowo sprawdza lokalizację obiektu blob, aby pobrać nowe wersje.

## <a name="portal-configuration"></a>Konfiguracja portalu

1. Przejdź do bramy aplikacji w portalu i wybierz bramę aplikacji.

    ![przegląd ag](media/custom-error/ag-overview.png)
2. Kliknij **pozycję Detektory** i przejdź do określonego odbiornika, w którym chcesz określić stronę błędu.

    ![Detektory bramy aplikacji](media/custom-error/ag-listener.png)
3. Skonfiguruj niestandardową stronę błędu dla błędu WAF 403 lub strony konserwacji 502 na poziomie odbiornika.

    > [!NOTE]
    > Tworzenie stron błędów niestandardowych na poziomie globalnym z witryny Azure portal nie jest obecnie obsługiwane.

4. Określ publicznie dostępny adres URL obiektu blob dla danego kodu stanu błędu i kliknij przycisk **Zapisz**. Brama aplikacji jest teraz skonfigurowana ze stroną błędu niestandardowego.

   ![Kody błędów bramy aplikacji](media/custom-error/ag-error-codes.png)

## <a name="azure-powershell-configuration"></a>Konfiguracja programu Azure PowerShell

Za pomocą programu Azure PowerShell można skonfigurować niestandardową stronę błędu. Na przykład globalna strona błędu niestandardowego:

`$updatedgateway = Add-AzApplicationGatewayCustomError -ApplicationGateway $appgw -StatusCode HttpStatus502 -CustomErrorPageUrl $customError502Url`

Lub strona błędu poziomu odbiornika:

`$updatedlistener = Add-AzApplicationGatewayHttpListenerCustomError -HttpListener $listener01 -StatusCode HttpStatus502 -CustomErrorPageUrl $customError502Url`

Aby uzyskać więcej informacji, zobacz [Add-AzApplicationGatewayCustomError](https://docs.microsoft.com/powershell/module/az.network/add-azapplicationgatewaycustomerror?view=azps-1.2.0) i [Add-AzApplicationGatewayHttpListenerCustomError](https://docs.microsoft.com/powershell/module/az.network/add-azapplicationgatewayhttplistenercustomerror?view=azps-1.3.0).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat diagnostyki bramy aplikacji, zobacz [Kondycja zaplecza, dzienniki diagnostyczne i metryki bramy aplikacji](application-gateway-diagnostics.md).