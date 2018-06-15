---
title: Jak skonfigurować usługę aplikacji środowiska v1
description: Konfigurowanie, zarządzanie i monitorowanie v1 środowiska usługi aplikacji
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: b5a1da49-4cab-460d-b5d2-edd086ec32f4
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.openlocfilehash: 34fb3f15c03a3d3ef5f0a27081539bf0a6d19c5f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23837218"
---
# <a name="configuring-an-app-service-environment-v1"></a>Konfigurowanie aplikacji usługi v1 środowiska

> [!NOTE]
> Ten artykuł dotyczy v1 środowiska usługi aplikacji.  Istnieje nowsza wersja środowiska usługi aplikacji jest łatwiejsza w użyciu, który jest uruchamiany na bardziej zaawansowanych infrastruktury. Aby dowiedzieć się więcej o nowy początek wersji z [wprowadzenie do środowiska usługi aplikacji](intro.md).
> 

## <a name="overview"></a>Omówienie
Na wysokim poziomie środowiska usługi aplikacji Azure składa się z kilku głównych składników:

* Usługa hostowana zasoby obliczeniowe, które są uruchomione w środowisku usługi aplikacji
* Magazyn
* Bazy danych
* Classic(V1) lub zasobów Azure Manager(V2) sieć wirtualną (VNet) 
* Podsieci w usłudze hostowanej środowiska usługi aplikacji uruchomionych w jej

### <a name="compute-resources"></a>Zasoby obliczeniowe
Zasoby obliczeniowe są używane z pul zasobów cztery.  Każdego środowiska usługi aplikacji (ASE) ma zestaw punktów końcowych front i trzy pule procesów roboczych możliwe. Nie trzeba używać wszystkie pule procesów roboczych trzy — Jeśli chcesz, można użyć jednego lub dwóch.

Hosty w pulach zasobów (interfejsy i pracowników) nie są bezpośrednio dostępne dla dzierżawcy. Nie można za pomocą protokołu RDP (Remote Desktop) można łączyć się z nimi, zmienić ich inicjowania obsługi lub działać jako administrator na nich.

