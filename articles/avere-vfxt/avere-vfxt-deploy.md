---
title: Wdrażanie Avere vFXT dla platformy Azure
description: Kroki, aby wdrożyć klaster vFXT Avere na platformie Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: da329b5c50fe7c39d9773743b40c2f990e298963
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55296379"
---
# <a name="deploy-the-vfxt-cluster"></a>Wdrażanie klastra vFXT

Ta procedura przeprowadzi Cię przez przy użyciu Kreatora wdrażania, które jest dostępne w portalu Azure Marketplace. Kreator automatycznie wdroży klaster przy użyciu szablonu usługi Azure Resource Manager. Po wprowadzeniu parametrów w postaci i kliknij przycisk **Utwórz**, Azure automatycznie wykonuje następujące czynności: 

* Utwórz kontroler klastra, który jest podstawowa maszyna wirtualna, zawierający oprogramowanie niezbędne do wdrażania i zarządzania klastrem.
* Skonfiguruj grupę zasobów i infrastruktury sieci wirtualnej, w tym tworzenie nowych elementów, w razie potrzeby.
* Tworzenie klastra maszyn wirtualnych węzła i skonfiguruj je jako Avere klastra.
* Jeśli jest to wymagane, tworzeniu nowego kontenera obiektów Blob platformy Azure i skonfigurować go jako filtr podstawowych klastra.

Po wykonaniu instrukcji w tym dokumencie, będziesz mieć sieć wirtualną, podsieć, kontrolera i klaster vFXT, jak pokazano na poniższym diagramie:

![Diagram przedstawiający sieć wirtualna, zawierający opcjonalne blob storage i w podsieci zawierającej trzy pogrupowane maszyn wirtualnych z etykietą vFXT węzły/vFXT klastra i jeden kontroler etykietami klastra maszyny Wirtualnej](media/avere-vfxt-deployment.png)

