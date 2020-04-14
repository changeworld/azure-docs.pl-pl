---
title: Optymalizacja usługi Azure CDN pod kątem typu dostarczania zawartości
description: Optymalizacja usługi Azure CDN pod kątem typu dostarczania zawartości
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: allensu
ms.openlocfilehash: 473636dc95d96ea348a42ec0f1090029bf3a7728
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260458"
---
# <a name="optimize-azure-cdn-for-the-type-of-content-delivery"></a>Optymalizacja usługi Azure CDN pod kątem typu dostarczania zawartości

Gdy dostarczasz treści do szerokiej grupy odbiorców na całym świecie, kluczowe znaczenie ma zapewnienie zoptymalizowanego dostarczania treści. [Usługa Azure Content Delivery Network (CDN)](cdn-overview.md) może zoptymalizować środowisko dostarczania na podstawie typu posiadanej zawartości. Zawartość może być witryną sieci Web, transmisją na żywo, filmem lub dużym plikiem do pobrania. Podczas tworzenia punktu końcowego sieci CDN, należy określić scenariusz w **opcji Zoptymalizowane pod kątem.** Wybór określa, która optymalizacja jest stosowana do zawartości dostarczanej z punktu końcowego sieci CDN.

Opcje optymalizacji są przeznaczone do używania zachowań najlepszych praktyk w celu zwiększenia wydajności dostarczania zawartości i lepszego odciążania pochodzenia. Opcje scenariusza wpływają na wydajność, modyfikując konfiguracje częściowego buforowania, fragmentowania obiektów i zasad ponawiania prób niepowodzenia pochodzenia. 

Ten artykuł zawiera omówienie różnych funkcji optymalizacji i kiedy należy ich używać. Aby uzyskać więcej informacji na temat funkcji i ograniczeń, zobacz odpowiednie artykuły dotyczące każdego typu optymalizacji.

> [!NOTE]
> Podczas tworzenia punktu końcowego sieci CDN opcje **zoptymalizowane pod kątem** mogą się różnić w zależności od typu profilu, w którym tworzony jest punkt końcowy. Dostawcy usługi Azure CDN stosują ulepszenia na różne sposoby, w zależności od scenariusza. 

## <a name="provider-options"></a>Opcje dostawcy

**Usługa Azure CDN Standard z** profili firmy Microsoft obsługuje następujące optymalizacje:

