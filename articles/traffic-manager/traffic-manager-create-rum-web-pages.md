---
title: Pomiary rzeczywistych użytkowników za pomocą stron internetowych — Usługa Azure Traffic Manager
description: W tym artykule dowiesz się, jak skonfigurować strony sieci Web do wysyłania pomiarów rzeczywistych użytkowników do usługi Azure Traffic Manager.
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: rohink
ms.custom: ''
ms.openlocfilehash: 927d774ee30a291607a8a47fc2fd6878c1bc6fee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938699"
---
# <a name="how-to-send-real-user-measurements-to-azure-traffic-manager-using-web-pages"></a>Jak wysyłać pomiary rzeczywistych użytkowników do usługi Azure Traffic Manager przy użyciu stron internetowych

Możesz skonfigurować strony internetowe tak, aby wysyłać pomiary rzeczywistych użytkowników do Menedżera ruchu, uzyskując klucz pomiarów rzeczywistego użytkownika (RUM) i osadząc wygenerowany kod na stronie internetowej.

## <a name="obtain-a-real-user-measurements-key"></a>Uzyskiwanie klucza pomiarów rzeczywistego użytkownika

Pomiary, które należy podjąć i wysłać do Usługi Traffic Manager z aplikacji klienckiej, są identyfikowane przez usługę przy użyciu unikatowego ciągu, zwanego **kluczem pomiarów rzeczywistego użytkownika (RUM).** Klucz RUM można uzyskać przy użyciu witryny Azure portal, interfejsu API REST lub przy użyciu interfejsu wiersza polecenia programu PowerShell lub platformy Azure.

Aby uzyskać klucz RUM przy użyciu witryny Azure portal:
1. Z poziomu przeglądarki zaloguj się do witryny Azure Portal. Jeśli jeszcze nie masz konta, możesz skorzystać z bezpłatnej miesięcznej wersji próbnej.
2. Korzystając z paska wyszukiwania portalu, wyszukaj nazwę profilu usługi Traffic Manager, który chcesz zmodyfikować, a następnie kliknij profil usługi Traffic Manager w wyświetlonych wynikach wyszukiwania.
3. W bloku profilu usługi Traffic Manager kliknij pozycję **Pomiary rzeczywistego użytkownika** w obszarze **Ustawienia**.
4. Kliknij **przycisk Generuj klucz,** aby utworzyć nowy klucz RUM.
 
   ![Klucz Generowanie pomiarów rzeczywistego użytkownika](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Rysunek 1: Generowanie klucza pomiarów rzeczywistych użytkowników**

5. Blok wyświetla teraz wygenerowany klucz RUM i fragment kodu JavaScript, który musi zostać osadzony na stronie HTML.
 
    ![Kod JavaScript dla klucza pomiarów rzeczywistych użytkowników](./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png)

    **Rysunek 2: Klucz pomiarów rzeczywistych użytkowników i pomiar JavaScript**
 
6. Kliknij przycisk **Kopiuj,** aby skopiować kod JavaScript. 

>[!IMPORTANT]
> Użyj wygenerowanej funkcji JavaScript dla pomiarów rzeczywistych użytkowników, aby działać poprawnie. Wszelkie zmiany w tym skrypcie lub skrypty używane przez pomiary rzeczywistego użytkownika może prowadzić do nieprzewidywalnego zachowania.

## <a name="embed-the-code-to-an-html-web-page"></a>Osadzanie kodu na stronie internetowej HTML

Po uzyskaniu klucza RUM następnym krokiem jest osadzenie tego skopiowanego języka JavaScript na stronie HTML odwiedzanej przez użytkowników końcowych. Edytowanie kodu HTML można wykonać na wiele sposobów i przy użyciu różnych narzędzi i przepływów pracy. W tym przykładzie pokazano, jak zaktualizować stronę HTML, aby dodać ten skrypt. Za pomocą tych wskazówek można dostosować go do przepływu pracy zarządzania źródłami HTML.

1.  Otwieranie strony HTML w edytorze tekstu
2.  Wklej kod JavaScript skopiowany we wcześniejszym kroku do sekcji BODY w HTML (skopiowany kod znajduje się w wierszu 8 & 9, patrz rysunek 3).
 
    ![Osadzanie kodu JavaScript na stronie internetowej w celu pomiarów rzeczywistych użytkowników](./media/traffic-manager-create-rum-web-pages/real-user-measurement-embed-script.png)  

    **Rysunek 3: Prosty HTML z osadzonymi pomiarami realnym użytkownika JavaScript**

3.  Zapisz plik HTML i hostuj go na serwerze internetowym podłączonym do Internetu. 
4. Następnym razem, gdy ta strona zostanie renderowana w przeglądarce internetowej, javascript, do którego istnieje odwołanie, zostanie pobrany, a skrypt wykona operacje pomiaru i raportowania.


## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [pomiarach rzeczywistych użytkowników](traffic-manager-rum-overview.md)
- Dowiedz [się, jak działa Usługa Traffic Manager](traffic-manager-overview.md)
- Dowiedz się więcej o [metodach routingu ruchu](traffic-manager-routing-methods.md) obsługiwanych przez usługę Traffic Manager
- Dowiedz się, jak [utworzyć profil usługi Traffic Manager](traffic-manager-create-profile.md)

