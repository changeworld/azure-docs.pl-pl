---
title: Integracja usługi Azure Functions z siecią wirtualną platformy Azure
description: Samouczek krok po kroku, pokazujący sposób nawiązywania połączeń z siecią wirtualną platformy Azure — funkcja
services: functions
author: alexkarcher-msft
manager: jehollan
ms.service: azure-functions
ms.topic: article
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 96ab479d3373eb6e575a00898f7007a4df252e39
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60002798"
---
# <a name="integrate-a-function-app-with-an-azure-virtual-network"></a>Integracja z aplikacją funkcji z siecią wirtualną platformy Azure

Ten samouczek pokazuje, jak łączyć się z zasobami w sieci wirtualnej platformy Azure za pomocą usługi Azure Functions.

W tym samouczku wdrożymy witrynę WordPress na maszynie Wirtualnej w sieci wirtualnej, który nie jest dostępny z Internetu. Następnie wdrożymy funkcji z dostępem do Internetu i sieci wirtualnej. Użyjemy tej funkcji można uzyskać dostęp do zasobów w witrynie WordPress wdrożonych w ramach sieci wirtualnej.

Aby uzyskać więcej informacji na temat działania systemu Rozwiązywanie problemów i zaawansowanej konfiguracji, zobacz [Integrowanie aplikacji z siecią wirtualną platformy Azure](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet). Usługa Azure functions w planie Premium mają takie same możliwości hostingu, jak aplikacje sieci web, więc wszystkie funkcje i ograniczenia, w tym artykule mają zastosowanie do funkcji.

## <a name="topology"></a>Topologia

 ![Interfejs użytkownika dla Integracja sieci wirtualnej][1]

## <a name="create-a-vm-inside-a-virtual-network"></a>Tworzenie maszyny Wirtualnej w sieci wirtualnej

Aby rozpocząć, utworzymy wstępnie skonfigurowane maszyny Wirtualne z systemem WordPress wewnątrz sieci wirtualnej. 

Wybraliśmy WordPress na maszynie Wirtualnej, ponieważ jest to jeden z co najmniej drogich zasobów, które mogą być wdrażane w sieci wirtualnej. Należy pamiętać, że ten scenariusz może również współpracować z dowolnego zasobu w sieci wirtualnej, takich jak interfejsy API REST, środowisk usługi App Service i innymi usługami platformy Azure.

1. Przejdź do witryny Azure Portal.
2. Dodaj nowy zasób, otwierając **Utwórz zasób** bloku.
3. Wyszukaj "[WordPress LEMP7 Maksymalna wydajność CentOS](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure)", a następnie otwórz blok jego tworzenia. 
4. Na **podstawy** skonfiguruj maszynę Wirtualną z następującymi informacjami:
    1. Utwórz nową grupę zasobów dla tej maszyny Wirtualnej upewnić się, oczyszczanie zasobów ułatwia na końcu tego samouczka. W tym miejscu używamy "Funkcja-sieć wirtualna-Tutorial" jako przykład.
    1. Nadaj maszyn wirtualnych unikatową nazwę. Używamy "Sieci Wirtualnej Wordpress" jako przykład.
    1. Wybierz region najbliżej Ciebie.
    1. Wybierz rozmiar jako B1s (1 vCPU, 1 GB pamięci).
    1. Dla konta administratora wybierz Uwierzytelnianie przy użyciu hasła, a następnie wprowadź unikatową nazwę użytkownika i hasło. Na potrzeby tego samouczka nie trzeba zalogować się do maszyny Wirtualnej, chyba że potrzebujesz rozwiązać problem.
    
        ![Karta podstawy do tworzenia maszyny Wirtualnej](./media/functions-create-vnet/create-vm-1.png)

