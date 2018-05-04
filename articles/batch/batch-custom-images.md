---
title: Zapewnij pule partii zadań Azure z niestandardowymi obrazami | Dokumentacja firmy Microsoft
description: Można utworzyć plik wsadowy puli z niestandardowego obrazu, aby udostępnić obliczeniowe węzłów, które zawierają oprogramowanie i dane potrzebne do aplikacji. Niestandardowe obrazy są wydajnym sposobem konfigurowania węzłów obliczeniowych do uruchamiania obciążeń partii.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 04/23/2018
ms.author: danlep
ms.openlocfilehash: 78bc50a1189d8f42281f81643a5e907d94480082
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="use-a-managed-custom-image-to-create-a-pool-of-virtual-machines"></a>Użyj zarządzanego obrazu niestandardowego do utworzenia puli maszyn wirtualnych 

Podczas tworzenia puli partii zadań Azure za pomocą konfiguracji maszyny wirtualnej, należy określić obraz maszyny Wirtualnej, który zawiera system operacyjny na każdym węźle obliczeń w puli. Obraz portalu Azure Marketplace, albo niestandardowego obrazu (obraz maszyny Wirtualnej zostanie utworzony i skonfigurowany samodzielnie), można utworzyć puli maszyn wirtualnych. Niestandardowy obraz musi być *zarządzanego obrazu* zasobu w tej samej subskrypcji platformy Azure i regionu co konto usługi partia zadań.

## <a name="why-use-a-custom-image"></a>Dlaczego warto używać niestandardowego obrazu?
Po podaniu niestandardowego obrazu jest mają kontrolę nad konfiguracji systemu operacyjnego i typ systemu operacyjnego i dysków z danymi do użycia. Obraz niestandardowy może zawierać aplikacji i danych referencyjnych, które staną się dostępne we wszystkich węzłach puli partii natychmiast po ich udostępnieniu.

Przy użyciu niestandardowego obrazu zaoszczędzić czas, w celu przygotowania węzły obliczeniowe z puli do uruchomienia obciążenie partii. Można użyć obrazu portalu Azure Marketplace i instalować oprogramowanie na każdy węzeł obliczeniowy po zainicjowaniu obsługi administracyjnej, może być bardziej wydajne przy użyciu niestandardowego obrazu.

Przy użyciu niestandardowego obrazu skonfigurowany dla danego scenariusza zapewniają wiele korzyści:

- **Konfigurowanie systemu operacyjnego (OS)**. Specjalnej konfiguracji systemu operacyjnego można wykonywać na niestandardowego obrazu dysku systemu operacyjnego. 
- **Przed rozpoczęciem instalacji aplikacji.** Można utworzyć niestandardowy obraz z wstępnie zainstalowane aplikacje na dysku systemu operacyjnego, który jest bardziej wydajny i mniej podatne na błędy niż instalowania aplikacji po zainicjowaniu obsługi administracyjnej węzły obliczeniowe przy użyciu StartTask.
- **Zaoszczędzić czas ponownego uruchomienia na maszynach wirtualnych.** Aplikacji zwykle wymaga ponownego uruchomienia maszyny Wirtualnej, który jest czasochłonne. Aby zaoszczędzić czas ponownego uruchomienia, należy wstępnie instalowania aplikacji. 
- **Skopiuj raz bardzo dużych ilości danych.** Możesz wprowadzić dane statyczne część zarządzanego obrazu niestandardowego kopiując go do zarządzanego obrazu dysków z danymi. To należy wykonać jeden raz i tylko udostępnia danych do każdego węzła w puli.
- **Wybór dysków.** Można utworzyć obraz niestandardowy zarządzany z dysku VHD z dyskiem zarządzanym maszyny Wirtualnej platformy Azure, migawkę tych dysków lub własnego systemu Linux lub Windows instalacji, który został skonfigurowany. Masz wybór za pomocą magazyn w warstwie premium dla dysku systemu operacyjnego i dysk z danymi.
- **Zwiększaj pul do dowolnej wielkości.** Gdy używasz zarządzanego obrazu niestandardowego można utworzyć puli do dowolnej wielkości, zażądać może zwiększyć się pula. Nie trzeba utworzyć kopii obiektu blob obrazu wirtualne dyski twarde, aby pomieścić liczby maszyn wirtualnych. 


