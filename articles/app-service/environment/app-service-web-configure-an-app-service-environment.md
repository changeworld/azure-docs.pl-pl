---
title: Konfigurowanie ase v1
description: Konfiguracja, zarządzanie i monitorowanie środowiska usługi app service w wersji 1. Ten doc jest dostępna tylko dla klientów, którzy używają starszej wersji ASE w wersji 1.
author: ccompy
ms.assetid: b5a1da49-4cab-460d-b5d2-edd086ec32f4
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: b37708e27887b20604a1fe921f14e51387793737
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687262"
---
# <a name="configuring-an-app-service-environment-v1"></a>Konfigurowanie środowiska usługi aplikacji w wersji 1

> [!NOTE]
> Ten artykuł dotyczy środowiska usługi app service w wersji 1.  Istnieje nowsza wersja środowiska usługi app service, która jest łatwiejsza w użyciu i działa na bardziej zaawansowanej infrastruktury. Aby dowiedzieć się więcej o nowej wersji, zacznij od [wprowadzenia do środowiska usługi app service](intro.md).
> 

## <a name="overview"></a>Omówienie
Na wysokim poziomie środowisko usługi Azure App Service składa się z kilku głównych składników:

* Zasoby obliczeniowe uruchomione w usłudze hosta usługi App Service Environment
* Magazyn
* Baza danych
* Sieć wirtualna platformy Azure (VNet) klasyczna(V1) lub Menedżer zasobów(V2) 
* Podsieć z uruchomiona w niej usługa hostowana w środowisku usługi app service

### <a name="compute-resources"></a>Zasoby obliczeniowe
Zasoby obliczeniowe są używane dla czterech pul zasobów.  Każde środowisko usługi aplikacji (ASE) ma zestaw front ends i trzy możliwe pule procesów roboczych. Nie musisz używać wszystkich trzech pul procesów roboczych — jeśli chcesz, możesz użyć tylko jednego lub dwóch.

Hosty w pulach zasobów (frontonów i pracowników) nie są bezpośrednio dostępne dla dzierżawców. Nie można używać protokołu RDP (Remote Desktop Protocol), aby połączyć się z nimi, zmienić ich inicjowanie obsługi administracyjnej lub działać jako administrator na nich.

