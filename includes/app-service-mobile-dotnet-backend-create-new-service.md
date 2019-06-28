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
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2019
ms.locfileid: "67325799"
---
1. Zaloguj się w witrynie [Azure Portal].

2. Kliknij pozycję **Utwórz zasób**.

3. W polu wyszukiwania wpisz **aplikacji sieci Web**.
    
4. Na liście wyników wybierz **aplikacji sieci Web** z witryny Marketplace.

5. Wybierz swoje **subskrypcji** i **grupy zasobów** (Wybierz istniejącą grupę zasobów _lub_ Utwórz nową (używając tej samej nazwie co aplikacja)).

6. Wybierz unikatową **nazwa** aplikacji sieci web.

7. Wybierz domyślne **Publikuj** opcję **kodu**.

8. W **stosu środowiska uruchomieniowego**, musisz wybrać wersję, w obszarze **ASP.NET** lub **węzła**. Jeśli tworzysz z zaplecza platformy .NET, należy wybrać wersję w ramach programu ASP.NET. W przeciwnym razie jeśli są przeznaczone dla aplikacji na podstawie węzła, wybierz jedną z wersji z węzła.

9. Wybierz po prawej stronie **systemu operacyjnego**, Linux lub Windows. 

10. Wybierz **Region** gdzie chcesz tej aplikacji do wdrożenia. 

11. Wybierz odpowiedni **planu usługi App Service** i kliknij przycisk **przeglądu i utworzyć**. 

12. W obszarze **Grupa zasobów** wybierz istniejącą grupę zasobów _lub_ utwórz nową (używając tej samej nazwy, co aplikacja).

13. Kliknij pozycję **Utwórz**. Zanim przejdziesz do kolejnych kroków, odczekaj kilka minut, aby usługa została wdrożona pomyślnie. Obserwuj ikonę powiadomień (dzwonek) w nagłówku portalu, pokazującą aktualizacje stanu.

14. Po zakończeniu wdrożenia kliknij **szczegóły wdrożenia** sekcji, a następnie kliknij polecenie zasobu typu **Microsoft.Web/sites**. Jej spowoduje przejście do aplikacji sieci Web usługi aplikacji, który został utworzony. 

15. Kliknij pozycję **konfiguracji** bloku w obszarze **ustawienia** i **ustawienia aplikacji**, kliknij pozycję **nowe ustawienie aplikacji** przycisk.

16. W **ustawienia aplikacji Dodaj/Edytuj** wpisz **nazwa** jako **MobileAppsManagement_EXTENSION_VERSION** i wartości jako **najnowsze** i kliknę przycisk OK.

Wszystko jest ustawione do użycia na tę nowo utworzoną aplikację sieci Web usługi aplikacji jako aplikacji mobilnej.

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/