---
title: Udostępnić bezpośrednich uczenia maszyny wirtualnej nauki danych na platformie Azure | Dokumentacja firmy Microsoft
description: Konfigurowanie i Utwórz głębokie uczenia danych nauki maszynę wirtualną na platformie Azure w celu wykonania analizy i uczenia maszynowego.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 2172acfd61188c09693b9c7dacf37ff7a638d7f4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="provision-a-deep-learning-virtual-machine-on-azure"></a>Zapewnij bezpośrednich uczenia maszyny wirtualnej na platformie Azure 

Głębokie uczenia maszyny wirtualnej (DLVM) jest specjalnie skonfigurowane wariant popularnych [maszyny wirtualnej nauki danych](http://aka.ms/dsvm) wystąpienia (DSVM), aby ułatwić korzystanie oparte na procesorze Graficznym maszyny Wirtualnej dla szybko szkolenia modele uczenia bezpośrednich. Jest obsługiwana z systemem Windows 2016 lub Ubuntu DSVM jako podstawa. DLVM współużytkują tego samego obrazów maszyn wirtualnych core, dlatego wszystkie bogaty zestaw narzędzi które są dostępne na DSVM. 

DLVM zawiera kilka narzędzi AI także w wersjach GPU RAM popularnych learning głębokie kognitywnych zestaw narzędzi firmy Microsoft, TensorFlow, Keras, Caffe2, moduł łańcucha; narzędzia do uzyskania dostępu i wstępnego przetwarzania obrazu, dane tekstowe, narzędzia dla danych nauki modelowania i rozwoju działania, takie jak Microsoft R Server Developer Edition, Anaconda Python notesów Jupyter dla języka Python i R, IDEs Python i R, SQL bazy danych i wielu innych narzędzia ML i analizy danych. 

## <a name="create-your-deep-learning-virtual-machine"></a>Tworzenie sieci bezpośrednich uczenia maszyny wirtualnej
Poniżej przedstawiono kroki, aby utworzyć wystąpienie z głębokiego Learning maszynę wirtualną: 

1. Przejdź do wyświetlania na maszynie wirtualnej [portalu Azure](https://portal.azure.com/#create/microsoft-ads.dsvm-deep-learningtoolkit
).
2. Wybierz **Utwórz** znajdujący się u dołu do kreatora.![ Utwórz dlvm](./media/dlvm-provision-wizard.PNG)
3. Kreator pozwala utworzyć DLVM wymaga **dane wejściowe** dla każdego z **cztery kroki** wyliczyć po prawej stronie tej liczby. Poniżej przedstawiono wejść potrzebne do skonfigurowania każdego z następujących czynności:
   
   1. **Podstawy**
      
      1. **Nazwa**: Nazwa serwera nauki danych tworzona.
      2. **Wybierz typ systemu operacyjnego dla maszyny Wirtualnej głębokie Learning**: Wybierz systemu Windows lub Linux (dla systemu Windows 2016 i DSVM podstawowej Ubuntu Linux)
      2. **Nazwa użytkownika**: identyfikator logowania konta administratora.
      3. **Hasło**: hasło konta administratora.
      4. **Subskrypcja**: Jeśli masz więcej niż jedną subskrypcję, wybierz jedną, na którym maszyna ma być utworzony i są rozliczane.
      5. **Grupa zasobów**: można utworzyć nową lub użyj **pusty** istniejącej grupy zasobów platformy Azure w ramach subskrypcji.
      6. **Lokalizacja**: wybierz centrum danych, która jest najbardziej odpowiednia. Zazwyczaj jest centrum danych, która zawiera większość danych lub zbliżony do fizycznej lokalizacji najszybszy dostęp do sieci. 
      
> [!NOTE]
> DLVM obsługuje wszystkie NC ND serii wystąpień maszyn wirtualnych procesora GPU. Podczas inicjowania obsługi administracyjnej DLVM, należy wybrać jeden z lokalizacji w platformy Azure, która ma GPU. Sprawdź [produkty Azure według regionu strony](https://azure.microsoft.com/regions/services/) strony dostępne lokalizacje i poszukaj **NC serii**, **serii NCv2**, **NCv3 serii** , lub **serii ND** w obszarze **obliczeniowe**. 

   2. **Ustawienia**: Wybierz jedno z serii NC (NC, NCv2, NCv3) lub serii ND GPU rozmiary maszyny wirtualnej, która spełnia Twoje wymagania funkcjonalne i ograniczenia kosztów. Utwórz konto magazynu dla maszyny Wirtualnej.  ![dlvm-settings](./media/dlvm-provision-step-2.PNG)
   
   3. **Podsumowanie**: Sprawdź, czy wszystkie informacje wprowadzone jest poprawna.
   5. **Kup**: kliknij **kupić** uruchomić udostępnianie. Łącze znajduje się na warunki transakcji. Maszyna wirtualna nie ma żadnych dodatkowych kosztów poza obliczeniowe dla rozmiaru serwera w **rozmiar** kroku. 

> [!NOTE]
> Udostępnianie powinno zająć około 10-20 minut. Stan inicjowania obsługi administracyjnej jest wyświetlana w portalu Azure.
> 


## <a name="how-to-access-the-deep-learning-virtual-machine"></a>Jak uzyskać dostęp głębokie Virtual Machine Learning

### <a name="windows-edition"></a>Wersja systemu Windows
Po utworzeniu maszyny Wirtualnej można pulpitu zdalnego do niego przy użyciu poświadczeń konta administratora, które skonfigurowano w poprzednim **podstawy** sekcji. 

### <a name="linux-edition"></a>Wersja systemu Linux

Po utworzeniu maszyny Wirtualnej można logowania się do go przy użyciu protokołu SSH. Korzystać z poświadczeń konta, które zostały utworzone w **podstawy** sekcji Krok 3 dla interfejsu powłoki tekstu. Na kliencie aWindows, możesz pobrać narzędzie klienta SSH, takie jak [Putty](http://www.putty.org). Jeśli wolisz pulpitu graficznego (X Windows System), można użyć X11 przekazywania na Putty lub instalowanie klienta X2Go.

> [!NOTE]
> Klient X2Go działa lepiej niż X11 przekazywania podczas testów. Zalecamy używanie klienta X2Go dla graficzny interfejs użytkownika.
> 
> 

#### <a name="installing-and-configuring-x2go-client"></a>Instalowanie i konfigurowanie klienta X2Go
Linux DLVM jest już zainicjowane z serwerem X2Go i gotowa do akceptowania połączeń klienta. Nawiązać pulpitu graficznego maszyny Wirtualnej systemu Linux, wykonaj następującą procedurę na komputerze klienckim:

1. Pobierz i zainstaluj klienta X2Go dla danej platformy klienta z [X2Go](http://wiki.x2go.org/doku.php/doc:installation:x2goclient).    
2. Uruchom klienta X2Go, a następnie wybierz **nowej sesji**. Otwiera okno konfiguracji z wieloma kartami. Wprowadź następujące parametry konfiguracji:
   * **Karta sesji**:
     * **Host**: Nazwa hosta lub adres IP maszyny Wirtualnej systemu Linux danych nauki.
     * **Logowania**: nazwa użytkownika na Maszynie wirtualnej systemu Linux.
     * **SSH Port**: pozostaw wartość domyślna 22.
     * **Typ sesji**: Zmień wartość na **XFCE**. Obecnie Linux DSVM obsługuje tylko XFCE pulpitu.
   * **Karta Media**: można wyłączyć obsługi dźwięku i klienta drukowania, jeśli nie ma potrzeby korzystania z nich.
   * **Foldery udostępnione**: katalogi z z komputerów klienckich zainstalowane na Maszynie wirtualnej systemu Linux, należy dodać katalogi komputera klienta, które chcesz udostępnić Maszynie wirtualnej na tej karcie.

Po zalogowaniu się z maszyną Wirtualną przy użyciu klienta SSH lub XFCE graficznego pulpitu za pomocą klienta X2Go, można przystąpić do uruchomienia przy użyciu narzędzia, które są zainstalowane i skonfigurowane na maszynie Wirtualnej. Na XFCE Zobacz aplikacje menu skrótów i ikony pulpitu dla wielu narzędzi.

Gdy maszyna wirtualna jest utworzone i udostępnione, można przystąpić do uruchomienia przy użyciu narzędzia, które są zainstalowane i skonfigurowane na nim. Brak Kafelki menu start i ikony pulpitu dla wielu narzędzi. 
