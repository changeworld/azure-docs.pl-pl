---
title: Zagadnienia dotyczące sieci, za pomocą środowiska usługi App Service — platformy Azure
description: Wyjaśnia, ruch sieciowy środowiska ASE i jak ustawić sieciowe grupy zabezpieczeń i tras zdefiniowanych przez użytkownika za pomocą środowiska ASE
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 73175b326c25d5d9a78155d0d9d888b655da1bfd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61226862"
---
# <a name="networking-considerations-for-an-app-service-environment"></a>Informacje dotyczące sieci środowiska App Service Environment #

## <a name="overview"></a>Przegląd ##

 Azure [środowiska App Service Environment] [ Intro] to wdrożenie usługi Azure App Service w podsieci sieci wirtualnej platformy Azure (VNet). Istnieją dwa typy wdrożenia dla środowiska usługi App Service (ASE):

- **Zewnętrzne środowisko ASE**: Przedstawia aplikacje hostowane środowisko ASE na adres IP dostępne za pośrednictwem Internetu. Aby uzyskać więcej informacji, zobacz [Tworzenie zewnętrznego środowiska ASE][MakeExternalASE].
- **ŚRODOWISKO ASE Z WEWNĘTRZNYM MODUŁEM RÓWNOWAŻENIA OBCIĄŻENIA**: Przedstawia aplikacje hostowane środowisko ASE na adres IP wewnątrz sieci wirtualnej. Wewnętrzny punkt końcowy jest wewnętrzny moduł równoważenia obciążenia (ILB), dlatego jest określana mianem środowisko ASE z wewnętrznym modułem równoważenia obciążenia. Aby uzyskać więcej informacji, zobacz [tworzenia i używania środowiska ASE z wewnętrznym modułem równoważenia obciążenia][MakeILBASE].

Istnieją dwie wersje środowiska App Service Environment: ASEv1 i ASEv2. Aby uzyskać informacji na temat środowiska ASEv1, zobacz [wprowadzenie do środowiska App Service Environment v1][ASEv1Intro]. Środowiska ASEv1 można wdrożyć w klasycznej lub sieci wirtualnej usługi Resource Manager. ASEv2 można wdrożyć tylko w sieci wirtualnej usługi Resource Manager.

Wywołania z ASE, które bardziej szczegółowo w Internecie należy pozostawić sieci wirtualnej przy użyciu adresu VIP przypisanych do środowiska ASE. Publiczny adres IP to adres VIP jest źródłowy adres IP dla wszystkich wywołań ze środowiska ASE, które bardziej szczegółowo w Internecie. Jeśli aplikacje w środowisku ASE wywołań do zasobów w sieci wirtualnej lub sieci VPN, źródłowy adres IP jest jeden z adresów IP w podsieci używana przez środowisko ASE. Ponieważ środowisko ASE znajduje się w sieci wirtualnej, można także przejść zasobów w ramach sieci wirtualnej bez przeprowadzania dodatkowej konfiguracji. Jeśli sieć wirtualna jest połączona z siecią lokalną, aplikacje w środowisku ASE również mieć dostęp do zasobów istnieje bez dodatkowej konfiguracji.

![Zewnętrznego środowiska ASE][1] 

W przypadku zewnętrznego środowiska ASE publicznych adresów VIP jest również punkt końcowy, który aplikacji środowiska ASE, rozwiązania dla:

* HTTP/S. 
* FTP/S. 
* Wdrażanie w Internecie.
* Zdalne debugowanie.

![ŚRODOWISKO ASE Z WEWNĘTRZNYM MODUŁEM RÓWNOWAŻENIA OBCIĄŻENIA][2]

Jeśli masz środowisko ASE z wewnętrznym modułem równoważenia obciążenia, adres wewnętrznego modułu równoważenia obciążenia jest punkt końcowy dla protokołu HTTP/S, FTP/S, wdrażanie w Internecie i zdalne debugowanie.

