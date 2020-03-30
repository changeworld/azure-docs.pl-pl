---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 06/20/2019
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: 72a69359d412a7560472fbb73ec525ab5d4a4fce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67325799"
---
1. Zaloguj się do [Portalu Azure].

2. Kliknij pozycję **Utwórz zasób**.

3. W polu wyszukiwania wpisz polecenie **Aplikacja Sieci Web**.
    
4. Na liście wyników wybierz aplikację **sieci Web** z portalu Marketplace.

5. Wybierz **subskrypcję** i **grupę zasobów** (wybierz istniejącą grupę zasobów _lub_ utwórz nową (o tej samej nazwie co aplikacja)).

6. Wybierz unikatową **nazwę** aplikacji internetowej.

7. Wybierz domyślną opcję **Publikuj** jako **Kod**.

8. W **stosie Runtime**należy wybrać wersję w obszarze **ASP.NET** lub **Node**. Jeśli budujesz zaplecze platformy .NET, wybierz wersję w obszarze ASP.NET. W przeciwnym razie, jeśli są przeznaczone dla aplikacji opartej na węźle, wybierz jedną z wersji z węzła.

9. Wybierz odpowiedni **system operacyjny**, Linux lub Windows. 

10. Wybierz **region, w** którym chcesz wdrożyć tę aplikację. 

11. Wybierz odpowiedni **plan usługi aplikacji** i naciśnij przycisk **Przejrzyj i utwórz**. 

12. W obszarze **Grupa zasobów** wybierz istniejącą grupę zasobów _lub_ utwórz nową (używając tej samej nazwy, co aplikacja).

13. Kliknij przycisk **Utwórz**. Zanim przejdziesz do kolejnych kroków, odczekaj kilka minut, aby usługa została wdrożona pomyślnie. Obserwuj ikonę powiadomień (dzwonek) w nagłówku portalu, pokazującą aktualizacje stanu.

14. Po zakończeniu wdrażania kliknij sekcję **Szczegóły wdrożenia,** a następnie kliknij zasób typu **Microsoft.web/sites**. Spowoduje to przejście do aplikacji sieci Web usługi aplikacji, która została właśnie utworzona. 

15. Kliknij na ostrze **Konfiguracja** w obszarze **Ustawienia** i w **ustawieniach aplikacji**kliknij przycisk **Nowe ustawienie aplikacji.**

16. Na stronie **ustawień Dodaj/Edytuj aplikację** wprowadź **pozycję Nazwa** jako **MobileAppsManagement_EXTENSION_VERSION** i Wartość jako **najnowsza** i naciśnij przycisk OK.

Wszystko jest ustawione do korzystania z tej nowo utworzonej aplikacji sieci Web usługi app service jako aplikacji mobilnej.

<!-- URLs. -->
[Portal Azure]: https://portal.azure.com/