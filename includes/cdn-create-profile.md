---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cdn
author: dksimpson
ms.service: cdn
ms.topic: include
ms.date: 04/04/2018
ms.author: rli; v-deasim
ms.custom: include file
ms.openlocfilehash: 692364e9b2e78b3bd1f63137148dfbc680364737
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
## <a name="create-a-new-cdn-profile"></a>Tworzenie nowego profilu CDN

Profil CDN jest kontenerem dla punktów końcowych usługi CDN i określa warstwę cenową.

1. W portalu Azure, w lewym górnym rogu wybierz **Utwórz zasób**.
    
    **Nowy** pojawi się okienko.
   
2. Wybierz **sieci Web i mobilność**, następnie **CDN**.
   
    ![Wybierz zasób usługi CDN](./media/cdn-create-profile/cdn-new-resource.png)

    **Profilu CDN** pojawi się okienko.

    Użyj ustawień określonych w tabeli po obrazu.
   
    ![Nowy profil CDN](./media/cdn-create-profile/cdn-new-profile.png)

    | Ustawienie  | Wartość |
    | -------- | ----- |
    | **Nazwa** | Wprowadź *Moje cdn profilu-123* nazwy profilu. Ta nazwa musi być globalnie unikatowe; Jeśli jest już używana, można wprowadzić inny. |
    | **Subskrypcja** | Wybierz subskrypcję platformy Azure z listy rozwijanej.|
    | **Grupa zasobów** | Wybierz **Utwórz nowy** , a następnie wprowadź *Moje zasobów grupy-123* nazwy grupy zasobów. Ta nazwa musi być globalnie unikatowe; Jeśli jest już używana, można wprowadzić inny. | 
    | **Lokalizacja grupy zasobów** | Wybierz **środkowe stany USA** z listy rozwijanej. |
    | **Warstwa cenowa** | Wybierz **standardowe Verizon** z listy rozwijanej. |
    | **Teraz utworzyć nowy punkt końcowy CDN** | Nie zaznaczaj. |  
   
3. Wybierz **Przypnij do pulpitu nawigacyjnego** do zapisania profilu do pulpitu nawigacyjnego po jego utworzeniu.
    
4. Wybierz **Utwórz** do tworzenia profilu. 

