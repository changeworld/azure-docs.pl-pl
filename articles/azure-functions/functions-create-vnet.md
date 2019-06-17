---
title: Integracja usługi Azure Functions z siecią wirtualną platformy Azure
description: Samouczek krok po kroku, pokazujący sposób nawiązywania połączeń z siecią wirtualną platformy Azure — funkcja
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: article
ms.date: 5/03/2019
ms.author: alkarche, glenga
ms.openlocfilehash: 55cce60ab3d1cda3cb870afd2f6214f917a04189
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063280"
---
# <a name="tutorial-integrate-functions-with-an-azure-virtual-network"></a>Samouczek: Integrowanie usługi Functions z usługą Azure virtual network

Ten samouczek pokazuje, jak łączyć się z zasobami w sieci wirtualnej platformy Azure za pomocą usługi Azure Functions. utworzysz funkcję, która ma dostęp zarówno z Internetem i maszyny Wirtualnej z systemem WordPress w sieci wirtualnej.

> [!div class="checklist"]
> * Tworzenie aplikacji funkcji w planie Premium
> * Wdrażanie witryny WordPress do maszyny Wirtualnej w sieci wirtualnej
> * Połącz aplikację funkcji z siecią wirtualną
> * Tworzenie serwera proxy funkcji dostępu do zasobów systemu WordPress
> * Żądania pliku WordPress z wewnątrz sieci wirtualnej

> [!NOTE]  
> Ten samouczek tworzy aplikację funkcji w planie Premium. Ten plan hostingu jest obecnie w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [plan w warstwie Premium].

## <a name="topology"></a>Topologia

Na poniższym diagramie przedstawiono architekturę rozwiązania, którą tworzysz:

 ![Interfejs użytkownika dla Integracja sieci wirtualnej](./media/functions-create-vnet/topology.png)

