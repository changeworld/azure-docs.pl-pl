---
title: Tworzenie niestandardowej sondy przy użyciu portalu
titleSuffix: Azure Application Gateway
description: Dowiedz się, jak utworzyć niestandardową sondę dla bramy aplikacji przy użyciu portalu
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 15daf47a1cb44635932311e60b3690af9ff58677
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74074605"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Tworzenie niestandardowej sondy dla bramy aplikacji przy użyciu portalu

> [!div class="op_single_selector"]
> * [Portal Azure](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager — program PowerShell](application-gateway-create-probe-ps.md)
> * [Klasyczny portal Azure — program PowerShell](application-gateway-create-probe-classic-ps.md)

W tym artykule dodasz niestandardową sondę kondycji do istniejącej bramy aplikacji za pośrednictwem witryny Azure portal. Za pomocą sond kondycji usługi Azure Application Gateway monitoruje kondycję zasobów w puli zaplecza.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Jeśli nie masz jeszcze bramy aplikacji, odwiedź stronę [Utwórz bramę aplikacji,](application-gateway-create-gateway-portal.md) aby utworzyć bramę aplikacji do pracy.

## <a name="create-probe-for-application-gateway-v2-sku"></a>Tworzenie sondy dla jednostki SKU bramy aplikacji w wersji 2

Sondy są konfigurowane w procesie dwuetapowym za pośrednictwem portalu. Pierwszym krokiem jest wprowadzenie wartości wymaganych dla konfiguracji sondy. W drugim kroku należy przetestować kondycję wewnętrznej bazy danych przy użyciu tej konfiguracji sondy i zapisać sondy. 

