---
title: 'Szybki start: Tworzenie maszyny DSVM z systemem Windows'
description: Konfigurowanie i tworzenie maszyny wirtualnej do nauki o danych na platformie Azure, analizy i uczenia maszynowego.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 09/10/2019
ms.openlocfilehash: e08287fec79ec634d6e2353328854e19565533ca
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71204087"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-windows"></a>Szybki start: Konfigurowanie Data Science Virtual Machine dla systemu Windows

Zacznij korzystać z Data Science Virtual Machine systemu Windows.

## <a name="prerequisite"></a>Wymagania wstępne

Aby utworzyć Data Science Virtual Machine systemu Windows, musisz mieć subskrypcję platformy Azure. [Wypróbuj bezpłatnie platformę Azure](https://azure.com/free).

## <a name="create-your-dsvm"></a>Tworzenie DSVM

Aby utworzyć wystąpienie DSVM:

1. Przejdź do [Azure Portal](https://portal.azure.com) może zostać wyświetlony monit o zalogowanie się do konta platformy Azure, jeśli jeszcze nie zalogowano się.
1. Znajdź listę maszyn wirtualnych, wpisując ciąg "maszyna wirtualna do nauki o danych" i wybierając pozycję "Data Science Virtual Machine-Windows 2016".

    ![Lista maszyn wirtualnych z systemem Windows](./media/provision-vm/search-windows.png)

1. Wybierz przycisk **Utwórz** u dołu.

    [![](media/provision-vm/create-windows.png "Przycisk służący do tworzenia maszyny z systemem Windows")](media/provision-vm/create-windows-expanded.png#lightbox)

1. Należy przekierować do bloku "Tworzenie maszyny wirtualnej".
   ![Karta podstawy odnosząca się do maszyny wirtualnej z systemem Windows](./media/provision-vm/review-create-windows.png)

1. Wypełnij kartę **podstawowe** :
      * **Subskrypcja**: Jeśli masz więcej niż jedną subskrypcję, wybierz tę, w której zostanie utworzona i rozliczona. Musisz mieć uprawnienia do tworzenia zasobów dla tej subskrypcji.
      * **Grupa zasobów**: Utwórz nową grupę lub Użyj istniejącej.
      * **Nazwa maszyny wirtualnej**: Wprowadź nazwę maszyny wirtualnej. Jest to sposób ich wyświetlania w Azure Portal.
      * **Lokalizacja**: Wybierz najbardziej odpowiednie centrum danych. Aby uzyskać najszybszy dostęp do sieci, znajduje się w centrum danych, które ma najwięcej z nich lub znajduje się najbliżej fizycznej lokalizacji. Dowiedz się więcej o [regionach platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/).
      * **Obraz**: Pozostaw wartość domyślną.
      * **Rozmiar**: Powinno to być automatycznie wypełniane rozmiarem, który jest odpowiedni dla ogólnych obciążeń. Dowiedz się więcej o [rozmiarach maszyn wirtualnych z systemem Windows na platformie Azure](../../virtual-machines/windows/sizes.md).
      * **Nazwa użytkownika**: Wprowadź nazwę użytkownika administratora. Jest to nazwa użytkownika, która będzie używana do logowania się do maszyny wirtualnej i nie musi być taka sama jak nazwa użytkownika platformy Azure.
      * **Hasło**: Wprowadź hasło, które będzie używane do logowania się do maszyny wirtualnej.    
1. Wybierz pozycję **Przegląd + utwórz**.
1. **Przegląd + tworzenie**
   * Sprawdź, czy wszystkie wprowadzone informacje jest poprawna. 
   * Wybierz pozycję **Utwórz**.


> [!NOTE]
> * Nie są naliczane opłaty licencyjne za oprogramowanie, które jest wstępnie załadowane na maszynę wirtualną. Koszt obliczeń jest płacony dla rozmiaru serwera wybranego w kroku **rozmiar** .
> * Inicjowanie obsługi trwa od 10 do 20 minut. Stan maszyny wirtualnej można wyświetlić na Azure Portal.

## <a name="access-the-dsvm"></a>Dostęp do DSVM

Po utworzeniu maszyny wirtualnej i zainicjowaniu jej obsługi postępuj zgodnie z instrukcjami wyświetlanymi w celu [nawiązania połączenia z maszyną wirtualną opartą na platformie Azure](../../marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm.md). Użyj poświadczeń konta administratora, które zostały skonfigurowane w kroku **podstawowe informacje** o tworzeniu maszyny wirtualnej. 

Możesz rozpocząć korzystanie z narzędzia, które są zainstalowane i skonfigurowane na maszynie Wirtualnej. Do wielu narzędzi można uzyskać dostęp za pomocą kafelków menu **Start** i ikon pulpitu.

Możesz również dołączyć DSVM do Azure Notebooks uruchamiania notesów Jupyter na maszynie wirtualnej i pomijania ograniczeń warstwy Bezpłatna usługa. Aby uzyskać więcej informacji, zobacz [Zarządzanie i Konfigurowanie projektów notesów](../../notebooks/configure-manage-azure-notebooks-projects.md#manage-and-configure-projects).

<a name="tools"></a>


## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z narzędziami w DSVM, otwierając menu **Start** .
* Dowiedz się więcej o usłudze Azure Machine Learning, odczytując informacje o tym, [co to jest usługa Azure Machine Learning?](../service/overview-what-is-azure-ml.md) i wypróbować [samouczki](../index.yml).
* W Eksploratorze plików przejdź do folderu C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts for Samples, które używają biblioteki kolekcję funkcji revoscaler w języku R, która obsługuje analizę danych w skali przedsiębiorstwa. 
* Zapoznaj się z artykułem [dziesięć rzeczy, które można wykonać na Data Science Virtual Machine](https://aka.ms/dsvmtenthings).
* Dowiedz się, jak tworzyć rozwiązania analityczne end-to-end systematycznie przy użyciu [zespołu danych dla celów naukowych](../team-data-science-process/index.yml).
* Odwiedź stronę [galerii Azure AI](https://gallery.cortanaintelligence.com) nauki i dane analizy przykładowych maszyny korzystające z usługi Azure Machine Learning i powiązane dane usług na platformie Azure. Podano również ikonę dla tej galerii w menu **Start** i na pulpicie maszyny wirtualnej.
* Zapoznaj się z odpowiednią [dokumentacją](./reference-windows-vm.md) dla tej maszyny wirtualnej.

