---
title: Przygotowywanie portalu dla rozwiązania StorSimple Virtual Array | Dokumentacja firmy Microsoft
description: Pierwszy samouczek wdrażania rozwiązania StorSimple virtual array obejmuje przygotowanie witryny Azure portal
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
ms.date: 01/11/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7c6f0a6371b38f0271237db0f7d80b831ecc145c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62127144"
---
# <a name="deploy-storsimple-virtual-array---prepare-the-azure-portal"></a>Wdrażanie rozwiązania StorSimple Virtual Array — przygotowanie witryny Azure portal

![](./media/storsimple-virtual-array-deploy1-portal-prep/getstarted4.png)
## <a name="overview"></a>Omówienie

Jest to pierwszy artykuł z tej serii samouczków wdrożenia wymagane całkowicie wdrożenie macierz wirtualna jako serwer plików lub serwer iSCSI przy użyciu modelu usługi Resource Manager. W tym artykule opisano przygotowania wymagane do tworzenia i konfigurowania usługi Menedżer urządzeń StorSimple przed aprowizowania macierzy wirtualnej. Ten artykuł zawiera również linki się lista kontrolna konfiguracji wdrażania i konfiguracji wymagań wstępnych.

Do ukończenia procesu instalacji i konfiguracji niezbędne są uprawnienia administratora. Firma Microsoft zaleca przejrzenie listy kontrolnej dotyczącej konfiguracji wdrożenia, przed przystąpieniem do wykonywania. Przygotowanie portalu zajmuje mniej niż 10 minut.

Informacje zawarte w tym artykule dotyczy wdrożenia macierzach wirtualnych StorSimple w witrynie Azure portal i chmury platformy Microsoft Azure dla instytucji rządowych.

### <a name="get-started"></a>Rozpoczęcie pracy
Wdrażanie przepływu pracy składa się z przygotowywanie portalu, aprowizacja macierzy wirtualnej w środowisku zwirtualizowanym i kończenie instalacji. Aby rozpocząć pracę z wdrożeniem rozwiązania StorSimple Virtual Array jako serwer plików lub serwer iSCSI, należy można znaleźć w następujących zasobach tabelaryczne.

#### <a name="deployment-articles"></a>Wdrażania

Aby wdrożyć rozwiązania StorSimple Virtual Array, zapoznaj się z następującymi artykułami w zalecanej sekwencji.

| **#** | **W tym kroku** | **Można to zrobić...** | **I korzystać z tych dokumentów.** |
| --- | --- | --- | --- |
| 1. |**Konfigurowanie witryny Azure portal** |Tworzenie i konfigurowanie usługi Menedżer urządzeń StorSimple przed aprowizacji rozwiązania StorSimple Virtual Array. |[Przygotowywanie portalu](storsimple-virtual-array-deploy1-portal-prep.md) |
| 2. |**Aprowizowanie macierzy wirtualnej** |Dla funkcji Hyper-V udostępniania i połącz rozwiązania StorSimple Virtual Array na komputerze hosta z funkcją Hyper-V w systemie Windows Server 2012 R2, Windows Server 2012 lub Windows Server 2008 R2. <br></br> <br></br> Dla oprogramowania VMware udostępniania i połącz rozwiązania StorSimple Virtual Array na komputerze hosta z systemem VMware ESXi 5.0, 5.5, 6.0 lub 6.5.<br></br> |[Aprowizowanie macierzy wirtualnej funkcji Hyper-v](storsimple-virtual-array-deploy2-provision-hyperv.md) <br></br> <br></br> [Aprowizowanie macierzy wirtualnej w środowisku programu VMware](storsimple-virtual-array-deploy2-provision-vmware.md) |
| 3. |**Konfigurowanie macierzy wirtualnej** |Dla swojego serwera plików wykonywania początkowej konfiguracji, zarejestrować swojego serwera plików StorSimple i przeprowadzić konfigurację urządzenia. Następnie można aprowizować udziały SMB. <br></br> <br></br> Dla serwera iSCSI wykonywania początkowej konfiguracji, zarejestrować serwer iSCSI StorSimple i przeprowadzić konfigurację urządzenia. Następnie można alokować woluminy iSCSI. |[Konfigurowanie macierzy wirtualnej jako serwera plików](storsimple-virtual-array-deploy3-fs-setup.md)<br></br> <br></br>[Konfigurowanie macierzy wirtualnej jako serwera iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md) |

Teraz możesz rozpocząć konfigurowanie witryny Azure Portal.

## <a name="configuration-checklist"></a>Lista kontrolna dotycząca konfiguracji

