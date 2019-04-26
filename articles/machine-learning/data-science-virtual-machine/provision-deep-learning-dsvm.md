---
title: Tworzenie maszyny wirtualnej do nauki o danych do uczenia głębokiego
titleSuffix: Azure
description: Konfigurowanie i tworzenie głębokiego uczenia maszyny wirtualnej analizy danych na platformie Azure, analizy i uczenia maszynowego.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 42430c847149f7eda2f0dbed1cff006a92f372ee
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60578609"
---
# <a name="provision-a-deep-learning-virtual-machine-on-azure"></a>Aprowizowanie maszyny wirtualnej na platformie Azure do uczenia głębokiego 

Głębokie uczenie maszyny wirtualnej (DLVM) jest specjalnie skonfigurowanego wariant popularnej [maszyny wirtualnej do nauki o danych](https://aka.ms/dsvm) (DSVM), aby ułatwić korzystanie z opartej na procesorze GPU maszyny Wirtualnej wystąpienia dla szybko szkolenie modeli uczenia głębokiego. Windows 2016 lub maszyny wirtualnej DSVM Ubuntu jest obsługiwany jako podstawy. DLVM udostępnia tych samych podstawowych obrazów maszyn wirtualnych i dlatego wszystkie rozbudowany zestaw narzędzi, dostępnej we nauki. 

DLVM zawiera szereg narzędzi sztucznej Inteligencji, w tym wersje procesora GPU dla popularnych platform uczenia głębokiego, takich jak Microsoft Cognitive Toolkit, TensorFlow, Keras, Caffe2, Chainer; narzędzia do uzyskania i przetwarzania wstępnego obrazu, dane tekstowe, narzędzia data science modelowania i opracowywania działań, takich jak Microsoft R Server Developer Edition, Anaconda Python notesów programu Jupyter dla języka Python i R, środowiska IDE języka Python i R, SQL bazy danych i wieloma innymi narzędzia ML i nauki o danych. 

## <a name="create-your-deep-learning-virtual-machine"></a>Tworzenie sieci maszyny wirtualnej do uczenia głębokiego
Poniżej przedstawiono kroki, aby utworzyć wystąpienie programu Deep maszyna wirtualna do uczenia: 

1. Przejdź do maszyny wirtualnej, w witrynie [witryny Azure portal](https://portal.azure.com/#create/microsoft-ads.dsvm-deep-learningtoolkit
).
2. Wybierz **Utwórz** znajdujący się u dołu, aby uwzględnić kreatora.![ Utwórz dlvm](./media/dlvm-provision-wizard.PNG)
3. Kreator pozwala utworzyć DLVM wymaga **dane wejściowe** dla każdego z **cztery kroki** wyliczane po prawej stronie tej liczby. Poniżej przedstawiono dane wejściowe wymagane do skonfigurowania każdego z następujących czynności:
   
   1. **Podstawy**
      
      1. **Nazwa**: Nazwa tworzonej serwera do nauki o danych.
      2. **Wybierz typ systemu operacyjnego dla maszyny Wirtualnej z głębokiego uczenia**: Wybierz pozycję Windows lub Linux (dla Windows 2016 i Ubuntu Linux-DSVM podstawowy)
      2. **Nazwa użytkownika**: Identyfikator logowania konta administratora.
      3. **Hasło**: Hasło do konta administratora.
      4. **Subskrypcja**: Jeśli masz więcej niż jedną subskrypcję, wybierz ten, na którym maszyna ma zostać utworzona i rozliczane.
      5. **Grupa zasobów**: Możesz utworzyć nową lub użyj **pusty** istniejącą grupę zasobów platformy Azure w ramach subskrypcji.
      6. **Lokalizacja**: Wybierz centrum danych, które jest najbardziej odpowiednie. Zazwyczaj jest centrum danych, który zawiera większość danych lub znajduje się najbliżej Twojej lokalizacji fizycznej najszybszy dostęp do sieci. 
      
> [!NOTE]
> DLVM obsługuje wszystkie NC i ND serii wystąpień maszyn wirtualnych procesorów GPU. Podczas aprowizowania DLVM, musisz wybrać jedną z lokalizacji platformy Azure, która ma procesorów GPU. Sprawdź [produkty Azure według regionu strony](https://azure.microsoft.com/regions/services/) stronie dostępnych lokalizacji i poszukaj **seria NC**, **seria NCv2**, **seria NCv3** , lub **seria ND** w obszarze **obliczenia**. 

1. **Ustawienia**: Wybierz jeden z serii NC (NC, NCv2, NCv3) lub seria ND rozmiarów maszyn wirtualnych GPU, który spełnia Twoje wymagania funkcjonalne i ograniczenia kosztów. Tworzenie konta magazynu dla maszyny Wirtualnej.  ![dlvm-settings](./media/dlvm-provision-step-2.PNG)
   
1. **Podsumowanie**: Sprawdź, czy wszystkie wprowadzone informacje jest poprawna.
1. **Kup**: Kliknij przycisk **Kup** do uruchomienia, jego obsługi. Dostępny jest link do warunków transakcji. Maszyna wirtualna nie ma żadnych dodatkowych kosztów poza obliczenia rozmiaru serwera wybranej w ramach **rozmiar** kroku. 

> [!NOTE]
> Aprowizacja powinno zająć około 10-20 minut. Stan aprowizacji jest wyświetlany w witrynie Azure portal.
> 


## <a name="how-to-access-the-deep-learning-virtual-machine"></a>Jak uzyskać dostęp do maszyny wirtualnej głębokiego uczenia

### <a name="windows-edition"></a>Wersja Windows
Po utworzeniu maszyny Wirtualnej możesz pulpitu zdalnego do niej przy użyciu poświadczeń konta administratora, które skonfigurowano w poprzednim **podstawy** sekcji. 

### <a name="linux-edition"></a>Wersja systemu Linux

Po utworzeniu maszyny Wirtualnej możesz można Zaloguj się do niej przy użyciu protokołu SSH. Użyj poświadczeń konta, które zostały utworzone w **podstawy** sekcji Krok 3 dla interfejsu powłoki tekstu. Na komputerze klienckim aWindows można pobrać narzędzia klienta SSH, takiego jak [Putty](https://www.putty.org). Jeśli wolisz graficzny desktop (X w systemie Windows), możesz użyć X11 przekazywania w programie Putty lub zainstalować klienta X2Go.

> [!NOTE]
> Klient X2Go spisywała się lepiej niż X11 przekazywania podczas testów. Zaleca się przy użyciu klienta X2Go dla interfejsu graficznego pulpitu.
> 
> 

#### <a name="installing-and-configuring-x2go-client"></a>Instalowanie i konfigurowanie klienta X2Go
DLVM systemu Linux jest już zainicjowana przy użyciu serwera X2Go i gotowy do akceptowania połączeń klienta. Aby połączyć pulpit graficzny maszyny Wirtualnej systemu Linux, należy wykonać poniższą procedurę na komputerze klienckim:

1. Pobieranie i instalowanie klienta X2Go dla danej platformy klienta [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient).    
2. Uruchom klienta X2Go, a następnie wybierz pozycję **nowej sesji**. Otwiera okno konfiguracji z wieloma kartami. Wprowadź następujące parametry konfiguracji:
   * **Karta sesji**:
     * **Host**: Nazwa hosta lub adres IP maszyny wirtualnej do nauki o danych systemu Linux.
     * **Zaloguj się**: Nazwa użytkownika na maszynie Wirtualnej systemu Linux.
     * **SSH portu**: Pozostaw to pole o 22, wartością domyślną.
     * **Typ sesji**: Zmień wartość na **XFCE**. Obecnie DSVM systemu Linux obsługuje tylko pulpit xfce.
   * **Karta Media**: Można wyłączyć dźwięk pomocy technicznej i klienta, drukowanie, jeśli nie potrzebujesz umożliwić ich używanie.
   * **Foldery udostępnione**: Katalogi z maszyn klienta zainstalowanego na maszynie Wirtualnej systemu Linux, dodać katalogi maszyny klienta, które chcesz się podzielić z maszyną Wirtualną na tej karcie.

Po zalogowaniu do maszyny Wirtualnej przy użyciu klienta SSH lub graficznego pulpit xfce za pomocą klienta X2Go, jesteś gotowy rozpocząć korzystanie z narzędzia, które są zainstalowane i skonfigurowane na maszynie Wirtualnej. Na XFCE widać ikony pulpitu i skróty w menu aplikacji dla wielu narzędzi.

Po utworzeniu maszyny Wirtualnej i zainicjowano obsługę administracyjną, jesteś gotowy rozpocząć korzystanie z narzędzia, które są zainstalowane i skonfigurowane na nim. Brak kafelków menu start i ikony pulpitu dla wielu narzędzi. 