Po utworzeniu klastra, wykonaj następujące czynności [Tworzenie punktu końcowego magazynu](#create-a-storage-endpoint-if-using-azure-blob) w Twojej sieci wirtualnej, jeśli za pomocą magazynu obiektów Blob. 

Przed użyciem szablonu tworzenia, upewnij się, że zostały rozwiązane te wymagania wstępne:  

1. [Nowa subskrypcja](avere-vfxt-prereqs.md#create-a-new-subscription)
1. [Uprawnienia właściciela do subskrypcji](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
1. [Limit przydziału dla klastra vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
1. [Role niestandardowe dostępu](avere-vfxt-prereqs.md#create-access-roles) -musi utworzyć rolę kontroli dostępu opartej na rolach można przypisać do węzłów klastra. Użytkownik może również utworzyć rolę niestandardowego dostępu dla kontrolera klastra, ale większość użytkowników potrwa roli właściciela domyślnej, która zapewnia uprawnienia kontrolera odpowiadającego do właściciela grupy zasobów. Odczyt [wbudowane role zasobów platformy Azure](../role-based-access-control/built-in-roles.md#owner) Aby uzyskać więcej szczegółów.

Aby uzyskać więcej informacji na temat kroków wdrażania klastra i planowania, przeczytaj [Planowanie systemu vFXT Avere](avere-vfxt-deploy-plan.md) i [Przegląd wdrażania](avere-vfxt-deploy-overview.md).

## <a name="create-the-avere-vfxt-for-azure"></a>Tworzenie Avere vFXT dla platformy Azure

Dostęp do szablonu tworzenia w witrynie Azure portal, wyszukując Avere i wybierając pozycję "Avere vFXT wdrożenia platformy Azure". <!-- xxx update if that name changes xxx --> 

<!-- **[XXX need production image of template deploy in search and/or entry page of template deploy XXX]** -->

Kliknij przycisk **Utwórz** do rozpoczęcia. 

![Portal Azure marketplace z pierwszej strony przedstawiający Szablon wdrożenia](media/avere-vfxt-deploy-first.png)

Szablon jest podzielony na cztery kroki - stron dwóch zbierania informacji oraz sprawdzania poprawności i potwierdzenie kroki. 

* Pierwszej strony koncentruje się na ustawień kontrolera klastra maszyny Wirtualnej. 
* Drugiej strony gromadzi informacje o parametry w celu utworzenia klastra i skojarzonych zasobów, takie jak podsieci i magazyn. 
* Strona 3 zawiera podsumowanie ustawień i weryfikuje konfigurację. 
* Strona 4 opisano oprogramowania warunków i postanowień i umożliwia rozpoczęcie procesu tworzenia klastra. 

## <a name="page-one-parameters---cluster-controller-information"></a>Stronie jeden parametrów - informacje o kontrolerze klastra

Na pierwszej stronie Szablon wdrożenia zbiera informacje o kontrolerze klastra. 

![Pierwsza strona Szablon wdrożenia](media/avere-vfxt-deploy-1.png)

Wypełnij następujące informacje:

* **Nazwa kontrolera klastra** — Ustaw nazwę kontrolera klastra maszyny Wirtualnej.

* **Nazwa użytkownika kontrolera** — Wypełnij głównej nazwy użytkownika dla kontrolera klastra maszyny Wirtualnej. 

* **Typ uwierzytelniania** — Wybierz hasło lub uwierzytelnianie klucza publicznego SSH do łączenia się z kontrolerem. Zaleca się metoda klucza publicznego SSH; Przeczytaj [sposób tworzenia i używania kluczy SSH](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) Jeśli potrzebujesz pomocy.

* **Hasło** lub **klucz publiczny SSH** — w zależności od wybranego typu uwierzytelniania, użytkownik musi podać klucz publiczny RSA lub hasło w polach dalej. To poświadczenie jest używane z nazwą użytkownika podany wcześniej.

* **Identyfikator roli tworzenia klastra Avere** — Użyj tego pola, aby określić roli kontroli dostępu dla kontrolera klastra. Wartość domyślna to wbudowana rola [właściciela](../role-based-access-control/built-in-roles.md#owner). Uprawnienia właściciela dla kontrolera klastra są ograniczone do grupy zasobów klastra. 

  Należy użyć globalnie unikatowy identyfikator, który odnosi się do roli. Wartość domyślna (Owner) identyfikator GUID jest 8e3af657-a8ff-443c-a75c-2fe8c4bcb635. Aby znaleźć identyfikator GUID rolę niestandardową, użyj tego polecenia: 

  ```azurecli
  az role definition list --query '[*].{roleName:roleName, name:name}' -o table --name 'YOUR ROLE NAME'
  ```

* **Subskrypcja** — Wybierz subskrypcję, Avere vFXT. 

* **Grupa zasobów** — wybierz grupę zasobów klastra vFXT Avere, lub kliknij przycisk "Utwórz nową" i wprowadź nową nazwę grupy zasobów. 

* **Lokalizacja** — wybierz lokalizację platformy Azure do klastra i zasobów.

Kliknij przycisk **OK** po zakończeniu. 

> [!NOTE]
> Jeśli chcesz mieć publicznego adresu IP kontrolera klastra, należy utworzyć nową sieć wirtualną dla klastra, zamiast zaznaczania istniejącej sieci. To ustawienie jest włączone drugiej strony.

## <a name="page-two-parameters---vfxt-cluster-information"></a>Informacje o klastrze strony dwa parametry — vFXT

Na drugiej stronie Szablon wdrożenia umożliwia ustawianie rozmiaru klastra, typ węzła, rozmiar pamięci podręcznej i parametry magazynu, wśród innych ustawień. 

![Druga Strona Szablon wdrożenia](media/avere-vfxt-deploy-2.png)

* **Liczba węzłów klastra vFXT Avere** — wybierz liczbę węzłów do użycia w klastrze. Wartość minimalna to trzy węzły, a maksymalna to dwanaście. 

* **Hasło administracyjnej klastra** — Utwórz hasło dla administrowania klastrami. To hasło, które będą używane z nazwą użytkownika ```admin``` do logowania się w Panelu sterowania klastra, do monitorowania klastra i skonfigurować ustawienia.

* **Rola operacji klastra Avere** — Określ nazwę roli kontroli dostępu dla węzłów klastra. Jest to rolę niestandardową, która została utworzona w kroku wymagań wstępnych. 

  Przykład opisanego w [tworzenie roli dostęp do węzła klastra](avere-vfxt-prereqs.md#create-the-cluster-node-access-role) zapisuje plik jako ```avere-operator.json``` i odpowiadające im nazwy roli ```avere-operator```.

* **Nazwa klastra vFXT Avere** -nadaj unikatową nazwę klastra. 

* **Rozmiar** — Określ typ maszyny Wirtualnej do użycia podczas tworzenia węzłów klastra. 

* **Rozmiar każdego węzła w pamięci podręcznej** -klastra pamięci podręcznej jest rozłożona się między węzłami klastra, więc całkowity rozmiar pamięci podręcznej w klastrze vFXT Avere będą rozmiar pamięci podręcznej w każdym węźle pomnożona przez liczbę węzłów. 

  Zalecana konfiguracja jest używać 1 TB na węzeł, jeśli za pomocą Standard_D16s_v3 węzły klastra, a do używania 4 TB na węzeł, jeśli przy użyciu Standard_E32s_v3 węzłów.

* **Sieć wirtualna** — Wybieranie istniejącej sieci wirtualnej do przechowywania klastra lub zdefiniować nową sieć wirtualną do utworzenia. 

  > [!NOTE]
  > Jeśli tworzysz nową sieć wirtualną, kontroler klastra będzie miał publiczny adres IP, dzięki czemu mają dostęp do nowej sieci prywatnej. Jeśli wybierzesz istniejącej sieci wirtualnej, kontroler klaster jest skonfigurowany bez publicznego adresu IP. 
  > 
  > Widoczna publicznie adresu IP na kontrolerze klastra zapewnia łatwiejszy dostęp do klastra vFXT, ale tworzy mały zagrożenie bezpieczeństwa. 
  >  * Publiczny adres IP na kontrolerze klastra umożliwia przy użyciu go jako hosta szybkie łączenie z klastrem vFXT Avere z poza podsieci prywatnej.
  >  * Jeśli nie skonfigurowano publiczny adres IP na kontrolerze, musisz podać innego hosta szybkie, połączenie sieci VPN lub usługi ExpressRoute do dostępu do klastra. Na przykład utworzyć kontroler sieci wirtualnej, który ma już skonfigurowane połączenie sieci VPN.
  >  * Jeśli utworzysz kontroler z publicznym adresem IP, należy chronić maszyny Wirtualnej kontrolera z sieciową grupą zabezpieczeń. Domyślnie vFXT Avere wdrożenia platformy Azure tworzy sieciową grupę zabezpieczeń i ograniczenie dostępu dla ruchu przychodzącego na tylko port 22 dla kontrolerów z publicznych adresów IP. System można dodatkowo zabezpieczyć, blokując szczegółów dostępu do sieci zakres adresów IP, źródłowy — oznacza to, Zezwalaj na połączenia tylko z komputerów, które mają być używane dla dostępu do klastra.

* **Podsieci** — Wybierz podsieć w istniejącej sieci wirtualnej lub utworzyć nową. 

* **Używać magazynu obiektów blob** — wybierz, czy należy utworzyć nowy kontener obiektów Blob platformy Azure i skonfigurować go jako magazynu zaplecza dla nowego klastra vFXT Avere. Jeśli zdecydujesz się utworzyć nowy kontener, musisz podać konto magazynu dla tego kontenera. Jeśli wybierzesz nie utworzyć nowy kontener obiektów blob, należy dołączyć magazynu po utworzeniu klastra (odczyt [skonfigurować magazyn](avere-vfxt-add-storage.md) instrukcje). Ustaw to pole **false** Jeśli nie chcesz utworzyć nowy kontener.

* **Konto magazynu** — w przypadku utworzenia nowego kontenera obiektów Blob platformy Azure, wprowadź nazwę konta magazynu. Konto magazynu musi być standardowe konto ogólnego przeznaczenia V2 skonfigurowane przy użyciu magazynu lokalnie nadmiarowego i warstwy dostępu gorąca. [Skonfigurować magazyn](avere-vfxt-add-storage.md#azure-storage-cloud-core-filer) artykuł zawiera szczegółowe informacje o wymaganiach dotyczących konta magazynu.

## <a name="validation-and-purchase"></a>Sprawdzanie poprawności i możliwości zakupu

Strona 3 zawiera podsumowanie konfiguracji i sprawdza poprawność parametrów. Po weryfikacji zakończy się pomyślnie, kliknij przycisk **OK** przycisk, aby kontynuować. 

![Trzeciej stronie Szablon wdrożenia — sprawdzanie poprawności](media/avere-vfxt-deploy-3.png)

Na stronie czterech kliknij **Utwórz** przycisk, aby zaakceptować warunki i utworzyć vFXT Avere klastra platformy Azure. 

![Przycisk Utwórz czwartej stronie Szablon wdrożenia — warunki i postanowienia,](media/avere-vfxt-deploy-4.png)

Wdrożenie klastra trwa 15 – 20 minut.

## <a name="gather-template-output"></a>Zebranie danych wyjściowych szablonu

Po zakończeniu tworzenia klastra w szablonie vFXT Avere generuje niektóre ważne informacje na temat nowego klastra. 

> [!TIP]
> Upewnij się skopiować adres IP zarządzania z danych wyjściowych szablonu. Potrzebujesz tego adresu do administrowania klastrem.

Aby znaleźć te informacje, wykonaj poniższą procedurę:

1. Przejdź do grupy zasobów dla kontrolera sieci klastra.

1. Po lewej stronie kliknij pozycję **wdrożeń**, a następnie **microsoft avere.vfxt szablonów**.

   ![Grupa zasobów strony portalu wdrożeń wybranej na po lewej stronie i microsoft-avere.vfxt-template wyświetlane w tabeli pod nazwą wdrożenia](media/avere-vfxt-outputs-deployments.png)

1. Po lewej stronie kliknij pozycję **dane wyjściowe**. Skopiuj wartości z każdego pola. 

   ![generuje stronę przedstawiający SSHSTRING RESOURCE_GROUP, lokalizacji, NETWORK_RESOURCE_GROUP, sieci, PODSIECI, SUBNET_ID, VSERVER_IPs i MGMT_IP wartości pól z prawej strony etykiety](media/avere-vfxt-outputs-values.png)


## <a name="create-a-storage-endpoint-if-using-azure-blob"></a>Tworzenie punktu końcowego magazynu (w przypadku korzystania z obiektów Blob platformy Azure)

Jeśli używasz usługi Azure Blob storage do obsługi magazynu danych zaplecza, należy utworzyć punkt końcowy usługi magazynu w sieci wirtualnej. To [punktu końcowego usługi](../virtual-network/virtual-network-service-endpoints-overview.md) utrzymuje ruchu w usłudze Azure Blob lokalnego zamiast przesyłać go przez internet.

1. Z poziomu portalu, kliknij przycisk **sieci wirtualne** po lewej stronie.
1. Wybierz sieć wirtualną dla kontrolera. 
1. Kliknij przycisk **punkty końcowe usługi** po lewej stronie.
1. Kliknij przycisk **Dodaj** u góry.
1. Pozostaw usługę jako ``Microsoft.Storage`` i wybierz podsieć kontrolera.
1. Kliknij u dołu, **Dodaj**.

  ![Azure portal zrzut ekranu z adnotacjami, aby uzyskać instrukcje tworzenia punktu końcowego usługi](media/avere-vfxt-service-endpoint.png)

## <a name="next-step"></a>Następny krok

Teraz, gdy klaster działa, i znasz jego adres IP zarządzania, możesz [nawiązać połączenie za pomocą narzędzia konfiguracji klastra](avere-vfxt-cluster-gui.md) Aby włączyć obsługę, Dodaj magazyn, jeśli potrzebne i Dostosuj inne ustawienia klastra.