Można ustawić ilość i rozmiar puli zasobów. W ASE masz cztery opcje rozmiaru, które są oznaczone od P1 do P4. Aby uzyskać szczegółowe informacje o tych rozmiarach i ich cenach, zobacz [Ceny usługi App Service](https://azure.microsoft.com/pricing/details/app-service/).
Zmiana ilości lub rozmiaru jest nazywana operacją skalowania.  W ciągu jednego czasu może być kontynuowana tylko jedna operacja skalowania.

**Front ends**: Front ends są punkty końcowe HTTP/HTTPS dla aplikacji, które są przechowywane w ASE. Nie uruchamiasz obciążeń na frontach.

* Ase rozpoczyna się od dwóch P2s, co jest wystarczające dla obciążeń deweloperskich/testowych i obciążeń produkcyjnych niskiego poziomu. Zdecydowanie zaleca się P3 dla umiarkowanych do dużych obciążeń produkcyjnych.
* W przypadku umiarkowanych do dużych obciążeń produkcyjnych zaleca się, aby mieć co najmniej cztery P3, aby upewnić się, że są wystarczające frontendy uruchomione po zaplanowaniu konserwacji. Zaplanowane czynności konserwacyjne będą sprowadzać jeden front end naraz. Zmniejsza to ogólną dostępną wydajność front-endu podczas prac konserwacyjnych.
* Przednie końce mogą potrwać do godziny. 
* Aby uzyskać dalsze dostrajanie skali, należy monitorować procent procesora CPU, procent pamięci i aktywne żądania metryki dla puli front-end. Jeśli procenty procesora CPU lub pamięci są powyżej 70 procent podczas uruchamiania P3s, dodaj więcej front ends. Jeśli wartość Aktywne żądania jest uśredniana do 15 000 do 20 000 żądań na frontonie, należy również dodać więcej front ends. Ogólnym celem jest utrzymanie procentów procesora CPU i pamięci poniżej 70%, a aktywne żądania średnio poniżej 15 000 żądań na frontonie podczas uruchamiania P3s.  

**Pracownicy:** Pracownicy są tam, gdzie twoje aplikacje są faktycznie uruchamiane. Podczas skalowania w górę planów usługi app service, który używa pracowników w puli skojarzonych procesów roboczych.

* Nie można od razu dodać pracowników. Ich dostarczenie może potrwać do godziny.
* Skalowanie rozmiaru zasobu obliczeniowego dla dowolnej puli zajmie < 1 godzinę na domenę aktualizacji. Istnieje 20 domen aktualizacji w ASE. Jeśli skalowany rozmiar obliczeń puli procesów procesowych z 10 wystąpień, może potrwać do 10 godzin.
* Jeśli zmienisz rozmiar zasobów obliczeniowych, które są używane w puli procesów roboczych, spowoduje to zimne uruchomienia aplikacji, które są uruchomione w tej puli procesów roboczych.

Najszybszym sposobem zmiany rozmiaru zasobów obliczeniowych puli procesów roboczych, w których nie są uruchomione żadne aplikacje, jest:

* Zmniejsz liczbę pracowników do 2.  Minimalna skala w dół w portalu wynosi 2. To zajmie kilka minut, aby zająćokować swoje wystąpienia. 
* Wybierz nowy rozmiar obliczeń i liczbę wystąpień. Od tego miejsca zajmie to do 2 godzin.

Jeśli aplikacje wymagają większego rozmiaru zasobów obliczeniowych, nie możesz skorzystać z poprzednich wskazówek. Zamiast zmieniać rozmiar puli procesów roboczych, która hostuje te aplikacje, można wypełnić inną pulę procesów roboczych pracownikami o żądanym rozmiarze i przenieść aplikacje do tej puli.

* Utwórz dodatkowe wystąpienia wymaganego rozmiaru obliczeń w innej puli procesów roboczych. Zajmie to do godziny.
* Ponownie przypisz plany usługi App Service, które obsługują aplikacje, które wymagają większego rozmiaru, do nowo skonfigurowanego puli pracowników. Jest to szybka operacja, która powinna potrwać mniej niż minutę.  
* Skaluj w dół pierwszej puli procesów roboczych, jeśli nie są już potrzebne te nieużywane wystąpienia. Ta operacja trwa kilka minut.

**Skalowanie automatyczne:** Jednym z narzędzi, które mogą pomóc w zarządzaniu zużyciem zasobów obliczeniowych, jest skalowanie automatyczne. Można użyć skalowania automatycznego dla pule front-end lub roboczego. Można wykonywać takie czynności, jak zwiększanie wystąpień dowolnego typu basenu rano i zmniejszanie go wieczorem. A może można dodać wystąpienia, gdy liczba pracowników, które są dostępne w puli pracowników spada poniżej pewnego progu.

Jeśli chcesz ustawić reguły skalowania automatycznego wokół metryk puli zasobów obliczeniowych, należy pamiętać o czasie wymaga, aby inicjowania obsługi administracyjnej. Aby uzyskać więcej informacji na temat skalowania automatycznego środowiska usługi app service, zobacz [Jak skonfigurować skalowanie automatyczne w środowisku usługi app service][ASEAutoscale].

### <a name="storage"></a>Magazyn
Każdy ASE jest skonfigurowany z 500 GB pamięci masowej. Ta przestrzeń jest używana we wszystkich aplikacjach w ase. Ta przestrzeń dyskowa jest częścią ase i obecnie nie można przełączyć do korzystania z miejsca do magazynowania. Jeśli wprowadzasz zmiany w routingu sieci wirtualnej lub zabezpieczeń, musisz nadal zezwalać na dostęp do usługi Azure Storage — lub nie może działać środowisko ASE.

### <a name="database"></a>baza danych
Baza danych przechowuje informacje, które definiuje środowisko, a także szczegóły dotyczące aplikacji, które są uruchomione w nim. To też jest częścią subskrypcji platformy Azure. To nie jest coś, co masz bezpośrednią zdolność do manipulowania. Jeśli wprowadzasz zmiany w routingu sieci wirtualnej lub zabezpieczeń, musisz nadal zezwalać na dostęp do platformy AZURE SQL — lub nie może działać środowisko ASE.

### <a name="network"></a>Network (Sieć)
Sieci wirtualnej, która jest używana z ASE może być taki, który został złożony podczas tworzenia ASE lub jeden, który został złożony z wyprzedzeniem. Podczas tworzenia podsieci podczas tworzenia środowiska ASE wymusza ono na ase w tej samej grupie zasobów co sieć wirtualna. Jeśli potrzebujesz grupy zasobów używanej przez program ASE, aby była inna niż grupa sieci wirtualnej, należy utworzyć ase przy użyciu szablonu Menedżera zasobów.

Istnieją pewne ograniczenia dotyczące sieci wirtualnej, która jest używana dla środowiska ASE:

* Sieć wirtualna musi być regionalną siecią wirtualną.
* Musi istnieć podsieć z 8 lub więcej adresów, gdzie jest wdrożony ase.
* Po podsieci jest używany do hosta ASE, zakres adresu podsieci nie można zmienić. Z tego powodu zaleca się, że podsieć zawiera co najmniej 64 adresy, aby pomieścić wszelkie przyszłe ase wzrostu.
* W podsieci nie może być nic innego, ale ase.

W przeciwieństwie do usługi hosta, która zawiera środowisko ASE, [sieć wirtualna][virtualnetwork] i podsieć znajdują się pod kontrolą użytkownika.  Sieć wirtualną można administrować za pośrednictwem interfejsu użytkownika sieci wirtualnej lub programu PowerShell.  Ase można wdrożyć w sieci wirtualnej Klasycznego lub Menedżera zasobów.  Środowisko portalu i interfejsu API różnią się nieco między sieciami wirtualnymi Klasycznego i Menedżera zasobów, ale środowisko ŚRODOWISKA ASE jest takie samo.

Sieć wirtualna, która jest używana do hosta ASE można użyć prywatnych adresów IP RFC1918 lub może używać publicznych adresów IP.  Jeśli chcesz użyć zakresu IP, który nie jest objęty RFC1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16), należy utworzyć sieć wirtualną i podsieć do użycia przez ASE przed utworzeniem ASE.

