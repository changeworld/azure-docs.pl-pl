---
title: Przygotowanie do zmiany adresu IP dla ruchu przychodzącego — usłudze Azure App Service
description: Jeśli adres IP dla ruchu przychodzącego będzie można zmienić, Dowiedz się, co należy zrobić, dzięki czemu aplikacja będzie nadal działać po zmianie.
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
ms.openlocfilehash: aaa89b5a3bb1af6878ed21e0160a534a1c989228
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61270068"
---
# <a name="how-to-prepare-for-an-inbound-ip-address-change"></a>Jak przygotować się do zmian ruchu przychodzącego adresu IP

Jeśli otrzymasz powiadomienie, że zmiana jest adres IP dla ruchu przychodzącego aplikację usługi Azure App Service, postępuj zgodnie z instrukcjami w tym artykule.

## <a name="determine-if-you-have-to-do-anything"></a>Określić, czy istnieją podejmować żadnych działań

* Opcja 1: Jeśli aplikację usługi App Service nie ma domena niestandardowa, jest wymagana żadna akcja.

* Opcja 2: Jeśli rekord CNAME (identyfikator URI wskazujący rekord DNS) jest skonfigurowany w portalu rejestracji domeny (innych firm w usłudze DNS platformy Azure lub dostawcy DNS), tylko jest wymagana żadna akcja.

* Opcja 3: Jeśli rekord A (wskazuje bezpośrednio na Twój adres IP rekordu DNS) jest skonfigurowany w portalu rejestracji domeny (innych firm w usłudze DNS platformy Azure lub dostawcy DNS), Zastąp istniejący adres IP nowym. Nowy adres IP można znaleźć, postępując zgodnie z instrukcjami w następnej sekcji.

* Opcja 4: Jeśli Twoja aplikacja znajduje się za modułem równoważenia obciążenia, filtr adresów IP lub inny mechanizm adresu IP, wymagającego adres IP swojej aplikacji, Zamień istniejący adres IP nowym. Nowy adres IP można znaleźć, postępując zgodnie z instrukcjami w następnej sekcji.

## <a name="find-the-new-inbound-ip-address-in-the-azure-portal"></a>Znajdź nowy adres IP dla ruchu przychodzącego w witrynie Azure portal

Nowy adres IP dla ruchu przychodzącego podanej aplikacji znajduje się w portalu w **wirtualny adres IP** pola. Ten nowy adres IP i starego są połączone do swojej aplikacji teraz, a później stary certyfikat zostanie odłączona.

1.  Otwórz [portal Azure](https://portal.azure.com).

2.  W menu nawigacji po lewej stronie wybierz **App Services**.

3.  Wybierz aplikację usługi App Service z listy.

1.  Jeśli aplikacja jest aplikacja funkcji, zobacz [aplikacji funkcji dla ruchu przychodzącego adresu IP](../azure-functions/ip-addresses.md#function-app-inbound-ip-address).

4.  W obszarze **ustawienia** nagłówka, kliknij przycisk **właściwości** w nawigacji po lewej stronie, a następnie znajdź sekcja o nazwie **wirtualny adres IP**.

5. Skopiuj adres IP, a następnie ponownie skonfiguruj swoje rekord domeny lub adresu IP mechanizmu.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule opisano sposób przygotowania do zmiany adresu IP, które zostało zainicjowane przez platformę Azure. Aby uzyskać więcej informacji na temat adresów IP w usłudze Azure App Service, zobacz [przychodzące i wychodzące adresy IP w usłudze Azure App Service](overview-inbound-outbound-ips.md).
