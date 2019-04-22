---
title: Wdrażanie Avere vFXT dla platformy Azure
description: Kroki, aby wdrożyć klaster vFXT Avere na platformie Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: v-erkell
ms.openlocfilehash: 7ded66c29f12b8f68746726ca6c126bffbc51f0d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59257318"
---
# <a name="deploy-the-vfxt-cluster"></a>Wdrażanie klastra vFXT

Ta procedura przeprowadzi Cię przez przy użyciu Kreatora wdrażania, które jest dostępne w portalu Azure Marketplace. Kreator automatycznie wdroży klaster przy użyciu szablonu usługi Azure Resource Manager. Po wprowadzeniu parametrów w postaci i kliknij przycisk **Utwórz**, Azure automatycznie wykonuje następujące czynności:

* Tworzy kontroler klastra, który jest podstawowa maszyna wirtualna, zawierający oprogramowanie niezbędne do wdrażania i zarządzania klastrem.
* Konfiguruje grupę zasobów i infrastruktury sieci wirtualnej, w tym tworzenie nowych elementów.
* Tworzy klaster maszyn wirtualnych węzła i konfiguruje je jako klaster Avere.
* Jeśli jest to wymagane, tworzy nowy kontener obiektów Blob platformy Azure i konfiguruje je jako filtr podstawowych klastra.

Po wykonaniu instrukcji w tym dokumencie, masz sieci wirtualnej, podsieci, kontrolera i klaster vFXT, jak pokazano na poniższym diagramie. Ten diagram przedstawia opcjonalny filtr podstawowych obiektów Blob platformy Azure, która obejmuje nowy kontener magazynu obiektów Blob (w nowe konto magazynu, nie wyświetlane) i punkt końcowy usługi dla usługi Microsoft storage wewnątrz tej podsieci. 

![Diagram przedstawiający trzy prostokąty koncentrycznych Avere składniki klastra. Ramka zewnętrzna jest oznaczona etykietą "Grupy zasobów" i zawiera sześciokąt z etykietą "Blob storage (opcjonalnie)". Dalej prostokąta w jest oznaczona etykietą "sieć wirtualna: 10.0.0.0/16 "i nie zawiera żadnych unikatowych składników. Najbardziej wewnętrzny jest oznaczona etykietą "Subnet:10.0.0.0/24" i zawiera maszynę Wirtualną etykietą "Controller klastra", stos trzech maszyn wirtualnych z etykietą "vFXT węzłów (vFXT klastra)" i sześciokąt z etykietą "Punkt końcowy usługi". Brak Strzałka połączenie z punktem końcowym usługi, (która znajduje się wewnątrz podsieci) i magazynu obiektów blob (czyli poza podsiecią i siecią wirtualną, w grupie zasobów). Strzałkę przechodzi przez podsieci i granice sieci wirtualnej.](media/avere-vfxt-deployment.png)  

Przed użyciem szablonu tworzenia, upewnij się, że zostały rozwiązane te wymagania wstępne:  

