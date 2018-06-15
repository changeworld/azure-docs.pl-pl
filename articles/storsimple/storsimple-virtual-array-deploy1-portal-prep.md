---
title: Portal przedprodukcyjnym dla tablicy wirtualnego StorSimple | Dokumentacja firmy Microsoft
description: Pierwszy samouczek, aby wdrożyć wirtualny tablicy StorSimple obejmuje przygotowanie portalu Azure
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 68a4cfd3-94c9-46cb-805c-46217290ce02
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/14/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6685c5ab7768176a0c8e7084c8512d5345732d9a
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2017
ms.locfileid: "24526557"
---
# <a name="deploy-storsimple-virtual-array---prepare-the-azure-portal"></a>Wdrażanie tablicy wirtualne StorSimple — przygotowanie portalu Azure

![](./media/storsimple-virtual-array-deploy1-portal-prep/getstarted4.png)
## <a name="overview"></a>Omówienie

Jest to pierwszy artykuł z serii samouczki wdrażania wymaganych do całkowicie wdrożenia wirtualnego tablica jako serwera plików lub serwera iSCSI przy użyciu modelu Resource Manager. W tym artykule opisano przygotowania wymagane do tworzenia i konfigurowania usługi Menedżer StorSimple urządzenia przed udostępniania wirtualnego tablicy. Ten artykuł zawiera również linki się lista kontrolna dotycząca konfiguracji wdrożenia i konfiguracji wymagań wstępnych.

Do ukończenia procesu instalacji i konfiguracji niezbędne są uprawnienia administratora. Firma Microsoft zaleca przejrzenie listy kontrolnej konfiguracji wdrożenia przed rozpoczęciem. Przygotowanie portalu ma mniej niż 10 minut.

Informacje zawarte w tym artykule dotyczą wdrażania tablic wirtualnego StorSimple w portalu Azure i Microsoft Azure dla instytucji rządowych chmury.

### <a name="get-started"></a>Rozpoczęcie pracy
Przepływ pracy wdrażania składa się z przygotowanie portalu, udostępniania wirtualnego tablicy w środowisku zwirtualizowanym i zakończeniu instalacji. Aby rozpocząć pracę z wdrożeniem tablicy wirtualnego StorSimple jako serwer plików lub serwera iSCSI, należy odwoływać się do następujących zasobów tabelaryczne.

#### <a name="deployment-articles"></a>Wdrażania

Aby wdrożyć tablica wirtualnego StorSimple, można znaleźć w następujących artykułach w określonej kolejności.

| **#** | **W tym kroku** | **Aby to zrobić...** | **I korzystać z tych dokumentów.** |
| --- | --- | --- | --- |
| 1. |**Konfigurowanie portalu Azure** |Tworzenie i konfigurowanie usługi Menedżer StorSimple urządzenia przed inicjowania obsługi administracyjnej dla tablicy wirtualne StorSimple. |[Przygotowanie portalu](storsimple-virtual-array-deploy1-portal-prep.md) |
| 2. |**Udostępnianie wirtualnych tablicy** |Dla funkcji Hyper-V udostępniania i Połącz z tablicą wirtualnego StorSimple na komputerze hosta z funkcją Hyper-V w systemie Windows Server 2012 R2, Windows Server 2012 lub Windows Server 2008 R2. <br></br> <br></br> Dla VMware udostępniania i nawiąż połączenie z tablicą wirtualnego StorSimple na komputerze hosta z systemem VMware ESXi 5.0, 5.5 lub 6.0.<br></br> |[Udostępnianie wirtualnych tablicy w funkcji Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) <br></br> <br></br> [Udostępnianie wirtualnych tablicy w środowisku programu VMware](storsimple-virtual-array-deploy2-provision-vmware.md) |
| 3. |**Konfigurowanie wirtualnego tablicy** |Dla serwera plików wykonywania początkowej konfiguracji, zarejestruj StorSimple serwera plików, a następnie przeprowadzić konfigurację urządzenia. Można następnie udostępnić udziałów SMB. <br></br> <br></br> Dla serwera iSCSI wykonywania początkowej konfiguracji, Zarejestruj serwer iSCSI StorSimple i ukończyć instalację na urządzeniu. Następnie można alokować woluminy iSCSI. |[Konfigurowanie wirtualnego tablicy jako serwer plików](storsimple-virtual-array-deploy3-fs-setup.md)<br></br> <br></br>[Konfigurowanie wirtualnego tablicy jako serwer iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md) |

Teraz można rozpocząć konfigurowanie portalu Azure.

## <a name="configuration-checklist"></a>Lista kontrolna dotycząca konfiguracji

