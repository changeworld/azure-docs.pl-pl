---
title: Przygotowanie do zmiany adresu IP ruchu wychodzącego — usłudze Azure App Service
description: Jeśli adres IP ruchu wychodzącego, będzie można zmienić, Dowiedz się, co należy zrobić, dzięki czemu aplikacja będzie nadal działać po zmianie.
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
ms.openlocfilehash: ac62217af096653d61a79ff29ae352c8e950f8af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61269785"
---
# <a name="how-to-prepare-for-an-outbound-ip-address-change"></a>Jak przygotować się na wychodzące zmiana adresu IP

Jeśli otrzymasz powiadomienie, które zmieniają się wychodzące adresy IP w Twojej aplikacji w usłudze Azure App Service, postępuj zgodnie z instrukcjami w tym artykule.

## <a name="determine-if-you-have-to-do-anything"></a>Określić, czy istnieją podejmować żadnych działań

* Opcja 1: Jeśli aplikację usługi App Service nie korzysta z filtrowania adresów IP, lista dołączania jawne lub specjalnej obsługi ruchu wychodzącego, takiego jak Zapora lub routingu, jest wymagana żadna akcja.

* Opcja 2: Jeśli aplikacja ma specjalna obsługa wychodzące adresy IP (Zobacz przykłady poniżej), Dodaj nowe adresy IP ruchu wychodzącego, wszędzie tam, gdzie są wyświetlane już istniejące. Nie zastępuje istniejące adresy IP. Możesz znaleźć nowe adresy IP ruchu wychodzącego zgodnie z instrukcjami w następnej sekcji.

  Na przykład wychodzący adres IP może być jawnie uwzględnione w zaporze spoza aplikacji lub usługi zewnętrznej płatności może mieć listy dozwolonych, który zawiera adres IP ruchu wychodzącego dla aplikacji. Jeśli Twój adres ruchu wychodzącego jest skonfigurowany na liście w dowolnym miejscu poza aplikację, która wymaga wprowadzenia zmian.

## <a name="find-the-outbound-ip-addresses-in-the-azure-portal"></a>Znajdź wychodzące adresy IP w witrynie Azure portal

Nowe wychodzące adresy IP są wyświetlane w portalu, zaczęły obowiązywać. Po uruchomieniu platformy Azure przy użyciu nowych odcisków starych już nie będą używane. Używany jest tylko jeden zestaw naraz, więc wpisy na listach dołączania musi zawierać zarówno stare i nowe adresy IP, aby uniknąć przestoju w przypadku przełącznika. 

1.  Otwórz [portal Azure](https://portal.azure.com).

2.  W menu nawigacji po lewej stronie wybierz **App Services**.

3.  Wybierz aplikację usługi App Service z listy.

1.  Jeśli aplikacja jest aplikacja funkcji, zobacz [funkcji aplikacji wychodzące adresy IP](../azure-functions/ip-addresses.md#find-outbound-ip-addresses).

4.  W obszarze **ustawienia** nagłówka, kliknij przycisk **właściwości** w nawigacji po lewej stronie, a następnie znajdź sekcja o nazwie **adresy IP ruchu wychodzącego**.

5. Skopiuj adresy IP i dodać je do swojej specjalnej obsługi ruchu wychodzącego, takie jak filtr lub listy dozwolonych. Nie należy usuwać istniejące adresy IP na liście.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule opisano sposób przygotowania do zmiany adresu IP, które zostało zainicjowane przez platformę Azure. Aby uzyskać więcej informacji na temat adresów IP w usłudze Azure App Service, zobacz [przychodzące i wychodzące adresy IP w usłudze Azure App Service](overview-inbound-outbound-ips.md).