1. [Nowa subskrypcja](avere-vfxt-prereqs.md#create-a-new-subscription)
1. [Uprawnienia właściciela do subskrypcji](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
1. [Limit przydziału dla klastra vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
1. [Punkt końcowy usługi magazynu (w razie potrzeby)](avere-vfxt-prereqs.md#create-a-storage-service-endpoint-in-your-virtual-network-if-needed) — jest to wymagane do wdrażania za pomocą istniejącej sieci wirtualnej i tworzenia usługi blob storage

Aby uzyskać więcej informacji na temat kroków wdrażania klastra i planowania, przeczytaj [Planowanie systemu vFXT Avere](avere-vfxt-deploy-plan.md) i [Przegląd wdrażania](avere-vfxt-deploy-overview.md).

## <a name="create-the-avere-vfxt-for-azure"></a>Tworzenie Avere vFXT dla platformy Azure

Dostęp do szablonu tworzenia w witrynie Azure portal, wyszukując Avere i wybierając pozycję "Avere vFXT dla szablonu Azure ARM". 

![Okno przeglądarki, przedstawiające witryny Azure portal za pomocą chleb smutek, "Nowe > Marketplace > wszystko". W obszarze strony i pole wyszukiwania ma wszystko "avere", a drugi element wyniku, "Avere vFXT dla szablonu Azure ARM" jest opisany w kolorze czerwonym, aby go zaznaczyć.](media/avere-vfxt-template-choose.png)

Po przeczytaniu szczegółowe informacje dotyczące vFXT Avere szablonu ARM Azure strony, kliknij przycisk **Utwórz** do rozpoczęcia. 

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

* **Subskrypcja** — Wybierz subskrypcję, Avere vFXT. 

* **Grupa zasobów** — wybierz istniejącą grupę zasobów pusty Avere vFXT klastra, lub kliknij przycisk "Utwórz nową" i wprowadź nową nazwę grupy zasobów. 

* **Lokalizacja** — wybierz lokalizację platformy Azure do klastra i zasobów.

Kliknij przycisk **OK** po zakończeniu. 

> [!NOTE]
> Jeśli chcesz mieć publicznego adresu IP kontrolera klastra, należy utworzyć nową sieć wirtualną dla klastra, zamiast zaznaczania istniejącej sieci. To ustawienie jest włączone drugiej strony.

## <a name="page-two-parameters---vfxt-cluster-information"></a>Informacje o klastrze strony dwa parametry — vFXT

Na drugiej stronie Szablon wdrożenia umożliwia ustawianie rozmiaru klastra, typ węzła, rozmiar pamięci podręcznej i parametry magazynu, wśród innych ustawień. 

![Druga Strona Szablon wdrożenia](media/avere-vfxt-deploy-2.png)

* **Liczba węzłów klastra vFXT Avere** — wybierz liczbę węzłów do użycia w klastrze. Wartość minimalna to trzy węzły, a maksymalna to dwanaście. 

* **Hasło administracyjnej klastra** — Utwórz hasło dla administrowania klastrami. To hasło, które będą używane z nazwą użytkownika ```admin``` do logowania się w Panelu sterowania klastra, do monitorowania klastra i skonfigurować ustawienia.

* **Nazwa klastra vFXT Avere** -nadaj unikatową nazwę klastra. 

* **Rozmiar** — w tej sekcji przedstawiono typ maszyny Wirtualnej, który będzie używany dla węzłów klastra. Mimo że istnieje tylko jedna opcja zalecana, **Zmień rozmiar** łącze powoduje otwarcie tabeli ze szczegółowymi informacjami na temat tego typu wystąpienia oraz link do Kalkulatora cen.  

* **Rozmiar każdego węzła w pamięci podręcznej** -klastra pamięci podręcznej jest rozłożona się między węzłami klastra, więc całkowity rozmiar pamięci podręcznej w klastrze vFXT Avere będą rozmiar pamięci podręcznej w każdym węźle pomnożona przez liczbę węzłów. 

  Jest to zalecana konfiguracja na potrzeby 4 TB w każdym węźle Standard_E32s_v3 węzłów.

* **Sieć wirtualna** — zdefiniować nową sieć wirtualną do przechowywania klastra lub wybieranie istniejącej sieci wirtualnej, która spełnia wymagania wstępne opisane w [Planowanie systemu vFXT Avere](avere-vfxt-deploy-plan.md#resource-group-and-network-infrastructure). 

  > [!NOTE]
  > Jeśli tworzysz nową sieć wirtualną, kontroler klastra będzie miał publiczny adres IP, dzięki czemu mają dostęp do nowej sieci prywatnej. Jeśli wybierzesz istniejącej sieci wirtualnej, kontroler klaster jest skonfigurowany bez publicznego adresu IP. 
  > 
  > Widoczna publicznie adresu IP na kontrolerze klastra zapewnia łatwiejszy dostęp do klastra vFXT, ale tworzy mały zagrożenie bezpieczeństwa. 
  >  * Publiczny adres IP na kontrolerze klastra umożliwia przy użyciu go jako hosta szybkie łączenie z klastrem vFXT Avere z poza podsieci prywatnej.
  >  * Jeśli nie skonfigurowano publiczny adres IP na kontrolerze, musisz podać innego hosta szybkie, połączenie sieci VPN lub usługi ExpressRoute do dostępu do klastra. Na przykład utworzyć kontroler sieci wirtualnej, który ma już skonfigurowane połączenie sieci VPN.
  >  * Jeśli utworzysz kontroler z publicznym adresem IP, należy chronić maszyny Wirtualnej kontrolera z sieciową grupą zabezpieczeń. Domyślnie vFXT Avere wdrożenia platformy Azure tworzy sieciową grupę zabezpieczeń i ograniczenie dostępu dla ruchu przychodzącego na tylko port 22 dla kontrolerów z publicznych adresów IP. System można dodatkowo zabezpieczyć, blokując szczegółów dostępu do sieci zakres adresów IP, źródłowy — oznacza to, Zezwalaj na połączenia tylko z komputerów, które mają być używane dla dostępu do klastra.

  Wdróż szablon konfiguruje również nową sieć wirtualną z punktu końcowego usługi storage dla usługi Azure Blob storage i ograniczona do tylko adresy IP z podsieci klastra kontroli dostępu do sieci. 

* **Podsieci** — Wybierz podsieć w istniejącej sieci wirtualnej lub utworzyć nową. 

* **Tworzenie i używanie usługi blob storage** — wybierz **true** Aby utworzyć nowy kontener obiektów Blob platformy Azure i skonfigurować go jako magazynu zaplecza dla nowego klastra vFXT Avere. Powoduje także utworzenie nowego konta magazynu w ramach tej samej grupie zasobów klastra i punkt końcowy usługi magazynowania firmy Microsoft w podsieci klastra. 
  
  Jeśli podasz istniejącą sieć wirtualną, musi mieć punktu końcowego usługi magazynu, przed utworzeniem klastra. (Aby uzyskać więcej informacji, przeczytaj [Planowanie systemu vFXT Avere](avere-vfxt-deploy-plan.md).)

  Ustaw to pole **false** Jeśli nie chcesz utworzyć nowy kontener. W takim przypadku należy dołączyć i skonfigurować Magazyn po utworzeniu klastra. Odczyt [skonfigurować magazyn](avere-vfxt-add-storage.md) instrukcje. 

* **(Nowe) Konto magazynu** — w przypadku utworzenia nowego kontenera obiektów Blob platformy Azure, wprowadź nazwę dla nowego konta magazynu. 

## <a name="validation-and-purchase"></a>Sprawdzanie poprawności i możliwości zakupu

Strona 3 przedstawiono podsumowanie konfiguracji i sprawdza poprawność parametrów. Po weryfikacji zakończy się pomyślnie, kliknij przycisk **OK** przycisk, aby kontynuować. 

![Trzeciej stronie Szablon wdrożenia — sprawdzanie poprawności](media/avere-vfxt-deploy-3.png)

Na stronie czterech wprowadź wszystkie wymagane informacje kontaktowe, a następnie kliknij przycisk **Utwórz** przycisk, aby zaakceptować warunki i utworzyć vFXT Avere klastra platformy Azure. 

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

## <a name="next-step"></a>Następny krok

Teraz, gdy klaster działa, i znasz jego adres IP zarządzania, możesz [nawiązać połączenie za pomocą narzędzia konfiguracji klastra](avere-vfxt-cluster-gui.md) Aby włączyć obsługę, Dodaj magazyn, jeśli potrzebne i Dostosuj inne ustawienia klastra.
