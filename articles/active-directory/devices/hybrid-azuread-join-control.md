---
title: Jak skonfigurować hybrydowych urządzeń w przyłączonych do usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak konfigurowanie urządzeń przyłączonych do usługi Azure Active Directory hybrydowych.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: 2332923946e414325b9723a59cf493d9d1060cc6
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2018
ms.locfileid: "39369177"
---
# <a name="how-to-control-the-hybrid-azure-ad-join-of-your-devices"></a>Jak kontrolować dołączenie do hybrydowej usługi Azure AD urządzeń

Dołączenie do hybrydowej usługi Azure AD jest proces automatycznego rejestrowania urządzeń przyłączonych do domeny lokalnej za pomocą usługi Azure AD. Istnieją przypadki, w których nie chcesz wszystkich urządzeń do automatycznej rejestracji. Jest to przykład wartości true, podczas początkowego wdrożenia, aby sprawdzić, czy wszystko działa zgodnie z oczekiwaniami.

Ten artykuł zawiera wskazówki dotyczące metody kontrolowania urządzeń, dołączenie do usługi Azure AD hybrydowej. 


## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że znasz:

-  [Wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](../device-management-introduction.md)
 
-  [Jak planowanie implementacji hybrydowej usługi Azure Active Directory join](hybrid-azuread-join-plan.md)

-  Konfigurowanie hybrydowej usługi Azure Active Directory join dla [domen zarządzanych](hybrid-azuread-join-managed-domains.md) lub [domen federacyjnych](hybrid-azuread-join-federated-domains.md)



## <a name="control-windows-current-devices"></a>Kontrolowanie bieżące urządzenia Windows

Dla urządzeń z systemem Windows pulpitu systemu operacyjnego, obsługiwana wersja to Windows Update rozliczenia 10 (wersja 1607) lub nowszej. Najlepszym rozwiązaniem jest uaktualnienie do najnowszej wersji systemu Windows 10.

Wszystkie systemy windows, bieżące urządzenia automatycznego rejestrowania w usłudze Azure AD na początku urządzenia lub użytkownika logowania. Można kontrolować to zachowanie, przy użyciu obiektu zasad grupy (GPO) lub System Center Configuration Manager.

Aby kontrolować bieżące urządzenia Windows, należy: 

1.  **Na wszystkich urządzeniach**: wyłączanie automatycznej rejestracji urządzeń.
2.  **Do wybranych urządzeń**: Włączanie automatycznej rejestracji urządzeń.

Jeśli sprawdzono, że wszystko działa zgodnie z oczekiwaniami, jesteś gotowy ponownie włączyć automatyczną rejestrację urządzeń dla wszystkich urządzeń.



## <a name="group-policy-object"></a>Obiekt zasad grupy 

Można kontrolować zachowanie rejestracji urządzenia z urządzeń przez wdrożenie następujących zasad grupy: **zarejestrować komputery przyłączone do domeny jako urządzenia**

**Aby ustawić obiekt zasad grupy**:

1.  Otwórz **Menedżera serwera**, a następnie przejdź do **narzędzia \> Zarządzanie zasadami grupy**.

2.  Przejdź do węzła domeny, która odnosi się do domeny, w którym ma być wyłączenie/włączenie rejestracji automatycznej.

3.  Kliknij prawym przyciskiem myszy **obiekty zasad grupy**, a następnie wybierz pozycję **New**.

4.  Wpisz nazwę (na przykład *dołączenie do hybrydowej usługi Azure AD*) dla obiektu zasad grupy. 

5.  Kliknij przycisk **OK**.

6.  Kliknij prawym przyciskiem myszy nowy obiekt zasad grupy, a następnie wybierz **Edytuj**.

7.  Przejdź do **konfiguracji komputera \> zasady \> Szablony administracyjne \> składników Windows \> rejestracja urządzeń w usłudze**. 

