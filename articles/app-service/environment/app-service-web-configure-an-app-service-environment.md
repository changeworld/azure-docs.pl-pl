---
title: Jak skonfigurować usługę App Service Environment w wersji 1 - Azure
description: Konfiguracji, zarządzania i monitorowania środowiska App Service Environment w wersji 1
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
ms.custom: seodec18
ms.openlocfilehash: 5c0b4117f6e7b48dce1746ad6eb3dbe29c0d16af
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130623"
---
# <a name="configuring-an-app-service-environment-v1"></a>Konfigurowanie aplikacji usługi Environment w wersji 1

> [!NOTE]
> Ten artykuł dotyczy środowiska App Service Environment w wersji 1.  Istnieje nowsza wersja usługi App Service Environment jest łatwiejszy w obsłudze, która działa na bardziej zaawansowanych infrastruktury. Aby dowiedzieć się więcej na temat nowej wersji rozpoczynać [wprowadzenie do usługi App Service Environment](intro.md).
> 

## <a name="overview"></a>Omówienie
Na wysokim poziomie Azure App Service Environment składa się z kilku głównych składników:

* Usługa hostowana zasoby obliczeniowe, które działają w środowisku App Service
* Magazyn
* Bazy danych
* Classic(V1) lub Azure Manager(V2) zasobów sieci wirtualnej (VNet) 
* Podsieci za pomocą usługi App Service Environment w przypadku hostowanych uruchomione w nim

### <a name="compute-resources"></a>Zasoby obliczeniowe
Korzystanie z zasobów obliczeniowych dla pul zasobów cztery.  Każdy App Service Environment (ASE) ma zbiór Frontony i trzy pule procesów roboczych możliwe. Nie trzeba używać wszystkich trzech procesów roboczych — Jeśli chcesz, wystarczy użyć jednego lub dwóch.

Hosty w pulach zasobów (frontonami ani procesami roboczymi) nie są dostępne dla dzierżaw. Nie można użyć protokołu RDP (Remote Desktop) aby połączyć się z nimi, zmienić ich inicjowania obsługi lub pełnić rolę administratora na nich.