1. Przenieś do **sieć** kartę, a następnie wprowadź następujące informacje:
    1.  Utwórz nową sieć wirtualną.
    1.  Wprowadź zakres adresów prywatnych i podsieci w ramach tego zakresu adresów. Rozmiar podsieci określi, ile maszyn wirtualnych, można użyć w ramach planu usługi App Service. Jeśli jesteś nowym użytkownikiem, adresy IP oraz wysyłanie ma [dokument, który omawia postawy](https://support.microsoft.com/en-us/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Adresowanie IP i podsieci są ważne w tym scenariuszu, więc zaleca się kilka przeczytania i obejrzyj kilka filmów w trybie online, dopóki dobrym pomysłem. 
    
        W tym przykładzie firma Microsoft jest optują za korzystaniem z sieci 10.10.0.0/16 z podsiecią 10.10.1.0/24. Firma Microsoft celi i przy użyciu /16 podsieci jest łatwy do obliczenia, które podsieci są dostępne w sieci 10.10.0.0/16.
        
        <img src="./media/functions-create-vnet/create-vm-2.png" width="700">

1. Po powrocie **sieć** karcie należy ustawić publicznego adresu IP **Brak**. W tym kroku zostanie wdrożona maszyny Wirtualnej z dostępem do sieci wirtualnej.
       
    <img src="./media/functions-create-vnet/create-vm-2-1.png" width="700">

7. Tworzenie maszyny Wirtualnej. Proces potrwa około 5 minut.
8. Po utworzeniu maszyny Wirtualnej, należy przejść do jego **sieć** kartę i zwróć uwagę na prywatny adres IP, na później. Maszyna wirtualna nie powinna mieć publiczny adres IP.

    ![14]

Masz teraz witrynę WordPress wdrażane wyłącznie w ramach sieci wirtualnej. Ta witryna nie jest dostępne z publicznej sieci internet.

## <a name="create-a-function-app-in-a-premium-plan"></a>Tworzenie aplikacji funkcji w planie Premium

Następnym krokiem jest tworzenie aplikacji funkcji w planie Premium. Plan w warstwie Premium oferuje skalę bez użycia serwera z wszystkich zalet dedykowanego planu usługi App Service. Funkcja aplikacji utworzonych za pomocą planu zużycie nie obsługują integracji sieci wirtualnej.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

## <a name="connect-your-function-app-to-your-virtual-network"></a>Połącz swoją aplikację funkcji do sieci wirtualnej

Z witryną programu WordPress zawierających pliki z w ramach sieci wirtualnej zapewnia teraz możliwość połączenia aplikacji funkcji do sieci wirtualnej.

1.  W portalu dla aplikacji funkcji w poprzednim kroku wybierz **funkcje platformy**. Następnie wybierz pozycję **sieć**.

    <img src="./media/functions-create-vnet/networking-0.png" width="850">

1.  Wybierz **kliknij tutaj, aby skonfigurować** w obszarze **Integracja z siecią wirtualną**.

    ![Stan do konfigurowania funkcji sieci](./media/functions-create-vnet/Networking-1.png)

1. Na stronie integracji sieci wirtualnej wybierz **Dodaj sieć wirtualna (wersja zapoznawcza)**.

    <img src="./media/functions-create-vnet/networking-2.png" width="600"> 
    
1.  Utwórz nową podsieć dla funkcji i plan usługi App Service do użycia. Należy pamiętać, że rozmiar podsieci ograniczy łączna liczba maszyn wirtualnych, które można dodać do planu usługi App Service. Sieci wirtualnej będzie automatycznie kierować ruchem między podsieciami w sieci wirtualnej, dzięki czemu nie ma znaczenia, czy funkcja jest w innej podsieci z maszyny Wirtualnej. 
    
    <img src="./media/functions-create-vnet/networking-3.png" width="600">

## <a name="create-a-function-that-accesses-a-resource-in-your-virtual-network"></a>Tworzenie funkcji, który uzyskuje dostęp do zasobów w sieci wirtualnej

Aplikacja funkcji może teraz uzyskiwać dostęp do sieci wirtualnej z naszą witrynę WordPress. Będziemy więc należy użyć funkcji dostępu do tego pliku i udostępnij go użytkownikowi. W tym przykładzie użyjemy witryny WordPress jako serwer proxy i interfejsu API jako funkcja wywołująca ponieważ są one można łatwo skonfigurować i wizualizacji. 

Równie łatwo można użyć innego interfejsu API wdrożonego w ramach sieci wirtualnej. Umożliwia także innej funkcji z kodem, który sprawia, że wywołania interfejsu API z interfejsem API wdrożonego w ramach sieci wirtualnej. Wystąpienie programu SQL Server wdrożonego w ramach sieci wirtualnej to idealny przykład.

1. W portalu Otwórz aplikację funkcji z poprzedniego kroku.
1. Tworzenie serwera proxy, wybierając **proxy** > **+**.

    <img src="./media/functions-create-vnet/new-proxy.png" width="250">

1. Skonfiguruj nazwę serwera proxy i trasy. W tym przykładzie używa "/ zakładu" jako ścieżka.
1. Wypełnij IP witryny WordPress z wcześniej, a następnie ustaw **URL wewnętrznej bazy danych** do `http://{YOUR VM IP}/wp-content/themes/twentyseventeen/assets/images/header.jpg`
    
    <img src="./media/functions-create-vnet/create-proxy.png" width="900">

Teraz spróbuj znaleźć adres URL zaplecza wklejając go bezpośrednio w nowej karcie przeglądarki, strony powinny limit czasu. Jest to spowodowane witryny WordPress jest podłączony do sieci wirtualnej sieci i nie przez internet. Jeśli możesz wkleić adres URL serwera proxy do przeglądarki, powinien zostać wyświetlony obraz roślin (pobierane z witryny WordPress) wewnątrz sieci wirtualnej. 

Aplikacja funkcji jest podłączony do Internetu i siecią wirtualną. Serwer proxy jest odbierania żądania za pośrednictwem publicznej sieci internet, a następnie działający jako prosty serwer proxy HTTP do przekazywania tego żądania wzdłuż w sieci wirtualnej. Serwer proxy przekazuje następnie odpowiedź z powrotem do publicznego Internetu. 

<img src="./media/functions-create-vnet/plant.png" width="900">

## <a name="next-steps"></a>Kolejne kroki

Funkcje działające w ramach planu Premium udostępnianie tej samej podstawowej infrastruktury usługi App Service jako aplikacje sieci web w planach PremiumV2. Całą dokumentację dla aplikacji sieci web ma zastosowanie do funkcji plan Premium.

* [Dowiedz się więcej na temat opcji sieciowych w funkcjach](./functions-networking-options.md)
* [Przeczytaj funkcje sieć — często zadawane pytania](./functions-networking-faq.md)
* [Dowiedz się więcej na temat sieci wirtualnych na platformie Azure](../virtual-network/virtual-networks-overview.md)
* [Włączyć więcej sieciowych funkcji i kontrola przy użyciu środowisk usługi App Service](../app-service/environment/intro.md)
* [Łączenie z poszczególnych zasobów lokalnych bez zmian w zaporze za pomocą połączeń hybrydowych](../app-service/app-service-hybrid-connections.md)
* [Dowiedz się więcej na temat serwerów proxy usługi Functions](./functions-proxies.md)

<!--Image references -->
[1]: ./media/functions-create-vnet/topology.png
[2]: ./media/functions-create-vnet/create-function-app.png
[3]: ./media/functions-create-vnet/create-app-service-plan.png
[4]: ./media/functions-create-vnet/configure-vnet.png
[5]: ./media/functions-create-vnet/create-vm-1.png
[6]: ./media/functions-create-vnet/create-vm-2.png
[7]: ./media/functions-create-vnet/create-vm-2-1.png
[8]: ./media/functions-create-vnet/networking-1.png
[9]: ./media/functions-create-vnet/networking-2.png
[10]: ./media/functions-create-vnet/networking-3.png
[11]: ./media/functions-create-vnet/new-proxy.png
[12]: ./media/functions-create-vnet/create-proxy.png
[14]: ./media/functions-create-vnet/vm-networking.png