Lista kontrolna dotycząca konfiguracji opisano informacje, które należy zebrać przed rozpoczęciem konfigurowania oprogramowania na rozwiązania StorSimple Virtual Array. Przygotowanie tych informacji usprawnia proces wdrażania urządzenia StorSimple w środowisku użytkownika. W zależności od tego, czy rozwiązania StorSimple Virtual Array jest wdrażany jako serwer plików lub serwer iSCSI, musisz dysponować jedną z poniższych list kontrolnych.

* Pobierz [Lista kontrolna konfiguracji serwera plików macierzy wirtualnej StorSimple](https://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf).
* Pobierz [iSCSI macierzy wirtualnej StorSimple Configuration Server — lista kontrolna](https://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf).

## <a name="prerequisites"></a>Wymagania wstępne

W tym miejscu możesz znaleźć wymagania wstępne dotyczące konfiguracji dla usługi Menedżer urządzeń StorSimple, rozwiązania StorSimple Virtual Array i sieci centrów danych.

### <a name="for-the-storsimple-device-manager-service"></a>Usługa Menedżer urządzeń StorSimple

Przed rozpoczęciem upewnij się, że:

* Masz konto Microsoft z poświadczeniami dostępu.
* Masz konto magazynu platformy Microsoft Azure z poświadczeniami dostępu.
* Twoja subskrypcja Microsoft Azure powinna być włączona dla usługi Menedżer urządzeń StorSimple.

### <a name="for-the-storsimple-virtual-array"></a>Dla macierzy wirtualnej StorSimple

Przed wdrożeniem macierzy wirtualnej, upewnij się, że:

* Masz dostęp do systemu hosta funkcji Hyper-V w systemie Windows Server 2008 R2 lub nowszym lub VMware (ESXi 5.0, 5.5, 6.0 lub 6.5), które mogą być używane do aprowizacji urządzenia.
* System hosta jest w stanie dedykować następujących zasobów, aby aprowizować macierz wirtualna:
  
  * Co najmniej 4 rdzenie.
  * Co najmniej 8 GB pamięci RAM. Jeśli użytkownik chce skonfigurować macierzy wirtualnej jako serwera plików, 8 GB obsługuje 2 miliony plików. Należy 16 GB pamięci RAM na potrzeby obsługi 2-4 miliony plików.
  * Jeden interfejs sieciowy.
  * Dysk wirtualny 500 GB danych systemu.

### <a name="for-the-datacenter-network"></a>Sieć centrum danych

Przed rozpoczęciem upewnij się, że:

* Sieć w centrum danych jest skonfigurowany dla wymagań sieciowych dotyczących urządzenia StorSimple. Aby uzyskać więcej informacji, zobacz [wymagania systemowe macierzy wirtualnej StorSimple](storsimple-ova-system-requirements.md).
* Rozwiązania StorSimple Virtual Array jest dedykowany 5 przepustowości Internetu MB/s (lub więcej) dostępnych przez cały czas. Ta przepustowość nie powinna być współużytkowana z żadną inną aplikacją.

## <a name="step-by-step-preparation"></a>Instrukcje krok po kroku przygotowania

Poniższe instrukcje krok po kroku umożliwia przygotowanie portalem usługi Menedżer urządzeń StorSimple.

## <a name="step-1-create-a-new-service"></a>Krok 1: Tworzenie nowej usługi

Jedno wystąpienie usługi Menedżer urządzeń StorSimple można zarządzać wieloma macierze wirtualne StorSimple. Wykonaj poniższe kroki, aby utworzyć wystąpienie usługi Menedżer urządzeń StorSimple. Jeśli masz istniejącą usługę Menedżer urządzeń StorSimple do zarządzania z macierzami wirtualnymi, Pomiń ten krok i przejdź do [krok 2: Pobierz klucz rejestracji usługi](#step-2-get-the-service-registration-key).

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

> [!IMPORTANT]
> Jeśli nie włączono automatycznego tworzenia konta magazynu przy użyciu usługi, po pomyślnym utworzeniu usługi musisz utworzyć co najmniej jedno konto magazynu.
> 
> * Jeśli nie utworzono automatycznie konta magazynu, przejdź do kroku [Konfigurowanie nowego konta magazynu dla usługi](#optional-step-configure-a-new-storage-account-for-the-service) w celu uzyskania szczegółowych informacji.
> * Jeśli włączono automatyczne tworzenie konta magazynu, przejdź do strony [krok 2: Pobierz klucz rejestracji usługi](#step-2-get-the-service-registration-key).
> 
> 

## <a name="step-2-get-the-service-registration-key"></a>Krok 2: Pobieranie klucza rejestracji usługi

Po skonfigurowaniu i uruchomieniu usługi Menedżer urządzeń StorSimple musisz pobrać klucz rejestracji usługi. Ten klucz służy do rejestrowania urządzenia StorSimple i łączenia go z usługą.

Wykonaj poniższe czynności w witrynie [Azure Portal](https://portal.azure.com/).

[!INCLUDE [storsimple-virtual-array-get-service-registration-key](../../includes/storsimple-virtual-array-get-service-registration-key.md)]

> [!NOTE]
> Klucz rejestracji usługi służy do rejestrowania wszystkich urządzeń Menedżer urządzeń StorSimple, które należy zarejestrować w usłudze Menedżer urządzeń StorSimple.
> 
> 

## <a name="step-3-download-the-virtual-array-image"></a>Krok 3: Pobierz obraz macierzy wirtualnej

Po umieszczeniu klucza rejestracji usługi, należy pobrać obraz macierzy wirtualnej odpowiedniego do aprowizowania macierzy wirtualnej w systemie hosta. Obrazy macierzy wirtualnej są zależne od systemu operacyjnego i można go pobrać ze strony Szybki Start w witrynie Azure portal.

> [!IMPORTANT]
> Oprogramowanie działające na macierz wirtualną StorSimple można używać tylko w usłudze Menedżer urządzeń StorSimple.
> 
> 

Wykonaj poniższe czynności w witrynie [Azure Portal](https://portal.azure.com/).

#### <a name="to-get-the-virtual-array-image"></a>Aby uzyskać obraz macierzy wirtualnej

1. Zaloguj się do [Azure Portal](https://portal.azure.com/). 
2. W witrynie Azure portal kliknij pozycję **Przeglądaj > Menedżerowie urządzeń StorSimple**.
3. Wybierz istniejącą usługę Menedżer urządzeń StorSimple. W **Menedżer urządzeń StorSimple** bloku kliknij **— Szybki Start**. 
4. Kliknij link odpowiadający obraz, który chcesz pobrać z Microsoft Download Center. Pliki obrazów mają rozmiar około 4,8 GB.
   
   * Dysk VHDX dla funkcji Hyper-V w systemie Windows Server 2012 lub nowszy
   * Wirtualny dysk twardy dla funkcji Hyper-V w systemie Windows Server 2008 R2 lub nowszy
   * Dysk VMDK dla oprogramowania VMWare ESXi 5.0, 5.5, 6.0 lub 6.5
5. Pobierz i rozpakuj plik na lokalnym dysku. Zanotuj lokalizację rozpakowanego pliku.

## <a name="optional-step-configure-a-new-storage-account-for-the-service"></a>Krok opcjonalny: Konfigurowanie nowego konta magazynu dla usługi

Ten krok jest opcjonalny i należy wykonać tylko wtedy, gdy nie włączono automatycznego tworzenia konta magazynu przy użyciu usługi.

Jeśli musisz utworzyć konto usługi Azure storage w innym regionie, zobacz [sposób tworzenia konta magazynu](../storage/common/storage-quickstart-create-account.md) instrukcje krok po kroku.

Wykonaj poniższe kroki w [witryny Azure portal](https://ms.portal.azure.com/) na stronie usługi Menedżer urządzeń StorSimple, aby dodać istniejące konto magazynu Microsoft Azure.

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Aby dodać poświadczeń konta magazynu, który ma tej samej subskrypcji platformy Azure jako usługi Menedżer urządzeń

1. Przejdź do usługi Menedżer urządzeń, wybierz i kliknij ją dwukrotnie. Spowoduje to otwarcie **Przegląd** bloku.
2. Wybierz **poświadczeń konta magazynu** w ramach **konfiguracji** sekcji.
3. Kliknij pozycję **Add** (Dodaj).
4. W **Dodaj konto magazynu** blok, wykonaj następujące czynności:
   
   1. Aby uzyskać **subskrypcji**, wybierz opcję **bieżącego**.
   
   2. Podaj nazwę konta usługi Azure storage.
   
   3. Wybierz **Włącz** tworzy bezpieczny kanał komunikacji sieciowej między chmurą a urządzeniem StorSimple. Wybierz **wyłączyć** tylko wtedy, gdy pracujesz w chmurze prywatnej.
   
   4. Kliknij pozycję **Add** (Dodaj). Otrzymasz powiadomienie po pomyślnym utworzeniu konta magazynu.<br></br>
   
      ![Dodawanie istniejących poświadczeń konta magazynu](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

## <a name="next-step"></a>Następny krok

Następnym krokiem jest do aprowizowania maszyny wirtualnej dla rozwiązania StorSimple Virtual Array. W zależności od systemu operacyjnego hosta szczegółowe instrukcje znajdują się w temacie:

* [Aprowizowanie macierzy wirtualnej StorSimple w funkcji Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md)
* [Aprowizowanie macierzy wirtualnej StorSimple w środowisku programu VMware](storsimple-virtual-array-deploy2-provision-vmware.md)

