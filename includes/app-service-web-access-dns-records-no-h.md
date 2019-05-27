---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: d001d76bca5b9a0837349b6e05b3b0a271ea3a73
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66136273"
---
> [!NOTE]
> Usługa Azure DNS umożliwia skonfigurowanie niestandardowej nazwy DNS dla aplikacji Azure Web Apps. Aby uzyskać więcej informacji, zobacz [Use Azure DNS to provide custom domain settings for an Azure service](../articles/dns/dns-custom-domain.md#app-service-web-apps) (Korzystanie z usługi Azure DNS w celu udostępnienia niestandardowych ustawień domeny dla usługi platformy Azure).
>
>

Zaloguj się do witryny internetowej dostawcy domeny.

Znajdź stronę służącą do zarządzania rekordami DNS. Każdy dostawca domeny ma własny interfejs rekordów DNS, dlatego zapoznaj się z dokumentacją dostawcy. Poszukaj obszarów witryny z etykietą **Nazwa domeny**, **DNS** lub **Zarządzanie serwerami nazw**. 

Często stronę rekordów DNS można znaleźć, wyświetlając informacje o koncie, a następnie szukając linków takich jak **Moja domena**. Przejdź do tej strony, a następnie poszukaj linku o nazwie podobnej do **Plik strefy**, **Rekordy DNS** lub **Konfiguracja zaawansowana**.

Poniższy zrzut ekranu przedstawia przykład strony rekordów DNS:

![Przykładowa strona rekordów DNS](./media/app-service-web-access-dns-records-no-h/example-record-ui.png)

Na ekranie z przykładu należy wybrać pozycję **Dodaj**, aby utworzyć rekord. Niektórzy dostawcy udostępniają różne linki na potrzeby dodawania różnych typów rekordów. Zapoznaj się z dokumentacją dostawcy.

> [!NOTE]
> W przypadku niektórych dostawców, np. GoDaddy, zmiany rekordów DNS nie zaczynają obowiązywać, dopóki nie wybierzesz oddzielnego linku **Zapisz zmiany**. 
