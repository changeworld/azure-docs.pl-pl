---
title: 'Szybki start: tworzenie maszyny wirtualnej do nauki o danych CentOS'
titleSuffix: Azure Data Science Virtual Machine
description: Skonfiguruj i utwórz maszynę wirtualną do nauki o danych dla systemu Linux (CentOS), aby wykonać analizę i uczenie maszynowe.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 09/13/2019
ms.openlocfilehash: 73541b31125ee6e99dc2351e26f6a564a1603487
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77526029"
---
# <a name="quickstart-set-up-a-centos-linux-data-science-virtual-machine-in-azure"></a>Szybki start: konfigurowanie maszyny wirtualnej do nauki o danych CentOS (Linux) na platformie Azure

Wykładanie pracy dzięki maszynie wirtualnej opartej na analizie danych opartej na centosie.

## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć maszynę wirtualną do nauki o danych CentOS, musisz mieć **subskrypcję platformy Azure.** [Utwórz bezpłatną subskrypcję](https://azure.com/free).

## <a name="create-your-centos-data-science-virtual-machine"></a>Tworzenie maszyny wirtualnej do nauki o danych CentOS

Oto kroki, aby utworzyć wystąpienie maszyny wirtualnej Do nauki o danych CentOS:

1. Przejdź do [witryny Azure portal](https://portal.azure.com). Jeśli nie jesteś jeszcze zalogowany, może zostać wyświetlony monit o zalogowanie się na swoje konto platformy Azure. 
1. Wpisz "maszyna wirtualna do nauki o danych" na pasku wyszukiwania i wybierz centos DSVM.

    ![Wynik wyszukiwania CentOS](./media/linux-dsvm-intro/search-centos.png)

1. W kolejnym oknie wybierz pozycję **Utwórz**.

    [![](media/linux-dsvm-intro/create-centos.png "Button to create a CentOS machine")](media/linux-dsvm-intro/create-centos-expanded.png#lightbox)

1. Należy przekierować do bloku "Utwórz maszynę wirtualną".
   
   ![Karta Podstawy odpowiadająca maszynie wirtualnej CentOS](./media/linux-dsvm-intro/review-create-centos.png)

1. Wprowadź następujące informacje, aby skonfigurować każdy krok kreatora:

    1. **Podstawy**:
    
       * **Subskrypcja:** Jeśli masz więcej niż jedną subskrypcję, wybierz tę, na której zostanie utworzony i obciążony. Musisz mieć uprawnienia do tworzenia zasobów dla tej subskrypcji.
       * **Grupa zasobów**: Utwórz nową grupę lub użyj istniejącej.
       * **Nazwa maszyny wirtualnej**: Wprowadź nazwę maszyny wirtualnej. W ten sposób będzie wyświetlany w witrynie Azure portal.
       * **Region:** Wybierz najbardziej odpowiednie centrum danych. Aby uzyskać najszybszy dostęp do sieci, jest to centrum danych, które ma większość danych lub znajduje się najbliżej fizycznej lokalizacji. Dowiedz się więcej o [regionach platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/).
       * **Obraz**: Pozostaw wartość domyślną.
       * **Rozmiar:** Należy automatycznie wypełniać rozmiar, który jest odpowiedni dla obciążeń ogólnych. Dowiedz się więcej o [rozmiarach maszyn wirtualnych z systemem Linux na platformie Azure](../../virtual-machines/linux/sizes.md).
       * **Typ uwierzytelniania**: Aby szybciej skonfigurować, wybierz "Hasło". 
         
         > [!NOTE]
         > Jeśli zamierzasz używać Usługi JupyterHub, upewnij się, że wybierz "Hasło", ponieważ JupyterHub *nie* jest skonfigurowany do używania kluczy publicznych SSH.

       * **Nazwa użytkownika**: Wprowadź nazwę użytkownika administratora. Jest to nazwa użytkownika, której użyjesz do zalogowania się do maszyny wirtualnej i nie musi być taka sama jak nazwa użytkownika platformy Azure. *Nie* należy używać wielkich liter.
         
         > [!NOTE]
         > Jeśli używasz wielkich liter w swojej nazwie użytkownika, JupyterHub nie będzie działać, a wystąpi błąd serwera wewnętrznego 500.

       * **Hasło**: Wprowadź hasło, którego użyjesz do zalogowania się do maszyny wirtualnej.    
    
   1. Wybierz pozycję **Przegląd + utwórz**.
   1. **Recenzja+tworzenie**
      * Sprawdź, czy wszystkie wprowadzone informacje są poprawne. 
      * Wybierz **pozycję Utwórz**.
    
    Inicjowanie obsługi administracyjnej powinno potrwać około 5 minut. Stan jest wyświetlany w witrynie Azure portal.

## <a name="how-to-access-the-centos-data-science-virtual-machine"></a>Jak uzyskać dostęp do maszyny wirtualnej CentOS Data Science

Dostęp do systemu CentOS DSVM można uzyskać na jeden z trzech sposobów:

  * Protokół SSH w sesjach terminalu
  * Program X2Go w sesjach graficznych
  * Serwery JupyterHub i JupyterLab w notesach programu Jupyter

Maszynę wirtualną do nauki o danych można również dołączyć do notesów platformy Azure, aby uruchomić notesy jupyter na maszynie wirtualnej i pominąć ograniczenia warstwy bezpłatnej usługi. Aby uzyskać więcej informacji, zobacz [Zarządzanie projektami notesów platformy Azure i konfigurowanie ich.](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier)

### <a name="ssh"></a>Protokół SSH

Po utworzeniu maszyny Wirtualnej, jeśli została skonfigurowana z dostępem SSH, można zalogować się do niej za pomocą SSH. Użyj poświadczeń konta utworzonych w sekcji **Podstawy** kroku 3 dla interfejsu powłoki tekstu. W systemie Windows można pobrać narzędzie klienckie SSH, takie jak [PuTTY](https://www.putty.org). Jeśli wolisz pulpit graficzny (X Window System), możesz użyć x11 przekazywania na PuTTY.

> [!NOTE]
> Klient X2Go wypadł lepiej niż x11 przekazywania w testach. Zalecamy korzystanie z klienta X2Go dla graficznego interfejsu pulpitu.

### <a name="x2go"></a>X2Go

Maszyna wirtualna z systemem Linux jest już aprowizowana za pomocą serwera X2Go i jest gotowa do zaakceptowania połączeń klientów. Aby połączyć się z pulpitem graficznym maszyny Wirtualnej systemu Linux, wykonaj następującą procedurę na kliencie:

1. Pobierz i zainstaluj klienta X2Go dla swojej platformy klienta z [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient).
1. Zanotuj publiczny adres IP maszyny wirtualnej, który można znaleźć w witrynie Azure portal, otwierając właśnie utworzoną maszynę wirtualną.

   ![Adres IP komputera CentOS](./media/linux-dsvm-intro/centos-ip-address.png)

1. Uruchom klienta X2Go. Jeśli okno "Nowa sesja" nie pojawi się automatycznie, przejdź do > sesja - > nowa sesja.

1. W oknie konfiguracji wynikowej wprowadź następujące parametry konfiguracji:
   * **Karta Sesja**:
     * **Host**: Wprowadź adres IP maszyny Wirtualnej, który został zanotowany wcześniej.
     * **Zaloguj się:** Wprowadź nazwę użytkownika na maszynie Wirtualnej Systemu Linux.
     * **Port SSH**: Pozostaw go na 22, wartość domyślna.
     * **Typ sesji**: Zmień wartość na **XFCE**. Obecnie maszyna wirtualna z systemem Linux obsługuje tylko pulpit XFCE.
   * **Karta Multimedia:** Możesz wyłączyć obsługę dźwięku i drukowanie przez klienta, jeśli nie musisz ich używać.
   * **Foldery udostępnione:** Jeśli chcesz katalogi z maszyn klienckich zainstalowanych na maszynie Wirtualnej systemu Linux, dodaj katalogi komputera klienckiego, które chcesz udostępnić maszynie wirtualnej na tej karcie.

   ![Konfiguracja X2go](./media/dsvm-ubuntu-intro/x2go-ubuntu.png)
1. Kliknij przycisk **OK**.
1. Kliknij pole w prawym okienku okna X2Go, aby przywołać ekran logowania dla maszyny Wirtualnej.
1. Wprowadź hasło maszyny Wirtualnej.
1. Kliknij przycisk **OK**.
1. Może być na to, aby nadać X2Go uprawnienie do ominięcia zapory, aby zakończyć łączenie.
1. Teraz powinien zostać wyświetlony interfejs graficzny dla systemu DsVM centos. 


### <a name="jupyterhub-and-jupyterlab"></a>JupyterHub i JupyterLab

CentOS DSVM uruchamia [JupyterHub](https://github.com/jupyterhub/jupyterhub), serwer jupyter wielu użytkowniczu. Aby nawiązać połączenie, należy wykonać następujące czynności:

   1. Zanotuj publiczny adres IP maszyny Wirtualnej, wyszukując i wybierając maszynę wirtualną w witrynie Azure portal.

       ![Adres IP komputera CentOS](./media/linux-dsvm-intro/centos-ip-address.png)

   1. Z komputera lokalnego otwórz przeglądarkę internetową i\/przejdź do https: /your-vm-ip:8000, zastępując "your-vm-ip" adresem IP, który wcześniej zanotowałeś.
   1. Wprowadź nazwę użytkownika i hasło użyte do utworzenia maszyny Wirtualnej i zaloguj się. 

      ![Wprowadź logowanie jupytera](./media/dsvm-ubuntu-intro/jupyter-login.png)

   1. Przejrzyj wiele przykładowych notesów, które są dostępne.

JupyterLab, następna generacja notebooków Jupyter i JupyterHub, jest również dostępna. Aby uzyskać do niego dostęp, zaloguj się do Usługi JupyterHub, a następnie przejdź do adresu URL https:\//your-vm-ip:8000/user/your-username/lab, zastępując "twoja nazwa użytkownika" nazwą użytkownika wybraną podczas konfigurowania maszyny Wirtualnej.

JupyterLab można ustawić jako domyślny serwer notebooka, dodając ten wiersz do: `/etc/jupyterhub/jupyterhub_config.py`

```python
c.Spawner.default_url = '/lab'
```

## <a name="next-steps"></a>Następne kroki

Oto jak możesz kontynuować naukę i eksplorację:

* Instruktaż [data science na maszynie wirtualnej do nauki o danych dla systemu Linux](linux-dsvm-walkthrough.md) pokazuje, jak wykonać kilka typowych zadań nauki o danych za pomocą systemu Linux DSVM aprowizowanym w tym miejscu. 
* Zapoznaj się z różnymi narzędziami do nauki o danych na modelu DSVM, wypróbuj narzędzia opisane w tym artykule. Można również `dsvm-more-info` uruchomić w powłoce na maszynie wirtualnej dla podstawowego wprowadzenia i wskaźników, aby uzyskać więcej informacji na temat narzędzi zainstalowanych w DSVM.  
* Dowiedz się, jak systematycznie tworzyć kompleksowe rozwiązania analityczne przy użyciu [procesu nauki o danych zespołu.](https://aka.ms/tdsp)
* Odwiedź [galerię sztucznej inteligencji platformy Azure,](https://gallery.azure.ai/) aby uzyskać przykłady uczenia maszynowego i analizy danych korzystające z usług sztucznej inteligencji platformy Azure.
* Zapoznaj się z odpowiednią [dokumentacją referencyjną](./reference-centos-vm.md) dla tej maszyny wirtualnej.