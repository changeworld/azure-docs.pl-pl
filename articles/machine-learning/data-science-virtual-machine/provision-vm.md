---
title: 'Szybki start: tworzenie maszyny wirtualnej do nauki o danych w systemie Windows'
titleSuffix: Azure Data Science Virtual Machine
description: Konfigurowanie i tworzenie maszyny wirtualnej do nauki o danych na platformie Azure do analizy i uczenia maszynowego.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 12/31/2019
ms.openlocfilehash: afcb676f68e7be9d3ebef11ea2c6876a86bbd062
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281786"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-windows"></a>Szybki start: konfigurowanie maszyny wirtualnej do nauki o danych dla systemu Windows

Wykładanie działa dzięki maszynie wirtualnej do nauki o danych w systemie Windows Server 2019.

## <a name="prerequisite"></a>Wymagania wstępne

Aby utworzyć maszynę wirtualną do nauki o danych systemu Windows, musisz mieć subskrypcję platformy Azure. [Wypróbuj platformę Azure za darmo](https://azure.com/free).
Należy pamiętać, że bezpłatne konta platformy Azure nie obsługują jednostek SKU obsługujących maszyny wirtualne z obsługą procesora GPU.

## <a name="create-your-dsvm"></a>Tworzenie dsvm

Aby utworzyć wystąpienie DSVM:

1. Przejdź do [witryny Azure portal](https://portal.azure.com) Może zostać wyświetlony monit o zalogowanie się do konta platformy Azure, jeśli nie jesteś jeszcze zalogowany.
1. Znajdź listę maszyn wirtualnych, wpisując w "maszynie wirtualnej do nauki o danych" i wybierając opcję "Data Science Virtual Machine - Windows 2019".

1. Wybierz przycisk **Utwórz** u dołu.

1. Należy przekierować do bloku "Utwórz maszynę wirtualną".

1. Wypełnij kartę **Podstawy:**
      * **Subskrypcja:** Jeśli masz więcej niż jedną subskrypcję, wybierz tę, na której zostanie utworzony i obciążony. Musisz mieć uprawnienia do tworzenia zasobów dla tej subskrypcji.
      * **Grupa zasobów**: Utwórz nową grupę lub użyj istniejącej.
      * **Nazwa maszyny wirtualnej**: Wprowadź nazwę maszyny wirtualnej. W ten sposób będzie wyświetlany w witrynie Azure portal.
      * **Lokalizacja:** Wybierz najbardziej odpowiednie centrum danych. Aby uzyskać najszybszy dostęp do sieci, jest to centrum danych, które ma większość danych lub znajduje się najbliżej fizycznej lokalizacji. Dowiedz się więcej o [regionach platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/).
      * **Obraz**: Pozostaw wartość domyślną.
      * **Rozmiar:** Należy automatycznie wypełniać rozmiar, który jest odpowiedni dla obciążeń ogólnych. Dowiedz się więcej o [rozmiarach maszyn wirtualnych systemu Windows na platformie Azure](../../virtual-machines/windows/sizes.md).
      * **Nazwa użytkownika**: Wprowadź nazwę użytkownika administratora. Jest to nazwa użytkownika, której użyjesz do zalogowania się do maszyny wirtualnej i nie musi być taka sama jak nazwa użytkownika platformy Azure.
      * **Hasło**: Wprowadź hasło, którego użyjesz do zalogowania się do maszyny wirtualnej.    
1. Wybierz pozycję **Przegląd + utwórz**.
1. **Recenzja+tworzenie**
   * Sprawdź, czy wszystkie wprowadzone informacje są poprawne. 
   * Wybierz **pozycję Utwórz**.


> [!NOTE]
> * Nie płacisz opłat licencyjnych za oprogramowanie, które jest fabrycznie załadowane na maszynie wirtualnej. Płacisz koszt obliczeń dla rozmiaru serwera wybranego w kroku **Rozmiar.**
> * Inicjowanie obsługi administracyjnej trwa od 10 do 20 minut. Stan maszyny Wirtualnej można wyświetlić w witrynie Azure portal.

## <a name="access-the-dsvm"></a>Dostęp do dsvm

Po utworzeniu i zainicjowaniu obsługi administracyjnej maszyny wirtualnej wykonaj kroki wymienione na liście, [aby połączyć się z maszyną wirtualną opartą na platformie Azure.](../../marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm.md) Użyj poświadczeń konta administratora skonfigurowanych w kroku **Podstawy** tworzenia maszyny wirtualnej. 

Możesz rozpocząć korzystanie z narzędzi, które są zainstalowane i skonfigurowane na maszynie wirtualnej. Dostęp do wielu narzędzi można uzyskać za pomocą kafelków menu **Start** i ikon pulpitu.

Można również dołączyć DSVM do notesów platformy Azure, aby uruchomić notesy Jupyter na maszynie Wirtualnej i pominąć ograniczenia warstwy bezpłatnej usługi. Aby uzyskać więcej informacji, zobacz [Zarządzanie projektami notesów i konfigurowanie](../../notebooks/configure-manage-azure-notebooks-projects.md#manage-and-configure-projects)ich .

<a name="tools"></a>


## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z narzędziami w systemie DSVM, otwierając menu **Start.**
* Dowiedz się więcej o usłudze Azure Machine Learning, [tutorials](../index.yml)czytając [co to jest usługa Azure Machine Learning?](../overview-what-is-azure-ml.md)
* Przeczytaj artykuł [Dziesięć rzeczy, które możesz wykonać na maszynie wirtualnej do nauki o danych](https://aka.ms/dsvmtenthings).
* Odwiedź [galerię sztucznej inteligencji platformy Azure,](https://gallery.cortanaintelligence.com) aby uzyskać przykłady uczenia maszynowego i analizy danych korzystające z usługi Azure Machine Learning i powiązanych usług danych na platformie Azure. Udostępniliśmy również ikonę tej galerii w menu **Start** i na pulpicie maszyny wirtualnej.

