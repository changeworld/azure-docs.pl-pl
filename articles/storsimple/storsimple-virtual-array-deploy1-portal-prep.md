---
title: Przygotowanie portalu do tablicy wirtualnej StorSimple
description: Pierwszy samouczek do wdrożenia tablicy wirtualnej StorSimple polega na przygotowaniu witryny Azure portal
author: alkohli
ms.assetid: 68a4cfd3-94c9-46cb-805c-46217290ce02
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7f2c7a6f9c1146627f81dc20386c59a91724b245
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254536"
---
# <a name="deploy-storsimple-virtual-array---prepare-the-azure-portal"></a>Wdrażanie tablicy wirtualnej StorSimple — przygotowywanie portalu Azure

![](./media/storsimple-virtual-array-deploy1-portal-prep/getstarted4.png)


## <a name="overview"></a>Omówienie

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Jest to pierwszy artykuł z serii samouczków wdrażania wymaganych do całkowitego wdrożenia tablicy wirtualnej jako serwera plików lub serwera iSCSI przy użyciu modelu Menedżera zasobów. W tym artykule opisano przygotowanie wymagane do utworzenia i skonfigurowania usługi StorSimple Device Manager przed inicjowania obsługi administracyjnej tablicy wirtualnej. W tym artykule znajduje się również łącza do listy kontrolnej konfiguracji wdrożenia i wymagań wstępnych konfiguracji.

Do ukończenia procesu instalacji i konfiguracji niezbędne są uprawnienia administratora. Zaleca się przejrzenie listy kontrolnej konfiguracji wdrożenia przed rozpoczęciem. Przygotowanie portalu zajmuje mniej niż 10 minut.

Informacje opublikowane w tym artykule dotyczą wdrażania tablic wirtualnych StorSimple w witrynie Azure portal i microsoft azure government cloud.

### <a name="get-started"></a>Wprowadzenie
Przepływ pracy wdrażania polega na przygotowaniu portalu, inicjowaniu obsługi administracyjnej tablicy wirtualnej w środowisku zwirtualizowanym i zakończeniu instalacji. Aby rozpocząć pracę z wdrożeniem tablicy wirtualnej StorSimple jako serwer plików lub serwer iSCSI, należy odwołać się do następujących zasobów tabeli.

#### <a name="deployment-articles"></a>Artykuły dotyczące wdrażania

Aby wdrożyć storsimple virtual array, odnoszą się do następujących artykułów w określonej kolejności.

| **#** | **W tym kroku** | **Robisz to ...** | **I korzystać z tych dokumentów.** |
| --- | --- | --- | --- |
| 1. |**Konfigurowanie portalu platformy Azure** |Utwórz i skonfiguruj usługę StorSimple Device Manager przed inicjowania obsługi administracyjnej storsimple tablicy wirtualnej. |[Przygotowanie portalu](storsimple-virtual-array-deploy1-portal-prep.md) |
| 2. |**Aprowizuj tablicę wirtualną** |W przypadku funkcji Hyper-V aprowizowanie i łączenie się z macierzą wirtualną StorSimple w systemie hosta z systemem Hyper-V w systemach Windows Server 2012 R2, Windows Server 2012 lub Windows Server 2008 R2. <br></br> <br></br> W przypadku oprogramowania VMware aprowizuj i połącz się z platformą Wirtualną StorSimple w systemie hosta z systemem VMware ESXi 5.0, 5.5, 6.0 lub 6.5.<br></br> |[Aprowizuj tablicę wirtualną w funkcji Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) <br></br> <br></br> [Aprowizuj tablicę wirtualną w VMware](storsimple-virtual-array-deploy2-provision-vmware.md) |
| 3. |**Konfigurowanie tablicy wirtualnej** |W przypadku serwera plików wykonaj wstępną konfigurację, zarejestruj serwer plików StorSimple i zakończ konfigurację urządzenia. Następnie można aprowizować udziały SMB. <br></br> <br></br> W przypadku serwera iSCSI wykonaj wstępną konfigurację, zarejestruj serwer iSCSI StorSimple i zakończ konfigurację urządzenia. Następnie można aprowizować woluminy iSCSI. |[Konfigurowanie tablicy wirtualnej jako serwera plików](storsimple-virtual-array-deploy3-fs-setup.md)<br></br> <br></br>[Konfigurowanie macierzy wirtualnej jako serwera iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md) |

