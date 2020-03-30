---
title: Usługa Azure AD Connect inicjująca inicjowanie obsługi administracyjnej nowej konfiguracji agenta
description: W tym artykule opisano sposób instalowania inicjowania obsługi administracyjnej w chmurze.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c086227d23901cb7904d8cc0a768e9f4b5ad43db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77620974"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-based-provisioning"></a>Tworzenie nowej konfiguracji dla obsługi administracyjnej opartej na chmurze usługi Azure AD Connect

Po zainstalowaniu agenta należy zalogować się do witryny Azure portal i skonfigurować inicjowanie obsługi administracyjnej chmury usługi Azure Active Directory (Azure AD) Connect. Wykonaj następujące kroki, aby włączyć agenta.

## <a name="configure-provisioning"></a>Konfigurowanie inicjowania obsługi administracyjnej
Aby skonfigurować inicjowanie obsługi administracyjnej, wykonaj następujące kroki.

1.  W witrynie Azure Portal wybierz pozycję **Azure Active Directory**.
1.  Wybierz **usługę Azure AD Connect**.
1.  Wybierz **pozycję Zarządzaj inicjowania obsługi administracyjnej (Wersja zapoznawcza)**.

    ![Zarządzanie inicjowania obsługi administracyjnej (wersja zapoznawcza)](media/how-to-configure/manage1.png)

1.  Wybierz **pozycję Nowa konfiguracja**.
1.  Na ekranie konfiguracji domena lokalna jest wstępnie wypełniona.
1.  Wprowadź **wiadomość e-mail z powiadomieniem**. Ten e-mail zostanie powiadomiony, gdy inicjowanie obsługi administracyjnej nie jest w dobrej kondycji.
1.  Przesuń selektor do **enable**i wybierz pozycję **Zapisz**.

    ![Inicjowanie obsługi administracyjnej usługi Azure AD (wersja zapoznawcza)](media/tutorial-single-forest/configure2.png)

## <a name="scope-provisioning-to-specific-users-and-groups"></a>Inicjowanie obsługi administracyjnej zakresu określonym użytkownikom i grupom
Można wyzwać agenta, aby zsynchronizować określonych użytkowników i grupy przy użyciu lokalnych grup usługi Active Directory lub jednostek organizacyjnych. Nie można skonfigurować grup i jednostek organizacyjnych w ramach konfiguracji. 

1.  W witrynie Azure Portal wybierz pozycję **Azure Active Directory**.
1.  Wybierz **usługę Azure AD Connect**.
1.  Wybierz **pozycję Zarządzaj inicjowania obsługi administracyjnej (Wersja zapoznawcza)**.
1.  W obszarze **Konfiguracja**wybierz konfigurację.

    ![Sekcja Konfiguracji](media/how-to-configure/scope1.png)

1.  W obszarze **Konfiguruj**wybierz pozycję **Wszyscy użytkownicy,** aby zmienić zakres reguły konfiguracji.

    ![Opcja Wszyscy użytkownicy](media/how-to-configure/scope2.png)

1. Po prawej stronie można zmienić zakres, aby uwzględnić tylko grupy zabezpieczeń. Wprowadź nazwę wyróżniającą grupy i wybierz pozycję **Dodaj**.

    ![Wybrana opcja grup zabezpieczeń](media/how-to-configure/scope3.png)

1.  Można też zmienić zakres, aby uwzględnić tylko określone jednostki organizacyjne. Wybierz **pozycję Gotowe** i **zapisz**.  
2.  Po zmianie zakresu należy [ponownie uruchomić inicjowanie obsługi administracyjnej,](#restart-provisioning) aby zainicjować natychmiastową synchronizację zmian.

    ![Wybrana opcja jednostek organizacyjnych](media/how-to-configure/scope4.png)


## <a name="restart-provisioning"></a>Ponowne uruchamianie obsługi administracyjnej 
Jeśli nie chcesz czekać na następne zaplanowane uruchomienie, wyzwolić inicjowanie obsługi administracyjnej uruchomione przy użyciu przycisku **inicjowania obsługi administracyjnej ponownego uruchamiania.** 
1.  W witrynie Azure Portal wybierz pozycję **Azure Active Directory**.
1.  Wybierz **usługę Azure AD Connect**.
1.  Wybierz **pozycję Zarządzaj inicjowania obsługi administracyjnej (Wersja zapoznawcza)**.
1.  W obszarze **Konfiguracja**wybierz konfigurację.

    ![Wybór konfiguracji w celu ponownego uruchomienia inicjowania obsługi administracyjnej](media/how-to-configure/scope1.png)

1.  U góry wybierz pozycję **Uruchom ponownie inicjowanie obsługi administracyjnej**.

## <a name="remove-a-configuration"></a>Usuwanie konfiguracji
Aby usunąć konfigurację, wykonaj następujące kroki.

1.  W witrynie Azure Portal wybierz pozycję **Azure Active Directory**.
1.  Wybierz **usługę Azure AD Connect**.
1.  Wybierz **pozycję Zarządzaj inicjowania obsługi administracyjnej (Wersja zapoznawcza)**.
1.  W obszarze **Konfiguracja**wybierz konfigurację.

    ![Wybór konfiguracji w celu usunięcia konfiguracji](media/how-to-configure/scope1.png)

1.  U góry ekranu konfiguracji wybierz pozycję **Usuń**.

    ![Przycisk Usuń](media/how-to-configure/remove1.png)

>[!IMPORTANT]
>Nie ma potwierdzenia przed usunięciem konfiguracji. Upewnij się, że jest to akcja, którą chcesz wykonać przed **wybraniem**opcji Usuń .


## <a name="next-steps"></a>Następne kroki 

- [Co to jest aprowizacja?](what-is-provisioning.md)
- [Co to jest aprowizacja w chmurze programu Azure AD Connect?](what-is-cloud-provisioning.md)
