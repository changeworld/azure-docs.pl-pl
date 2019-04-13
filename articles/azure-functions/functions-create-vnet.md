---
title: Integracja usługi Azure Functions z siecią wirtualną platformy Azure
description: Samouczek krok po kroku, informacjami na temat nawiązywania połączenia z siecią wirtualną platformy Azure — funkcja
services: functions
author: alexkarcher-msft
manager: jehollan
ms.service: azure-functions
ms.topic: article
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: f8ddcbcb25dc45ee71304ffa1bc5c0c4d3751b61
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/12/2019
ms.locfileid: "59523736"
---
# <a name="integrate-a-function-app-with-an-azure-virtual-network"></a>Integracja z aplikacją funkcji z siecią wirtualną platformy Azure

Ten samouczek krok po kroku dowiesz się, jak łączyć się z zasobami w sieci Wirtualnej platformy Azure za pomocą usługi Azure Functions.

W tym samouczku będziemy wdrażać witrynę WordPress na maszynie Wirtualnej w prywatnej bez — dostępny przez internet, sieć Wirtualną. Następnie wdrożymy funkcji z dostępem do Internetu i siecią Wirtualną. Firma Microsoft użyje tej funkcji dostęp do zasobów w witrynie WordPress wdrożonych w ramach sieci Wirtualnej.

Aby uzyskać więcej informacji na temat działania systemu, rozwiązywanie problemów i zaawansowanej konfiguracji, zobacz dokument [Integrowanie aplikacji z usługą Azure Virtual Network](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet). Usługa Azure Functions w planie Premium mają takie same możliwości hostingu, jak aplikacje sieci web, więc wszystkie funkcje i ograniczenia, w tym dokumencie dotyczą funkcji również.

## <a name="topology"></a>Topologia

 ![Integracja z siecią wirtualną interfejsu użytkownika][1]

## <a name="creating-a-vm-inside-of-a-vnet"></a>Tworzenie maszyny Wirtualnej wewnątrz sieci Wirtualnej

Na początek, utworzymy wstępnie skonfigurowane maszyny Wirtualnej z systemem Wordpress wewnątrz sieci Wirtualnej. 

WordPress na maszynie Wirtualnej został wybrany, ponieważ jest to jeden z co najmniej drogich zasobów, które mogą być wdrażane w sieci Wirtualnej. Należy pamiętać, że ten scenariusz może również współpracować z dowolnego zasobu w sieci Wirtualnej, takich jak interfejsy API REST, środowisk usługi App Service i innymi usługami platformy Azure.

1. Przejdź do witryny Azure Portal
2. Dodaj nowy zasób, otwierając blok "Tworzenie zasobu"
3. Wyszukaj "[WordPress LEMP7 Maksymalna wydajność CentOS](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure)" i otworzyć jego blok tworzenia 
4. W bloku tworzenia należy skonfigurować maszynę Wirtualną z następującymi informacjami:
    1. Utwórz nową grupę zasobów dla tej maszyny Wirtualnej upewnić się, oczyszczanie zasobów ułatwia na końcu tego samouczka. Czy mogę o nazwie Moja grupa zasobów "Funkcja-sieć wirtualna-Tutorial"
    1. Nadaj maszyn wirtualnych unikatową nazwę. Czy mogę o nazwie mnie "Siecią Wirtualną Wordpress"
    1. Wybierz region najbliższy do Ciebie
    1. Wybierz rozmiar jako B1s (1 vcpu, 1 GB pamięci)
    1. Dla konta administratora wybierz Uwierzytelnianie przy użyciu hasła, a następnie wprowadź unikatową nazwę użytkownika i hasło. W tym samouczku nie musisz zalogować się do maszyny Wirtualnej, chyba że potrzebujesz rozwiązać problem.
    
        ![Utwórz kartę podstawy maszyny Wirtualnej](./media/functions-create-vnet/create-vm-1.png)