Ponieważ ta funkcja umieszcza usługę Azure App Service w sieci wirtualnej, oznacza to, że aplikacje hostowane w środowisku ASE mogą teraz uzyskać dostęp do zasobów udostępnianych bezpośrednio za pośrednictwem usługi ExpressRoute lub wirtualnych sieci prywatnych (VPN) między lokacjami. Aplikacje, które znajdują się w środowisku usługi app service nie wymagają dodatkowych funkcji sieciowych, aby uzyskać dostęp do zasobów dostępnych dla sieci wirtualnej, która hostuje środowisko usługi App Service. Oznacza to, że nie trzeba używać integracji sieci wirtualnej lub połączeń hybrydowych, aby uzyskać dostęp do zasobów w sieci wirtualnej lub połączone z nią. Nadal można jednak użyć obu tych funkcji, aby uzyskać dostęp do zasobów w sieciach, które nie są połączone z siecią wirtualną.

Na przykład można użyć integracji sieci wirtualnej do integracji z siecią wirtualną, która jest w ramach subskrypcji, ale nie jest połączona z siecią wirtualną, w której znajduje się środowisko ASE. Nadal można również używać połączeń hybrydowych, aby uzyskać dostęp do zasobów, które znajdują się w innych sieciach, tak jak zwykle.  

Jeśli sieć wirtualna jest skonfigurowana z siecią VPN usługi ExpressRoute, należy pamiętać o niektórych potrzebach routingu, które ma środowisko ASE. Istnieją konfiguracje trasy zdefiniowane przez użytkownika (UDR), które są niezgodne z ase. Aby uzyskać więcej informacji na temat uruchamiania środowiska ASE w sieci wirtualnej za pomocą usługi ExpressRoute, zobacz [Uruchamianie środowiska usługi app service w sieci wirtualnej za pomocą usługi ExpressRoute][ExpressRoute].

#### <a name="securing-inbound-traffic"></a>Zabezpieczanie ruchu przychodzącego
Istnieją dwie podstawowe metody kontrolowania ruchu przychodzącego do ase.  Sieciowych grup zabezpieczeń (NSG) można użyć do kontrolowania, jakie adresy IP mogą uzyskiwać dostęp do środowiska ASE, jak opisano w tym miejscu [Jak kontrolować ruch przychodzący w środowisku usługi app service,](app-service-app-service-environment-control-inbound-traffic.md) a także skonfigurować środowisko ASE za pomocą wewnętrznego modułu równoważenia obciążenia (ILB).  Te funkcje mogą być również używane razem, jeśli chcesz ograniczyć dostęp przy użyciu sieciowychg do ase ILB.

