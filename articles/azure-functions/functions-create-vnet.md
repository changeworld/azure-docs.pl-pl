---
title: Integracja funkcji platformy Azure z siecią wirtualną platformy Azure
description: Samouczek krok po kroku, który pokazuje, jak połączyć funkcję z siecią wirtualną platformy Azure
author: alexkarcher-msft
ms.topic: article
ms.date: 5/03/2019
ms.author: alkarche
ms.reviewer: glenga
ms.openlocfilehash: 0c70c69f547405eb8ebdcf6dcc6ae597db151e53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75433208"
---
# <a name="tutorial-integrate-functions-with-an-azure-virtual-network"></a>Samouczek: integrowanie usługi Functions z siecią wirtualną platformy Azure

W tym samouczku pokazano, jak używać funkcji platformy Azure do łączenia się z zasobami w sieci wirtualnej platformy Azure. utworzysz funkcję, która ma dostęp zarówno do Internetu, jak i do maszyny Wirtualnej z systemem WordPress w sieci wirtualnej.

> [!div class="checklist"]
> * Tworzenie aplikacji funkcyjnej w planie Premium
> * Wdrażanie witryny WordPress na maszynie Wirtualnej w sieci wirtualnej
> * Podłączanie aplikacji funkcji do sieci wirtualnej
> * Tworzenie serwera proxy funkcji w celu uzyskania dostępu do zasobów WordPress
> * Żądanie pliku WordPress z wnętrza sieci wirtualnej

## <a name="topology"></a>Topologia

Na poniższym diagramie przedstawiono architekturę utworzonego rozwiązania:

 ![Interfejs użytkownika do integracji z siecią wirtualną](./media/functions-create-vnet/topology.png)