Lista kontrolna dotycząca konfiguracji opisano informacje, które należy zebrać przed rozpoczęciem konfigurowania oprogramowania na tablica wirtualnego StorSimple. Te informacje wcześniejsze przygotowanie pomaga usprawnić proces wdrażania urządzenia StorSimple w środowisku. Zależności od tego, czy tablica wirtualne StorSimple jest wdrożona jako serwera plików lub serwera iSCSI, potrzebujesz jednego z następujących list kontrolnych.

* Pobierz [Lista kontrolna konfiguracji serwera plików tablicy wirtualnego StorSimple](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf).
* Pobierz [iSCSI tablicy wirtualnego StorSimple Lista kontrolna dotycząca konfiguracji serwera](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf).

## <a name="prerequisites"></a>Wymagania wstępne

W tym miejscu możesz znaleźć wymagania wstępne dotyczące konfiguracji dla usługi Menedżer urządzeń StorSimple, tablica wirtualnego StorSimple i sieci centrum danych.

### <a name="for-the-storsimple-device-manager-service"></a>Usługa Menedżer urządzeń StorSimple

Przed rozpoczęciem upewnij się, że:

* Masz konto Microsoft z poświadczeniami dostępu.
* Masz konto magazynu platformy Microsoft Azure z poświadczeniami dostępu.
* Subskrypcja platformy Microsoft Azure powinno być włączone dla usługi Menedżer StorSimple urządzenia.

### <a name="for-the-storsimple-virtual-array"></a>Dla tablicy wirtualnego StorSimple

Przed wdrożeniem wirtualnego tablicy, upewnij się, że:

* Masz dostęp do systemu hosta funkcji Hyper-V w systemie Windows Server 2008 R2 lub nowszym lub VMware (ESXi 5.0, 5.5 lub 6.0), które mogą być używane do udostępniania urządzenia.
* System hosta jest w stanie dedykować następujących zasobów, aby udostępnić wirtualny tablica:
  
  * Co najmniej 4 rdzenie.
  * Co najmniej 8 GB pamięci RAM. Jeśli planujesz skonfigurować wirtualny tablicy jako serwer plików, 8 GB obsługuje 2 milionów plików. Należy 16 GB pamięci RAM 2-4 miliony plików obsługi.
  * Jeden interfejs sieciowy.
  * Dysk wirtualny 500 GB danych systemu.

### <a name="for-the-datacenter-network"></a>Dla sieci centrum danych

Przed rozpoczęciem upewnij się, że:

* Sieć w centrum danych jest skonfigurowany zgodnie z wymaganiami sieci dla urządzenia StorSimple. Aby uzyskać więcej informacji, zobacz [wymagania systemowe tablicy wirtualnego StorSimple](storsimple-ova-system-requirements.md).
* Tablica wirtualnego StorSimple ma dedykowany 5 MB/s przepustowości połączenia z Internetem (lub więcej) dostępne przez cały czas. Przepustowość nie powinien być współużytkowany z innych aplikacji.

## <a name="step-by-step-preparation"></a>Krok przygotowania

Poniższe instrukcje krok po kroku umożliwia przygotowanie portalem usługi Menedżer StorSimple urządzenia.

## <a name="step-1-create-a-new-service"></a>Krok 1. Tworzenie nowej usługi