Podczas tworzenia ase, utworzy adres VIP w sieci wirtualnej.  Istnieją dwa typy VIP, zewnętrzne i wewnętrzne.  Po utworzeniu ASE z zewnętrznym VIP następnie aplikacje w ASE będą dostępne za pośrednictwem internet routable adres IP. Po wybraniu opcji Wewnętrzny program ASE zostanie skonfigurowany z równoważeniem obciążenia sieciowego i nie będzie bezpośrednio dostępny w Internecie.  Ase ILB nadal wymaga zewnętrznego adresu VIP, ale jest używany tylko do zarządzania platformy Azure i dostępu do konserwacji.  

Podczas tworzenia ase równoważenia obciążenia sieciowego należy podać poddomenę używaną przez ase ILB i będzie musiał zarządzać własnym DNS dla poddomeny, którą określisz.  Ponieważ można ustawić nazwę poddomeny, należy również zarządzać certyfikatem używanym do dostępu HTTPS.  Po utworzeniu ASE zostanie wyświetlony monit o dostarczenie certyfikatu.  Aby dowiedzieć się więcej na temat tworzenia i używania środowiska ASE równoważenia obciążenia wewnętrznego, można przeczytać [Przy użyciu wewnętrznego modułu równoważenia obciążenia ze środowiskiem usługi app service][ILBASE]. 

## <a name="portal"></a>Portal
Środowisko usługi App Service można zarządzać i monitorować je przy użyciu interfejsu użytkownika w witrynie Azure portal. Jeśli masz ASE, prawdopodobnie zobaczysz symbol usługi App Service na pasku bocznym. Ten symbol jest używany do reprezentowania środowisk usługi app service w witrynie Azure portal:

![Symbol środowiska usługi aplikacji][1]

Aby otworzyć interfejs użytkownika, który zawiera listę wszystkich środowisk usługi app service, można użyć ikony lub wybierz szewron (">" symbol) w dolnej części paska bocznego, aby wybrać środowiska usługi app service. Wybierając jeden z ases wymienionych, należy otworzyć interfejs użytkownika, który jest używany do monitorowania i zarządzania nim.

![Interfejs użytkownika do monitorowania środowiska usługi app service i zarządzania nim][2]

Ten pierwszy blok pokazuje niektóre właściwości ase, wraz z wykresu metryki na pulę zasobów. Niektóre właściwości, które są wyświetlane w **bloku Essentials** są również hiperłącza, które otworzą bloku, który jest skojarzony z nim. Na przykład można wybrać nazwę **sieci wirtualnej,** aby otworzyć interfejs użytkownika skojarzony z siecią wirtualną, w której działa środowisko ASE. **Plany usługi app service** i **aplikacje** każdy otworzyć bloków, które listy tych elementów, które znajdują się w ASE.  

### <a name="monitoring"></a>Monitorowanie
Wykresy umożliwiają wyświetlanie różnych metryk wydajności w każdej puli zasobów. W przypadku puli front-end można monitorować średni procesor i pamięć. W przypadku pul procesów roboczych można monitorować ilość, która jest używana i ilość, która jest dostępna.

Wiele planów usługi app service można korzystać z pracowników w puli procesów procesowych. Obciążenie nie jest dystrybuowane w taki sam sposób, jak w przypadku serwerów frontonalnej, więc użycie procesora CPU i pamięci nie zapewniają wiele w sposób przydatnych informacji. Ważniejsze jest śledzenie liczby pracowników, których używałeś i którzy są dostępni — zwłaszcza jeśli zarządzasz tym systemem dla innych.  

Można również użyć wszystkich metryk, które mogą być śledzone na wykresach, aby skonfigurować alerty. Konfigurowanie alertów w tym miejscu działa tak samo jak w innych miejscach w usłudze App Service. Alert można ustawić z części Interfejsu użytkownika **alertów** lub z wiercenia w dowolnym interfejsie użytkownika metryk i wybierając **pozycję Dodaj alert**.

![Interfejs użytkownika metryk][3]

Metryki, które zostały właśnie omówione są metryki środowiska usługi app service. Istnieją również metryki, które są dostępne na poziomie planu usługi aplikacji. To jest, gdy monitorowanie procesora i pamięci ma wiele sensu.

