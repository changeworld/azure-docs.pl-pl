---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/25/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 5104c3b4446a8d3747ce7cc1648ef05dd117eb3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74013798"
---
Program Storage Explorer 1.10.0 umożliwia użytkownikom przekazywanie, pobieranie i kopiowanie dysków zarządzanych, a także tworzenie migawek. Dzięki tym dodatkowym możliwościom można użyć Eksploratora magazynu do migracji danych z lokalnego środowiska na platformę Azure i migracji danych między regionami platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten artykuł, musisz wykonać następujące czynności:
- Subskrypcja platformy Azure
- Co najmniej jeden dysk zarządzany platformy Azure
- Najnowsza wersja [Eksploratora usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/)

## <a name="connect-to-an-azure-subscription"></a>Łączenie się z subskrypcją platformy Azure

Jeśli Eksplorator magazynu nie jest połączony z platformą Azure, nie będzie można go używać do zarządzania zasobami. Ta sekcja przechodzi na łączenie go z kontem platformy Azure, dzięki czemu można zarządzać zasobami przy użyciu Eksploratora magazynu.

1. Uruchom Eksploratora usługi Azure Storage i kliknij ikonę **wtyczki** po lewej stronie.

    ![Kliknij ikonę wtyczki](media/disks-upload-vhd-to-managed-disk-storage-explorer/plug-in-icon.png)

1. Wybierz **pozycję Dodaj konto platformy Azure**, a następnie kliknij przycisk **Dalej**.

    ![Dodawanie konta platformy Azure](media/disks-upload-vhd-to-managed-disk-storage-explorer/connect-to-azure.png)

1. W oknie dialogowym **Azure Sign in** wprowadź poświadczenia platformy Azure.

    ![Okno dialogowe logowania do platformy Azure](media/disks-upload-vhd-to-managed-disk-storage-explorer/sign-in.png)

1. Wybierz subskrypcję z listy, a następnie kliknij przycisk **Zastosuj**.

    ![Wybierz swoją subskrypcję](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-subscription.png)

## <a name="upload-a-managed-disk-from-an-on-prem-vhd"></a>Przekazywanie dysku zarządzanego z dysku VHD

1. W lewym okienku rozwiń pozycję **Dyski** i wybierz grupę zasobów, do której chcesz przekazać dysk.

    ![Wybieranie grupy zasobów 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Wybierz **pozycję Przekaż**.

    ![Wybieranie pozycji Przekaż](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-button.png)

1. W **obszarze Przekaż wirtualne dyski Określ** źródłowy dysk WIRTUALNY, nazwę dysku, typ systemu operacyjnego, region, do którego chcesz przekazać dysk, a także typ konta. W niektórych regionach obsługiwane są strefy dostępności, dla tych regionów można wybrać wybraną strefę.
1. Wybierz **pozycję Utwórz,** aby rozpocząć przekazywanie dysku.

    ![Prześlij okno dialogowe vhd](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-vhd-dialog.png)

1. Stan przesłania będzie teraz wyświetlany w części **Działania**.

    ![Stan przesyłania](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-uploading.png)

1. Jeśli przekazywanie zostało zakończone, a dysk nie jest widoczny w prawym okienku, wybierz pozycję **Odśwież**.

## <a name="download-a-managed-disk"></a>Pobieranie dysku zarządzanego

W poniższych krokach wyjaśniono, jak pobrać dysk zarządzany na dysk VHD on-prem. Stan dysku musi być **niepodłączony,** aby można było pobrać, nie można pobrać **dołączonego** dysku.

1. W lewym okienku, jeśli nie jest jeszcze rozwinięty, rozwiń pozycję **Dyski** i wybierz grupę zasobów, z której chcesz pobrać dysk.

    ![Wybieranie grupy zasobów 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. W prawym okienku wybierz dysk, który chcesz pobrać.
1. Wybierz **pozycję Pobierz,** a następnie wybierz miejsce, w którym chcesz zapisać dysk.

    ![Pobieranie dysku zarządzanego](media/disks-upload-vhd-to-managed-disk-storage-explorer/download-button.png)

1. Wybierz **pozycję Zapisz,** a rozpocznie się pobieranie dysku. Stan pobierania zostanie wyświetlony w **działaniu**.

    ![Stan pobierania](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-downloading.png)

## <a name="copy-a-managed-disk"></a>Kopiowanie dysku zarządzanego

Za pomocą Eksploratora magazynu można skopiować dysk manged w obrębie lub między regionami. Aby skopiować dysk:

1. Z listy rozwijanej **Dyski** po lewej stronie wybierz grupę zasobów zawierającą dysk, który chcesz skopiować.

    ![Wybieranie grupy zasobów 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. W prawym okienku wybierz dysk, który chcesz skopiować, a następnie wybierz pozycję **Kopiuj**.

    ![Kopiowanie dysku zarządzanego](media/disks-upload-vhd-to-managed-disk-storage-explorer/copy-button.png)

1. W lewym okienku wybierz grupę zasobów, w której chcesz wkleić dysk.

    ![Wybieranie grupy zasobów 2](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg2.png)

1. Wybierz **pozycję Wklej** w prawym okienku.

    ![Wklejanie dysku zarządzanego](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-button.png)

1. W oknie dialogowym **Wklej dysk** wypełnij wartości. Można również określić strefę dostępności w obsługiwanych regionach.

    ![Okno dialogowe Wklejanie dysku](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-disk-dialog.png)

1. Wybierz **pozycję Wklej,** a dysk rozpocznie kopiowanie, stan zostanie wyświetlony w obszarze **Działania**.

    ![Stan kopiowania wklejać](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-copying.png)

## <a name="create-a-snapshot"></a>Tworzenie migawki

1. Z listy rozwijanej **Dyski** po lewej stronie wybierz grupę zasobów zawierającą dysk, który chcesz migać.

    ![Wybieranie grupy zasobów 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Po prawej stronie wybierz dysk, który chcesz utworzyć migawkę, a następnie wybierz pozycję **Utwórz migawkę**.

    ![Tworzenie migawki](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-button.png)

1. W **obszarze Utwórz migawkę**określ nazwę migawki oraz grupę zasobów, w której chcesz ją utworzyć. Następnie wybierz pozycję **Utwórz**.

    ![Okno dialogowe Tworzenie migawki](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-dialog.png)

1. Po utworzeniu migawki można wybrać **otwórz w portalu** w **działaniach,** aby wyświetlić migawkę w witrynie Azure portal.

    ![Otwórz migawkę w portalu](media/disks-upload-vhd-to-managed-disk-storage-explorer/open-in-portal.png)

## <a name="next-steps"></a>Następne kroki
