---
title: Włączanie dostępu do witryny prywatnej do usług Azure Functions
description: Dowiedz się, jak skonfigurować dostęp do witryny prywatnej sieci wirtualnej platformy Azure dla usługi Azure Functions.
author: mcollier
ms.author: mcollier
ms.service: azure-functions
ms.topic: tutorial
ms.date: 02/15/2020
ms.openlocfilehash: ada08de182791c6ecb2b83ef3b924bf40975e1ee
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78851314"
---
# <a name="tutorial-establish-azure-functions-private-site-access"></a>Samouczek: Ustanawianie dostępu do witryny prywatnej usług Azure Functions

W tym samouczku pokazano, jak włączyć dostęp do [witryny prywatnej](./functions-networking-options.md#private-site-access) za pomocą usługi Azure Functions. Korzystając z dostępu do witryny prywatnej, można wymagać, aby kod funkcji był wyzwalany tylko z określonej sieci wirtualnej.

Dostęp do witryny prywatnej jest przydatne w scenariuszach, gdy dostęp do aplikacji funkcji musi być ograniczony do określonej sieci wirtualnej. Na przykład aplikacja funkcji może mieć zastosowanie tylko do pracowników określonej organizacji lub usług, które znajdują się w określonej sieci wirtualnej (takich jak inna funkcja platformy Azure, maszyna wirtualna platformy Azure lub klaster AKS).

Jeśli aplikacja Functions musi uzyskać dostęp do zasobów platformy Azure w sieci wirtualnej lub połączonych za pośrednictwem [punktów końcowych usługi,](../virtual-network/virtual-network-service-endpoints-overview.md)potrzebna jest [integracja z siecią wirtualną.](./functions-create-vnet.md)

W tym samouczku dowiesz się, jak skonfigurować dostęp do witryny prywatnej dla aplikacji funkcji:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej
> * Tworzenie usługi Bastion platformy Azure
> * Tworzenie aplikacji funkcji platformy Azure
> * Konfigurowanie punktu końcowego usługi sieci wirtualnej
> * Tworzenie i wdrażanie funkcji platformy Azure
> * Wywoływanie funkcji z zewnątrz i w sieci wirtualnej

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="topology"></a>Topologia

Na poniższym diagramie przedstawiono architekturę rozwiązania, które ma zostać utworzone:

![Diagram architektury wysokiego poziomu dla rozwiązania dostępu do witryn prywatnych](./media/functions-create-private-site-access/topology.png)

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku ważne jest, aby zrozumieć adresowanie IP i podsieci. Możesz zacząć od [tego artykułu, który obejmuje podstawy adresowania i podsieci](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Wiele innych artykułów i filmów jest dostępnych online.

## <a name="sign-in-to-azure-portal"></a>Logowanie do witryny Azure Portal

Zaloguj się do [Portalu Azure](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Pierwszym krokiem w tym samouczku jest utworzenie nowej maszyny wirtualnej wewnątrz sieci wirtualnej.  Maszyna wirtualna będzie używana do uzyskiwania dostępu do funkcji po ograniczeniu dostępu do niej tylko z poziomu sieci wirtualnej.

1. Wybierz przycisk **Utwórz zasób.**

2. W polu wyszukiwania `Windows Server`wpisz i wybierz pozycję **Windows Server** w wynikach wyszukiwania.

3. Wybierz **pozycję Centrum danych systemu Windows Server 2019** z listy opcji systemu Windows Server i naciśnij przycisk **Utwórz.**

4. Na karcie **Podstawy** użyj ustawień maszyny Wirtualnej określonych w tabeli poniżej obrazu:

    >[!div class="mx-imgBorder"]
    >![Karta Podstawowe informacje o nowej maszynie Wirtualnej systemu Windows](./media/functions-create-private-site-access/create-vm-3.png)

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której tworzone są zasoby. |
    | [**Grupa zasobów**](../azure-resource-manager/management/overview.md) | myResourceGroup | Wybierz grupę zasobów, aby zawierać wszystkie zasoby dla tego samouczka.  Korzystanie z tej samej grupy zasobów ułatwia oczyszczanie zasobów po zakończeniu pracy z tym samouczkiem. |
    | **Nazwa maszyny wirtualnej** | myVM ( myVM ) | Nazwa maszyny Wirtualnej musi być unikatowa w grupie zasobów |
    | [**Region**](https://azure.microsoft.com/regions/) | (Stany Zjednoczone) Północno-środkowe stany USA | Wybierz region w pobliżu lub w pobliżu funkcji, do których chcesz uzyskać dostęp. |
    | **Publiczne porty wejściowe** | Brak | Wybierz **opcję Brak,** aby upewnić się, że nie ma połączenia przychodzącego z maszyną wirtualną z Internetu. Zdalny dostęp do maszyny Wirtualnej zostanie skonfigurowany za pośrednictwem usługi Azure Bastion. |

5. Wybierz kartę **Sieć** i wybierz pozycję **Utwórz nowy,** aby skonfigurować nową sieć wirtualną.

    >[!div class="mx-imgBorder"]
    >![Tworzenie nowej sieci wirtualnej dla nowej maszyny Wirtualnej](./media/functions-create-private-site-access/create-vm-networking.png)

6. W **obszarze Tworzenie sieci wirtualnej**użyj ustawień w tabeli poniżej obrazu:

    >[!div class="mx-imgBorder"]
    >![Tworzenie nowej sieci wirtualnej dla nowej maszyny Wirtualnej](./media/functions-create-private-site-access/create-vm-vnet-1.png)

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **Nazwa** | myResourceGroup-vnet | Można użyć domyślnej nazwy wygenerowanej dla sieci wirtualnej. |
    | **Zakres adresów** | 10.10.0.0/16 | Użyj jednego zakresu adresów dla sieci wirtualnej. |
    | **Nazwa podsieci** | Samouczek | Nazwa podsieci. |
    | **Zakres adresów** (podsieć) | 10.10.1.0/24 | Rozmiar podsieci określa, ile interfejsów można dodać do podsieci. Ta podsieć jest używana przez maszynę wirtualną. Podsieć `/24` udostępnia 254 adresy hostów. |

7. Wybierz **przycisk OK,** aby utworzyć sieć wirtualną.
8. Na karcie Sieć upewnij **się,** że opcja **Brak** jest wybrana dla **publicznego adresu IP**.
9. Wybierz kartę **Zarządzanie,** a następnie w **obszarze Konto magazynu diagnostycznego**wybierz pozycję **Utwórz nowy,** aby utworzyć nowe konto magazynu.
10. Pozostaw wartości domyślne dla sekcji **Tożsamość,** **Automatyczne zamykanie**i **Kopia zapasowa.**
11. Wybierz pozycję **Przegląd + utwórz**. Po zakończeniu sprawdzania poprawności wybierz pozycję **Utwórz**. Proces tworzenia maszyny Wirtualnej trwa kilka minut.

## <a name="configure-azure-bastion"></a>Konfigurowanie bastionu platformy Azure

[Usługa Azure Bastion](https://azure.microsoft.com/services/azure-bastion/) to w pełni zarządzana usługa platformy Azure, która zapewnia bezpieczny dostęp RDP i SSH do maszyn wirtualnych bezpośrednio z witryny Azure portal. Korzystanie z usługi Azure Bastion eliminuje konieczność konfigurowania ustawień sieciowych związanych z dostępem do protokołu RDP.

1. W portalu wybierz pozycję **Dodaj** u góry widoku grupy zasobów.
2. W polu wyszukiwania wpisz "Bastion".  Wybierz "Bastion".
3. Wybierz **pozycję Utwórz,** aby rozpocząć proces tworzenia nowego zasobu bastionu platformy Azure. W sekcji **Sieć wirtualna** zostanie wyświetlony komunikat o `AzureBastionSubnet` błędzie, ponieważ nie ma jeszcze podsieci. Podsieć jest tworzona w następujących krokach. Użyj ustawień w tabeli pod obrazem:

    >[!div class="mx-imgBorder"]
    >![Rozpoczęcie tworzenia bastionu platformy Azure](./media/functions-create-private-site-access/create-bastion-basics-1.png)

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **Nazwa** | myBastion (myBastion) | Nazwa nowego zasobu Bastion |
    | **Region** | Północno-środkowe stany USA | Wybierz [region](https://azure.microsoft.com/regions/) w swojej okolicy lub w pobliżu innych usług, do których Twoje funkcje uzyskują dostęp. |
    | **Sieć wirtualna** | myResourceGroup-vnet | Sieć wirtualna, w której zostanie utworzony zasób Bastion w |
    | **Podsieci** | Usługa AzureBastionSubnet | Podsieć w sieci wirtualnej, do której zostanie wdrożony nowy zasób hosta bastionu. Należy utworzyć podsieć przy `AzureBastionSubnet`użyciu wartości nazwy . Ta wartość umożliwia platformie Azure wiedzieć, które podsieci wdrożyć zasoby bastionu. Należy użyć podsieci `/27` o rozmiarze `/26`co najmniej większym ( ,`/27`itd.). |

    > [!NOTE]
    > Aby uzyskać szczegółowy, krok po kroku przewodnik do tworzenia zasobu Bastion platformy Azure, zobacz [Tworzenie azure bastion hosta](../bastion/bastion-create-host-portal.md) samouczka.

4. Utwórz podsieć, w której platforma Azure może aprowizować hosta bastionu platformy Azure. Wybranie **opcji Zarządzaj konfiguracją podsieci** powoduje otwarcie nowego okienka, w którym można zdefiniować nową podsieć.  Wybierz **+ Podsieć,** aby utworzyć nową podsieć.
5. Podsieć musi być `AzureBastionSubnet` nazwa i prefiks podsieci musi być co najmniej `/27`.  Wybierz **przycisk OK,** aby utworzyć podsieć.

    >[!div class="mx-imgBorder"]
    >![Tworzenie podsieci dla hosta Bastion platformy Azure](./media/functions-create-private-site-access/create-bastion-subnet-2.png)

6. Na stronie **Tworzenie bastionu** wybierz `AzureBastionSubnet` nowo utworzone z listy dostępnych podsieci.

    >[!div class="mx-imgBorder"]
    >![Tworzenie hosta bastionu platformy Azure z określoną podsiecią](./media/functions-create-private-site-access/create-bastion-basics-2.png)

7. Wybierz **pozycję Recenzja & utwórz**. Po zakończeniu sprawdzania poprawności wybierz pozycję **Utwórz**. Utworzenie zasobu Bastion platformy Azure zajmie kilka minut.

## <a name="create-an-azure-functions-app"></a>Tworzenie aplikacji funkcji platformy Azure

Następnym krokiem jest utworzenie aplikacji funkcji na platformie Azure przy użyciu [planu zużycia](functions-scale.md#consumption-plan). Można wdrożyć kod funkcji do tego zasobu w dalszej części samouczka.

1. W portalu wybierz pozycję **Dodaj** u góry widoku grupy zasobów.
2. Wybierz **aplikację funkcji > obliczeń**
3. W sekcji **Podstawy** użyj ustawień aplikacji funkcji, jak określono w poniższej tabeli.

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **Grupa zasobów** | myResourceGroup | Wybierz grupę zasobów, aby zawierać wszystkie zasoby dla tego samouczka.  Korzystanie z tej samej grupy zasobów dla aplikacji funkcji i maszyny Wirtualnej ułatwia czyszczenie zasobów po zakończeniu pracy z tym samouczkiem. |
    | **Nazwa aplikacji funkcji** | Nazwa unikatowa w skali globalnej | Nazwa identyfikująca nową aplikację funkcji. Prawidłowe znaki to a-z (bez uwzględniania wielkości liter), 0-9 i -. |
    | **Publikowanie** | Code | Opcja publikowania plików kodu lub kontenera Docker. |
    | **Stos środowiska uruchomieniowego** | Preferowany język | Wybierz środowisko uruchomieniowe, które obsługuje ulubiony język programowania funkcji. |
    | **Region** | Północno-środkowe stany USA | Wybierz [region](https://azure.microsoft.com/regions/) w swojej okolicy lub w pobliżu innych usług, do których Twoje funkcje uzyskują dostęp. |

    Wybierz przycisk **Dalej: Hosting >.**
4. W sekcji **Hosting** wybierz odpowiednie **konto magazynu**, **System operacyjny**i **Zaplanuj** opisaną w poniższej tabeli.

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **Konto magazynu** | Nazwa unikatowa w skali globalnej | Utwórz konto magazynu używane przez aplikację funkcji. Nazwy kont usługi Magazyn muszą mieć długość od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery. Można również użyć istniejącego konta, które musi spełniać [wymagania konta magazynu](./functions-scale.md#storage-account-requirements). |
    | **System operacyjny** | Preferowany system operacyjny | System operacyjny jest wstępnie wybrany na podstawie wyboru stosu środowiska wykonawczego, ale w razie potrzeby można zmienić to ustawienie. |
    | **Planowanie** | Zużycie | [Plan hostingu](./functions-scale.md) określa, jak aplikacja funkcji jest skalowany i zasoby dostępne dla każdego wystąpienia. |
5. Wybierz **pozycję Przejrzyj + Utwórz,** aby przejrzeć wybrane ustawienia konfiguracji aplikacji.
6. Wybierz pozycję **Utwórz**, aby zaaprowizować aplikację funkcji i wdrożyć ją.

## <a name="configure-access-restrictions"></a>Konfigurowanie ograniczeń dostępu

Następnym krokiem jest skonfigurowanie [ograniczeń dostępu,](../app-service/app-service-ip-restrictions.md) aby upewnić się, że tylko zasoby w sieci wirtualnej mogą wywoływać tę funkcję.

Dostęp do [witryny prywatnej](functions-networking-options.md#private-site-access) jest włączona przez utworzenie [punktu końcowego usługi](../virtual-network/virtual-network-service-endpoints-overview.md) azure virtual network między aplikacją funkcji a określoną siecią wirtualną. Ograniczenia dostępu są implementowane za pośrednictwem punktów końcowych usługi. Punkty końcowe usługi zapewniają, że tylko ruch pochodzący z określonej sieci wirtualnej może uzyskać dostęp do wyznaczonego zasobu. W takim przypadku wyznaczony zasób jest funkcja azure.

1. W aplikacji funkcji przejdź do karty **Funkcje platformy.** **Networking** *Networking*
2. Strona **Stan funkcji sieciowych** jest punktem wyjścia do konfigurowania drzwiach frontowych platformy Azure, usługi Azure CDN, a także ograniczeń dostępu. Wybierz **pozycję Konfiguruj ograniczenia dostępu,** aby skonfigurować dostęp do witryny prywatnej.
3. Na stronie Ograniczenia dostępu widoczne są tylko **domyślne** ograniczenie. Wartość domyślna nie nakłada żadnych ograniczeń dotyczących dostępu do aplikacji funkcji.  Wybierz **pozycję Dodaj regułę,** aby utworzyć konfigurację ograniczeń dostępu do witryny prywatnej.
4. W okienku **Dodawanie ograniczeń dostępu** wybierz pozycję Sieć **wirtualna** z listy rozwijanej **Typ,** a następnie wybierz poprzednio utworzoną sieć wirtualną i podsieć.
5. Na stronie **Ograniczenia dostępu** jest teraz wyświetlane nowe ograniczenie. Zmiana **stanu punktu końcowego** może potrwać `Disabled` kilka `Provisioning` `Enabled`sekund.

    >[!IMPORTANT]
    > Każda aplikacja funkcji ma [witrynę Zaawansowane narzędzie (Kudu),](../app-service/app-service-ip-restrictions.md#scm-site) która służy do zarządzania wdrożeniami aplikacji funkcji. Ta witryna jest dostępna z `<FUNCTION_APP_NAME>.scm.azurewebsites.net`adresu URL, takiego jak: . Ponieważ ograniczenia dostępu nie zostały włączone w tej lokacji wdrażania, nadal można wdrożyć kod projektu z lokalnej stacji roboczej dewelopera lub usługi kompilacji bez konieczności aprowizowania agenta w sieci wirtualnej.

## <a name="access-the-functions-app"></a>Dostęp do aplikacji funkcji

1. Powrót do poprzednio utworzonej aplikacji funkcji.  W sekcji **Omówienie** skopiuj adres URL.

    >[!div class="mx-imgBorder"]
    >![Pobierz adres URL aplikacji Funkcji](./media/functions-create-private-site-access/access-function-overview.png)

2. Jeśli spróbujesz uzyskać dostęp do aplikacji funkcji teraz z komputera poza siecią wirtualną, otrzymasz http 403 strona wskazująca, że aplikacja jest zatrzymana.  Aplikacja nie zostanie zatrzymana. Odpowiedź jest rzeczywiście HTTP 403 IP Zakazany stan.
3. Teraz uzyskasz dostęp do funkcji z wcześniej utworzonej maszyny wirtualnej, która jest połączona z siecią wirtualną. Aby uzyskać dostęp do witryny z maszyny Wirtualnej, musisz połączyć się z maszyną wirtualną za pośrednictwem usługi Azure Bastion.  Najpierw wybierz **pozycję Połącz,** a następnie wybierz pozycję **Bastion**.
4. Podaj wymaganą nazwę użytkownika i hasło, aby zalogować się do maszyny wirtualnej.  Wybierz przycisk **Połącz**. Pojawi się nowe okno przeglądarki, aby umożliwić interakcję z maszyną wirtualną.
5. Ponieważ ta maszyna wirtualna uzyskuje dostęp do funkcji za pośrednictwem sieci wirtualnej, można uzyskać dostęp do witryny z przeglądarki sieci web na maszynie Wirtualnej.  Należy pamiętać, że podczas gdy aplikacja funkcji jest dostępna tylko z poziomu wyznaczonej sieci wirtualnej, publiczny wpis DNS pozostaje. Jak pokazano powyżej, próba uzyskania dostępu do witryny spowoduje odpowiedź HTTP 403.

## <a name="create-a-function"></a>Tworzenie funkcji

Następnym krokiem w tym samouczku jest utworzenie funkcji platformy Azure wyzwalanej przez protokół HTTP. Wywoływanie funkcji za pośrednictwem protokołu HTTP GET lub POST powinno spowodować odpowiedź "Hello, {name}".  

1. Postępuj zgodnie z jednym z następujących przewodników Szybki start, aby utworzyć i wdrożyć aplikację usługi Azure Functions.

    * [Kod programu Visual Studio](./functions-create-first-function-vs-code.md)
    * [Visual Studio](./functions-create-your-first-function-visual-studio.md)
    * [Wiersz polecenia](./functions-create-first-azure-function-azure-cli.md)
    * [Maven (Java)](./functions-create-first-java-maven.md)

2. Podczas publikowania projektu usługi Azure Functions wybierz zasób aplikacji funkcji, który został utworzony wcześniej w tym samouczku.
3. Sprawdź, czy funkcja jest wdrożona.

    >[!div class="mx-imgBorder"]
    >![Wdrożona funkcja na liście funkcji](./media/functions-create-private-site-access/verify-deployed-function.png)

## <a name="invoke-the-function-directly"></a>Wywołać funkcję bezpośrednio

1. Aby przetestować dostęp do funkcji, należy skopiować adres URL funkcji. Wybierz wdrożoną funkcję, a następnie wybierz **pozycję</> Pobierz adres URL funkcji**. Następnie kliknij przycisk **Kopiuj,** aby skopiować adres URL do schowka.

    >[!div class="mx-imgBorder"]
    >![Kopiowanie adresu URL funkcji](./media/functions-create-private-site-access/get-function-url.png)

    > [!NOTE]
    > Po uruchomieniu funkcji zostanie wyświetlony błąd środowiska uruchomieniowego w portalu informujący, że nie można uruchomić środowiska wykonawczego funkcji. Pomimo tekstu wiadomości, aplikacja funkcji jest rzeczywiście uruchomiona. Ten błąd jest wynikiem nowych ograniczeń dostępu, które uniemożliwiają portalowi wykonywanie zapytań w celu sprawdzenia środowiska wykonawczego.

2. Wklej adres URL do przeglądarki internetowej. Podczas próby uzyskania dostępu do aplikacji funkcji z komputera spoza sieci wirtualnej, otrzymasz odpowiedź HTTP 403 wskazującą, że aplikacja jest zatrzymana.

## <a name="invoke-the-function-from-the-virtual-network"></a>Wywoływanie funkcji z sieci wirtualnej

Uzyskiwanie dostępu do funkcji za pośrednictwem przeglądarki sieci web (przy użyciu usługi Azure Bastion) na skonfigurowaną maszynę wirtualną w sieci wirtualnej powoduje sukces!

>[!div class="mx-imgBorder"]
>![Dostęp do funkcji platformy Azure za pośrednictwem usługi Azure Bastion](./media/functions-create-private-site-access/access-function-via-bastion-final.png)

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Następne kroki


> [!div class="nextstepaction"]
> [Dowiedz się więcej o opcjach sieci w funkcji](./functions-networking-options.md)