## <a name="prerequisites"></a>Wymagania wstępne

- **Zasobu zarządzanego obrazu**. Aby utworzyć puli maszyn wirtualnych przy użyciu niestandardowego obrazu, należy utworzyć zasobu zarządzanego obrazu w tej samej subskrypcji platformy Azure i regionu co konto usługi partia zadań. Dla opcji przygotować się do zarządzanego obrazu zobacz sekcję poniżej.
- **Uwierzytelnianie usługi Azure Active Directory (AAD)**. Interfejs API klienta wsadowym należy użyć uwierzytelniania usługi AAD. Azure partii obsługę usługi AAD jest udokumentowany w [rozwiązań usług uwierzytelniania partii z usługą Active Directory](batch-aad-auth.md).

    
## <a name="prepare-a-custom-image"></a>Przygotuj obraz niestandardowy
Można przygotować zarządzanego obrazu z dysku VHD, z maszyny Wirtualnej platformy Azure z dyskami zarządzanych lub migawki maszyny Wirtualnej. Dla partii zaleca się utworzenie zarządzanego obrazu z maszyny Wirtualnej z dysków zarządzanych lub migawki maszyny Wirtualnej. Zarządzanego obrazu źródłowego zasobu powinny istnieć dla pul na skalowanie w górę i może zostać usunięta po usunięciu puli. 

Podczas przygotowywania obrazu, należy pamiętać następujące kwestie:

* Upewnij się, że podstawowy obraz systemu operacyjnego używany do aprowizowania pul usługi Batch nie ma wstępnie zainstalowanych rozszerzeń platformy Azure, takich jak rozszerzenie skryptu niestandardowego. Jeśli obraz zawiera wstępnie zainstalowane rozszerzenie, platforma Azure może napotkać problemy podczas wdrażania maszyny wirtualnej.
* Upewnij się, czy podstawowy obraz systemu operacyjnego, podane przez użytkownika używa domyślnej dysku tymczasowego. Agenta węzła partii oczekuje obecnie domyślnym dysku tymczasowym.
* Nie można usunąć zasobu zarządzanego obrazu przywoływanego przez pulę partii przez czas ich istnienia puli. Jeśli zasobu zarządzanego obrazu zostanie usunięty, a następnie nie może zwiększyć się pula wszelkie dodatkowe. 

### <a name="to-create-a-managed-image"></a>Aby utworzyć obraz zarządzanych
Wszystkie istniejące przygotowane systemu Windows lub Linux dysku systemu operacyjnego służy do tworzenia zarządzanego obrazu. Na przykład jeśli chcesz użyć lokalnego obrazu, następnie przekaż dysku do konta usługi Azure Storage, który znajduje się w tej samej subskrypcji i regionu jako konta usługi partia zadań przy użyciu narzędzia AzCopy lub innego narzędzia przekazywania. Aby uzyskać szczegółowe kroki w celu przekazania dysku VHD i utworzyć obraz zarządzanych, zobacz wskazówki dotyczące [Windows](../virtual-machines/windows/upload-generalized-managed.md) lub [Linux](../virtual-machines/linux/upload-vhd.md) maszyn wirtualnych.

Można również przygotować zarządzanego obrazu z nowej lub istniejącej maszyny Wirtualnej platformy Azure lub migawki maszyny Wirtualnej. 

* W przypadku tworzenia nowej maszyny Wirtualnej, można użyć obrazu portalu Azure Marketplace jako obrazu podstawowego do zarządzanego obrazu, a następnie dostosować go. 

