---
title: Pomiarów rzeczywistego użytkownika usługi Azure Traffic Manager ze stronami sieci web | Dokumentacja firmy Microsoft
description: Konfigurowanie stron sieci web, aby wysyłać pomiary dotyczące prawdziwych użytkowników do usługi Traffic Manager
services: traffic-manager
documentationcenter: traffic-manager
author: asudbring
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: 2d044457df80f16a6e8073e7f3253a611f74d8a8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071217"
---
# <a name="how-to-send-real-user-measurements-to-azure-traffic-manager-using-web-pages"></a>Jak wysyłać pomiary dotyczące prawdziwych użytkowników do usługi Azure Traffic Manager za pomocą stron sieci web

Można skonfigurować stron sieci web w taki sposób, aby wysyłanie pomiarów rzeczywistego użytkownika do usługi Traffic Manager, uzyskując klucza rzeczywistego użytkownika pomiarów (RUM) i osadzanie wygenerowanego kodu do strony sieci web.

## <a name="obtain-a-real-user-measurements-key"></a>Uzyskaj klucz pomiarów rzeczywistego użytkownika

Pomiary zająć i wysyłanie do usługi Traffic Manager z aplikacji klienckiej są identyfikowane przez usługę za pomocą unikatowy ciąg o nazwie **klucza rzeczywistego użytkownika pomiarów (RUM)** . Można uzyskać klucz uruchamianie przy użyciu witryny Azure portal, interfejsu API REST lub przy użyciu programu PowerShell lub wiersza polecenia platformy Azure.

Aby uzyskać klucz RUM przy użyciu witryny Azure portal:
1. Z poziomu przeglądarki Zaloguj się do witryny Azure portal. Jeśli nie masz jeszcze konta, możesz zarejestrować się w bezpłatnej wersji próbnej jednego miesiąca.
2. Na pasku wyszukiwania portalu Wyszukaj nazwę profilu usługi Traffic Manager, który chcesz zmodyfikować, a następnie kliknij profil usługi Traffic Manager w taki sposób, w wynikach, wyświetlana.
3. W bloku profilu usługi Traffic Manager, kliknij **pomiarów rzeczywistego użytkownika** w obszarze **ustawienia**.
4. Kliknij przycisk **Wygeneruj klucz** można utworzyć nowego klucza (rum).
 
   ![Wygeneruj klucz pomiarów rzeczywistego użytkownika](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Rysunek 1: Generowanie klucza pomiarów rzeczywistego użytkownika**

5. W tym bloku teraz wyświetlane generowania kluczy RUM i wstawki kodu JavaScript, który ma być osadzone w kodzie strony HTML.
 
    ![Kod JavaScript dla klucza pomiarów rzeczywistego użytkownika](./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png)

    **Rysunek 2: Klucz pomiarów rzeczywistego użytkownika oraz kod JavaScript pomiaru**
 
6. Kliknij przycisk **kopiowania** przycisk, aby skopiować kod JavaScript. 

>[!IMPORTANT]
> Użyj wygenerowanego kodu JavaScript dla funkcji pomiarów rzeczywistego użytkownika, aby działać prawidłowo. Wszelkie zmiany do tego skryptu lub skrypty używane przez pomiarów rzeczywistego użytkownika może spowodować nieprzewidywalne zachowanie.

## <a name="embed-the-code-to-an-html-web-page"></a>Osadzanie kodu do strony internetowej HTML

Po uzyskaniu klucza uruchamianie, następnym krokiem jest osadzanie tego skopiowany JavaScript do strony HTML, które użytkownicy końcowi odwiedzać. Edytowanie kodu HTML może odbywać się na wiele sposobów i przy użyciu różnych narzędzi i przepływów pracy. Ten przykład przedstawia sposób aktualizacji na stronie HTML, aby dodać ten skrypt. Te wskazówki można użyć w celu dostosowania go do przepływu pracy zarządzania źródła HTML.

1.  Otwórz stronę HTML w edytorze tekstu
2.  Wklej kod JavaScript skopiowany w poprzednim kroku części treści pliku HTML (skopiowany kod jest w wierszu 8 i 9, zobacz rysunek 3).
 
    ![Osadzanie kodu Javascript do strony sieci web dla pomiarów rzeczywistego użytkownika](./media/traffic-manager-create-rum-web-pages/real-user-measurement-embed-script.png)  

    **Rysunek 3: Prosty kod HTML z osadzonych rzeczywistych JavaScript pomiary dotyczące prawdziwych użytkowników**

3.  Zapisz plik HTML i hosta go na serwerze sieci Web połączone z Internetem. 
4. Następnym razem, gdy ta strona jest renderowany w przeglądarce sieci web, JavaScript, do których odwołuje się zostanie pobrany i skrypt będzie wykonywać pomiaru i operacje raportowania.


## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [pomiarów rzeczywistego użytkownika](traffic-manager-rum-overview.md)
- Dowiedz się, [jak działa usługa Traffic Manager](traffic-manager-overview.md)
- Dowiedz się więcej o [metody routingu ruchu](traffic-manager-routing-methods.md) obsługiwane przez usługę Traffic Manager
- Dowiedz się, jak [Tworzenie profilu usługi Traffic Manager](traffic-manager-create-profile.md)

