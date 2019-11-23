---
title: 'Szybki Start: Tworzenie Ubuntu DSVM'
description: Skonfiguruj i Utwórz Data Science Virtual Machine dla systemu Linux (Ubuntu), aby przeprowadzić analizę i uczenie maszynowe.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 09/10/2019
ms.openlocfilehash: 951191422e80ef6224cd140beed782de31cb3822
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677842"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-linux-ubuntu"></a>Szybki Start: Konfigurowanie Data Science Virtual Machine dla systemu Linux (Ubuntu)

Zacznij korzystać z Data Science Virtual Machine Ubuntu.

## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć Data Science Virtual Machine systemu Windows, musisz mieć subskrypcję platformy Azure. [Wypróbuj bezpłatnie platformę Azure](https://azure.com/free).
Zanotuj bezpłatne konta platformy Azure nie obsługują jednostek SKU maszyn wirtualnych obsługujących procesor GPU.

## <a name="create-your-data-science-virtual-machine-for-linux"></a>Tworzenie maszyny wirtualnej do nauki o danych dla systemu Linux

Poniżej przedstawiono kroki, aby utworzyć wystąpienie maszyny wirtualnej do nauki o danych dla systemu Linux:

1. Przejdź do [Azure Portal](https://portal.azure.com) może zostać wyświetlony monit o zalogowanie się do konta platformy Azure, jeśli jeszcze nie zalogowano się.
1. Znajdź listę maszyn wirtualnych, wpisując ciąg "maszyna wirtualna do nauki o danych" i wybierając pozycję "Data Science Virtual Machine dla systemu Linux (Ubuntu)".
    
    ![Ubuntu maszyny wirtualnej](./media/dsvm-ubuntu-intro/search-ubuntu.png)

1. W następnym oknie wybierz pozycję **Utwórz**.

   [![](media/dsvm-ubuntu-intro/create-linux.png "Button to create an Ubuntu machine")](media/dsvm-ubuntu-intro/create-linux-expanded.png#lightbox)

1. [![](media/dsvm-ubuntu-intro/create-linux.png "Przycisk służący do tworzenia maszyny Ubuntu")](media/dsvm-ubuntu-intro/create-linux-expanded.png#lightbox)
   
   ![Należy przekierować do bloku "Tworzenie maszyny wirtualnej".](./media/dsvm-ubuntu-intro/review-create-ubuntu.png)

1. Karta podstawy odnosząca się do maszyny wirtualnej Ubuntu

    1. Wprowadź następujące informacje, aby skonfigurować każdy krok kreatora:
    
       * **Podstawy**: **Subskrypcja**: Jeśli masz więcej niż jedną subskrypcję, wybierz tę, w której zostanie utworzona i rozliczona.
       * Musisz mieć uprawnienia do tworzenia zasobów dla tej subskrypcji.
       * **Grupa zasobów**: Utwórz nową grupę lub Użyj istniejącej. **Nazwa maszyny wirtualnej**: Wprowadź nazwę maszyny wirtualnej.
       * Jest to sposób ich wyświetlania w Azure Portal. **Region**: wybierz najbardziej odpowiednie centrum danych. Aby uzyskać najszybszy dostęp do sieci, znajduje się w centrum danych, które ma najwięcej z nich lub znajduje się najbliżej fizycznej lokalizacji.
       * Dowiedz się więcej o [regionach platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/).
       * **Obraz**: pozostaw wartość domyślną. **Rozmiar**: powinno to być automatycznie wypełniane rozmiarem odpowiednim dla ogólnych obciążeń.
       * Dowiedz się więcej o [rozmiarach maszyn wirtualnych z systemem Linux na platformie Azure](../../virtual-machines/linux/sizes.md). 
         
         > [!NOTE]
         > **Typ uwierzytelniania**: aby szybciej skonfigurować konfigurację, wybierz pozycję "hasło".

       * Jeśli zamierzasz używać JupyterHub, upewnij się, że wybrano opcję "Password" (hasło), ponieważ JupyterHub *nie* jest skonfigurowana do używania kluczy publicznych SSH. **Nazwa użytkownika**: Wprowadź nazwę użytkownika administratora. Jest to nazwa użytkownika, która będzie używana do logowania się do maszyny wirtualnej i nie musi być taka sama jak nazwa użytkownika platformy Azure.
         
         > [!NOTE]
         > *Nie* używaj wielkich liter.

       * Jeśli używasz wielkich liter w nazwie użytkownika, JupyterHub nie będzie działał i wystąpi błąd wewnętrzny serwera 500.    
    
   1. **Hasło**: wprowadź hasło, które będzie używane do logowania się do maszyny wirtualnej.
   1. Wybierz pozycję **Przegląd + utwórz**.
      * **Przegląd + tworzenie** 
      * Sprawdź, czy wszystkie wprowadzone informacje jest poprawna.
    
    Wybierz pozycję **Utwórz**. Aprowizacja powinno zająć około 5 minut.

## <a name="how-to-access-the-ubuntu-data-science-virtual-machine"></a>Stan jest wyświetlany w Azure Portal.

Jak uzyskać dostęp do Data Science Virtual Machine Ubuntu

  * Możesz uzyskać dostęp do Ubuntu DSVM na jeden z trzech sposobów:
  * SSH przez sesje usług terminalowych
  * X2Go graficzny sesji

JupyterHub i JupyterLab dla notesów programu Jupyter Możesz również dołączyć Data Science Virtual Machine do Azure Notebooks do uruchamiania notesów Jupyter na maszynie wirtualnej i pomijania ograniczeń warstwy bezpłatnej usługi.

### <a name="ssh"></a>Aby uzyskać więcej informacji, zobacz [Zarządzanie i Konfigurowanie projektów Azure Notebooks](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier).

Protokół SSH Po utworzeniu maszyny wirtualnej, jeśli została ona skonfigurowana przy użyciu dostępu SSH, możesz zalogować się do niej przy użyciu protokołu SSH. Użyj poświadczeń konta, które zostały utworzone w **podstawy** sekcji Krok 3 dla interfejsu powłoki tekstu. W systemie Windows można pobrać narzędzie klienta SSH [, takie jak](https://www.putty.org)wyglądająco.

> [!NOTE]
> Jeśli wolisz korzystać z graficznego pulpitu (X systemu Windows), możesz użyć funkcji przekazywania dalej X11. Klient X2Go spisywała się lepiej niż X11 przekazywania do testowania.

### <a name="x2go"></a>Zaleca się przy użyciu klienta X2Go dla interfejsu graficznego pulpitu.

X2Go Maszyna wirtualna z systemem Linux została już zainicjowana z serwerem X2Go i gotowa do akceptowania połączeń klienckich.

1. Aby połączyć pulpit graficzny maszyny Wirtualnej systemu Linux, należy wykonać poniższą procedurę na komputerze klienckim:
1. Pobieranie i instalowanie klienta X2Go dla danej platformy klienta [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient).

   ![Zanotuj publiczny adres IP maszyny wirtualnej, który można znaleźć w Azure Portal, otwierając właśnie utworzoną maszynę wirtualną.](./media/dsvm-ubuntu-intro/ubuntu-ip-address.png)

1. Ubuntu adres IP maszyny Uruchom klienta programu X2Go.

1. Jeśli okno "Nowa sesja" nie zostanie wyskakujące automatycznie, przejdź do sesji > nowej sesji.
   * W wyniku okna konfiguracji wprowadź następujące parametry konfiguracji:
     * **Karta sesji**:
     * **Host**: wprowadź adres IP maszyny wirtualnej, która została zanotowana wcześniej.
     * **Logowanie**: Wprowadź nazwę użytkownika na maszynie wirtualnej z systemem Linux.
     * **SSH Port**: pozostaw 22, wartością domyślną. **Typ sesji**: Zmień wartość na **XFCE**.
   * Obecnie maszyna wirtualna z systemem Linux obsługuje tylko pulpit pulpit Xfce.
   * **Karta Media**: można wyłączyć dźwięk pomocy technicznej i klienta, drukowanie, jeśli nie potrzebujesz umożliwić ich używanie.

   ![**Foldery udostępnione**: katalogi z maszyn klienta zainstalowanego na maszynie Wirtualnej systemu Linux, dodać katalogi maszyny klienta, które chcesz się podzielić z maszyną Wirtualną na tej karcie.](./media/dsvm-ubuntu-intro/x2go-ubuntu.png)
1. Konfiguracja X2go
1. Wybierz **OK**.
1. Kliknij pole w prawym okienku okna X2Go, aby wyświetlić ekran logowania dla maszyny wirtualnej.
1. Wprowadź hasło dla maszyny wirtualnej.
1. Wybierz **OK**.
1. Może być konieczne nadanie X2Go uprawnień do obejścia zapory, aby zakończyć nawiązywanie połączenia. 


### <a name="jupyterhub-and-jupyterlab"></a>Powinien być teraz widoczny interfejs graficzny Ubuntu DSVM.

JupyterHub i JupyterLab Ubuntu DSVM uruchamia [JupyterHub](https://github.com/jupyterhub/jupyterhub), wieloużytkownikowego serwera Jupyter.

   1. Aby nawiązać połączenie, wykonaj następujące czynności:
      Zanotuj publiczny adres IP dla maszyny wirtualnej, wyszukując i wybierając maszynę wirtualną w Azure Portal.

   1. ![](./media/dsvm-ubuntu-intro/ubuntu-ip-address.png) adres IP maszyny Ubuntu
   1. Na komputerze lokalnym Otwórz przeglądarkę internetową i przejdź do protokołu https:\//Your-VM-IP: 8000, zastępując ciąg "The-VM-IP" adresem IP, który został wcześniej zanotowany. 

      ![Wprowadź nazwę użytkownika i hasło, które zostały użyte podczas tworzenia maszyny wirtualnej i zaloguj się.](./media/dsvm-ubuntu-intro/jupyter-login.png)

   1. Wprowadź nazwę logowania Jupyter

Przejrzyj wiele przykładowych notesów, które są dostępne. JupyterLab, następna generacja notesów Jupyter i JupyterHub, jest również dostępna.

Aby uzyskać do niego dostęp, zaloguj się do JupyterHub, a następnie przejdź do adresu URL https:\//Your-VM-IP: 8000/User/Nazwa użytkownika/laboratorium, zastępując ciąg "Twoja nazwa użytkownika" nazwą użytkownika wybraną podczas konfigurowania maszyny wirtualnej.

```python
c.Spawner.default_url = '/lab'
```

## <a name="next-steps"></a>Możesz ustawić JupyterLab jako domyślny serwer notesu, dodając ten wiersz do `/etc/jupyterhub/jupyterhub_config.py`:

Następne kroki

* Poniżej przedstawiono, jak można kontynuować Twoją naukę oraz eksploracji: 
* Analiza [danych w Data Science Virtual Machine dla systemu Linux](linux-dsvm-walkthrough.md) zawiera informacje na temat wykonywania kilku typowych zadań związanych z nauką o danych przy użyciu DSVM systemu Linux. Zapoznaj się z różnymi narzędziami do nauki o danych w DSVM, korzystając z narzędzi opisanych w tym artykule.  
* Aby uzyskać więcej informacji na temat narzędzi zainstalowanych na maszynie wirtualnej, można również uruchomić `dsvm-more-info` w ramach powłoki w ramach maszyny wirtualnej.
* Dowiedz się, jak tworzyć rozwiązania analityczne end-to-end systematycznie przy użyciu [zespołu danych dla celów naukowych](https://aka.ms/tdsp).
* Odwiedź stronę [galerii Azure AI](https://gallery.azure.ai/) maszyny nauki i dane analizy przykładów, które korzystają z usług Azure AI.