W ase wszystkie plany usługi app service są dedykowane plany usługi app service. Oznacza to, że tylko aplikacje, które są uruchomione na hostach przydzielonych do tego planu usługi app service są aplikacje w tym planie usługi app service. Aby wyświetlić szczegółowe informacje na temat planu usługi app service, należy wyświetlić plan usługi app service z dowolnej listy w interfejsie użytkownika ASE lub z **witryny Przeglądaj usługi aplikacji** (która zawiera listę wszystkich z nich).   

### <a name="settings"></a>Ustawienia
W bloku ASE znajduje się sekcja **Ustawienia,** która zawiera kilka ważnych możliwości:

**Właściwości ustawień:** > **Properties**Blok **Ustawienia** otwiera się automatycznie po wyświetleniu bloku ASE. U góry znajduje się **właściwości**. Istnieje wiele elementów tutaj, które są zbędne do tego, co widzisz w **Essentials**, ale to, co jest bardzo przydatne jest **wirtualny adres IP,** a także **wychodzące adresy IP**.

![Ustawienia bloku i właściwości][4]

**Ustawienia** > **adresy IP:** Podczas tworzenia aplikacji SSL (IP Secure Sockets Layer) w środowisku ASE potrzebny jest adres SSL IP. Aby uzyskać jeden, twój ASE potrzebuje adresów SSL IP, które jest właścicielem, które mogą być przydzielane. Po utworzeniu ASE ma jeden adres SSL IP w tym celu, ale można dodać więcej. Istnieje opłata za dodatkowe adresy SSL IP, jak pokazano w [cenniku usługi App Service][AppServicePricing] (w sekcji dotyczącej połączeń SSL). Dodatkową ceną jest cena IP SSL.

**Ustawienia** >  / **pul procesowych puli frontowego:** Każdy z tych bloków puli zasobów oferuje możliwość wyświetlanie informacji tylko na tej puli zasobów, oprócz zapewnienia formantów, aby w pełni skalować tę pulę zasobów.**Front End Pool**  

Blok podstawowy dla każdej puli zasobów zawiera wykres z metrykami dla tej puli zasobów. Podobnie jak w razie potrzeby w odniesieniu do wykresów z bloku ASE, można przejść do wykresu i skonfigurować alerty zgodnie z potrzebami. Ustawienie alertu z bloku ASE dla określonej puli zasobów robi to samo, co robi to z puli zasobów. W bloku **Ustawienia** puli procesów procesowych masz dostęp do wszystkich planów usługi aplikacji lub usługi app service, które są uruchomione w tej puli procesów roboczych.

![Interfejs użytkownika ustawień puli pracowników][5]

### <a name="portal-scale-capabilities"></a>Możliwości skalowania portalu
Istnieją trzy operacje skali:

* Zmiana liczby adresów IP w ase, które są dostępne dla użycia protokołu IP SSL.
* Zmiana rozmiaru zasobu obliczeniowego używanego w puli zasobów.
* Zmiana liczby zasobów obliczeniowych, które są używane w puli zasobów, ręcznie lub za pomocą skalowania automatycznego.

W portalu istnieją trzy sposoby kontrolowania liczby serwerów, które mają w pulach zasobów:

* Operacja skalowania od głównego ostrza ASE u góry. Można wprowadzić wiele zmian konfiguracji skalowania do pul front-end i procesów roboczych. Wszystkie są stosowane jako pojedyncza operacja.
* Ręczna operacja skalowania z bloku **skalowania** indywidualnej puli zasobów, która znajduje się w obszarze **Ustawienia**.
* Skalowanie automatyczne, które można skonfigurować z puli poszczególnych zasobów **skalowania** bloku.

Aby użyć operacji skalowania na bloku ASE, przeciągnij suwak do żądanej ilości i zapisz. Ten interfejs użytkownika obsługuje również zmianę rozmiaru.  

![Skalowanie interfejsu użytkownika][6]

Aby użyć możliwości ręcznego lub skalowania automatycznego w określonej puli zasobów, przejdź odpowiednio do **sekcji Ustawienia** > **pul procesowych puli** **frontowego.** /  Następnie otwórz pulę, którą chcesz zmienić. Przejdź do **pozycji Skalowanie w** > **poziomie** ustawień lub **Skalowanie w** > **górę**. **Blok Skaluj w poziomie** umożliwia kontrolowanie ilości wystąpienia. **Skalowanie w górę** umożliwia kontrolowanie rozmiaru zasobu.  

