---
title: Przygotowanie do zmiany adresu SSL IP — usłudze Azure App Service
description: Jeśli Twój adres SSL IP będzie można zmienić, Dowiedz się, co należy zrobić, dzięki czemu aplikacja będzie nadal działać po zmianie.
services: app-service\web
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 06/28/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 6c8c86ff6212acc31e961d6ae62836ca2b7b7380
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61268912"
---
# <a name="how-to-prepare-for-an-ssl-ip-address-change"></a>Jak przygotować się do zmiany adresu SSL IP

Jeśli otrzymasz powiadomienie, że zmiana jest adres SSL IP aplikacji usługi Azure App Service, postępuj zgodnie z instrukcjami w tym artykule, aby zwolnić istniejącego adresu SSL IP i przypisać nowe.

## <a name="release-ssl-ip-addresses-and-assign-new-ones"></a>Zwolnienie adresów SSL IP i przypisać nowe

1.  Otwórz [portal Azure](https://portal.azure.com).

2.  W menu nawigacji po lewej stronie wybierz **App Services**.

3.  Wybierz aplikację usługi App Service z listy.

4.  W obszarze **ustawienia** nagłówka, kliknij przycisk **ustawienia protokołu SSL** w nawigacji po lewej stronie.

1. W sekcji wiązania SSL zaznacz rekord nazwy hosta. W wyświetlonym edytorze wybierz **SNI SSL** na **typ SSL** menu listy rozwijanej i kliknij przycisk **Dodawanie powiązania**. Gdy pojawi się komunikat o powodzeniu operacji, został wydany istniejącego adresu IP.

6.  W **powiązania SSL** ponownie wybierz ten sam rekord nazwy hosta, za pomocą certyfikatu. W edytorze, która zostanie otwarta, tym razem wybierz **SSL opartych na protokole IP** na **typ SSL** menu listy rozwijanej i kliknij przycisk **Dodawanie powiązania**. Gdy pojawi się komunikat o powodzeniu operacji, nabyciu nowego adresu IP.

7.  Jeśli rekord A (wskazuje bezpośrednio na Twój adres IP rekordu DNS) jest skonfigurowany w portalu rejestracji domeny (innych firm w usłudze DNS platformy Azure lub dostawcy DNS), zamienić istniejącego adresu IP na nowo wygenerowane. Nowy adres IP można znaleźć, postępując zgodnie z instrukcjami w następnej sekcji.

## <a name="find-the-new-ssl-ip-address-in-the-azure-portal"></a>Znajdź nowy adres SSL IP w witrynie Azure Portal

1.  Poczekaj kilka minut, a następnie otwórz [witryny Azure portal](https://portal.azure.com).

2.  W menu nawigacji po lewej stronie wybierz **App Services**.

3.  Wybierz aplikację usługi App Service z listy.

4.  W obszarze **ustawienia** nagłówka, kliknij przycisk **właściwości** w nawigacji po lewej stronie, a następnie znajdź sekcja o nazwie **wirtualny adres IP**.

5. Skopiuj adres IP, a następnie ponownie skonfiguruj swoje rekord domeny lub adresu IP mechanizmu.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule opisano sposób przygotowania do zmiany adresu IP, które zostało zainicjowane przez platformę Azure. Aby uzyskać więcej informacji na temat adresów IP w usłudze Azure App Service, zobacz [adresy protokołu SSL i SSL IP w usłudze Azure App Service](overview-inbound-outbound-ips.md).