### <a name="enter-probe-properties"></a><a name="createprobe"></a>Wprowadź właściwości sondy

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Jeśli nie masz jeszcze konta, możesz zarejestrować się w celu [bezpłatnego miesięcznego okresu próbnego](https://azure.microsoft.com/free)

2. W okienku Ulubione witryny Azure Portal kliknij pozycję Wszystkie zasoby. Kliknij bramę aplikacji w bloku Wszystkie zasoby. Jeśli wybrana subskrypcja zawiera kilka zasobów, możesz wpisać partners.contoso.net w polu Filtruj według nazwy..., aby łatwo uzyskać dostęp do bramy aplikacji.

3. Wybierz **sondy kondycji,** a następnie wybierz pozycję **Dodaj,** aby dodać nową sondę kondycji.

   ![Dodaj nową sondę][4]

4. Na stronie **Dodaj sondę kondycji** wypełnij wymagane informacje dla sondy, a po zakończeniu wybierz **przycisk OK**.

   |**Ustawienie** | **Wartość** | **Szczegóły**|
   |---|---|---|
   |**Nazwa**|customProbe (niestandardowy)|Ta wartość jest przyjazną nazwą nadaną sondy, która jest dostępna w portalu.|
   |**Protokół**|HTTP lub HTTPS | Protokół używany przez sondę kondycji. |
   |**Host**|Tj contoso.com|Ta wartość jest nazwą hosta wirtualnego (innego niż nazwa hosta maszyny Wirtualnej) uruchomionego na serwerze aplikacji. Sonda jest wysyłana do (protocol)://(host name):(port from httpsetting)/urlPath.  Ma to zastosowanie, gdy wiele lokacji jest skonfigurowany na bramie aplikacji. Jeśli brama aplikacji jest skonfigurowana dla pojedynczej lokacji, wprowadź "127.0.0.1".|
   |**Wybieranie nazwy hosta z ustawień HTTP wewnętrznej bazy danych**|Tak lub Nie|Ustawia nagłówek *hosta* w sondzie na nazwę hosta zasobu zaplecza w puli zaplecza skojarzonej z ustawieniem HTTP, z którym jest skojarzona ta sonda. Szczególnie wymagane w przypadku zaplecza wielu dzierżawców, takich jak usługa aplikacji platformy Azure. [Dowiedz się więcej](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address)|
   |**Ścieżka**|/ lub inna ścieżka|Pozostała część pełnego adresu URL dla niestandardowej sondy. Prawidłowa ścieżka zaczyna się od "/". Dla domyślnej ścieżki\/http: /contoso.com po prostu użyj '/' |
   |**Interwał (sek.)**|30|Jak często sonda jest uruchamiana w celu sprawdzenia kondycji. Nie zaleca się ustawiania niższej niż 30 sekund.|
   |**Limit czasu (s)**|30|Czas oczekiwania sondy przed przesuniem czasu. Jeśli prawidłowa odpowiedź nie zostanie odebrana w tym okresie, sonda jest oznaczona jako nieudana. Interwał limitu czasu musi być na tyle wysoki, że można wywołać http, aby upewnić się, że strona kondycji wewnętrznej bazy danych jest dostępna. Należy zauważyć, że wartość limitu czasu nie powinna być większa niż wartość "Interwał" używana w tym ustawieniu sondy lub wartość limitu czasu żądania w ustawieniu HTTP, która będzie skojarzona z tą sondą.|
|**Niezdrowy próg**|3|Liczba kolejnych nieudanych prób należy uznać za złej kondycji. Próg można ustawić na 1 lub więcej.|
   |**Użyj warunków dopasowywania sondy**|Tak lub Nie|Domyślnie odpowiedź HTTP(S) z kodem stanu między 200 a 399 jest uważana za w dobrej kondycji. Można zmienić dopuszczalny zakres kodu odpowiedzi wewnętrznej bazy danych lub treści odpowiedzi wewnętrznej bazy danych. [Dowiedz się więcej](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)|
   |**Ustawienia HTTP**|wybór z listy rozwijanej|Sonda zostanie skojarzona z wybranymi w tym miejscu ustawieniami HTTP i w związku z tym będzie monitorować kondycję tej puli zaplecza skojarzonej z wybranym ustawieniem HTTP. Użyje tego samego portu dla żądania sondy, co używany w wybranym ustawieniu HTTP. Można wybrać tylko te ustawienia HTTP, które nie są skojarzone z żadną inną sondą niestandardową. <br>Należy zauważyć, że tylko te ustawienia HTTP są dostępne dla skojarzeń, które mają ten sam protokół co protokół wybrany w tej konfiguracji sondy i mają ten sam stan dla *przełącznika ustawienia HTTP nazwy hosta pobrania z zaplecza.*|
   
   > [!IMPORTANT]
   > Sonda będzie monitorować kondycję wewnętrznej bazy danych tylko wtedy, gdy jest skojarzona z co najmniej jednym ustawieniem HTTP. Będzie monitorować zasoby zaplecza tych pul zaplecza, które są skojarzone z ustawieniami HTTP, z którymi jest skojarzona ta sonda. Żądanie sondy zostanie wysłane do http://(nazwa hosta):(port z httpsetting)/urlPath.

### <a name="test-backend-health-with-the-probe"></a>Testowanie kondycji zaplecza za pomocą sondy

Po wprowadzeniu właściwości sondy można przetestować kondycję zasobów zaplecza, aby sprawdzić, czy konfiguracja sondy jest poprawna i czy zasoby zaplecza działają zgodnie z oczekiwaniami.

1. Wybierz **opcję Testuj** i zanotuj wynik sondy. Brama aplikacji testuje kondycję wszystkich zasobów wewnętrznej bazy danych w pulach wewnętrznej bazy danych skojarzonych z ustawieniami HTTP używanymi dla tej sondy. 

   ![Test kondycji zaplecza][5]

2. Jeśli istnieją jakieś złej kondycji zasobów wewnętrznej bazy danych, a następnie sprawdź **Szczegóły** kolumny, aby zrozumieć przyczynę złej kondycji stanu zasobu. Jeśli zasób został oznaczony w złej kondycji z powodu niepoprawnej konfiguracji sondy, wybierz łącze **Wróć do sondy** i edytuj konfigurację sondy. W przeciwnym razie jeśli zasób został oznaczony w złej kondycji z powodu problemu z wewnętrznej bazy danych, a następnie rozwiązać problemy z zasobem wewnętrznej bazy danych, a następnie przetestować wewnętrznej bazy danych ponownie, wybierając **Wróć do sondy** łącza i wybierz **test**.

   > [!NOTE]
   > Można zapisać sondę nawet w złej kondycji zasobów wewnętrznej bazy danych, ale nie jest zalecane. Dzieje się tak, ponieważ brama aplikacji usuwa te zasoby wewnętrznej bazy danych z puli wewnętrznej bazy danych, które są określane jako w złej kondycji przez sondę. W przypadku braku żadnych zdrowych zasobów w puli wewnętrznej bazy danych, nie będzie można uzyskać dostępu do aplikacji i otrzymasz błąd 502.

   ![Wyświetl wynik sondy][6]

3. Wybierz **dodaj,** aby zapisać sondę. 

## <a name="create-probe-for-application-gateway-v1-sku"></a>Tworzenie sondy dla jednostki SKU bramy aplikacji w wersji 1

Sondy są konfigurowane w procesie dwuetapowym za pośrednictwem portalu. Pierwszym krokiem jest utworzenie sondy. W drugim kroku należy dodać sondę do wewnętrznej bazy danych http ustawienia bramy aplikacji.

### <a name="create-the-probe"></a><a name="createprobe"></a>Tworzenie sondy

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Jeśli nie masz jeszcze konta, możesz zarejestrować się w celu [bezpłatnego miesięcznego okresu próbnego](https://azure.microsoft.com/free)

2. W okienku Ulubione witryny Azure Portal wybierz pozycję **Wszystkie zasoby**. Wybierz bramę aplikacji na stronie **Wszystkie zasoby.** Jeśli wybrana subskrypcja zawiera kilka zasobów, możesz wpisać partners.contoso.net w polu Filtruj według nazwy..., aby łatwo uzyskać dostęp do bramy aplikacji.

3. Wybierz **sondy,** a następnie wybierz pozycję **Dodaj,** aby dodać sondę.

   ![Dodaj ostrze sondy z wypełnionymi informacjami][1]

4. Na bloku **Dodaj sondę kondycji** wypełnij wymagane informacje dla sondy, a po zakończeniu wybierz **przycisk OK**.

   |**Ustawienie** | **Wartość** | **Szczegóły**|
   |---|---|---|
   |**Nazwa**|customProbe (niestandardowy)|Ta wartość jest przyjazną nazwą nadaną sondy, która jest dostępna w portalu.|
   |**Protokół**|HTTP lub HTTPS | Protokół używany przez sondę kondycji. |
   |**Host**|Tj contoso.com|Ta wartość jest nazwą hosta wirtualnego (innego niż nazwa hosta maszyny Wirtualnej) uruchomionego na serwerze aplikacji. Sonda jest wysyłana do (protocol)://(host name):(port from httpsetting)/urlPath.  Ma to zastosowanie, gdy wiele lokacji jest skonfigurowany na bramie aplikacji. Jeśli brama aplikacji jest skonfigurowana dla pojedynczej lokacji, wprowadź "127.0.0.1".|
   |**Wybieranie nazwy hosta z ustawień HTTP wewnętrznej bazy danych**|Tak lub Nie|Ustawia nagłówek *hosta* w sondzie na nazwę hosta zasobu zaplecza w puli zaplecza skojarzonej z ustawieniem HTTP, z którym jest skojarzona ta sonda. Szczególnie wymagane w przypadku zaplecza wielu dzierżawców, takich jak usługa aplikacji platformy Azure. [Dowiedz się więcej](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address)|
   |**Ścieżka**|/ lub inna ścieżka|Pozostała część pełnego adresu URL dla niestandardowej sondy. Prawidłowa ścieżka zaczyna się od "/". Dla domyślnej ścieżki\/http: /contoso.com po prostu użyj '/' |
   |**Interwał (sek.)**|30|Jak często sonda jest uruchamiana w celu sprawdzenia kondycji. Nie zaleca się ustawiania niższej niż 30 sekund.|
   |**Limit czasu (s)**|30|Czas oczekiwania sondy przed przesuniem czasu. Jeśli prawidłowa odpowiedź nie zostanie odebrana w tym okresie, sonda jest oznaczona jako nieudana. Interwał limitu czasu musi być na tyle wysoki, że można wywołać http, aby upewnić się, że strona kondycji wewnętrznej bazy danych jest dostępna. Należy zauważyć, że wartość limitu czasu nie powinna być większa niż wartość "Interwał" używana w tym ustawieniu sondy lub wartość limitu czasu żądania w ustawieniu HTTP, która będzie skojarzona z tą sondą.|
|**Niezdrowy próg**|3|Liczba kolejnych nieudanych prób należy uznać za złej kondycji. Próg można ustawić na 1 lub więcej.|
   |**Użyj warunków dopasowywania sondy**|Tak lub Nie|Domyślnie odpowiedź HTTP(S) z kodem stanu między 200 a 399 jest uważana za w dobrej kondycji. Można zmienić dopuszczalny zakres kodu odpowiedzi wewnętrznej bazy danych lub treści odpowiedzi wewnętrznej bazy danych. [Dowiedz się więcej](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)|

   > [!IMPORTANT]
   > Nazwa hosta nie jest taka sama jak nazwa serwera. Ta wartość jest nazwą hosta wirtualnego uruchomionego na serwerze aplikacji. Sonda jest wysyłana do http://(nazwa hosta):(port z httpsetting)/urlPath

### <a name="add-probe-to-the-gateway"></a>Dodawanie sondy do bramy

Teraz, gdy sonda została utworzona, nadszedł czas, aby dodać go do bramy. Ustawienia sondy są ustawiane w ustawieniach http wewnętrznej bazy danych bramy aplikacji.

1. Kliknij **ustawienia HTTP** na bramie aplikacji, aby przywołać blok konfiguracji, kliknij bieżące ustawienia http wewnętrznej bazy danych wymienione w oknie.

   ![okno ustawień https][2]

2. Na stronie ustawień **appGatewayBackEndHttpSettings** zaznacz pole wyboru **Użyj niestandardowej sondy** i wybierz sondę utworzoną w sekcji [Utwórz sondę](#createprobe) na z listy rozwijanej **Sonda niestandardowa.**
   Po zakończeniu kliknij przycisk **Zapisz** i zostaną zastosowane ustawienia.

## <a name="next-steps"></a>Następne kroki

Służy do wyświetlania kondycji zasobów wewnętrznej bazy danych określonych przez sondę przy użyciu [widoku kondycji wewnętrznej bazy danych](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health).

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png
[6]: ./media/application-gateway-create-probe-portal/figure6.png