Funkcje działające w ramach planu Premium mają takie same możliwości hostingu, jak aplikacje sieci web w usłudze Azure App Service, która obejmuje funkcję Integracja z siecią wirtualną. Aby uzyskać więcej informacji na temat integracji sieci wirtualnej, w tym Rozwiązywanie problemów i Zaawansowana konfiguracja, zobacz [Integrowanie aplikacji z siecią wirtualną platformy Azure](../app-service/web-sites-integrate-with-vnet.md).

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku jest ważne, że rozumiesz, adresy IP oraz wysyłanie. Można zacząć od [w tym artykule, który obejmuje podstawowe informacje dotyczące adresowania i podsieci](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Wiele więcej artykułów i filmów wideo dostępnych w trybie online.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-function-app-in-a-premium-plan"></a>Tworzenie aplikacji funkcji w planie Premium

Najpierw należy utworzyć aplikację funkcji na platformie [plan w warstwie Premium]. Ten plan zapewnia skalowanie bez użycia serwera, obsługując Integracja sieci wirtualnej.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

Możesz przypiąć aplikację funkcji do pulpitu nawigacyjnego, wybierając ikonę pinezki w prawym górnym rogu. Przypinanie ułatwia powrót do tej aplikacji funkcji, po utworzeniu maszyny Wirtualnej.

## <a name="create-a-vm-inside-a-virtual-network"></a>Tworzenie maszyny Wirtualnej w sieci wirtualnej

Następnie utwórz wstępnie skonfigurowane maszyny Wirtualne z systemem WordPress wewnątrz sieci wirtualnej ([WordPress LEMP7 Max Performance](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure) przez firmy Jetware). Ze względu na jej niskie koszty i wygody używany jest maszyny Wirtualnej z systemem WordPress. Ten sam scenariusz współpracuje z dowolnego zasobu w sieci wirtualnej, takich jak interfejsy API REST, środowisk usługi App Service i innymi usługami platformy Azure. 

1. W portalu, wybierz **+ Utwórz zasób** w okienku nawigacji po lewej stronie, w polu wyszukiwania wpisz `WordPress LEMP7 Max Performance`, i naciśnij klawisz Enter.

1. Wybierz **Wordpress LEMP Max Performance** w wynikach wyszukiwania. Wybierz plan oprogramowania **Wordpress LEMP Max Performance centos** jako **Plan oprogramowania** i wybierz **Utwórz**.

1. W **podstawy** kartę, należy użyć ustawień maszyny Wirtualnej określonych w tabeli znajdującej się poniżej obrazu:

    ![Karta podstawy do tworzenia maszyny Wirtualnej](./media/functions-create-vnet/create-vm-1.png)

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w którym są tworzone zasoby. | 
    | **[Grupa zasobów](../azure-resource-manager/resource-group-overview.md)**  | myResourceGroup | Wybierz `myResourceGroup`, lub grupy zasobów, które utworzono za pomocą aplikacji funkcji. Przy użyciu tej samej grupy zasobów dla aplikacji funkcji platformy WordPress w maszynę Wirtualną i plan hostingu ułatwia wyczyścić zasoby po ukończeniu tego samouczka. |
    | **Nazwa maszyny wirtualnej** | VNET-Wordpress | Nazwa maszyny Wirtualnej musi być unikatowa w grupie zasobów |
    | **[Region](https://azure.microsoft.com/regions/)** | (Europa) Europa Zachodnia | Wybierz region, w okolicy lub w pobliżu funkcje, które dostęp do maszyny Wirtualnej. |
    | **Rozmiar** | B1s | Wybierz **Zmień rozmiar** a następnie wybierz obraz standardowa B1s, który ma 1 Procesor wirtualny vCPU i 1 GB pamięci. |
    | **Typ uwierzytelniania** | Hasło | Aby używać uwierzytelniania za pomocą hasła, należy także określić **Username**, bezpieczną **hasło**, a następnie **Potwierdź hasło**. Na potrzeby tego samouczka nie trzeba zalogować się do maszyny Wirtualnej, chyba że potrzebujesz rozwiązać problem. |

1. Wybierz **sieć** , a w obszarze Konfiguracja sieci wirtualne wybierz pozycję **Utwórz nową**.

1. W **Utwórz sieć wirtualną**, użyj ustawień w tabeli znajdującej się poniżej obrazu:

    ![Tworzenie maszyny Wirtualnej na karcie sieci](./media/functions-create-vnet/create-vm-2.png)

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **Nazwa** | myResourceGroup-vnet | Można użyć domyślnej nazwy generowane dla sieci wirtualnej. |
    | **Zakres adresów** | 10.10.0.0/16 | Używaj zakresu pojedynczy adres sieci wirtualnej. |
    | **Nazwa podsieci** | Tutorial-Net | Nazwa podsieci. |
    | **Zakres adresów** (podsieć) | 10.10.1.0/24   | Rozmiar podsieci definiuje, ile interfejsy mogą być dodawane do tej podsieci. Ta podsieć jest używana przez witrynę WordPress.  A `/24` podsieć zawiera adresy 254 hostów. |

1. Wybierz **OK** można utworzyć sieci wirtualnej.

1. Wstecz w **sieć** kartę, wybrać **Brak** dla **publiczny adres IP**.

1. Wybierz **zarządzania** kartę, a następnie w obszarze **konto magazynu diagnostyki**, wybierz konto magazynu utworzone za pomocą aplikacji funkcji.

1. Wybierz pozycję **Przegląd + utwórz**. Po zakończeniu weryfikacji, wybierz **Utwórz**. Maszyna wirtualna utworzyć proces trwa kilka minut. Utworzonej maszyny Wirtualnej można uzyskać dostęp tylko do sieci wirtualnej.

1. Po utworzeniu maszyny Wirtualnej wybierz **przejdź do zasobu** Aby wyświetlić stronę dla nowej maszyny Wirtualnej, następnie wybierz polecenie **sieć** w obszarze **ustawienia**.

1. Sprawdź, czy nie **publiczny adres IP**. Zanotuj **prywatny adres IP**, którego możesz nawiązać połączenie z maszyną wirtualną z aplikacji funkcji.

    ![Ustawienia sieciowe w maszynie Wirtualnej](./media/functions-create-vnet/vm-networking.png)

Masz teraz witrynę WordPress wdrażane wyłącznie w ramach sieci wirtualnej. Ta witryna nie jest dostępne z publicznej sieci internet.

## <a name="connect-your-function-app-to-the-virtual-network"></a>Połącz swoją aplikację funkcji z siecią wirtualną

Z witryną programu WordPress w Maszynę wirtualną w sieci wirtualnej zapewnia teraz możliwość połączenia aplikacji funkcji do tej sieci wirtualnej.

1. W nowej aplikacji funkcji, wybierz **funkcje platformy** > **sieć**.

    ![Wybierz sieć w aplikacji funkcji](./media/functions-create-vnet/networking-0.png)

1. W obszarze **Integracja z siecią wirtualną**, wybierz opcję **kliknij tutaj, aby skonfigurować**.

    ![Stan do konfigurowania funkcji sieci](./media/functions-create-vnet/Networking-1.png)

1. Na stronie integracji sieci wirtualnej wybierz **Dodaj sieć wirtualna (wersja zapoznawcza)** .

    ![Dodaj Integracja sieci wirtualnej w wersji zapoznawczej](./media/functions-create-vnet/networking-2.png)

1. W **stan funkcji sieci**, użyj ustawień w tabeli znajdującej się poniżej obrazu:

    ![Definiowanie sieci wirtualnej aplikacji — funkcja](./media/functions-create-vnet/networking-3.png)

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **Virtual Network** | MyResourceGroup-vnet | Ta sieć wirtualna jest jedną, która została utworzona wcześniej. |
    | **Podsieć** | Utwórz nową podsieć | Utwórz podsieć w sieci wirtualnej dla aplikacji funkcji do użycia. Integracja sieci wirtualnej musi być skonfigurowana do używania pustą podsieć. Nie ma znaczenia, czy funkcji, użyj innej podsieci niż maszyny Wirtualnej. Sieć wirtualna automatycznie kieruje ruchem między dwiema podsieciami. |
    | **Nazwa podsieci** | Function-Net | Nazwa nowej podsieci. |
    | **Blok adresów sieci wirtualnej** | 10.10.0.0/16 | Wybierz ten sam blok adresów używanych przez witrynę WordPress. Powinien mieć tylko jeden blok adresów zdefiniowanych. |
    | **Zakres adresów** | 10.10.2.0/24   | Całkowita liczba wystąpień, które aplikacja funkcji planu Premium można skalować do rozmiar podsieci, ogranicza możliwość użycia. W tym przykładzie użyto `/24` podsieć o 254 hostów dostępnych adresów. Ta podsieć jest nadmiernie aprowizowane, ale łatwy do obliczenia. |

1. Wybierz **OK** Aby dodać podsieć. Zamknij strony Integracja z siecią wirtualną i stan funkcji sieci, aby powrócić do strony aplikacji funkcji.

Aplikacja funkcji może teraz uzyskiwać dostęp do sieci wirtualnej, w którym uruchomiono witrynę WordPress. Następnie użyj [usługi Azure Functions Proxies](functions-proxies.md) zwrócić plik z witryny WordPress.

## <a name="create-a-proxy-to-access-vm-resources"></a>Utwórz serwer proxy, aby uzyskiwać dostęp do zasobów maszyny Wirtualnej

Integracja sieci wirtualnej jest włączona umożliwia tworzenie serwera proxy w aplikacji funkcji do przekazywania żądań do maszyny Wirtualnej uruchomionej w sieci wirtualnej.

1. W aplikacji funkcji, wybierz **proxy** >  **+** , następnie użyj ustawień serwera proxy w tabeli pod ilustracją:

    ![Zdefiniuj ustawienia serwera proxy](./media/functions-create-vnet/create-proxy.png)

    | Ustawienie  | Sugerowana wartość  | Opis      |
    | -------- | ---------------- | ---------------- |
    | **Nazwa** | Zakładu produkcyjnego | Nazwa może zawierać żadnej wartości. Służy do identyfikowania serwera proxy. |
    | **Szablon trasy** | /Plant | Trasy, który jest mapowany do zasobu maszyny Wirtualnej. |
    | **Adres URL zaplecza** | http://<YOUR_VM_IP>/wp-content/themes/twentyseventeen/assets/images/header.jpg | Zastąp `<YOUR_VM_IP>` z adresem IP swojej maszyny wirtualnej WordPress, która została utworzona wcześniej. To mapowanie zwraca pojedynczy plik z witryny. |

1. Wybierz **Utwórz** można dodać serwer proxy do aplikacji funkcji.

## <a name="try-it-out"></a>Wypróbuj

1. W przeglądarce, próby dostępu do adresu URL używany jako **URL wewnętrznej bazy danych**. Zgodnie z oczekiwaniami, upłynął limit czasu żądania. Przekroczono limit czasu występuje, ponieważ witryny WordPress jest podłączony do sieci wirtualnej i nie przez internet.

1. Kopiuj **adres URL serwera Proxy** z Twojego nowego serwera proxy i wklej go na pasku adresu przeglądarki. Zwrócone obraz pochodzi z witryny WordPress, działających w sieci wirtualnej.

    ![Plik obrazu zakładu produkcyjnego zwrócony z witryny WordPress](./media/functions-create-vnet/plant.png)

Aplikacja funkcji jest podłączony do Internetu i siecią wirtualną. Serwer proxy jest odbierania żądania za pośrednictwem publicznej sieci internet, a następnie działający jako prosty serwer proxy HTTP do przekazywania tego żądania do połączonych sieci wirtualnej. Serwer proxy przekazuje następnie odpowiedź z powrotem do publicznie w Internecie.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku witryna WordPress służy jako interfejs API, która jest wywoływana przy użyciu serwera proxy w aplikacji funkcji. W tym scenariuszu sprawia, że dobry samouczek ponieważ ułatwiają konfigurowanie i wizualizację. Można użyć innego interfejsu API wdrożonego w ramach sieci wirtualnej. Można również utworzono funkcję z kodem, który wywołuje interfejsy API wdrożonego w ramach sieci wirtualnej. Zrealizować bardziej realistyczny scenariusz jest funkcją, która używa interfejsów API klienta danych w celu wywołania wystąpienia programu SQL Server wdrożony w sieci wirtualnej.

Funkcje działające w ramach planu Premium udostępnianie tej samej podstawowej infrastruktury usługi App Service jako aplikacje sieci web w planach PremiumV2. Cała dokumentacja dla [aplikacji sieci web w usłudze Azure App Service](../app-service/overview.md) ma zastosowanie do funkcji plan Premium.

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat opcji sieciowych w funkcjach](./functions-networking-options.md)

[Plan w warstwie Premium]: functions-scale.md#premium-plan
