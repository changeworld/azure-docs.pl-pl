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

## <a name="create-your-data-science-virtual-machine-for-linux"></a>Tworzenie Data Science Virtual Machine dla systemu Linux

Poniżej przedstawiono procedurę tworzenia wystąpienia Data Science Virtual Machine dla systemu Linux:

1. Przejdź do [Azure Portal](https://portal.azure.com) może zostać wyświetlony monit o zalogowanie się do konta platformy Azure, jeśli jeszcze nie zalogowano się.
1. Znajdź listę maszyn wirtualnych, wpisując ciąg "maszyna wirtualna do nauki o danych" i wybierając pozycję "Data Science Virtual Machine dla systemu Linux (Ubuntu)".
    
    ![Ubuntu maszyny wirtualnej](./media/dsvm-ubuntu-intro/search-ubuntu.png)

1. W następnym oknie wybierz pozycję **Utwórz**.

   [![](media/dsvm-ubuntu-intro/create-linux-expanded.png#lightbox)(media/dsvm-ubuntu-intro/create-linux.png "Przycisk służący do tworzenia maszyny Ubuntu")](media/dsvm-ubuntu-intro/create-linux-expanded.png#lightbox)

1. Należy przekierować do bloku "Tworzenie maszyny wirtualnej".
   
   ![Karta podstawy odnosząca się do maszyny wirtualnej Ubuntu](./media/dsvm-ubuntu-intro/review-create-ubuntu.png)

1. Wprowadź następujące informacje, aby skonfigurować każdy krok kreatora:

    1. **Podstawowe informacje**:
    
       * **Subskrypcja**: Jeśli masz więcej niż jedną subskrypcję, wybierz tę, w której zostanie utworzona i rozliczona. Musisz mieć uprawnienia do tworzenia zasobów dla tej subskrypcji.
       * **Grupa zasobów**: Utwórz nową grupę lub Użyj istniejącej.
       * **Nazwa maszyny wirtualnej**: Wprowadź nazwę maszyny wirtualnej. Jest to sposób ich wyświetlania w Azure Portal.
       * **Region**: wybierz najbardziej odpowiednie centrum danych. Aby uzyskać najszybszy dostęp do sieci, znajduje się w centrum danych, które ma najwięcej z nich lub znajduje się najbliżej fizycznej lokalizacji. Dowiedz się więcej o [regionach platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/).
       * **Obraz**: pozostaw wartość domyślną.
       * **Rozmiar**: powinno to być automatycznie wypełniane rozmiarem odpowiednim dla ogólnych obciążeń. Dowiedz się więcej o [rozmiarach maszyn wirtualnych z systemem Linux na platformie Azure](../../virtual-machines/linux/sizes.md).
       * **Typ uwierzytelniania**: aby szybciej skonfigurować konfigurację, wybierz pozycję "hasło". 
         
         > [!NOTE]
         > Jeśli zamierzasz używać JupyterHub, upewnij się, że wybrano opcję "Password" (hasło), ponieważ JupyterHub *nie* jest skonfigurowana do używania kluczy publicznych SSH.

       * **Nazwa użytkownika**: Wprowadź nazwę użytkownika administratora. Jest to nazwa użytkownika, która będzie używana do logowania się do maszyny wirtualnej i nie musi być taka sama jak nazwa użytkownika platformy Azure. *Nie* używaj wielkich liter.
         
         > [!NOTE]
         > Jeśli używasz wielkich liter w nazwie użytkownika, JupyterHub nie będzie działał i wystąpi błąd wewnętrzny serwera 500.

       * **Hasło**: wprowadź hasło, które będzie używane do logowania się do maszyny wirtualnej.    
    
   1. Wybierz pozycję **Recenzja + Utwórz**.
   1. **Przegląd + tworzenie**
      * Sprawdź, czy wszystkie wprowadzone informacje są poprawne. 
      * Wybierz pozycję **Utwórz**.
    
    Inicjowanie obsługi powinno trwać około 5 minut. Stan jest wyświetlany w Azure Portal.

## <a name="how-to-access-the-ubuntu-data-science-virtual-machine"></a>Jak uzyskać dostęp do Data Science Virtual Machine Ubuntu

Możesz uzyskać dostęp do Ubuntu DSVM na jeden z trzech sposobów:

  * Protokół SSH dla sesji terminalu
  * X2Go dla sesji graficznych
  * JupyterHub i JupyterLab dla notesów Jupyter

Możesz również dołączyć Data Science Virtual Machine do Azure Notebooks do uruchamiania notesów Jupyter na maszynie wirtualnej i pomijania ograniczeń warstwy bezpłatnej usługi. Aby uzyskać więcej informacji, zobacz [Zarządzanie i Konfigurowanie projektów Azure Notebooks](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier).

### <a name="ssh"></a>SSH

Po utworzeniu maszyny wirtualnej, jeśli została ona skonfigurowana przy użyciu dostępu SSH, możesz zalogować się do niej przy użyciu protokołu SSH. Użyj poświadczeń konta utworzonych w sekcji **podstawy** kroku 3 dla interfejsu powłoki tekstu. W systemie Windows można pobrać narzędzie klienta SSH [, takie jak](https://www.putty.org)wyglądająco. Jeśli wolisz korzystać z graficznego pulpitu (X systemu Windows), możesz użyć funkcji przekazywania dalej X11.

> [!NOTE]
> Klient X2Go był lepszy niż X11 do przesyłania dalej w testowaniu. Zalecamy korzystanie z klienta X2Go w interfejsie graficznym.

### <a name="x2go"></a>X2Go

Maszyna wirtualna z systemem Linux została już zainicjowana z serwerem X2Go i gotowa do akceptowania połączeń klienckich. Aby nawiązać połączenie z graficznym pulpitem maszyny wirtualnej z systemem Linux, wykonaj poniższą procedurę na kliencie:

1. Pobierz i zainstaluj klienta programu X2Go dla platformy klienckiej z programu [x2go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient).
1. Zanotuj publiczny adres IP maszyny wirtualnej, który można znaleźć w Azure Portal, otwierając właśnie utworzoną maszynę wirtualną.

   ![Ubuntu adres IP maszyny](./media/dsvm-ubuntu-intro/ubuntu-ip-address.png)

1. Uruchom klienta programu X2Go. Jeśli okno "Nowa sesja" nie zostanie wyskakujące automatycznie, przejdź do sesji > nowej sesji.

1. W wyniku okna konfiguracji wprowadź następujące parametry konfiguracji:
   * **Karta sesji**:
     * **Host**: wprowadź adres IP maszyny wirtualnej, która została zanotowana wcześniej.
     * **Logowanie**: Wprowadź nazwę użytkownika na maszynie wirtualnej z systemem Linux.
     * **Port SSH**: pozostaw wartość domyślną równą 22.
     * **Typ sesji**: Zmień wartość na **pulpit Xfce**. Obecnie maszyna wirtualna z systemem Linux obsługuje tylko pulpit pulpit Xfce.
   * **Karta multimedia**: można wyłączyć obsługę dźwięku i drukowanie klientów, jeśli nie trzeba ich używać.
   * **Foldery udostępnione**: Jeśli chcesz, aby katalogi z komputerów klienckich były zainstalowane na maszynie wirtualnej z systemem Linux, Dodaj katalogi na komputerze klienckim, które chcesz udostępnić z maszyną wirtualną na tej karcie.

   ![Konfiguracja X2go](./media/dsvm-ubuntu-intro/x2go-ubuntu.png)
1. Wybierz **przycisk OK**.
1. Kliknij pole w prawym okienku okna X2Go, aby wyświetlić ekran logowania dla maszyny wirtualnej.
1. Wprowadź hasło dla maszyny wirtualnej.
1. Wybierz **przycisk OK**.
1. Może być konieczne nadanie X2Go uprawnień do obejścia zapory, aby zakończyć nawiązywanie połączenia.
1. Powinien być teraz widoczny interfejs graficzny Ubuntu DSVM. 


### <a name="jupyterhub-and-jupyterlab"></a>JupyterHub i JupyterLab

Ubuntu DSVM uruchamia [JupyterHub](https://github.com/jupyterhub/jupyterhub), wieloużytkownikowego serwera Jupyter. Aby nawiązać połączenie, wykonaj następujące czynności:

   1. Zanotuj publiczny adres IP dla maszyny wirtualnej, wyszukując i wybierając maszynę wirtualną w Azure Portal.
      @no__t — adres IP maszyny 0Ubuntu @ no__t-1

   1. Na komputerze lokalnym Otwórz przeglądarkę internetową i przejdź do protokołu https: \//Twoje-VM-IP: 8000, zastępując ciąg "The-VM-IP" adresem IP, który został wcześniej zanotowany.
   1. Wprowadź nazwę użytkownika i hasło, które zostały użyte podczas tworzenia maszyny wirtualnej i zaloguj się. 

      ![Wprowadź nazwę logowania Jupyter](./media/dsvm-ubuntu-intro/jupyter-login.png)

   1. Przejrzyj wiele przykładowych notesów, które są dostępne.

Dostępna jest również JupyterLab nowej generacji notesów Jupyter i JupyterHub. Aby uzyskać do niego dostęp, zaloguj się do JupyterHub, a następnie przejdź do adresu URL https: \//typu-VM-IP: 8000/użytkownika/nazwy użytkownika/laboratorium, zastępując ciąg "swoją nazwą użytkownika" nazwą użytkownika wybraną podczas konfigurowania maszyny wirtualnej.

Możesz ustawić JupyterLab jako domyślny serwer notesu, dodając ten wiersz do `/etc/jupyterhub/jupyterhub_config.py`:

```python
c.Spawner.default_url = '/lab'
```

## <a name="next-steps"></a>Następne kroki

Oto jak można kontynuować uczenie i eksplorację:

* Analiza [danych w Data Science Virtual Machine dla systemu Linux](linux-dsvm-walkthrough.md) zawiera informacje na temat wykonywania kilku typowych zadań związanych z nauką o danych przy użyciu DSVM systemu Linux. 
* Zapoznaj się z różnymi narzędziami do nauki o danych w DSVM, korzystając z narzędzi opisanych w tym artykule. Możesz również uruchomić `dsvm-more-info` w powłoce na maszynie wirtualnej, aby zapoznać się z podstawowymi informacjami o narzędziach zainstalowanych na maszynie wirtualnej.  
* Dowiedz się, jak kompleksowo kompilować kompleksowe rozwiązania analityczne przy użyciu [zespołowego procesu nauki o danych](https://aka.ms/tdsp).
* Odwiedź [Azure AI Gallery](https://gallery.azure.ai/) , aby zapoznać się z przykładami dotyczącymi usługi Machine Learning i analizy danych, które korzystają z usług Azure AI.
* Zapoznaj się z odpowiednią [dokumentacją](./reference-ubuntu-vm.md) dla tej maszyny wirtualnej.
