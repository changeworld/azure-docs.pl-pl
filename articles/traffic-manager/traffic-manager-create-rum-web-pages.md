---
title: Rzeczywiste pomiary użytkownika usługi Azure Traffic Manager ze stronami sieci web | Dokumentacja firmy Microsoft
description: Konfigurowanie stron sieci web do wysyłania rzeczywiste pomiary użytkownika do usługi Traffic Manager
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 71661d8640dbe14082292cb9637964adac9fa803
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30180626"
---
# <a name="how-to-send-real-user-measurements-to-azure-traffic-manager-using-web-pages"></a>Jak wysłać rzeczywiste pomiary użytkownika do usługi Azure Traffic Manager przy użyciu stron sieci web

Można skonfigurować stron sieci web do wysłania do usługi Traffic Manager rzeczywiste pomiary użytkownika uzyskania klucza rzeczywiste pomiary użytkownika (RUM) i osadzanie wygenerowany kod do strony sieci web.

## <a name="obtain-a-real-user-measurements-key"></a>Uzyskaj klucz rzeczywiste pomiary użytkownika

Pomiary zająć i wysłać do Menedżera ruchu z aplikacji klienta są identyfikowane przez tę usługę, używając unikatowy ciąg o nazwie **klucza rzeczywiste pomiary użytkownika (RUM)**. Możesz otrzymać klucz rumu przy użyciu portalu Azure, interfejsu API REST, lub za pomocą programu PowerShell lub wiersza polecenia platformy Azure.

Aby uzyskać klucz RUM przy użyciu portalu Azure:
1. W przeglądarce Zaloguj się do portalu Azure. Jeśli nie masz już konto, możesz zarejestrować się bezpłatnej wersji próbnej jeden miesiąc.
2. Na pasku wyszukiwania portalu, wyszukaj nazwę profilu Menedżera ruchu, który chcesz zmodyfikować, a następnie kliknij profil Menedżera ruchu w wynikach który wyświetlone.
3. W bloku profilu usługi Traffic Manager, kliknij **rzeczywiste pomiary użytkownika** w obszarze **ustawienia**.
4. Kliknij przycisk **Wygeneruj klucz** można utworzyć nowego klucza RUM.
 
  ![Generowanie klucza rzeczywiste pomiary użytkownika](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Rysunek 1: Generowanie klucza pomiarów rzeczywistego użytkownika**

5. Blok są obecnie wyświetlane generowania kluczy RUM i fragment kodu JavaScript, który ma być osadzone w kodzie strony HTML.
 
    ![Kod JavaScript dla klucza rzeczywiste pomiary użytkownika](./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png)

    **Rysunek 2: Klucz pomiarów rzeczywistego użytkownika i pomiar JavaScript**
 
6.  Kliknij przycisk **kopiowania** przycisk, aby skopiować kod JavaScript. 

>[!IMPORTANT]
> Użyj wygenerowanego kodu JavaScript dla funkcji rzeczywiste pomiary użytkownika działać prawidłowo. Zmiany wprowadzone w tym skryptu lub skryptów używanych przez rzeczywiste pomiary użytkownika może spowodować nieprzewidywalne zachowanie.

## <a name="embed-the-code-to-an-html-web-page"></a>Osadzanie kodu HTML strony sieci web

Po uzyskaniu klucza rumu, następnym krokiem jest osadzanie tego skopiowanych JavaScript do strony HTML, które użytkownicy końcowi mogą odwiedzać. Edytowanie HTML może zostać wykonane na wiele sposobów i przy użyciu różnych narzędzi i przepływów pracy. W tym przykładzie pokazano, jak zaktualizować stronę HTML, aby dodać ten skrypt. Skorzystać z poniższych wskazówek, aby dostosować ją do przepływu pracy zarządzania źródła HTML.

1.  Otwórz stronę HTML, w edytorze tekstu
2.  Wklej skopiowany w poprzednim kroku sekcji treść HTML kod JavaScript (skopiowanego kodu jest w wierszu 8 & 9, zobacz rysunek 3).
 
    ![Osadzanie kodu Javascript do strony sieci web dla rzeczywiste pomiary użytkownika](./media/traffic-manager-create-rum-web-pages/real-user-measurement-embed-script.png)  

    **Rysunek 3: Proste HTML z osadzonych JavaScript rzeczywiste pomiary użytkownika**

3.  Zapisz plik HTML i hosta na serwerze sieci Web połączony z Internetem. 
4. Następnym razem, gdy ta strona jest renderowany w przeglądarce sieci web, JavaScript, do których odwołuje się jest pobierana i skrypt zostanie wykonany, miary i operacje raportowania.


## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [rzeczywiste pomiary użytkownika](traffic-manager-rum-overview.md)
- Dowiedz się [działania Menedżera ruchu](traffic-manager-overview.md)
- Dowiedz się więcej o [metody routingu ruchu](traffic-manager-routing-methods.md) obsługiwane przez Menedżera ruchu
- Dowiedz się, jak [Tworzenie profilu Menedżera ruchu](traffic-manager-create-profile.md)

