---
title: Przechowywanie pakietu usługi AppSource w magazynie platformy Azure i generowanie adresu URL za pomocą klucza Sygnatury dostępu Współdzielonego
description: Szczegółowe informacje o krokach wymaganych do przekazania i zabezpieczenia pakietu AppSource.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: dsindona
ms.openlocfilehash: 57bc370fd160b8b3d6d7941ea28cd460c99f3d96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285371"
---
<a name="store-your-appsource-package-to-azure-storage-and-generate-a-url-with-sas-key"></a>Przechowywanie pakietu usługi AppSource w magazynie platformy Azure i generowanie adresu URL za pomocą klucza Sygnatury dostępu Współdzielonego
=============================================================================

Aby zachować bezpieczeństwo plików, wszyscy partnerzy muszą przechowywać plik pakietu usługi AppSource na koncie magazynu obiektów blob platformy Azure i używać klucza Sygnatury dostępu Współdzielonego do jego udostępniania. Pobierzemy plik pakietu z lokalizacji magazynu platformy Azure do certyfikacji i użyjemy go do wersji próbnych usługi AppSource.

Aby przekazać pakiet do magazynu obiektów blob, należy wykonać następujące czynności:

1. Przejdź <https://azure.microsoft.com> do bezpłatnego konta próbnego lub konta rozliczeniowego i utwórz je za darmo.

2. Zaloguj się do [witryny Azure Portal](https://portal.azure.com/).

3. Utwórz nowe konto magazynu, klikając **+ Nowy** i przechodząc do konta **Dane + Magazyn.**

   ![Data + magazyn w witrynie Microsoft Azure Portal](media/CRMScreenShot7.png)

4. Wprowadź **nazwę nazwy** i nazwę **grupy zasobów,** a następnie kliknij przycisk **Utwórz.**

   ![Tworzenie konta magazynu w witrynie Microsoft Azure Portal](media/CRMScreenShot8.png)

5. Przejdź do nowo utworzonej grupy zasobów i utwórz nowy kontener obiektów blob.

   ![Przekazywanie pakietu jako obiektu blob przy użyciu witryny Microsoft Azure Portal](media/CRMScreenShot9.png)

6. Jeśli jeszcze tego nie zrobiono, pobierz i zainstaluj [Eksploratora magazynu platformy](https://storageexplorer.com/)Microsoft Azure .

7. Otwórz Eksploratora magazynu i użyj tej ikony, aby połączyć się z kontem magazynu platformy Azure.

8. Przejdź do utworzonego kontenera obiektów blob i kliknij przycisk **Przekaż,** aby dodać plik zip pakietu.

   ![Przekazywanie pakietu przy użyciu Eksploratora magazynu firmy Microsoft](media/CRMScreenShot10.png)

9. Kliknij prawym przyciskiem myszy plik i wybierz pozycję **Pobierz podpis dostępu współdzielonego**.

   ![Uzyskiwanie podpisu dostępu współdzielonego pliku platformy Azure](media/CRMScreenShot11.png)

10. Zmodyfikuj **czas wygaśnięcia,** aby sygnatura dostępu Współdzielonego była aktywna przez miesiąc, a następnie kliknij przycisk **Utwórz**.

    ![Modyfikowanie daty wygaśnięcia sygnatury dostępu Współdzielonego pliku platformy Azure](media/CRMScreenShot12.png)

11. Skopiuj pole adresu URL i zapisz go na później. Podczas tworzenia skojarzonej oferty należy wprowadzić ten adres URL. 

    ![Kopiowanie adresu URL sygnatury dostępu Współdzielonego pliku platformy Azure](media/CRMScreenShot13.png)