![Skalowanie ustawień interfejsu użytkownika][7]

## <a name="fault-tolerance-considerations"></a>Względy odporności na uszkodzenia
Można skonfigurować środowisko usługi app service, aby używać do 55 całkowitych zasobów obliczeniowych. Z tych 55 zasobów obliczeniowych tylko 50 może służyć do obsługi obciążeń. Powód jest dwojaki. Istnieje co najmniej 2 zasoby obliczeniowe front-end.  To pozostawia do 53 do obsługi alokacji puli pracowników. Aby zapewnić odporność na uszkodzenia, należy mieć dodatkowy zasób obliczeniowy, który jest przydzielany zgodnie z następującymi regułami:

* Każda pula procesów procesowych wymaga co najmniej 1 dodatkowego zasobu obliczeniowego, który nie jest dostępny do przypisania obciążenia.
* Gdy ilość zasobów obliczeniowych w puli procesów procesowych przekracza określoną wartość, do odporności na uszkodzenia wymagany jest inny zasób obliczeniowy. Nie dotyczy to puli front-end.

W dowolnej puli pojedynczego procesu roboczego wymagania dotyczące tolerancji na uszkodzenia są określone dla danej wartości zasobów X przypisanych do puli procesów roboczych:

* Jeśli X jest między 2 i 20, ilość zasobów obliczeniowych użytecznych, które można użyć dla obciążeń jest X-1.
* Jeśli X jest między 21 i 40, ilość zasobów obliczeniowych użytecznych, które można użyć dla obciążeń jest X-2.
* Jeśli X jest między 41 i 53, ilość zasobów obliczeniowych użytecznych, które można użyć dla obciążeń jest X-3.

Minimalny rozmiar ma 2 serwery front-end i 2 pracowników.  Z powyższych stwierdzeń następnie, oto kilka przykładów do wyjaśnienia:  

* Jeśli masz 30 pracowników w jednej puli, a następnie 28 z nich może służyć do obsługi obciążeń.
* Jeśli masz 2 pracowników w jednej puli, a następnie 1 może służyć do obsługi obciążeń.
* Jeśli masz 20 pracowników w jednej puli, a następnie 19 może służyć do obsługi obciążeń.  
* Jeśli masz 21 pracowników w jednej puli, nadal tylko 19 może służyć do obsługi obciążeń.  

Aspekt odporności na uszkodzenia jest ważny, ale należy o tym pamiętać podczas skalowania powyżej określonych progów. Jeśli chcesz dodać więcej pojemności będzie z 20 wystąpień, a następnie przejdź do 22 lub więcej, ponieważ 21 nie dodaje więcej pojemności. To samo dotyczy powyżej 40, gdzie następna liczba, która dodaje pojemność, wynosi 42.  

## <a name="deleting-an-app-service-environment"></a>Usuwanie środowiska usługi aplikacji
Jeśli chcesz usunąć środowisko usługi app service, a następnie po prostu użyj **akcji Usuń** u góry bloku środowiska usługi app service. Po wykonaniu tej tej zgody zostanie wyświetlony monit o wprowadzenie nazwy środowiska usługi app service, aby potwierdzić, że naprawdę chcesz to zrobić. Należy zauważyć, że po usunięciu środowiska usługi aplikacji, należy usunąć całą zawartość w nim, jak również.  

![Usuwanie interfejsu użytkownika środowiska usługi aplikacji][9]  

## <a name="getting-started"></a>Wprowadzenie
Aby rozpocząć korzystanie ze środowisk usługi app service, zobacz [Jak utworzyć środowisko usługi app service](app-service-web-how-to-create-an-app-service-environment.md).

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/ase-icon.png
[2]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-aseblade.png
[3]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolchart.png
[4]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-properties.png
[5]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolblade.png
[6]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-scalecommand.png
[7]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolscale.png
[8]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-pricingtiers.png
[9]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-deletease.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[Appserviceplans]: ../overview-hosting-plans.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoScale]: app-service-web-scale-a-web-app-in-an-app-service-environment.md
[ControlInbound]: app-service-app-service-environment-control-inbound-traffic.md
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md