Funkcje uruchomione w planie Premium mają takie same możliwości hostingu jak aplikacje sieci web w usłudze Azure App Service, która zawiera funkcję integracji sieci wirtualnej. Aby dowiedzieć się więcej na temat integracji sieci wirtualnej, w tym rozwiązywania problemów i zaawansowanej konfiguracji, zobacz [Integrowanie aplikacji z siecią wirtualną platformy Azure](../app-service/web-sites-integrate-with-vnet.md).

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku ważne jest, aby zrozumieć adresowanie IP i podsieci. Możesz zacząć od [tego artykułu, który obejmuje podstawy adresowania i podsieci](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Wiele innych artykułów i filmów jest dostępnych online.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="create-a-function-app-in-a-premium-plan"></a>Tworzenie aplikacji funkcyjnej w planie Premium

Najpierw tworzysz aplikację funkcyjną w [planie Premium]. Ten plan zapewnia skalę bezserwerową, jednocześnie obsługując integrację sieci wirtualnej.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

Aplikację funkcji można przypiąć do pulpitu nawigacyjnego, wybierając ikonę pinezki w prawym górnym rogu. Przypinanie ułatwia powrót do tej aplikacji funkcji po utworzeniu maszyny Wirtualnej.

## <a name="create-a-vm-inside-a-virtual-network"></a>Tworzenie maszyny wirtualnej w sieci wirtualnej

Następnie utwórz wstępnie skonfigurowaną maszynę wirtualną, która uruchamia WordPress wewnątrz sieci wirtualnej[(WordPress LEMP7 Max Performance](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure) by Jetware). Maszyna wirtualna WordPress jest używana ze względu na niski koszt i wygodę. Ten sam scenariusz działa z dowolnego zasobu w sieci wirtualnej, takich jak interfejsy API REST, środowiska usługi app service i innych usług platformy Azure. 

1. W portalu wybierz pozycję **+ Utwórz zasób** w lewym `WordPress LEMP7 Max Performance`okienku nawigacji w typie pola wyszukiwania i naciśnij klawisz Enter.

1. Wybierz **Wordpress LEMP Max Performance** w wynikach wyszukiwania. Wybierz plan oprogramowania **Wordpress LEMP Max Performance for CentOS** jako **plan oprogramowania** i wybierz **pozycję Utwórz**.

1. Na karcie **Podstawy** użyj ustawień maszyny Wirtualnej określonych w tabeli poniżej obrazu:

    ![Karta Podstawowe informacje dotyczące tworzenia maszyny Wirtualnej](./media/functions-create-vnet/create-vm-1.png)

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której tworzone są zasoby. | 
    | **[Grupa zasobów](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Wybierz `myResourceGroup`opcję lub grupę zasobów utworzoną za pomocą aplikacji funkcji. Korzystanie z tej samej grupy zasobów dla aplikacji funkcji, WordPress VM i plan hostingu ułatwia czyszczenie zasobów po zakończeniu tego samouczka. |
    | **Nazwa maszyny wirtualnej** | VNET-Wordpress | Nazwa maszyny Wirtualnej musi być unikatowa w grupie zasobów |
    | **[Region](https://azure.microsoft.com/regions/)** | (Europa) Europa Zachodnia | Wybierz region w pobliżu lub w pobliżu funkcji, które uzyskują dostęp do maszyny Wirtualnej. |
    | **Rozmiar** | B1s (własnach B1 | Wybierz **pozycję Zmień rozmiar,** a następnie wybierz standardowy obraz B1s, który ma 1 procesor vCPU i 1 GB pamięci. |
    | **Typ uwierzytelniania** | Hasło | Aby użyć uwierzytelniania hasłem, należy również określić **nazwę użytkownika**, bezpieczne **hasło**, a następnie **potwierdzić hasło**. W tym samouczku nie trzeba logować się do maszyny Wirtualnej, chyba że trzeba rozwiązać problem. |

1. Wybierz kartę **Sieć** i w obszarze Konfigurowanie sieci wirtualnych wybierz pozycję **Utwórz nowy**.

1. W **obszarze Tworzenie sieci wirtualnej**użyj ustawień w tabeli poniżej obrazu:

    ![Karta Tworzenie maszyny Wirtualnej w sieci](./media/functions-create-vnet/create-vm-2.png)

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **Nazwa** | myResourceGroup-vnet | Można użyć domyślnej nazwy wygenerowanej dla sieci wirtualnej. |
    | **Zakres adresów** | 10.10.0.0/16 | Użyj jednego zakresu adresów dla sieci wirtualnej. |
    | **Nazwa podsieci** | Samouczek-Net | Nazwa podsieci. |
    | **Zakres adresów** (podsieć) | 10.10.1.0/24   | Rozmiar podsieci określa, ile interfejsów można dodać do podsieci. Ta podsieć jest używana przez witrynę WordPress.  Podsieć `/24` udostępnia 254 adresy hostów. |

1. Wybierz **przycisk OK,** aby utworzyć sieć wirtualną.

1. Z powrotem na karcie **Sieć** wybierz pozycję **Brak** dla **publicznego adresu IP**.

1. Wybierz kartę **Zarządzanie,** a następnie w **obszarze Konto przechowywania diagnostyki**wybierz konto Magazynu utworzone za pomocą aplikacji funkcji.

1. Wybierz pozycję **Przegląd + utwórz**. Po zakończeniu sprawdzania poprawności wybierz pozycję **Utwórz**. Proces tworzenia maszyny Wirtualnej trwa kilka minut. Utworzona maszyna wirtualna może uzyskać dostęp tylko do sieci wirtualnej.

1. Po utworzeniu maszyny Wirtualnej wybierz pozycję **Przejdź do zasobu,** aby wyświetlić stronę nowej maszyny Wirtualnej, a następnie wybierz pozycję **Sieć** w obszarze **Ustawienia**.

1. Sprawdź, czy nie ma **publicznego adresu IP**. Zanotuj **prywatny adres IP,** którego używasz do łączenia się z maszyną wirtualną z aplikacji funkcji.

    ![Ustawienia sieci na maszynie Wirtualnej](./media/functions-create-vnet/vm-networking.png)

Masz teraz witrynę WordPress wdrożoną w całości w sieci wirtualnej. Ta strona nie jest dostępna z publicznego Internetu.

## <a name="connect-your-function-app-to-the-virtual-network"></a>Łączenie aplikacji funkcji z siecią wirtualną

Dzięki witrynie WordPress działającej na maszynie wirtualnej w sieci wirtualnej możesz teraz połączyć aplikację funkcji z tą siecią wirtualną.

1. W nowej aplikacji funkcji wybierz **platformę funkcje** > **sieci**.

    ![Wybieranie sieci w aplikacji funkcyjnej](./media/functions-create-vnet/networking-0.png)

1. W obszarze **Integracja sieci wirtualnej**wybierz pozycję **Kliknij tutaj, aby skonfigurować**.

    ![Stan konfigurowania funkcji sieciowej](./media/functions-create-vnet/Networking-1.png)

1. Na stronie integracji sieci wirtualnej wybierz pozycję **Dodaj sieć wirtualną (wersja zapoznawcza)**.

    ![Dodawanie podglądu integracji sieci wirtualnej](./media/functions-create-vnet/networking-2.png)

1. W **obszarze Stan funkcji sieciowych**użyj ustawień w tabeli poniżej obrazu:

    ![Definiowanie sieci wirtualnej aplikacji funkcji](./media/functions-create-vnet/networking-3.png)

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **Sieć wirtualna** | MyResourceGroup-vnet | Ta sieć wirtualna jest siecią utworzoną wcześniej. |
    | **Podsieci** | Tworzenie nowej podsieci | Utwórz podsieć w sieci wirtualnej dla aplikacji funkcji do użycia. Integracja sieci wirtualnej musi być skonfigurowana do używania pustej podsieci. Nie ma znaczenia, że funkcje używają innej podsieci niż maszyna wirtualna. Sieć wirtualna automatycznie kieruje ruch między dwiema podsieciami. |
    | **Nazwa podsieci** | Sieć funkcyjna | Nazwa nowej podsieci. |
    | **Blok adresów sieci wirtualnej** | 10.10.0.0/16 | Wybierz ten sam blok adresu używany przez witrynę WordPress. Powinien mieć zdefiniowany tylko jeden blok adresu. |
    | **Zakres adresów** | 10.10.2.0/24   | Rozmiar podsieci ogranicza całkowitą liczbę wystąpień, które aplikacja funkcji planu Premium można skalować w poziomie. W tym przykładzie użyto podsieci `/24` z 254 dostępnymi adresami hosta. Ta podsieć jest nadmiernie aprowizacji, ale łatwe do obliczenia. |

1. Wybierz **przycisk OK,** aby dodać podsieć. Zamknij strony Integracja sieci wirtualnej i Stan funkcji sieciowych, aby powrócić do strony aplikacji funkcji.

Aplikacja funkcji może teraz uzyskać dostęp do sieci wirtualnej, w której działa strona WordPress. Następnie [użyjesz serwerów proxy usługi Azure Functions,](functions-proxies.md) aby zwrócić plik z witryny WordPress.

## <a name="create-a-proxy-to-access-vm-resources"></a>Tworzenie serwera proxy w celu uzyskania dostępu do zasobów maszyn wirtualnych

Po włączeniu integracji sieci wirtualnej można utworzyć serwer proxy w aplikacji funkcji do przesyłania dalej żądań do maszyny Wirtualnej uruchomionej w sieci wirtualnej.

1. W aplikacji funkcji wybierz pozycję **Serwery proxy** > **+**, a następnie użyj ustawień serwera proxy w tabeli poniżej obrazu:

    ![Definiowanie ustawień serwera proxy](./media/functions-create-vnet/create-proxy.png)

    | Ustawienie  | Sugerowana wartość  | Opis      |
    | -------- | ---------------- | ---------------- |
    | **Nazwa** | Zakład | Nazwa może być dowolną wartością. Służy do identyfikacji serwera proxy. |
    | **Szablon trasy** | /roślina | Kierowanie, które mapuje do zasobu maszyny Wirtualnej. |
    | **Adres URL zaplecza** | http://<YOUR_VM_IP>/wp-content/themes/twentyseventeen/assets/images/header.jpg | Zamień `<YOUR_VM_IP>` na adres IP maszyny Wirtualnej WordPress, który został utworzony wcześniej. To mapowanie zwraca pojedynczy plik z witryny. |

1. Wybierz **pozycję Utwórz,** aby dodać serwer proxy do aplikacji funkcyjnej.

## <a name="try-it-out"></a>Testowanie

1. W przeglądarce spróbuj uzyskać dostęp do adresu URL używanego jako **adres URL wewnętrznej bazy danych**. Zgodnie z oczekiwaniami, czas żądania się przesunie. Limit czasu występuje, ponieważ witryna WordPress jest podłączona tylko do sieci wirtualnej, a nie do Internetu.

1. Skopiuj wartość **adresu URL serwera proxy** z nowego serwera proxy i wklej ją do paska adresu przeglądarki. Zwrócony obraz pochodzi z witryny WordPress działającej wewnątrz sieci wirtualnej.

    ![Plik obrazu roślin zwrócony z witryny WordPress](./media/functions-create-vnet/plant.png)

Aplikacja funkcji jest połączona zarówno z Internetem, jak i z siecią wirtualną. Serwer proxy odbiera żądanie za pośrednictwem publicznego Internetu, a następnie działa jako prosty serwer proxy HTTP do przesyłania dalej tego żądania do podłączonej sieci wirtualnej. Następnie serwer proxy przekazuje odpowiedź z powrotem do Ciebie publicznie przez Internet.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku witryna WordPress służy jako interfejs API, który jest wywoływany przy użyciu serwera proxy w aplikacji funkcji. Ten scenariusz sprawia, że dobry samouczek, ponieważ jest łatwy do skonfigurowania i wizualizacji. Można użyć dowolnego innego interfejsu API wdrożonego w sieci wirtualnej. Można również utworzyć funkcję z kodem, który wywołuje interfejsy API wdrożone w sieci wirtualnej. Bardziej realistyczny scenariusz to funkcja, która używa interfejsów API klienta danych do wywoływania wystąpienia programu SQL Server wdrożonego w sieci wirtualnej.

Funkcje uruchomione w planie Premium współużytkują tę samą podstawową infrastrukturę usługi app service, co aplikacje internetowe w planach PremiumV2. Cała dokumentacja dotycząca [aplikacji sieci web w usłudze Azure App Service](../app-service/overview.md) dotyczy funkcji planu Premium.

> [!div class="nextstepaction"]
> [Dowiedz się więcej o opcjach sieci w funkcji](./functions-networking-options.md)

[Plan Premium]: functions-scale.md#premium-plan
