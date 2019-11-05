---
title: 'Szybki Start: Tworzenie DSVM systemu Windows'
description: Skonfiguruj i Utwórz Data Science Virtual Machine na platformie Azure na potrzeby analiz i uczenia maszynowego.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 09/10/2019
ms.openlocfilehash: afc026ccb602538b0749fa294cee763efa3d27d0
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73483237"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-windows"></a>Szybki Start: Konfigurowanie Data Science Virtual Machine dla systemu Windows

Zacznij korzystać z Data Science Virtual Machine systemu Windows.

## <a name="prerequisite"></a>Wymagania wstępne

Aby utworzyć Data Science Virtual Machine systemu Windows, musisz mieć subskrypcję platformy Azure. [Wypróbuj bezpłatnie platformę Azure](https://azure.com/free).
Zanotuj bezpłatne konta platformy Azure nie obsługują jednostek SKU maszyn wirtualnych obsługujących procesor GPU.

## <a name="create-your-dsvm"></a>Tworzenie DSVM

Aby utworzyć wystąpienie DSVM:

1. Przejdź do [Azure Portal](https://portal.azure.com) może zostać wyświetlony monit o zalogowanie się do konta platformy Azure, jeśli jeszcze nie zalogowano się.
1. Znajdź listę maszyn wirtualnych, wpisując ciąg "maszyna wirtualna do nauki o danych" i wybierając pozycję "Data Science Virtual Machine-Windows 2016".

    ![Lista maszyn wirtualnych z systemem Windows](./media/provision-vm/search-windows.png)

1. Wybierz przycisk **Utwórz** u dołu.

    [![](media/provision-vm/create-windows.png "Button to create a Windows machine")](media/provision-vm/create-windows-expanded.png#lightbox)

1. Należy przekierować do bloku "Tworzenie maszyny wirtualnej".
   Karta podstawy ![odpowiadająca maszynie wirtualnej z systemem Windows](./media/provision-vm/review-create-windows.png)

1. Wypełnij kartę **podstawowe** :
      * **Subskrypcja**: Jeśli masz więcej niż jedną subskrypcję, wybierz tę, w której zostanie utworzona i rozliczona. Musisz mieć uprawnienia do tworzenia zasobów dla tej subskrypcji.
      * **Grupa zasobów**: Utwórz nową grupę lub Użyj istniejącej.
      * **Nazwa maszyny wirtualnej**: Wprowadź nazwę maszyny wirtualnej. Jest to sposób ich wyświetlania w Azure Portal.
      * **Lokalizacja**: wybierz najbardziej odpowiednie centrum danych. Aby uzyskać najszybszy dostęp do sieci, znajduje się w centrum danych, które ma najwięcej z nich lub znajduje się najbliżej fizycznej lokalizacji. Dowiedz się więcej o [regionach platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/).
      * **Obraz**: pozostaw wartość domyślną.
      * **Rozmiar**: powinno to być automatycznie wypełniane rozmiarem odpowiednim dla ogólnych obciążeń. Dowiedz się więcej o [rozmiarach maszyn wirtualnych z systemem Windows na platformie Azure](../../virtual-machines/windows/sizes.md).
      * **Nazwa użytkownika**: Wprowadź nazwę użytkownika administratora. Jest to nazwa użytkownika, która będzie używana do logowania się do maszyny wirtualnej i nie musi być taka sama jak nazwa użytkownika platformy Azure.
      * **Hasło**: wprowadź hasło, które będzie używane do logowania się do maszyny wirtualnej.    
1. Wybierz pozycję **Przegląd + utwórz**.
1. **Przegląd + tworzenie**
   * Sprawdź, czy wszystkie wprowadzone informacje są poprawne. 
   * Wybierz pozycję **Utwórz**.


> [!NOTE]
> * Nie są naliczane opłaty licencyjne za oprogramowanie, które jest wstępnie załadowane na maszynę wirtualną. Koszt obliczeń jest płacony dla rozmiaru serwera wybranego w kroku **rozmiar** .
> * Inicjowanie obsługi trwa od 10 do 20 minut. Stan maszyny wirtualnej można wyświetlić na Azure Portal.

## <a name="access-the-dsvm"></a>Dostęp do DSVM

Po utworzeniu maszyny wirtualnej i zainicjowaniu jej obsługi postępuj zgodnie z instrukcjami wyświetlanymi w celu [nawiązania połączenia z maszyną wirtualną opartą na platformie Azure](../../marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm.md). Użyj poświadczeń konta administratora, które zostały skonfigurowane w kroku **podstawowe informacje** o tworzeniu maszyny wirtualnej. 

Możesz zacząć korzystać z narzędzi, które są zainstalowane i skonfigurowane na maszynie wirtualnej. Do wielu narzędzi można uzyskać dostęp za pomocą kafelków menu **Start** i ikon pulpitu.

Możesz również dołączyć DSVM do Azure Notebooks uruchamiania notesów Jupyter na maszynie wirtualnej i pomijania ograniczeń warstwy Bezpłatna usługa. Aby uzyskać więcej informacji, zobacz [Zarządzanie i Konfigurowanie projektów notesów](../../notebooks/configure-manage-azure-notebooks-projects.md#manage-and-configure-projects).

<a name="tools"></a>


## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z narzędziami w DSVM, otwierając menu **Start** .
* Dowiedz się więcej na temat Azure Machine Learning, odczytując informacje o tym, [co jest Azure Machine Learning?](../service/overview-what-is-azure-ml.md) i wypróbować [samouczki](../index.yml).
* W Eksploratorze plików przejdź do folderu C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts for Samples, które używają biblioteki kolekcję funkcji revoscaler w języku R, która obsługuje analizę danych w skali przedsiębiorstwa. 
* Zapoznaj się z artykułem [dziesięć rzeczy, które można wykonać na Data Science Virtual Machine](https://aka.ms/dsvmtenthings).
* Dowiedz się, jak kompleksowo kompilować kompleksowe rozwiązania analityczne przy użyciu [zespołowego procesu nauki o danych](../team-data-science-process/index.yml).
* Odwiedź [Azure AI Gallery](https://gallery.cortanaintelligence.com) , aby poznać przykłady dla przykładów usługi Machine Learning i analizy danych, które używają Azure Machine Learning i powiązanych usług danych na platformie Azure. Podano również ikonę dla tej galerii w menu **Start** i na pulpicie maszyny wirtualnej.

