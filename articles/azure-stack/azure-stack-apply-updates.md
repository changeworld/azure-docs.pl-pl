---
title: Stosowanie aktualizacji w stosie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zaimportować i zainstaluj pakiety aktualizacji firmy Microsoft dla systemu Azure stosu zintegrowany.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: mabrigg
ms.openlocfilehash: 0f23216c6aced60dc651a0f10179281bc9a29c2c
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2018
ms.locfileid: "29802655"
---
# <a name="apply-updates-in-azure-stack"></a>Stosowanie aktualizacji w stosie Azure

*Dotyczy: Azure stosu zintegrowane systemy*

Jako operator stosu Azure można stosować przez Kafelek pakietów na stos Azure za pomocą aktualizacji w portalu administratora usługi Microsoft update. Musi pobrać pakiet aktualizacji programu Microsoft, importowanie plików pakietu do stosu usługi Azure i następnie zainstalować pakiet aktualizacji. 

## <a name="download-the-update-package"></a>Pobierz pakiet aktualizacji programu

Jeśli pakiet aktualizacji programu Microsoft Azure stosu jest dostępny, Pobierz pakiet do lokalizacji, która jest dostępny ze stosu Azure, a następnie przejrzyj zawartość pakietu. Pakiet aktualizacji obejmuje zazwyczaj następujące pliki:

- Samowyodrębniający *PackageName*pliku .exe. Ten plik zawiera ładunek dla tej aktualizacji, na przykład najnowszej aktualizacji zbiorczej dla systemu Windows Server.   
- Odpowiadającego *PackageName*bin plików. Pliki te zapewniają kompresja ładunku, z którym skojarzony jest *PackageName*pliku .exe. 
- Pliku Metadata.xml. Ten plik zawiera ważne informacje o aktualizacji, na przykład wydawcy, nazwa, wymagań wstępnych, rozmiar i adres URL pomocy technicznej ścieżki.

## <a name="import-and-install-updates"></a>Importowanie i zainstaluj aktualizacje

Poniższa procedura przedstawia sposób importowania i instalowania pakietów aktualizacji w portalu administratora.

> [!IMPORTANT]
> Zalecamy powiadomienie użytkowników wszystkie operacje obsługi, i Zaplanuj konserwacji systemu windows podczas poza godzinami pracy możliwie. Operacje konserwacji może mieć wpływ na zarówno obciążeń użytkownika, jak i działania portalu.

1. W portalu administratora, wybierz **więcej usług**. Następnie w obszarze **dane i magazyn** kategorii, wybierz opcję **kont magazynu**. (Lub, w polu filtru zacznij pisać **kont magazynu**i zaznacz go.)

    ![Wskazuje, gdzie można znaleźć konta magazynu w portalu](media/azure-stack-apply-updates/ApplyUpdates1.png)

2. W polu filtru wpisz **aktualizacji**i wybierz **updateadminaccount** konta magazynu.

    ![Przedstawiono sposób wyszukiwania updateadminaccount](media/azure-stack-apply-updates/ApplyUpdates2.png)

3. W magazynie konta szczegółów, w obszarze **usług**, wybierz pozycję **obiekty BLOB**.
 
    ![Pokazuje, jak uzyskać dostęp do obiektów blob dla konta magazynu](media/azure-stack-apply-updates/ApplyUpdates3.png) 
 
4. W obszarze **usługa Blob**, wybierz pozycję **+ kontener** do utworzenia kontenera. Wprowadź nazwę (na przykład *1709 aktualizacji*), a następnie wybierz **OK**.
 
     ![Pokazuje, jak dodać kontener na koncie magazynu](media/azure-stack-apply-updates/ApplyUpdates4.png)

5. Po utworzeniu kontenera, kliknij nazwę kontenera, a następnie kliknij przycisk **przekazać** przekazywania plików pakietu do kontenera.
 
    ![Pokazuje sposób przekazywania plików pakietu](media/azure-stack-apply-updates/ApplyUpdates5.png)

6. W obszarze **przekazywanie obiektu blob**, kliknij ikonę folderu, wskaż lokalizację pliku .exe pakiet aktualizacji, a następnie kliknij **Otwórz** w oknie Eksploratora plików.
  
7. W obszarze **przekazywanie obiektu blob**, kliknij przycisk **przekazać**. 
 
    ![Wskazuje, gdzie można przekazać pliku każdego pakietu](media/azure-stack-apply-updates/ApplyUpdates6.png)

8. Powtórz kroki 6 i 7 dla *PackageName*bin i pliki Metadata.xml. Nie należy importować plik Notice.txt uzupełniające, jeśli uwzględniona.
9. Na koniec można przejrzeć powiadomienia (ikonę dzwonka w prawym górnym rogu portalu). Powiadomienia powinny wskazywać, że przekazywania została ukończona. 
10. Przejdź z powrotem do aktualizacji kafelka na pulpicie nawigacyjnym. Kafelek powinny wskazywać, że dostępna jest aktualizacja. Kliknij Kafelek, aby przejrzeć pakietu aktualizacji nowo dodany.
11. Aby zainstalować tę aktualizację, wybierz pakiet, który jest oznaczony jako **gotowe** i albo kliknij prawym przyciskiem myszy pakiet i wybierz **Aktualizuj**, lub kliknij przycisk **Aktualizuj** akcji u góry .
12. Po kliknięciu instalowania pakietu aktualizacji, można wyświetlić stan w **szczegóły uruchomienia aktualizacji** obszaru. W tym miejscu możesz także kliknąć **pobrać dzienniki pełnej** do pobierania plików dziennika.
13. Po ukończeniu aktualizacji kafelka aktualizacji zawiera zaktualizowaną wersję stosu Azure.

## <a name="next-steps"></a>Kolejne kroki

- [Zarządzanie aktualizacjami w stosie Azure — omówienie](azure-stack-updates.md)
- [Azure stos obsługi zasad](azure-stack-servicing-policy.md)
