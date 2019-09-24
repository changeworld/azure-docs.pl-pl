---
title: 'Szybki start: Tworzenie CentOS Data Science Virtual Machine'
description: Skonfiguruj i Utwórz Data Science Virtual Machine dla systemu Linux (CentOS), aby przeprowadzić analizę i uczenie maszynowe.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 09/13/2019
ms.openlocfilehash: 1761673198b3d8ffc3740d64f525c9e70bda9e3e
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71204186"
---
# <a name="quickstart-set-up-a-centos-linux-data-science-virtual-machine-in-azure"></a>Szybki start: Konfigurowanie Data Science Virtual Machine CentOS (Linux) na platformie Azure

Zacznij korzystać z Data Science Virtual Machine opartych na CentOS.

## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć CentOS Data Science Virtual Machine, musisz mieć **subskrypcję platformy Azure**. [Utwórz bezpłatną subskrypcję](https://azure.com/free).

## <a name="create-your-centos-data-science-virtual-machine"></a>Utwórz CentOS Data Science Virtual Machine

Poniżej przedstawiono kroki, które należy wykonać, aby utworzyć wystąpienie Data Science Virtual Machine CentOS:

1. Przejdź do witryny [Azure Portal](https://portal.azure.com). Jeśli użytkownik nie jest jeszcze zalogowany, może zostać wyświetlony monit o zalogowanie się do konta platformy Azure. 
1. Wpisz "maszyna wirtualna do nauki o danych" na pasku wyszukiwania, a następnie wybierz pozycję CentOS DSVM.

    ![CentOS wyniki wyszukiwania](./media/linux-dsvm-intro/search-centos.png)

1. W następnym oknie wybierz pozycję **Utwórz**.

    [![](media/linux-dsvm-intro/create-centos.png "Przycisk służący do tworzenia maszyny CentOS")](media/linux-dsvm-intro/create-centos-expanded.png#lightbox)

1. Należy przekierować do bloku "Tworzenie maszyny wirtualnej".
   
   ![Karta podstawy odnosząca się do maszyny wirtualnej CentOS](./media/linux-dsvm-intro/review-create-centos.png)

1. Wprowadź następujące informacje, aby skonfigurować każdy krok kreatora:

    1. **Podstawy**:
    
       * **Subskrypcja**: Jeśli masz więcej niż jedną subskrypcję, wybierz tę, w której zostanie utworzona i rozliczona. Musisz mieć uprawnienia do tworzenia zasobów dla tej subskrypcji.
       * **Grupa zasobów**: Utwórz nową grupę lub Użyj istniejącej.
       * **Nazwa maszyny wirtualnej**: Wprowadź nazwę maszyny wirtualnej. Jest to sposób ich wyświetlania w Azure Portal.
       * **Region**: Wybierz najbardziej odpowiednie centrum danych. Aby uzyskać najszybszy dostęp do sieci, znajduje się w centrum danych, które ma najwięcej z nich lub znajduje się najbliżej fizycznej lokalizacji. Dowiedz się więcej o [regionach platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/).
       * **Obraz**: Pozostaw wartość domyślną.
       * **Rozmiar**: Powinno to być automatycznie wypełniane rozmiarem, który jest odpowiedni dla ogólnych obciążeń. Dowiedz się więcej o [rozmiarach maszyn wirtualnych z systemem Linux na platformie Azure](../../virtual-machines/linux/sizes.md).
       * **Typ uwierzytelniania**: Aby uzyskać szybszy Instalator, wybierz pozycję "hasło". 
         
         > [!NOTE]
         > Jeśli zamierzasz używać JupyterHub, upewnij się, że wybrano opcję "Password" (hasło), ponieważ JupyterHub *nie* jest skonfigurowana do używania kluczy publicznych SSH.

       * **Nazwa użytkownika**: Wprowadź nazwę użytkownika administratora. Jest to nazwa użytkownika, która będzie używana do logowania się do maszyny wirtualnej i nie musi być taka sama jak nazwa użytkownika platformy Azure. *Nie* używaj wielkich liter.
         
         > [!NOTE]
         > Jeśli używasz wielkich liter w nazwie użytkownika, JupyterHub nie będzie działał i wystąpi błąd wewnętrzny serwera 500.

       * **Hasło**: Wprowadź hasło, które będzie używane do logowania się do maszyny wirtualnej.    
    
   1. Wybierz pozycję **Przegląd + utwórz**.
   1. **Przegląd + tworzenie**
      * Sprawdź, czy wszystkie wprowadzone informacje jest poprawna. 
      * Wybierz pozycję **Utwórz**.
    
    Aprowizacja powinno zająć około 5 minut. Stan jest wyświetlany w Azure Portal.

## <a name="how-to-access-the-centos-data-science-virtual-machine"></a>Jak uzyskać dostęp do Data Science Virtual Machine CentOS

Możesz uzyskać dostęp do CentOS DSVM na jeden z trzech sposobów:

  * SSH przez sesje usług terminalowych
  * X2Go graficzny sesji
  * JupyterHub i JupyterLab dla notesów programu Jupyter

Możesz również dołączyć Data Science Virtual Machine do Azure Notebooks do uruchamiania notesów Jupyter na maszynie wirtualnej i pomijania ograniczeń warstwy bezpłatnej usługi. Aby uzyskać więcej informacji, zobacz [Zarządzanie i Konfigurowanie projektów Azure Notebooks](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier).

### <a name="ssh"></a>Protokół SSH

Po utworzeniu maszyny wirtualnej, jeśli została ona skonfigurowana przy użyciu dostępu SSH, możesz zalogować się do niej przy użyciu protokołu SSH. Użyj poświadczeń konta, które zostały utworzone w **podstawy** sekcji Krok 3 dla interfejsu powłoki tekstu. W systemie Windows można pobrać narzędzie klienta SSH [, takie jak](https://www.putty.org)wyglądająco. Jeśli wolisz korzystać z graficznego pulpitu (X systemu Windows), możesz użyć funkcji przekazywania dalej X11.

> [!NOTE]
> Klient X2Go spisywała się lepiej niż X11 przekazywania do testowania. Zaleca się przy użyciu klienta X2Go dla interfejsu graficznego pulpitu.

### <a name="x2go"></a>X2Go

Maszyna wirtualna z systemem Linux została już zainicjowana z serwerem X2Go i gotowa do akceptowania połączeń klienckich. Aby połączyć pulpit graficzny maszyny Wirtualnej systemu Linux, należy wykonać poniższą procedurę na komputerze klienckim:

1. Pobieranie i instalowanie klienta X2Go dla danej platformy klienta [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient).
1. Zanotuj publiczny adres IP maszyny wirtualnej, który można znaleźć w Azure Portal, otwierając właśnie utworzoną maszynę wirtualną.

   ![CentOS adres IP maszyny](./media/linux-dsvm-intro/centos-ip-address.png)

1. Uruchom klienta programu X2Go. Jeśli okno "Nowa sesja" nie zostanie wyskakujące automatycznie, przejdź do sesji > nowej sesji.

1. W wyniku okna konfiguracji wprowadź następujące parametry konfiguracji:
   * **Karta sesji**:
     * **Host**: Wprowadź adres IP maszyny wirtualnej, która została zanotowana wcześniej.
     * **Logowanie**: Wprowadź nazwę użytkownika na maszynie wirtualnej z systemem Linux.
     * **Port SSH**: Pozostaw wartość domyślną równą 22.
     * **Typ sesji**: Zmień wartość na **pulpit Xfce**. Obecnie maszyna wirtualna z systemem Linux obsługuje tylko pulpit pulpit Xfce.
   * **Karta multimedia**: Można wyłączyć obsługę dźwięku i drukowanie klienta, jeśli nie trzeba ich używać.
   * **Foldery udostępnione**: Jeśli chcesz, aby katalogi z komputerów klienckich były zainstalowane na maszynie wirtualnej z systemem Linux, Dodaj do tej karty katalogi komputerów klienckich, które mają być współużytkowane z maszyną wirtualną.

   ![Konfiguracja X2go](./media/dsvm-ubuntu-intro/x2go-ubuntu.png)
1. Kliknij przycisk **OK**.
1. Kliknij pole w prawym okienku okna X2Go, aby wyświetlić ekran logowania dla maszyny wirtualnej.
1. Wprowadź hasło dla maszyny wirtualnej.
1. Kliknij przycisk **OK**.
1. Może być konieczne nadanie X2Go uprawnień do obejścia zapory, aby zakończyć nawiązywanie połączenia.
1. Powinien być teraz widoczny interfejs graficzny CentOS DSVM. 


### <a name="jupyterhub-and-jupyterlab"></a>JupyterHub i JupyterLab

CentOS DSVM uruchamia [JupyterHub](https://github.com/jupyterhub/jupyterhub), wieloużytkownikowego serwera Jupyter. Aby nawiązać połączenie, wykonaj następujące czynności:

   1. Zanotuj publiczny adres IP dla maszyny wirtualnej, wyszukując i wybierając maszynę wirtualną w Azure Portal.

       ![CentOS adres IP maszyny](./media/linux-dsvm-intro/centos-ip-address.png)

   1. Na komputerze lokalnym Otwórz przeglądarkę internetową i przejdź do protokołu https:\//Your-VM-IP: 8000, zastępując ciąg "The-VM-IP" adresem IP, który został wcześniej zanotowany.
   1. Wprowadź nazwę użytkownika i hasło, które zostały użyte podczas tworzenia maszyny wirtualnej i zaloguj się. 

      ![Wprowadź nazwę logowania Jupyter](./media/dsvm-ubuntu-intro/jupyter-login.png)

   1. Przejrzyj wiele przykładowych notesów, które są dostępne.

JupyterLab, następna generacja notesów Jupyter i JupyterHub, jest również dostępna. Aby uzyskać do niego dostęp, zaloguj się w usłudze JupyterHub, a następnie przejdź do adresu\/URL https:/Your-VM-IP: 8000/User/username/Lab, zastępując ciąg "Twoja nazwa użytkownika" nazwą użytkownika wybraną podczas konfigurowania maszyny wirtualnej.

Możesz ustawić JupyterLab jako domyślny serwer notesu, dodając ten wiersz do `/etc/jupyterhub/jupyterhub_config.py`:

```python
c.Spawner.default_url = '/lab'
```

## <a name="next-steps"></a>Następne kroki

Poniżej przedstawiono, jak można kontynuować Twoją naukę oraz eksploracji:

* Informacje o przewodniku dotyczące [Data Science Virtual Machine dla systemu Linux](linux-dsvm-walkthrough.md) przedstawiają sposób wykonywania kilku typowych zadań związanych z nauką o danych przy użyciu DSVM systemu Linux. 
* Zapoznaj się z różnymi narzędziami do nauki o danych w DSVM, korzystając z narzędzi opisanych w tym artykule. Możesz również uruchomić `dsvm-more-info` w powłoce programu na maszynie wirtualnej, aby uzyskać podstawowe wprowadzenie i uzyskać więcej informacji na temat narzędzi zainstalowanych w DSVM.  
* Dowiedz się, jak tworzyć rozwiązania analityczne end-to-end systematycznie przy użyciu [zespołu danych dla celów naukowych](https://aka.ms/tdsp).
* Odwiedź stronę [galerii Azure AI](https://gallery.azure.ai/) maszyny nauki i dane analizy przykładów, które korzystają z usług Azure AI.
* Zapoznaj się z odpowiednią [dokumentacją](./reference-centos-vm.md) dla tej maszyny wirtualnej.