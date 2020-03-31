---
title: Wdrażanie usługi Avere vFXT dla platformy Azure
description: Kroki wdrażania klastra Avere vFXT na platformie Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: e70d1dfebcf25ee8f4e90a062cee6dd72a663e02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252599"
---
# <a name="deploy-the-vfxt-cluster"></a>Wdrażanie klastra vFXT

Ta procedura przeprowadzi Cię za pomocą kreatora wdrażania dostępnego w portalu Azure Marketplace. Kreator automatycznie wdraża klaster przy użyciu szablonu usługi Azure Resource Manager. Po wprowadzeniu parametrów w formularzu i kliknięciu **przycisku Utwórz**platforma Azure automatycznie wykona następujące zadania:

* Tworzy kontroler klastra, który jest podstawową maszyną wirtualną, która zawiera oprogramowanie potrzebne do wdrażania klastra i zarządzania nim.
* Konfiguruje grupę zasobów i infrastrukturę sieci wirtualnej, w tym tworzenie nowych elementów.
* Tworzy maszyny wirtualne węzła klastra i konfiguruje je jako klaster Avere.
* Jeśli jest to wymagane, tworzy nowy kontener obiektów blob platformy Azure i konfiguruje go jako filer rdzenia klastra.

Po postępowaniu zgodnie z instrukcjami zawartymi w tym dokumencie będzie sieć wirtualna, podsieć, kontroler klastra i klaster vFXT, jak pokazano na poniższym diagramie. Ten diagram przedstawia opcjonalny plik podstawowy obiektów Blob platformy Azure, który zawiera nowy kontener magazynu obiektów Blob (w nowym koncie magazynu, nie pokazano) i punkt końcowy usługi dla magazynu firmy Microsoft wewnątrz podsieci.

![diagram przedstawiający trzy koncentryczne prostokąty ze składnikami klastra Avere. Zewnętrzny prostokąt jest oznaczony jako "Grupa zasobów" i zawiera sześciokąt z etykietą "Magazyn obiektów blob (opcjonalnie)". Następny prostokąt w jest oznaczony jako "Sieć wirtualna: 10.0.0.0/16" i nie zawiera żadnych unikatowych składników. Najbardziej wewnętrzny prostokąt jest oznaczony jako "Podsieć:10.0.0.0/24" i zawiera maszynę wirtualną z etykietą "Kontroler klastra", stos trzech maszyn wirtualnych oznaczonych jako "węzły vFXT (klaster vFXT)" i sześciokąt z etykietą "Punkt końcowy usługi". Istnieje strzałka łącząca punkt końcowy usługi (który znajduje się wewnątrz podsieci) i magazynu obiektów blob (który znajduje się poza podsiecią i siecią wirtualną w grupie zasobów). Strzałka przechodzi przez granice podsieci i sieci wirtualnej.](media/avere-vfxt-deployment.png)

Przed użyciem szablonu tworzenia upewnij się, że zostały rozwiązane następujące wymagania wstępne:  

