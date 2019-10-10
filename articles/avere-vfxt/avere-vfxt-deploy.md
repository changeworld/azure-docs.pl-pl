---
title: Wdrażanie avere vFXT dla platformy Azure
description: Procedura wdrażania klastra avere vFXT na platformie Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: rohogue
ms.openlocfilehash: 6ddf950bf2d138a94675ee394109a0d227ea206b
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255457"
---
# <a name="deploy-the-vfxt-cluster"></a>Wdrażanie klastra vFXT

Ta procedura przeprowadzi Cię przez użytkownika za pomocą Kreatora wdrażania dostępnego w portalu Azure Marketplace. Kreator automatycznie wdraża klaster przy użyciu szablonu Azure Resource Manager. Po wprowadzeniu parametrów w formularzu i kliknięciu przycisku **Utwórz**platforma Azure automatycznie wykonuje następujące czynności:

* Tworzy kontroler klastra, który jest podstawową maszyną wirtualną, która zawiera oprogramowanie wymagające wdrożenia klastra i zarządzania nim.
* Konfiguruje grupę zasobów i infrastrukturę sieci wirtualnej, w tym tworzenie nowych elementów.
* Tworzy maszyny wirtualne węzła klastra i konfiguruje je jako klaster avere.
* Jeśli jest to wymagane, program tworzy nowy kontener obiektów blob platformy Azure i konfiguruje go jako podstawowy plik klastra.

Po wykonaniu instrukcji przedstawionych w tym dokumencie będzie dostępna sieć wirtualna, podsieć, kontroler i klaster vFXT, jak pokazano na poniższym diagramie. Ten diagram przedstawia opcjonalny plik usługi Azure Blob Core, który obejmuje nowy kontener usługi BLOB Storage (na nowym koncie magazynu, niepokazywany) i punkt końcowy usług magazynu Microsoft Storage w podsieci. 

![Diagram przedstawiający trzy prostokąty koncentryczne ze składnikami klastra avere. Zewnętrzny prostokąt ma etykietę "Grupa zasobów" i zawiera sześciokąt oznaczony etykietą "BLOB Storage (opcjonalnie)". Następny prostokąt w jest oznaczony etykietą "Sieć wirtualna: 10.0.0.0/16" i nie zawiera żadnych unikatowych składników. Wewnętrzny prostokąt ma etykietę "Subnet: 10.0.0.0/24" i zawiera maszynę wirtualną o nazwie "kontroler klastra", stos trzech maszyn wirtualnych z etykietą "vFXT nodes (vFXT cluster)" i "punkt końcowy usługi". Istnieje strzałka łącząca punkt końcowy usługi (znajdujący się wewnątrz podsieci) i magazyn obiektów BLOB (znajdujący się poza podsiecią i siecią wirtualną w grupie zasobów). Strzałka przechodzi przez granice podsieci i sieci wirtualnej.](media/avere-vfxt-deployment.png)  

Przed użyciem szablonu tworzenia upewnij się, że zostały spełnione następujące wymagania wstępne:  