Jedno wystąpienie usługi Menedżer StorSimple urządzeń można zarządzać wiele tablic wirtualne StorSimple. Wykonaj poniższe kroki, aby utworzyć wystąpienie usługi Menedżer urządzeń StorSimple. Jeśli masz istniejącą usługę Menedżer StorSimple urządzenia do zarządzania z macierzami wirtualnego, Pomiń ten krok i przejdź do [krok 2: pobieranie klucza rejestracji usługi](#step-2-get-the-service-registration-key).

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

> [!IMPORTANT]
> Jeśli nie włączono automatycznego tworzenia konta magazynu przy użyciu usługi, po pomyślnym utworzeniu usługi musisz utworzyć co najmniej jedno konto magazynu.
> 
> * Jeśli nie utworzono automatycznie konta magazynu, przejdź do kroku [Konfigurowanie nowego konta magazynu dla usługi](#optional-step-configure-a-new-storage-account-for-the-service) w celu uzyskania szczegółowych informacji.
> * Jeśli włączono automatyczne tworzenie konta magazynu, przejdź do części [Krok 2. Pobieranie klucza rejestracji usługi](#step-2-get-the-service-registration-key).
> 
> 

## <a name="step-2-get-the-service-registration-key"></a>Krok 2. Pobieranie klucza rejestracji usługi

Po skonfigurowaniu i uruchomieniu usługi Menedżer urządzeń StorSimple musisz pobrać klucz rejestracji usługi. Ten klucz służy do rejestrowania urządzenia StorSimple i łączenia go z usługą.

Wykonaj poniższe kroki w [portalu Azure](https://portal.azure.com/).

[!INCLUDE [storsimple-virtual-array-get-service-registration-key](../../includes/storsimple-virtual-array-get-service-registration-key.md)]

> [!NOTE]
> Klucz rejestracji usługi służy do rejestrowania wszystkich urządzeń Menedżer urządzeń StorSimple, które należy zarejestrować przy użyciu usługi Menedżer StorSimple urządzenia.
> 
> 

## <a name="step-3-download-the-virtual-array-image"></a>Krok 3: Pobierz obraz wirtualnego tablicy

Po klucz rejestracji usługi, będzie konieczne pobranie obrazu odpowiednie tablicy wirtualnego do udostępniania wirtualnego tablicy w systemie hosta. Obrazy wirtualnego tablicy są zależne od systemu operacyjnego, można pobrać ze strony szybkiego startu w portalu Azure.

> [!IMPORTANT]
> Oprogramowanie działające na tablicy wirtualnego StorSimple można używać tylko w usłudze Menedżer StorSimple urządzenia.
> 
> 

Wykonaj poniższe kroki w [portalu Azure](https://portal.azure.com/).

#### <a name="to-get-the-virtual-array-image"></a>Do pobrania obrazu wirtualnego tablicy

1. Zaloguj się do [Azure Portal](https://portal.azure.com/). 
2. W portalu Azure kliknij **Przeglądaj > menedżerów urządzenia StorSimple**.
3. Wybierz istniejącą usługę Menedżer StorSimple urządzenia. W **Menedżera urządzeń StorSimple** bloku, kliknij przycisk **Szybki Start**. 
4. Kliknij link odpowiadający obrazu, który ma zostać pobrana z Microsoft Download Center. Pliki są około 4.8 GB.
   
   * VHDX dla funkcji Hyper-V w systemie Windows Server 2012 lub nowszym
   * Wirtualnego dysku twardego funkcji Hyper-v w systemie Windows Server 2008 R2 lub nowszym
   * VMDK dla VMWare ESXi 5.0, 5.5 lub 6.0
5. Pobierz i rozpakuj plik lokalny dysk, co Zanotuj którym znajduje się plik rozpakowane.

## <a name="optional-step-configure-a-new-storage-account-for-the-service"></a>Krok opcjonalny: Konfigurowanie nowego konta magazynu dla usługi

Ten krok jest opcjonalny i należy wykonać tylko wtedy, gdy nie włączono automatycznego tworzenia konta magazynu z usługą.

Jeśli musisz utworzyć konto magazynu platformy Azure w innym regionie, zobacz [jak utworzyć konto magazynu](../storage/common/storage-create-storage-account.md#create-a-storage-account) instrukcje krok po kroku.

Wykonaj poniższe kroki w [portalu Azure](https://ms.portal.azure.com/) na stronie usługi Menedżer StorSimple urządzenia można dodać istniejącego konta magazynu Microsoft Azure.

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Aby dodać poświadczeniami konta magazynu, które mają tej samej subskrypcji platformy Azure jako usługa Menedżera urządzeń

1. Przejdź do usługi Menedżera urządzeń, wybierz opcję i kliknij ją dwukrotnie. Spowoduje to otwarcie **omówienie** bloku.
2. Wybierz **poświadczeń konta magazynu** w **konfiguracji** sekcji.
3. Kliknij pozycję **Dodaj**.
4. W **dodawania konta magazynu** blok, wykonaj następujące czynności:
   
    1. Aby uzyskać **subskrypcji**, wybierz pozycję **bieżącego**.
   
    2. Podaj nazwę konta magazynu Azure.
   
    3. Wybierz **włączyć** można utworzyć bezpiecznego kanału komunikacji sieciowej między urządzeniem StorSimple i chmury. Wybierz **wyłączyć** tylko wtedy, gdy pracujesz w chmurze prywatnej.
   
    4. Kliknij pozycję **Dodaj**. Użytkownik jest powiadamiany po pomyślnym utworzeniu konta magazynu.<br></br>
   
     ![Dodawanie istniejących poświadczeń dla konta magazynu](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

## <a name="next-step"></a>Następny krok

Następnym krokiem jest do udostępnienia maszyny wirtualnej dla macierzy wirtualne StorSimple. W zależności od systemu operacyjnego hosta Zobacz szczegółowe instrukcje w:

* [Zapewnij tablicą wirtualnego StorSimple w funkcji Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md)
* [Zapewnij tablicą wirtualnego StorSimple w środowisku programu VMware](storsimple-virtual-array-deploy2-provision-vmware.md)