* [Nowa subskrypcja](avere-vfxt-prereqs.md#create-a-new-subscription)
* [Uprawnienia właściciela subskrypcji](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
* [Przydział dla klastra vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
* [Punkt końcowy usługi magazynowania (w razie potrzeby)](avere-vfxt-prereqs.md#create-a-storage-service-endpoint-in-your-virtual-network-if-needed) — wymagane dla wdrożeń korzystających z istniejącej sieci wirtualnej i tworzenia magazynu obiektów blob

Aby uzyskać więcej informacji na temat kroków wdrażania klastra i planowania, przeczytaj [omówienie](avere-vfxt-deploy-overview.md)planowania systemu i wdrożenia [systemu VFXT Avere](avere-vfxt-deploy-plan.md) .

## <a name="create-the-avere-vfxt-for-azure"></a>Tworzenie avere vFXT dla platformy Azure

Uzyskaj dostęp do szablonu tworzenia w witrynie Azure portal, wyszukując avere i wybierając "Avere vFXT for Azure ARM Template".

![Okno przeglądarki z witryną Azure portal z okruchami chleba "Nowy > Marketplace > wszystko". Na stronie Wszystko pole wyszukiwania ma termin "avere", a drugi wynik ,,Avere vFXT for Azure ARM Template" jest opisany na czerwono, aby go wyróżnić.](media/avere-vfxt-template-choose.png)

Po przeczytaniu szczegółów na stronie Avere vFXT for Azure ARM Template, kliknij jego **przycisk Utwórz,** aby rozpocząć.

![Platforma Azure marketplace z pierwszą stroną szablonu wdrożenia z](media/avere-vfxt-deploy-first.png)

Szablon jest podzielony na cztery kroki - dwie strony zbierania informacji, a także kroki weryfikacji i potwierdzenia.

* Strona pierwsza zbiera ustawienia maszyny Wirtualnej kontrolera klastra.
* Strona druga zbiera parametry do tworzenia klastra i dodatkowe zasoby, takie jak podsieci i magazynu.
* Strona trzecia podsumowuje wybory i sprawdza poprawność konfiguracji.
* Strona czwarta wyjaśnia warunki oprogramowania i umożliwia rozpoczęcie procesu tworzenia klastra.

## <a name="page-one-parameters---cluster-controller-information"></a>Parametry strony pierwszej — informacje o kontrolerze klastra

Pierwsza strona szablonu wdrażania koncentruje się na kontrolerze klastra.

![Pierwsza strona szablonu wdrożenia](media/avere-vfxt-deploy-1.png)

Wypełnij następujące informacje:

* **Nazwa kontrolera klastra** — umożliwia ustawienie nazwy maszyny Wirtualnej kontrolera klastra.

* **Nazwa użytkownika kontrolera** — umożliwia ustawienie głównej nazwy użytkownika maszyny Wirtualnej kontrolera klastra.

* **Typ uwierzytelniania** — wybierz uwierzytelnianie za pomocą hasła lub klucza publicznego SSH do łączenia się z kontrolerem. Zalecana jest metoda klucza publicznego SSH; przeczytaj [Jak tworzyć i używać kluczy SSH,](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) jeśli potrzebujesz pomocy.

* **Hasło** lub **klucz publiczny SSH** — w zależności od wybranego typu uwierzytelniania w następnych polach należy podać klucz publiczny RSA lub hasło. To poświadczenie jest używane z nazwą użytkownika podana wcześniej.

* **Subskrypcja** — wybierz subskrypcję dla Avere vFXT.

* **Grupa zasobów** — wybierz istniejącą pustą grupę zasobów dla klastra VFXT Avere lub kliknij przycisk "Utwórz nowe" i wprowadź nową nazwę grupy zasobów.

* **Lokalizacja** — wybierz lokalizację platformy Azure dla klastra i zasobów.

Po zakończeniu kliknij **przycisk OK.**

> [!NOTE]
> Jeśli chcesz, aby kontroler klastra miał publiczny adres IP, utwórz nową sieć wirtualną dla klastra zamiast wybierania istniejącej sieci. To ustawienie znajduje się na drugiej stronie.

## <a name="page-two-parameters---vfxt-cluster-information"></a>Parametry strony drugiej — informacje o klastrze vFXT

Druga strona szablonu wdrożenia umożliwia ustawienie rozmiaru klastra, typu węzła, rozmiaru pamięci podręcznej i parametrów magazynu, między innymi.

![Druga strona szablonu wdrożenia](media/avere-vfxt-deploy-2.png)

* **Liczba węzłów klastra Avere vFXT** — wybierz liczbę węzłów w klastrze. Minimum to trzy węzły, a maksymalna to dwanaście.

* **Hasło administracyjne klastra** — tworzenie hasła do administrowania klastrem. To hasło jest używane ```admin``` z nazwą użytkownika do logowania się do panelu sterowania klastra, gdzie można monitorować klaster i konfigurować ustawienia klastra.

* **Nazwa klastra Avere vFXT** — nadaj klastrowi unikatową nazwę.

* **Rozmiar** — w tej sekcji przedstawiono typ maszyny Wirtualnej, który będzie używany dla węzłów klastra. Chociaż istnieje tylko jedna zalecana opcja, link **Zmień rozmiar** otwiera tabelę ze szczegółami dotyczącymi tego typu wystąpienia i łączem do kalkulatora cen.

* **Rozmiar pamięci podręcznej na węzeł** — pamięć podręczna klastra jest rozłożona na węzły klastra, więc całkowity rozmiar pamięci podręcznej w klastrze VFXT Avere będzie ten rozmiar pomnożony przez liczbę węzłów.

  Zalecana konfiguracja: Użyj 4 TB na węzeł dla Standard_E32s_v3 węzłów.

* **Sieć wirtualna** — zdefiniuj nową sieć wirtualną, aby pomieścić klaster, lub wybierz istniejącą sieć, która spełnia wymagania wstępne opisane w [zaplanuj system VFXT Avere](avere-vfxt-deploy-plan.md#subscription-resource-group-and-network-infrastructure).

  > [!NOTE]
  > Jeśli utworzysz nową sieć wirtualną, kontroler klastra będzie miał publiczny adres IP, dzięki czemu można uzyskać dostęp do nowej sieci prywatnej. Jeśli wybierzesz istniejącą sieć wirtualną, kontroler klastra jest konfigurowany bez publicznego adresu IP.
  >
  > Publicznie widoczny adres IP na kontrolerze klastra zapewnia łatwiejszy dostęp do klastra vFXT, ale stwarza niewielkie zagrożenie bezpieczeństwa.
  >* Publiczny adres IP na kontrolerze klastra umożliwia użycie go jako hosta szybkiego dostępu do łączenia się z klastrem VFXT Avere spoza podsieci prywatnej.
  >* Jeśli nie masz publicznego adresu IP na kontrolerze, potrzebujesz innego hosta szybkiego dostępu, połączenia sieci VPN lub usługi ExpressRoute, aby uzyskać dostęp do klastra. Na przykład użyj istniejącej sieci wirtualnej, która ma już skonfigurowane połączenie sieci VPN.
  >* Jeśli tworzysz kontroler z publicznym adresem IP, należy chronić maszynę wirtualną kontrolera za pomocą sieciowej grupy zabezpieczeń. Domyślnie wdrożenie avere vFXT for Azure tworzy sieciową grupę zabezpieczeń, która ogranicza dostęp przychodzący tylko do portu 22 dla kontrolerów z publicznymi adresami IP. Można dodatkowo chronić system, blokując dostęp do zakresu adresów źródłowych IP — oznacza to, że zezwalaj tylko na połączenia z maszyn, których zamierzasz używać do uzyskiwania dostępu do klastra.

  Nowa sieć wirtualna jest również skonfigurowana z punktem końcowym usługi magazynu dla magazynu obiektów Blob platformy Azure i z zablokowaną kontrolą dostępu do sieci, aby zezwalać tylko na adresy IP z podsieci klastra.

* **Podsieć** — wybierz podsieć lub utwórz nową.

* **Tworzenie i używanie magazynu obiektów blob** — wybierz **true,** aby utworzyć nowy kontener obiektów Blob platformy Azure i skonfigurować go jako magazyn zaplecza dla nowego klastra VFXT Avere. Ta opcja tworzy również nowe konto magazynu w grupie zasobów klastra i tworzy punkt końcowy usługi magazynu firmy Microsoft wewnątrz podsieci klastra.
  
  Jeśli podasz istniejącą sieć wirtualną, przed utworzeniem klastra musi ona mieć punkt końcowy usługi magazynu. (Aby uzyskać więcej informacji, przeczytaj [artykuł Planowanie systemu VFXT Avere.)](avere-vfxt-deploy-plan.md)

  Ustaw to pole na **false,** jeśli nie chcesz tworzyć nowego kontenera. W takim przypadku należy dołączyć i skonfigurować magazyn po utworzeniu klastra. Przeczytaj [pozycję Konfiguruj magazyn,](avere-vfxt-add-storage.md) aby uzyskać instrukcje.

* **(Nowe) Konto magazynu** — jeśli tworzysz nowy kontener obiektów blob platformy Azure, wprowadź nazwę nowego konta magazynu.

## <a name="validation-and-purchase"></a>Walidacja i zakup

Strona trzecia podsumowuje konfigurację i sprawdza poprawność parametrów. Po pomyślnym zakończeniu sprawdzania poprawności sprawdź podsumowanie i kliknij przycisk **OK.**

> [!TIP]
> Ustawienia tworzenia tego klastra można zapisać, klikając łącze **Pobierz szablon i parametry** obok przycisku **OK.** Te informacje mogą być przydatne, jeśli trzeba utworzyć podobny klaster później — na przykład, aby utworzyć klaster zastępczy w scenariuszu odzyskiwania po awarii. (Przeczytaj [wskazówki dotyczące odzyskiwania po awarii,](disaster-recovery.md) aby dowiedzieć się więcej).

![Trzecia strona szablonu wdrożenia — sprawdzanie poprawności](media/avere-vfxt-deploy-3.png)

Strona czwarta zawiera warunki użytkowania i linki do informacji o prywatności i cenach.

Wprowadź brakujące informacje kontaktowe, a następnie kliknij przycisk **Utwórz,** aby zaakceptować warunki i utworzyć klaster Avere vFXT dla platformy Azure.

![Czwarta strona szablonu wdrożenia - warunki, przycisk utwórz](media/avere-vfxt-deploy-4.png)

Wdrożenie klastra trwa 15-20 minut.

## <a name="gather-template-output"></a>Zbieranie danych wyjściowych szablonu

Po zakończeniu tworzenia klastra przez szablon Avere vFXT wyprowadza ważne informacje o nowym klastrze.

> [!TIP]
> Pamiętaj, aby skopiować **adres IP zarządzania** z danych wyjściowych szablonu. Ten adres jest potrzebny do administrowania klastrem.

Aby znaleźć informacje:

1. Przejdź do grupy zasobów dla kontrolera klastra.

1. Po lewej stronie kliknij pozycję **Wdrożenia**, a następnie **szablon microsoft-avere.vfxt**.

   ![Strona portalu grupy zasobów z zaznaczonymi wdrożeniami po lewej stronie i szablonem microsoft-avere.vfxt wyświetlanym w tabeli pod nazwą wdrożenia](media/avere-vfxt-outputs-deployments.png)

1. Po lewej stronie kliknij pozycję **Wyjścia**. Skopiuj wartości w każdym z pól.

   ![strona wyjścia z wartościami SSHSTRING, RESOURCE_GROUP, LOCATION, NETWORK_RESOURCE_GROUP, NETWORK, SUBNET, SUBNET_ID, VSERVER_IPs i MGMT_IP w polach po prawej stronie etykiet](media/avere-vfxt-outputs-values.png)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy klaster jest uruchomiony i znasz jego adres IP zarządzania, [połącz się z narzędziem konfiguracji klastra](avere-vfxt-cluster-gui.md).

Użyj interfejsu konfiguracji, aby dostosować klaster, w tym następujące zadania konfiguracji:

* [Włącz obsługę](avere-vfxt-enable-support.md)
* [Dodaj magazyn](avere-vfxt-add-storage.md) (w razie potrzeby)