* [Ogólne dostarczanie w internecie](#general-web-delivery). Ta optymalizacja jest również używana do przesyłania strumieniowego multimediów i pobierania dużych plików.

> [!NOTE]
> Dynamiczne przyspieszanie witryny firmy Microsoft jest oferowane za pośrednictwem [usługi Azure Front Door Service.](https://docs.microsoft.com/azure/frontdoor/front-door-overview)

**Usługi Azure CDN Standard firmy Verizon** i **Azure CDN Premium z** profili Verizon obsługują następujące optymalizacje:

* [Ogólne dostarczanie w internecie](#general-web-delivery). Ta optymalizacja jest również używana do przesyłania strumieniowego multimediów i pobierania dużych plików.

* [Dynamiczne przyspieszanie witryny](#dynamic-site-acceleration) 


**Usługa Azure CDN Standard z profili Akamai** obsługuje następujące optymalizacje:

* [Ogólne dostarczanie w sieci Web](#general-web-delivery) 

* [Ogólne przesyłanie strumieniowe multimediów](#general-media-streaming)

* [Przesyłanie strumieniowe multimediów na żądanie](#video-on-demand-media-streaming)

* [Pobieranie dużych plików](#large-file-download)

* [Dynamiczne przyspieszanie witryny](#dynamic-site-acceleration) 

Firma Microsoft zaleca testowanie różnic w wydajności między różnymi dostawcami, aby wybrać optymalnego dostawcę dla twojego dostawcy.

## <a name="select-and-configure-optimization-types"></a>Wybieranie i konfigurowanie typów optymalizacji

Podczas tworzenia punktu końcowego sieci CDN, wybierz typ optymalizacji, który najlepiej pasuje do scenariusza i typu zawartości, które mają dostarczyć punkt końcowy. **Ogólne dostarczanie w sieci Web** jest domyślnym wyborem. W przypadku istniejącego standardu usługi Azure CDN tylko z punktów końcowych **Akamai** można zaktualizować opcję optymalizacji w dowolnym momencie. Ta zmiana nie przerywa dostarczania z usługi Azure CDN. 

1. W **standardzie usługi Azure CDN z profilu Akamai** wybierz punkt końcowy.

    ![Wybór punktu końcowego](./media/cdn-optimization-overview/01_Akamai.png)

2. W obszarze USTAWIENIA wybierz pozycję **Optymalizacja**. Następnie wybierz typ z listy rozwijanej **Zoptymalizowane pod kątem.**

    ![Optymalizacja i wybór typu](./media/cdn-optimization-overview/02_Select.png)

## <a name="optimization-for-specific-scenarios"></a>Optymalizacja dla określonych scenariuszy

Można zoptymalizować punkt końcowy sieci CDN dla jednego z tych scenariuszy. 

### <a name="general-web-delivery"></a>Ogólne dostarczanie w sieci Web

Ogólne dostarczanie w sieci web jest najczęstszą opcją optymalizacji. Został on zaprojektowany do ogólnej optymalizacji zawartości sieci Web, takich jak strony internetowe i aplikacje sieci web. Ta optymalizacja może być również używana do pobierania plików i filmów.

Typowa strona internetowa zawiera zawartość statyczną i dynamiczną. Zawartość statyczna zawiera obrazy, biblioteki JavaScript i arkusze stylów, które mogą być buforowane i dostarczane do różnych użytkowników. Zawartość dynamiczna jest personalizowana dla poszczególnych użytkowników, na przykład dla wiadomości dostosowanych do profilu użytkownika. Zawartość dynamiczna, taka jak zawartość koszyka, nie jest buforowana, ponieważ jest unikatowa dla każdego użytkownika. Ogólne dostarczanie stron internetowych może zoptymalizować całą witrynę. 

> [!NOTE]
> Jeśli używasz **standardu usługi Azure CDN z profilu Akamai,** wybierz ten typ optymalizacji, jeśli średni rozmiar pliku jest mniejszy niż 10 MB. W przeciwnym razie, jeśli średni rozmiar pliku jest większy niż 10 MB, wybierz **opcję Duży plik pobierania** z listy **rozwijanej Zoptymalizowane pod kątem.**

### <a name="general-media-streaming"></a>Ogólne przesyłanie strumieniowe multimediów

Jeśli chcesz użyć punktu końcowego do przesyłania strumieniowego na żywo i przesyłania strumieniowego wideo na żądanie, wybierz ogólny typ optymalizacji przesyłania strumieniowego multimediów.

Przesyłanie strumieniowe multimediów jest zależne od czasu, ponieważ pakiety, które przychodzą późno na kliencie, takie jak częste buforowanie zawartości wideo, może spowodować pogorszenie środowiska wyświetlania. Optymalizacja przesyłania strumieniowego multimediów zmniejsza opóźnienia dostarczania zawartości multimedialnej i zapewnia użytkownikom płynne przesyłanie strumieniowe. 

Ten scenariusz jest typowy dla klientów usługi multimediów platformy Azure. Korzystając z usług multimediów platformy Azure, otrzymasz jeden punkt końcowy przesyłania strumieniowego, który może służyć do przesyłania strumieniowego na żywo i na żądanie. W tym scenariuszu klienci nie muszą przełączać się do innego punktu końcowego, gdy zmieniają się z przesyłania strumieniowego na żywo na żądanie. Optymalizacja ogólnego przesyłania strumieniowego multimediów obsługuje ten typ scenariusza.

W przypadku **usługi Azure CDN Standard firmy Microsoft**, azure **CDN Standard firmy Verizon**i usługi Azure **CDN Premium firmy Verizon**użyj ogólnego typu optymalizacji dostarczania w sieci Web, aby dostarczać ogólną zawartość multimediów strumieniowych.

Aby uzyskać więcej informacji na temat optymalizacji przesyłania strumieniowego multimediów, zobacz [Optymalizacja przesyłania strumieniowego multimediów](cdn-media-streaming-optimization.md).

### <a name="video-on-demand-media-streaming"></a>Przesyłanie strumieniowe multimediów na żądanie

Optymalizacja przesyłania strumieniowego multimediów wideo na żądanie poprawia jakość przesyłania strumieniowego wideo na żądanie. Jeśli używasz punktu końcowego do przesyłania strumieniowego wideo na żądanie, użyj tej opcji.

W przypadku **usługi Azure CDN Standard firmy Microsoft**, azure **CDN Standard firmy Verizon**i azure **CDN Premium z** profili Verizon użyj ogólnego typu optymalizacji dostarczania sieci Web, aby dostarczać zawartość multimediów strumieniowych wideo na żądanie.

Aby uzyskać więcej informacji na temat optymalizacji przesyłania strumieniowego multimediów, zobacz [Optymalizacja przesyłania strumieniowego multimediów](cdn-media-streaming-optimization.md).

> [!NOTE]
> Jeśli punkt końcowy sieci CDN służy przede wszystkim zawartości wideo na żądanie, należy użyć tego typu optymalizacji. Główną różnicą między tym typem optymalizacji a typem optymalizacji ogólnego przesyłania strumieniowego multimediów jest limit czasu ponawiania próby połączenia. Limit czasu jest znacznie krótszy do pracy ze scenariuszami przesyłania strumieniowego na żywo.
>

### <a name="large-file-download"></a>Pobieranie dużych plików

W przypadku **usługi Azure CDN Standard z profili Akamai** duże pliki do pobrania są zoptymalizowane pod kątem zawartości większej niż 10 MB. Jeśli średni rozmiar pliku jest mniejszy niż 10 MB, użyj ogólnego dostarczania w sieci Web. Jeśli średnie rozmiary plików są stale większe niż 10 MB, może być bardziej efektywne, aby utworzyć oddzielny punkt końcowy dla dużych plików. Na przykład aktualizacje oprogramowania układowego lub oprogramowania są zazwyczaj dużymi plikami. Aby dostarczyć pliki większe niż 1,8 GB, wymagana jest optymalizacja pobierania dużych plików.

W przypadku **usługi Azure CDN Standard firmy Microsoft**, azure **CDN Standard firmy Verizon**i azure **CDN Premium z** profili Verizon użyj ogólnego typu optymalizacji dostarczania sieci Web, aby dostarczać dużą zawartość do pobierania plików. Nie ma ograniczeń co do rozmiaru pobierania plików.

Aby uzyskać więcej informacji na temat optymalizacji dużych plików, zobacz [Optymalizacja dużych plików](cdn-large-file-optimization.md).

### <a name="dynamic-site-acceleration"></a>Przyspieszanie witryn dynamicznych

 Dynamiczne przyspieszanie witryny (DSA) jest dostępne dla **usługi Azure CDN Standard firmy Akamai**, **Azure CDN Standard firmy Verizon**i Azure CDN Premium z profili **Verizon.** Optymalizacja ta obejmuje dodatkową opłatę za korzystanie; Aby uzyskać więcej informacji, zobacz [Ceny sieci dostarczania zawartości](https://azure.microsoft.com/pricing/details/cdn/).

> [!NOTE]
> Dynamiczne przyspieszanie witryny firmy Microsoft jest oferowane za pośrednictwem [usługi Azure Front Door Service,](https://docs.microsoft.com/azure/frontdoor/front-door-overview) która jest globalną usługą dowolnej [emisji](https://en.wikipedia.org/wiki/Anycast) wykorzystującą prywatną globalną sieć firmy Microsoft do dostarczania obciążeń aplikacji.

DSA zawiera różne techniki, które korzystają z opóźnienia i wydajności zawartości dynamicznej. Techniki obejmują optymalizację trasy i sieci, optymalizację TCP i inne. 

Można użyć tej optymalizacji, aby przyspieszyć aplikację sieci web, która zawiera wiele odpowiedzi, które nie są buforowane. Przykładami są wyniki wyszukiwania, transakcje realizacji transakcji lub dane w czasie rzeczywistym. Można nadal używać podstawowych funkcji buforowania usługi Azure CDN dla danych statycznych. 

Aby uzyskać więcej informacji na temat dynamicznego przyspieszania lokacji, zobacz [Dynamiczne przyspieszanie lokacji](cdn-dynamic-site-acceleration.md).