Dostępne są następujące porty dostępu zwykła aplikacja:

| Użycie | Od | Do |
|----------|---------|-------------|
|  HTTP/HTTPS  | Użytkownika można konfigurować |  80, 443 |
|  FTP/FTPS    | Użytkownika można konfigurować |  21, 990, 10001-10020 |
|  Visual Studio zdalne debugowanie  |  Użytkownika można konfigurować |  4020, 4022, 4024 |
|  Wdrażanie usługi sieci Web | Użytkownika można konfigurować | 8172 |

Ta zasada obowiązuje, jeśli jesteś na zewnętrznym środowiskiem ASE lub w środowisku ASE z wewnętrznym modułem równoważenia obciążenia. Jeśli używasz zewnętrznego środowiska ASE, napotkasz tych portów na publicznych adresów VIP. Jeśli środowisko ASE z wewnętrznym modułem równoważenia obciążenia, napotkasz tych portów na wewnętrznego modułu równoważenia obciążenia. Jeśli port 443 jest zablokowanie, może to być wpływ na niektóre funkcje dostępne w portalu. Aby uzyskać więcej informacji, zobacz [zależności portalu](#portaldep).

## <a name="ase-subnet-size"></a>Rozmiar podsieci środowiska ASE ##

Nie można zmienić rozmiaru podsieci używanej do hostowania środowiska ASE po jego wdrożeniu.  Środowisko ASE używa adresu dla każdej infrastruktury roli oraz jak w przypadku każdego wystąpienia planu izolowanej usługi App Service.  Ponadto są 5 adresów używanych przez sieci platformy Azure dla każdej podsieci, który jest tworzony.  Środowisko ASE z żadnych planów usługi App Service w ogóle będzie używać 12 adresów przed przystąpieniem do tworzenia aplikacji.  Jeśli środowisko ASE z wewnętrznym modułem równoważenia obciążenia jest następnie zostanie użyty 13 adresów przed przystąpieniem do tworzenia aplikacji w tym środowisku ASE. Podczas skalowania środowiska ASE rolami infrastruktury zostaną dodane co wielokrotnością 15 i 20 wystąpień planu usługi App Service.

   > [!NOTE]
   > W tej podsieci, ale środowisko ASE można nic innego. Pamiętaj wybrać przestrzeń adresów, który pozwala na przyszły rozwój. Nie można zmienić tego ustawienia później. Firma Microsoft zaleca rozmiar `/24` za pomocą 256 adresów.

Skalowanie w górę lub w dół, są dodawane nowe role odpowiedni rozmiar, a następnie obciążenia są migrowane z bieżącego rozmiaru do rozmiaru docelowego. Tylko wtedy, gdy aplikacje są migrowane są usuwane oryginalnych maszyn wirtualnych. Oznacza to, że jeśli masz środowisko ASE przy użyciu 100 wystąpień ASP istniała okres, w których należy podwoić liczbę maszyn wirtualnych.  To z tego powodu, które firma Microsoft zaleca korzystanie z "/ 24" Aby uwzględnić zmiany, które mogą być wymagane.  

## <a name="ase-dependencies"></a>Zależności środowiska ASE ##

### <a name="ase-inbound-dependencies"></a>Środowisko ASE zależności dla ruchu przychodzącego ###

Środowisko ASE przychodzące dostępu, które zależności są:

| Użycie | Od | Do |
|-----|------|----|
| Zarządzanie | Adresy zarządzania usługi aplikacji | Podsieci środowiska ASE: 454, 455 |
|  Środowisko ASE wewnętrznej komunikacji | Podsieci środowiska ASE: Wszystkie porty | Podsieci środowiska ASE: Wszystkie porty
|  Zezwalaj na moduł równoważenia obciążenia platformy Azure dla ruchu przychodzącego | Moduł równoważenia obciążenia platformy Azure | Podsieci środowiska ASE: Wszystkie porty
|  Przypisane adresy IP aplikacji | Przypisane adresy aplikacji | Podsieci środowiska ASE: Wszystkie porty

Ruch przychodzący zarządzania zapewnia poleceń i kontroli środowiska ASE, oprócz systemu monitorowania. Adresy źródła dla tego ruchu są wymienione w [adresy zarządzania środowiska ASE] [ ASEManagement] dokumentu. Konfiguracja zabezpieczeń sieci musi zezwolić na dostęp ze wszystkich adresów IP na portach 454 i 455. Jeśli zablokujesz dostęp z tych adresów, Twoje środowisko ASE przestanie złej kondycji i następnie wstrzymane.

W obrębie podsieci środowiska ASE ma wiele porty używane do wewnętrznych składników komunikacji i ich zmienić.  Wymaga to wszystkie porty w podsieci środowiska ASE, które mają być dostępne z podsieci środowiska ASE. 

Do komunikacji między równoważenia obciążenia platformy Azure oraz podsieci środowiska ASE niezbędne porty, które muszą być otwarte są 454 i 455 16001. 16001 port jest używany keep alive ruchu między modułu równoważenia obciążenia i środowisko ASE. Jeśli używasz środowiska ASE z wewnętrznym modułem równoważenia obciążenia, a następnie zablokować ruch w dół, aby po prostu 454, 455, 16001 portów.  Jeśli używasz zewnętrznego środowiska ASE, należy wziąć pod uwagę porty dostępu normalna aplikacja.  Jeśli używasz aplikacji przypisane adresy należy otworzyć ją do wszystkich portów.  Po adres zostanie przypisany do określonej aplikacji, moduł równoważenia obciążenia będzie używać portów, które nie są znane z wyprzedzeniem do wysyłania ruchu HTTP i HTTPS do środowiska ASE.

Jeśli używasz aplikacji przypisanych adresów IP, musisz zezwolić na ruch z adresów IP przypisanych do aplikacji w celu podsieci środowiska ASE.

Ruch TCP, których można użyć w na portach 454 i 455 musi przejść z tego samego adresu VIP lub będzie mieć problemu z routingiem asymetrycznym. 

### <a name="ase-outbound-dependencies"></a>Wychodzące zależności środowiska ASE ###

Na potrzeby dostępu wychodzącego środowisko ASE zależy od wielu systemów zewnętrznych. Wiele z tych zależności systemowe są definiowane przy użyciu nazw DNS i nie mapowania stały zestaw adresów IP. W związku z tym środowisko ASE wymaga dostęp ruchu wychodzącego z podsieci środowiska ASE do wszystkich zewnętrznych adresów IP na różnych portów. 

Pełną listę zależności wychodzące są wymienione w dokumencie, który opisuje [Zablokowanie ruchu wychodzącego środowiska App Service Environment](./firewall-integration.md). Jeśli środowisko ASE utraci dostęp do jego zależności, przestanie działać. Jeśli tak się stanie wystarczająco długi, środowisko ASE jest zawieszone. 

### <a name="customer-dns"></a>Klient DNS ###

Jeśli sieć wirtualna jest skonfigurowany na serwerze DNS przez klienta, obciążenia dzierżaw go użyć. Środowisko ASE jest nadal wymaga do komunikacji z usługą Azure DNS do celów zarządzania. 

Jeśli sieć wirtualna jest skonfigurowana z klientem DNS po drugiej stronie sieci VPN, serwer DNS musi być dostępny z poziomu podsieci zawierającej środowisko ASE.

Aby przetestować rozwiązania z aplikacji sieci web można użyć polecenia konsoli *nameresolver*. Przejdź do okna debugowania witryny funkcji scm aplikacji lub przejdź do aplikacji w portalu i wybierz pozycję Konsola. W wierszu polecenia powłoki można wydać polecenie *nameresolver* oraz adres, który chcesz wyszukać. Wynik, wracając jest taka sama jak aplikacja otrzymamy podczas wprowadzania tych samych wyszukiwania. Jeśli używasz nslookup wykonasz wyszukiwania, zamiast tego używanie usługi Azure DNS.

Jeśli zmienisz ustawienia DNS sieci wirtualnej, należącym do środowiska ASE, konieczne będzie ponowne uruchomienie środowiska ASE. Aby uniknąć ponownego uruchomienia środowiska ASE, zdecydowanie zaleca się skonfigurowania ustawień DNS dla sieci wirtualnej, przed przystąpieniem do tworzenia środowiska ASE.  

<a name="portaldep"></a>

## <a name="portal-dependencies"></a>Zależności portalu ##

Oprócz zależności funkcjonalne środowisko ASE istnieje kilka dodatkowych elementów związane z środowisko pracy w portalu. Niektóre funkcje w witrynie Azure portal są zależne od bezpośredni dostęp do _witryny SCM_. Istnieją dwa adresy URL, dla każdej aplikacji w usłudze Azure App Service. Pierwszy adres URL jest dostęp do aplikacji. Drugi adres URL jest dostęp do witryny SCM, nazywana także _konsoli Kudu_. Funkcje, które używają witryny SCM:

-   Zadania Web Job
-   Funkcje
-   Przesyłanie strumieniowe dzienników
-   Kudu
-   Rozszerzenia
-   Eksplorator procesów
-   Konsola

Gdy używasz środowisko ASE z wewnętrznym modułem równoważenia obciążenia, witryny SCM jest internet dostępne spoza sieci wirtualnej. Gdy aplikacja jest hostowana na środowisko ASE z wewnętrznym modułem równoważenia obciążenia, niektóre funkcje nie będą działać z poziomu portalu.  

Wiele z tych funkcji, które zależą od witryny SCM są również dostępne bezpośrednio w konsoli programu Kudu. Można podłączyć do niego bezpośrednio, a nie przy użyciu portalu. Jeśli aplikacja jest hostowana w środowisku ASE z wewnętrznym modułem równoważenia obciążenia, należy używać poświadczeń publikowania do logowania. Adres URL, aby przejść do witryny SCM aplikacji hostowanej w środowisku ASE z wewnętrznym modułem równoważenia obciążenia ma następujący format: 

```
<appname>.scm.<domain name the ILB ASE was created with> 
```

Jeśli nazwa domeny środowiska ASE wewnętrznego modułu równoważenia obciążenia *"contoso.NET"* i Twoja nazwa aplikacji jest *testapp*, aplikacja zostanie osiągnięty w *testapp.contoso.net*. Witryny SCM, która łączy się z jej zostanie osiągnięty w *testapp.scm.contoso.net*.

## <a name="functions-and-web-jobs"></a>Funkcje i zadania Web Job ##

Zarówno funkcje, jak i sieci Web zadań są zależne od witryny SCM, ale są obsługiwane do użytku w portalu, nawet jeśli Twoje aplikacje są w środowisku ASE wewnętrznego modułu równoważenia obciążenia, tak długo, jak przeglądarka może dotrzeć do witryny SCM.  Jeśli używasz certyfikatu z podpisem własnym za pomocą środowiska ASE wewnętrznego modułu równoważenia obciążenia, należy włączyć przeglądarkę, aby ufać temu certyfikatowi.  Dla programu Internet Explorer i Microsoft Edge, która oznacza, że certyfikat musi znajdować się w magazynie komputera zaufania.  Jeśli używasz przeglądarki Chrome, a następnie oznacza to, że wcześniej zaakceptowane certyfikatu w przeglądarce, prawdopodobnie bezpośrednio naciskając witryny scm.  Najlepszym rozwiązaniem jest Użyj komercyjnego certyfikatu obecnego w łańcuchu zaufania przeglądarki.  

## <a name="ase-ip-addresses"></a>Adresy IP środowiska ASE ##

Środowisko ASE ma kilka adresów IP, pod uwagę. Oto one:

- **Publiczny adres IP dla ruchu przychodzącego**: Używany do ruchu aplikacji w zewnętrznym środowiskiem ASE i ruch związany z zarządzaniem w zewnętrznym środowiskiem ASE i środowisko ASE z wewnętrznym modułem równoważenia obciążenia.
- **Wychodzące publiczny adres IP**: Używane jako "od" adres IP dla połączeń wychodzących ze środowiska ASE, które należy pozostawić sieci wirtualnej, które nie są kierowane szczegółów sieci VPN.
- **Adres IP wewnętrznego modułu równoważenia obciążenia**: Jeśli używasz środowiska ASE z wewnętrznym modułem równoważenia obciążenia.
- **Aplikacja przypisana SSL opartego na protokole IP adresów**: Możliwe tylko za pomocą zewnętrznego środowiska ASE i po skonfigurowaniu SSL opartego na protokole IP.

Te adresy IP są łatwo widoczna w ASEv2 w witrynie Azure portal z poziomu interfejsu użytkownika środowiska ASE. Jeśli masz środowisko ASE z wewnętrznym modułem równoważenia obciążenia, znajduje się adres IP dla wewnętrznego modułu równoważenia obciążenia.

   > [!NOTE]
   > Te adresy IP nie ulegnie zmianie, tak długo, jak środowisko ASE pozostaje uruchomiona.  Jeśli środowisko ASE staje się wstrzymane, a następnie przywrócić, adresy, używane przez środowisko ASE zostanie zmieniona. Normalne przyczynę wstrzymane środowisko ASE wewnętrznego jest możesz zablokować dostęp do funkcji zarządzania dla ruchu przychodzącego lub zablokować dostęp do zależności środowiska ASE. 

![Adresy IP][3]

### <a name="app-assigned-ip-addresses"></a>Adresy IP przypisane do aplikacji ###

Za pomocą zewnętrznego środowiska ASE adresów IP można przypisać do poszczególnych aplikacji. Nie możesz tego zrobić za pomocą środowisko ASE z wewnętrznym modułem równoważenia obciążenia. Aby uzyskać więcej informacji na temat konfigurowania aplikacji, aby mieć swój własny adres IP, zobacz [powiązania istniejącego niestandardowego certyfikatu SSL w usłudze Azure App Service](../app-service-web-tutorial-custom-ssl.md).

Gdy aplikacja ma własny adres SSL opartego na protokole IP, środowisko ASE zastrzega sobie dwa porty do mapowania na ten adres IP. Jeden port jest dla ruchu HTTP i innych portów to dla protokołu HTTPS. Te porty są wyświetlane w interfejsie użytkownika środowiska ASE w sekcji adresów IP. Ruch musi być w stanie nawiązać połączenie z adresu VIP tych portów lub aplikacje są niedostępne. Ten wymóg jest pamiętać podczas konfigurowania sieciowych grup zabezpieczeń (NSG).

## <a name="network-security-groups"></a>Grupy zabezpieczeń sieci ##

[Sieciowe grupy zabezpieczeń] [ NSGs] zapewniają możliwość kontrolowania dostępu do sieci w sieci wirtualnej. Korzystając z portalu, ma regułę Odmów niejawne pod najniższy priorytet, Odmów wszystkiego. Możesz tworzyć są z reguły zezwalania.

W przypadku środowiska ASE nie masz dostępu do maszyn wirtualnych, używane do hostowania środowiska ASE, sam. Są one w ramach subskrypcji zarządzanych przez firmę Microsoft. Jeśli chcesz ograniczyć dostęp do aplikacji w środowisku ASE, należy ustawić sieciowych grup zabezpieczeń w podsieci środowiska ASE. W ten sposób zapłaty uważnego zależności środowiska ASE. Jeśli zablokujesz jakieś zależności, środowisko ASE przestanie działać.

Sieciowe grupy zabezpieczeń można skonfigurować za pomocą witryny Azure portal lub za pośrednictwem programu PowerShell. W tym miejscu informacje przedstawia witryny Azure portal. Tworzenie i zarządzanie sieciowymi grupami zabezpieczeń w portalu jako zasobem najwyższego poziomu w ramach **sieć**.

Gdy wymagania dotyczące ruchu przychodzącego i wychodzącego są brane pod uwagę, sieciowe grupy zabezpieczeń powinien wyglądać podobnie do sieciowych grup zabezpieczeń, w poniższym przykładzie. Zakres adresów sieci wirtualnej jest _192.168.250.0/23_, i podsieci, która znajduje się w środowisku ASE jest _192.168.251.128/25_.

Pierwsze dwa wymagania dla ruchu przychodzącego dla środowiska ASE funkcji są wyświetlane u góry listy, w tym przykładzie. One włączyć zarządzanie środowiska ASE i umożliwić ASE do komunikowania się z samym sobą. Inne wpisy wszystkich dzierżawców można konfigurować i mogą kontrolować dostęp sieciowy do aplikacje hostowane w środowisku ASE. 

![Reguły zabezpieczeń dla ruchu przychodzącego][4]

Domyślna reguła umożliwia adresów IP w sieci wirtualnej na komunikowanie się z podsieci środowiska ASE. Inną regułę domyślną umożliwia równoważenia obciążenia, znany także jako publicznych adresów VIP do komunikowania się z ASE. Aby wyświetlić domyślne reguły, wybierz **domyślne reguły** obok **Dodaj** ikony. Jeśli umieścisz Odmów, wszystkie inne reguły po pokazano reguły sieciowej grupy zabezpieczeń, uniemożliwia ruchu między adres VIP i środowisko ASE. Aby zapobiec ruchu przychodzącego z wewnątrz sieci wirtualnej, należy dodać własne reguły w celu umożliwienia ruchu przychodzącego. Korzystanie ze źródła, które są równe AzureLoadBalancer z miejsca docelowego **wszelkie** i zakres portów **\***. Ponieważ reguła sieciowej grupy zabezpieczeń jest stosowana do podsieci środowiska ASE, trzeba należeć do określonego miejsca docelowego.

Jeśli adres IP przypisany do aplikacji, upewnij się, że porty pozostawić otwarty. Aby zobaczyć porty, wybierz **środowiska App Service Environment** > **adresów IP**.  

Wszystkie elementy, które są wyświetlane w następujących reguł ruchu wychodzącego są potrzebne, z wyjątkiem ostatniego elementu. Umożliwiają one dostęp sieciowy do zależności środowiska ASE, które zostały podane wcześniej w tym artykule. Jeśli zablokujesz któryś z nich Twoje środowisko ASE przestanie działać. Ostatni element na liście umożliwia środowiska ASE do komunikowania się z innymi zasobami w sieci wirtualnej.

![Reguły zabezpieczeń dla ruchu wychodzącego][5]

Po zdefiniowaniu sieciowych grup zabezpieczeń, należy je przypisać do podsieci, która znajduje się w środowisku ASE. Jeśli nie pamiętasz środowiska ASE w sieci wirtualnej lub podsieci, można go było wyświetlić na stronie portalu środowiska ASE. Aby przypisać sieciowej grupy zabezpieczeń do podsieci, przejdź do podsieci interfejsu użytkownika, a następnie wybierz sieciową grupę zabezpieczeń.

## <a name="routes"></a>Trasy ##

Wymuszone tunelowanie ma miejsce podczas skonfigurować trasy w sieci wirtualnej, dzięki czemu ruch wychodzący nie przejdź bezpośrednio do Internetu, ale gdzie indziej, takie jak brama usługi ExpressRoute lub urządzenie wirtualne.  Jeśli chcesz skonfigurować środowisko ASE w taki sposób, a następnie odczytać dokument na [Konfigurowanie środowiska App Service Environment przy wymuszonego tunelowania][forcedtunnel].  W tym dokumencie poinformuje, dostępne opcje Aby pracować z usługi ExpressRoute i wymuszonym tunelowaniem.

Po utworzeniu środowiska ASE w witrynie portal musimy również utworzyć zbiór tabel tras w podsieci, która jest tworzona przy użyciu środowiska ASE.  Te trasy po prostu, że na wysyłanie ruchu wychodzącego bezpośrednio do Internetu.  
Aby ręcznie utworzyć ten sam tras, wykonaj następujące kroki:

1. Przejdź do witryny Azure Portal. Wybierz **sieć** > **tabele tras**.

2. Utwórz nową tabelę tras w tym samym regionie, co sieć wirtualna.

3. W ramach tabeli tras interfejsu użytkownika, zaznacz **trasy** > **Dodaj**.

4. Ustaw **typu następnego przeskoku** do **Internet** i **prefiksu adresu** do **0.0.0.0/0**. Wybierz pozycję **Zapisz**.

    Zostanie wyświetlony podobny do poniższego:

    ![Trasy funkcjonalności][6]

5. Po utworzeniu nowej tabeli tras, przejdź do podsieci, która zawiera środowiska ASE. Wybieranie tabeli tras z listy w portalu. Po zapisaniu zmian, następnie powinny sieciowe grupy zabezpieczeń i tras oznaczane przy użyciu podsieci.

    ![Sieciowe grupy zabezpieczeń i trasy][7]

## <a name="service-endpoints"></a>Punkty końcowe usługi ##

Punkty końcowe usługi pozwalają ograniczyć dostęp do usług wielodostępnych do zestawu sieci wirtualnych i podsieci platformy Azure. Więcej informacji na temat punktów końcowych usługi zawiera dokumentacja [punktów końcowych usługi dla sieci wirtualnej][serviceendpoints]. 

Po włączeniu punktów końcowych usługi w zasobie niektóre trasy są utworzone z wyższym priorytetem niż wszystkie inne trasy. Jeśli używasz punktów końcowych usługi w środowisku ASE z wymuszonym tunelowaniem, ruch związany z zarządzaniem usługami Azure SQL i Azure Storage nie będzie tunelowany w sposób wymuszony. 

Kiedy punkty końcowe usługi są włączone w podsieci z wystąpieniem usługi Azure SQL, wszystkie wystąpienia usługi Azure SQL połączone z tej podsieci muszą mieć włączone punkty końcowe usługi. Jeśli chcesz uzyskać dostęp do wielu wystąpień usługi Azure SQL z tej samej podsieci, musisz włączyć punkty końcowe usługi na każdym wystąpieniu usługi Azure SQL. Usługa Azure Storage nie zachowuje się tak samo jak usługa Azure SQL. Włączenie punktów końcowych usługi w usłudze Azure Storage powoduje zablokowanie dostępu do tego zasobu z podsieci, ale można nadal uzyskiwać dostęp do innych kont usługi Azure Storage, nawet jeśli nie mają włączonych punktów końcowych usługi.  

![Punkty końcowe usługi][8]

<!--Image references-->
[1]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow.png
[2]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow2.png
[3]: ./media/network_considerations_with_an_app_service_environment/networkase-ipaddresses.png
[4]: ./media/network_considerations_with_an_app_service_environment/networkase-inboundnsg.png
[5]: ./media/network_considerations_with_an_app_service_environment/networkase-outboundnsg.png
[6]: ./media/network_considerations_with_an_app_service_environment/networkase-udr.png
[7]: ./media/network_considerations_with_an_app_service_environment/networkase-subnet.png
[8]: ./media/network_considerations_with_an_app_service_environment/serviceendpoint.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ASEManagement]: ./management-addresses.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[forcedtunnel]: ./forced-tunnel-support.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