Można ustawić liczby puli zasobów i rozmiaru. W przypadku środowiska ASE masz cztery opcje rozmiaru, które są oznaczone etykietami od P1 do P4. Aby uzyskać szczegółowe informacje o tych rozmiarach i ich ceny, zobacz [cennik usługi App Service](https://azure.microsoft.com/pricing/details/app-service/).
Zmiana rozmiaru lub ilość nosi nazwę operacji skalowania.  Operacja skalowania tylko jedna może być w toku w danym momencie.

**Frontonu kończy się**: Frontony są punktów końcowych HTTP/HTTPS dla aplikacji, które są przechowywane w środowisku ASE. Obciążenia nie działają w frontonów.

* Środowisko ASE rozpoczyna się od dwóch P2s, na której jest wystarczająca na potrzeby obciążeń deweloperskich/testowych i obciążeń produkcyjnych niskiego poziomu. Zdecydowanie zaleca się P3s dla umiarkowany do obciążeń produkcyjnych duże.
* Umiarkowany do obciążeń produkcyjnych duże zaleca się, że masz co najmniej cztery P3s, aby upewnić się, ma wystarczających Frontony uruchomione po wystąpieniu zaplanowanej konserwacji. Działania zaplanowanej konserwacji zostanie wyświetlone w dół jednym frontonu w danym momencie. Pozwala to zmniejszyć ogólną dostępnej pojemności frontonu podczas czynności konserwacyjnych.
* Frontony może potrwać do godziny aprowizację. 
* Dalsze szczegóły skalowania, należy monitorować procent użycia procesora CPU i pamięci oraz metryki aktywne żądania dla puli frontonu. W przypadku wartości procentowych procesora CPU lub pamięci przekracza 70% podczas uruchamiania P3s, Dodaj więcej frontonów. Wartość aktywnych żądań uśrednia do 15 000 do 20 000 żądań na frontonie, należy również dodać więcej frontonów. Podstawowym celem jest zapewnienie procent procesora CPU i pamięci, poniżej 70% i aktywne żądania uśrednianie do poniżej 15 000 żądań na wierzch kończy się po uruchomieniu P3s.  

**Procesy robocze**: Procesy robocze są, gdzie faktycznie działają. Skalowanie w górę swoje plany usługi App Service, która korzysta z procesów roboczych w puli procesów roboczych skojarzone.

* Nie możesz natychmiast dodać pracowników. One może potrwać do godziny aprowizację.
* Skalowanie rozmiaru zasobu obliczeniowego, dla każdej puli potrwa < 1 godziny dla domeny aktualizacji. Istnieją 20 domen aktualizacji w środowisku ASE. W przypadku zmiany skali rozmiar obliczeń w puli procesów roboczych z 10 wystąpień może potrwać do 10 godzin.
* W przypadku zmiany rozmiaru zasobów obliczeniowych, które są używane w puli procesów roboczych spowoduje zimnych startów aplikacje, które są uruchomione w tej puli procesów roboczych.

Jest to najszybszy sposób, aby zmienić rozmiar zasobu obliczeń w puli procesów roboczych, które nie jest uruchomione wszystkie aplikacje:

* Skaluj w dół liczbę procesów roboczych do 2.  Minimalna skalowanie w dół rozmiar w portalu jest 2. Potrwa kilka minut można cofnąć alokacji wystąpień. 
* Wybierz nowy rozmiar obliczeń i liczbę wystąpień. W tym miejscu potrwa do 2 godzin.

Jeśli aplikacje wymagają większy rozmiar zasobów obliczeniowych, nie można korzystać z wcześniejszych wskazówkach. Zamiast zmieniać rozmiar puli procesów roboczych, który jest hostem tych aplikacji, można wypełnić innej puli procesów roboczych z pracowników żądany rozmiar i przenieść swoje aplikacje do tej puli.

* Utwórz dodatkowe wystąpienia rozmiaru obliczeń potrzebne w innej puli procesów roboczych. Będzie to potrwać do godziny do ukończenia.
* Przypisz ponownie swoje plany usługi App Service, które hostują aplikacje, które muszą większy rozmiar puli procesów roboczych nowo skonfigurowanego. To szybka operacja, która powinno zająć mniej niż minutę.  
* Skaluj w dół do pierwszej puli procesów roboczych, jeśli nie potrzebujesz już tych nieużywanych wystąpień. Ta operacja trwa kilka minut.

**Skalowanie automatyczne**: Jednym z narzędzi, które mogą ułatwić Ci Zarządzanie użycia zasobów obliczeniowych jest skalowanie automatyczne. Możesz użyć skalowania automatycznego dla frontonu lub pule procesów roboczych. Można wykonywanie czynności takich jak wzrost wystąpień dowolnego typu puli rano i zmniejszyć jego wieczorem. Lub może być można dodać wystąpienia, gdy liczba procesów roboczych, które są dostępne w puli procesów roboczych nie spadnie poniżej określonego progu.

Jeśli chcesz ustawić reguły skalowania automatycznego wokół metryki puli zasobów obliczeniowych, a następnie należy pamiętać, czas, który wymaga inicjowania obsługi administracyjnej. Aby uzyskać więcej szczegółów na temat skalowania automatycznego w środowiskach usługi App Service, zobacz [jak skonfigurować automatyczne skalowanie w środowisku usługi App Service][ASEAutoscale].

### <a name="storage"></a>Magazyn
Każdy ASE jest skonfigurowane z 500 GB miejsca do magazynowania. To miejsce jest używane dla wszystkich aplikacji w środowisku ASE. To miejsce do magazynowania jest częścią środowiska ASE i obecnie nie mogą być przełączane do użycia miejsca do magazynowania. Jeśli wprowadzasz dostosowania do zabezpieczeń lub routing w sieci wirtualnej, należy nadal zezwalają na dostęp do usługi Azure Storage — lub środowisko ASE nie będzie działać.

### <a name="database"></a>Database (Baza danych)
Baza danych przechowuje informacje, które definiują środowisko, a także szczegółowe informacje o aplikacji, które są uruchomione w nim. Zbyt jest częścią subskrypcji przechowywanych w usłudze Azure. Nie jest coś, co masz możliwość bezpośrednie manipulowanie. Jeśli wprowadzasz dostosowania do zabezpieczeń lub routing w sieci wirtualnej, należy nadal zezwalają na dostęp do usługi SQL Azure — lub środowisko ASE nie będzie działać.

### <a name="network"></a>Sieć
Sieć wirtualną, która jest używana przy użyciu środowiska ASE może być taki, który wprowadzone podczas tworzenia środowiska ASE lub takiego, które zostały wprowadzone wcześniej. Po utworzeniu podsieci podczas tworzenia środowiska ASE wymusza ASE znajdować się w tej samej grupie zasobów co sieć wirtualna. Jeśli potrzebujesz grupę zasobów używaną przez środowisko ASE może być inny niż w przypadku sieci wirtualnej, należy utworzyć środowisko ASE przy użyciu szablonu usługi resource manager.

Istnieją pewne ograniczenia dotyczące sieci wirtualnej, która jest używana do środowiska ASE:

* Sieć wirtualna musi znajdować się regionalnej sieci wirtualnej.
* Musi istnieć podsieci z co najmniej 8 adresów wdrożonym środowiska ASE.
* Po podsieć jest używana do hostowania środowiska ASE, nie można zmienić zakres adresów podsieci. Z tego powodu zaleca się, że podsieć zawiera co najmniej 64 adresów na potrzeby przyszłego rozwoju środowiska ASE.
* Nie można nic w podsieci, ale środowisko ASE.

W przeciwieństwie do usługi hostowanej, która zawiera środowisko ASE [sieci wirtualnej] [ virtualnetwork] i podsieci są pod kontrolą użytkownika.  Można administrować sieci wirtualnej za pośrednictwem wirtualnej sieci interfejsu użytkownika lub środowiska PowerShell.  Środowisko ASE można wdrożyć w klasycznej lub siecią wirtualną Menedżera zasobów.  Portal i interfejsu API środowiska różnią się nieco między klasycznej sieci wirtualnej i sieci wirtualnych Menedżera zasobów, ale środowisko ASE jest taka sama.

Sieć wirtualną, która jest używana do hostowania środowiska ASE można użyć albo prywatnych adresów RFC1918 IP lub jego używania publicznych adresów IP.  Jeśli chcesz użyć zakres adresów IP, który nie jest objęta RFC1918 (10.0.0.0/8, 172.16.0.0/12 i 192.168.0.0/16), a następnie należy utworzyć sieci wirtualnej i podsieci, który będzie używany przez środowisko ASE wcześniejsze tworzenie środowiska ASE.

Ponieważ ta funkcja umieszcza usługi Azure App Service w sieci wirtualnej, oznacza to, czy Twojej aplikacji, które są hostowane w środowisku ASE można teraz uzyskać dostęp do zasobów, które są dostępne za pośrednictwem usługi ExpressRoute lub wirtualnych sieci prywatnych (VPN) lokacja lokacja bezpośrednio. Aplikacje, które znajdują się w środowisku App Service Environment nie wymagają dodatkowe funkcje sieci na dostęp do zasobów dostępnych w sieci wirtualnej, który jest hostem środowiska App Service Environment. Oznacza to, że nie trzeba uzyskać dostęp do zasobów, w lub jest połączony z siecią wirtualną za pomocą integracji sieci Wirtualnej lub połączeń hybrydowych. Nadal służy obu tych funkcji, jednak dostęp do zasobów w sieciach, które nie są połączone z siecią wirtualną.

Na przykład można użyć Integracja sieci Wirtualnej do integracji z sieci wirtualnej, która znajduje się w Twojej subskrypcji, ale nie jest podłączony do sieci wirtualnej, który znajduje się w środowisku ASE. Dostęp do zasobów znajdujących się w innych sieciach, tak samo, jak zwykle można nadal również służy połączeń hybrydowych.  

Jeśli masz sieć wirtualną skonfigurowane przy użyciu usługi ExpressRoute sieci VPN, należy pamiętać niektórych potrzeb routingu, które ma środowisko ASE. Istnieją pewne konfiguracje trasa zdefiniowana przez użytkownika (UDR), które są niezgodne z ASE. Aby uzyskać więcej informacji na temat uruchamiania środowiska ASE w sieci wirtualnej przy użyciu usługi ExpressRoute, zobacz [uruchomione środowisko App Service w sieci wirtualnej przy użyciu usługi ExpressRoute][ExpressRoute].

#### <a name="securing-inbound-traffic"></a>Zabezpieczanie ruchu przychodzącego
Istnieją dwie podstawowe metody kontrolować ruch przychodzący do środowiska ASE.  Groups(NSGs) zabezpieczeń sieci można użyć do kontrolowania IP, jakie adresy mogą uzyskiwać dostęp do środowiska ASE, zgodnie z opisem w tym miejscu [jak kontrolować ruch przychodzący w środowisku usługi App Service](app-service-app-service-environment-control-inbound-traffic.md) i środowisko ASE można również skonfigurować przy użyciu wewnętrznego modułu równoważenia obciążenia (ILB).  Te funkcje można również ze sobą, jeśli chcesz ograniczyć dostęp przy użyciu sieciowych grup zabezpieczeń do środowiska ASE wewnętrznego modułu równoważenia obciążenia.

Po utworzeniu środowiska ASE, zostanie utworzony adres VIP w sieci wirtualnej.  Istnieją dwa typy adresów VIP, wewnętrznych i zewnętrznych.  Po utworzeniu środowiska ASE z zewnętrznym wirtualnym adresem IP następnie swoje aplikacje w środowisku ASE będzie dostępny przy użyciu adresu IP z obsługą routingu internetowego. Po wybraniu wewnętrzne środowiska ASE zostaną skonfigurowane z wewnętrznym modułem równoważenia obciążenia i nie jest bezpośrednio dostępny przez internet.  Środowisko ASE z wewnętrznym modułem równoważenia obciążenia nadal wymaga zewnętrznego wirtualnego adresu IP, ale jest używana tylko dla dostępu do platformy Azure, zarządzania i konserwacji.  

Podczas tworzenia środowiska ASE z wewnętrznym modułem równoważenia obciążenia zapewnia poddomeny używane przez środowisko ASE wewnętrznego modułu równoważenia obciążenia i będzie trzeba zarządzać własną usługą DNS w domenie podrzędnej, które określisz.  Ponieważ wartość nazwy domeny podrzędnej należy zarządzać certyfikatu używanego na potrzeby dostępu do protokołu HTTPS.  Po utworzeniu środowiska ASE monit o podanie certyfikatu.  Aby dowiedzieć się więcej na temat tworzenia i używania środowiska ASE z wewnętrznym modułem równoważenia obciążenia przeczytaj [przy użyciu wewnętrznego modułu równoważenia obciążenia w środowisku usługi App Service][ILBASE]. 

## <a name="portal"></a>Portal
Można zarządzać i monitorować środowiska App Service Environment za pomocą interfejsu użytkownika w witrynie Azure portal. Jeśli masz środowisko ASE, następnie najprawdopodobniej będzie widoczny symbol usługi App Service na Twoje pasku bocznym. Ten symbol jest używana do reprezentowania środowisk usługi App Service w witrynie Azure portal:

![App Service Environment symboli][1]

Aby otworzyć Interfejs użytkownika, który zawiera listę wszystkich środowisk App Service Environment, można użyć ikony lub wybierz cudzysłów ostrokątny (">" symbol) u dołu paska bocznego wybrać środowisk usługi App Service. Wybranie jednego z środowiska ASE na liście, możesz otworzyć Interfejs użytkownika, który służy do monitorowania i zarządzania nim.

![Interfejs użytkownika do monitorowania i zarządzania środowiska App Service Environment][2]

Ten pierwszy blok niektórych właściwości środowiska ASE, wraz z wykresu metryki na pulę zasobów. Niektóre właściwości, które są wyświetlane w **Essentials** bloku są również hiperłącza, które zostanie otwarty blok, który jest skojarzony z nim. Na przykład, możesz wybrać **sieci wirtualnej** nazwę, aby otworzyć Interfejs użytkownika skojarzonego z siecią wirtualną, działający w środowisku ASE. **Plany usługi App Service** i **aplikacje** każdej otwartej bloków w witrynie, w których przedstawiono te elementy, które znajdują się w środowisku ASE.  

### <a name="monitoring"></a>Monitorowanie
Wykresy pozwalają wyświetlić różne metryki wydajności w każdej puli zasobów. Dla puli frontonu można monitorować średnią procesora CPU i pamięci. W przypadku pul procesów roboczych można monitorować ilość, która jest używana i ilością dostępną.

Użyj wielu usługi App Service ułatwia plany procesów roboczych w puli procesów roboczych. Obciążenia nie są rozmieszczane w taki sam sposób, zgodnie z serwerami frontonu, więc użycie procesora CPU i pamięci nie udostępniają wiele przeszkodą w nim przydatne informacje. Jest niezwykle ważne śledzić liczbę procesów roboczych, które były używane i są dostępne — zwłaszcza, jeśli zarządzasz systemie dla innych użytkowników.  

Umożliwia także wszystkie metryki, które mogą być śledzone na wykresach konfigurowania alertów. Konfigurowanie alertów w tym miejscu działa tak samo jak w innych miejscach w usłudze App Service. Można ustawić alert z poziomu **alerty** interfejsu użytkownika wchodzi w skład lub przechodzenia do szczegółów w dowolnej metryki interfejsu użytkownika i wybierając polecenie **Dodaj Alert**.

![Metryki interfejsu użytkownika][3]

Metryki, które właśnie zostały omówione są metryki środowiska App Service Environment. Dostępne są także metryk, które są dostępne na poziomie plan usługi App Service. Jest to, gdzie jest bardzo rozsądne zapewniają monitorowanie Procesora i pamięci.

W przypadku środowiska ASE wszystkie plany usługi App Service są dedykowane plany usługi App Service. Oznacza to, że tylko aplikacje, które są uruchomione na hostach, przydzielone do planu usługi App Service czy aplikacje w tym planie usługi App Service. Aby wyświetlić szczegóły dotyczące planu usługi App Service, wywołaj planu usługi App Service za pomocą dowolnego z list w Interfejsie użytkownika środowiska ASE lub z **planów usługi App Service Przeglądaj** (który zawiera listę wszystkich z nich).   

### <a name="settings"></a>Ustawienia
W bloku środowisko ASE jest **ustawienia** sekcji, która zawiera kilka ważnych możliwości:

**Ustawienia** > **właściwości**: **Ustawienia** automatycznie zostanie otwarty blok po uruchomieniu blok środowiska ASE. U góry znajduje **właściwości**. Istnieje kilka elementów w tym miejscu, które są nadmiarowe, aby zobaczyć w **Essentials**, ale co to jest bardzo przydatny jest **wirtualnego adresu IP**, także **wychodzące adresy IP**.

![Blok ustawień i właściwości][4]

**Ustawienia** > **adresów IP**: Po utworzeniu aplikacji IP Secure Sockets Layer (SSL) w środowisku ASE jest potrzebny adres IP SSL. Aby można było go uzyskać, Twoje środowisko ASE wymaga adresów IP SSL, które jest właścicielem, które mogą być przydzielone. Po utworzeniu środowiska ASE zawiera jeden adres IP SSL w tym celu, ale można dodać więcej. Jest opłata za dodatkowe połączenie IP SSL adresy, jak pokazano na [cennik usługi App Service] [ AppServicePricing] (w sekcji połączenia SSL). Dodatkowe cena to cena połączenie IP SSL.

**Ustawienia** > **puli frontonu** / **pule procesów roboczych**: Każda z tych bloków puli zasobów oferuje możliwość wyświetlania informacji tylko w tej puli zasobów, oprócz zapewniania formanty do w pełni skalowania tej puli zasobów.  

Podstawowy blok dla każdej puli zasobów zawiera wykres za pomocą metryk dla tej puli zasobów. Podobnie jak z wykresami z poziomu bloku ASE można przejść do wykresu i Konfigurowanie alertów zgodnie z potrzebami. Ustawienie alert z poziomu bloku ASE dla puli zasobów określonych działa tak samo jak to zrobić z puli zasobów. Z puli procesów roboczych **ustawienia** bloku, masz dostęp do wszystkich aplikacji lub plany usługi App Service, które są uruchomione w tej puli procesów roboczych.

![Interfejs użytkownika ustawień puli procesów roboczych][5]

### <a name="portal-scale-capabilities"></a>Możliwości skalowania w portalu
Istnieją trzy operacje skalowania:

* Zmienianie liczby adresów IP w środowisku ASE, które są dostępne do użycia protokołu SSL z adresu IP.
* Zmiana rozmiaru zasobu obliczeniowego, który jest używany w puli zasobów.
* Zmienianie liczby zasobów obliczeniowych, które są używane w puli zasobów, ręcznie lub za pomocą skalowania automatycznego.

W portalu istnieją trzy sposoby kontrolowania liczby serwerów, które mają w pulach zasobów:

* Operacja skalowania w głównym bloku ASE u góry. Zmiany konfiguracji można było skalowania wielu pul frontonu i procesu roboczego. Są one wszystkie stosowane jako pojedyncza operacja.
* Operacją skalowanie ręczne z puli zasobów poszczególnych **skalowania** bloku, który znajduje się w **ustawienia**.
* Skalowanie automatyczne, który został skonfigurowany z puli zasobów poszczególnych **skalowania** bloku.

Aby użyć operacji skalowania do bloku środowiska ASE, suwaka ilości i Zapisz. Ten interfejs obsługuje również zmiany rozmiaru.  

![Skalowanie interfejsu użytkownika][6]

Aby użyć możliwości ręczne lub automatyczne skalowanie puli określonego zasobu, przejdź do **ustawienia** > **pula frontonu** / **pule procesów roboczych** jako jest to właściwe. Następnie otwórz puli w której chcesz zmienić. Przejdź do **ustawienia** > **skalowanie w poziomie** lub **ustawienia** > **skalowanie w górę**. **Skalowanie w poziomie** bloku pozwala na sterowanie ilość wystąpienia. **Skaluj w górę** umożliwia kontrolowanie rozmiaru zasobu.  

![Ustawienia skalowania interfejsu użytkownika][7]

## <a name="fault-tolerance-considerations"></a>Zagadnienia dotyczące odporności na uszkodzenia
Można skonfigurować środowisko App Service do użycia do 55 zasoby obliczeniowe całkowitej. Te 55 zasobów obliczeniowych zaledwie 50 może służyć do obsługi zadań. Przyczyną jest podwójny. Istnieje co najmniej 2 zasoby obliczeniowe frontonu.  Która pozostawia do 53 do obsługi alokacji puli procesów roboczych. W celu zapewnienia odporności na uszkodzenia, musisz mieć zasób obliczeniowy, przydzielany zgodnie z następującymi zasadami:

* Każdej puli procesów roboczych wymaga co najmniej 1 zasób obliczeniowy, która nie jest dostępna do przypisania obciążenia.
* Gdy ilość zasobów obliczeniowych w puli procesów roboczych przekroczy określoną wartość, inny zasób obliczeniowy jest wymagana dla odporności na uszkodzenia. Nie jest w przypadku puli frontonu.

W każdej puli pojedynczego procesu roboczego wymagania dotyczące odporności na uszkodzenia są, dla danej wartości X zasoby przydzielone do puli procesów roboczych:

* Gdy wartość X jest od 2 do 20, ilość zasobów obliczeniowych można używać, które można użyć w przypadku obciążeń jest X-1.
* Gdy wartość X jest od 21 do 40, ilość zasobów obliczeniowych można używać, które można użyć w przypadku obciążeń jest X-2.
* Gdy wartość X jest między 41 i 53, ilość zasobów obliczeniowych można używać, które można użyć w przypadku obciążeń jest X-3.

Minimalny rozmiar umożliwia ma 2 serwery frontonu oraz 2 procesy robocze.  Przy użyciu powyższych instrukcji następnie poniżej przedstawiono kilka przykładów, aby wyjaśnić:  

* Jeśli masz 30 pracowników w jednej puli 28 z nich może służyć obciążeń hostów.
* Jeśli masz 2 procesy robocze w jednej puli 1 może służyć obciążeń hostów.
* Jeśli masz 20 pracowników w jednej puli 19 może służyć obciążeń hostów.  
* Jeśli masz 21 pracowników w jednej puli, następnie nadal tylko 19 może służyć do obsługi zadań.  

Ważne jest aspekt odporności na uszkodzenia, ale musisz mieć na uwadze podczas skalowania powyżej określonych wartości progowych. Jeśli chcesz dodać więcej pojemności, przechodząc od 20 wystąpień, następnie przejdź do 22 lub nowszej, ponieważ 21 nie dodaje żadnych większej pojemności. Dotyczy to także przechodzenie ponad 40, gdzie kolejny numer, który dodaje pojemności jest 42.  

## <a name="deleting-an-app-service-environment"></a>Usuwanie środowiska App Service
Jeśli chcesz usunąć środowisko App Service, po prostu użyj **Usuń** akcji w górnej części bloku usługi App Service Environment. Gdy to zrobisz, zostanie wyświetlony monit o wprowadzenie nazwy środowiska App Service, aby upewnić się, że naprawdę chcesz to zrobić. Należy pamiętać o tym, po usunięciu środowiska usługi App Service, usunięcie całej zawartości w nim także.  

![Usuń środowisko App Service Environment interfejsu użytkownika][9]  

## <a name="getting-started"></a>Wprowadzenie
Aby rozpocząć pracę przy użyciu środowisk usługi App Service, zobacz [jak utworzyć środowisko App Service Environment](app-service-web-how-to-create-an-app-service-environment.md).

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
