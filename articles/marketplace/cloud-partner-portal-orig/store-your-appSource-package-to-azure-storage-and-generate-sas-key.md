---
title: Store pakietu usługi AppSource do usługi Azure storage i wygenerować adresu URL przy użyciu klucza sygnatury dostępu Współdzielonego | Dokumentacja firmy Microsoft
description: Wyszczególnia kroki wymagane do przekazania i zabezpieczania pakietu usługi AppSource.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: ad0e6eaae5c0fad74ea484827e0f8d535cfbf579
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60750064"
---
<a name="store-your-appsource-package-to-azure-storage-and-generate-a-url-with-sas-key"></a>Store pakietu usługi AppSource do usługi Azure storage i wygenerować adresu URL przy użyciu klucza sygnatury dostępu Współdzielonego
=============================================================================

Aby zachować bezpieczeństwo plików, wszyscy partnerzy należy ich plik pakietu usługi AppSource jest przechowywany na koncie magazynu obiektów blob platformy Azure i używać klucza sygnatury dostępu Współdzielonego do udostępniania go. Plik pakietu zostaną pobrane z lokalizacji magazynu platformy Azure do certyfikacji i z niej korzystać w przypadku wersji próbnej usługi AppSource.

Do przekazania pakietu do magazynu obiektów blob, należy użyć następujących czynności:

1. Przejdź do <https://azure.microsoft.com> i Utwórz bezpłatne konto wersji próbnej lub rozliczane.

2. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

3. Utwórz nowe konto magazynu, klikając **+ nowy** i przechodząc do **dane + magazyn** konta.

   ![Dane + bloku magazynu w portalu Microsoft Azure](media/CRMScreenShot7.png)

4. Wprowadź **nazwa** i **grupy zasobów** nazwę i kliknij przycisk **Utwórz** przycisku.

   ![Tworzenie konta magazynu w portalu Microsoft Azure](media/CRMScreenShot8.png)

5. Przejdź do swojej nowo utworzonej grupy zasobów i utworzyć nowy kontener obiektów blob.

   ![Przekaż pakiet jako obiekt blob przy użyciu portalu platformy Microsoft Azure](media/CRMScreenShot9.png)

6. Jeśli jeszcze tego nie zrobiono, Pobierz i zainstaluj program Microsoft [Eksploratora usługi Azure Storage](https://storageexplorer.com/).

7. Otwórz Eksploratora usługi Storage i użyć ikony, aby nawiązać połączenie z kontem usługi Azure storage.

8. Przejdź do utworzonego kontenera obiektów blob, a następnie kliknij przycisk **przekazywanie** można dodać pliku zip pakietu.

   ![Przekaż pakiet za pomocą Eksploratora magazynu firmy Microsoft](media/CRMScreenShot10.png)

9. Kliknij prawym przyciskiem myszy plik i wybierz **Uzyskaj sygnaturę dostępu współdzielonego**.

   ![Uzyskiwanie sygnatury dostępu współdzielonego dla plików platformy Azure](media/CRMScreenShot11.png)

10. Modyfikowanie **czas wygaśnięcia** Aby uaktywnić sygnatury dostępu Współdzielonego przez jeden miesiąc, a następnie przycisk **Utwórz**.

    ![Modyfikowanie daty wygaśnięcia sygnatury dostępu Współdzielonego dla plików platformy Azure](media/CRMScreenShot12.png)

11. Kopiuj pole adresu URL i zapisz go na później. Należy wprowadzić ten adres URL, podczas tworzenia oferty skojarzone. 

    ![Skopiuj adres URL sygnatury dostępu Współdzielonego plików platformy Azure](media/CRMScreenShot13.png)