1. [Nowa subskrypcja](avere-vfxt-prereqs.md#create-a-new-subscription)
1. [Uprawnienia właściciela subskrypcji](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
1. [Przydział dla klastra vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
1. [Punkt końcowy usługi magazynu (w razie potrzeby)](avere-vfxt-prereqs.md#create-a-storage-service-endpoint-in-your-virtual-network-if-needed) — wymagany do wdrożenia przy użyciu istniejącej sieci wirtualnej i tworzenia magazynu obiektów BLOB

Aby uzyskać więcej informacji o krokach i planowaniu wdrożenia klastra, przeczytaj artykuł Planowanie systemu i [wdrożenia](avere-vfxt-deploy-overview.md) [programu avere vFXT](avere-vfxt-deploy-plan.md) .

## <a name="create-the-avere-vfxt-for-azure"></a>Tworzenie avere vFXT dla platformy Azure

Aby uzyskać dostęp do szablonu tworzenia w Azure Portal, należy wyszukać avere i wybrać pozycję "avere vFXT dla szablonu Azure ARM". 

![Okno przeglądarki zawierające Azure Portal z chleb Crumbs "nowe > Marketplace > wszystko". Na stronie wszystko, w polu wyszukiwania znajduje się termin "avere" i drugi wynik, "avere vFXT for Azure ARM template" został wyróżniony kolorem czerwonym, aby go zaznaczyć.](media/avere-vfxt-template-choose.png)

Po przeczytaniu szczegółów na stronie szablon avere vFXT for Azure ARM kliknij pozycję **Utwórz** , aby rozpocząć. 

![Portal Azure Marketplace z pierwszą stroną szablonu wdrożenia](media/avere-vfxt-deploy-first.png)

Szablon jest podzielony na cztery kroki — dwie strony zbierające informacje, a także kroki weryfikacji i potwierdzenia. 

* Jedna strona koncentruje się na ustawieniach maszyny wirtualnej kontrolera klastra. 
* Druga strona zbiera parametry służące do tworzenia klastra i skojarzonych zasobów, takich jak podsieci i magazyn. 
* Na trzy strony podsumowuje ustawienia i weryfikuje konfigurację. 
* Strona cztery objaśnia warunki użytkowania oprogramowania i umożliwia rozpoczęcie procesu tworzenia klastra. 

## <a name="page-one-parameters---cluster-controller-information"></a>Strona z jednym parametrem — informacje o kontrolerze klastra

Na pierwszej stronie szablonu wdrożenia są zbierane informacje o kontrolerze klastra. 

![Pierwsza strona szablonu wdrożenia](media/avere-vfxt-deploy-1.png)

Wprowadź następujące informacje:

* **Nazwa kontrolera klastra** — Ustaw nazwę maszyny wirtualnej kontrolera klastra.

* **Nazwa użytkownika kontrolera** — wprowadź nazwę użytkownika root dla maszyny wirtualnej kontrolera klastra. 

* **Typ uwierzytelniania** — wybierz opcję hasło lub uwierzytelnianie klucza publicznego SSH w celu nawiązania połączenia z kontrolerem. Zalecana jest metoda klucza publicznego SSH; Jeśli potrzebujesz pomocy, przeczytaj artykuł [jak utworzyć klucze SSH i jak z nich korzystać](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) .

* **Hasło** lub **klucz publiczny SSH** — w zależności od wybranego typu uwierzytelniania należy podać klucz publiczny RSA lub hasło w następnych polach. To poświadczenie jest używane z podaną wcześniej nazwą użytkownika.

* **Subskrypcja** — wybierz subskrypcję usługi avere vFXT. 

* **Grupa zasobów** — wybierz istniejącą pustą grupę zasobów dla klastra avere vFXT lub kliknij pozycję "Utwórz nową" i wprowadź nową nazwę grupy zasobów. 

* **Lokalizacja** — wybierz lokalizację platformy Azure dla klastra i zasobów.

Po zakończeniu kliknij przycisk **OK** . 

> [!NOTE]
> Jeśli chcesz, aby kontroler klastra miał publiczny adres IP, Utwórz nową sieć wirtualną dla klastra zamiast wybierania istniejącej sieci. To ustawienie znajduje się na stronie dwie.

## <a name="page-two-parameters---vfxt-cluster-information"></a>Strona dwa parametry — informacje o klastrze vFXT

Druga strona szablonu wdrożenia umożliwia ustawienie rozmiaru klastra, typu węzła, rozmiaru pamięci podręcznej i parametrów magazynu między innymi ustawieniami. 

![Druga strona szablonu wdrożenia](media/avere-vfxt-deploy-2.png)

* **Liczba węzłów klastra avere vFXT** — wybierz liczbę węzłów, które mają być używane w klastrze. Minimalna wartość to trzy węzły, a wartość maksymalna to 12. 

* **Hasło do administrowania klastrem** — Utwórz hasło do administrowania klastrem. To hasło będzie używane z nazwą użytkownika ```admin```, aby zalogować się do panelu sterowania klastra w celu monitorowania klastra i konfigurowania ustawień.

* **Avere vFXT Nazwa klastra** — nadaj klastrowi unikatową nazwę. 

* **Rozmiar** — Ta sekcja zawiera typ maszyny wirtualnej, który będzie używany w węzłach klastra. Mimo że istnieje tylko jedna zalecana opcja, link **Zmień rozmiar** otwiera tabelę ze szczegółowymi informacjami o tym typie wystąpienia i linkiem do kalkulatora cen.  

* **Rozmiar pamięci podręcznej na węzeł** — pamięć podręczna klastra jest rozłożona na węzły klastra, więc łączny rozmiar pamięci podręcznej w klastrze avere vFXT będzie rozmiar pamięci podręcznej na węzeł pomnożony przez liczbę węzłów. 

  Zalecaną konfiguracją jest użycie 4 TB na węzeł dla węzłów Standard_E32s_v3.

* **Sieć wirtualna** — Zdefiniuj nową sieć wirtualną do przechowywania klastra lub wybierz istniejącą sieć wirtualną spełniającą wymagania wstępne opisane w temacie [Planowanie systemu avere vFXT](avere-vfxt-deploy-plan.md#resource-group-and-network-infrastructure). 

  > [!NOTE]
  > Jeśli utworzysz nową sieć wirtualną, kontroler klastra będzie miał publiczny adres IP, aby można było uzyskać dostęp do nowej sieci prywatnej. W przypadku wybrania istniejącej sieci wirtualnej kontroler klastra zostanie skonfigurowany bez publicznego adresu IP. 
  > 
  > Publicznie widoczny adres IP na kontrolerze klastra zapewnia łatwiejszy dostęp do klastra vFXT, ale tworzy niewielkie zagrożenie bezpieczeństwa. 
  >  * Publiczny adres IP na kontrolerze klastra umożliwia korzystanie z niego jako hosta skoku w celu nawiązania połączenia z klastrem usługi avere vFXT spoza podsieci prywatnej.
  >  * Jeśli na kontrolerze nie zostanie skonfigurowany publiczny adres IP, należy użyć innego hosta skoku, połączenia sieci VPN lub ExpressRoute, aby uzyskać dostęp do klastra. Na przykład utwórz kontroler w sieci wirtualnej, dla której skonfigurowano już połączenie sieci VPN.
  >  * Jeśli tworzysz kontroler z publicznym adresem IP, należy chronić maszynę wirtualną kontrolera przy użyciu sieciowej grupy zabezpieczeń. Domyślnie wdrożenie avere vFXT for Azure tworzy sieciową grupę zabezpieczeń i ogranicza dostęp przychodzący do portu 22 dla kontrolerów z publicznymi adresami IP. Aby zapewnić lepszą ochronę systemu, można zablokować dostęp do zakresu adresów IP źródła, czyli zezwalać na połączenia tylko z maszyn, które mają być używane do dostępu do klastra.

  Szablon wdrożenia konfiguruje również nową sieć wirtualną z punktem końcowym usługi magazynu dla magazynu obiektów blob platformy Azure i z funkcją kontroli dostępu do sieci zadostępną tylko do adresów IP z podsieci klastra. 

* **Podsieć** — wybierz podsieć z istniejącej sieci wirtualnej lub Utwórz nową. 

* **Tworzenie i Używanie magazynu obiektów BLOB** — wybierz **wartość true** , aby utworzyć nowy kontener obiektów blob platformy Azure i skonfigurować go jako magazyn zaplecza dla nowego klastra avere vFXT. Ta opcja powoduje także utworzenie nowego konta magazynu w ramach tej samej grupy zasobów co klaster i punkt końcowy usługi magazynu firmy Microsoft w podsieci klastra. 
  
  W przypadku podania istniejącej sieci wirtualnej przed utworzeniem klastra musi ona mieć punkt końcowy usługi magazynu. (Aby uzyskać więcej informacji, przeczytaj temat [Planowanie systemu avere vFXT](avere-vfxt-deploy-plan.md)).

  Jeśli nie chcesz tworzyć nowego kontenera, należy ustawić **wartość false** dla tego pola. W takim przypadku należy dołączyć i skonfigurować magazyn po utworzeniu klastra. Przeczytaj temat [Konfigurowanie magazynu](avere-vfxt-add-storage.md) , aby uzyskać instrukcje. 

* **(Nowe) konto magazynu** — Jeśli tworzysz nowy kontener obiektów blob platformy Azure, wprowadź nazwę nowego konta magazynu. 

## <a name="validation-and-purchase"></a>Sprawdzanie poprawności i zakup

Na trzy strony podsumowuje konfigurację i sprawdza poprawność parametrów. Po pomyślnym zakończeniu walidacji kliknij przycisk **OK** , aby wykonać operację. 

![Trzecia strona szablonu wdrożenia — Walidacja](media/avere-vfxt-deploy-3.png)

Na stronie cztery wprowadź wymagane informacje kontaktowe, a następnie kliknij przycisk **Utwórz** , aby zaakceptować warunki i utworzyć avere vFXT dla klastra platformy Azure. 

![Czwarta Strona szablonu wdrożenia — warunki i postanowienia, przycisk Utwórz](media/avere-vfxt-deploy-4.png)

Wdrożenie klastra trwa 15-20 minut.

## <a name="gather-template-output"></a>Zbierz dane wyjściowe szablonu

Po zakończeniu tworzenia klastra przez szablon avere vFXT należy uzyskać pewne ważne informacje o nowym klastrze. 

> [!TIP]
> Upewnij się, że adres IP zarządzania jest kopiowany z szablonu wyjściowego. Ten adres jest potrzebny do administrowania klastrem.

Aby znaleźć te informacje, wykonaj następującą procedurę:

1. Przejdź do grupy zasobów dla kontrolera klastra.

1. Po lewej stronie kliknij pozycje **wdrożenia**, a następnie pozycję **Microsoft-avere. vfxt-Template**.

   ![Strona portalu grupy zasobów z wdrożeniami wybranymi po lewej stronie i Microsoft-avere. vfxt-Template pokazanych w tabeli pod nazwą wdrożenia](media/avere-vfxt-outputs-deployments.png)

1. Po lewej stronie kliknij pozycję dane **wyjściowe**. Skopiuj wartości w każdym z pól. 

   ![na stronie dane wyjściowe wyświetlane są wartości SSHSTRING, RESOURCE_GROUP, LOCATION, NETWORK_RESOURCE_GROUP, NETWORK, SUBNET, SUBNET_ID, VSERVER_IPs i MGMT_IP w polach po prawej stronie etykiet](media/avere-vfxt-outputs-values.png)

## <a name="next-step"></a>Następny krok

Teraz, gdy klaster działa i znasz jego adres IP zarządzania, możesz [połączyć się z narzędziem konfiguracji klastra](avere-vfxt-cluster-gui.md) , aby włączyć obsługę, dodać magazyn, jeśli to konieczne, i dostosować inne ustawienia klastra.