Teraz możesz rozpocząć konfigurowanie witryny Azure Portal.

## <a name="configuration-checklist"></a>Lista kontrolna konfiguracji

Lista kontrolna konfiguracji opisuje informacje, które należy zebrać przed skonfigurowaniem oprogramowania w tablicy wirtualnej StorSimple. Przygotowanie tych informacji z wyprzedzeniem pomaga usprawnić proces wdrażania urządzenia StorSimple w twoim środowisku. W zależności od tego, czy macierz wirtualna StorSimple jest wdrażana jako serwer plików, czy serwer iSCSI, potrzebna jest jedna z następujących list kontrolnych.

* Pobierz [listę kontrolną konfiguracji serwera plików storsimple virtual array](https://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf).
* Pobierz listę kontrolną [konfiguracji serwera serwera iSCSI storSimple virtual array](https://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf).

## <a name="prerequisites"></a>Wymagania wstępne

W tym miejscu znajdują się wymagania wstępne dotyczące konfiguracji usługi StorSimple Device Manager, tablicy wirtualnej StorSimple i sieci centrum danych.

### <a name="for-the-storsimple-device-manager-service"></a>Usługa Menedżer urządzeń StorSimple

Przed rozpoczęciem upewnij się, że:

* Masz konto Microsoft z poświadczeniami dostępu.
* Masz konto magazynu platformy Microsoft Azure z poświadczeniami dostępu.
* Subskrypcja platformy Microsoft Azure powinna być włączona dla usługi StorSimple Device Manager.

### <a name="for-the-storsimple-virtual-array"></a>Dla tablicy wirtualnej StorSimple

Przed wdrożeniem tablicy wirtualnej upewnij się, że:

* Masz dostęp do systemu hosta z systemem Hyper-V w systemie Windows Server 2008 R2 lub nowszym lub VMware (ESXi 5.0, 5.5, 6.0 lub 6.5), który może służyć do aprowizowania urządzenia.
* System hosta jest w stanie poświęcić następujące zasoby do aprowizowania tablicy wirtualnej:
  
  * Co najmniej 4 rdzenie.
  * Co najmniej 8 GB pamięci RAM. Jeśli planujesz skonfigurować tablicę wirtualną jako serwer plików, 8 GB obsługuje 2 miliony plików. Potrzebujesz 16 GB pamięci RAM do obsługi 2 - 4 milionów plików.
  * Jeden interfejs sieciowy.
  * Dysk wirtualny o przekątną 500 GB do danych systemowych.

### <a name="for-the-datacenter-network"></a>Sieć centrum danych

Przed rozpoczęciem upewnij się, że:

* Sieć w centrum danych jest skonfigurowana zgodnie z wymaganiami sieciowymi dla urządzenia StorSimple. Aby uzyskać więcej informacji, zobacz [StorSimple Virtual Array Wymagania systemowe](storsimple-ova-system-requirements.md).
* Twoja tablica wirtualna StorSimple ma dedykowaną przepustowość Internetu 5 Mbps (lub więcej) dostępną przez cały czas. Ta przepustowość nie powinna być współużytkowana z żadną inną aplikacją.

## <a name="step-by-step-preparation"></a>Przygotowanie krok po kroku

Skorzystaj z poniższych instrukcji krok po kroku, aby przygotować portal do usługi StorSimple Device Manager.

## <a name="step-1-create-a-new-service"></a>Krok 1. Tworzenie nowej usługi

Pojedyncze wystąpienie usługi StorSimple Device Manager może zarządzać wieloma tablicami wirtualnymi StorSimple. Wykonaj poniższe kroki, aby utworzyć wystąpienie usługi Menedżer urządzeń StorSimple. Jeśli masz istniejącą usługę StorSimple Device Manager do zarządzania tablicami wirtualnymi, pomiń ten krok i przejdź do [kroku 2: Pobierz klucz rejestracji usługi](#step-2-get-the-service-registration-key).

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

Wykonaj poniższe czynności w witrynie [Azure Portal](https://portal.azure.com/).

[!INCLUDE [storsimple-virtual-array-get-service-registration-key](../../includes/storsimple-virtual-array-get-service-registration-key.md)]

> [!NOTE]
> Klucz rejestracji usługi służy do rejestrowania wszystkich urządzeń StorSimple Device Manager, które muszą zarejestrować się w usłudze StorSimple Device Manager.
> 
> 

## <a name="step-3-download-the-virtual-array-image"></a>Krok 3: Pobierz obraz tablicy wirtualnej

Po kluczu rejestracji usługi należy pobrać odpowiedni obraz tablicy wirtualnej, aby aprowizować tablicę wirtualną w systemie hosta. Obrazy tablic wirtualnych są specyficzne dla systemu operacyjnego i można je pobrać ze strony Szybki start w witrynie Azure portal.

> [!IMPORTANT]
> Oprogramowanie uruchomione w macierzy wirtualnej StorSimple może być używane tylko z usługą StorSimple Device Manager.
> 
> 

Wykonaj poniższe czynności w witrynie [Azure Portal](https://portal.azure.com/).

#### <a name="to-get-the-virtual-array-image"></a>Aby uzyskać obraz tablicy wirtualnej

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/). 
2. W witrynie Azure portal kliknij pozycję **Przeglądaj > Menedżerów urządzeń StorSimple**.
3. Wybierz istniejącą usługę StorSimple Device Manager. W bloku **Menedżer urządzeń StorSimple** kliknij pozycję **Szybki start**. 
4. Kliknij łącze odpowiadające obrazowi, który chcesz pobrać z Centrum pobierania Microsoft. Pliki obrazów mają rozmiar około 4,8 GB.
   
   * VHDX dla funkcji Hyper-V w systemie Windows Server 2012 i nowszym
   * VHD dla funkcji Hyper-V w systemie Windows Server 2008 R2 lub nowszym
   * VMDK dla VMWare ESXi 5.0, 5.5, 6.0 lub 6.5
5. Pobierz i rozpakuj plik na lokalnym dysku. Zanotuj lokalizację rozpakowanego pliku.

## <a name="optional-step-configure-a-new-storage-account-for-the-service"></a>Krok opcjonalny: konfigurowanie nowego konta magazynu dla usługi

Ten krok jest opcjonalny i powinien być wykonywany tylko wtedy, gdy nie włączysz automatycznego tworzenia konta magazynu w usłudze.

Jeśli chcesz utworzyć konto magazynu platformy Azure w innym regionie, zobacz [Jak utworzyć konto magazynu](../storage/common/storage-account-create.md) dla instrukcji krok po kroku.

Wykonaj następujące kroki w [witrynie Azure portal](https://ms.portal.azure.com/) na stronie usługi StorSimple Device Manager, aby dodać istniejące konto magazynu platformy Microsoft Azure.

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Aby dodać poświadczenia konta magazynu, które ma taką samą subskrypcję platformy Azure jak usługa Menedżer urządzeń

1. Przejdź do usługi Menedżer urządzeń, wybierz ją i kliknij dwukrotnie. Spowoduje to otwarcie **overview** blade.
2. Wybierz **poświadczenia konta magazynu** w sekcji **Konfiguracja.**
3. Kliknij przycisk **Dodaj**.
4. W bloku **Dodaj konto magazynu** wykonaj następujące czynności:
   
   1. W obszarze **Subskrypcja**wybierz pozycję **Bieżący**.
   
   2. Podaj nazwę swojego konta magazynu platformy Azure.
   
   3. Wybierz **włącz,** aby utworzyć bezpieczny kanał komunikacji sieciowej między urządzeniem StorSimple a chmurą. Wybierz **opcję Wyłącz** tylko wtedy, gdy działasz w chmurze prywatnej.
   
   4. Kliknij przycisk **Dodaj**. Użytkownik zostanie powiadomiony po pomyślnym utworzeniu konta magazynu.<br></br>
   
      ![Dodawanie istniejącego poświadczenia konta magazynu](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

## <a name="next-step"></a>Następny krok

Następnym krokiem jest aprowizowanie maszyny wirtualnej dla storsimple virtual array. W zależności od systemu operacyjnego hosta szczegółowe instrukcje znajdują się w temacie:

* [Aprowizuj tablicę wirtualną StorSimple w funkcji Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md)
* [Aprowizuj tablicę wirtualną StorSimple w VMware](storsimple-virtual-array-deploy2-provision-vmware.md)

