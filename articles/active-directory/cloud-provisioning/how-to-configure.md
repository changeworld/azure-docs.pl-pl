---
title: Azure AD Connect nową konfigurację agenta aprowizacji w chmurze
description: W tym temacie opisano sposób instalowania aprowizacji w chmurze.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 956962503dcb3a6355d455ff96744143c5965d1e
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794304"
---
# <a name="azure-ad-connect-cloud-provisioning-new-configuration"></a>Azure AD Connect nową konfigurację aprowizacji w chmurze

Po zainstalowaniu agenta należy zalogować się do Azure Portal i skonfigurować aprowizacji.  Aby włączyć agenta, wykonaj następujące czynności.

## <a name="configure-provisioning"></a>Konfigurowanie aprowizacji
Aby skonfigurować Inicjowanie obsługi, wykonaj następujące czynności:

1.  W portalu usługi Azure AD kliknij pozycję **Azure Active Directory**
2.  Kliknij **Azure AD Connect**
3.  Wybierz pozycję **Zarządzaj Provisioning (wersja zapoznawcza)** 
![](media/how-to-configure/manage1.png)

4.  Kliknij pozycję **Nowa konfiguracja**.
5.  Na ekranie Konfiguracja domena lokalna jest wstępnie wypełniona
6. Wprowadź **wiadomość e-mail z powiadomieniem**. Ta wiadomość e-mail zostanie powiadomiona 
7. gdy aprowizacji nie jest w dobrej kondycji.  
8. Przenieś selektor, aby go **włączyć** , a następnie kliknij przycisk **Zapisz**.
![](media/tutorial-single-forest/configure2.png)

## <a name="scoping-provisioning-to-specific-users-and-groups"></a>Określanie zakresu aprowizacji dla określonych użytkowników i grup
Jeśli chcesz, aby Agent mógł tylko synchronizować określonych użytkowników i grupy, możesz to zrobić. Zakres można określać przy użyciu lokalnych grup usługi AD lub jednostek organizacyjnych. W ramach konfiguracji nie można konfigurować grup i jednostek organizacyjnych. 

1.  W portalu usługi Azure AD kliknij pozycję **Azure Active Directory**
2.  Kliknij **Azure AD Connect**
3.  Wybierz pozycję **Zarządzaj aprowizacjim (wersja zapoznawcza)**
4.  W obszarze **Konfiguracja** kliknij konfigurację.  
![](media/how-to-configure/scope1.png)

5.  W obszarze **Konfiguracja**wybierz pozycję **Wszyscy użytkownicy** , aby zmienić zakres reguły konfiguracji.
![](media/how-to-configure/scope2.png)

6. Po prawej stronie można zmienić zakres, aby uwzględnić tylko grupy zabezpieczeń, wprowadzając nazwę wyróżniającą grupy, a następnie klikając przycisk **Dodaj**.
![](media/how-to-configure/scope3.png)

7. Lub Zmień ją tak, aby obejmowała tylko określone jednostki organizacyjne. Kliknij przycisk **gotowe** i **Zapisz**.
![](media/how-to-configure/scope4.png)


## <a name="restart-provisioning"></a>Ponowne uruchamianie aprowizacji 
Jeśli nie chcesz czekać na następne zaplanowane uruchomienie, możesz wyzwolić uruchomienie usługi przy użyciu przycisku Uruchom ponownie Inicjowanie obsługi administracyjnej. 
1.  W portalu usługi Azure AD kliknij pozycję **Azure Active Directory**
2.  Kliknij **Azure AD Connect**
3.  Wybierz pozycję **Zarządzaj aprowizacjim (wersja zapoznawcza)**
4.  W obszarze **Konfiguracja** kliknij konfigurację.  
![](media/how-to-configure/scope1.png)

5.  W górnej części kliknij pozycję **Uruchom ponownie Inicjowanie obsługi administracyjnej**.

## <a name="removing-a-configuration"></a>Usuwanie konfiguracji
Jeśli chcesz usunąć konfigurację, możesz to zrobić, wykonując następujące kroki.

1.  W portalu usługi Azure AD kliknij pozycję **Azure Active Directory**
2.  Kliknij **Azure AD Connect**
3.  Wybierz pozycję **Zarządzaj aprowizacjim (wersja zapoznawcza)**
4.  W obszarze **Konfiguracja** kliknij konfigurację.  
![](media/how-to-configure/scope1.png)

5.  Kliknij przycisk **Usuń**w górnej części strony.
![](media/how-to-configure/remove1.png)

>[!IMPORTANT]
>Nie ma potwierdzenia przed usunięciem konfiguracji, dlatego należy się upewnić, że jest to akcja, którą chcesz wykonać przed kliknięciem przycisku **Usuń**.


## <a name="next-steps"></a>Następne kroki 

- [Co to jest inicjowanie obsługi?](what-is-provisioning.md)
- [Co to jest Azure AD Connect aprowizacji w chmurze?](what-is-cloud-provisioning.md)
