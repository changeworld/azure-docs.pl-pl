---
title: Optymalizacja usługi Azure CDN dla typu dostarczania zawartości
description: Optymalizacja usługi Azure CDN dla typu dostarczania zawartości
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: magattus
ms.openlocfilehash: 954d19fb557540e4fdc6b17f313127e01eba97a7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60636330"
---
# <a name="optimize-azure-cdn-for-the-type-of-content-delivery"></a>Optymalizacja usługi Azure CDN dla typu dostarczania zawartości

Podczas można dostarczać zawartość do wielu odbiorców globalnych krytyczne jest zapewnienie zoptymalizowanego dostarczania zawartości. [Usługa Azure Content Delivery Network (CDN)](cdn-overview.md) może zoptymalizować proces dostarczania, na podstawie typu zawartości, masz. Zawartość może być witryny sieci Web, strumienia na żywo, wideo lub dużych plików do pobrania. Po utworzeniu punktu końcowego usługi CDN, należy określić scenariusza w **zoptymalizowane pod kątem** opcji. Wybór określa, które optymalizacji są stosowane do zawartości dostarczanych z punktu końcowego usługi CDN.

Opcje optymalizacji zaprojektowano w celu zachowania najlepszym rozwiązaniem z zakresu umożliwia zwiększenie wydajności dostarczania zawartości i odciążania lepsze pochodzenia. Wybór scenariusza wpłynąć na wydajność, modyfikując konfiguracje częściowe pamięci podręcznej, obiekt segmentu i zasady ponawiania źródła błędu. 

Ten artykuł zawiera omówienie różnych funkcji optymalizacji i kiedy należy ich używać. Aby uzyskać więcej informacji na temat funkcjami i ograniczeniami zobacz odpowiednie artykuły na poszczególnych typach poszczególnych optymalizacji.

> [!NOTE]
> Po utworzeniu punktu końcowego usługi CDN **zoptymalizowane pod kątem** opcje różnią się w zależności od typu punktu końcowego jest tworzony w profilu. Azure dostawców sieci CDN zastosować rozszerzenie na różne sposoby, w zależności od scenariusza. 

## <a name="provider-options"></a>Opcje dostawcy

**Usługa Azure CDN Standard from Microsoft** profile obsługuje następujące optymalizacje:

* [Ogólne dostarczanie w Internecie](#general-web-delivery). Tego rodzaju optymalizacji jest również używany do przesyłania strumieniowego multimediów i pobierania dużych plików.

> [!NOTE]
> Przyspieszanie witryn dynamicznych od firmy Microsoft jest oferowana za pośrednictwem [usługi Azure Service drzwiami frontowymi](https://docs.microsoft.com/azure/frontdoor/front-door-overview).

**Usługa Azure CDN Standard from Verizon** i **Azure CDN Premium from Verizon** profile obsługują następujące optymalizacje:

* [Ogólne dostarczanie w Internecie](#general-web-delivery). Tego rodzaju optymalizacji jest również używany do przesyłania strumieniowego multimediów i pobierania dużych plików.

* [Przyspieszanie witryn dynamicznych](#dynamic-site-acceleration) 


**Usługa Azure CDN Standard from Akamai** profile obsługują następujące optymalizacje:

* [Ogólne dostarczanie w Internecie](#general-web-delivery) 

* [Ogólne transmisje strumieniowe multimediów](#general-media-streaming)

* [Przesyłanie strumieniowe multimediów wideo na żądanie](#video-on-demand-media-streaming)

* [Pobieranie dużych plików](#large-file-download)

* [Przyspieszanie witryn dynamicznych](#dynamic-site-acceleration) 

Firma Microsoft zaleca, należy przetestować wydajność różnice między różnych dostawców, aby wybrać optymalne dostawcy do dostarczania.

## <a name="select-and-configure-optimization-types"></a>Wybierz i skonfiguruj typów optymalizacji

Po utworzeniu punktu końcowego usługi CDN wybierz typ optymalizacji, który najlepiej odpowiada scenariusza i typu zawartości, który ma punkt końcowy, aby dostarczać. **Ogólne dostarczanie w Internecie** jest ustawieniem domyślnym. Dla istniejących **Azure CDN Standard from Akamai** punktów końcowych, możesz zaktualizować opcji optymalizacji w dowolnym momencie. Ta zmiana nie przerywać dostarczanie z usługi Azure CDN. 

1. W **Azure CDN Standard from Akamai** profilu, wybierz punkt końcowy.

    ![Punkt końcowy zaznaczenia](./media/cdn-optimization-overview/01_Akamai.png)

2. W obszarze Ustawienia wybierz **optymalizacji**. Następnie wybierz typ z **zoptymalizowane pod kątem** listy rozwijanej.

    ![Wybór optymalizacji i typ](./media/cdn-optimization-overview/02_Select.png)

## <a name="optimization-for-specific-scenarios"></a>Optymalizacja dla konkretnych scenariuszy

Można zoptymalizować punktu końcowego usługi CDN dla jednego z tych scenariuszy. 

### <a name="general-web-delivery"></a>Ogólne dostarczanie w sieci Web

Ogólne dostarczanie w Internecie jest najbardziej typowych opcji optymalizacji. Jest ona przeznaczona do optymalizacji zawartości ogólne sieci web, takich jak strony sieci Web i aplikacji sieci web. Tego rodzaju optymalizacji może również służyć dla pliku, i pobiera wideo.

Typowe witryny sieci Web zawiera zawartości statycznej i dynamicznej. Zawartość statyczna obejmuje obrazów, bibliotek JavaScript i arkusze stylów, które mogą być buforowane i dostarczane do różnych użytkowników. Zawartość dynamiczna jest spersonalizowane dla poszczególnych użytkowników, takich jak elementy wiadomości, które są dostosowane do profilu użytkownika. Zawartość dynamiczna, takie jak koszyk sklepowy zawartość, nie są buforowane, ponieważ jest on unikatowy dla każdego użytkownika. Ogólne dostarczanie w Internecie można zoptymalizować całej witryny sieci Web. 

> [!NOTE]
> Jeśli używasz **Azure CDN Standard from Akamai** profilu, wybierz ten typ optymalizacji, jeśli Twoja średni rozmiar plików jest mniejsza niż 10 MB. W przeciwnym razie, jeśli Twoja średni rozmiar pliku jest większy niż 10 MB, wybierz **pobierania dużych plików** z **zoptymalizowane pod kątem** listy rozwijanej.

### <a name="general-media-streaming"></a>Ogólne transmisje strumieniowe multimediów

Jeśli chcesz używać punktu końcowego dla transmisja strumieniowa na żywo oraz wideo na żądanie, przesyłania strumieniowego, wybierz typ multimediów ogólne, optymalizacja przesyłania strumieniowego.

Strumieniowe przesyłanie multimediów jest zależne od czasu, ponieważ pakiety pojawiające się późno na kliencie, na przykład częste buforowanie zawartości filmu wideo, może spowodować pogorszenie środowiska oglądania. Multimediów strumieniowych optymalizacji zmniejsza opóźnienie w przypadku dostarczania zawartości multimediów i zapewnia bezproblemowe środowisko przesyłania strumieniowego dla użytkowników. 

Ten scenariusz jest typowy dla klientów usługi Azure media. Gdy używasz usługi Azure media services, uzyskujesz jeden przesyłania strumieniowego punkt końcowy, który może służyć do przesyłania strumieniowego na żywo i na żądanie. W tym scenariuszu klienci nie muszą przełączyć się do innego punktu końcowego, gdy zmieniają się z na żywo do przesyłania strumieniowego na żądanie. Optymalizacja przesyłania strumieniowego multimediów ogólnego obsługują tego rodzaju scenariusza.

Dla **Azure CDN Standard from Microsoft**, **Azure CDN Standard from Verizon**, i **Azure CDN Premium from Verizon**, użyj typu optymalizacji dostarczania ogólnych sieci web do dostarczanie ogólne przesyłania strumieniowego zawartości multimedialnej.

Aby uzyskać więcej informacji na temat Optymalizacja przesyłania strumieniowego multimediów, zobacz [multimediów strumieniowych optymalizacji](cdn-media-streaming-optimization.md).

### <a name="video-on-demand-media-streaming"></a>Przesyłanie strumieniowe multimediów wideo na żądanie

Optymalizacja przesyłania strumieniowego multimediów wideo na żądanie zwiększa przesyłania strumieniowego zawartości wideo na żądanie. Użyj tej opcji, jeśli używasz punktu końcowego przesyłania strumieniowego wideo na żądanie.

Dla **Azure CDN Standard from Microsoft**, **Azure CDN Standard from Verizon**, i **Azure CDN Premium from Verizon** profile, użyj optymalizacji dostarczania ogólnych sieci web Typ do dostarczania zawartości wideo na żądanie dotyczących usługi przesyłania strumieniowego multimediów.

Aby uzyskać więcej informacji na temat Optymalizacja przesyłania strumieniowego multimediów, zobacz [multimediów strumieniowych optymalizacji](cdn-media-streaming-optimization.md).

> [!NOTE]
> Jeśli punkt końcowy usługi CDN służy przede wszystkim zawartości wideo na żądanie, użyj tego typu optymalizacji. Główna różnica między tego typu optymalizacji i przesyłania strumieniowego typu optymalizacji multimediów ogólnego jest limit czasu ponawiania prób połączenia. Limit czasu jest znacznie krótszy, do pracy ze scenariuszami transmisji strumieniowej na żywo.
>

### <a name="large-file-download"></a>Pobieranie dużych plików

Aby uzyskać **Azure CDN Standard from Akamai** profile dużych plików są zoptymalizowane pliki do pobrania zawartości przekracza 10 MB. Jeśli Twoje średni rozmiar plików jest mniejsza niż 10 MB, należy użyć ogólne dostarczanie w Internecie. Swoje rozmiary plików średni są stale przekracza 10 MB, może być bardziej efektywne tworzenie oddzielnego punktu końcowego dla dużych plików. Aktualizacje oprogramowania układowego i oprogramowania są zwykle dużych plików. Aby dostarczyć pliki większe niż 1,8 GB, optymalizacja pobierania dużych plików jest wymagana.

Dla **Azure CDN Standard from Microsoft**, **Azure CDN Standard from Verizon**, i **Azure CDN Premium from Verizon** profile, użyj optymalizacji dostarczania ogólnych sieci web Typ do dostarczania zawartości pobierania dużych plików. Nie ma ograniczeń dotyczących rozmiaru pobierania pliku.

Aby uzyskać więcej informacji na temat Optymalizacja dużych plików, zobacz [Optymalizacja dużych plików](cdn-large-file-optimization.md).

### <a name="dynamic-site-acceleration"></a>Przyspieszanie witryn dynamicznych

 Przyspieszanie witryn dynamicznych (DSA) jest dostępna dla **Azure CDN Standard from Akamai**, **Azure CDN Standard from Verizon**, i **Azure CDN Premium from Verizon** profilów. Tego rodzaju optymalizacji pociąga za sobą dodatkowych opłat do użycia Aby uzyskać więcej informacji, zobacz [cennik usługi Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/).

> [!NOTE]
> Przyspieszanie witryn dynamicznych od firmy Microsoft jest oferowana za pośrednictwem [usługi Azure Service drzwiami frontowymi](https://docs.microsoft.com/azure/frontdoor/front-door-overview) co jest globalnym [emisji dowolnej](https://en.wikipedia.org/wiki/Anycast) service korzystanie z prywatnej globalnej sieci firmy Microsoft do świadczenia obciążeń aplikacji.

DSA obejmuje różne techniki, które korzyści opóźnienie i wydajność zawartości dynamicznej. Techniki obejmują optymalizacji routingu i sieci, optymalizacja protokołu TCP i inne. 

Tego rodzaju optymalizacji umożliwia przyspieszenie aplikacji sieci web, która zawiera wiele odpowiedzi, które nie podlega buforowaniu. Przykładami są wyniki wyszukiwania, transakcje wyewidencjonowania lub danych w czasie rzeczywistym. Można na potrzeby podstawowych sieć CDN systemu Azure na potrzeby buforowania danych statycznych. 

Aby uzyskać więcej informacji na temat przyspieszanie witryn dynamicznych, zobacz [przyspieszanie witryn dynamicznych](cdn-dynamic-site-acceleration.md).



