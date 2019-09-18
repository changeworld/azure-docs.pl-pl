---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/09/2018
ms.author: cephalin
ms.openlocfilehash: ddeb46a2c7bc7f24f55c22f446926529cee7b598
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059775"
---
Po rozpropagowaniu rekordów dla nazwy domeny należy mieć możliwość użycia przeglądarki, aby sprawdzić, czy nazwa domeny niestandardowej może być używana do uzyskiwania dostępu do aplikacji sieci Web w Azure App Service.

> [!NOTE]
> Propagacja rekordu CNAME przez system DNS może zająć trochę czasu. Możesz użyć usługi, takiej jak <a href="https://www.digwebinterface.com/">https://www.digwebinterface.com/</a> , aby sprawdzić, czy rekord CNAME jest dostępny.
> 
> 

Jeśli aplikacja sieci Web nie została jeszcze dodana jako punkt końcowy Traffic Manager, musisz to zrobić przed rozpoczęciem rozpoznawania nazw, ponieważ niestandardowa nazwa domeny ma Traffic Manager. Traffic Manager następnie kieruje trasy do aplikacji sieci Web. Użyj informacji w obszarze [punkty końcowe dodawania lub usuwania](../articles/traffic-manager/traffic-manager-endpoints.md) , aby dodać aplikację sieci Web jako punkt końcowy w profilu Traffic Manager.

> [!NOTE]
> Jeśli aplikacja sieci Web nie jest wyświetlana na liście podczas dodawania punktu końcowego, sprawdź, czy jest ona skonfigurowana dla **standardowego** trybu planu App Service. Aby móc korzystać z Traffic Manager, musisz użyć trybu **standardowego** dla aplikacji sieci Web.
> 
> 

1. W przeglądarce Otwórz [Portal Azure](https://portal.azure.com).
2. Na karcie **Web Apps** kliknij nazwę aplikacji sieci Web, wybierz pozycję **Ustawienia**, a następnie wybierz pozycję **domeny niestandardowe** .
   
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)
3. W bloku **domeny niestandardowe** kliknij pozycję **Dodaj nazwę hosta**.
4. Użyj pól tekstowych Nazwa **hosta** , aby wprowadzić niestandardową nazwę domeny, która ma zostać skojarzona z tą aplikacją sieci Web.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-8.png)
5. Kliknij przycisk **Sprawdź poprawność** , aby zapisać konfigurację nazwy domeny.
6. Po kliknięciu pozycji **Sprawdź poprawność** platformy Azure zostanie rozpoczęty przepływ pracy weryfikacji domeny. Spowoduje to sprawdzenie własności domeny, a także dostępności nazwy hosta i powodzenia raportu lub szczegółowego błędu wraz ze wskazówkami opisującymi, jak naprawić błąd.    
7. Po pomyślnym zakończeniu walidacji przycisk **Dodaj nazwę hosta** stanie się aktywny, a będziesz mieć możliwość przypisania nazwy hosta. Teraz przejdź do niestandardowej nazwy domeny w przeglądarce. Twoja aplikacja powinna być teraz uruchomiona przy użyciu niestandardowej nazwy domeny. 
   
   Gdy konfiguracja zostanie ukończona, nazwa domeny niestandardowej zostanie wyświetlona w sekcji **nazwy domen** w aplikacji sieci Web.

W tym momencie należy mieć możliwość wprowadzenia nazwy domeny Traffic Manager w przeglądarce i sprawdzenia, czy pomyślnie nastąpi przejście do aplikacji sieci Web.

