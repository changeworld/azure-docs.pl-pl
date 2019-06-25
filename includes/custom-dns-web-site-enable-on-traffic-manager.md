---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/09/2018
ms.author: cephalin
ms.openlocfilehash: 67b9c0ba2566206b0e70db51844b21e5d5d3c261
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183808"
---
Po wykonaniu propagacji rekordów dla nazwy domeny, należy użyć przeglądarki, aby sprawdzić, czy nazwa domeny niestandardowej może służyć do dostępu do aplikacji sieci web w usłudze Azure App Service.

> [!NOTE]
> Upłynąć trochę czasu, Twoje CNAME Propagacja za pośrednictwem systemu DNS. Można użyć usługi, takie jak <a href="https://www.digwebinterface.com/"> https://www.digwebinterface.com/ </a> Aby sprawdzić, czy rekord CNAME jest dostępny.
> 
> 

Jeśli Twoja aplikacja sieci web nie jest już dodano jako punktu końcowego usługi Traffic Manager, należy to zrobić, aby rozpoznawanie nazw działa jako trasy nazwy domeny niestandardowej do usługi Traffic Manager. Usługa Traffic Manager, a następnie trasy do aplikacji sieci web. Skorzystaj z informacji w [apletu Dodaj lub Usuń punkty końcowe](../articles/traffic-manager/traffic-manager-endpoints.md) do dodania aplikacji sieci web jako punktu końcowego w profilu usługi Traffic Manager.

> [!NOTE]
> Jeśli Twoja aplikacja sieci web nie jest wyświetlana podczas dodawania punktu końcowego, sprawdź, czy została ona skonfigurowana do **standardowa** tryb planu usługi App Service. Należy użyć **standardowa** tryb dla aplikacji sieci web w taki sposób, aby móc pracować z usługą Traffic Manager.
> 
> 

1. Otwórz w przeglądarce, [witryny Azure Portal](https://portal.azure.com).
2. W **aplikacji sieci Web** kliknij nazwę aplikacji sieci web, wybierz pozycję **ustawienia**, a następnie wybierz pozycję **domen niestandardowych**
   
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)
3. W **domen niestandardowych** bloku kliknij **Dodaj nazwę hosta**.
4. Użyj **Hostname** pola tekstowe, aby wprowadzić nazwę domeny usługi Traffic Manager do skojarzenia z tą aplikacją sieci web.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-8.png)
5. Kliknij przycisk **weryfikacji** można zapisać konfiguracji nazwę domeny.
6. Po kliknięciu **weryfikacji** Azure będzie uruchamiał przepływu pracy weryfikacji domeny. Spowoduje to zaewidencjonowanie własność domeny, a także Powodzenie raport i dostępność nazwy hosta lub szczegółowy komunikat o błędzie z wskazówki na temat naprawić błąd.    
7. Po pomyślnej weryfikacji **Dodaj nazwę hosta** przycisk stanie się aktywny i będzie można przypisać nazwy hosta. Teraz przejdź do swojej niestandardowej nazwy domeny w przeglądarce. Powinien zostać wyświetlony działającego aplikacji przy użyciu niestandardowej nazwy domeny. 
   
   Po zakończeniu konfiguracji niestandardowej nazwy domeny zostaną wyświetlone w **nazw domen** części aplikacji sieci web.

W tym momencie można wprowadzić nazwę domeny usługi Traffic Manager w przeglądarce i zobacz, że jej pomyślnie spowoduje przejście do aplikacji sieci web.