* Jeśli planowane jest przechwycenie obrazu przy użyciu portalu, upewnij się, że maszyna wirtualna zostanie utworzona z dyskiem zarządzanym. Jest to domyślne ustawienie magazynu podczas tworzenia maszyny Wirtualnej.

* Gdy maszyna wirtualna jest uruchomiona, się z nim połączyć za pośrednictwem protokołu RDP (dla systemu Windows) lub protokołu SSH (dla systemu Linux). Zainstaluj oprogramowanie niezbędne skopiować żądanych danych, a następnie Uogólnij maszyny Wirtualnej.  

Dla kroki w celu uogólnienia maszyny Wirtualnej platformy Azure i utworzyć obraz zarządzanych, zobacz wskazówki dotyczące [Windows](../virtual-machines/windows/capture-image-resource.md) lub [Linux](../virtual-machines/linux/capture-image.md) maszyn wirtualnych.

W zależności od tego, jak zaplanować tworzenie puli partii z obrazem należy następujący identyfikator obrazu:

* Jeśli planujesz tworzenie puli za pomocą obrazu przy użyciu interfejsów API partii, **identyfikator zasobu** obrazu, który ma postać `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`. 
* Jeśli zamierzasz korzystać z portalu **nazwa** obrazu. 





## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Utwórz pulę z niestandardowego obrazu w portalu

Po zapisaniu niestandardowego obrazu i znasz jego nazwie lub identyfikatorze zasobu, można utworzyć puli partii z tego obrazu. Poniższe kroki pokazują, jak utworzyć pulę z portalu Azure.

> [!NOTE]
> W przypadku tworzenia puli przy użyciu jednego z interfejsów API partii, upewnij się, że tożsamość, którą można używać do uwierzytelniania usługi AAD ma uprawnienia do zasobu obrazu. Zobacz [rozwiązań usług uwierzytelniania partii z usługą Active Directory](batch-aad-auth.md).
>

1. W witrynie Azure Portal przejdź do swojego konta usługi Batch. To konto musi być w tej samej subskrypcji i regionu jako grupa zasobów, zawierający obraz niestandardowy. 
2. W **ustawienia** oknie po lewej stronie, wybierz opcję **pule** elementu menu.
3. W **pule** wybierz **Dodaj** polecenia.
4. Na **Dodaj pulę,** wybierz **niestandardowego obrazu (Linux/system Windows)** z **typ obrazu** listy rozwijanej. Z **obrazu maszyny Wirtualnej niestandardowe** listy rozwijanej, wybierz nazwę obrazu (forma krótka/identyfikatora zasobu).
5. Wybierz poprawny **wydawcy lub oferta/jednostki Sku** dla niestandardowego obrazu.
6. Określ pozostałe wymagane ustawienia, w tym **rozmiaru węzła**, **Target dedykowanych węzłów**, i **niski priorytet węzłów**, a także wszelkie potrzebne ustawienia opcjonalne.

    Na przykład w przypadku obraz niestandardowy Microsoft Windows Server Datacenter 2016 **Dodaj pulę,** zostanie wyświetlone okno, jak pokazano poniżej:

    ![Dodaj pulę z niestandardowego obrazu systemu Windows](media/batch-custom-images/add-pool-custom-image.png)
  
Aby sprawdzić, czy istniejącej puli jest oparta na niestandardowego obrazu, zobacz **systemu operacyjnego** właściwości w sekcji Podsumowanie zasobów z **puli** okna. Jeśli w puli została utworzona z obrazu niestandardowego, jest równa **niestandardowego obrazu maszyny Wirtualnej**.

Wszystkie obrazy niestandardowe skojarzone z pulą są wyświetlane w puli **właściwości** okna.
 
## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać szczegółowe informacje o partii, zobacz [Programowanie równoległe na dużą skalę obliczeniowe rozwiązań w partii](batch-api-basics.md).