8.  Kliknij prawym przyciskiem myszy **zarejestrować komputery przyłączone do domeny jako urządzenia**, a następnie wybierz pozycję **Edytuj**.

    > [!NOTE] 
    > Ten szablon zasad grupy została zmieniona z wcześniejszych wersjach konsoli zarządzania zasadami grupy. Jeśli używasz starszej wersji konsoli, przejdź do strony **konfiguracji komputera \> zasady \> Szablony administracyjne \> składników Windows \> dołączania \> Automatycznie sprzężenia klienta komputerów w miejscu pracy**. 

9.  Wybierz jedną z następujących ustawień, a następnie kliknij przycisk **Zastosuj**:

    - **Wyłączone** — aby zapobiec automatycznej rejestracji urządzeń
    - **Włączone** — w celu umożliwienia rejestracji urządzeń automatycznego

10. Kliknij przycisk **OK**.

Należy połączyć obiekt zasad grupy z wybraną lokalizację. Na przykład aby ustawić te zasady dla wszystkich przyłączonych do domeny bieżącego urządzeń w Twojej organizacji, należy połączyć obiekt zasad grupy do domeny. Przeprowadzenie wdrożenia kontrolowanego, należy ustawić te zasady na przyłączonym do domeny Windows bieżące urządzenia, które należą do jednostki organizacyjnej lub grupie zabezpieczeń.

### <a name="configuration-manager-controlled-deployment"></a>Wdrożenie kontrolowane w programie Configuration Manager 

Można kontrolować zachowanie rejestracji urządzenia z bieżącym urządzeń poprzez skonfigurowanie następującego ustawienia klienta: ** automatycznego rejestrowania nowych urządzeń przyłączonych do domeny systemu Windows 10 w usłudze azure Active Directory **


**Aby ustawić ustawienia klienta**:

1.  Otwórz **programu Configuration Manager**, a następnie przejdź do **usług w chmurze**.

2.  W obszarze **ustawienia urządzenia**, wybierz jedną z następujących ustawień **automatycznego rejestrowania nowych urządzeń przyłączonych do domeny systemu Windows 10 w usłudze azure Active Directory**:

    - **Nie** — aby zapobiec automatycznej rejestracji urządzeń
    - **Tak** — w celu umożliwienia rejestracji urządzeń automatycznego


3.  Kliknij przycisk **OK**.
    

Należy połączyć to ustawienie klienta z wybraną lokalizację. Na przykład aby skonfigurować to ustawienie dla wszystkich urządzeń bieżącego Windows w organizacji klienta, należy połączyć ustawienia domeny klienta. Przeprowadzenie wdrożenia kontrolowanego, można skonfigurować ustawienie klienta umożliwiające Windows przyłączone do domeny bieżącego urządzenia, które należą do jednostki organizacyjnej lub grupie zabezpieczeń.

## <a name="control-windows-down-level-devices"></a>Urządzenia niskiego poziomu Windows

Aby zarejestrować urządzenia niskiego poziomu Windows, musisz pobrać i zainstalować pakiet Instalatora Windows (msi) z Centrum pobierania na [Microsoft dołączania komputerów do systemu Windows 10](https://www.microsoft.com/en-us/download/details.aspx?id=53554) strony.

Pakiet można wdrożyć za pomocą to system dystrybucji oprogramowania, takich jak System Center Configuration Manager. Pakiet obsługuje opcje standardowej instalacji dyskretnej, za pomocą parametru cichy. [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager) Current Branch zapewnia dodatkowe korzyści z wcześniejszych wersji, takich jak możliwość śledzenia rejestracje ukończone.

Instalator jest utworzenie zaplanowanego zadania w systemie, który jest uruchamiany w kontekście użytkownika. Zadanie jest wyzwalane, gdy użytkownik loguje się do Windows. Zadanie dyskretnie łączy urządzenie z usługą Azure AD przy użyciu poświadczeń użytkownika, po uwierzytelnieniu w usłudze Azure AD.


Aby kontrolować rejestracji urządzeń, należy wdrożyć pakiet Instalatora Windows tylko dla wybranej grupy systemu Windows niższego poziomu urządzeń. Jeśli sprawdzono, że wszystko działa zgodnie z oczekiwaniami, jesteś gotowy do wdrożenia pakietu do wszystkich urządzeń niskiego poziomu.


## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](../device-management-introduction.md)



