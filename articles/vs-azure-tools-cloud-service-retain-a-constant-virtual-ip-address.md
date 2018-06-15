---
title: Jak utrzymać stałej wirtualnego adresu IP dla usługi w chmurze platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak i upewnij się, że nie zmieniają się wirtualnego adresu IP (VIP) usługi w chmurze Azure.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 4a58e2c6-7a79-4051-8a2c-99182ff8b881
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/21/2017
ms.author: ghogen
ms.openlocfilehash: c02e80a97fe5943019f446299e370bf029a01ff4
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31795096"
---
# <a name="retain-a-constant-virtual-ip-address-for-an-azure-cloud-service"></a>Zachowaj stałej wirtualnego adresu IP dla usługi w chmurze Azure
Po zaktualizowaniu usługi w chmurze, która jest hostowana na platformie Azure, konieczne może być upewnij się, że nie zmieniają się wirtualnego adresu IP (VIP) usługi. Wiele usług zarządzania domeny używa systemu nazw domen (DNS) do rejestracji nazw domen. DNS działa tylko wtedy, gdy adres VIP jest taka sama. Można użyć **Kreator publikowania** w narzędziach Azure, upewnij się, że adres VIP usługi w chmurze nie zmieniają podczas aktualizacji. Aby uzyskać więcej informacji o sposobie używania zarządzania DNS domeny dla usługi w chmurze, zobacz [Konfigurowanie niestandardowej nazwy domeny dla usługi w chmurze Azure](cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="publish-a-cloud-service-without-changing-its-vip"></a>Publikowanie usługi w chmurze bez zmieniania ich adresów VIP
Adres VIP usługi w chmurze jest przydzielony wdrażając najpierw go do platformy Azure w określonym środowisku, na przykład środowiska produkcyjnego. Adres VIP zmienia się tylko wtedy, gdy jawnie Usuń wdrożenie lub wdrożenie jest niejawnie usunięte przez proces wdrażania aktualizacji. Aby zachować adres VIP, nie można usuwać wdrożenia, a użytkownik powinien upewnić się, że program Visual Studio nie powoduje usunięcia wdrożenia automatycznie. 

Można określić ustawienia wdrażania w **Kreator publikowania**, który obsługuje kilka opcji wdrażania. Można określić świeże wdrożenie lub wdrażanie aktualizacji przyrostowych lub jednocześnie. Oba rodzaje wdrożenia aktualizacji zachować adres VIP. Definicje różnego typu wdrożenia, zobacz [Kreator publikowania aplikacji Azure](vs-azure-tools-publish-azure-application-wizard.md). Ponadto można kontrolować, czy poprzedniego wdrożenia usługi w chmurze jest usuwana, jeśli wystąpi błąd. Jeśli ta opcja nie ustawiony prawidłowo, adres VIP może zmienić nieoczekiwanie.

## <a name="update-a-cloud-service-without-changing-its-vip"></a>Aktualizowanie usługi w chmurze bez zmieniania ich adresów VIP
1. Utwórz lub Otwórz projekt usługi w chmurze platformy Azure w programie Visual Studio. 

2. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy projekt. W menu skrótów wybierz **publikowania**.

    ![Menu Publikowanie](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/solution-explorer-publish-menu.png)

3. W **publikowanie aplikacji platformy Azure** oknie dialogowym Wybierz subskrypcję platformy Azure, do której chcesz wdrożyć. Zarejestruj się, jeśli to konieczne i wybierz pozycję **dalej**.

    ![Publikowanie Azure aplikacji stronę logowania](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-signin.png)

4. Na **typowe ustawienia** karcie, upewnij się, że nazwa chmury usługi, na którym jest wdrażany, **środowiska**, **konfiguracji kompilacji**i **Konfiguracji usługi** są wszystkie prawidłowe.

    ![Publikowanie kartę typowe ustawienia aplikacji Azure](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-common-settings.png)

5. Na **Zaawansowane ustawienia** karcie, upewnij się, że **etykieta wdrożenia** i **konta magazynu** są poprawne. Sprawdź, czy **usunąć wdrożenie w przypadku niepowodzenia** pole wyboru jest wyczyszczone, a następnie sprawdź, czy **aktualizację wdrożenia** pole wyboru jest zaznaczone. Po usunięciu zaznaczenia **usunąć wdrożenie w przypadku niepowodzenia** pole wyboru upewnieniu się, że sieci wirtualne adresy IP nie jest utracone, jeśli wystąpi błąd podczas wdrażania. Wybierając **aktualizację wdrożenia** pole wyboru, należy upewnić wdrożenia nie jest usuwany, a Twoje VIP utratę przy ponownym publikowania aplikacji. 

    ![Publikowanie kartę Zaawansowane ustawienia aplikacji Azure](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-advanced-settings.png)

6. Dodatkowo sposób ról do aktualizacji, zaznacz **ustawienia** obok **aktualizację wdrożenia**. Wybierz opcję **aktualizacji przyrostowej** lub **jednoczesne aktualizowanie**i wybierz **OK**. Wybierz **aktualizacji przyrostowej** można zaktualizować każde wystąpienie aplikacji, po kolei, dzięki czemu aplikacja jest zawsze dostępna. Wybierz **jednoczesne aktualizowanie** zaktualizować wszystkich wystąpień aplikacji w tym samym czasie. Jednoczesne aktualizowanie jest szybsze, ale z usługą mogą być niedostępne podczas procesu aktualizacji. Gdy skończysz, wybierz **dalej**.

    ![Publikowanie strony Ustawienia wdrożenia aplikacji Azure](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-deployment-update-settings.png)

7. W **publikowanie aplikacji platformy Azure** okno dialogowe, wybierz opcję **dalej** do momentu **Podsumowanie** zostanie wyświetlona strona. Sprawdź ustawienia, a następnie wybierz **publikowania**.
   
    ![Publikowanie na stronie Podsumowanie aplikacji Azure](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-summary.png)

## <a name="next-steps"></a>Kolejne kroki
- [Za pomocą programu Visual Studio Azure Kreator publikowania aplikacji](vs-azure-tools-publish-azure-application-wizard.md)