1. Przejście do karty sieciowe, a następnie wprowadź następujące informacje:
    1.  Utwórz nową sieć wirtualną
    1.  Wprowadź zakres żądanego adresu prywatnego i podsieci w ramach tego zakresu adresów. Rozmiar podsieci określi, ile maszyn wirtualnych, można użyć w ramach planu usługi App Service. Adresy IP oraz wysyłanie są dla Ciebie nowe, czy [dokumentów poświęconych podstawom](https://support.microsoft.com/en-us/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Adresowanie IP i podsieci są ważne w tym scenariuszu, więc będzie najlepiej odczytywanie kilka artykułów i obserwowanie kilka filmów w trybie online, dopóki dobrym pomysłem. 
        1. W tym przykładzie po wyrażeniu zgody na uczestnictwo sieci 10.10.0.0/16 za pomocą podsieci 10.10.1.0/24. Wybieram overprovision i używać /16 podsieci jest łatwy do obliczenia, które podsieci są dostępne w sieci 10.10.0.0/16.
        
        <img src="./media/functions-create-vnet/create-vm-2.png" width="700">

1. Powrót na karcie Sieć Ustaw publicznego adresu IP "None". Spowoduje to wdrożenie maszyny Wirtualnej z dostępem tylko w sieci wirtualnej.
       
    <img src="./media/functions-create-vnet/create-vm-2-1.png" width="700">

7. Tworzenie maszyny Wirtualnej. To potrwa około 5 minut.
8. Po utworzeniu maszyny Wirtualnej można znaleźć jego karty sieciowe i zanotuj adres prywatny adres IP na później. Maszyna wirtualna nie powinna mieć publiczny adres IP.

    ![14]

Będą teraz mieć witrynę wordpress wdrażane wyłącznie w ramach sieci wirtualnej. Ta witryna nie jest dostępne z publicznej sieci internet.

## <a name="create-a-premium-plan-function-app"></a>Tworzenie planu Premium aplikacji funkcji

Następnym krokiem jest tworzenie aplikacji funkcji w planie premium. Plan w warstwie premium to nowa oferta, która oferuje skalowania bez użycia serwera z wszystkich zalet dedykowanego planu usługi App Service. Aplikacje funkcji planu zużycie nie obsługują integracji sieci wirtualnej.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

## <a name="connect-your-function-app-to-your-vnet"></a>Połącz swoją aplikację funkcji do sieci Wirtualnej

Z witryną programu WordPress hostowania plików z poziomu sieci wirtualnej zapewnia teraz możliwość połączenia aplikacji funkcji do sieci Wirtualnej.

1.  W portalu dla aplikacji funkcji w poprzednim kroku wybierz **funkcje platformy**, a następnie wybierz **sieci**

    <img src="./media/functions-create-vnet/networking-0.png" width="850">

1.  Wybierz **kliknij, aby skonfigurować** w obszarze Integracja sieci wirtualnej

    ![Skonfiguruj stan funkcji sieci](./media/functions-create-vnet/Networking-1.png)

1. Na stronie integracji sieci Wirtualnej, wybierz **Dodaj sieć wirtualna (wersja zapoznawcza)**

    <img src="./media/functions-create-vnet/networking-2.png" width="600"> 
    
1.  Utwórz nową podsieć dla planu funkcji i App Service do użycia. Należy pamiętać, że rozmiar podsieci ograniczy łączna liczba maszyn wirtualnych, możesz dodać do planu usługi app service. SIECI wirtualnej będzie automatycznie kierować ruchem między podsieciami w sieci Wirtualnej, dzięki czemu nie ma znaczenia, czy funkcja jest w innej podsieci z maszyny Wirtualnej. 
    
    <img src="./media/functions-create-vnet/networking-3.png" width="600">

## <a name="create-a-function-that-accesses-a-resource-in-your-vnet"></a>Tworzenie funkcji, który uzyskuje dostęp do zasobów w sieci Wirtualnej

Aplikacja funkcji teraz uzyskiwać dostęp sieci Wirtualnej z naszej witryny wordpress skorzystamy używanie funkcji dostępu do tego pliku i udostępnij go użytkownikowi. W tym przykładzie użyjemy witryny wordpress jako interfejs API i funkcji serwera Proxy jako funkcji wywołujących ponieważ są one można łatwo skonfigurować i wizualizacji. Równie łatwo można użyć innego interfejsu API wdrożony w sieci Wirtualnej, a inną funkcję przy użyciu kodu, dzięki czemu interfejs API wywołuje w interfejsie API wdrożonego w ramach sieci wirtualnej. SQL server wdrożonego w ramach sieci wirtualnej jest idealny przykład.

1. W portalu Otwórz aplikację funkcji z poprzedniego kroku
1. Tworzenie serwera Proxy, wybierając **serwerów proxy** > **+**

    <img src="./media/functions-create-vnet/new-proxy.png" width="250">

1. Skonfiguruj nazwę serwera Proxy i trasy. Wybieram /plant jako Mój trasy.
1. Wypełnij IP witryny wordpress z wcześniej, a następnie ustaw adres URL zaplecza `http://{YOUR VM IP}/wp-content/themes/twentyseventeen/assets/images/header.jpg`
    
    <img src="./media/functions-create-vnet/create-proxy.png" width="900">

Teraz Jeśli użytkownik spróbuje znaleźć adres URL zaplecza wklejając go bezpośrednio w nowej karcie przeglądarki, strony powinny limit czasu. To jest oczekiwane, witryny wordpress jest połączony tylko sieci wirtualnej, a nie przez internet. Jeśli w przeglądarce powinien zostać wyświetlony obraz piękne zakładu, pobierane z witryny Wordpress wewnątrz sieci wirtualnej możesz wkleić adres URL serwera Proxy. 

Aplikacja funkcji jest podłączony do Internetu i siecią Wirtualną. Serwer proxy jest odbierania żądania za pośrednictwem publicznej sieci internet, a następnie działający jako prosty serwer proxy HTTP do przekazywania tego żądania wzdłuż w sieci wirtualnej. Serwer proxy przekazuje następnie odpowiedź z powrotem do publicznego Internetu. 

<img src="./media/functions-create-vnet/plant.png" width="900">

## <a name="next-steps"></a>Następne kroki

Funkcje działające w ramach planu Premium udostępnianie tej samej podstawowej infrastruktury usługi App Service jako aplikacje sieci Web na PV2 planów. Oznacza to, że całą dokumentację dla aplikacji sieci Web ma zastosowanie do funkcji plan Premium.

1. [Dowiedz się więcej na temat opcji sieciowych w funkcjach w tym miejscu](./functions-networking-options.md)
1. [Przeczytaj funkcje, w tym miejscu sieć — często zadawane pytania](./functions-networking-faq.md)
1. [Dowiedz się więcej na temat sieci wirtualnych na platformie Azure](../virtual-network/virtual-networks-overview.md)
1. [Włączyć więcej sieciowych funkcji i kontrola przy użyciu środowisk usługi App Service](../app-service/environment/intro.md)
1. [Łączenie z poszczególnych zasobów lokalnych bez zmian w zaporze przy użyciu połączeń hybrydowych](../app-service/app-service-hybrid-connections.md)
1. [Dowiedz się więcej na temat serwerów proxy funkcji](./functions-proxies.md)

<!--Image references-->
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