Można ustawić liczby puli zasobów i rozmiaru. W elemencie ASE masz cztery opcje rozmiaru, które są oznaczone jako P1 za pośrednictwem P4. Aby uzyskać więcej informacji o tych rozmiary i ich ceny, zobacz [cennik usługi aplikacji](https://azure.microsoft.com/pricing/details/app-service/).
Zmiana rozmiaru lub ilość nosi nazwę operacji skalowania.  Operacja skalowania tylko jedna może być w toku w czasie.

**Zakończenia przód**: front końce są punktów końcowych HTTP/HTTPS do pracy z aplikacjami, które są wstrzymane w Twojej ASE. Nie uruchamiaj obciążeń w front kończy się.

* ASE rozpoczyna się od dwóch P2s, który jest wystarczająca dla obciążeń i testowania i obciążeń produkcyjnych niskiego poziomu. Zdecydowanie zaleca się P3s dla umiarkowany w przypadku obciążeń produkcyjnych duże.
* Umiarkowany w przypadku obciążeń produkcyjnych duże zaleca się, że masz co najmniej cztery P3s, aby upewnić się, są wystarczające front kończy się uruchomione podczas zaplanowanej konserwacji. Działania zaplanowanej konserwacji zostanie wyświetlone w dół jeden fronton naraz. Zmniejsza to całkowita dostępna pojemność frontonu podczas działania obsługi.
* Interfejsy może potrwać do godziny do udostępniania. 
* Dalsze szczegóły skali, należy monitorować procent użycia procesora CPU, pamięci i metryki aktywne żądania dla puli frontonu. Jeśli wartości procentowe pamięci lub Procesora przekracza 70% podczas uruchamiania P3s, dodać więcej front kończy się. Jeśli wartość aktywnych żądań oblicza średnią do 15 000 do 20 000 żądań na frontonu, należy również dodać więcej front kończy się. Ogólnym celem jest zachowanie procesora CPU i pamięci wartości procentowe poniżej 70% i aktywnych żądań uśrednianie się poniżej 15 000 żądań na wierzch kończyć podczas pracy P3s.  

**Pracownicy**: pracownicy są, której uruchamiane aplikacje. Skalowanie w górę planów usługi aplikacji, która korzysta z procesów roboczych w puli skojarzony proces roboczy.

* Nie można dodać natychmiast pracowników. One może potrwać do godziny do udostępniania.
* Skalowanie rozmiar zasobów obliczeniowych, dla każdej puli potrwa < 1 godzina na domenę aktualizacji. W elemencie ASE jest 20 domen aktualizacji. W przypadku zmiany rozmiaru obliczeń puli procesów roboczych z wystąpieniami 10 skali może zająć do 10 godzin.
* Jeśli zmienisz rozmiar zasoby obliczeniowe, które są używane w puli procesów roboczych spowoduje zimnych startów aplikacje, które działają w tej puli procesów roboczych.

Jest to najszybszy sposób, aby zmienić rozmiar zasobów obliczeniowych w puli procesów roboczych, który nie działa dla wszystkich aplikacji:

* Skalowanie w dół do liczby procesów roboczych do 2.  Minimalna skali w dół rozmiar w portalu jest 2. Potrwa kilka minut, aby zwolnić swoich wystąpień. 
* Wybierz nowy rozmiar obliczeń i liczby wystąpień. W tym miejscu potrwa do 2 godzin.

Jeśli aplikacje wymagają większy rozmiar zasobów obliczeniowych, możesz nie może korzystać z poprzedniej wskazówki. Zamiast zmiany rozmiaru puli procesów roboczych, który jest hostem tych aplikacji, można wypełnić innej puli procesów roboczych z pracowników wymagany rozmiar i przenieść swoje aplikacje do tej puli.

* Utwórz dodatkowe wystąpienia rozmiar wymagane obliczeń w innej puli procesów roboczych. Będzie to potrwać do godziny do wykonania.
* Ponownie przypisać obsługujące aplikacje, które muszą większy rozmiar do nowo skonfigurowanego procesu roboczego puli planów usługi aplikacji. Jest to szybkie operacji, które powinny zająć mniej niż minutę.  
* Jeśli nie musisz już tych nieużywane wystąpienia skali pierwszej puli procesów roboczych. Ta operacja może zająć kilka minut.

**Skalowanie automatyczne**: jedno z narzędzi, które ułatwiają zarządzanie użycia zasobów obliczeniowych jest automatycznie. Umożliwia skalowanie automatyczne dla frontonu lub pule procesów roboczych. Możesz wykonywanie czynności takich jak zwiększenie swoich wystąpień dowolnego typu puli w nocy i jej zmniejszenia wieczorem. Lub może dodać wystąpień, gdy liczba pracowników, które są dostępne w puli procesów roboczych spadnie poniżej określonego progu.

Jeśli chcesz ustawić reguły automatycznego skalowania wokół metryki puli zasobów obliczeniowych, a następnie pamiętać podczas inicjowania obsługi administracyjnej wymaga. Aby uzyskać więcej informacji na temat Skalowanie automatyczne środowiska usługi App Service, zobacz [Konfigurowanie automatycznego skalowania w środowisku usługi aplikacji][ASEAutoscale].

### <a name="storage"></a>Magazyn
Każdy ASE skonfigurowano 500 GB miejsca do magazynowania. Ten obszar jest używany przez wszystkie aplikacje w ASE. To miejsce do magazynowania jest częścią ASE i obecnie nie można przełączyć do użycia miejsca do magazynowania. Podczas wprowadzania korekt do routingu w sieci wirtualnej lub zabezpieczeń, należy nadal dostęp do usługi Azure Storage — lub ASE nie może działać.

### <a name="database"></a>Database (Baza danych)
Bazy danych zawiera informacje, które definiują środowiska, a także szczegółowe informacje o aplikacji, które są uruchomione w nim. Jest za część przechowywana Azure subskrypcji. Nie jest coś, co ma bezpośredni możliwość manipulowania. Podczas wprowadzania korekt do routingu w sieci wirtualnej lub zabezpieczeń, należy nadal dostęp do usługi SQL Azure--lub ASE nie może działać.

### <a name="network"></a>Sieć
Sieć wirtualną, która jest używana z Twojej ASE może być taki, który zostanie podjęta, gdy utworzono ASE lub jedną, który zgłosił wcześniejsze. Po utworzeniu podsieci podczas tworzenia ASE wymusza ASE w tej samej grupie zasobów co sieć wirtualna. Jeśli potrzebujesz grupy zasobów, używany przez Twoje ASE maja być inne niż w przypadku sieci wirtualnej, następnie należy utworzyć Twoje ASE przy użyciu szablonu usługi resource manager.

Istnieją pewne ograniczenia dotyczące sieci wirtualnej, która jest używana do ASE:

* Sieć wirtualna musi być regionalną sieć wirtualną.
* Musi istnieć co najmniej 8 adresów podsieci wdrożonym ASE.
* Po podsieci jest używana do hostowania ASE, nie można zmienić zakres adresów podsieci. Z tego powodu zaleca się, że podsieć zawiera co najmniej 64 adresy na uwzględnienie przyszłego rozwoju ASE.
* Nie można w tej podsieci, ale ASE nic.

W przeciwieństwie do usługi hostowanej, która zawiera ASE [sieci wirtualnej] [ virtualnetwork] i podsieci są pod kontrolą użytkownika.  Można administrować sieci wirtualnej za pośrednictwem wirtualnej sieci interfejsu użytkownika lub środowiska PowerShell.  ASE można wdrożyć w klasyczny lub Menedżera zasobów w sieci wirtualnej.  Portal i środowiska interfejsu API są nieco inne między Classic i sieci wirtualnych Menedżera zasobów, ale środowisko ASE jest taka sama.

Sieć wirtualną, która jest używana do hostowania ASE można użyć albo prywatnych adresów RFC1918 IP lub może używać publicznych adresów IP.  Jeśli chcesz użyć zakres IP, który nie pasuje do żadnego RFC1918 (10.0.0.0/8 172.16.0.0/12, 192.168.0.0/16), a następnie musisz utworzyć sieci wirtualnej i podsieci, który będzie używany przez Twoje ASE wcześniejsze tworzenie ASE.

Ponieważ ta funkcja umieszcza usługi Azure App Service w sieci wirtualnej, oznacza to, że Twoje aplikacje, które znajdują się w sieci ASE można teraz uzyskiwać dostęp do zasobów, które są dostępne za pośrednictwem programu ExpressRoute lub wirtualnych sieci prywatnych (VPN) lokacja lokacja bezpośrednio. Aplikacje, które znajdują się w środowisku usługi aplikacji nie wymagają dodatkowe funkcje sieci na dostęp do zasobów dostępnych do sieci wirtualnej, który jest hostem środowiska usługi aplikacji. Oznacza to, że nie trzeba używać integracji sieci Wirtualnej lub połączeń hybrydowych na uzyskanie dostępu do zasobów w lub podłączone do sieci wirtualnej. Nadal można obie te funkcje Chociaż dostęp do zasobów w sieci, które nie są podłączone do sieci wirtualnej.

Na przykład umożliwia integrację sieci Wirtualnej integracji z siecią wirtualną, która jest w ramach subskrypcji, ale nie jest podłączony do sieci wirtualnej, która jest Twoje ASE. Można nadal umożliwia również połączeń hybrydowych dostęp do zasobów, które znajdują się w innych sieciach, podobnie jak zwykle.  

Jeśli masz sieci wirtualnej skonfigurowaną ExpressRoute sieci VPN, należy zwrócić uwagę niektórych potrzeb routingu, które ma ASE. Istnieją pewne konfiguracje trasy zdefiniowane przez użytkownika (przez), które są niezgodne z ASE. Aby uzyskać więcej informacji o uruchamianiu ASE w sieci wirtualnej z usługi ExpressRoute, zobacz [uruchamianie środowiska usługi aplikacji w sieci wirtualnej z ExpressRoute][ExpressRoute].

#### <a name="securing-inbound-traffic"></a>Zabezpieczanie ruchu przychodzącego
Istnieją dwie podstawowe metody, aby kontrolować ruch przychodzący do Twojej ASE.  Groups(NSGs) zabezpieczeń sieci można użyć do kontrolowania jakie IP adresy mogą uzyskiwać dostęp do Twojego ASE, zgodnie z opisem w tym miejscu [kontrola ruchu przychodzącego w środowisku usługi aplikacji](app-service-app-service-environment-control-inbound-traffic.md) i można również skonfigurować Twoje ASE z wewnętrznego Balancer(ILB) obciążenia.  Te funkcje można również ze sobą, jeśli chcesz ograniczyć dostęp za pomocą grup NSG do Twojej ASE ILB.

Po utworzeniu ASE utworzy adresu VIP w sieci wirtualnej.  Istnieją dwa typy adresów VIP, wewnętrznych i zewnętrznych.  Po utworzeniu ASE z zewnętrznego adresu VIP aplikacji w Twojej ASE zostaną dostępny za pośrednictwem adresu IP routingu internetowego. Po wybraniu wewnętrzne Twojej ASE zostaną skonfigurowane ILB i nie będzie bezpośrednio dostępny internet.  ASE ILB nadal wymaga zewnętrznego adresu VIP, ale jest używana tylko dla platformy Azure, zarządzanie i obsługę dostępu.  

Podczas tworzenia ILB ASE Podaj poddomeny używane przez ILB ASE i będzie musiał zarządzać własnego systemu DNS dla domeny podrzędnej, które określisz.  Ponieważ ustawienia nazwy poddomeny należy zarządzać certyfikatu używanego na potrzeby dostępu protokołu HTTPS.  Po utworzeniu ASE monit o podanie certyfikatu.  Aby dowiedzieć się więcej na temat tworzenia i używania ASE ILB przeczytaj [przy użyciu wewnętrznego modułu równoważenia obciążenia z środowiska usługi aplikacji][ILBASE]. 

## <a name="portal"></a>Portal
Można zarządzać i monitorować środowiska usługi aplikacji za pomocą interfejsu użytkownika w portalu Azure. Jeśli masz ASE, następnie najprawdopodobniej będzie widoczna ikona usługi aplikacji na Twoje paska bocznego. Ten symbol jest używany do reprezentowania środowiska usługi App Service w portalu Azure:

![Symbol środowiska usługi aplikacji][1]

Aby otworzyć Interfejs użytkownika, który zawiera listę wszystkich środowisk usługi aplikacji, można użyć ikony lub wybrać cudzysłów ostrokątny (">" symbol) w dolnej części paska bocznego, aby wybrać środowiska usługi App Service. Wybranie jednego z ASEs na liście, możesz otworzyć Interfejs użytkownika, który służy do monitorowania i zarządzania nim.

![Interfejs umożliwiający monitorowanie i zarządzanie środowiskiem usługi aplikacji][2]

Ten pierwszy blok zawiera niektóre właściwości sieci ASE wraz z wykresu metryki dla każdej puli zasobów. Niektóre właściwości, które przedstawiono w **Essentials** bloku są również hiperłącza, które spowoduje to otwarcie bloku, który jest skojarzony z nim. Na przykład można wybrać **sieci wirtualnej** nazwę, aby otworzyć Interfejs użytkownika skojarzonego z siecią wirtualną uruchomioną w Twojej ASE. **Planów usługi App Service** i **aplikacji** Otwórz każdy bloków, których te elementów, które znajdują się w sieci ASE.  

### <a name="monitoring"></a>Monitorowanie
Wykresy umożliwiają wyświetlanie różne metryki wydajności w każdej puli zasobów. Dla puli frontonu można monitorować średnią Procesora i pamięci. Dla pule procesów roboczych można monitorować ilość, która jest używana i ilość, która jest dostępna.

Użyj wielu usługi aplikacji, jakie można wprowadzać planów pracowników w puli procesów roboczych. Obciążenie nie jest dystrybuowane w taki sam sposób, jak z serwerami frontonu, więc użycia Procesora i pamięci nie zapewniają znacznie kategoriach przydatnych informacji na temat. Jest większe znaczenie śledzić liczbę pracowników, które zostały użyte i są dostępne — zwłaszcza, jeśli zarządzasz systemie dla innych użytkowników.  

Umożliwia także wszystkie metryki, które można śledzić na wykresach konfigurowania alertów. Konfigurowanie alertów w tym miejscu działa tak samo jak w innych miejscach w usłudze App Service. Można ustawić alert z poziomu **alerty** interfejsu użytkownika należą do lub z przechodzenia do dowolnego metryki interfejsu użytkownika i wybierając szczegółów **dodać Alert**.

![Metryki interfejsu użytkownika][3]

Metryki, które właśnie zostały omówione są metryki środowiska usługi aplikacji. Dostępne są także metryk, które są dostępne na poziomie planu usługi aplikacji. Jest to, gdzie monitorowanie procesora CPU i pamięci sprawia, że wiele znaczeniu.

W elemencie ASE planów usługi aplikacji są dedykowane planów usługi aplikacji. Oznacza to, że tylko aplikacje, które są uruchomione na hostach przydzielone do planu usługi aplikacji to aplikacje w tym planie usługi aplikacji. Aby wyświetlić szczegóły na swój plan usługi aplikacji, wywołaj planu usługi aplikacji z dowolnego z listy w Interfejsie użytkownika ASE lub z **planów usługi App Service Przeglądaj** (które wyświetla listę wszystkich z nich).   

### <a name="settings"></a>Ustawienia
W bloku ASE jest **ustawienia** sekcja, która zawiera kilka ważnych funkcji:

**Ustawienia** > **właściwości**: **ustawienia** automatycznie zostanie otwarty blok po uruchomieniu z bloku ASE. U góry jest **właściwości**. Liczba elementów w tym miejscu, które są zbędne, aby wyświetlić w **Essentials**, ale co to jest bardzo przydatny **wirtualnego adresu IP**, oraz **wychodzących adresów IP**.

![Blok ustawień i właściwości][4]

**Ustawienia** > **adresów IP**: po utworzeniu aplikacji IP Secure Sockets Layer (SSL) w Twojej ASE należy adresów IP protokołu SSL. Aby można było go uzyskać, Twoje ASE musi adresów IP protokołu SSL, które jest właścicielem które mogą być przydzielone. Po utworzeniu ASE ma jeden adres IP SSL do tego celu, ale można dodać więcej. Brak opłat dla dodatkowych SSL z adresu IP, adresy, jak pokazano w [cennik usługi aplikacji] [ AppServicePricing] (w sekcji połączenia SSL). Dodatkowe cena to cena SSL z adresu IP.

**Ustawienia** > **Front End puli** / **pule procesów roboczych**: każdego z tych blokach puli zasobów oferuje możliwość wyświetlania informacji tylko w tej puli zasobów, oprócz formantów pełni skalowania tej puli zasobów.  

Podstawowy bloku dla każdej puli zasobów udostępnia wykres z metryki dla tej puli zasobów. Podobnie jak w przypadku wykresów z bloku ASE można przejdź do wykresu i Konfigurowanie alertów, zgodnie z potrzebami. Ustawianie alertu z bloku ASE puli zasobów dla określonych ma tak samo, jak to zrobić z puli zasobów. Z puli procesów roboczych **ustawienia** bloku, musisz mieć dostęp do wszystkich aplikacji lub planów usługi App Service, które działają w tej puli procesów roboczych.

![Proces roboczy puli ustawienia interfejsu użytkownika][5]

### <a name="portal-scale-capabilities"></a>Możliwości skalowania portalu
Istnieją trzy operacje skalowania:

* Zmiana liczby adresów IP w ASE, które są dostępne do użycia protokołu SSL z adresu IP.
* Zmiana rozmiaru zasobu obliczeń, który jest używany w puli zasobów.
* Zmiana liczby zasoby obliczeniowe, które są używane w puli zasobów, ręcznie lub za pośrednictwem Skalowanie automatyczne.

W portalu istnieją trzy sposoby kontrolowania liczby serwerów znajdujących się w sieci pule zasobów:

* Operację skalowania w głównym bloku ASE u góry. Umożliwia skali wiele zmian w konfiguracji puli frontonu i proces roboczy. Są one wszystkich stosowane jako jedna operacja.
* Operację skalowania ręczne z puli zasobów poszczególnych **skali** bloku, która znajduje się w **ustawienia**.
* Skalowanie automatyczne, który został skonfigurowany z puli zasobów poszczególnych **skali** bloku.

Aby używać operacji skalowania w bloku ASE, przeciągnij suwak do ilości i Zapisz. Ten interfejs obsługuje również zmiany rozmiaru.  

![Skala interfejsu użytkownika][6]

Aby korzystać z funkcji ręcznego i automatycznego skalowania w puli zasobów dla określonych, przejdź do **ustawienia** > **Front End puli** / **pule procesów roboczych** odpowiednio. Następnie otwarcie puli, który chcesz zmienić. Przejdź do **ustawienia** > **skalowanie** lub **ustawienia** > **skalowanie w górę**. **Skaluj w poziomie** bloku umożliwia sterowanie ilości wystąpieniu. **Skaluj w górę** umożliwia kontrolowanie rozmiaru zasobu.  

![Ustawienia skalowania interfejsu użytkownika][7]

## <a name="fault-tolerance-considerations"></a>Zagadnienia dotyczące odporności na uszkodzenia
Można skonfigurować maksymalnie 55 zasoby obliczeniowe całkowita środowiska usługi aplikacji. Z tych 55 zasoby obliczeniowe tylko 50 może służyć do obsługi zadań. Przyczyną tego jest podwójny. Brak co najmniej 2 zasoby obliczeniowe frontonu.  Który pozostawia do 53 do obsługi alokacji puli procesów roboczych. Aby zapewnić odporność na uszkodzenia, trzeba zasobu dodatkowe zasoby obliczeniowe, który jest przydzielony zgodnie z następującymi zasadami:

* Każdego procesu roboczego puli wymaga co najmniej 1 zasobów dodatkowe zasoby obliczeniowe, który nie jest dostępny do przypisania obciążenia.
* Gdy ilość zasobów obliczeniowych w puli procesów roboczych przekracza określoną wartość, inne zasoby obliczeniowe jest wymagany dla odporność na uszkodzenia. To nie jest w przypadku puli frontonu.

W każdej puli pojedynczego procesu roboczego wymagania dotyczące odporności na uszkodzenia obejmują dla danej wartości X zasoby przydzielone do puli procesów roboczych:

* Jeśli X to od 2 do 20, ilość zasoby obliczeniowe można używać, których można użyć w przypadku obciążeń jest X-1.
* Jeśli X to od 21 do 40, ilość zasoby obliczeniowe można używać, których można użyć w przypadku obciążeń jest X-2.
* Jeśli X jest między 41 i 53, ilość zasoby obliczeniowe można używać, których można użyć w przypadku obciążeń jest X-3.

Minimalnego miejsca zajmowanego ma 2 serwerów frontonu i 2 procesy robocze.  Z powyższych przypadków następnie, poniżej przedstawiono kilka przykładów wyjaśnienie:  

* Jeśli masz 30 pracowników w jednej puli 28 z nich mogą używane do obsługi zadań.
* Jeśli masz 2 procesy robocze w jednej puli 1 można użyć do obsługi zadań.
* Jeśli masz 20 pracowników w jednej puli 19 można użyć do obsługi zadań.  
* Jeśli masz 21 pracowników w jednej puli, można następnie nadal 19 tylko w przypadku obciążeń hosta.  

Ważne jest aspekt odporności na uszkodzenia, ale należy pamiętać o jego skalowania niektórych progi. Jeśli chcesz dodać większej pojemności z wystąpień 20, następnie przejdź do 22 lub nowszej ponieważ 21 nie dodaje żadnych większej pojemności. To samo dotyczy mają powyżej 40, gdzie kolejny numer, który dodaje pojemności jest 42.  

## <a name="deleting-an-app-service-environment"></a>Usuwanie środowiska usługi aplikacji
Jeśli chcesz usunąć środowiska usługi aplikacji, po prostu użyj **usunąć** akcji w górnej części bloku środowiska usługi aplikacji. Po wykonaniu tej czynności zostanie wyświetlony monit wprowadź nazwę środowiska usługi aplikacji, aby upewnić się, że naprawdę chcesz to zrobić. Należy pamiętać, usunięcie środowiska usługi aplikacji powoduje usunięcie całej zawartości w niej również.  

![Usuwanie środowiska usługi aplikacji interfejsu użytkownika][9]  

## <a name="getting-started"></a>Wprowadzenie
Wprowadzenie do środowiska usługi App Service, zobacz [tworzenie środowiska usługi aplikacji](app-service-web-how-to-create-an-app-service-environment.md).

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
[Appserviceplans]: ../azure-web-sites-web-hosting-plans-in-depth-overview.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoScale]: app-service-web-scale-a-web-app-in-an-app-service-environment.md
[ControlInbound]: app-service-app-service-environment-control-inbound-traffic.md
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md
